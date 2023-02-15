
## 简介

Keycloak 为现代应用和分布式服务提供了一套完整的认证授权管理解决方案，它是开源的，是一个独立的认证授权服务器。它主要是基于OAuth2协议的实现，同时提供了多种语言库，让我们可以很快速地根据我们的需求将Keycloak集成到我们的项目中去使用。

这里主要介绍Keycloak的一些基本使用实践，在学习Keycloak之前，最好要先了解OAuth2的协议流程，否则比较难以理解keycloak的认证过程。在熟悉了OAuth2协议以后去学习Keycloak，其实就很简单了。

若想了解OAuth2协议，可以看这篇 理解OAuth2协议

下面列出的是我所知道的Keycloak相关的内容，下面会详细介绍其中的某一些：

1. 是一个独立的认证授权服务器，提供完整的认证授权解决方案
2. 主要基于OpenID-Connect & SAML协议
3. 基本的登录注册，以及登录注册页面主题自定义
4. 很人性化的用户界面管理，比如用户、角色、session、Clients等等的管理
5. 具有独立的数据库，用于存储用户等认证授权数据
6. 支持联合数据存储，比如集成Ldap服务器；提供SPI扩展，比如user Storage SPI，可以让用户的一部分数据存储在你自己的数据库，一部分存储在keycloak自己的数据库
7. 提供多种语言库集成keycloak
8. 提供管理API，用于管理keycloak中所有的认证授权对象
9. Docker-compose一键安装，同时windows解压版解压后即可使用


## 启动Keycloak
[Docker - Keycloak](https://www.keycloak.org/getting-started/getting-started-docker)
从终端开始使用以下命令启动Keycloak：

```bash
docker run -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak:20.0.3 start-dev
```

这将启动Keycloak暴露在本地端口8080上。它还将创建一个具有用户名`admin`和密码`admin`初始管理员用户。

## 登录管理控制台

转到[Keycloak管理控制台](http://localhost:8080/admin)，并使用您之前创建的用户名和密码登录。

## 创建一个领域

Keycloak的领域相当于租户。它允许创建孤立的应用程序和用户组。默认情况下，Keycloak中只有一个名为`master`的领域。这专门用于管理Keycloak，不应用于您自己的应用程序。

让我们创造我们的第一个领域。

1.  打开[Keycloak管理控制台](http://localhost:8080/admin)
    
2.  将鼠标悬停在左上角显示`master`的下拉菜单上，然后单击`Create realm`
    
3.  用以下值填写表格：
    
    -   王国名称：`myrealm`
        
    
4.  点击`Create`
    

![添加领域](https://www.keycloak.org/resources/images/guides/add-realm.png)

## 创建用户

最初，新领域没有用户，所以让我们创建一个：

1.  打开[Keycloak管理控制台](http://localhost:8080/admin)
    
2.  点按“`Users`”（左侧菜单）
    
    -   点按“`Create new user`”（表格右上角）
        
    
3.  用以下值填写表格：
    
    -   用户名：`myuser`
        
    -   名字：你的名字
        
    -   姓氏：您的姓氏
        
    
4.  点击`Create`
    

![添加用户](https://www.keycloak.org/resources/images/guides/add-user.png)

用户需要设置初始密码才能登录。要做到这一点：

1.  点按“`Credentials`”（页面顶部）
    
2.  用密码填写`Set password`表格
    
3.  单击`Temporary`旁边的`ON`，以防止在首次登录时更新密码
    

![设置密码](https://www.keycloak.org/resources/images/guides/set-password.png)

## 登录帐户控制台

现在，让我们尝试登录帐户控制台，以验证用户配置是否正确。

1.  打开[钥匙斗篷帐户控制台](http://localhost:8080/realms/myrealm/account)
    
2.  使用`myuser`登录以及您之前创建的密码
    

您现在应该登录帐户控制台，用户可以在其中管理他们的帐户。

![Keycloak帐户控制台](https://www.keycloak.org/resources/images/guides/account-console.png)

## 保护您的第一个应用程序

让我们试着保护我们的第一个应用程序。第一步是使用Keycloak实例注册此应用程序：

1.  打开[Keycloak管理控制台](http://localhost:8080/admin)
    
2.  点击'客户'
    
3.  点击“创建客户端”
    
4.  用以下值填写表格：
    
    -   客户端类型：`OpenID Connect`
        
    -   客户端ID：`myclient`
        
    
5.  点击“下一步”
    
6.  确保启用了“标准流程”
    
7.  点击'保存'
    

![添加客户端](https://www.keycloak.org/resources/images/guides/add-client-1.png)

创建客户端后，您需要更新客户端的以下值：

1.  有效的重定向URI：`[https://www.keycloak.org/app/*](https://www.keycloak.org/app/*)`
    
2.  网络起源：`[https://www.keycloak.org](https://www.keycloak.org/)`
    

记得点按“`Save`”。

![更新客户端](https://www.keycloak.org/resources/images/guides/add-client-2.png)

为了方便您，我们在[Keycloak网站上](https://www.keycloak.org/app/)有一个SPA测试应用程序。

打开[https://www.keycloak.org/app/](https://www.keycloak.org/app/)，然后单击`Save`以使用默认配置。

现在，您可以单击`Sign in`，使用您之前开始的Keycloak服务器对此应用程序进行身份验证。

## 下一个

在您开始生产Keycloak之前，您还需要做几件事，包括：

-   切换到生产就绪数据库，如PostgreSQL
    
-   使用您自己的证书配置SSL
    
-   将管理员密码切换到更安全的密码