# 企业微信通讯录读写同步

本项目是使用[go-zero](https://go-zero.dev/cn/)框架编写的restful风格;本程序仅是[企业微信自建应用安全性升级上线通知（8月15日）](https://developer.work.weixin.qq.com/community/announcement/detail?content_id=16355305024656062098)后通讯录的同步读写的解决方案。

* 从2022年6月20号20点开始，除通讯录同步以外的基础应用（如客户联系、微信客服、会话存档、日程等），以及新创建的自建应用与代开发应用，调用该接口时，不再返回以下字段：头像、性别、手机、邮箱、企业邮箱、员工个人二维码、地址，应用需要通过[oauth2手工授权](https://developer.work.weixin.qq.com/document/path/90196#15232)的方式获取管理员与员工本人授权的字段。

* 【重要】从2022年8月15日10点开始，“企业管理后台 - 管理工具 - 通讯录同步”的新增IP将不能再调用此接口，企业可通过「[获取成员ID列表](https://developer.work.weixin.qq.com/document/path/90196#40856)」和「[获取部门ID列表](https://developer.work.weixin.qq.com/document/path/90196#36259)」接口获取userid和部门ID列表。[查看调整详情](https://developer.work.weixin.qq.com/document/path/90196#40802)。

**2022年6月20号20点前创建的自建应用读取成员信息没有任何限制，不适用于本程序！！！**

## 使用方法

### 数据库配置
请修改`etc/wecom-api.yaml`下的`DataSource`配置为自己的数据库连接信息(仅支持Mysql)

### 项目启动
下载对应平台下的编译好的二进制文件
以linux为例，下载`wecom-zero-linux-amd64`后在命令行启动

    ./wecom-zero-linux-amd64 -f etc/wecom-api.yaml

因为程序默认的配置文件就是　`etc/wecom-api.yaml`,如果配置文件路径和名称对应，上面的启动命令也可以简化为

    ./wecom-zero-linux-amd64

**注意**：因为使用的[wecom-go-sdk](https://github.com/go-laoji/wecom-go-sdk)会在本地生成`cache.db`目录用来缓存一些信息，请确保启动服务的系统用户在当前目录下有写权限

### 企业微信应用配置

项目启动后会在相应数据库中生成对应的数据表,项目启动后需要在`corp_config`表中配置应用信息

| id            | corp_id       |  agent_callback_token | agent_callback_key| agent_secret    |agent_id      |
|:-------------:|:-------------:|:---------------------:|:-----------------:|:---------------:|:------------:|
| 1             | 企业的corpid   | 通讯录应用的token       | 通讯录应用的aeskey  |通讯录应用的secret | 置空　　       |
| 2　　　　　　　 | 企业的corpid   |　自建应用的token        |　自建应用的aeskey   |自建应用的secret   | 自建应用agentid|

**注意**：配置中的`id`不能随意修改,编号1必需是`通讯录同步助手`的配置;编号2必需是`自建应用`配置

### 企业微信后台配置

* 通讯录同步助手回调地址URL: `http(s)://yourdomain.com/wecom/callback/1`
* 自建应用回调地址URL: `http(s)://yourdomain.com/wecom/callback/2`
* 配置两个应用的可信IP为部署程序的出口IP地址
* 设置自建应用的应用主页地址为`https://yourdomain.com/wecom/home`,成员进入应用会调起敏感数据授权弹窗

需要配合[nginx配置](nginx.conf.template)进行域名解析及端口代理；其它web服务器请自行适配！

## 接口调用

支持接口请参考[apifox](https://www.apifox.cn/apidoc/shared-0c469679-94d7-4871-8738-d2fd78e098a0)完成调试！

## 联系方式

更多信息请关注公众号与我联系！
![wechat](https://github.com/go-laoji/wecom-go-sdk/raw/develop/qrcode.bmp)