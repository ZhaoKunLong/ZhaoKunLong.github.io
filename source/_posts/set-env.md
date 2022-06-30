---
title: set-env
date: 2022-04-28 18:47:26
tags:
---

## 设置环境变量

    # 查看所有 环境变量
    export  
    # 设置变量
    export TZ="Australia/Sydney"
    # 删除变量
    unset TZ

    # 对于node 
    process.env
    # 删除node变量
    process.env.TZ=null

## 时间问题
-  Moment 使用toDate 会自动转化时令。
-  MongoDb 保存日期，会直接保存为UTC时间。 将原有值减去时区。
-  夏时令 是为了节省能源， 充分使用光照，发明的。 

## webhook 问题
- 使用webhook 接受参数
- 可以开发插件，将请求在转发到localhost上面
- https://webhook.site/#!/6f31a334-510d-4fec-9a90-f829b7304950/58b05f8e-713d-4ae8-af57-8a790efc7a8f/1
