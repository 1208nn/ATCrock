---
title: 如何在docker和本地环境运行jh论坛
date: 2025-10-25 01:24:47
tags: jhlt
category: tech
---

# 如何在docker和本地环境运行jh论坛

## docker&本地环境

用`idea/vs`打开项目，先修改`application.yml`（那些账号密码什么的都换一下），然后在终端里面输入`docker compose up`（记得打开`docker`），然后等待项目挂起就行。挂起后，在本地运行论坛即可

如果出现`failed to do request: Get…………error`，说明网络环境不稳定，可以多试几次

如果出现
> ✘ sentinel-dashboard Error     unknown: failed to resolve reference "docker.io/bladex/sentinel-dashboard:1.8.8": unexpected status from HEAD re...                          10.4s
> Error response from daemon: unknown: failed to resolve reference "docker.io/bladex/sentinel-dashboard:1.8.8": unexpected status from HEAD request to https://docker.m.daocloud.io/v2/bladex/sentinel-dashboard/manifests/1.8.8?ns=docker.io: 403 Forbidden

这种情况，可能是镜像源或网络配置有问题。优先查看是否是镜像源配置问题，一般网络配置不会有那么大影响

如果出现`error from registry: 🚫-> https://github.com/DaoCloud/public-image-mirror/issues/2328 🔗 这镜像不在白名单. this image is not in the allowlist.`，解决方法和上述同样，检查镜像源是否可用。

## 纯本地环境

### 前期准备

#### 配置`nacos`

`nacos`安装完后，需要把`conf/application.properties`里面的`mode`改成`standalone`，然后需要设置`nacos`账号密码(需要在项目的`application.yml`里面同步修改，否则会报错)

`nacos`运行地址修改：在`conf/application.properties`中添加`nacos.inetutils.ip-address=127.0.0.1`(也可以是别的，但是本地运行就挂本地的就行)，需要在项目的`application.yml`里面同步修改这个地址

#### 配置`redis`

`redis`安装后，可以选择不设置密码，并把项目的`application.yml`里面的`data:redis:password`注释掉；若设置密码，则在该处添加你的密码

### 项目的`application.yml`修改

除了上述的`nacos`和`redis`配置的修改，还需要对其他内容进行配置

1.直接打开项目时，`application`的名字还是`application.example.yml`，需要去掉`example`

(该处原为`nacos:nacos-config-application-example.properties`，需要改掉文件名)

```yml
spring:
  config:
    import: "nacos:nacos-config-application.properties?refresh=true"
```

2.源的`cube`和`user-center`配置是空的（在配置的最下面），需要相应配置请联系论坛开发人员

3.由于**~~不知名~~原因**，需要把配置`dubbo`里面`protocol`的`tri`改成`dubbo`协议，这样`dubbo`才能正常连接

```yml
dubbo:
  protocol:
    name: tri->dubbo
```

### 启动前准备

1. 由于论坛的`dubbo`配置口在`50052`，可以先在终端内查询这个端口是否被占用，若被占用则先关闭这个端口，然后再运行论坛，否则`dubbo`会因为端口被占用而无法正常启动

2. 启动前需要**先启动本地的`nacos`和`redis`**

- **`nacos`启动成功的标志**：`startup.sh`打开的命令行最后一句会有`Nacos started successfully in stand alone mode. use embedded storage`这一行

- **`redis`启动成功的标志**：`redis.cli`打开后不会马上闪退，而是会显示`redis`配置中的`IP`地址（如`127.0.0.1>`）

3. 这一行的最后，加了`&allowPublicKeyRetrieval=true`，源配置文件里面没有这一个，可以加上

        datasource:
          url: jdbc:mysql://your_ip/your_database?useSSL=false&autoReconnect=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true

如果一切就绪，点击运行后等待加载，直至出现“帖子热度计算中”这一类字眼，并短期内不报错，则论坛成功运行
