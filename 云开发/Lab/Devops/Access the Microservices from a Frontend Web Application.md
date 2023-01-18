

## 1 Prerequisites

To start this Lab, you must have a working Kubernetes cluster. `haproxy` is supposed to be running on your bastion. Please check all these points carefully before proceeding.

You can start the lab even if you have not yet deployed the `photo` and `photographer` services on your cluster.


登陆openstack-dfvs.imt-atlantique.fr网站，上面有创建好的k8s集群，下载openrc.sh文件，查看堡垒机controller的公网IP和控制机k8s-cont的内网IP，在本地虚拟机/.ssh/下创建config文件，写入：
```
Host bastion
  Hostname 10.29.246.156
  User ubuntu

Host k8s-cont
  Hostname 192.168.10.31
  ProxyJump bastion
  User ubuntu
```

是根据这一节改写的[[OpenStack and Ansible#7.1.1 Set up SSH to use a Jump Host]]

然后在本地虚拟机运行`ssh k8s-cont`就能连接上controller了

## 2 Goal

The objective of this lab is to implement an application based on the services (`photo`, `photographer` and `tags`) that you have developed. To ensure that only authorized users have access to the photos, the application cannot be exposed using only the microservices that you have developed so far. If we did that, anybody could accesss the APIs to create (and delete!) photographers and photos. Although most of the work has been done with the implementation of the services, there is still a substantial amount of work to do before we can offer such an application. Your concerns will be as follows:

- How to secure communications between the browser and your Kubernetes cluster ?
- How to ensure that the user is accessing the server hosting your application and not that of a hacker ?
- Your application consists of two services exposing a Rest API and a third service exposing a gRPC API. Which API will you expose outside your cluster?
- How will you protect and monitor the use of your services ?
- How will you implement a graphical interface (probably in JavaScript) to use the application in a WEB browser ?

> [!NOTE] 前端的重点
> - 如何保证浏览器和你的Kubernetes集群之间的通信安全？
> - 如何确保用户访问的是托管你的应用程序的服务器，而不是黑客的服务器？
> - 你的应用程序包括两个暴露于Rest API的服务和一个暴露于gRPC API的第三个服务。你将把哪个API暴露在你的集群之外？
> - 你将如何保护和监控你的服务的使用？
> - 你将如何实现一个图形界面（可能是JavaScript）以在WEB浏览器中使用该应用程序？

Of course you are not going to implement all this from scratch. You're going to use Kubernetes resources, security tools, an external authentication service and an API gateway. More precisely, you will use:

- NGINX Ingress controller (https://kubernetes.github.io/ingress-nginx/)
- Gravitee as an API Gateway ([http://gravitee.io](http://gravitee.io/)）
- Keycloak for the Authentication Service ([http://keycloak.org](http://keycloak.org/))


> [!NOTE] 使用的第三方服务
> - NGINX 作为入口控制器ingress controller(https://kubernetes.github.io/ingress-nginx/)
> - Gravitee作为API网关([http://gravitee.io](http://gravitee.io/))
> - 用于认证服务的Keycloak([http://keycloak.org](http://keycloak.org/))




## 3 Check the configuratopn of the NGINX Ingress Controller

As you have seen in the Kubernetes lab, by default, all Kubernetes resources are not reachable from outside the cluster. In the Kubernetes lab, you have used a NodePort to expose a service but this solution has the downside of forcing all the users of the cluster to coordinate as only one service can use a given port number. Another possibility is to declare a service to be of type "LoadBalancer". In this case, Kubernetes will automatically ask the cloud provider to create a new load balancer to expose the service to the outside world. This works only if the cloud provider does support this functionality. Most cloud providers, including OpenStack, do but the OpenStack platform that you are using does not. Furthermore, using services of type LoadBalancer implies that each service will have its own public IP address implying additional costs associated with running a load balancer.

To address these shortcomings, there is yet another way of exposing a service on Kubernetes: **Ingress Controllers**. Typically, an ingress controller is associated with a load balancer, just like a service of type LoadBalancer but the difference is that a single ingress controller can expose an arbitrary number of services so that you need to pay for only one load balancer and you only need one public IP address. Given that the OpenStack platform that you are using does not offer load balancers, you will use the HAproxy running on your bastion host as a "load balancer." HAproxy does offer all the functionalities of a load balancer but, as it is running on a single VM, it is a single point of failure. While it is possible to install HAproxy on multiple VMs, it is outside the scope of this class. You can think of HAproxy running on the bastion VM as the equivalent of a load balancer provided by the cloud platform, albeit a non-highly available one.

Among all the available ingress controller, you will use the Nginx one.

The careful reader might be wondering how he should configure HAproxy in order to forward traffic to the ingress controller. The answer is that, for the sake of simplicity, you have just installed an Nnginx ingress controller that has created a service of type NodePort. Recall that this implies that any traffic received on the corresponding port on any of the **VMs** of the cluster will be forwarded to this service. The ingress controller service will then take care of delivering each flow to the appropriate destination (more on this below).

You can first verify that you do have an ingress controller running by making sure that the output of the command `kubectl get pods -n ingress-nginx` contains a pod (it is OK if it also shows two extra pods as "completed"). Then you can obtain the port number of the NodePort service by running `kubectl -n ingress-nginx get svc`. The output should have something like `80:32131/TCP` and `443:32308/TCP` under the `PORT(s)` column header. In this example, 32131 (resp. 32308) is the port number that the ingress service is listening on for HTTP (resp. HTTPS).

And these are the port numbers that must be used when configuring HAproxy *so that it will send all the traffic received by the bastion on port 80 and port 443 to one of the Kubernetes VMs*. It is up to you to check that the configuration of HAproxy is correct. (Hint: you can use the section about the kubectl traffic as a starting point.) 

> [!NOTE] 为什么需要NGINX
> 
> [[NGINX用作反向代理服务器实现负载均衡]]
> 
> 正如你在Kubernetes实验室中看到的，默认情况下，所有的Kubernetes资源都无法从集群外部到达。在Kubernetes实验室中，你使用了NodePort来暴露一个服务，但这个解决方案的缺点是迫使集群的所有用户进行协调，因为只有一个服务可以使用给定的端口号。另一种可能性是将一个服务声明为 "LoadBalancer "类型。在这种情况下，Kubernetes会自动要求云提供商创建一个新的负载平衡器，将服务暴露给外部世界。这只有在云提供商确实支持这一功能的情况下才有效。大多数云提供商，包括OpenStack，都支持，但你所使用的OpenStack平台不支持。此外，使用LoadBalancer类型的服务意味着每个服务将有自己的公共IP地址，这意味着与运行负载平衡器有关的额外费用。
> 
> 为了解决这些缺点，还有一种在Kubernetes上暴露服务的方式。**入口控制器**。通常，一个入口控制器与一个负载均衡器相关联，就像一个LoadBalancer类型的服务一样，但不同的是，一个入口控制器可以暴露任意数量的服务，这样你只需要为一个负载均衡器付费，而且你只需要一个公共IP地址。鉴于你所使用的OpenStack平台不提供负载平衡器，你将使用运行在你的堡垒主机上的HAproxy作为 "负载平衡器"。HAproxy确实提供了负载平衡器的所有功能，但是，由于它运行在一个单一的虚拟机上，它是一个单点故障。虽然有可能在多个虚拟机上安装HAproxy，但这不在本课的范围之内。你可以把运行在bastion VM上的HAproxy看作是云平台提供的负载平衡器的等价物，尽管是一个非高度可用的负载平衡器。
> [[NGINX的负载均衡]]
> [[Kubernetes中Service、Ingress与Ingress Controller的作用与关系]]



> [!NOTE] 检查NGINX配置
> 你可以首先验证你确实有一个反向代理在运行：
> 
> 确保`kubectl get pods -n ingress-nginx`命令的输出包含一个pod（如果它还显示两个额外的pod为 "已完成 "也是可以的）。
> 
> 然后你可以通过运行`kubectl -n ingress-nginx get svc`来获得NodePort服务的端口号。输出结果应该是`80:32131/TCP`和`443:32308/TCP`在`PORT(s)`列标题下。
> - 在这个例子中，32131（或32308）是ingress服务监听HTTP（或HTTPS）的端口号。
> 
> 而这些是配置HAproxy时必须使用的端口号，*这样它就会把堡垒在80端口和443端口收到的所有流量发送到其中一个Kubernetes虚拟机*。由你来检查HAproxy的配置是否正确。(提示：你可以使用关于kubectl流量的部分作为一个起点。) 



## 4 Securing Ingress

The Ingress Controller will be the gateway to your Kubernetes cluster. So before you even think about securing your photo sharing application, you need to secure this front door. **HTTPS** provides a good solution for this: it is a secure version of the **HTTP** protocol that relies upon **TLS** (Transport Layer Security). Thanks to the use of TLS, 1) the server hosting your application will be able to prove to the client that it is who it claims to be 2) the communications between the client (curl, Web Browser, Mobile App, …) and the server will be encrypted. It is out of the scope of this lab to detail how TLS works, but you should know at least that it relies on certificates signed by trusted certification authorities. When you access a server with HTTPS, your browser checks that the certificate of the server has been signed by an authority it trusts. These trusted authorities are kept as root certificates in a store managed by the browser or by the Operating System.

An ideal solution would have been to use **letsencrypt** services (or any other authority trusted by browser vendors) to sign the certificate for your server. Unfortunately, to do this, you must be able to prove either that you control the DNS for the domain name associated with this certificate or that you can respond to an HTTP challenge issued by the authority. As your servers are not reachable from the Internet (no public DNS, no public address), you do not have the possibility to answer such a challenge or to do anything related to the DNS entries.

As a replacement, you will define your own (root) certificate authority and tell your browser that you trust it (basically, you will trust yourself). This will be sufficient for the purposes of the lab.


> [!NOTE] 使用HTTPS保证安全
> Ingress controller（反向代理程序）将是你的Kubernetes集群的网关。因此，在你考虑保护你的照片共享应用程序之前，你需要保护这个前门。**HTTPS**为此提供了一个很好的解决方案：它是**HTTP**协议的一个安全版本，依赖于**TLS**（传输层安全）。由于使用了TLS，
> 
> - 托管你的应用程序的服务器将能够向客户证明它就是它所声称的那个人
> - 客户（curl、Web浏览器、移动应用程序...）和服务器之间的通信将被加密。
> 
> 详细介绍TLS的工作原理不在本实验室的范围之内，但你至少应该知道，它依赖于由受信任的认证机构签署的证书。当你用HTTPS访问一个服务器时，你的浏览器会检查该服务器的证书是否已经由它所信任的机构签署。这些受信任的认证机构作为根证书保存在一个由浏览器或操作系统管理的商店里。


> [!NOTE] 使用letsencrypt签署服务器证书
> 一个理想的解决方案是使用**letsencrypt**服务（或任何其他被浏览器供应商信任的权威机构）来签署你的服务器的证书。不幸的是，要做到这一点，你必须能够证明你控制着与该证书相关联的域名的DNS，或者你能够回应权威机构发出的HTTP挑战。由于你的服务器从互联网上无法到达（没有公共DNS，没有公共地址），你没有可能回答这样的挑战或做任何与DNS条目有关的事情。
> 
> 作为替代，你将定义你自己的（根）证书机构，并告诉你的浏览器你信任它（基本上，你将信任你自己）。这对于实验室的目的来说已经足够了。



### 4.1 Root CA

Each group has its own **Certificate Authority**. The file corresponding to this authority has been dropped in your Mattermost channel.

### 4.2 Add your Root CA to Firefox

Now you have to add your Root CA to the Firefox certificate store so that your browser accepts the certificates signed by the private key of your Root CA.

Find a way to add your certificate to the Firefox certificate store. By doing this, you are making a conscious choice to trust the certificates that will be signed by this certificate authority. 

### 4.3 Cert Manager

You could now generate certificates signed by your Root CA using the `openssl` tool but as you know, we like to automate all the tasks that can be automated. Instead of using `openssl` commands to generate certificates for your servers, you will use a certificate manager. This certificate manager has already been installed and is ready to generate certificates signed by the private key of your root authority.

[[k8s使用cert-manager签发免费证书]]

### 4.4 Deploy a Secure Hello Word Server on your K8S Cluster

To make sure it all works, you will now deploy a FastAPI REST service in your K8S cluster. This service handles GET requests for the `/hello` endpoint and returns a "hello" string in a HTML document.

The image of this service is available and you can download the K8S file defining the deployment and the service (of type ClusterIP) at the following address: `www.cloud.rennes.enst-bretagne.fr/files/app-lab/k8s-hello.yml`. Run the following command to install it.

```
> kubectl apply -f k8s-hello.yml
```
[[k8s-hello.yml和k8s-hello-ingress.yml分析]]
For the moment, the service is not reachable from outside your cluster but you should be able to interact with it by logging in to a node in your cluster.

Please use SSH to log in to one of your Kubernetes controller and use `curl` to issue a `GET` request to the `/hello` endpoint of your `hello` service (do not forget to unset `http_proxy` on the command line before issuing the `curl` command).

If all went well, you have successfully joined your service from inside your cluster. Now it's time to try to access it from the outside and you will do it in a secure way. To do this, you need a Kubernetes resource of type **Ingress**.

Download the Ingress for the `Hello` service from `www.cloud.rennes.enst-bretagne.fr/files/app-lab/k8s-hello-ingress.yml`. Its content is the following:

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello 
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/issuer: C = FR, ST = Bretagne, L = Rennes, O = IMT Atlantique, OU = DevOps Lab, CN = Group 05
spec:
  rules:
  - host: hello-05.example.com 
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: hello-service
            port:
              number: 80
  tls:
  - hosts:
    - hello-05.example.com
    secretName: hello-secret
```

The Ingress file for the `Hello` service is explained below:

- In the `metadata` section, there is an `annotations` entry called `cert-manager.io/issuer`. The value **must** match the name of the issuer in the `my-root-ca-issuer.yml` file (see the previous section).
	- 替换成了C = FR, ST = Bretagne, L = Rennes, O = IMT Atlantique, OU = DevOps Lab, CN = Group 05
- In the `spec` section, there is a `tls` entry under which you must list the hosts you want to be accessible via HTTPS. The name of the secret must be given (`secretName`).

Replace `xy` by your group number and run the following command to install the Ingress for the `Hello` service.

```
> kubectl apply -f k8s-hello-ingress.yml
```

Now you have one last thing to do on your local machine before you can access your service. As you do not have access to a DNS for the OpenStack servers, you will modify the `/etc/hosts` file (on your local VM) to force the resolution of `hello-xy.example.com` to the IP address of your bastion (which runs the HAproxy load balancer). 

Modify the `/etc/hosts` file on your local VM.

Now you should be able to access your `Hello` service by typing [http://hello-xy.example.com](http://hello-xy.example.com/) in the address bar of your browser. If you have done everything correctly, you SHOULD not get a security alert because the certificate for the `Hello` service has been signed by an authority that you have declared you trust.

If you encounter a security alert, then it means that something has gone wrong and you need to investigate the source of the problem!

## 5 Keycloak
[[安装Keycloak]]
Keycloak ([http://www.keycloak.org](http://www.keycloak.org/)) is an Open Source tool for identity and access management. You will use this tool for providing authentification to your photo sharing application.

To allow you to discover some of the features of Keycloak, we suggest you to follow the tutorial on the following page: https://www.keycloak.org/getting-started/getting-started-docker. It consists in running keycloak in a docker container on your local machine, creating a realm, a user and using keycloak authentication to register with a third party application.

Please pay attention to the URIs you must use in this setup

- http://localhost:8080/admin for Keycloak Admin Console
- http://localhost:8080/realms/myrealm/account for Account Console (provided that you have created a realm with name `myrealm`).

Once you are done with this short tutorial, you can destroy the container running Keycloak on your local machine.

## 6 API Gateway

The use of an API Gateway will now allow us to build the API for our photo sharing application and to secure this API. The API gateway we have chosen is **Gravitee**, an open source tool. You will only use a small part of the possibilities of this API Gateway but nothing will prevent you from discovering more of its possibilities by yourself.

The **Gravitee** API Gateway has already been installed on your K8S cluster.

> [!NOTE]
> Take the opportunity to change your `/etc/hosts` file in a way that `apim-xy.example.com` translates to the floating IP of your bastion.

此时本地虚拟机上的的/etc/hosts文件内容：

```
127.0.0.1       localhost
127.0.1.1       SYS-Ubuntu22
10.29.246.156   hello-05.example.com
10.29.246.156   apim-05.example.com

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```


You should be able to access the **Gravitee** console with the following URI `https://apim-xy.example.com/console/#`. The login/password to authenticate is `admin/admin`. If you can't get the following dashboard (do not pay attention to the number of APIs or Applications), then clean your Firefox cache and try to reload the page.

![gravitee-first-screen.png](http://www.cloud.rennes.enst-bretagne.fr/gravitee-first-screen.png)

### 6.1 First Steps with Gravitee

You will discover in the following sections the different steps to build a **Gravitee API** and a **Gravitee App(lication)**.

#### 6.1.1 Create the API

- Left panel: select **APIs** and click on the **+** sign (bottom right of the window)

  ![api-1.png](http://www.cloud.rennes.enst-bretagne.fr/api-1.png)

- Choose **PATHS BASED** and click on **CREATE**

  The screenshot below does not quite match what you have on your screen because the creation of a **PATHS BASED** API is discouraged by the authors of Gravitee in favor of **DESIGN STUDIO**. We still suggest you to choose **PATHS BASED** (instead of a graphical overview of the interface supported by **PATHS BASED**, you have to settle for a link entitled: Or you can still use paths based Import - Create). You will have the opportunity to discover by yourself the interface and the possibilities offered by **DESIGN STUDIO** when you feel comfortable enough with the API concept as defined by Gravitee.

  ![api-2.png](http://www.cloud.rennes.enst-bretagne.fr/api-2.png)

- Choose **api-photographer**, **0.1** for the name and the version of the API. Add a description text for the API. Finally, add a context path which **must** start with `/gateway`. We suggest: `/gateway/photographer`. Click on **NEXT**.

  ![api-3.png](http://www.cloud.rennes.enst-bretagne.fr/api-3.png)

- Give the name of the backend. The backend is the URI of your photographer service which will handle the requests. If your service is in the `default` Kubernetes name space, choose `http://photographer-service`. If your service is in another namespace (lets say `yournamespace`), then use the following name: `http://photographer-service.yournamespace`

  ![api-4.png](http://www.cloud.rennes.enst-bretagne.fr/api-4.png)

- Subscription to an API occurs through a **Gravitee** plan (see https://docs.gravitee.io/apim/3.x/apim_publisherguide_plans_subscriptions.html). Give a name for the plan. Choose **API Key** for the type. As stated in the official documentation, it provides a very basic level of security. Do not forget to slide the latch **Auto validate subscription** and click on **NEXT**.

  ![api-5.png](http://www.cloud.rennes.enst-bretagne.fr/api-5.png)

- Click on **SKIP** to skip the documentation page.

  ![api-6.png](http://www.cloud.rennes.enst-bretagne.fr/api-6.png)

- Press the button **CREATE AND START THE API** and Confirm.

  ![api-7.png](http://www.cloud.rennes.enst-bretagne.fr/api-7.png)

- Choose **PUBLISH THE API** in the red box called **Danger Zone** and Confirm.

  ![api-8.png](http://www.cloud.rennes.enst-bretagne.fr/api-8.png)

#### 6.1.2 Create the App

- Left panel: select **Applications** and click on the **+** sign (bottom right of the window)

  ![app-1.png](http://www.cloud.rennes.enst-bretagne.fr/app-1.png)

- Choose **app-photographer**, for the name of the application. Add a description text for the application. Click on **NEXT**.

  ![app-2.png](http://www.cloud.rennes.enst-bretagne.fr/app-2.png)

- Enter **web** for the type of your application. Do not enter anything for the **Client ID**. Click on **NEXT**.

  ![app-3.png](http://www.cloud.rennes.enst-bretagne.fr/app-3.png)

- Click in the top box entitled **Select an API to subscribe**, choose **api-photographer** and click on **Subscribe** for subscribing to the unique plan (There should be only one plan unless you have created several previously). Click on **NEXT**.

  ![app-4.png](http://www.cloud.rennes.enst-bretagne.fr/app-4.png)

- Click on the **CREATE THE APPLICATION** button and Confirm.

  ![app-5-bis.png](http://www.cloud.rennes.enst-bretagne.fr/app-5-bis.png)

- Congratulations. You are done. All that remains to do is to learn the key API to use to access your API. Click on **Subscriptions** (left bottom panel).

  ![app-6.png](http://www.cloud.rennes.enst-bretagne.fr/app-6.png)

- Click on your API called **api-photographer**.

  ![app-7.png](http://www.cloud.rennes.enst-bretagne.fr/app-7.png)

- The API key is visible in the **Api Keys** box. Copy it; you will need it to access your photographer service.

  ![app-8.png](http://www.cloud.rennes.enst-bretagne.fr/app-8.png)

#### 6.1.3 Curl

To check that everything is working properly, before using a browser, you will use a tool that you know well by now: `curl`.

Below is the command you need to use to transmit the HTTP header containing the API key. Of course, you will replace the **YOUR-API-KEY** string with your API key.

```
curl --insecure https://apim-xy.example.com/gateway/photographer/photographers -H 'X-Gravitee-Api-Key: *YOUR-API-KEY*'
```

Why should you use such a URI ?:

- `apim-xy.example.com/gateway/photographer` reaches your API. (yes, choosing `/photographer` to identify your API may not have been the best choice)
- `/photographers` will be sent to your `photographer` microservice.

#### 6.1.4 Introducing CORS

You will now use an extremely simple graphical user interface to access your `photographer` service from a browser. This interface (whose code is given to you) only presents a button to get the list of photographers (no matter if you have some in your database or not). This graphical interface is of no interest other than to illustrate what **CORS** is.

```
<html>  
<head>  
<script type = "text/javascript">  
  function getPhotographers() {   
    var request = new XMLHttpRequest();
    request.open('GET', 'https://apim-xy.example.com/gateway/photographer/photographers');
    request.setRequestHeader('X-Gravitee-Api-Key', 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx');
    request.onreadystatechange = function () {
      if (this.readyState === 4) {
        console.log('Status:', this.status);
        console.log('Headers:', this.getAllResponseHeaders());
        console.log('Body:', this.responseText);
      }
    };
    request.send();
  }  
</script>  
</head>  
<body>  
<input type = "button" onclick = "getPhotographers()" value = "List Photographers" style="font-size : 80px;">  
</body>  
</html>  
```

The GUI code above contains a few lines of HTML and JavaScript. When the button is pressed, the JS function `getPhotographers` is called. The body of this function is very simple:

- An `XMLHttpRequest` object is built.
- The `open` method is used to initialize the request by providing the name of the HTTP verb and the URI.
- The `setRequestHeader` is used to set the value of an HTTP request header. Of course, we provide the API key, without which we cannot query the photographer service (remember the Gravitee plan).
- The `onreadystatechange` property is set with an event handler that will be called as soon as the state of the request has changed.
- Finally, the request is sent with the `send` method.

Update your `/etc/hosts` file (on your local VM) to force the resolution of `www-xy.example.com` to the IP address of your bastion (which runs the HAproxy load balancer). 

You will now set up a WEB server in your Kubernetes cluster to serve the `index.html` file containing the GUI code. An Ingress resource will allow to reach this server from outside using the name `https://www-xy.example.com`

Download the code for this basic GUI from http://www.cloud.rennes.enst-bretagne.fr/files/app-lab/basicgui.tar.bz2. Update the `index.html` file with your own API Key. Replace `xy` in the `index.html` and `k8s-basicgui-server.yml` files with your group number. Then, build, tag (choose a name) and push the docker image to your registry. Update the name of the image in the `k8s-basicgui-server.yml` file (As you can see by examining the contents, this K8S file defines a deployment, a service and an ingress. So it has everything you need to access your web server from your local machine with the following URI: `https://www-xy.example.com`). Finally use `kubectl` to deploy your basic GUI to your Kubernetes cluster.

You will now display the graphical interface in your Firefox browser and check that you can get the list of photographers by clicking on the button.

Type the URI of your server in the address bar of Firefox and display the **Web Developer Tools** (accessible from the **Preferences** -> **Web Developer** menu)

If you have followed all the recommendations, you should have something like the screenshot below (Yes, the button is a bit prominent, but it has to take up space in our interface).

![basic-gui.png](http://www.cloud.rennes.enst-bretagne.fr/basic-gui.png)

If you click on the button, you should see (look at the console at the bottom of your Firefox window) errors like this: **Cross-Origin Request Blocked:**

What does this mean ?

For security reasons, browsers implement a **same-origin** security policy that prohibits code loaded from origin *A* from calling code on origin *B* (`www-xy.example.com` and `apim-xy.example.com` are two different origins from the browser's perspective). Obviously, this is very annoying because what you are trying to do here seems very reasonable:

- Load a GUI from a Web server.
- Query a service from the GUI (which has been designed specifically to interact with this service).

Fortunately, there is a mechanism called **CORS** for **Cross-origin resource sharing** that will allow you to unblock this situation. In a few words, the general idea is the following: The browser asks the API server at `apim-xy.example.com`: "do you allow code from `www-xy.example.com` to interact with you" ? The server at `apim-xy.example.com` can then authorize or not depending on the origin of the code. For more details, you can read this: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS.

Now, you will have to configure your gateway API to allow the code downloaded from `www-xy.example.com` to do a `GET` on the `photographer` service.

Using the gravitee console, select your API and then click on **Proxy** (bottom left panel) and click on **CORS** to configure it properly to allow your JS code (from `https://www-xy.example.com`) to do a `GET` on your `photographer` service. (The page you have to work on for configuring CORS is the one shown in the screenshot below).

![api-cors.png](http://www.cloud.rennes.enst-bretagne.fr/api-cors.png)

Each time you are asked to redeploy your API (a small yellow banner with the follwing message **API out of sync, deploy your API**appears at the top of the window when this is necessary), do so.

If you need more documentation, you can read this:

- https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Methods
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Headers
- https://docs.gravitee.io/apim/3.x/apim_publisherguide_configuring_cors.html

### 6.2 Adding a New Endpoint and a Dynamic Routing Policy

So far, we have only used one backend endpoint: the `photographer` service. Of course, the API gateway also needs to interact with the `photo` service for the application to be complete.

Using the gravitee console, select your API and then click on **Proxy** (bottom left panel) and click on **Endpoints** to bring up the interface allowing to add a backend endpoint. (The page you have to work on for adding such an endpoint is shown below).

![api-add-endpoint-1.png](http://www.cloud.rennes.enst-bretagne.fr/api-add-endpoint-1.png)

Use the interface first to rename the **Name** of the `photographer` backend (from `default` to `photographer`) and, then, to add the `photo` backend (use also `photo` for the **Name** of this new backend endpoint). You should arrive at what is shown in the screenshot below.

![api-add-endpoint-2.png](http://www.cloud.rennes.enst-bretagne.fr/api-add-endpoint-2.png)

Congratulations! You are one step closer to the final goal.

We now need to choose which API will be exposed by the Gateway API. This API will be used by the React application that will be made available to you. Of course, this API must allow you to create photographers, delete them, upload photos, display a photographer's gallery, etc. For the sake of simplicity, we will therefore propose an API that will be a sort of union of the APIs of the `photo` and`photographer` services, the API of the `tags` service not being exposed because it is used internally by the photo service only.

In addition, we also need to set up a **dynamic routing policy** to redirect requests reaching our API to the right service according to some criteria.

Using the gravitee console, select your API and then click on **Design** (bottom left panel) By doing this, you should bring up the interface as shown below.

![api-add-policy-1.png](http://www.cloud.rennes.enst-bretagne.fr/api-add-policy-1.png)

Drag and drop the **Dynamic Routing** box from the left to the middle of the window (to place it in a box whose contours are barely visible). See the screenshot below to see what you must get.

![api-add-policy-2.png](http://www.cloud.rennes.enst-bretagne.fr/api-add-policy-2.png)

The screenshot below illustrates what you need to do to make your Gateway API redirect requests to the right backend depending on the path (`/gallery`, `/photo`, etc.).

![api-add-policy-3.png](http://www.cloud.rennes.enst-bretagne.fr/api-add-policy-3.png)

Based on the model shown on the screenshot, add as many routing rules as necessary so that the API can do everything that the `photo`and `photographer` microservices offer. Of course, you have to add more routing rules than there are on the screenshot. Do not forget to save your set of rules and to redeploy the API when asked.

### 6.3 Securing the API with Keycloak and Gravitee

So far, you have successfully installed **Keycloak**, an identity manager and **Gravitee**, an API gateway. Now it's time to try to make it all work together. Our goal will be to implement a React application allowing a user to authenticate to **Keycloak** and secure access to the API using a **JWT** token. Upon authentication, the React client will retrieve the token from Keycloak and will pass it to the Gravitee API Gateway in an HTTP header. The API gateway, which will have previously learned the public key corresponding to the private key used by Keycloak to sign the token, will then be able to authorize access to the API. That seems a little complicated? the following steps will enable you to demystify all that.

The first thing to do is to create a realm called `photo` and a client in that realm called `photoapp`. The names are imperative (`photo`and `photoapp`). If you do not respect these choices, the React application that will be provided to you will not work. Below are two screenshots of Keycloak for adding a realm and a client in the realm.

![keycloak-gravitee-0.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-0.png)

![keycloak-gravitee-1.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-1.png)

Still with the Keycloak window, choose **Realm Settings** and select the **General** tab (see first screenshot below). Click on the **OpenID Endpoint Configuration**. A new Firefox tab should appear (see second screenshot below).

![keycloak-gravitee-2bis.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-2bis.png)

Copy the **jwks_uri** and keep it in a safe place, you will need it very soon. Why do we do this? Keycloak will provide the React application with a **JWT** token for each authenticated user. This token will be signed with a private key by Keycloak. The URI you just copied allows any application (for example, Gravitee) that will use it to retrieve the public key corresponding to this private key. Thanks to this public key, the Gravitee application will be able to verify the signature of a JWT token when it receives one.

![keycloak-gravitee-2ter.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-2ter.png)

Now let's switch back to Gravitee. The first thing to do is to close the plan you had previously created. Then create a new plan. Choose a **Name**, a **Description** and don't forget to to slide the latch **Auto validate subscription** before clicking on **NEXT**.

![keycloak-gravitee-2.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-2.png)

On the next screen, choose **JWT** for Authentication Type. Leave the **Signature** as it is and select **Retrieve JWKS from URL** for the **JWKS resolver**. In the **Resolver Parameter**, paste the **jwks_uri** that you had set aside a moment ago. Mind you, this isn't over … since Keycloak and Gravitee are both running in Kubernetes, Gravitee needs to be given a name that can be resolved in Kubernetes. So replace `https://keycloak-xy.example.com` by `http://keycloak:8080` Click on **NEXT**, **NEXT** and **SAVE**.

![keycloak-gravitee-3.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-3.png)

Publish the plan (see below) and do not forget to redeploy the API.

![keycloak-gravitee-4.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-4.png)

The last step is still with Gravitee but now with the application. Select your app and choose **Global settings**. Type your **Client ID** in the **OAuth2 Integration** frame. The client ID **must** match the Keycloak client ID: **photoapp**

![keycloak-gravitee-5.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-5.png)

Subscribe to this wonderful new plan (see below)

![keycloak-gravitee-6.png](http://www.cloud.rennes.enst-bretagne.fr/keycloak-gravitee-6.png)

Congratulations. You have now completed the integration of Keycloak with Gravitee and you can deploy the graphical client written in React. This client will allow a user to authenticate to Keycloak and then use your API securely through Gravitee.

### 6.4 Nginx Frontend Serving Static Files for the React Application

You need to install a Nginx service that will serve the React files of the WEB application.

Download the k8s file for the Nginx frontend serving the static files for the React application.

```
> wget http://www.cloud.rennes.enst-bretagne.fr/files/app-lab/k8s-web-frontend.yml
```

Deploy this frontend to your Kubernetes cluster.

The WEB application is available at `https://www-xy.example.com` provided of course that your hosts file resolve the `www-xy.example.com` to the floating IP of your bastion.

Author: Alberto Blanc, Jean-Pierre Le Narzul

Created: 2023-01-13 Fri 00:42

[Validate](https://validator.w3.org/check?uri=referer)