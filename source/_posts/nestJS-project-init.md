---
title: nestJS-project-init
date: 2022-02-24 19:28:57
tags:
---
## 如何初始化 nestJS 项目

## 语言篇
- typescript
  - set ts.config
- ESlint
- .gitignore
- gitHook
- init app module

## 工具
- 设置 ESlint, prettier 扩展
- 使用 nest-cli 
  - [使用费命令](https://github.com/nestjs/nest-cli/blob/master/commands/new.command.ts)
  - nest new . 在当前目录生成。 `注意会跟README.md 有合并冲突，需要将其先删掉`
  - nest new {project-name} --directory `[位置路径]`
  - nest new {project-name} --skip-git 

- generate|g [options] <schematic> [name] [path]  Generate a Nest element.
- nest g  module name [path] `这里的path 是以src 为root 的`
- eg: `nest g co client modules` 是在src/modules 下面

## 添加 config 模块
- 下载 config pack `npm i --save @nestjs/config` 它使用了 [dotenv](https://github.com/motdotla/dotenv) `@nestjs/config requires TypeScript 4.1 or later.`
- 在 `app.module.ts` 导入
---
  - 导入后就可以使用了， 它会自动去找到 `.env` 文件然后 加载并parse它， 如果有多个的话就通过指定 envFilePath (接受一个数组或字符串，都表示路径) 的办法进行区别使用
  - 如果有多个文件都有相应的变量， 第一个文件的变量`优先级最高`。
  - `.env`的变量 和外部的 `export DATABASE_URL=test` 外部变量的`优先级最高`
  - 根据环境不同使用的变量放到不同环境中. 
- 设置为全局导入，这样就避免每个 module 都要 imports
```TypeScript
import {Module} from '@nestjs/common'
import {ConfigModule} from '@nestjs/config'

@Module({
  imports:[ConfigModule.forRoot({
    envFilePath:`.env.${process.env.NODE_ENV}` || ['.env'],
    isGlobal:true
    // ignoreEnvFile:true  禁用   .env 文件。 在生产环境可以禁用，然后使用 task-definition.js 来定义 容器内的环境变量。 但是本身容器环境变量优先级就高于。env.
  })]
})
export class AppModule {}
```

- 如果使用的不是 `.env` 而是 `config.development.json` 模式的话，就将 文件改写到 ts 文件中。然后·export·。 再在module中引入
- ·config/development.ts· 
```TypeScript
export default()=>({
  port:3000,
  database:{
    host:''
    port:''
  }
})
```
- 在module 中使用 load 加载并parse它
  - load 加载的是一个数组也允许 加载多个配置
- ·AppModule.ts·
   
```TypeScript
import {Module} from '@nestjs/common'
import {ConfigModule} from '@nestjs/config'
import development from 'config/development'
import prod from 'config/prod'

@Module({
imports:[
  ConfigModule.forRoot({
    load:[development,prod]
  })
]
})
export class AppModule{}
```

- 使用 `configService` 的`get` 方法将值取出来
- 依赖注入   `constructor(private configService:configService)`
```TypeScript
const port = this.configService.get<string>('key', 'default value')
```
- 这里使用的是 env 的key 如果是 json 的话就是 ·database.host· dot 的key

- [验证配置文件的类型和期望值](https://docs.nestjs.com/techniques/configuration#schema-validation) 没太大必要。
- 使用的是config 的话，可以通过 interface  进行验证

- 可以通过 joni 进行 数据类型验证。 也可以使用 validation.ts

## 配置启动设置 NODE_ENV

- [cross-env](https://www.npmjs.com/package/cross-env)
```shell
npm install --save-dev cross-env

{
  scripts:{
    "dev" : "cross-env NODE_ENV={val} ..... 后面跟上之前的"
  }
}
```

## 设置logger
- 通过 @nestjs/common 可以引入 Logger  类用作 里面的logger service
```test
  - disable logging entirely
  - specify the log level of detail (e.g., display errors, warnings, debug information, etc.)
  - override timestamp in the default logger (e.g., use ISO8601 standard as date format)
  - completely override the default logger
  - customize the default logger by extending it
  - make use of dependency injection to simplify composing and testing your application
```
- 也可以继承后 重新创建一个实现，来更改logger
- 更加高级的办法也可以使用其他的logger 框架， [winston](https://github.com/winstonjs/winston)


- 基础使用添加logger 到系统
- `如果需要特殊配置的话可以设置，如果不需要的话，可以不配置`
```TypeScript
const app = await NestFactory.create(AppModule, {
  logger: ['error', 'warn'], //'log', 'error', 'warn', 'debug', and 'verbose'. false 意思是禁用logger
});
await app.listen(3000);

//在其他service 中可以直接使用

import { Logger, Injectable } from '@nestjs/common';

@Injectable()
class MyService {
  private readonly logger = new Logger(MyService.name);

  doSomething() {
    this.logger.log('Doing something...');
  }
}
```

- 使用 基础办法，需要在每一个用到service的地方使用new logger()

- 所以我们需要进阶办法，
- 创建一个logger module。让它成为全局module， 然后，在每一用到的地方，使用
- 创建一个 loggerModule
- 创建一个 loggerService类 实现 loggerService 这个接口. 因为 `ConsoleLogger 已经实现了 LoggerService` 所以这里我们可以直接 extends 这个 ConsoleLogger 类

- 并export loggerService. 然后在app.module 里面导入它，并设置为全局
- 我们可以使用了 在需要的地方的constructor()引入就可以使用了
```TypeScript
import { Injectable } from '@nestjs/common';
import { MyLogger } from './my-logger.service';

@Injectable()
export class CatsService {
  constructor(private myLogger: MyLogger) {
    // Due to transient scope, CatsService has its own unique instance of MyLogger,
    // so setting context here will not affect other instances in other services
    this.myLogger.setContext('CatsService'); //CatsService.name
  }

  findAll(): Cat[] {
    // You can call all the default methods
    this.myLogger.warn('About to return cats!');
    // And your custom methods
    this.myLogger.customLog();
    const cats: Cat[] = [];
    return cats;
  }
}
```




####  如何设置一个module 为全局注册

- [global module](https://docs.nestjs.com/modules#global-modules)
- Nestjs 的设计理念就是module 是独立的，必须引入之后才能起作用。
- 那面多多次引入我们可以设置这个 module  是全局的 然后再 根module 里面引入一次以后就不需要再继续引用了。 
```typescript
import { Module, Global } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Global()
@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}

The @Global() decorator makes the module global-scoped. Global modules should be registered only once, generally by the root or core module. In the above example, the CatsService provider will be ubiquitous, and modules that wish to inject the service will not need to import the CatsModule in their imports array.
```
- 应该只能引入一次， 在根节点。 然后 那个 providers和export的 service就会无处不在。只需要引入service 就可以了。 在其他module里面不需要providers 和import它了。

## 如何设置 nestjs  Debugging


- [通过vscode  的自动附加来进行添加](https://www.jianshu.com/p/5a4d1d5234e3)
-  设置debug: 自动附加。
- 然后启动 npm run start:debug   检测到自动附加后就可以调试。

##### 想要实现在 launch 里面直接启动。然后附加的话。就比较难了
- [vscode 里面的launch](https://javascript.plainenglish.io/debugging-nestjs-in-vscode-d474a088c63b)
- 设置 Vscode 的 launch.js
-  通过设置 task.js 来进行启动程序。 然后再launch里面使用 acctach
```json
// `launch.json`
{
    "configurations": [
        {
            "type": "node",
            "name": "Run Script: start:debug",
            "request": "attach",
            "port": 9229,            
            "preLaunchTask":"npm: start:debug",
            "cwd": "${workspaceFolder}",
        }
    ]
}

// tasks.json
{
	"version": "2.0.0",
	"tasks": [
		{
			"label": "npm: start:debug",
			"type": "npm",
			"script": "start:debug",
			"group": {
				"kind": "test",
				"isDefault": true
			},
			"problemMatcher": [],
			"detail": "cross-env NODE_ENV=development nest start --debug --watch"
		}
	]
}
```
- 这样无法停止。尝试办法 
    - 所以先尝试是否能够通过自带的先启动加断点 - 无法启动。 还是会出现 ·Waiting for the debugger to disconnect...· 暂时无法解决
    - 使用launch.js 启动命令行 所以失败
    - 使用 task 定义，再launch 启动时候启动task `start：debug` 然后再关闭的时候关闭 task. 但是这样就无法进行断点， 需要将断点设置为智能。然后 通过attach 进行调试，这样。当关闭链接的时候。是无法触发 postDebugTask 进行task  关闭的。
    - 使用 attach 无法trigger `postDebugTask` 也就无法停止debug.
- 自定义 launch 
  -  这条路也走不通了

- 最终解决就是先使用 start：debug 然后选择智能attach 不使用 launch
