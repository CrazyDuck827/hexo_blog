---
title: PHP面试汇总
categories: 
    - PHP
tags:
    - PHP
    - 面试
keywords: 
    - PHP
    - 面试
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/RPAIEAjwLkmZR1fayU9gQD3xEJzoBhJ3.jpg
---

### 语言概况
#### 定义
    PHP是超文本预处理器，是弱类型语言（无须强调数据类型）
#### 数据类型
    四种标量类型：boolean （布尔型）、integer （整型）、float （浮点型, 也称作 double)、string （字符串）
    两种复合类型：array （数组）、object （对象）
    两种特殊类型：resource（资源）、NULL（NULL）
#### 函数
#### 面向对象oop
##### 特性
    封装：封装是指将现实世界中存在的某个客体的属性与行为绑定在一起，并放置在一个逻辑单元内
    继承：继承性是子类自动共享父类数据结构和方法的机制，这是类之间的一种关系
    多态：多态性是指相同的函数或方法可作用于多种类型的对象上并获得不同的结果
##### 魔术方法
##### 接口(interface)
##### 抽象类（abstract）
#### PHP7新特性
1. 标量类型与返回值类型声明
    > 增加了标量类型声明的特性，标量类型声明有两种模式：强制模式 (默认)，严格模式；
    > 默认情况下，所有的PHP文件都处于弱类型校验模式；
    > 强制模式强制转换，严格模式不符合报错；
2. PHP NULL 合并运算符
    > $site = isset($_GET['site']) ? $_GET['site'] : test';
    > $site = $_GET['site'] ?? 'test';
3. 太空船运算符（组合比较符）
    > $a <=> $b
    > a=b，返回0；a>b，返回1；a<b，返回-1
4. 常量数组
    > 在 PHP 5.6 中仅能通过 const 定义常量数组，PHP 7 可以通过 define() 来定义
5. 匿名类
    > 支持通过 new class 来实例化一个匿名类，这可以用来替代一些"用后即焚"的完整类定义
6. use语句
    > 可以使用一个 use 从同一个 namespace 中导入类、函数和常量
7. array + array 合并数组
    > +：
        >不论是数字索引，还是字符串索引，当出现相同的索引时，都会舍弃后者
    > array_merge：
        >当遇到相同的数字索引时，会对数字索引重新编号，而不会丢弃
        >当遇到相同的字符串索引时，后者会覆盖前者
8. 闭包（closure）
9. 性能
    > 减少内存分配次数
    > 多使用栈内存
    > 缓存数组的hash值
    > 字符串解析成桉树改为宏展开
    > 使用大块连续内存代替小块破碎内存

#### PHP8新特性
1. 新增联合类型（Union Types）
2. 添加了 WeakMap
3. 添加了 ValueError 类
4. 新增联合类型（Union Types）
5. 新增的特性大多是语法糖，主要是JIT
    JIT是一种编译器策略，它将代码表述为一种中间状态，在运行时将其转换为依赖于体系结构的机器码，并即时执行，在PHP8中，Zend VM不需要解释某些操作码，并且这些指令将直接作为CPU级指令执行


#### RESTful API
    互联网应用程序的API设计理念
#### 加密
##### 对称加密
    对称加密指的就是加密和解密使用同一个秘钥，所以叫做对称加密；例：DES，AES，3DES
##### 非对称加密
    加密和解密使用不同的秘钥，一把作为公开的公钥，另一把作为私钥。公钥加密的信息，只有私钥才能解密。私钥加密的信息，只有公钥才能解密；例：RSA，ECC
##### 对称加密和非对称加密的区别
    对称加密比非对称加密的效率高，但因为私钥的管理问题，安全性要差
##### php常见加密方式
    MD5，Base64，RSA，Crype，Sha1
#### PDO
#### MVC
#### Smarty
#### Composer
#### 接口
    CURL，Guzzle，RPC
#### PSR规范

### 底层
#### 垃圾回收机制
##### 在 PHP 5.2 及以前的版本中，PHP 的垃圾回收采用的是[【引用计数】](https://www.php.net/manual/zh/features.gc.refcounting-basics.php)算法
- php 的变量存储在「zval」变量容器（数据结构）中，「zval」属性包含如下信息：
        当前变量的数据类型；
        当前变量的值；
        用于标识变量是否为引用传递的 is_ref 布尔类型标识；
        指向该「zval」变量容器的变量个数的 refcount 标识符（即这个 zval 被引用的次数，注意这里的引用不是指引用传值，注意区分）。
   > 当一个变量被赋值时，就会生成一个对应的「zavl」变量容器。使用xdebug_debug_zval()函数查看「zavl」容器信息

    ```php
        // 变量赋值时，refcount 值等于 1
        $name = 'liugongzi';
        xdebug_debug_zval('name'); // (refcount=1, is_ref=0)string 'liugongzi' (length=9)
        
        // $name 作为值赋值给另一个变量， refcount 值增加 1
        $copy = $name;
        xdebug_debug_zval('name'); // (refcount=2, is_ref=0)string 'liugongzi' (length=9)
        
        // 销毁变量，refcount 值减掉 1
        unset($copy);
        xdebug_debug_zval('name'); // (refcount=1, is_ref=0)string 'liugongzi' (length=9)
    ```

- 写时复制：如果通过赋值的方式赋值给变量时不会申请新内存来存放新变量所保存的值，而是简单的通过一个计数器来共用内存，只有在其中的一个引用指向变量的值发生变化时，才申请新空间来保存值内容以减少对内存的占用
    > 在这个过程中发生以下几个操作: 
        将 \$copy 从 \$name 的 zval（内从）中分离出来（即复制）；
        将 \$name 的 refcount 减去 1；
        对 \$copy 的 zval 进行修改（重新赋值和修改 refcount）；

    ```php
        $name = 'liugongzi';
        xdebug_debug_zval('name'); // name: (refcount=1, is_ref=0)string 'liugongzi' (length=9)
        
        $copy = $name;
        xdebug_debug_zval('name'); // name: (refcount=2, is_ref=0)string 'liugongzi' (length=9)
        
        // 将新的值赋值给变量 $copy
        $copy = 'liugongzi handsome';
        xdebug_debug_zval('name'); // name: (refcount=1, is_ref=0)string 'liugongzi' (length=9)
        xdebug_debug_zval('copy'); // copy: (refcount=1, is_ref=0)='liugongzi handsome'
    ```
    
- 内存泄露：简单来说就是「引用计数」算法无法检测并释放循环引用所使用的内存，最终导致内存泄露。             

    ```php
        // @link http://php.net/manual/zh/function.memory-get-usage.php#96280
        function convert($size)
        {
            $unit=array('b','kb','mb','gb','tb','pb');
            return @round($size/pow(1024,($i=floor(log($size,1024)))),2).' '.$unit[$i];
        }
        
        // 注意：有用的地方从这里开始
        $memory = memory_get_usage();
        
        $a = array( 'one' );
        // 引用自身（循环引用）
        $a[] =&$a;
        
        xdebug_debug_zval( 'a' );
        var_dump(convert(memory_get_usage() - $memory)); // 296 b
        unset($a); // 删除变量 $a，由于 $a 中的元素引用了自身（循环引用）最终导致 $a 所使用的内存无法被回收
        var_dump(convert(memory_get_usage() - $memory)); // 568 b
    ```

##### PHP 5.3 之后对内存回收的实现做了优化，通过采用[【引用计数系统的同步周期回收】](https://www.php.net/manual/zh/features.gc.collecting-cycles.php)算法实现内存管理
- 在引用计数基础上增强：
    1. 引入了可能根（possible root）的概念：通过引用计数相关学习，我们知道如果一个变量（zval）被引用，要么是被全局符号表中的符号引用（即变量），要么被复杂类型（如数组）的 zval 中的符号（数组的元素）引用，那么这个 zval 变量容器就是「可能根」。
    2. 引入根缓冲区（root buffer）的概念：根缓冲区用于存放所有「可能根」，它是固定大小的，默认可存 10000 个可能根，如需修改可以通过修改 PHP 源码文件 Zend/zend_gc.c 中的常量GC_ROOT_BUFFER_MAX_ENTRIES，再重新编译。
    3. 回收周期：当缓冲区满时，对缓冲区中的所有可能根进行垃圾回收处理。
    
- 回收过程：
    1. 缓冲区（紫色框部分，称为疑似垃圾），存储所有可能根（步骤 A）；
    2. 采用深度优先算法遍历「根缓冲区」中所有的「可能根（即 zval 遍历容器）」，并对每个 zval 的 refcount 减 1，为了避免遍历时对同一个 zval 多次减 1（因为不同的根可能遍历到同一个 zval）将这个 zvel 标记为「已减」（步骤 B）；
    3. 再次采用深度优先遍历算法遍历「可能根 zval」。当 zval 的 refcount 值不为 0 时，对其加 1, 否则保持为 0。并请已遍历的 zval 变量容器标记为「已恢复」（即步骤 B 的逆运算）。那些 zval 的 refcount 值为 0 （蓝色框标记）的就是应该被回收的变量（步骤 C）；
    4. 删除所有 refcount 为 0 的可能根（步骤 D）。
    
- 整体过程：
> 采用深度优先算法执行：默认删除 > 模拟恢复 > 执行删除 达到内存回收的目的。

- 优化后的引用计数算法优势：
    1. 将内存泄露控制在阀值内，这个由缓存区实现，达到缓冲区大小执行新一轮垃圾回收；
    2. 提升了垃圾回收性能，不是每次 refcount 减 1 都执行回收处理，而是等到根缓冲区满时才开始执行垃圾回收。

##### PHP7的内存管理
- PHP 5 中 zval 实现上的主要问题：

        zval 总是单独 从堆中分配内存；
        zval 总是存储引用计数和循环回收 的信息，即使是整型（bool /null）这种可能并不需要此类信息的数据；
        在使用对象或者资源时，直接引用会导致两次计数；
        某些间接访问需要一个更好的处理方式。比如现在访问存储在变量中的对象间接使用了四个指针（指针链的长度为四）；
        直接计数也就意味着数值只能在 zval 之间共享。如果想在 zval 和 hashtable key 之间共享一个字符串就不行（除非 hashtable key 也是 zval）。
- PHP 7 中的 zval 数据结构实现的调整：

        最基础的变化就是 zval 需要的内存 不再是单独从堆上分配，不再由 zval 存储引用计数。
        复杂数据类型（比如字符串、数组和对象）的引用计数由其自身来存储。
- 这种实现的优势：

        简单数据类型不需要单独分配内存，也不需要计数；
        不会再有两次计数的情况。在对象中，只有对象自身存储的计数是有效的；
        由于现在计数由数值自身存储（PHP 有 zval 变量容器存储），所以也就可以和非 zval 结构的数据共享，比如 zval 和 hashtable key 之间；
        间接访问需要的指针数减少了。

#### 内核 - Zend（语言引擎）
##### php脚本解析步骤
- 解释器部分：分析输入代码，翻译代码，然后执行代码
- 功能部分：完成语言的功能（函数，等等）
- 接口部分：与web通信，等等

#### PHP-FPM 进程管理器
##### 进程管理器分类
- CGI
   - 定义：是Web Server 与 Web Application 之间数据交换的一种协议（也就是规定了传递数据的格式）
   - 原理：Web Brower(浏览器/客户端) ----(通过http协议传输) ---> Http Server(服务器nginx/apache) ---> CGI Program（创建新进程，初始化配置、环境） ---> 处理请求，返回结果 --->退出进程
- FastCGI
   - 定义：同CGI，是一种通讯协议，但比 CGI 在效率上做了一些优化。同样 SCGI和 FastCGI 类似
   - 产生：CGI 每次处理都要创建新进程，初始化，处理退出进程，会给服务器造成很大压力，从而产生了 FastCGI （相当于一个常驻的 CGI）
   - 原理：FastCGI 会先启动一个 master 进程用来初始化配置、环境 ---> 当有请求过来是启动多个子进程worker处理，而且当worker不够用时，master可以根据配置预先启动几个worker等着，当空闲的worker太多时，也会停掉一些，这样就提高了性能节约资源
- PHP-CGI
   - 定义：是PHP（Web Application）对 Web Server 提供的 CGI 协议的接口程序
   - 原理：PHP-CGI  就是 php 自带的 FastCGI 管理器
   - 问题：
   变更php.ini之后需要重启才能使新的php.ini生效，不能平滑重启
   若直接杀死php-cgi，php便无法运行
- PHP-FPM
   - 定义：是PHP（Web Application）对 Web Server 提供的 CGI 协议的接口程序，额外提供了相对智能一些的任务管理
   - 原理：Web Brower(浏览器/客户端) ----(通过http协议传输) ---> Http Server(服务器nginx/apache) ---> 配置解析，路由到index.php --- 加载 nginx 的 fast-cgi 模块 ---> fast-cgi 监听 127.0.0.1:9000 地址 ---> 通过 fast-cgi 协议将请求转发给 php-fpm 处理 ---> php-fpm 监听 127.0.0.1:9000 ---> php-fpm 启动 worker进程处理请求 ---> 返回nginx ---> nginx通过http返回客户端
   - 基本实现：
   创建master进程，在master进程中创建并监听socket，然后fork（分叉）出多个子进程
   子进程各自accept（接收）请求，它在启动后阻塞在accept上，有请求到达后开始读取请求数据，读取完成后开始处理然后再返回，在这期间是不会接收其它请求的，也就是说fpm的子进程同时只能响应一个请求
   - 改进：
   PHP-CGI 的升级版，提供了进程管理功能
   加入守护进程，即使被杀死之后也能快速重启
   master进程只有一个，负责监听端口、cgi、php环境初始化、子进程状态，接受来自web server服务器的请求，而worker进程则一般有多个(具体数量根据实际需要配置)，负责处理php请求，每个进程内部嵌入了一个php解释器，是php代码真正执行的地方

##### FPM进程管理模式
- static: 静态进程管理，在启动时master按照pm.max_children配置fork出相应数量的worker进程，即worker进程数是固定不变的
- dynamic: 动态进程管理，首先在fpm启动时按照pm.start_servers初始化一定数量的worker，运行期间如果master发现空闲worker数低于pm.min_spare_servers配置数(表示请求比较多，worker处理不过来了)则会fork worker进程，但总的worker数不能超过pm.max_children，如果master发现空闲worker数超过了pm.max_spare_servers(表示闲着的worker太多了)则会杀掉一些worker，避免占用过多资源，master通过这4个值来控制worker数
- ondemand: 这种方式一般很少用，在启动时不分配worker进程，等到有请求了后再通知master进程fork worker进程，总的worker数不超过pm.max_children，处理完成后worker进程不会立即退出，当空闲时间超过pm.process_idle_timeout后再退出

#### 反射
- 主要目的
   - 在运行时分析类或者对象的状态，导出或提取出关于类、方法、属性、参数等的详细信息，包括注释
   - 这种动态获取信息以及动态调用对象方法的功能，被称为反射API
- 运用
   - 反射在日常的 Web 开发中其实用的不多，更多的是在偏向底层一些的代码中，比如说框架的底层中依赖注入、对象池、动态代理、自动获取插件列表、自动生成文档以及一些设计模式等等
- 作用
   - 获取类的信息
   - 执行类的方法
   - 执行类的方法
   - 依赖注入（结合服务容器使用）

#### 工作模式
- CGI通用网关接口模式
- CGI通用网关接口模式
- CLI命令行模式
- MOD_PHP模块模式

#### 多进程，多线程

### 设计模式
#### 创建型（专注于如何实例化对象或相关对象组）
##### √ 单例模式（Singleton）
    确保只创建特定类的一个对象
    三私一公：私有化一个属性用于存放唯一的一个实例；私有化构造方法；私有化克隆方法，用来创建并只允许创建一个实例；公有化静态方法，用于向系统提供这个实例
##### 简单工厂模式（Simple Factory）
    只为客户端生成一个实例，而不会向客户端公开任何实例化逻辑
##### 工厂方法模式（Factory Method）
    将实例化逻辑委托给子类的方法
##### 抽象工厂模式（Abstract Factory）
    抽象工厂模式（Abstract Factory）
##### √ 构建器模式
    将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示
##### √ 原型模式（Prototype）
    通过克隆基于现有对象创建对象

#### 结构型（主要涉及对象组成）
##### √ 适配器模式（Adapter）
    将某个对象的接口适配为另一个对象所期望的接口
##### 将某个对象的接口适配为另一个对象所期望的接口
    将抽象与实现解耦，使得两者可以独立的变化
##### 组合模式（Composite）
    使得用户对单个对象和组合对象的使用具有一致性
##### 装饰模式（Decorator）
    允许向一个已有的对象添加新的功能或部分内容，同时又不改变其结构
##### √ 门面模式（Facade）
    为复杂的子系统提供了简化的界面
##### 享元模式（Flyweight）
    它用于通过尽可能多地与类似对象共享来最小化内存使用或计算开销
##### √ 代理模式（Proxy）
    构建了透明置于两个不同对象之内的一个对象，从而能够截取或代理这两个对象间的通信或访问

#### 行为型（关注对象之间的职责分配。它们与结构模式的不同之处在于它们不仅指定了结构，还概述了它们之间的消息传递 / 通信模式）
##### √ 责任链模式（Chain Of Responsibilities）
    请求从一端进入并继续从一个对象到另一个对象，直到找到合适的处理程序
##### 命令行模式（Command）
    允许您将操作封装在对象中。这种模式背后的关键思想是提供将客户端与接收器分离的方法
##### 迭代器模式（Iterator）
    提供了一种访问对象元素而不暴露底层表示的方法
##### 中介者模式（Mediator）
    添加第三方对象（称为 mediator）来控制两个对象（称为同事）之间的交互。它有助于减少彼此通信的类之间的耦合
##### 备忘录模式（Memento）
    是关于以稍后可以以平滑方式恢复的方式捕获和存储对象的当前状态
##### √ 观察者模式（Observer）
    定义对象之间的依赖关系，以便每当对象更改其状态时，都会通知其所有依赖项
##### 访问者模式（Visitor）
    允许您向对象添加更多操作，而无需修改它们
##### √ 策略模式（Strategy）
    允许您根据情况切换算法或策略
##### 状态模式（State）
    允许您在状态更改时更改类的行为
##### 模板方法模式（Template Method）
    定义了如何执行某个算法的框架，但是将这些步骤的实现推迟到子类

### 框架
#### Laravel
- 核心架构
    1. 生命周期
        【创建一个应用程序 / 服务容器】：public/index.php 文件加载 Composer 生成的自动加载设置，然后从 bootstrap/app.php 脚本中检索 Laravel 应用程序的实例， Laravel 本身采取的第一个动作是创建一个应用程序 / 服务容器
        【HTTP / Console 内核】：app/Http/Kernel.php类定义了一个 bootstrappers 数组。 这些 bootstrappers 配置了错误处理， 日志， 检测应用环境，中间件，以及其它在请求被处理前需要执行的任务；可以把该内核想象作一个代表整个应用的大黑盒子，输入 HTTP 请求，返回 HTTP 响应
        【服务提供者（关键）】：config/app.php 配置文件中的 providers 数组中。 第一步，所有服务提供者的 register 方法会被调用，然后一旦所有服务提供者均注册后， boot 方法才被调用
        【请求调度】：一旦启动且所有服务提供者被注册，Request 会被递送给路由
    2. 服务容器
        一个用于管理类依赖以及实现依赖注入的强有力工具，类的依赖通过构造函数，或者某些情况下通过 「setter」 方法 「注入」到类中
    3. 服务提供者
        服务提供者是所有 Laravel 应用程序的引导中心，引导可以理解为注册
    4. 门面 Facades
        为应用的 服务容器 提供了一个「静态」 接口
    5. 契约
        契约是一组接口，它们由框架提供并定义了核心服务;和 Facades （不须要在你类中的构造函数去引用依赖）不同的是，契约允许你给自己的类定义明确的依赖

- 底层设计模式（核心）
    1. 类的反射和依赖注入
        - PHP反射是程序实现依赖注入的基础，也是Laravel的服务容器实现服务解析的基础。PHP具有完整的反射 API，提供了对类、接口、函数、方法和扩展进行逆向工程的能力
    2. 服务容器（IocContainer）
        - 服务容器是一个用于管理类依赖和执行依赖注入的强大工具
        - 控制反转(IOC)和依赖注入(DI)
           - 依赖注入和控制反转是对同一件事情的不同描述，只是它们描述的角度不同
           - 依赖注入是从应用程序的角度在描述，应用程序依赖容器创建并注入它所需要的外部资源；
           - 控制反转是从容器的角度在描述，容器控制应用程序，由容器反向的向应用程序注入应用程序所需要的外部资源。
    3. 服务提供器(ServiceProvider)
        - 服务提供器是所有 Laravel 应用程序引导中心。你的应用程序自定义的服务、第三方资源包提供的服务以及 Laravel 的所有核心服务都是通过服务提供器进行注册(register)和引导(boot)的
        - 服务提供器的注册过程：
           - 判断当前服务提供器是否被注册过，如注册过直接返回对象
           - 解析服务提供器
           - 调用服务提供器的 register 函数
           - 标记当前服务提供器已经注册完毕
           - 若框架已经加载注册完毕所有的服务容器，那么就启动服务提供器的 boot 函数，该函数由于是 call 调用，所以支持依赖注入。
        
#### ThinkPHP
- 核心架构（CBD）
    >核心（Core）：框架的核心代码
    行为（Behavior）：HOOK，在系统核心之上，设置了很多标签扩展位，而每个标签位置可以依次执行各自的独立行为
    驱动（ Driver ）：数据库驱动、缓存驱动、标签库驱动和模板引擎驱动，以及外置的类扩展

### 安全
#### 基本原则：不对外界展示服务器或程序设计细节（屏蔽错误），不相信任何用户提交的数据（过滤用户提交）
#### 分类
- xss
   - 原理：跨站脚本攻击，恶意攻击者往Web页面里插入恶意的Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的
   - 解决：过滤输入
- csrf
   - 原理：跨站请求伪造，伪装成受信任用户的请求来利用受信任的网站
   - 解决：
        使用token进行验证
        验证HTTP Referer字段。HTTP Referer字段，记录该HTTP请求的来源。如果来源是外部，那么就拒绝这个请求
        一般框架，像ThinkPHP、laravel有表单令牌可以做防范
- sql注入
   - 原理：通过把SQL命令插入到Web表单提交查询字符串，最终达到欺骗服务器执行恶意的SQL命令
   - 解决：
        php.ini 中把safe_mode 打开，关闭危险函数
        打开magic_quotes_gpc，打开后将自动把用户提交对sql的查询进行转换 比如把 ' 转为 \'等，这对防止sql注射有重大作用
        使用PDO预处理的方式，将输入的数值，绑定到参数
        使用htmlspecialchars()等函数进行转义。还可以对输入类型进行检测，类型转换
- ddos
   - 原理：分布式拒绝服务。DDoS攻击是指攻击者通过控制大量的僵尸主机，向被攻击目标发送大量精心构造的攻击报文，造成被攻击者所在网络的链路拥塞、系统资源耗尽，从而使被攻击者产生拒绝向正常用户的请求提供服务的效果
   - 解决：黑白名单

### 设计思想

### Socket
### 算法
#### 排序算法
- 冒泡排序
        思路分析：在要排序的一组数中，对当前还未排好的序列，从前往后对相邻的两个数依次进行比较和调整，让较大的数往下沉，较小的往上冒。即，每当两相邻的数比较后发现它们的排序与排序要求相反时，就将它们互换
- 选择排序
        思路分析：在要排序的一组数中，选出最小的一个数与第一个位置的数交换。然后在剩下的数当中再找最小的与第二个位置的数交换，如此循环到倒数第二个数和最后一个数比较为止
- 插入排序
        思路分析：在要排序的一组数中，假设前面的数已经是排好顺序的，现在要把第n个数插到前面的有序数中，使得这n个数也是排好顺序的。如此反复循环，直到全部排好顺序
- 快速排序
        思路分析：选择一个基准元素，通常选择第一个元素或者最后一个元素。通过一趟扫描，将待排序列分成两部分，一部分比基准元素小，一部分大于等于基准元素。此时基准元素在其排好序后的正确位置，然后再用同样的方法递归地排序划分的两部分

-----
     
### 常见面试问题

#### Get与post两种方式的区别？
> 1. 传输方式：
>    -	get通过URL传递，可见
>    -	post是通过Request body传递，不可见
> 2. 传输数据大小：
>    - get一般传输数据大小不超过2k-4k；
>    - post 请求传输数据的大小根据php.ini 配置文件设定，也可以无限大。
> 3. GET产生一个TCP数据包，POST产生两个TCP数据包

#### session与cookie的区别与联系？
> - 区别
> 1. 存放位置：session保存在服务器；cookie保存在客户端
> 2. 存放的形式：session是以对象的形式保存在服务器；cookie以字符串的形式保存在客户端
> 3. 用途：session适合做客户的身份验证；cookie适合保存用户个人设置，爱好等
> 4. 路径：session不能区分路径，同一个用户在防问一个网站期间，所有的session在任何地方都可以访问到；而cookie如果设置了路径参数，那么在用一个网站中不能路径下的cookie相互访问不到
> 5. 安全性：cookie不安全，可以分析存放在本地的cookie并进行cookie欺骗
> 6. 大小以及数量限制：Cookie有限制，各个浏览器不同。一般认为 Session 没有大小和数量限制
> - 联系：Session 需要借助 Cookie 才能正常工作，SessionID存放在cookie，当然也可通过get传递SessionID获取已经保存的session内容

#### 为什么session的安全性大于cookie？
> - sessionID存储在cookie中，若要攻破session首先要攻破cookie；
> - sessionID是要有人登录，或者启动session_start才会有，所以攻破cookie也不一定能得到sessionID；
> - 第二次启动session_start后，前一次的sessionID就是失效了，session过期后，sessionID也随之失效
> - sessionID是加密的

#### echo、print、print_r、printf、var_dump的区别？
> - echo：语句结构，无返回值
> - print：是函数，打印字符串，有返回值
> - printf()：源于C语言中的printf()。该函数输出格式化的字符串
> - print_r：能打印复合类型，如数组，对象
> - var_dump:能打印对象数组，并且带数据类型，判断一个变量的类型与长度

#### PHP 中传值与传引用的区别？
> - 按值传递：函数范围内对值的任何改变在函数外部都会被忽略
> - 按引用传递：函数范围内对值的任何改变在函数外部也能反映出这些修改
> - 优缺点：按值传递时，php必须复制值。特别是对于大型的字符串和对象来说，这将会是一个代价很大的操作。按引用传递则不需要复制值，对于性能提高很有好处

#### include和require的区别？
> - 在失败的时候：include产生一个warning（下面代码正常执行），而require产生直接产生错误中断
> - include有返回值，require没有

#### 获取客户端和服务端的IP？
> - 客户端：$_SERVER["REMOTE_ADDR"]
> - 服务端：$_SERVER["SERVER_ADDR"]

#### PHP 不使用第三个变量实现交换两个变量的值？
> list($b,$a)=array($a,$b);

#### php-fpm在请求链路的体现？
> 1. www.example.com
> 2. Nginx
> 3. 路由到www.example.com/index.php
> 4. 加载nginx的fast-cgi模块
> 5. fast-cgi监听127.0.0.1:9000地址
> 6. www.example.com/index.php请求到达127.0.0.1:9000
> 7. php-fpm 监听127.0.0.1:9000
> 8. php-fpm 接收到请求，启用worker进程处理请求
> 9. php-fpm 处理完请求，返回给nginx
> 10. nginx将结果通过http返回给浏览器

#### php-fpm有几种工作模式？
> PHP-FPM进程管理方式有
> 1. 动态（Dynamic）:均衡优先，适合小内存服务器，2g左右
>    - 优点：动态扩容，不浪费系统资源
>    - 缺点：所有worker都在工作，新的请求到来需要等待创建worker进程，最长等待1s（内部存在一个1s的定时器，去查看，创建进程），频繁启停进程消耗cpu，请求数稳定，不需要频繁销毁
> 2. 静态（Static）:性能优先， 适合大内存机器
>    - 优点：不用动态判断负载，提升性能
>    - 缺点：如果配置成static，只需要考虑max_children数量，数量取决于cpu的个数和应用的响应时间，一次启动固定大小进程浪费系统资源
> 3. 按需分配（Ondemand）:内存优先，适合微小的内存，2g以下【不推荐】
>    - 优点：按流量需求创建，不浪费系统资源
>    - 缺点：因为php-fpm是短连接的，如果每次请求都先建立连接，大流量场景下会使得master进程变得繁忙，浪费cpu，不适合大流量模式

#### 怎么理解 依赖注入(DI)与控制反转(Ioc)？

#### php的弱类型是怎么实现的？
> php是通过c语言进行实现，但是c语言为强类型，那php的弱语言类型是通过PHP底层设计了一个zval(“Zend value”的缩写)的数据结构,可以用来表示任意类型的PHP值
> zval结构如下：

属性名	|含义|	默认值
:--:|:--:|:--:
refcount__gc|	表示引用计数	|1
is_ref__gc|	表示是否为引用	|0
value|	存储变量的值	|
type|	变量具体的类型	|

### 参考资料

[PHPer面试2021](https://www.kancloud.cn/martist/phper-will-get-bat-tmd-offer-in-2021)

[垃圾回收机制](https://www.php.net/manual/zh/features.gc.php)
[PHP 垃圾回收与内存管理指引](https://learnku.com/articles/15582/php-garbage-collection-and-memory-management-guidelines)

[设计模式超级简单的解释](https://learnku.com/articles/24982)
[web开发者知识体系-设计模式](https://www.kancloud.cn/martist/ma_zhao_liu/376793)


