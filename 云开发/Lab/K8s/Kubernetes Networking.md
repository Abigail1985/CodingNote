

## 1 Introduction

As Kubernetes is not an Infrastructure as a Service (Iaas) platform, it does not offer the "virtual version" of "real devices." Its goal is simply to run containers on a cluster of (physical or virtual) machines. Kubernetes requires that all containers can communicate directly at layer 3, without any NAT between them. Containers must also be able to send/receive IP packets to/from the (physical or virtual) machines of the cluster.

Kubernetes does not specify how satisfy these constraints. It is up to the operator of the cluster to make sure that they are. Several solutions are possible. From very simple ones, requiring the operator to insert routes by hand on the different machines, to more viable ones, that are completely automated. Calico, Flannel, and Kube Router are some of the possible fully automated solutions, just to name a few.

Contrary to Open Stack, Kubernetes typically uses only a single network connecting all the nodes. One of the reasons is that Kubernetes is often deployed on top of a IaaS platform (such as Open Stack) that usually provide only one network interface on each VM by default. It is possible, at least in theory, to use multiple networks but such a solution is rarely used.

Your Kubernetes cluster uses Kube Router, as it is probably the easiest solution to understand. All the Kubernetes (control and pod) traffic goes through the "provider" network of your previous Open Stack installation. For the sake of simplicity and consistency the `enp1s0f2`interface on each machine is still connected to VLAN number 3 on the TP-Link switch and has the same address (of the form `10.x.y.12`, `10.x.y.22`, `10.x.y.32`, `10.x.y.42` for the `controller`, `compute1`, `compute2`, and `compute3` respectively, with `x` equal to the room number (`27` or `30`) and `y` equal to the bench number).

Given that, starting from the next lab, you will use an object storage with Kubernetes, the `enp1s0f0` interface of each machine is still connected to the "management network" (VLAN number 2 with the same IP addresses as before.

### 1.1 Kube Router Overview

Kube Router assigns a different IP (sub) prefix to each (physical or virtual) machine in the cluster. It can therefore assign an IP address from one of these prefixes for each pod. For instance, if it assigns the 10.235.67.0/24 to the node `compute1`, all the pods running on that node will have an IP address belonging to that prefix (e.g., 10.235.67.3, 10.235.67.35, 10.235.67.23).

Kube Router configures the routing tables on each machine with the appropriate entries. For example, if the 10.235.67.0/24 corresponds to `compute1`, whose IP address is 10.27.4.22, Kube Router will add a route for 10.235.67.0/24 on all the other machines in the cluster with 10.27.4.22 as the next hop.

Using the command `ip route` on each machine, determine the IP prefixes that Kube Router has assigned to each node (including the controller). 

Kube Router uses the BGP routing protocol to populate these routing tables. This way it is possible to add more nodes to the cluster, or additional sub-prefixes to any node that needs to host more than 253 containers (253=256-3 because, as always, the first and last IP address in a prefix are reserved, and an additional IP address on each node is assigned to the `kube-bridge` in order to provide the "default gateway" (or "default router") for the pods).

### 1.2 Interacting with your Kubernetes cluster

Just like OpenStack, Kubernetes has its own command line client: `kubectl`. With OpenStack, you had to source the "rc" file containing the configuration parameters needed by the command line client. `kubectl`, instead, reads a configuration file containing all the required parameters. By default, this file is `~/.kub/config`. You can find the configuration file for your cluster in `/etc/kubernetes`. 

Execute the following commands **on the controller** to copy it where `kubectl` will find it:

> mkdir ~/.kube
> sudo cp /etc/kubernetes/admin.conf .
> sudo chown user:user admin.conf
> mv admin.conf .kube/config

You can find this file in the home directory of the `user` user on the controller. For the sake of simplicity, you are going to use the administrator credentials.

You can verify that `kubectl` has been configured correctly by running the command `kubectl get nodes`: if the output shows four machines (one controller and three workers) in the `Ready` state, everything is OK. Otherwise, call one of the teachers. Note that it is normal for the `ROLE` to be `<none>` for the three worker nodes.

## 2 Pod to Pod traffic

For the sake of simplicity, the teaching staff has prepared a few yaml files containing the definitions of all the resources (pods, deployments, and services) needed for this lab. They are all available at `www.cloud.rennes.enst-bretagne.fr/k8s-network-lab/file_name`

Download the following files (for example by using `wget`) on your local machine:

-   `k8s-network-lab-s1.yml`
-   `k8s-network-lab-c1.yml`

### 2.1 Two Pods on the Same Node

Your first task is to analyze the traffic between two pods when these are on the same node. 

Start a webserver and a `busybox` container using the following commands (make sure that you are in the same directory where you have download the yaml files!).

kubectl apply -f k8s-network-lab-s1.yml
kubectl apply -f k8s-network-lab-c1.yml

Make sure that everything is working as expected by asking kubectl to show all the pods, including their IP address, and on which node they are running (`kubectl get pods -o wide`).

You should have three pods, one called `client-1`, another `client-2` and a third one, whose name starts with `webserver-1`. `client-1` and `webserver-1` should be on the same node. Call the instructor if this is not the case.

Open a shell first on `client-1`, then on `client-2`, and then `webserver-1` with the command below. On each container, use the commands `ip a` to obtain the IP and MAC address and `ip route` to obtain the routing table. Write down the IP and MAC address of each pod, as well as the number at the end of the name of the interface name that starts with `eth0` (e.g., `eth0@if21`). Write down the IP address of the default gateway for each pod. Are the IP addresses the same as in the output of `kubectl get pods -o wide`?

Start drawing a picture showing the pods, their IP addresses and how they are connected to the physical interface of the worker node.

Use `ctrl-d` (that is the control key plus the `d` key) to disconnect once you are done.

kubectl exec -it pod_name -- sh

The number at the end of the `eth0` interface name (21 in the example above) corresponds to the interface number on the physical machine connected to that pod. You can start tcpdump on that interface if you want to capture the packets to/from that pod.

Execute the command `ip a` on the machine running the `client-1` and `webserver-1` pods. Note how each interface has a number (it is at the very beginning of the first line describing each interface and it is followed by a colon). For example, the output of `ip a` on the machine hosting `client-1` in the example above contains the following:

21: vethaffb9729@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master kube-bridge state UP group default 
    link/ether 3e:cb:16:5a:1d:6d brd ff:ff:ff:ff:ff:ff link-netnsid 3
    inet6 fe80::3ccb:16ff:fe5a:1d6d/64 scope link 
       valid_lft forever preferred_lft forever

Note that the name of the interface is only `vethaffb9729` (**without the `@if3` part**). Note as well that 3 is the number of the corresponding interface inside the container (feel free to run again the command `ip a` in the container to verify this).

Start two `tcpdump` instances on the node hosting `client-1` and `webserver-1`. Note that you must open two terminals to that node, so that you can run the two `tcpdump` in parallel. Use the command `sudo tcpdump -i vethaffb9729 -w file_name.pcap`.  **Replace `vethaffb9729` with the name of your interface** and `file_name` with an **appropriate file name, which must be different for the two `tcpdump` commands**. Note that it is customary to use the `.pcap` extension for packet capture files. 

Connect to the `client-1` by executing the following command: `kubectl exec -it client-1 sh` and then ping the `webserver-1` ip address with the command `ping -c 5 ip_address`. (This command will send five ICMP echo request messages and then stop.)

Then use the command `curl ip_address:8000` to download the default page from the webserver.

Stop `tcpdump` with `ctrl-c` and then copy the capture files to your local machine with `scp` so that you can analyze them with Wireshark.

Answer the following questions in a text file called `k8s-net-question-1.txt`.

Put the names of the two students in your group on the first two lines of the file.

1.  Are there any ARP exchanges?
2.  What are the IP and MAC addresses of the IP packets containing the ICMP echo requests and echo reply?
3.  Are the IP and MAC addresses of the IP packets carrying the TCP connection started by `curl` the same as in the question above?
4.  Compare the TTL values of the IP packer in the different traces. Explain what you observe.
5.  In your opinion, did any of the packets in your trace files leave the machine hosting `client-1`? Why?

#### 2.1.1 Clearing the ARP Cache of a Container

As you know, all Operating Systems cache ARP addresses discovered through ARP requests and replies. How often these entries are refreshed depends on the specific OS, the version, and any specific configuration. Because of the ARP cache, you might not find an ARP exchange where you expected it.

For some reason, it is not possible to clear the ARP cache from a shell running inside the container. (Most likely because Docker created the container without the appropriate "capability.") But it is still possible to clear the ARP cache of a container by using `nsenter` on the node hosting the container.

Note that all the commands in this section must be executed on the node running the container. First of all, you must find the identifier of the network namespace of the container. The easiest option is to use the command `sudo lsns -t net`, the problem is that it is not always easy to guess which namespace corresponds to a given container. In the case of the `client` containers it is very easy because they run `sleep` as the command. Note that you need to remember (or write down) the PID (fourth column), and not the NS identifier (the first column).

If you have multiple containers running the same command, or if you do not know the command running inside a container, you can first use `sudo docker ps` to find the container ID (first column in the output). Then you can use the command `sudo docker inspect --format '{{ .State.Pid }}' container-id-or-name` to obtain the PID.

Once you know the PID, you can finally clear the ARP cache with the command `sudo nsenter -t your_PID -n ip neigh flush all`.

### 2.2 Two Pods on the Different Nodes

Your goal is now to understand what happens when the two pods are on different nodes. Recall that `client-2` must be on a different node than the `webserver-1` pod.

Using the commands described in the previous section:

-   Gather the information about `client-2` (IP and MAC address, default gateway).
-   Find the `veth` interface connected `client-2`.
-   Start `tcpdump` on the `veth` interface of each node.
-   Start a third `tcpdump` on the `enp1s0f2` interface of the machine hosting `webserver-1`.
-   Ping `webserver-1` from `client-2`.
-   Use `curl` to download the default page from `client-2`.
-   Copy the capture files to your computer and open then with Wireshark.

Answer the following questions in a text file called `k8s-net-question-2.txt`.

Put the names of the two students in your group on the first two lines of the file.

1.  Are there any ARP exchanges?
2.  What are the IP and MAC addresses of the IP packets containing the ICMP echo requests and echo reply?
3.  Compare the MAC addresses used in this case to those used in the previous case (pods on the same node). What is the main difference?
4.  Compare the TTL values of the IP packer in the different traces. Explain what you observe.
5.  In your opinion, did any of the frames (packets) in your trace files leave the machine hosting `client-2`? Why? (Hint: this is a trick question, the answer is not as simple as you may think at first. Make sure to take into account **all the headers (including layer two)** of all the packets that you have captured.)

### 2.3 Pod to Service

Recall that pods are ephemeral and can die, for example if the node hosting them fails. As long as a pod is part of a deployment, Kubernetes will automatically create a new instance of the failed pod. But the new instance will usually have a new IP address. For instance, in the case of the node failure, Kuberentes has no choice but start it on a different node and hence its IP address must belong to the IP prefix assigned to the new node.

Recall as well that, one of the key features of Kuberentes is that one can easily scale horizontally an application. For example, if the webserver in our example must support a large number of concurrent connections, one would ask Kubernetes to make sure that two (or more) instances of that pod are always available. This is what deployments are for. If you look at the `k8s-network-lab-s1.yml` you will see that the webserver does belong to a deployment, albeit with `replicas` set to one for the sake of simplicity.

If you have more than one instance of a pod, each one has its own IP address. If a client wants to connect to a pod, it must choose to which instance. This is obviously not a viable solution. This is one of the reasons for which Kubernetes offers the "service" abstraction. You might have noticed that the `k8s-network-lab-s1.yml` does define a service as well. You can confirm this with the `kubectl get svc` command that will also give you the ClusterIP of the service. This is a "virtual" ip address that is reachable from any pod and from any node. Recent Kubernetes versions, including the one installed on your bench, use the ipvs (IP Virtual Server) feature of the Linux kernel. You can confirm this by executing the command `sudo ipvsadm -Ln` on any node. You should see one entry corresponding to the ClusterIP of the webserver service. This entry should also contain the IP address of the `webserver-1` pod. When the Linux kernel receives a packet whose IP address is the ClusterIP of a service, it operates as a static NAT by replacing the destination IP address (i.e., the ClusterIP) with one of the IP addresses of the pods implementing the service. Thanks to its connection tracking capabilities, the kernel can also replace the source address of the packets flowing in the opposite direction (e.g., from the `webserver-1` to the client).

Furthermore, Kuberentes can offer a DNS service to resolve service names to the corresponding ClusterIP. Your Kubernetes cluster does have this capability. As you might have noticed when you ran the `sudo ipvsadm -Ln` command, there is a ClusterIP hosting a service on port 53, which is the DNS well known port. You can confirm this by running `kubectl get svc --all-namespaces` to obtain the list off all the services, including those running on namespaces other than the default one.

Your goal is now to understand what happens when a pod communicates with a service, using its name.

Using the commands described in the previous section:

-   Start `tcpdump` on the `veth` interfaces connected to `webserver-1` and `client-2`
-   Start a third `tcpdump` on the `enp1s0f2` interface of the machine hosting `webserver-1`.
-   Ping `webserver-service-1` from `client-2`.
-   Use `curl` to download the default page from `client-2`. Use the name of the service and not the ClusterIP.
-   Copy the capture files to the controller and open then with Wireshark.

Answer the following questions in a file called `k8s-net-question-3.txt`:

Put the names of the two students in your group on the first two lines of the file. 

1.  Are there any ARP exchanges?
2.  What are the IP and MAC addresses of the IP packets containing the ICMP echo requests and echo reply?
3.  Compare the IP addresses of the packets sent from `client-2` to the addresses in the corresponding packet received by`websever-1`. What do you observe?
4.  Compare the TTL values of the IP packer in the different traces. Explain what you observe.

Take a picture of the network diagram and save it in a file called `k8s-net-diagram.jpg` (change the extension of the file if you use another format). Upload this file and the text files with your answers to the questions on Moodle. .

## 3 More Network Interfaces (Optional)

You might have noticed that the output of `ip a` contains more interfaces than those that you have analyzed so far. There are, of course, the five physical network interfaces (`enp1s0f0, enp1s0f1, enp1s0f2, enp1s0f3, eno1`) and the loopback interface. But there is also a `docker0` bridge, a `nodelocaldns` dummy interface and, at least on some nodes, additional `veth` interfaces. 

Docker, by default, creates the `docker0` bridge to connect containers to the external world. But, given that Kubernetes uses its own networking solution, this bridge is not used. Those of you who have used Docker in the past might recognize the `172.17.0.1/16`prefix that Docker uses for its containers.

### 3.1 Nodelocaldns Dummy Interface

You can think of this interface as a trick to have a "floating" interface that is not connected to anything else. In other words, by itself, it is completely useless. But the nodelocaldns pod, which runs on each node, binds to this interface, whose IP address is 169.254.25.10. This a "link local" address, therefore it can be the same on each node.

As the name suggests, the nodelocaldns pod implements a DNS cache. When a pod wants to resolve a name, it sends the request to 169.254.25.10. The nodelocaldns receives this requests and, if it knows the answer, it replies immediately, otherwise it forwards the request to the clusterIP of the coredns service.

You can verify all this as follows: open a shell on one of the client pods (or on the webserver pod) and type `cat /etc/resolv.conf`you will see that the first line is `nameserver 169.254.25.10` meaning that the DNS server of each pod is `169.254.25.10`, i.e., the nodelocaldns pod. You can see the configuration of this caching DNS server with the command `kubectl --namespace kube-system describe configmap nodelocaldns`. You should see that it binds to `169.254.25.10` and that it forward requests to an IP address in the clusterIP range. If you run the command `kubectl --namespace kube-system get svc` (to get all the services in the `kube=system` namespace) you should find the same clusterIP assigned to the `coredns` service.

Finally, you can verify that there is a `nodelocaldns` pod on each node of your cluster with the command `kubectl get pods --all-namespaces -o wide`.

In case you are wondering, when the kubelet running on each node asks Docker to start a container, it specifies the address of the DNS server that Docker will put in  `/etc/resolv.conf`. You can check this by looking at `/etc/kubernetes/kubelet-config.yaml` that contains the value `169.254.25.10` under `clusterDNS`. This is needed because, by default, Docker uses the same `/etc/resolv.conf` of the host inside each container.

### 3.2 Additional veth Interfaces

You already know that pods are connected to the `kube-bridge` by `veth` pairs but you might be wondering why there are so many pods in the output of `kubectl get pods --all-namespaces` and not as many `veth` pairs. The reason is that most of the pods in the `kube-system` are connected directly to the network interface of each physical node. You can verify this by looking at the description of some of these pods: `kubectl --namespace kube-system describe pod <pod-name>`. Towards the beginning of the output, you have the IP address of the pod and it should be the IP address of the `enp1s0f2` interface of the node on which the pod is running. Note that there can be exceptions to this (see below). These pods use the root network namespace, in other words, they can bind directly to any of the interfaces in that namespace. This implies that they must ensure that they are using a port number that is not used by any other pod. This constraint is not a problem, as they are all part of Kubernetes itself.

The careful reader is probably wondering how the `nodelocaldns`, described in the previous section, can forward DNS queries to the DNS server, given that we only discussed how it binds to an interface that is not connected to anything! The answer is that the `nodelocaldns` pod is in the root network namespace. You can verify this by asking `kubectl` to describe one of the `nodelocaldns`pods (`kubectl --namespace kube-system describe pod <nodelocaldns-pod>`). You will see that the IP address of the pod is the IP address of the `enp1s0f2` interface of the node hosting the pod. You can therefore think of the `nodelocaldns` pod as an application layer gateway ("router") that receives DNS requests on the dummy interface `nodelocaldns` and, if it does not know the answer, forwards them to the DNS service through the root network namespace and, eventually, the `enp1s0f2` interface (after the requests have gone through the virtual IP translation step associated with the corresponding cluster IP).

Some of the pods in the `kube-system` namespace, for example those with `dashboard` in their name, use their own network namespace and, therefore, must be associated to a `veth` pair.

If you see a `veth` pair on one of your nodes and you are wondering to which pod it corresponds, you can do as follows: first get the list of network namespaces on the node with `sudl lsns -t net`. You should have one or more namespaces for which the command is `/pause`. These are the namespaces corresponding to the pods on that node. (Note that there is always a root namespace associated with the command `/sbin/init`.) The output of `lsns` contains the process ID (PID), which you can use to execute a command inside that namespace with `sudo nsenter -t <pid> -n ip a`. This way you know the IP address of the pod, which you can then look for in the list of all the pods running on that node (`kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=<node-name>`). 

### 3.3 The "pause" containers

As we are using Docker as the container runtime, you can use `sudo docker ps` to get the list of all the containers running a node. (You might want to use `docker ps --format 'table {{.Image}}\t{{.Command}}\t{{.Names}}'` to have a more readable output.) If you do that, you might be surprised by the number of containers, which is always higher than the number of pods. This is because each pod contains at least two containers: the container with the "application" that was started by the user, as well as the "pause" container, whose main role is to hold the network namespace. This is why `/pause` is the command associated with the network namespace in the output of `sudo lsns -t net`. But this is not the only role of the pause container, which is reminiscent of the `init`process in Unix system. If you are curious, you can find more details on this [page](https://www.ianlewis.org/en/almighty-pause-container).

Author: Alberto Blanc, Christophe Couturier, Gwendal Simon, Jean-Pierre Le Narzul

Created: 2022-11-29 Tue 12:35

[Validate](https://validator.w3.org/check?uri=referer)