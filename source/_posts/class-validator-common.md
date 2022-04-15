---
title: class-validator-common
date: 2022-03-29 18:37:01
tags:
---

## 常用的 class-validator

### 所有验证器的集合

- [都在 decorator 文件里](https://github.com/typestack/class-validator/blob/develop/src/decorator/decorators.ts)

### 类型验证 `typechecker`

- 可以查看 `Typechecker` 文件
  1. IsString()
  2. IsNumber()
  3. IsBoolean()
  4. IsEnum(EnumType)
  5. IsDate
  6. IsInt
  7. IsArray
  8. IsObject

### 常用类型验证

- IsNotEmpty `用于必传的字符串， 防止字符串为空`
- Min `最小值`
- Max `最大值`
- IsEmail `验证email`
- IsPhoneNumber `验证手机号`

### 修改验证返回信息

- `message： string ｜ ((ValidationArguments ）=> string)`

```typescript
@IsNotEmpty()
  @IsEnum(YesOrNo, {
    message: (validationArguments: ValidationArguments) => {
      console.log(validationArguments);
      return `${validationArguments.property} must be a valid enum value: ${validationArguments.constraints}`;
    },
  })
  isInternationalStudent: YesOrNo;
```


