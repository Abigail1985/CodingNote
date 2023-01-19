
## 1 Introduction

According to its website: "Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications." In other words, you can use Kubernetes to run applications comprised of one or more containers on a cluster of physical or virtual machines. Kubernetes takes care of starting and stopping containers on one (or more) of the nodes of the cluster.

The goal of this lab is to discover the main abstractions of Kubernetes.

## 2 Kubernetes

The Kubernetes cluster has been installed on the dedicated machines in your OpenStack project.

### 2.1 SSH Configuration

Configure ssh, by editing the `~/.ssh/config` as you have done in the previous labs, so that you can use the bastion to connect to the controller and to the workers. Make sure that the names you use to connect to each VM (i.e., the word after the `Host` keyword) are exactly the same as the names of the VMs. For instance, if you first controller is called `k8s-cont`, there must be an entry with `Host k8s-cont` in your ssh configuration file. 

### 2.2 Accessing your K8S Cluster from the Controller

You can interact with your cluster by logging to the controller and using `kubectl` on the controller itself.

Execute the command `kubectl get nodes` to make sure that your cluster is up and running. If this is indeed the case, you should have a list of four nodes, with one controller and three workers.

### 2.3 Accessing the K8S Cluster from your VM

As the controller does not have a floating IP address, you will use the bastion to interact with your K8S cluster from your local VM. For this purpose, a **HAProxy** server has been installed on your bastion to redirect K8S requests (on port 6443) to your controller.

Check the configuration of HAProxy on the bastion.

It is also possible to use your cluster remotely by using `kubectl` on your local VM and by using the appropriate configuration file.

Copy the Kubernetes config file in the appropriate directory on your local VM and change the IP address in the `config` K8S file so that you can interact with your cluster using `kubectl`.

## 3 Kubernetes Secret

Now you have to go one step further and allow your Kubernetes nodes to access the private registry associated with your GitLab project. Thereafter, we consider that you have successfully completed Section 2.4.1 of the Docker lab. You are going to use a **Kubernetes secret** to store the token giving access to the private registry. As indicated by the official Kubernetes documentation:

> **Kubernetes Secrets** let you store and manage sensitive information, such as passwords, tokens, and keys. Storing confidential information in a `Secret` is safer and more flexible than putting it verbatim in a Pod definition or in a container image.

[[k8s的secret]]
[[k8s使用secret为pod拉取镜像]]


Create a Secret based on your existing Docker credentials

```
> kubectl create secret generic regcred \
  --from-file=.dockerconfigjson=/home/user/.docker/config.json \
  --type=kubernetes.io/dockerconfigjson
```

When writing a Kubernetes manifest for pod that uses a docker image stored on the private registry, you must use this secret.

> [!important] 
> 在为使用存储在私有注册表中的 docker 映像的 pod 编写 Kubernetes 配置时，您必须使用此密钥。

## 4 First Steps with your Kubernetes Cluster

All the files mentioned below are available at the following address: 

```
www.cloud.rennes.enst-bretagne.fr/files/k8s-lab
```

### 4.1 Pods

In Kubernetes, you cannot run containers directly, you must put them inside a "pod." For the sake of simplicity, you can just think of a pod as a container running in Kubernetes. (It is actually possible to have more than one container inside the same pod, but we will not use this option.) You will start by creating (i.e., running) a pod containing a container built using the `Dockerfile` that you can find at the aforementioned url.

Download the `myindex.html` file (again, from the same url as above). Build, tag, and push the Docker container image (replace `theuser` and `theproject` by appropriate values). Make sure that the build succeeds!

```
> docker build -t test-webserver .
> docker tag [ImageID] gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-xy/k8s_test
> docker push gitlab-registry.imt-atlantique.fr/devops-s23/devops-s23-xy/k8s_test
```

While you can interact directly with a Kubernetes cluster using only the command line utility `kubectl`, it is often easier to use yaml files to store the description of different resources (pods, deployments, services, etc.).

Take a look at the `test-webserver-pod.yml` (available at the url mentioned above), which is a very simple file describing a pod based on the image that you just created and pushed. All parameters should be self-explanatory and it should be clear that you must change `theuser` and `theproject` by appropriate values.

Start a pod, and make sure that it is running:

```
> kubectl apply -f test-webserver-pod.yml 
> kubectl get pods
```

The output should be something like the following, at least at first:

```
NAME             READY     STATUS              RESTARTS   AGE
test-webserver   0/1       ContainerCreating   0          7s
```

If everything goes well the output should then change to:

```
NAME             READY     STATUS    RESTARTS   AGE
test-webserver   1/1       Running   1          10m
```

To get more details about your pod, execute the following command:

```
> kubectl describe pod test-webserver
```

You might have noticed that the output does contain the IP address of the pod. In Kubernetes, each pod has its own IP address, which is unique inside the cluster. 

You can verify that your pod is indeed serving a web page by starting another pod in the cluster and get a shell prompt with the following commands (wait for a prompt before typing the second command):

```
> kubectl run -i --tty alpine --image=gitlab-devops.cloud.rennes.enst-bretagne.fr:4567/devops/shared/alpine-curl -- sh
\# curl [IP address of the test-webserver pod]:8000
```

If everything goes well, you should see the contents of the index.html file that you put in your container. The first command starts a container based on the Alpine Linux image, which is known for being small. The second line uses curl to connect to the `test-webserver` pod.

In case you want to connect again to the alpine container you can get its name with `kubectl get pods` and then reopen a shell with:

```
> kubectl attach container_name -c alpine -i -t
```

It is a good idea to delete the pod once your are done with it:

```
> kubectl delete pod test-webserver
```

### 4.2 Deployments

As you know, horizontal scalability is one of the key elements of cloud native applications. In the case of Kuberenetes, or any other container orchestrator, for that matter, this means starting new containers (pods) when the load increases. As you have just seen, it is possible to achieve this by starting new pods based on the same image. But such a manual approach is unfeasible in a production environment. This is why Kubernetes uses "deployments" to handle the creation (and destruction) of pods.

From the official [documentation](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-intro/):

> The Deployment instructs Kubernetes how to create and update instances of your application. Once you have created a Deployment, the Kubernetes controllers schedule application instances onto individual Nodes in the cluster. Once the application instances are created, a Kubernetes Deployment Controller continuously monitors those instances. If the Node hosting an instance goes down or is deleted, the Deployment controller replaces it. This provides a self-healing mechanism to address machine failure or maintenance.

The `test-webserver-deployment.yml` file contains the parameters of a test deployment. This file is slightly more complicated than the one of the pod. The first thing to notice is the `template` section in the `spec` section: it contains another `spec` section that describes the container template. Unsurprisingly, this section is identical to the one in the `test-webserver-pod.yml` file. The difference is that the `template` section contains a `metadata` section that specifies a "label."

From the [official documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/):

> Labels are key/value pairs that are attached to objects, such as pods. Labels are intended to be used to specify identifying attributes of objects that are meaningful and relevant to users, but do not directly imply semantics to the core system. Labels can be used to organize and to select subsets of objects. Labels can be attached to objects at creation time and subsequently added and modified at any time. Each object can have a set of key/value labels defined.

In other words, all containers created by this deployment will have the `app:webserver` label.

The outer `spec` section contains a `selector` section, indicating that this deployment applies to all the containers with the `app: webserver` label.

Now that you know what a deployment is, you can actually create it:

```
> kubectl apply -f test-webserver-deployment.yml
```

You can get a list of deployments with:

```
> kubectl get deployments
```

You should see your deployment. You should also see three new pods if you run `kubectl get pods`.

If you still have the alpine container running, you can use it to make sure that there are indeed three different containers running a webserver. (Refer to the previous section for instructions on how to create the alpine container, get the IP addresses of each pod, and use `curl`.)

It is now time to destroy the deployment before moving the to the next section:

```
> kubectl delete deployment test-webserver-deployment
```

### 4.3 Services

Thanks to deployments, it is easy to tell Kubernetes how many instances of a given container must be running at all times. The astute reader might be wondering how can clients access these different containers. The solution presented in the previous section, based on running `kubectl describe container_name` to get the IP address of each container, is obviously a non-starter for a production environment. Kubernetes has, yet another, abstraction for this: services.

From the [official](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/#creating-a-service) documentation:

> So we have pods running nginx in a flat, cluster wide, address space. In theory, you could talk to these pods directly, but what happens when a node dies? The pods die with it, and the Deployment will create new ones, with different IPs. This is the problem a Service solves.
>
> A Kubernetes Service is an abstraction which defines a logical set of Pods running somewhere in your cluster, that all provide the same functionality. When created, each Service is assigned a unique IP address (also called clusterIP). This address is tied to the lifespan of the Service, and will not change while the Service is alive. Pods can be configured to talk to the Service, and know that communication to the Service will be automatically load-balanced out to some pod that is a member of the Service

As usual, you can use a yaml file to specify the parameters of a service. Look at the file `test-webserver-service.yml` for an example. Again, we use a label selector to specify which pods are the back-ends of this service (i.e., which containers implement the service).

Create a service, and get its description:

```
> kubectl apply -f test-webserver-service.yml 
> kubectl describe service webserver
```

The output should be something like this:

```
Name:              webserver
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=webserver
Type:              ClusterIP
IP:                10.96.173.138
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```

Note how the `Endpoints` are empty: this is because, as long as you did delete the deployment, there are no running containers with the`app: webserver` label. 

Let's change this by creating the deployment again, and then get the service description:

```
> kubectl apply -f test-webserver-deployement.yml
> kubectl describe service webserver
```

Now the `Endpoints` should not be empty anymore. You can user the alpine container to verify that you can curl the service IP (10.96.173.138 in the example above).

While using the ClusterIP does work, it has the significant downside that its value changes every time the service is created. Therefore you cannot use in your source code. Luckily, Kubernetes supports using the name of the service. 

You can verify this by running the following in the alpine container:

```
# curl webserver:8000
```

### 4.4 Exposing an Application (Port) Outside the Cluster

If you want to `test-webserver` service to be accessible from outside the cluster, you can use the `test-webserver-service-nodeport.yml` file. This file defines the service as a `nodePort` service, forcing Kubernetes to make it accessible on the `30300` port on each VM in the cluster. In other words, you can connect to port `30300` on **any** of the VMs of your Kubernetes cluster and Kubernetes will route your request to the VM where the pod is actually running. You should also be able to access the service from outside Open Stack by using the floating IP that you have assigned to the bastion.

One can omit the `nodePort: 30300` line. In such a case, Kubernetes will pick a port at random in the between 30000 and 32767. It is possible to known which port was selected with `kubectl describe service service_name`.

Add the following lines at the end of the HAProxy configuration file (`/etc/haproxy/haproxy.cfg`) and replace the `your_nodeport` with the port you choosed (or the one picked by kubernetes):

```
listen kubernetes-nodeport
  bind *:80
  option ssl-hello-chk
  mode tcp
  timeout client 3h
  timeout server 3h
  server k8s-cont 192.168.10.31:your_nodeport
  balance roundrobin 
```

Author: Alberto Blanc, Jean-Pierre Le Narzul

Created: 2023-01-04 Wed 14:38

[Validate](https://validator.w3.org/check?uri=referer)