对于公司内部的项目, 我们不可能使用公有开放的镜像仓库, 一般情况可能会花钱买  `docker`私仓服务, 或者说自己在服务器上搭建自己的私仓, 但不管怎样, 我们如何让`k8s`能够拉取私有仓库的镜像

##### 1. 登录`docker`镜像仓库

这里以`阿里云docker镜像仓库`为例

```dart
docker login --username=yin32167@aliyun.com registry.cn-hangzhou.aliyuncs.com
```

之后输入密码就可以了, 这个时候我们可以在配置文件中查看登录情况

```jsx
cat ~/.docker/config.json
```

> 这个时候我们虽然可以通过`docker pull`命令拉取镜像, 但无法通过`k8s`创建`pod`方式拉取

##### 2. 生成密钥`secret`

```dart
kubectl create secret docker-registry regsecret --docker-server=registry.cn-hangzhou.aliyuncs.com --docker-username=yin32167@aliyun.com --docker-password=xxxxxx --docker-email=yin32167@aliyun.com
```

> 其中:  
> regsecret: 指定密钥的键名称, 可自行定义  
> --docker-server: 指定`docker`仓库地址  
> --docker-username: 指定`docker`仓库账号  
> --docker-password: 指定`docker`仓库密码  
> --docker-email: 指定邮件地址(选填)

![](//upload-images.jianshu.io/upload_images/938819-ba0638279141d6d4.png?imageMogr2/auto-orient/strip|imageView2/2/w/1034)

> 可以看到当前除了默认的密钥, 还有我们刚才生成的. 另外要注意的是, 该密钥只能在对应`namespace`使用, 也就是这里的`default`, 如果需要用到其他`namespace`, 比如说`test`, 就需要在生成的时候指定参数 `-n test`

##### 3. `yml`文件加入密钥参数

```csharp
containers:
- name: channel
  image: registry-internal.cn-hangzhou.aliyuncs.com/yin32167/channel:dev-1.0
ports:
- containerPort: 8114
imagePullSecrets:
- name: regsecret
```

> 其中`imagePullSecrets`是声明拉取镜像时需要指定密钥, `regsecret` 必须和上面生成密钥的键名一致, 另外检查一下`pod`和密钥是否在同一个`namespace`, 之后`k8s`便可以拉取镜像

  
  
作者：殷临风  
链接：https://www.jianshu.com/p/fd13c2762d81  
来源：简书  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
