---
title: "go语言结构体，方法，接口"
date: 2020-05-18T14:50:12+08:00
draft: true
tags: ["GO", "golang"]
categories: ["IT技术"]
---

## 结构体，方法，接口

结构体，方法，接口，方法和接口都是对结构体内部做处理的，相当于结构体的内部属性

```GO
//结构体
type Student struct{
    old int
    height float64
    hanyu int
    shuxue int
}
//方法
func (s Student) Sum() int{
    return hanyu + shuxue
} 
//接口
type Human interface{
    Sum() int
}
//使用
human := Student{old: 15, height: 158, hanyu: 92, shuxue: 94}
(&human).Sum()
```

## 类型



## 方法

方法和函数差不多

## 接口

