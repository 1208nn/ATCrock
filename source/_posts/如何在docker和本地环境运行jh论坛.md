# 						

# 			如何在docker和本地环境运行jh论坛



## docker&本地环境

用idea/vs打开项目，先修改application.yml（那些账号密码什么的都换一下），然后在终端里面输入docker compose up（记得打开docker），然后等待项目挂起就行。挂起后，在本地运行论坛即可

如果出现	failed to do request: Get…………error，说明网络环境不稳定，可以多试几次

如果出现	 ✘ sentinel-dashboard Error     unknown: failed to resolve reference "docker.io/bladex/sentinel-dashboard:1.8.8": unexpected status from HEAD re...                          10.4s 
Error response from daemon: unknown: failed to resolve reference "docker.io/bladex/sentinel-dashboard:1.8.8": unexpected status from HEAD request to https://docker.m.daocloud.io/v2/bladex/sentinel-dashboard/manifests/1.8.8?ns=docker.io: 403 Forbidden 这种情况，可能是镜像源或网络配置有问题。优先查看是否是镜像源配置问题，一般网络配置不会有那么大影响

如果出现error from registry: 🚫-> https://github.com/DaoCloud/public-image-mirror/issues/2328 🔗 这镜像不在白名单. this image is not in the allowlist.，解决方法和上述同样，检查镜像源是否可用。



## 纯本地环境

### 前期准备

**下载nacos，redis**

nacos安装完后，需要把conf/application.properties里面的mode改成standalone，然后需要设置nacos账号密码(需要在项目的application.yml里面同步修改，否则会报错)

nacos运行地址修改：在conf/application.properties中添加nacos.inetutils.ip-address=127.0.0.1(也可以是别的，但是本地运行就挂本地的就行)，需要在项目的application.yml里面同步修改这个地址

redis安装后，可以选择不设置密码，并把项目的application.yml里面的data:redis:password注释掉；若设置密码，则在该处添加你的密码

### 项目的application.yml修改

除了上述的nacos和redis配置的修改，还需要对其他内容进行配置

1.直接打开项目时，application的名字还是application.example.yml，需要去掉example

(该处原为"nacos:nacos-config-application-example.properties，需要改掉文件名")

```
spring:
```

```
config:
  import: "nacos:nacos-config-application.properties?refresh=true"
```

2.源的cube和user-center配置是空的（在配置的最下面），需要相应配置请联系论坛开发人员

3.由于**不知名原因**，需要把配置dubbo里面protocol的tri改成dubbo协议，这样dubbo才能正常连接

```
dubbo:
  protocol:
    name: tri->dubbo
```



### 启动前准备

1.由于论坛的dubbo配置口在50052，可以先在cmd中查询这个端口是否被占用，若被占用则先关闭这个端口，然后再运行论坛，否则dubbo会因为端口被占用而无法正常启动

2.启动前需要**先启动本地的nacos和redis**

**nacos启动成功的标志**：startup.sh打开的命令行最后一句会有“Nacos started successfully in stand alone mode. use embedded storage”这一行

**redis启动成功的标志：**redis.cli打开后不会马上闪退，而是会显示redis配置中的ip地址（如127.0.0.1>）

3.

```
datasource:
  url: jdbc:mysql://your_ip/your_database?useSSL=false&autoReconnect=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
```

这一行的最后，加了&allowPublicKeyRetrieval=true，源配置文件里面没有这一个，可以加上



如果一切就绪，点击运行后等待加载，直至出现“帖子热度计算中”这一类字眼，并短期内不报错，则论坛成功运行
