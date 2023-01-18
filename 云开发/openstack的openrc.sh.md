
> 如果使用命令行工具对OpenStack云系统发起查询请求，必须有合适的认证凭据。

> 获得使用命令行客户端的认证证书，最简单的方式是用OpenStack控制面板，登录dashboard之后，右上角可以下载OpenStack RC 文件。

> 使用这种方法生成可以在shell脚本中源化（source）的文件填入命令行工具需要的环境变量，以了解服务端点和认证信息在何处。

> 登录控制面板的用户会指定openrc文件的文件名，比如demo-openrc.sh。作为管理员登录，文件名就是admin-openrc.sh。

下面以admin用户登录为例对生成的admin-openrc.sh进行解释。

```bash
#!/usr/bin/env bash
# To use an OpenStack cloud you need to authenticate against the Identity
# service named keystone, which returns a **Token** and **Service Catalog**.
# The catalog contains the endpoints for all services the user/tenant has
# access to - such as Compute, Image Service, Identity, Object Storage, Block
# Storage, and Networking (code-named nova, glance, keystone, swift,
# cinder, and neutron).
# 要使用OpenStack云系统，需要通过Keystone身份服务的认证，
# 认证成功后keystone会返回一个**令牌**和**服务目录**。
# 服务目录包含有用户或者租户访问的所有服务--包括Compute, Image Service, Identity, Object Storage, Block Storage, and Networking (code-named nova,glance, keystone, swift,cinder, and neutron)

# *NOTE*: Using the 3 *Identity API* does not necessarily mean any other
# OpenStack API is version 3. For example, your cloud provider may implement
# Image API v1.1, Block Storage API v2, and Compute API v2.0. OS_AUTH_URL is
# only for the Identity API served through keystone.
# *注意*：使用3.0版本的*认证API*并不意味着任何其他OpenStack API的版本是3.0。
# 例如你的云供应商可能运用的是Image API v1.1, Block Storage API v2, and Compute API v2.0。
# OS_AUTH_URL 仅适用于通过keystone提供的Identity API。
export OS_AUTH_URL=http://192.168.1.200:5000/v3

# With the addition of Keystone we have standardized on the term **project**
# as the entity that owns the resources.
# 在Keystone身份服务里，我们将**租户**这个词的含义标准化为拥有资源的实体。
export OS_PROJECT_ID=afeabb49b7a1491cad684fb09b563ecb
export OS_PROJECT_NAME="admin"
export OS_USER_DOMAIN_NAME="Default"
if [ -z "$OS_USER_DOMAIN_NAME" ]; then unset OS_USER_DOMAIN_NAME; fi
export OS_PROJECT_DOMAIN_ID="default"
if [ -z "$OS_PROJECT_DOMAIN_ID" ]; then unset OS_PROJECT_DOMAIN_ID; fi

# unset v2.0 items in case set
# v2.0项如果设置了，在案例集中取消。
unset OS_TENANT_ID
unset OS_TENANT_NAME

# In addition to the owning entity (tenant), OpenStack stores the entity
# performing the action as the **user**.
# 除了拥有资源的实体（租户），OpenStack以**用户**的身份对实体进行操作。
export OS_USERNAME="admin"

# With Keystone you pass the keystone password.
# 使用Keystone服务需要提供keystone的密码。
# 这种方式不以明文形式保存密码是一个比较建议的做法。只是在当源化（source）或者运行脚本的时候会提示输入密码，
# 然后将将输入的内容保存在环境变量OS_PASSWORD中。
# 这样便需要一次手动交互。如果需要设置非交互操作，可以将输入的内容直接存储在脚本中，但一定要配置好对应脚本文件的安全性和权限。
echo "Please enter your OpenStack Password for project $OS_PROJECT_NAME as user $OS_USERNAME: "
read -sr OS_PASSWORD_INPUT
export OS_PASSWORD=$OS_PASSWORD_INPUT

# If your configuration has multiple regions, we set that information here.
# OS_REGION_NAME is optional and only valid in certain environments.
# 如果你的配置中有多种域，则在这里进行设置相关信息。
# OS_REGION_NAME是可选项并且只在合适的环境可用。
export OS_REGION_NAME="RegionOne"

# Don't leave a blank variable, unset it if it was empty
# 不要保留空变量，如果它是空的则取消设置
if [ -z "$OS_REGION_NAME" ]; then unset OS_REGION_NAME; fi
export OS_INTERFACE=public
export OS_IDENTITY_API_VERSION=3
```

  

作者：北岸冷若冰霜  
链接：https://juejin.cn/post/6995113078777970695  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。