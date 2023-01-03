


## 1 Introduction

OpenStack is an IaaS (Infrastructure as a Service) cloud computing platform. One can use it to create virtual machines, allocate block storage ressources, create virtual networks and so on. Emmanuel Braux, from DISI, has installed an instance of OpenStack (thanks Emmanuel!) on a set of physical nodes, located on the campus in Brest. Starting tomorrow, and throughout this module, you will use the resources provided by this IMT Atlantique instance of OpenStack. Today, you will use another OpenStack platform installed by the teaching staff. You will pay close attention to the information given on the board by the teachers regarding these two platforms.

Throughout the course, you will work on a photo sharing application. This application will use a MongoDB database. MongoDB is an open source, document-oriented database. It supports replication and sharding (providing horizontal scalability). As a NoSQL database, MongoDB discards the traditional table-based relational model. In MongoDB, data is stored in JSON documents with no predefined data format. 

The goals of this lab are the following:

- Discover how to use OpenStack and more specifically, its command-line interface (CLI).
- Install and configure a MongoDB database on an OpenStack server.
- Automate actions using Open Stack Heat templates and Ansible. While it does take some time to learn these new tools, they are an essential DevOps ingredient.

## 2 Creating your First Network and Server

You have already taken the necessary steps to be able to use your OpenStack project:

- you know that you have to source a file (`devops-s23-xy-openrc.sh`) before you can use the CLI
- you have already (each and every one of you) created a keypair so that you can connect via SSH to your future OpenStack servers.

The next steps will only have to be executed by one of the two members of your pair. We suggest you use a Zoom session and screen sharing so that the one who does not perform the steps can follow and help the one who does. Remember that this is group work (in pairs).

### 2.1 Create a Security Group

A security group is a set of IP filter rules that define which packets are allowed to pass and which are not. When instanciating a virtual machine, one has to choose the security group that will filter the traffic to this virtual machine. Here, you will create a new security group, called `MYSECGRP` and you will add two rules to authorize SSH and ICMP (ping) traffic to the (future) instance. An alternative would be to use the `default` security group and to add rules to it.

> [!NOTE] 安全组是什么
> 安全组是一组IP过滤器规则，定义哪些数据包被允许传递，哪些不允许传递。
> 在实例化虚拟机时，必须选择将过滤此虚拟机流量的安全组。
> 在这里，您将创建一个名为`MYSECGRP`的新安全组，并将添加两条规则，以授权SSH和ICMP（ping）流量进入（未来）实例。
> 另一种选择是使用`default`安全组并向其添加规则。


- [ ] 创建安全组
Create a security group and its associated rules using the following OpenStack commands:

```
   (openstack) security group create MYSECGRP
   (openstack) security group rule create --ingress --protocol icmp MYSECGRP
   (openstack) security group rule create --ingress --protocol tcp --dst-port 22 M
YSECGRP
```

You have now completed all the preparatory (and tedious) steps. Now you will be able to do something more rewarding: create a network and instanciate a virtual machine.

### 2.2 Create a Network

The network topology that you will create is illustrated by the following figure:

![OS_network_topology-v2.png](http://www.cloud.rennes.enst-bretagne.fr/OS_network_topology-v2.png)

You may see the `external` network. Additionnaly, there is the `MYNET` network and its associated subnet (192.168.2.0/24) that you will also create soon in your OpenStack project. A router (called `MYROUTER`) interconnects the `external` network and the `mynet` network. A instance of a virtual machine (called `MYSERVER`) is attached to the 192.168.2.0/24 subnet. Once again, do not pay attention to the `common` network. You will not use it.

- [ ] 创建网络和虚拟子网
- Create a network and its associated subnet using the following OpenStack commands:

```
   (openstack) network create MYNET
   (openstack) subnet create MYSUBNET --network MYNET --subnet-range 192.168.2.0/2
4 --dns-nameserver 192.44.75.10
```

- [ ] 显示网络拓扑
- Use the dashboard to display your network topology.

Once the private network and subnets have been created, it is time to create the router, to set its external gateway and to link it to your private network.

- [ ] 创建路由器并配置
- Create a router and configure it using the following OpenStack commands:

```
(openstack) router create MYROUTER
(openstack) router set MYROUTER --external-gateway external
(openstack) router add subnet MYROUTER MYSUBNET
```

### 2.3 Create a Server

Congratulations! You have just configured your first private network and you are now ready to create an instance of a virtual machine (called a Server in the OpenStack vocabulary) and make it reachable from your local machine. 

- [ ] 选择服务器的flavor

When creating a Server, one has to choose the flavor, i.e., a set of values for the Virtual CPUs (vCPUs), memory and disk, as well as the image that will be used to boot the virtual machine.

To display the list of available flavors, execute the following OpenStack command:

```
(openstack) flavor list
```

At the time of writing this text, nine flavors are available: from `m1.nano` (targeted for `CirrOS`, a minimal Linux distribution) to `s20.large` (for a large server with a lot of RAM). For your first instance, you will use the `s10.small` flavor which will be enough for running a MongoDB test server on Ubuntu.

> [!关于flavor] 服务器的flavor的选择
> 有九种口味可供选择：
> 从`m1.nano`（针对`CirrOS`，最小的Linux发行版） 到
> `s20.large`（适用于具有大量RAM的大型服务器）。
> 
> 首先，**您将使用`s10.small`口味**，这足以在Ubuntu上运行MongoDB测试服务器。


- [ ] 选择可用的镜像
To display the list of available images, execute the following OpenStack command:

```
(openstack) image list
```

At the time of writing this text, one image is available. You will use the `ubuntu20` image to boot an operating system.

> [!NOTE] 服务器镜像的选择
> 您将使用**`ubuntu20`**映像启动操作系统。


- [ ] 创建openstack服务器，自己在文档里搜索怎么办
Create a server using the appropriate OpenStack command. 

**Hint**: Search in the OpenStack documentation how to create a server. Please, do not forget to use the `MYSECGRP` security group and the `mykey` keypair. You will use `MYSERVER` as an OpenStack name for your server.

If you have successfully created your server, you are almost done. Your virtual machine has been created but you still can't connect to it from your local machine because you did not create yet a floating IP on the external network.


> [!NOTE] 创建openstack服务器注意
>在OpenStack文档中搜索如何创建服务器。请不要忘记
>- 使用`MYSECGRP`安全组和`mykey`密钥对。
>- 您将使用`MYSERVER`作为服务器的OpenStack名称

- [ ] 创建floating IP
Create a floating IP using the following OpenStack command: 

```
(openstack) floating ip create external
```

A floating IP has been allocated to your OpenStack project on the external network. Now, you need to associate it to your virtual machine.

- [ ] 关联floating IP和服务器
Associate the allocated floating IP to your instance using the following OpenStack command: 

```
(openstack) server add floating ip MYSERVER 10.37.ab.cd
```

### 2.4 SSH to the Virtual Machine

==建立本地计算机到Openstack服务器的连接==

You should be able to **establish an SSH connection to your OpenStack server from your local machine**. Recall that when you have created your server, you have injected the keypair `mykey` (well, you were supposed to do that). It means that to SSH your server, you must provide the name of the local file containing the private key (its name should be `~/.ssh/osvm`).

- [ ] 为托管私钥的文件设置适当的 unix 权限，否则 ssh 会报错
Please, set the appropriate unix rights to the file hosting the private key or ssh will complain about that.

```
> chmod 700 ~/.ssh/osvm
```

- [ ] 从本地计算机ssh到Openstack服务器
SSH to your server using the following Shell command.

```
> ssh -i ~/.ssh/osvm ubuntu@10.37.ab.cd
```

### 2.5 Administrative Tasks on your Server

您的OpenStack服务器无法完全访问互联网（出于安全原因）。不要惊慌。
1）您可以访问一些基本服务
2）APT服务器代理已部署在您可以从服务器访问的主机上。在以下内容中，假设您已进入服务器。

Your OpenStack server does not have a full access to the Internet (for security reasons). Don't panic. 1) You may access some essential services 2) An APT server proxy has been deployed on a host that you can access from your server. In the following, it is assumed that you have ssh'ed into your server.

- [ ] 将主机名（`MYSERVER`）添加到`/etc/hosts`文件中的127.0.0.1行中（不要忘记使用`sudo`来修改此文件）
Add the hostname (`MYSERVER`) to the 127.0.0.1 line in the `/etc/hosts` file (do not forget to use `sudo` to be allowed to modify this file).

- [ ] 将语言设置为默认语言
Execute the following shell command to set the language to the default language

```
> export LC_ALL=C
```


- [ ] 更新可用软件包的列表并安装更新
Execute the two following shell commands to update the list of available packages and to install the newer versions of theses packages. Be patient … upgrading the system takes a while.

```
> sudo apt update
> sudo apt upgrade
```

## 3 Installing MongoDB by Hand

### 3.1 Installation of Mongo on the OpenStack server

You will now install the MongoDB database on your OpenStack server. You **must** ssh to your server to execute the installation steps.

- [ ] 在Openstack服务器安装MongoDB
Install MongoDB on your server by following the official tutorial: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/

- [ ] 配置MongoDB保证外部访问
You must additionnaly change a configuration parameter to make your MongoDB server reachable from any machine on the Internet (including your local machine).


> [!NOTE] MongoDB配置
> 修改`/etc/mongod.conf`文件中以`bindIp`开头的行。将`127.0.0.1`替换为服务器的IP地址（`MYNET`网络上的地址）。执行`sudo systemctl restart mongod`使用此新配置参数重新启动MongoDB服务器

Modify the line starting with `bindIp` in the `/etc/mongod.conf` file. Replace the `127.0.0.1` with the IP address of your server (the address on the `MYNET` network). Execute the following command to restart the MongoDB server with this new configuration parameter.

```
> sudo systemctl restart mongod
```

### 3.2 Install of Mongo Shell on your local VM

- [ ] 在本地虚拟机上安装Mongo Shell
Install the Mongo Shell on your local VM according the same tutorial: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/. Just replace mongodb-org by mongodb-org-shell when executing the `apt install` command.

> [!NOTE] MongoShell安装
> 按照同一教程在本地虚拟机上安装Mongo Shell：[https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)。在执行`apt install`命令时，只需将mongodb-org替换为mongodb-org-shell即可。


### 3.3 Connect to your MongoDB Server

You will now use the mongo shell to test your installation. The MongoDB server is running on your OpenStack server. You will start the mongo shell on your local machine.

Execute the following shell command on your local machine.

```
> mongo --port 27017 --host 10.37.ab.cd
```

Does it work ? Normally, you should encounter some connection problems: "Failed to connect to …".

Think about what is happening and find a way to solve the connection problem. Hint: think about that you did to make ssh work earlier in the lab.

### 3.4 Interact with your MongoDB Server

Let's now create a database and add some documents in a collection using the Mongo shell. In the following, we suppose that you have successfully connected to the MongoDB server running on you OpenStack server. You will create a database named `photo` and you will insert a few documents in a collection named `photographers`. In our example, a document stores the name of a photographer along with the list of his preferred photographic interests.

Execute the following Mongo commands on your local machine from the Mongo shell.

```
> use photos
> alice = {"name": "alice", "interest": ["landscape"]}
> db.photographers.insert( alice )
> bob = {"name": "bob", "interest": ["landscape", "street"]}
> db.photographers.insert( bob )
> charlie = {"name": "charlie", "interest": ["street", "portrait"]}
> db.photographers.insert( charlie )
```

As you may have guessed, `use photos` creates a database named `photos`. `db` refers to the current database and using `db.photographers` creates a collection named `photographers` in the `photos` database. To list all the documents in the `photographers` collection you may execute the following command:

```
> db.photographers.find()
```

To view only the photographers who are interested by street photography, you may execute the following command:

```
> db.photographers.find( {"interest": "street"} )
```

### 3.5 Debugging MongoDB problems

Sometimes, not everything works on the very first try … If you encounter problems with MongoDB, you can ssh into the server and use the following command to find out if the server process is running or not:

```
> sudo systemctl status mongod
```

The output of this command will tell you whether the MongoDB server is running or not and if it is not, it will also show an error message.

Most server programs in Linux (and Unix in general) write messages to log files in the `/var/log` directory. MongoDB creates its own sub-directory (`/var/log/mongodb`). You can use the `less` command to display log files (and any other text file).

## 4 Discovering Ansible



Ansible ([http://ansible.com](http://ansible.com/)) is an automation tool that allows to install and configure remote systems and apps (it can do even more but we will mainly use it for this function). The following figure (borrowed from [https://sysadmincasts.com](https://sysadmincasts.com/)) illustrates the way Ansible works. From a management node, on which Ansible is installed, remote systems are configured and applications are installed and configured. The Ansible management node connects to each managed node thanks to SSH. As illustrated by the figure, the management node uses an inventory file and a playbook file. The inventory file, as its name suggests, contains a list of the managed nodes (DNS name and/or IP addresses). Ansible Playbooks contain the list of tasks to be executed on the managed nodes.

![ansible.png](http://www.cloud.rennes.enst-bretagne.fr/ansible.png)

It is time now to prepare an inventory file with the floating IP address of your OpenStack server.

Put the following contents (replace w.x.y.z with the floating IP address of your OpenStack server) in a file named `inventory` on your local VM.

```
w.x.y.z
```

On your local VM, execute the following ansible command. 

```
> ansible all -i inventory -u ubuntu --private-key ~/.ssh/osvm -a "/bin/echo hello"
```

You have executed your first ansible command and you have used an inventory file to select the target of the command. What makes Ansible interesting for installing and configuring remote servers is the possibility of writing complex playbooks. A playbook is a YAML file; in its simplest form, it is used to map hosts to tasks. Let's see an example with the following playbook:

```
---

- hosts: all  

  tasks:
    - name: Add a user
      user:
        name: bob
        group: admin
        state: present
      become: true
      become_user: root
```

There is only one task in this playbook. Its name is `user`. It is used to add a user account to a computer (server), or more precisely, to ensure that a user account is present (or absent) in a system. The `hosts: all` line means that we want to apply the tasks to all the hosts in the inventory file.

From your local VM, download the playbook file from the following address: http://www.cloud.rennes.enst-bretagne.fr/files/openstack-lab/user-playbook.yml

Execute the ansible playbook using the following command.

```
> ansible-playbook -i inventory -u ubuntu --private-key ~/.ssh/osvm user-playbook.yml
```

The first time you execute the playbook, the `bob` user account is added on the OpenStack server. If you execute the playbook once more, ansible will note that the `bob` user account is present and will do nothing.

## 5 Deleting your Network and your VM

Execute the following OpenStack commands to delete the server, the router and the network. As you can use only a limited amount of resources in OpenStack, it is important to delete these elements in order to release the resources that they are using.

```
(openstack) floating ip delete ...
(openstack) server delete MYSERVER
(openstack) router unset --external-gateway MYROUTER
(openstack) router remove subnet MYROUTER MYSUBNET
(openstack) router delete MYROUTER
(openstack) network delete MYNET
(openstack) security group delete MYSECGRP
```

## 6 Discovering Heat

As stated in the OpenStack documentation (https://docs.openstack.org/heat/rocky/), /"Heat is a service to orchestrate composite cloud applications using a declarative template format through an OpenStack-native REST API"/. What does it mean? Instead of executing multiple OpenStack Shell commands to provision your resources (remember what you have done to get a Server reachable from your local VM), you will describe all the OpenStack resources you need in YAML files (called heat templates) and you will use a `stack create` command to provision your infrastructure. 

Download the following three files:

- http://www.cloud.rennes.enst-bretagne.fr/files/openstack-lab/mongo-bastion-stack.yml
- http://www.cloud.rennes.enst-bretagne.fr/files/openstack-lab/vm-template.yaml
- http://www.cloud.rennes.enst-bretagne.fr/files/openstack-lab/vm-keys.yml

The first file is the "main file" that defines all the resources that we want OpenStack to create. In order to minimize "copy and paste" operations, the second file (`vm-template.yaml`) contains a parametrized sub-template that creates a single VM. Note that the extension of this file **must be `.yaml` and not `.yml`**, otherwise it does not work. The third file (`vm-keys.yml`) contains a list of public keys that will be injected in each VM. This way, the owners of the corresponding private keys will be able to connect to the VMs. This way the teaching staff will be able to connect to your VMs. Note that, in real life, you must be extremely careful about accepting public keys and injecting them in a VM as this operation gives complete access to your VMs.

Execute the following bash command (copy/paste it) to add your public key to `vm-keys.yml` (you **must** be in the directory where the `vm-keys.yml`is located **before** executing this command).

```
{ (echo -n "    - " && cat ~/.ssh/osvm.pub) > /tmp/o; (mv vm-keys.yml /tmp/k); (cat /tmp/k /tmp/o > vm-keys.yml) }
```

At this point, the public keys of the teachers and of the one of you who executes the steps are in the `vm-keys.yml` file. It therefore remains to add the public key of your colleague, who is part of your pair.

Make sure you retrieve your colleague's public key and copy it into an `osvm2.pub` file and execute the following command.

```
{ (echo -n "    - " && cat ~/.ssh/osvm2.pub) > /tmp/o; (mv vm-keys.yml /tmp/k); (cat /tmp/k /tmp/o > vm-keys.yml) }
```

Like the Python programming language, YAML relies on indentation to delimit code blocks, rather than curly braces or specific statements. Please, pay attention to this when editing heat templates and configure your editor to introduce spaces when you press the TAB key. As most of the lines in the Heat template are self explanatory, we will not comment on each one. However, it is worth calling your attention to the `init_script` resource in the `vm-template.yaml` file. This small initialization script adds the name of server in `/etc/hosts` and sets up an APT proxy. You can find a complete guide to the Heat templates on the official OpenStack website: https://docs.openstack.org/heat/rocky/ in the "Template Guide" section. Note that you are using the rocky release of OpenStack. Make sure that you are reading the documentation for that version!

The provided template is **incomplete** but it is functional. As explained in the `description` key (see the first lines), this template is supposed to provision two servers: one for a Mongo server and one for a Bastion server. The idea is to use the Bastion server to install and to configure the MongoDB server. The Bastion server will be accessible from the outside on port 22 (for ssh). You will complete this template to achieve this goal. For the moment, let's try to provision the resources described by the template or - in other words - to create the stack described by the YAML file.

Execute the following openstack command to create the stack.

```
> openstack stack create -t mongo-bastion--stack.yml -e vm-keys.yml singlemongo
```

Use the dashboard to display the resources of your stack.

If you select the `Resources` tab of your `singlemongo` stack, you should see that nine resources have been created.

Delete your stack.

### 6.1 Modify and Execute a Stack

As explained above, the stack is incomplete. Only the bastion server has been defined in the heat template.

Discuss inside your group to identify what has to be done (in terms of resources to be provisioned) so that the Bastion server is accessible from the outside on port 22 (for ssh) and the Mongo server is accessible only from the internal network (on any port).

Modify the stack and execute the appropriate command to create it.

## 7 Installing MongoDB with Ansible

Now, you will learn how to write your own playbook to install and configure applications on the Mongo OpenStack server (well, it's not yet a Mongo server but it will soon be one, if everything goes according to plan).

### 7.1 Using a Jump Host

You have recently used Ansible to automate some actions on an OpenStack server (see Section [4](http://www.cloud.rennes.enst-bretagne.fr/openstack-lab.html#orgd41af23)). The main difference is that you are no more using two virtual machines but three virtual machines (your local VM, the Bastion OpenStack server and the Mongo OpenStack server) and that you can’t directly reach the Mongo OpenStack server (the one on which you need to execute some actions) from your local VM. One possibility could be to install Ansible on the Bastion OpenStack server and to use it as the management node to execute actions on the Mongo OpenStack server. It works well but it is cumbersome as it requires to transfer the playbook and the inventory on the Bastion server. Not a big deal when the playbook is fully functionnal but far from ideal when the playbook has to be debugged, edited on your local VM, transferred to the Bastion server, again and again. Instead, you will still use your local VM as the management node for Ansible and you will ask Ansible to use the Bastion server as a "jump host" for accessing servers for which you do not have direct access (i.e. the Mongo server).

#### 7.1.1 Set up SSH to use a Jump Host

To use the the Bastion server as a jump host, you have to define a `config` file in your `~/.ssh` directory on your local VM. This file must contain two entries: one for the Bastion and one for the Mongo host. The following `config` file allows to connect to the Mongo host (private address: `192.168.2.2`) using the bastion host (public address: `10.29.244.83`) as an intermediary host.

```
Host bastion
  Hostname 10.29.244.83
  User ubuntu

Host mongo
  Hostname 192.168.2.2
  ProxyJump bastion
  User ubuntu
```

The following command allows to connect by SSH to the mongo host.

```
> ssh mongo
```

#### 7.1.2 Make Ansible use the SSH config File

There is nothing special to do or to write to make Ansible use the bastion host to reach the Mongo host. Just build an inventory file containing the name given to your host in the SSH config file (mongo) and Ansible will use the information from the SSH config file (path to the key, user user and use of the bastion). to reach the Mongo host. With this simple inventory file:

```
mongo
```

the following ansible command will execute the echo program on your Mongo server using the Bastion server as an intermediary host.

```
> ansible all -i inventory -a "/bin/echo hello"
```

### 7.2 Write the playbook

You are now ready to write your own playbook to install and start MongoDB on the Mongo OpenStack server by using the bastion as an intermediary host. Please, refer to the instructions given for a manual installation of MongoDB to determine the actions that should be driven by Ansible. Do not hesitate to use the following Ansible modules: `apt_key`, `apt_repository`, `apt` and `service`.

Write the ansible playbook and test it.

## 8 Key takeaways

- OpenStack is a IaaS platform;
  - It provides VMs, virtual networks, virtual disks, virtual routers, etc.
  - One can use the openstack command-line-client to create, query, delete, and modify resources (VMs, networks, etc.).
  - It is possible to write programs to automate the creation/destruction of resources (you did not do this in this class though).
  - Heat templates describe a collection of resources (VMs, networks, subnets, etc.). This is much faster that using the command line client. (Heat templates are specific to OpenStack but each cloud platform has something equivalent. For example, AWS has CloudFormation).
- Performing system administration tasks by hand is time consuming and error prone. (Installing software, adding users, changing file/directory permissions, etc.)
- Ansible allows to automate system administration tasks:
  - The inventory file contains a list of machines, possibly divided into different groups, on which tasks will be executed.
  - Playbooks are yaml files that describe the tasks that have to be executed on the different machines. It is possible to specify that a task (or set of tasks) must be performed only on the machines belonging to a certain group.
  - Ansible uses ssh to connect to each machine.
  - With Ansible it is trivial to perform the same tasks on many machines: it suffices to add them to the inventory file.
  - Ansible playbooks are supposed to be "idempotent" so that they can be executed multiple times, for example in the case of connection errors, without any effect on the final result.
  - It is up to the author of the playbook to make sure that it is indeed idempotent, for example by stating that a certain directory (or file) must be present rather than issuing a command to create the directory (or file).
- MongoDB is a NoSQL database that stores (json) "documents" that are organized into different "collections."
  - MongoDB does not enforce a "schema": documents belonging to the same collection can have different fields. It is, nonetheless, a good idea to enforce a schema and make sure that all documents in a collection have the same schema (or, at the very least, that they all have certain fields).
  - MongoDB does not use SQL as its query language.

