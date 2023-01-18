## 一、YAML基础

YAML是专门用来写配置文件的语言，非常简洁和强大，使用比json更方便。它实质上是一种通用的数据串行化格式。

YAML语法规则：
大小写敏感
使用缩进表示层级关系
缩进时不允许使用Tal键，只允许使用空格
缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
”#” 表示注释，从这个字符一直到行尾，都会被解析器忽略　　

在Kubernetes中，只需要知道两种结构类型即可：Lists Maps


#### 1.1 YAML Maps

Map顾名思义指的是字典，即一个Key:Value 的键值对信息。例如：

```yaml
apiVersion: v1
kind: Pod
```

注：---为可选的分隔符 ，当需要在一个文件中定义多个结构的时候需要使用。上述内容表示有两个键apiVersion和kind，分别对应的值为v1和Pod。



Maps的value既能够对应字符串也能够对应一个Maps。例如：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube100-site
  labels:
    app: web
```

注：上述的YAML文件中，metadata这个KEY对应的值为一个Maps，而嵌套的labels这个KEY的值又是一个Map。实际使用中可视情况进行多层嵌套。

YAML处理器根据行缩进来知道内容之间的关联。上述例子中，使用两个空格作为缩进，但空格的数据量并不重要，只是至少要求一个空格并且所有缩进保持一致的空格数 。例如，name和labels是相同缩进级别，因此YAML处理器知道他们属于同一map；它知道app是lables的值因为app的缩进更大。

注意：**在YAML文件中绝对不要使用tab键**

#### 1.2 YAML Lists

List即列表，说白了就是数组，例如：

```
args
 -beijing
 -shanghai
 -shenzhen
 -guangzhou
```

可以指定任何数量的项在列表中，每个项的定义以破折号（-）开头，并且与父元素之间存在缩进。在JSON格式中，表示如下：

```
{
  "args": ["beijing", "shanghai", "shenzhen", "guangzhou"]
}
```

当然Lists的子项也可以是Maps，Maps的子项也可以是List，例如：

```
apiVersion: v1
kind: Pod
metadata:
  name: kube100-site
  labels:
    app: web
spec:
  containers:
    - name: front-end
      image: nginx
      ports:
        - containerPort: 80
    - name: flaskapp-demo
      image: jcdemo/flaskapp
      ports: 8080
```

如上述文件所示，定义一个containers的List对象，每个子项都由name、image、ports组成，每个ports都有一个KEY为containerPort的Map组成，转成JSON格式文件：

```
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
        "name": "kube100-site",
        "labels": {
            "app": "web"
        },
 
  },
  "spec": {
        "containers": [{
            "name": "front-end",
            "image": "nginx",
            "ports": [{
                "containerPort": "80"
            }]
        }, {
            "name": "flaskapp-demo",
            "image": "jcdemo/flaskapp",
            "ports": [{
                "containerPort": "5000"
            }]
        }]
  }
}
```

## 二、说明

定义配置时，指定最新稳定版API
配置文件应该存储在集群之外的版本控制仓库中。如果需要，可以快速回滚配置、重新创建和恢复
应该使用YAML格式编写配置文件，而不是json。YAML对用户更加友好
可以将相关对象组合成单个文件，通常会更容易管理
不要没必要指定默认值，简单和最小配置减小错误
在注释中说明一个对象描述更好维护

## 三、使用YAML创建Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube100-site
  labels:
    app: web
spec:
  containers:
    - name: front-end
      image: nginx
      ports:
        - containerPort: 80
    - name: flaskapp-demo
      image: jcdemo/flaskapp
      ports:
        - containerPort: 5000　

```

apiVersion：此处值是v1，这个版本号需要根据安装的[Kubernetes](https://so.csdn.net/so/search?q=Kubernetes&spm=1001.2101.3001.7020)版本和资源类型进行变化，记住不是写死的。
kind：此处创建的是Pod，根据实际情况，此处资源类型可以是Deployment、Job、Ingress、Service等。
metadata：包含Pod的一些meta信息，比如名称、namespace、标签等信息。
spec：包括一些container，storage，volume以及其他Kubernetes需要的参数，以及诸如是否在容器失败时重新启动容器的属性。可在特定Kubernetes API找到完整的Kubernetes Pod的属性。

（1）查看apiVersion

```
# kubectl api-versions
admissionregistration.k8s.io/v1beta1
apiextensions.k8s.io/v1beta1
apiregistration.k8s.io/v1
apiregistration.k8s.io/v1beta1
apps/v1
apps/v1beta1
apps/v1beta2
authentication.k8s.io/v1
authentication.k8s.io/v1beta1
authorization.k8s.io/v1
authorization.k8s.io/v1beta1
autoscaling/v1
autoscaling/v2beta1
batch/v1
batch/v1beta1
certificates.k8s.io/v1beta1
events.k8s.io/v1beta1
extensions/v1beta1
networking.k8s.io/v1
policy/v1beta1
rbac.authorization.k8s.io/v1
rbac.authorization.k8s.io/v1beta1
storage.k8s.io/v1
storage.k8s.io/v1beta1
v1
```

（2）下面是一个典型的容器定义：

```
spec:
  containers:
    - name: front-end
      image: nginx
      ports:
        - containerPort: 80
```

上述例子只是一个简单的最小定义：一个名字（front-end）、基于nginx的镜像，以及容器将会监听的指定端口号（80）。

除了上述的基本属性外，还能够指定复杂的属性，包括容器启动运行的命令、使用的参数、工作目录以及每次实例化是否拉取新的副本。 还可以指定更深入的信息，例如容器的退出日志的位置。容器可选的设置属性包括：

name、image、command、args、workingDir、ports、env、resource、volumeMounts、livenessProbe、readinessProbe、livecycle、terminationMessagePath、imagePullPolicy、securityContext、stdin、stdinOnce、tty

（3）kubectl创建Pod

```
# kubectl create -f test_pod.yaml
pod "kube100-site" created
```

（4）查看Pod状态

```
# kubectl get pod
 
NAME                          READY     STATUS    RESTARTS   AGE
kube100-site                  2/2       Running   0          2m
　　
```

## 四、创建Deployment

名词解释

```yaml
#test-pod 
apiVersion: v1 #指定api版本，此值必须在kubectl apiversion中   
kind: Pod #指定创建资源的角色/类型   
metadata: #资源的元数据/属性   
  name: test-pod #资源的名字，在同一个namespace中必须唯一   
  labels: #设定资源的标签 
    k8s-app: apache   
    version: v1   
    kubernetes.io/cluster-service: "true"   
  annotations:            #自定义注解列表   
    - name: String        #自定义注解名字   
spec: #specification of the resource content 指定该资源的内容   
  restartPolicy: Always #表明该容器一直运行，默认k8s的策略，在此容器退出后，会立即创建一个相同的容器   
  nodeSelector:     #节点选择，先给主机打标签kubectl label nodes kube-node1 zone=node1   
    zone: node1   
  containers:   
  - name: test-pod #容器的名字   
    image: 10.192.21.18:5000/test/chat:latest #容器使用的镜像地址   
    imagePullPolicy: Never #三个选择Always、Never、IfNotPresent，每次启动时检查和更新（从registery）images的策略， 
                           # Always，每次都检查 
                           # Never，每次都不检查（不管本地是否有） 
                           # IfNotPresent，如果本地有就不检查，如果没有就拉取 
    command: ['sh'] #启动容器的运行命令，将覆盖容器中的Entrypoint,对应Dockefile中的ENTRYPOINT   
    args: ["$(str)"] #启动容器的命令参数，对应Dockerfile中CMD参数   
    env: #指定容器中的环境变量   
    - name: str #变量的名字   
      value: "/etc/run.sh" #变量的值   
    resources: #资源管理 
      requests: #容器运行时，最低资源需求，也就是说最少需要多少资源容器才能正常运行   
        cpu: 0.1 #CPU资源（核数），两种方式，浮点数或者是整数+m，0.1=100m，最少值为0.001核（1m） 
        memory: 32Mi #内存使用量   
      limits: #资源限制   
        cpu: 0.5   
        memory: 1000Mi   
    ports:   
    - containerPort: 80 #容器开发对外的端口 
      name: httpd  #名称 
      protocol: TCP   
    livenessProbe: #pod内容器健康检查的设置 
      httpGet: #通过httpget检查健康，返回200-399之间，则认为容器正常   
        path: / #URI地址   
        port: 80   
        #host: 127.0.0.1 #主机地址   
        scheme: HTTP   
      initialDelaySeconds: 180 #表明第一次检测在容器启动后多长时间后开始   
      timeoutSeconds: 5 #检测的超时时间   
      periodSeconds: 15  #检查间隔时间   
      #也可以用这种方法   
      #exec: 执行命令的方法进行监测，如果其退出码不为0，则认为容器正常   
      #  command:   
      #    - cat   
      #    - /tmp/health   
      #也可以用这种方法   
      #tcpSocket: //通过tcpSocket检查健康    
      #  port: number    
    lifecycle: #生命周期管理   
      postStart: #容器运行之前运行的任务   
        exec:   
          command:   
            - 'sh'   
            - 'yum upgrade -y'   
      preStop:#容器关闭之前运行的任务   
        exec:   
          command: ['service httpd stop']   
    volumeMounts:  #挂载持久存储卷 
    - name: volume #挂载设备的名字，与volumes[*].name 需要对应     
      mountPath: /data #挂载到容器的某个路径下   
      readOnly: True   
  volumes: #定义一组挂载设备   
  - name: volume #定义一个挂载设备的名字   
    #meptyDir: {}   
    hostPath:   
      path: /opt #挂载设备类型为hostPath，路径为宿主机下的/opt,这里设备类型支持很多种 
    #nfs

```

（1）创建一个yaml文件

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.10
        ports:
        - containerPort: 80
```

（2）创建deployment

```
[root@master-01 YAML_k8s]# kubectl create -f nginx-deployment.yaml
deployment.apps "nginx-deployment" created
[root@master-01 YAML_k8s]# kubectl get pod -o  wide
NAME                                READY     STATUS    RESTARTS   AGE       IP              NODE
nginx-deployment-6b7b4d57b4-26wzj   1/1       Running   0          2m        10.20.184.83    master-01
nginx-deployment-6b7b4d57b4-9w7tm   1/1       Running   0          2m        10.20.190.60    node-01
nginx-deployment-6b7b4d57b4-mhh8t   1/1       Running   0          2m        10.20.254.108   node-03
[root@master-01 YAML_k8s]# kubectl get deployment
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3         3         3            3           2m
```

（3）查看标签

```
[root@master-01 YAML_k8s]# kubectl get pod --show-labels
NAME                                READY     STATUS    RESTARTS   AGE       LABELS
nginx-deployment-6b7b4d57b4-26wzj   1/1       Running   0          3m        app=nginx,pod-template-hash=2636081360
nginx-deployment-6b7b4d57b4-9w7tm   1/1       Running   0          3m        app=nginx,pod-template-hash=2636081360
nginx-deployment-6b7b4d57b4-mhh8t   1/1       Running   0          3m        app=nginx,pod-template-hash=2636081360

```

（4）通过标签查找Pod

```
[root@master-01 YAML_k8s]# kubectl get pod -l app=nginx
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-6b7b4d57b4-26wzj   1/1       Running   0          6m
nginx-deployment-6b7b4d57b4-9w7tm   1/1       Running   0          6m
nginx-deployment-6b7b4d57b4-mhh8t   1/1       Running   0          6m

```

（5）deployment创建过程

Deployment 管理的是replicaset-controller，RC会创建Pod。Pod自身会下载镜像并启动镜像

```
[root@master-01 YAML_k8s]# kubectl describe rs nginx-deployment
...
...
...
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  33m   replicaset-controller  Created pod: nginx-deployment-6b7b4d57b4-9w7tm
  Normal  SuccessfulCreate  33m   replicaset-controller  Created pod: nginx-deployment-6b7b4d57b4-26wzj
  Normal  SuccessfulCreate  33m   replicaset-controller  Created pod: nginx-deployment-6b7b4d57b4-mhh8t
12345678910
[root@master-01 YAML_k8s]# kubectl describe pod nginx-deployment-6b7b4d57b4-26wzj

...
...
...
Events:
 Type    Reason                 Age   From                Message
 ----    ------                 ----  ----                -------
 Normal  Scheduled              36m   default-scheduler   Successfully assigned nginx-deployment-6b7b4d57b4-26wzj to master-01
 Normal  SuccessfulMountVolume  36m   kubelet, master-01  MountVolume.SetUp succeeded for volume "default-token-v5vw9"
 Normal  Pulled                 36m   kubelet, master-01  Container image "nginx:1.10" already present on machine
 Normal  Created                36m   kubelet, master-01  Created container
 Normal  Started                36m   kubelet, master-01  Started container

```

（6）升级镜像（nginx1.10–>nginx1.11）

```
[root@master-01 YAML_k8s]# kubectl set image deploy/nginx-deployment nginx=nginx:1.11
deployment.apps "nginx-deployment" image updated
 
[root@master-01 YAML_k8s]# kubectl exec -it nginx-deployment-b96c97dc-2pxjf bash
root@nginx-deployment-b96c97dc-2pxjf:/# nginx -V
nginx version: nginx/1.11.13

```

升级镜像的过程是逐步进行的，pod不会一下子全部关闭，而是一个一个升级

（7）查看发布状态

```
[root@master-01 ~]# kubectl rollout status deploy/nginx-deployment
deployment "nginx-deployment" successfully rolled out

```

（8）查看deployment历史修订版本

```
[root@master-01 ~]# kubectl rollout history deploy/nginx-deployment
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
 
# 显示历史有两个版本
 
[root@master-01 ~]# kubectl rollout history deploy/nginx-deployment --revision=1
deployments "nginx-deployment" with revision #1
Pod Template:
  Labels:   app=nginx
    pod-template-hash=2636081360
  Containers:
   nginx:
    Image:  nginx:1.10
    Port:   80/TCP
    Host Port:  0/TCP
    Environment:    <none>
    Mounts: <none>
  Volumes:  <none>
 
[root@master-01 ~]# kubectl rollout history deploy/nginx-deployment --revision=2
deployments "nginx-deployment" with revision #2
Pod Template:
  Labels:   app=nginx
    pod-template-hash=65275387
  Containers:
   nginx:
    Image:  nginx:1.11
    Port:   80/TCP
    Host Port:  0/TCP
    Environment:    <none>
    Mounts: <none>
  Volumes:  <none>

```

（8）编辑deployment

```
[root@master-01 ~]# kubectl edit deploy/nginx-deployment
 
 
# 将nginx版本改为1.12
...
...
...
   spec:
      containers:
      - image: nginx:1.12
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80

```

升级过程：

```
[root@master-01 ~]# kubectl rollout status deploy/nginx-deployment
Waiting for rollout to finish: 1 out of 3 new replicas have been updated...
Waiting for rollout to finish: 1 out of 3 new replicas have been updated...
Waiting for rollout to finish: 1 out of 3 new replicas have been updated...
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for rollout to finish: 1 old replicas are pending termination...
Waiting for rollout to finish: 1 old replicas are pending termination...
deployment "nginx-deployment" successfully rolled out

```

（10）扩容/缩容（指定–replicas的数量）

```
[root@master-01 ~]# kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
nginx-deployment-6b47cf4878-8mjkr   1/1       Running   0          1m
nginx-deployment-6b47cf4878-kr978   1/1       Running   0          1m
nginx-deployment-6b47cf4878-tvhvl   1/1       Running   0          1m

[root@master-01 ~]# kubectl scale deploy/nginx-deployment --replicas=5
deployment.extensions "nginx-deployment" scaled

[root@master-01 ~]# kubectl get pod
NAME                                READY     STATUS              RESTARTS   AGE
nginx-deployment-6b47cf4878-6r5dz   0/1       ContainerCreating   0          4s
nginx-deployment-6b47cf4878-7sjtt   0/1       ContainerCreating   0          4s
nginx-deployment-6b47cf4878-8mjkr   1/1       Running             0          2m
nginx-deployment-6b47cf4878-kr978   1/1       Running             0          2m
nginx-deployment-6b47cf4878-tvhvl   1/1       Running             0          2m

```

（11）创建Service提供对外访问的接口

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    app: nginx
spec:
  ports:
  - port: 88
    targetPort: 80
  selector:
    app: nginx
 
####
apiVersion: 指定版本
 
kind: 类型
 
name: 指定服务名称
 
labels: 标签
 
port: Service 服务暴露的端口
 
targetPort: 容器暴露的端口
 
seletor: 关联的Pod的标签

```

创建service

```
# kubectl create -f nginx-service.yaml
```

查看service（访问Pod是有负载均衡的）

```
[root@master-01 YAML_k8s]# kubectl get svc/nginx-service
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   10.254.131.176   <none>        88/TCP    1m
123
# curl 10.254.131.176:88
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
   body {
       width: 35em;
       margin: 0 auto;
       font-family: Tahoma, Verdana, Arial, sans-serif;
   }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

```

对service的描述

```
# kubectl describe svc/nginx-service
Name:              nginx-service
Namespace:         default
Labels:            app=nginx
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP:                10.254.131.176
Port:              <unset>  88/TCP
TargetPort:        80/TCP
Endpoints:         10.20.184.19:80,10.20.184.84:80,10.20.190.62:80 + 2 more...
Session Affinity:  None
Events:            <none>

```

（12）回滚到以前的版本

```
# kubectl rollout history deploy/nginx-deployment
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>
123456
# kubectl rollout history deploy/nginx-deployment --revision=3
deployments "nginx-deployment" with revision #3
Pod Template:
 Labels:   app=nginx
   pod-template-hash=2603790434
 Containers:
  nginx:
   Image:  nginx:1.12
   Port:   80/TCP
   Host Port:  0/TCP
   Environment:    <none>
   Mounts: <none>
 Volumes:  <none>
12345678910111213
# 回滚到上一个版本
# kubectl rollout undo deploy/nginx-deployment
deployment.apps "nginx-deployment"


# 查看版本
# kubectl describe deploy/nginx-deployment
...
...
Labels:  app=nginx
 Containers:
  nginx:
   Image:        nginx:1.11

```

（13）回滚到指定版本

```
# kubectl rollout history deploy/nginx-deployment
deployments "nginx-deployment"
REVISION  CHANGE-CAUSE
1         <none>
3         <none>
4         <none>
 
# 指定版本
# kubectl rollout undo deploy/nginx-deployment --to-revision=1
deployment.apps "nginx-deployment"

```

### 附上一个具体的yaml解释文件：

```yaml
# yaml格式的pod定义文件完整内容：
apiVersion: v1       #必选，版本号，例如v1
kind: Pod       #必选，Pod
metadata:       #必选，元数据
  name: string       #必选，Pod名称
  namespace: string    #必选，Pod所属的命名空间
  labels:      #自定义标签
    - name: string     #自定义标签名字
  annotations:       #自定义注释列表
    - name: string
spec:         #必选，Pod中容器的详细定义
  containers:      #必选，Pod中容器列表
  - name: string     #必选，容器名称
    image: string    #必选，容器的镜像名称
    imagePullPolicy: [Always | Never | IfNotPresent] #获取镜像的策略 Alawys表示下载镜像 IfnotPresent表示优先使用本地镜像，否则下载镜像，Nerver表示仅使用本地镜像
    command: [string]    #容器的启动命令列表，如不指定，使用打包时使用的启动命令
    args: [string]     #容器的启动命令参数列表
    workingDir: string     #容器的工作目录
    volumeMounts:    #挂载到容器内部的存储卷配置
    - name: string     #引用pod定义的共享存储卷的名称，需用volumes[]部分定义的的卷名
      mountPath: string    #存储卷在容器内mount的绝对路径，应少于512字符
      readOnly: boolean    #是否为只读模式
    ports:       #需要暴露的端口库号列表
    - name: string     #端口号名称
      containerPort: int   #容器需要监听的端口号
      hostPort: int    #容器所在主机需要监听的端口号，默认与Container相同
      protocol: string     #端口协议，支持TCP和UDP，默认TCP
    env:       #容器运行前需设置的环境变量列表
    - name: string     #环境变量名称
      value: string    #环境变量的值
    resources:       #资源限制和请求的设置
      limits:      #资源限制的设置
        cpu: string    #Cpu的限制，单位为core数，将用于docker run --cpu-shares参数
        memory: string     #内存限制，单位可以为Mib/Gib，将用于docker run --memory参数
      requests:      #资源请求的设置
        cpu: string    #Cpu请求，容器启动的初始可用数量
        memory: string     #内存清楚，容器启动的初始可用数量
    livenessProbe:     #对Pod内个容器健康检查的设置，当探测无响应几次后将自动重启该容器，检查方法有exec、httpGet和tcpSocket，对一个容器只需设置其中一种方法即可
      exec:      #对Pod容器内检查方式设置为exec方式
        command: [string]  #exec方式需要制定的命令或脚本
      httpGet:       #对Pod内个容器健康检查方法设置为HttpGet，需要制定Path、port
        path: string
        port: number
        host: string
        scheme: string
        HttpHeaders:
        - name: string
          value: string
      tcpSocket:     #对Pod内个容器健康检查方式设置为tcpSocket方式
         port: number
       initialDelaySeconds: 0  #容器启动完成后首次探测的时间，单位为秒
       timeoutSeconds: 0   #对容器健康检查探测等待响应的超时时间，单位秒，默认1秒
       periodSeconds: 0    #对容器监控检查的定期探测时间设置，单位秒，默认10秒一次
       successThreshold: 0
       failureThreshold: 0
       securityContext:
         privileged:false
    restartPolicy: [Always | Never | OnFailure]#Pod的重启策略，Always表示一旦不管以何种方式终止运行，kubelet都将重启，OnFailure表示只有Pod以非0退出码退出才重启，Nerver表示不再重启该Pod
    nodeSelector: obeject  #设置NodeSelector表示将该Pod调度到包含这个label的node上，以key：value的格式指定
    imagePullSecrets:    #Pull镜像时使用的secret名称，以key：secretkey格式指定
    - name: string
    hostNetwork:false      #是否使用主机网络模式，默认为false，如果设置为true，表示使用宿主机网络
    volumes:       #在该pod上定义共享存储卷列表
    - name: string     #共享存储卷名称 （volumes类型有很多种）
      emptyDir: {}     #类型为emtyDir的存储卷，与Pod同生命周期的一个临时目录。为空值
      hostPath: string     #类型为hostPath的存储卷，表示挂载Pod所在宿主机的目录
        path: string     #Pod所在宿主机的目录，将被用于同期中mount的目录
      secret:      #类型为secret的存储卷，挂载集群与定义的secre对象到容器内部
        scretname: string  
        items:     
        - key: string
          path: string
      configMap:     #类型为configMap的存储卷，挂载预定义的configMap对象到容器内部
        name: string
        items:
        - key: string
```