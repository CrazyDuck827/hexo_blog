---
title: PHP反射机制
categories: 
    - PHP
tags:
    - PHP
    - 反射
keywords: 
    - 反射
    - PHP反射
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/7fqLN8A4acX9HKs2zbqOYYvtAe6ILp06.png
---

### 概念
- 反射在每个面向对象的编程语言中都存在，它的主要目的就是在运行时分析类或者对象的状态，导出或提取出关于类、方法、属性、参数等的详细信息，包括注释，private。
- 反射是操纵面向对象范型中元模型的 API，可用于构建复杂，可扩展的应用。反射在日常的 Web 开发中其实用的不多，更多的是在偏向底层一些的代码中，比如：自动加载插件，自动生成文档，框架的底层中依赖注入、对象池、动态代理等等。
- [官方文档](https://www.php.net/manual/zh/book.reflection.php)

### 反射类

- ReflectionClass -- 获取类相关信息，如获取属性、方法、文档注释等
- ReflectionFunction -- 获取函数相关信息
- ReflectionMethod -- 获取方法相关信息
- ReflectionParameter -- 函数或方法的参数的相关信息

### 反射的应用

#### 依赖注入

- 以Laravel框架中的依赖注入为例，实际上是在底层调用了 Illuminate\Container\Contaiern::build($concrete) 这个方法

```
    public function build($concrete){
        $reflector = new ReflectionClass($concrete);
        // 创建了一个代表 $concrete 类的 ReflectionClass 对象
        $constructor = $reflector->getConstructor();
        // 获取类的构造函数

        if (is_null($constructor)) {
            // 没有构造函数返回类的实例化
            return new $concrete;
        }

        $dependencies = $constructor->getParameters();
        // 有构造函数，获取构造函数的参数
        $instances = $this->resolveDependencies($dependencies);
        // 解决依赖关系
        return $reflector->newInstanceArgs($instances);
        // 从给出的参数创建一个新的类实例返回
    }
```

### 参考资料

[反射在 PHP 中的应用](https://learnku.com/articles/7538/the-application-of-reflection-in-php)

[详解 PHP 反射的基本使用](https://learnku.com/articles/37162)

