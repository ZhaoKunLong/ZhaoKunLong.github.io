---
title: partial-type-with-DTO-in-nest
date: 2022-03-29 18:46:23
tags:
---
# NestJs DTO 继承 ｜ 部分验证 方法
## 通过 [`@nestjs/mapped-types`](https://github.com/nestjs/mapped-types) 包进行实现
### 导入包
```sh
cnpm i --save @nestjs/mapped-types
```
### 包内提供的功能
> #### Available mapped types:
> - PartialType - returns a type (class) with all the properties of the input type set to optional (requirement: at least 1 validation decorator applied to each property)
>  - `返回一个新的类将所有属性变为optional。每个属性必须有一个验证器`
>  - PickType - constructs a new type (class) by picking a set of properties from an input type
>  - `从输入的类内取出需要的属性重新生成一个类进行验证`
>  - OmitType - constructs a type by picking all properties from an input type and then removing a particular set of keys
>  - `从输入的类中去除一些属性重新生成一个类进行验证`
>  - IntersectionType - combines two types into one new type (class)
>  - `合并两个类为一个类进行验证`

### 如何使用包
```Typescript
class CreateBooking{
  @IsString()
  name:string;
}
export class CreateBookingDTO extends PartialType(CreateBooking) {}
```