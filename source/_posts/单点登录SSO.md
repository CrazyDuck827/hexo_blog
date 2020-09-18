---
title: 单点登录SSO
categories: 
    - SSO
tags:
    - SSO
keywords: 
    - SSO
    - 单点登录
    - CAS
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/up-d764cee306c088e88dc2e9dd3d166e6e761.png
---

### 概念

    多个应用系统间，用户只需要登录一次就可以访问所有相互信任的应用系统

### 单系统登录

#### 流程图

![单系统登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/77c39456-2d6e-43c4-ac34-201b9edfa23b.jpg)

#### 理解漫画

![单系统登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/eb01e898-de58-4882-a6d6-7bd58fbae726.jpg)
![单系统登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/6cbf867e-0ca2-462d-a2f4-8d5458023d72.jpg)
![单系统登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/c2058a67-d694-4600-a632-abf37803360a.jpg)
![单系统登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/5b6f1b98-d0d3-448c-bf57-2301568e67a2.jpg)

### 单点登录

#### CAS

    CAS（Central Authentication Service），即中央认证服务，是 Yale 大学发起的一个开源项目，旨在为 Web 应用系统提供一种可靠的单点登录方法。
    
#### 流程图

![单系统登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/ebf178b6-0950-482b-8031-5bb70b4888d5.jpg)

#### 理解漫画

![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/c51d357e-cb38-44b7-ab62-fe1851de7241.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/59097fe8-a985-41cc-9061-9a180afe4e40.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/8acda14c-ce75-44d1-bdfc-15fd2cfdcf02.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/64c6735b-a718-421e-96bf-dd194e2e6ef3.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/6d24b4ec-9aea-4598-8aed-19394dbaa7da.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/69527a5d-63ca-4a6c-b7ec-d18184103f2e.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/d920d4a4-8dfe-425c-b60f-cd956914962e.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/f93b4062-239f-44d5-af0c-c60926f168cd.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/727aed80-e096-42a3-b5bf-e3e808b63caa.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/f15c5fd7-9148-46dc-bedf-5aa76f981ad5.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/27aaf03e-20e2-40c7-b111-f4bda27484cd.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/87dbaf4b-1dca-4468-8b52-b872d118bed5.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/a24727b4-6ac4-4ef0-ad55-9f853591ecd2.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/23a59adc-351c-426f-94b7-0c6b2c73f17e.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/b51ef4e6-4056-467c-818c-15fb2c70e4b7.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/87dbaf4b-1dca-4468-8b52-b872d118bed5.jpg)
![单点登录](https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/image/b681aad5-9875-467c-96a3-39f9c8da3392.jpg)

#### 注意

- 所有的登录过程都依赖于 CAS 服务，包含用户登录页面、ST 生成、验证

- 为了保证 ST 的安全性，一般 ST 都是随机生成的，没有规律性。CAS 规定 ST 只能保留一定的时间，之后 CAS 服务会让它失效，而且，CAS 协议规定 ST 只能使用一次，无论 ST 验证是否成功，CAS 服务都会清除服务端缓存中的该 ST，从而规避同一个 ST 被使用两次或被窃取的风险

- 用户推出时，通知所有注册系统注销

### 参考资料

[图文并茂，为你揭开“单点登录“的神秘面纱](https://www.zoo.team/article/sso)
[单点登录原理与简单实现](https://www.cnblogs.com/ywlaker/p/6113927.html)
