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
- 