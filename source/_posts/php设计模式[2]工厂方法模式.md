---
title: php设计模式[2]工厂方法模式
date: 2019-11-07 11:25:00
categories: 
    - PHP
tags:
    - PHP
    - 设计模式
---

### 应用场景
    要实例化的对象充满不确定性可能会改变的时候
    要创建的对象的数目和类型是未知的

### 结构
    1个 interface 或者 abstract 产品父类
    多个实现 interface 或者继承 abstract 的具体产品类
    
    1个 interface 或者 abstract 工厂父类
    多个实现 interface 或者继承 abstract 的具体工厂类
    
    具体工厂类和具体产品类一一对应
    在具体工厂类中实例化具体的产品类

### 示例

Operation.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

/**
 * 操作类型抽象类
 */
abstract class Operation
{
    /**
     * 运算符号左边的值
     */
    protected $numberA = 0;

    /**
     * 运算符号右边的值
     */
    protected $numberB = 0;

    /**
     * 计算结果
     */
    abstract public function getResult();

    /**
     * 给 numberA 赋值
     */
    public function setNumberA($number)
    {
        $this->numberA = $number;
    }

    /**
     * 给 numberB 赋值
     */
    public function setNumberB($number)
    {
        $this->numberB = $number;
    }
}
```

Add.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

/**
 * 加法
 */
class Add extends Operation
{
    /**
     * 计算结果
     */
    public function getResult()
    {
        return $this->numberA + $this->numberB;
    }
}
```

Sub.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

/**
 * 减法
 */
class Sub extends Operation
{
    /**
     * 计算结果
     */
    public function getResult()
    {
        return $this->numberA - $this->numberB;
    }
}
```

Mul.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

/**
 * 乘法
 */
class Mul extends Operation
{
    /**
     * 计算结果
     */
    public function getResult()
    {
        return $this->numberA * $this->numberB;
    }
}
```

Div.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

/**
 * 除法
 */
class Div extends Operation
{
    /**
     * 计算结果
     */
    public function getResult()
    {
        if ($this->numberB == 0) {
            throw new \InvalidArgumentException('除数不能为0');
        }
        return $this->numberA / $this->numberB;
    }
}
```

Factory.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

/**
 * 工厂抽象类
 */
abstract class Factory
{
    /**
     * 创建产品
     */
    abstract public function create();
}
```

AddFactory.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

/**
 * 加法工厂
 */
class AddFactory extends Factory
{
    /**
     * 创建加法产品类
     */
    public function create()
    {
        return new Add();
    }
}
```

使用的时候 new 各工厂即可；
运行示例；
index.php
```
<?php

namespace Baijunyao\DesignPatterns\FactoryMethod;

require __DIR__.'/../vendor/autoload.php';

/**
 * 客户端
 */
class Client
{
    /**
     * 不好的示例   直接 new 具体的产品
     */
    public function bad()
    {
        // 计算 1+2
        $operation = new Add();
        $operation->setNumberA(1);
        $operation->setNumberB(2);
        $result = $operation->getResult();
        echo $result;

        echo '<br>';

        // 计算 3+4
        $operation = new Add();
        $operation->setNumberA(3);
        $operation->setNumberB(4);
        $result = $operation->getResult();
        echo $result;
    }

    /**
     * 好的示例  new 产品对应的工厂
     */
    public function good()
    {
        $factory = new AddFactory();
        $operation = $factory->create();
        $operation->setNumberA(1);
        $operation->setNumberB(2);
        $result = $operation->getResult();
        echo $result;
    }
}

$client = new Client();
$client->bad();
echo '<br>';
$client->good();
```

>通过代码我们可以看出
工厂方法不需要再做判断了
但是增加了工作量
每增加一个产品都需要增加对应的工厂
这就形成了一种特殊的代码重复
不过式设计模式并不是独立使用的
很多时候都是多个模式互相配合来弱化各自的缺点

![oop](/image/oop2.jpg)