# 如何提交自己想要的应用

## 4. 创建应用文件 (以 Halo 为例)



v1.3 及以上版本可以在 1Panel 宿主机使用 1panel app init <应用的key> <应用的版本> 来快速初始化应用文件 (注意不是 1pctl 命令)

文件夹格式

```
├──halo // 以 halo 的 key 命名 ，下面解释什么是 key 
	├── logo.png // 应用 logo , 最好是 180 * 180 px
	├── data.yml // 应用声明文件
	├── README.md // 应用的 README
	├── 2.2.0 // 应用版本 注意不要以 v 开头
	│   ├── data.yml // 应用的参数配置，下面有详细介绍
	│   ├── data // 挂载出来的目录 
	|   ├── scripts // 脚本目录 存放 init.sh upgrade.sh uninstall.sh
	│   └── docker-compose.yml // docker-compose 文件
	└── 2.3.2
	    ├── data.yml
	    ├── data
	    └── docker-compose.yml
```



应用声明文件 data.yml

> 本文件主要用于声明应用的一些信息

```
additionalProperties:  #固定参数
    key: halo   #应用的 key ，仅限英文，用于在 Linux 创建文件夹
    name: Halo  #应用名称
    tags:  
        - WebSite #应用标签，可以有多个，请参照下方的标签列表 
    shortDescZh: 强大易用的开源建站工具 #应用中文描述，不要超过30个字
    shortDescEn: Powerful and easy-to-use open source website builder #应用英文描述 
    type: website  #应用类型，区别于应用分类，只能有一个，请参照下方的类型列表
    crossVersionUpdate: true  #是否可以跨大版本升级
    limit: 0  #应用安装数量限制，0 代表无限制
    website: https://halo.run/  #官网地址
    github: https://github.com/halo-dev/halo #github 地址 
    document: https://docs.halo.run/ #文档地址
```



应用标签 - tags 字段（持续更新。。。）

| key      | name       |
| -------- | ---------- |
| WebSite  | 建站       |
| Server   | Web 服务器 |
| Runtime  | 运行环境   |
| Database | 数据库     |
| Tool     | 工具       |
| CI/CD    | CI/CD      |
| Local    | 本地       |

应用类型 - type 字段

| type    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| website | website 类型在 1Panel 中支持在网站中一键部署,wordpress halo 都是此 type |
| runtime | mysql openresty redis 等类型的应用                           |
| tool    | phpMyAdmin redis-commander jenkins 等类型的应用              |

应用参数配置文件 data.yml （注意区分于应用主目录下面的 data.yaml）

> 本文件主要用于生成安装时要填写的 form 表单，在应用版本文件夹下面 可以无表单，但是需要有这个 data.yml文件，并且包含 formFields 字段

以安装 halo 时的 form 表单 为例

![iShot_2023-03-18_14 03 43](https://user-images.githubusercontent.com/31820853/226111412-9c7b25a1-83f2-4621-8789-7ef85a2695dd.png)

如果要生成上面的表单，需要这么填写 data.yml

```
additionalProperties:  #固定参数
    formFields:  
        - default: ""  
          envKey: PANEL_DB_HOST  #docker-compose 文件中的参数
          key: mysql  #依赖应用的 key , 例如 mysql
	  labelEn: Database Service  #英文的label
	  labelZh: 数据库服务  #中文的label
	  required: true  #是否必填
          type: service  #如果需要依赖其他应用，例如数据库，使用此 type 
        - default: halo  
          envKey: PANEL_DB_NAME  
          labelEn: Database  
          labelZh: 数据库名  
          random: true  #是否在 default 文字后面，增加随机字符串
          required: true  
          rule: paramCommon  #校验规则
          type: text  #需要手动填写的，使用此 type
        - default: halo  
          envKey: PANEL_DB_USER  
          labelEn: User  
          labelZh: 数据库用户  
          random: true  
          required: true  
          rule: paramCommon  
          type: text  
        - default: halo  
          envKey: PANEL_DB_USER_PASSWORD  
          labelEn: Password  
          labelZh: 数据库用户密码  
          random: true  
          required: true  
          rule: paramComplexity  
          type: password  #密码字段使用此 type
        - default: admin  
          envKey: HALO_ADMIN  
          labelEn: Admin Username  
          labelZh: 超级管理员用户名  
          required: true  
          rule: paramCommon  
          type: text  
        - default: halo  
          envKey: HALO_ADMIN_PASSWORD  
          labelEn: Admin Password  
          labelZh: 超级管理员密码  
          random: true  
          required: true  
          rule: paramComplexity  
          type: password  
        - default: http://localhost:8080  
          edit: true  
          envKey: HALO_EXTERNAL_URL  
          labelEn: External URL  
          labelZh: 外部访问地址  
          required: true  
          rule: paramExtUrl  
          type: text  
        - default: 8080  
          edit: true  
          envKey: PANEL_APP_PORT_HTTP  
          labelEn: Port  
          labelZh: 端口  
          required: true  
          rule: paramPort  
          type: number #端口使用此 type 
```



关于端口字段：

1. PANEL_APP_PORT_HTTP 有 web 访问端口的优先使用此 envKey
2. envKey 中包含 PANEL_APP_PORT 前缀会被认定为端口类型，并且用于安装前的端口占用校验。注意：端口需要是外部端口

关于 type 字段：

| type     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| service  | `type: service` 如果该应用需要依赖其他组件，如 mysql redis 等，可以通过 `key: mysql` 定义依赖的名称，在创建应用时会要求先创建依赖的应用。 |
| password | `type: password` 敏感信息，如密码相关的字段会默认不显示明文。 |
| text     | `type: text` 一般内容，比如数据库名称，默认明文显示。        |
| number   | `type: number` 一般用在端口相关的配置上，只允许输入数字。    |
| select   | `type: select` 选项，比如 `true`, `false`，日志等级等。      |

简单的例子

```
# type: service，定义一个 mysql 的 service 依赖。
- default: ""  
    envKey: DB_HOST
    key: mysql
    labelEn: Database Service
    labelZh: 数据库服务
    required: true
    type: service

# type: password
- default: Np2qgqtiUayA857GpuVI0Wtg
    edit: true
    envKey: DB_PASSWORD
    labelEn: Database password
    labelZh: 数据库密码
    required: true
    type: password

# type: text
- default: 192.168.100.100
    disabled: true.
    envKey: REDIS_HOST
    labelEn: Redis host
    labelZh: Redis 主机
    type: text

# type: number
- default: 3306
    disabled: true
    envKey: DB_PORT
    labelEn: Database port
    labelZh: 数据库端口
    rule: paramPort
    type: number

# type: select
- default: "ERROR"
    envKey: LOG_LEVEL
    labelEn: Log level
    labelZh: 日志级别
    required: true
    type: select
    values:
        - label: DEBUG
          value: "DEBUG"
        - label: INFO
          value: "INFO"
        - label: WARNING
          value: "WARNING"
        - label: ERROR
          value: "ERROR"
        - label: CRITICAL
          value: "CRITICAL"
```



rule 字段目前支持的几种校验

| rule            | 规则                                                   |
| --------------- | ------------------------------------------------------ |
| paramPort       | 用于限制端口范围为 1-65535                             |
| paramExtUrl     | 格式为 http(s)://(域名/ip):(端口)                      |
| paramCommon     | 英文、数字、.-和_,长度2-30                             |
| paramComplexity | 支持英文、数字、.%@$!&~_-,长度6-30，特殊字符不能在首尾 |

应用 docker-compose.yml 文件

> ${PANEL_APP_PORT_HTTP} 类型的参数，都在 data.yml 中有声明

```
version: "3"  
services:  
  halo:  
    image: halohub/halo:2.2.0  
    container_name: ${CONTAINER_NAME}  // 固定写法，勿改
    restart: always  
    networks:  
      - 1panel-network  // 1Panel 创建的应用都在此网络下
    volumes:  
      - ./data:/root/.halo2  
    ports:  
      - ${PANEL_APP_PORT_HTTP}:8090  
    command:  
      - --spring.r2dbc.url=r2dbc:pool:${HALO_PLATFORM}://${PANEL_DB_HOST}:${HALO_DB_PORT}/${PANEL_DB_NAME}  
      - --spring.r2dbc.username=${PANEL_DB_USER}  
      - --spring.r2dbc.password=${PANEL_DB_USER_PASSWORD}  
      - --spring.sql.init.platform=${HALO_PLATFORM}  
      - --halo.external-url=${HALO_EXTERNAL_URL}  
      - --halo.security.initializer.superadminusername=${HALO_ADMIN}  
      - --halo.security.initializer.superadminpassword=${HALO_ADMIN_PASSWORD}  
    labels:  
      createdBy: "Apps"  
  
networks:  
  1panel-network:  
    external: true
```



## 5. 脚本



1Panel 在 安装之前、升级之前、卸载之后支持执行 .sh 脚本
分别对应 init.sh upgrade.sh uninstall.sh
存放目录(以halo为例) : halo/2.2.0/scripts

## 6. 本地测试



将应用目录上传到 1Panel 的 /opt/1panel/resource/apps/local 文件夹下
注意：/opt 为 1Panel 默认安装目录，请根据自己的实际情况修改
上传完成后，目录结构如下

```
├──halo 
	├── logo.png 
	├── data.yml
	├── README.md 
	├── 2.2.0 
	    ├── data.yml 
	    ├── data  
	    └── docker-compose.yml
```



在 1Panel 应用商店中，点击更新应用列表按钮同步本地应用

> v1.2 版本及之前版本的本地应用，请参考[这个文档](https://github.com/1Panel-dev/appstore/wiki/v1.2-版本本地应用升级指南)修改

## 7. 提交文件



```
git add .
git commit -m "Add my-app"
git push origin dev
```



## 8. 提交 Pull Request



- 在你的仓库点击 Pull requests 菜单
- 点击 New pull request ，填写标题和描述
- 选择由你的分支提交到 1Panel-dev/appstore