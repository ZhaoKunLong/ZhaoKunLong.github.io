---
title: nest--pipe
date: 2022-03-18 18:38:32
tags:
---

# NestJS pipe

## pipe 的作用

- 转换数据
- 验证数据
- 在这两种情况下, 管道都对控制器路由处理程序 arguments 正在处理的对象进行操作. Nest 会在调用这个方法之前插入一个管道，管道会先拦截方法的调用参数,进行转换或是验证处理，然后用转换好或是验证好的参数调用原方法。

## pipe 原理

- 对路由 controller 的 arguments 进行处理。 拦截路由方法的参数，进行转换/验证 controller 处理部分之前进行检查和转换。如果出现错误就跑出错误，然后阻止继续执行。
- 了解原理进行自定义 pipe

```Typescript
// 1.  每个pipe 必须实现 PipeTransform. 必须实现 它的 transform 方法
// 也就是一个ts里面的一个参数装饰器。
import { PipeTransform, Injectable, ArgumentMetadata } from '@nestjs/common';
// PipeTransform<T, R>是必须由任何管道实现的通用接口。泛型接口用于T指示输入的类型value，并R指示transform()方法的返回类型。
@Injectable()
export class ValidationPipe implements PipeTransform<string,string> {
  transform(value: any, metadata: ArgumentMetadata) {
    // 进行校验然后  throw 错误。
    return value;
  }
}

// value 是路由处理的参数。也就是pipe 所加到的位置的参数。 metadata 是对这个参数的元数据的描述。
export interface ArgumentMetadata {
  type: 'body' | 'query' | 'param' | 'custom';
  metatype?: Type<unknown>;
  data?: string;
}

// type	指示参数是 body @Body()、 query @Query()、 param@Param()、自定义参数 自定义参数处理装饰器 [自定义参数处理装饰器](https://docs.nestjs.com/custom-decorators) @IP
// metatype	提供参数的元类型，例如String. 注意：该值是undefined如果您在路由处理程序方法签名中省略类型声明，或者使用原始 JavaScript。
// data	传递给装饰器的字符串，例如@Body('string'). 如果undefined您将装饰器括号留空。从Body 中所取的值。
//eg  @Param('id', ParseIntPipe)    value =>  路由的param所有参数（因为是用再param上的）。 type => param.  metatype =>  value的元数据 data => 'id'
// 如果是用在 controller 上面的话。 value 就是所有的路由参数（包括query, body, ）
```

## 转换数据

- 内置 pipe `ParseIntPipe` `ParseBoolPipe` `ParseEnumPipe`
- 如何使用
- 固定使用在 controller 内。
  1. 转换参数
- 转换管道的 例子
  1. 转换为int
  ```Typescript
   import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';

    @Injectable()
    export class ParseIntPipe implements PipeTransform<string, number> {
      transform(value: string, metadata: ArgumentMetadata): number {
        const val = parseInt(value, 10);
        if (isNaN(val)) {
          throw new BadRequestException('Validation failed');
        }
        return val;
      }
    }
  ```
  2. 还有一种是 可以在转换管道里面进行查询 `UserByIdPipe`
  ```Typescript
    @Get(':id')
    findOne(@Param('id', UserByIdPipe) userEntity: UserEntity) {
      return userEntity;
    }
  ```
- 提供默认值
```Typescript
    @Get()
    async findAll(
      @Query('activeOnly', new DefaultValuePipe(false), ParseBoolPipe) activeOnly: boolean,
      @Query('page', new DefaultValuePipe(0), ParseIntPipe) page: number,
    ) {
      return this.catsService.findAll({ activeOnly, page });
    }
``` 

- 使用方法

```Typescript
// 1. 直接在参数装饰器上使用。
@Get(':id')
async findOne(@Param('id', ParseIntPipe) id: number) {
  return this.catsService.findOne(id);
}
// 2. 使用实例 如果new 的时候必须传入config 配置 可以 new ParseIntPipe({errorHttpStatusCode:HttpStatus.NOT_ACCEPTABLE})
@Get(':id')
async findOne(@Param('id',  new ParseIntPipe()) id: number) {
  return this.catsService.findOne(id);
}

// 3. 这样不起作用
async findOne(@Body(ParseIntPipe) id: number) {
  return this.catsService.findOne(id);
}

// 4. 在此处进行body处理
@UsePipe(ParseIntPipe)
async findOne(@Body() id: number) {
  return this.catsService.findOne(id);
}
```

## 验证管道

### 使用`joi` schema 进行数据输入验证

1.  `joi` schema 是什么？ [使用 joi 进行数据验证](https://juejin.cn/post/6946905410087419941)


    - 通过创建schema 对数据进行验证。 也可以延伸出，对一个OBJ进行格式验证。
      1. 先定义对象的验证方式和需要的步骤 `objSchema`
      2. 使用 `objSchema.validate(data)` 来对 data进行数据验证。

```JavaScript
const Joi = require('joi');
const schema = Joi.object({
username: Joi.string()
  .alphanum()
  .min(3)
  .max(30)
})

exports.helloWorld = (req, res) => {
  const { error, value } = schema.validate(req.query)
  if (error) {
    return res.status(422).json({ error: error })
  }
  res.send('Hello, World')
}
```

2. 在 Nestjs 中通过 Pipe 实现

   - 创建`controller`

   ```Typescript
   // cat.controller.ts

        @Post()
        async create(@Body() createCatDto:any) {
          this.catsService.create(createCatDto);
        }
   ```

   - `Joi` 创建验证 object

   ```Typescript
   // create-cat.schema.ts
        export const createCatSchema = Joi.object({
          name: Joi.string(),
          age: Joi.number().min(3),
          breed: Joi.string().alphanum()
        })
   ```

   - 创建`pipe` 进行数据输入验证

   ```Typescript
   // JoiValidation.pipe.ts
        import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';
        import { ObjectSchema } from 'joi'; // 导入类型

        @Injectable()
        export class JoiValidationPipe implements PipeTransform {
          constructor(private schema: ObjectSchema) {} // 在应用pipe的时候 导入

          transform(value: any, metadata: ArgumentMetadata) {
            const { error } = this.schema.validate(value); // 对参数进行验证
            if (error) {
              throw new BadRequestException('Validation failed'); // 失败的话就抛出错误
            }
            return value; // 通过的话就继续使用
          }
        }
   ```
   - 在`controller`上应用
   ```Typescript
    // cat.controller.ts
          import { createCatSchema } from "createCat.schema";

          @Post()
          @UsePipes(new JoiValidationPipe(createCatSchema))
          async create(@Body() createCatDto:any) {
              this.catsService.create(createCatDto);
            }
   ```

### 使用类 `class-validate`, `class-transform` 来通过类定义进行验证`data`.

1. class-validator class-transform 是什么？
    - [class-validator 进行数据验证](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/697331/)
    -  class-validator, 提供了类属性装饰器，但是是用来验证一个类的属性的。 是採用注解的方式進行校驗，底層使用的是老牌的校驗包validator.js。
    -  官网例子
    ``` Typescript
          export class Post {
            @Length(10, 20)
            title: string;
            @Contains('hello')
            text: string;
            @IsEmail()
            email: string;
            @IsFQDN()
            site: string;
            @IsDate()
            createDate: Date;
          }
        let post = new Post();
        post.title = 'Hello'; // should not pass
        post.text = 'this is a great post about hell world'; // should not pass
        post.email = 'google.com'; // should not pass
        post.site = 'googlecom'; // should not pass

        // 实例化后 通过validate 进行验证
        validate(post).then(errors => {
          // errors is an array of validation errors
          if (errors.length > 0) {
            console.log('validation failed. errors: ', errors);
          } else {
            console.log('validation succeed');
          }
        });

        // 如果参数里面的验证装饰器里面有异步的
        validateOrReject(post).catch(errors => {
            console.log('Promise rejected (validation failed). Errors: ', errors);
         });
        // or
        async function validateOrRejectExample(input) {
          try {
            await validateOrReject(input);
          } catch (errors) {
            console.log('Caught promise rejection (validation failed). Errors: ', errors);
          }
        }
    ```
    - class-transformer, 可以將一個json轉成指定的類的對象，
    ```Typescript
        import {plainToClass} from "class-transformer";
        let users = plainToClass(User, userJson); // to convert user plain object a single user. also supports arrays
    ```

    - 这样的话， 就可以。 定义好数据类。 然后使用 `plainToClass` 将数据转换为对应的类型，然后再通过 `validate` 进行验证了。


2. 在 nest 中使用 pipe 实现
   - 创建 `CreateCatDTO` 类 并添加 class-valuator 跟 class-transform 两个验证
   ```Typescript
   // create-cat.dto.ts
   export class CreateCatDTO{
     @IsString()
     name:string;
     @IsNumber()
     age: number;
     @IsString()
     breed:string;
   }
   ``` 
   - 创建`ValidationPipe`
      - 通过 plainToClass 将value 转换为 `CreateCatDTO` 类型。 
      - 使用validate 对value 和类进行验证。
   ```Typescript
    import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';
    import { validate } from 'class-validator';
    import { plainToClass } from 'class-transformer';

    @Injectable()
    export ValidationPipe implements PipeTransform {

      private toValidate(metatype: Function): boolean {  
        const types: Function[] = [String, Boolean, Number, Array, Object];
        return !types.includes(metatype);
      }
     async transform(value: any, { metatype, type, data }: ArgumentMetadata) {
       // 验证 metatype 是否是所需类型。 排除普通类型的验证。 也就是说 如果 参数是 `async xxx(@body() body: string| boolean |array| Object | number)` 这些普通类型 是不会进行验证的。 
       //当 当前正在处理的参数是原生 JavaScript 类型时，它负责绕过验证步骤（这些不能附加验证装饰器，因此没有理由通过验证步骤运行它们）
        if (!metatype || !this.toValidate(metatype)) {
           return value;
        }


       // 给数据附上对应的类型
       // 我们使用 class-transformer 函数plainToClass()将我们的纯 JavaScript 参数对象转换为类型化对象，以便我们可以应用验证。我们必须这样做的原因是传入的 post body 对象在从网络请求反序列化时没有任何类型信息
       const object = plainToClass(metatype, value);
       // 验证数据
       const error = await validate(object)
       if(error.length>0){
         throw new BadRequestException('Validation failed');
       }
       return value
     }
   }
   ```
   - 向 `controller` 绑定validationPipe 
      - 最后一步是绑定ValidationPipe. 管道可以是参数范围、方法范围、控制器范围或全局范围。
   ```Typescript
      @Post()
      async create(
        // 或者 @Body(ValidationPipe) createCatDto: CreateCatDto,
        @Body(new ValidationPipe()) createCatDto: CreateCatDto,
      ) {
        this.catsService.create(createCatDto);
      }
   ```
   -  全局范围绑定管道。 很多路由都需要验证参数。那么就在全局范围内进行管道绑定。 
      - 通过 main.ts 进行添加。
      ``` Typescript
      // main.ts
        async function bootstrap() {
          const app = await NestFactory.create(AppModule);
          app.useGlobalPipes(new ValidationPipe());
          await app.listen(3000);
        }
        bootstrap();
      ```
      - 通过 `useGlobalPipes` 进行的绑定 在模块注册层面是无法进行注册的。 所以需要在app进行注册。
      ```Typescript
      import { Module } from '@nestjs/common';
      import { APP_PIPE } from '@nestjs/core';

      @Module({
        providers: [
          {
            provide: APP_PIPE,
            useClass: ValidationPipe, // 也可以是 useValue： new ValidationPipe()
          },
        ],
      })
      export class AppModule {}
      ```

3. `Nest`内置`ValidationPipe`提供了比我们在本章中构建的示例更多的选项，为了说明定制管道的机制，[这里有很多验证的例子](https://docs.nestjs.com/techniques/validation)

## 使用 validation
  1. 安装
  ```sh
   npm i --save class-validator class-transformer
  ```
  2. 在全局使用 main.ts
  ```Typescript
  app.useGlobalPipes(
    new ValidationPipe({
      transform: true,    // 会在验证的时候进行数据类型转换
      // whitelist: true, 如果没有添加验证器的属性会被忽略// 且 不在类里面的属性也会被忽略
    }),
  );
  ```