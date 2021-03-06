# 基于SAML的IDP设计

	本项目是为毕业设计使用的，没有其他用途

## 模型说明

通常情况下，一个公司内部的服务需要单独管理用户，而SSO的出现使得公司可以统一管理用户，一个用户在一个公司内只需要一个帐号。

此时当用户需要登录的时候，会跳转到一个IDP服务器上进行登录，登录成功后，IDP向原服务器返回登录数据。

但是此时每个公司仍旧需要一个独立的用户管理系统。

而此项目将不同公司间的IDP服务器进行 **统一管理** 。

提供给每个公司一个 **虚拟** 的IDP服务器。

公司内部用户在访问资源的时候先跳转到虚拟的IDP服务器进行登录，然后再返回到资源服务器。

优点：公司 **不需要** 自行管理用户服务器，降低数据丢失和内部人员盗窃数据的风险，降低管理成本。

缺点：将用户托管在云端，这本身就是一种风险,一旦云端服务器被攻破，所有公司的数据都会被获取到。而且当断网的时候无法登录。

### 模型图

![模型图](http://ww4.sinaimg.cn/large/a74ecc4cjw1e3r8nns2jvj.jpg "设计模型图")

## LDAP

LDAP是轻量级目录访问协议的简称,用于访问目录服务，使用树状的层次结构来查看数据,每个用户都是其中的一个节点，可以将其类比成UNIX文件树。  

## 目录型数据库和关系型数据库的区别

* 目录查询操作比关系数据库有更高的效率，但是更新效率比关系数据库低
* 目录不支持关系数据库那样的复杂查询，比如两个表的连接。
* 目录不支持多操作的事物完整性，没有方式确认一些操作是全部成功还是全部失败
* 目录能够能好和更灵活的支持子查询和匹配查询
* 目录协议更适合应用于广域网，比如因特网或者大型公司的网络
* 目录的管理，配置，和调试比关系型数据库更简单
* 在使用关系数据库之前，必须首先定义表结构(模式)才可以进行操作。而目录中所使用的模式是由LDAP定义好的一系列类组成的。对于目录中的每条记录中必须属于其中的一个类或者多个类。这些类定义了该记录中可以存储的信息。
* 目录以对象的形式存储数据。信息被组织成树型结构。
* 目录服务支持分布式存储结构，容易实现数据的扩展，能满足大容量存储的要求。


## LDAP优点

1. 读取性能优越，适合大量读的场合
2. 应用程序不需要存储数据库帐号，安全
3. 通过不同的分支可以进行逻辑隔离
4. 非常方便的管理用户和组织

尤其是其安全功能，是本项目的重点。  
传统的DBMS都需要应用程序存储一个数据库帐号，一旦应用程序被攻破，数据库也随之被攻破。而LDAP并不存在数据库帐号，LDAP中用户既是数据，又是帐号，每个用户可以使用自己的帐号去连接LDAP数据库，同样，管理员帐号也作为LDAP中的一个节点存在。但是也正是这种局限性，这套系统有一些功能无法实现。  
用户身份验证可以使用多种方法，最基本的如密码验证，高级一点的还支持证书验证和OTP验证。

## 功能说明

### 超级管理员提供的功能

1. 超级管理员自身管理  
    * 登录
    * 注销
    * 修改密码

2. 配置各个租户的信息
    * 新建租户 
    * 修改租户 
    * 查看租户列表 
    * 查看网站统计数据
    * 配置各个租户的管理员

    服务器通过租户名生成一个唯一的IDP URL，同时生成LDAP数据库中一个分支，各租户间的信息相互隔离


### 给租户管理员提供的功能

1. 管理员自身功能
    * 登录
    * 注销
    * 修改密码
2. 群组管理
    * 新建群组
    * 修改群组
    * 删除群组
    * 查看群组
3. 用户管理
    * 新建用户
    * 修改用户
    * 删除用户
    * 查看用户
4. 用户群组管理
    * 设置用户所属的群组
    * 设置SAML字段


### 给用户提供的功能

1. 用户注册
2. 用户登录，注销
3. 用户基本属性修改 

### 运行流程

![流程图](http://ww4.sinaimg.cn/large/a74eed94jw1e3qnc3hkaoj.jpg "运行流程图")
