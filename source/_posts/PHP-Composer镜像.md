---
title: PHP-Composer镜像
date: 2019-11-18 12:30:39
toc: true
categories: 
    - PHP
tags:
    - PHP
    - Composer
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/ns5avppS3yMh73CFjc8pGSU6wZMtCXu1.png
---

### Composer镜像

镜像名 | 地址 | 赞助商 | 更新频率 | 备注
---|:--:|---:|---:|---:
阿里云 Composer 镜像 | 	https://mirrors.aliyun.com/composer | 阿里云 | 96 秒 | 推荐
腾讯云 Composer 镜像 | 	https://mirrors.cloud.tencent.com/composer | 腾讯云 | 24 小时 | -
PHP 国内 Composer 镜像 | 	https://packagist.phpcomposer.com | 仁润股份 | 24 小时 | 不稳定
华为云 Composer 镜像 | 	https://repo.huaweicloud.com/repository/php | 未知 | 96 秒 | -
php.cnpkg.org Composer 镜像 | 	https://php.cnpkg.org | 安畅网络 | 60 秒 | -

### 配置镜像

- 全局命令
```
    composer config -g repos.packagist composer https://mirrors.aliyun.com/composer
```

- 项目composer.json
```
    "repositories": {
        "packagist": {
            "type": "composer",
            "url": "https://mirrors.aliyun.com/composer/"
        }
    }
```

### 多线程下载
- 全局安装插件：hirak/prestissimo
```
    composer global require hirak/prestissimo
```