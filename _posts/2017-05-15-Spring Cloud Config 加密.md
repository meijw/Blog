---
layout: post

title:  "Spring Cloud Config 加密"

date:   2017-05-15

excerpt: "配置完Spring Cloud Config后所有配置文件存储在Git上（包括密码、账号等）通过查看git就能敏感信息。"

tag:
- Spring Cloud
- Spring Cloud Config

comments: false

---

# 使用情景

配置完Spring Cloud Config后所有配置文件存储在Git上（包括密码、账号等）通过查看git就能敏感信息。

# 升级JCE环境

1.  下载：JAVA8 JCE 地址： [java8 JCE下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)

2.  上述链接下载解压后拷贝到 **JDK/jre/lib/security**.

# KeyStore配置

1.  使用keytool生成 KeyStore

>   keytool -genkeypair -alias speed4j-config-server -keyalg RSA -dname "CN=Web Server,OU=Unit,O=Organization,L=City,S=State,C=US" -keypass speed4j -keystore speed4j-config-server.jks -storepass speed4j

# 配置application.properties

```properties
encrypt.key=speed4j
encrypt.key-store.location=speed4j-config-server.jks
encrypt.key-store.alias=speed4j-config-server
encrypt.key-store.password=speed4j
encrypt.key-store.secret=speed4j
```

# 开启Spring Cloud Server 加密密码

1.  访问链接获取加密后的密码（encrypt-password）  
    >curl -u [{username}:{password}] {config:host}:{port}/encrypt -d {your-password}

2.  如果JCE没有成功安装运行上述命令spring cloud server会报invalid key size的错误（还未下载JCE之前生成的key位数不符合Spring Cloud Config长度限制（太短））

3.  获取加密后的密码，在项目的application.properties中修改密码

4.  将加密后的密码复制并在前面添加｛cipher｝字段，spring cloud server就知道这是加密的字段  
    > datasource.password = {cipher}encrypt-password
