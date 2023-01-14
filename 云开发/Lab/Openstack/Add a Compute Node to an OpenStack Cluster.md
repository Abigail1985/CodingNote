
## 1 Introduction

OpenStack is an IaaS (Infrastructure as a Service) cloud computing platform to, among others, create virtual machines, allocate block storage resources, and create virtual networks. 

The goal of this lab is to get a first experience of configuring a data-center with Open Stack. You will have the opportunity to discover the key components of Open Stack. This lab is based on the official Installation Guide of the Ussuri version, which can be found at:https://docs.openstack.org/ussuri/install/. Your group is associated with a label number x. You are in the room labeled either 27 or 30. You will manage a “data-center” of four physical machines. Your mission is to configure this data-center with OpenStack so that three machines (called Compute Nodes) can be available to host Virtual Machines (VMs). Three physical machines (one Controller Node and two Compute Nodes) are already configured. In this lab, you will configure the fourth machine as a third compute node.

## 2 Platform

Below is a pic of the platform (also called "banc") that you will use during this lab (and the forthcoming labs of the UE). 

![platform.jpg](http://www.cloud.rennes.enst-bretagne.fr/platform.jpg)

The platform is made of:

- 4 Dell PCs numbered PC-1, PC-2, PC-3 and PC-4. Each PC has four Ethernet ports. Ubuntu Server 18.04 is installed on each PC. The `root` password is `pass4root`. The `user` user is a `sudo` user and its password is `pass4user`.

- 1 KVM switch. This device is at the top of the banc. This switch allows you to share the screen, keyboard, and mouse from the position at the right of the platform between PC-2, PC-3 and PC-4. PC-1 is directly connected to the screen, keyboard, and mouse at the left of the platform.

- 1 TP-Link switch, located below the KVM switch. This lab will use this switch intensively. For the moment, do not pay any attention to the cables used to connect the ports of the TP-Link switch to the ports of the patch panel.

- 1 patch panel, just below the TP-link switch. The patch panel enables the transfer of the network interfaces that are located at the back of the PCs towards the front of the platform. Each PC has four Ethernet interfaces, numbered 0, 1, 2, and 3. The mapping between the interfaces of the PCs and the ports of the patch panel is as follows:

  - Interface 0 (respectively 1, 2 and 3) of PC-1 is connected to the port 1 (respectively 2, 3 and 4) of the patch panel.
  - Interface 0 (respectively 1, 2 and 3) of PC-2 is connected to the port 7 (respectively 8, 9 and 10) of the patch panel.
  - Interface 0 (respectively 1, 2 and 3) of PC-3 is connected to the port 13 (respectively 14, 15 and 16) of the patch panel.
  - Interface 0 (respectively 1, 2 and 3) of PC-4 is connected to the port 19 (respectively 20, 21 and 22) of the patch panel.

  Of course, you cannot check this mapping from your position at the front of the platform ! You have to trust us or you may look behind you the back of another platform if there is one behind your position in the room.

- 1 Cisco Catalyst router, below the TP-Link switch. This device will not be used during this lab.

- 1 Cisco Catalyst switch placed vertically at the left of PC-1. This device will not be used during this lab.

Carefully identify all these devices on your platform. Please, do not disconnect any cable!

Below is a crop of the plaform with focus on the patch panel. You can observe that the four interfaces of PC-1, PC-2, and PC-3 are used and connected to the switch. Additionally, port 5 of the patch panel is also used. This port does not map to an Ethernet interface of any PC. It is used to connect your platform to a network, which interconnects all the platforms of the room and provides also Internet access.

![platform-patch-panel.jpg](http://www.cloud.rennes.enst-bretagne.fr/platform-patch-panel.jpg)

## 3 OpenStack Network

The networking architecture of your OpenStack platform is based on three networks:

- A management network on `10.0.0.0/24`. It connects the physical machines and, as its name implies, is used for management (i.e., OpenStack) traffic only. Each service provides an "API endpoint", which allows other services to communicate and interact. No Internet access is provided on this network. Interface numbered 0 is used for connecting to this network on each PC. The name of the interface is `enp1s0f0`.

- A provider network on

   

  ```
  10.30.0.0/16
  ```

   

  with gateway

   

  ```
  10.30.0.254
  ```

   

  in room B30, on

   

  ```
  10.27.0.0/16
  ```

   

  with gateway

   

  ```
  10.27.0.254
  ```

   

  in room B27. Internet access is provided on this network. This access is used 1) for administrative purposes such as packages installation, DNS resolution, and NTP time synchronization and 2) to provide Internet access to the virtual machines that will be created on your OpenStack infrastructure. On each PC, two interfaces are connected to this provider network: interface numbered 1, name

   

  ```
  enp1s0f1
  ```

   

  and interface numbered 2, name

   

  ```
  enp1s0f2
  ```

  .

  - `enp1s0f1` has no address allocated on this network. This interface will be used to allocate floating IP addresses to the virtual machines.
  - `enp1s0f2` has an address given by a DHCP server. The numbering plan allocates the address `10.{room}.{platform}.{pc * 10 + itf}` to every interface connected to this network. If we consider room B30, platform 5, interface 2 (`enp1s0f2`) of PC-1, the allocated address is `10.30.5.12`.

- A overlay network on `10.0.1.0/24`. It connects the physical machines and is used for communication between VMs. Interface numbered 3 is used for connecting to this network on each PC. The name of the interface is `enp1s0f3`.

## 4 The TP-Link Switch

As you may have observed, the interfaces of the PCs are connected to the TP-Link switch (except for PC-4 as it will be your responsibility to add this PC to the OpenStack infrastructure). 

- The interfaces numbered 0 (`enp1s0f0`) of PC-1, PC-2 and PC-3 are connected to the ports numbered 1, 3 and 5 of the TP-Link switch.
- The interfaces numbered 1 (`enp1s0f1`) and 2 (`enp1s0f2`) of PC-1, PC-2 and PC-3 are connected to the ports numbered 9, 11, 13, 15, 17 and 19 of the TP-Link switch.
- The interfaces numbered 3 (`enp1s0f3`) of PC-1, PC-2 and PC-3 are connected to the ports numbered 2, 4 and 6 of the TP-Link switch.

You will administer the switch from PC-1. Ideally, you would use a free network interface on PC-1 for this purpose but unfortunately we are short of network interfaces (all are already used). To fix this problem, you will temporarily:

- disconnect the cable from the port numbered 1 of the TP-Link switch
- reconnect it on the port numbered 16 on the TP-Link switch (interface `enp1s0f0` of PC-1 will be connected to port 16). We need to do that because, port 1 is associated to a VLAN that does not allow to access the switch's management interface.
- assign (temporarily) an address to the `enp1s0f0` interface on network `192.168.0.0/24`. The IP address of the switch on this network is `192.168.0.1`.

Disconnect/reconnect the cable according the instructions aforementioned, login to the controller node as the `user` user (password is `pass4user`) and assign an address on `192.168.0.0/24` to `enp1s0f0` as indicated below.

```
> sudo ip addr add 192.168.0.2/24 dev enp1s0f0
```

Launch the Firefox browser.

Type the following address in the address bar: [http://192.168.0.1](http://192.168.0.1/). The interface shown in the following figure should appear in your browser. Log in with `admin/admin` (for `Username/Password`) to access the management interface of the TP-Link switch.

![switch-login.png](http://www.cloud.rennes.enst-bretagne.fr/switch-login.png)

Select the `L2 FEATURES` tab (top of the GUI) and then, the `VLAN/802.1Q VLAN` tab (left of the GUI). The following interface should appear.

![switch-overall-vlan.png](http://www.cloud.rennes.enst-bretagne.fr/switch-overall-vlan.png)

There are four VLANs:

- `System-VLAN` with ports 7, 8, 12, 14, 16, 18 and from 20 to 28. This VLAN is the default VLAN assigned to unused ports.
- `management` with ports 1, 3 and 5 corresponding to the management network.
- `provider` with ports 9, 10, 11, 13, 15, 17 and 19 corresponding to the provider network.
- `overlay` with ports 2, 4, 6 corresponding to the overlay network.

Explain why port 10 of the switch is part of the `provider` VLAN.

## 5 Network Integration of PC-4

### 5.1 Wiring PC-4

The physical connection between PC-4 and the TP-Link switch is the very first step you have to accomplish. As for the other PCs, the first interface (numbered 0) must be used for connecting to the management network and the second (numbered 1) and third (numbered 2) must be used for connecting to the provider network. The fourth interface (numbered 3) must be used for connecting to the overlay network.

You have to connect four network cables from the four Ethernet interfaces of PC-4 to the appropriate ports on the TP-Link switch. Please, for the sake of simplicity, respect the convention used for the others PCs to choose the ports on the TP-Link switch and for the colors of the cables (white cables for connecting to the provider network and green for connecting to the management and overlay networks). Remember that you **do not have** to access the interfaces of PC-4 at the rear of the platform: use the patch panel!

### 5.2 Configure the TP-Link Switch to add PC-4

You have connected the cables from PC-4 to the TP-Link switch device and you know how to reach the configuration interface of the switch from a browser running on PC-1. You are now ready to configure the switch to add the ports connected to PC-4 to the appropriate VLANs.

Click on the editing icon next to the line corresponding to the management VLAN to display the `VLAN Config` panel (see figure below). Add the port numbered 7 to the `management` VLAN. **Repeat this operation** for the provider VLAN and for the overlay VLAN (choose the appropriate ports!). You **must also remove** the ports added in the management, provider, and overlay VLANs from the System-VLAN.

![switch-vlan-management.png](http://www.cloud.rennes.enst-bretagne.fr/switch-vlan-management.png)

The last action to perform is to configure the ports. You must select the `Port Config` panel on the main window. The figure below shows the interface displayed after the selection of the `Port Config` panel.

![switch-port-config.png](http://www.cloud.rennes.enst-bretagne.fr/switch-port-config.png)

The `PVID` displayed next to each port number must correspond to the VLAN, whose port is associated to. Given that ports numbered 1, 3, 5 are associated to the `management` VLAN and that the `PVID` of this VLAN is 2, the value 2 has been set in the `PVID` column next to these ports.

If you have done your job properly, the port numbered 7 has been associated to the `management` VLAN. To set the `PVID` value next to port 7, select the line and modify the value in the input field (see figure below). Next, click on the Apply button. **Repeat this operation**for the other ports connected to the interfaces of PC-4.

![switch-add-port-7-in-pvid-2.png](http://www.cloud.rennes.enst-bretagne.fr/switch-add-port-7-in-pvid-2.png)

**Call the assistant to check your configuration of the switch.** Wait for his approval and then, save the configuration of the TP-Link switch, disconnect the cable from port 16 and reconnect it to the port numbered 1 of the TP-Link switch. 

### 5.3 Configure the Interfaces of PC-4

You must now configure the four network interfaces of PC-4. You will use **Netplan**, an utility for easily configuring networking on a linux system. You create a YAML description of the required network interfaces and what each should be configured to do. This yaml file, named `01-netcfg.yaml` must be placed in the directory `/etc/netplan`.

Look at what has been done for the other PCs and complete the definition of this file on PC-4.

### 5.4 Post-Installation of Network

There are still some actions to perform to connect your machine in the data-center environment (we still do not talk of the Open Stack environment, as you will see later). First, you have to give a name to your machine. We strongly suggest `compute3` (and this is the name we will use hereafter).

To set the name of your host, please edit `/etc/hostname` file accordingly on `compute3`.

Then, you have to make the `compute3` machine aware of the hostnames of the other machines in the data-center. You have to edit the`/etc/hosts` file in `compute3` and add the hostnames for `controller`, `compute1` and `compute2`. Similarly, you have to advertise the `compute3` hostname to all other machines of the data-center. In this scenario, since we do not implement any automatic mechanism to broadly advertise the name, you have to manually "inform" all the other machines.

Edit the `/etc/hosts` file on the four nodes: `controller`, `compute1`, `compute2` and `compute3`. **Reboot** `PC-4`.

Finally, you have to make sure all machines are well synchronized. We will use Chrony, an implementation of NTP, to properly synchronize services among nodes. The controller node is set such that it is the accurate reference (lower stratum) server and all the other nodes use the controller node for reference. For `compute3`, it requires some configuration. 

First, install Chrony on `compute3`.

```
> apt install chrony
```

Then, edit the `/etc/chrony/chrony.conf` file and comment out or remove all but one server key. Change it to reference the controller node.

```
server controller iburst
```

Comment out the following lines.

```
pool ntp.ubuntu.com        iburst maxsources 4
pool 0.ubuntu.pool.ntp.org iburst maxsources 1
pool 1.ubuntu.pool.ntp.org iburst maxsources 1
pool 2.ubuntu.pool.ntp.org iburst maxsources 2
```

Restart the NTP service.

```
> service chrony restart
```

## 6 Configure PC-4 to be a Compute Node

### 6.1 Get the Right Repository

We will use the `apt` tool to get the `ussuri` release of OpenStack. We thus have to first configure the right OpenStack repository to make sure that the machine will fetch the right service.

Please, run `sudo -i` to open an interactive login shell as the `root` user and type the following commands:

```
> apt update
> apt upgrade
> apt install software-properties-common
> add-apt-repository cloud-archive:ussuri
> apt update
> apt dist-upgrade
> apt install python3-openstackclient
```

### 6.2 Install the Nova Service

Now, it is time for some basic information about Nova. Nova (or OpenStack Compute) is a key element of an Infrastructure-as-a-Service (IaaS) system. The main modules are implemented in Python. Nova interacts with Keystone for authentication, Glance for images, and Horizon for the user and administrative interface. Nova can scale horizontally on standard hardware, and download images to launch VM instances. Nova consists of several areas and components, including:

- `nova-api service`: It accepts and responds to end user compute API calls. The service supports the OpenStack Compute API, the Amazon EC2 API, and a special Admin API for privileged users to perform administrative actions. It enforces some policies and initiates most orchestration activities, such as running an instance.
- `nova-compute service`: A worker daemon that creates and terminates virtual machine instances through hypervisor APIs. For example XenAPI for XenServer/XCP, libvirt for KVM or QEMU, and VMwareAPI for VMware. Basically, the daemon accepts actions from the queue (RabbitMQ) and performs a series of system commands such as launching a KVM instance and updating its state in the database.
- `nova-scheduler service`: It takes a virtual machine instance request from the queue and determines on which compute server host it runs. For more information about how scheduling works in nova, see: https://docs.openstack.org/nova/ussuri/reference/scheduling.html
- `nova-conductor`: This service provides database access to compute nodes.

The `controller` node contains API endpoints for most of these services. Since two nodes already host Nova (here `compute1` and `compute2`), it means that these Nova services have been installed and configured. In particular, you can check the currently running Nova services in your data-center by typing the following command on your `controller` node:

```
> source admin-openrc
> openstack compute service list
```

On the `compute3` node, you must first install the right nova service for the Ussuri release of OpenStack (keep using the shell as the `root` user).

```
> apt install nova-compute
```

#### 6.2.1 Configure Nova

Edit the `/etc/nova/nova.conf` file and complete the actions below.

- First, you have to configure the access to the RabbitMQ message queue. In Open Stack (as in many other scalable distributed services), the idea is that all command actions are sent to a message queue, and then these actions are consumed. In our case, the RabbitMQ message service is hosted on the `controller`. We must provide the information to have access to the queue message. The password used on the `controller` for the RabbitMQ service is `usr`. In the `[DEFAULT]` section, configure RabbitMQ message queue access:

  ```
  [DEFAULT]
  # ...
  transport_url = rabbit://openstack:RABBIT_PASS@controller
  ```

- Next, you have to configure the access to the `Keystone` (Identity) service. Again, this service runs on the `controller` node. You must thus provide all information so that the `nova-compute` on the `compute3` host can connect to the right endpoint and get the clearance authorization. In the `[api]` and `[keystone_authtoken]` sections, configure the access to `Keystone` (the password chosen for the `nova` user in the Identity service is `usr`):

  ```
  [api]
  # ...
  auth_strategy = keystone
  
  [keystone_authtoken]
  # ...
  www_authenticate_uri = http://controller:5000/
  auth_url = http://controller:5000/
  memcached_servers = controller:11211
  auth_type = password
  project_domain_name = Default
  user_domain_name = Default
  project_name = service
  username = nova
  password = NOVA_PASS
  ```

Four actions are required to configure the management network used by Nova.

- The first one is to set the IP address of the management network. Nova service requires the `controller` node to be able to manage the instances from the management network. In the `[DEFAULT]` section, configure the `my_ip` option.

  ```
  [DEFAULT]
  # ...
  my_ip = MANAGEMENT_INTERFACE_IP_ADDRESS
  ```

- In the `[neutron]` section, we have to configure access parameters ( replace `NEUTRON_PASS` with the password chosen for the `neutron` user in the Identity service: once again, this password is `usr`)

  ```
  [neutron]
  # ...
  auth_url = http://controller:5000
  auth_type = password
  project_domain_name = default
  user_domain_name = default
  region_name = RegionOne
  project_name = service
  username = neutron
  password = NEUTRON_PASS
  ```

- Finally, the last action is related to remote console access. The server component listens all IP addresses whereas the proxy component listens only the management interface IP address of the compute node. (for those who wants details, the base URL indicates the location where you can use a web browser to access remote consoles of instances on this compute node). In the `[vnc]`section, enable and configure remote console access.

  ```
  [vnc]
  # ...
  enabled = true
  server_listen = 0.0.0.0
  server_proxyclient_address = $my_ip
  novncproxy_base_url = http://controller:6080/vnc_auto.html
  ```

- In the `[glance]` section, you must configure the location of the Image service API. The `compute3` node must know how to find the images of the VM to start and stop. It requires connecting to the endpoints of the Glance service, which in our case runs on the `controller` node. In the `[glance]` section, configure the location of the Image service

  ```
  [glance]
  # ...
  api_servers = http://controller:9292
  ```

- You must configure the lock path for `oslo_concurrency`, a library library used for safely running multi-thread, multi-process applications using locking mechanisms. In the `[oslo_concurrency]` section, configure the lock path:

  ```
  [oslo_concurrency]
  # ...
  lock_path = /var/lib/nova/tmp
  ```

- The placement service tracks the inventory and usage of compute nodes. Compute nodes register to the placement service and report inventory and allocation information. In the `[placement]` section, configure the Placement API (the password used for the `placement` user in the Identity service is … `usr`).

  ```
  [placement]
  # ...
  region_name = RegionOne
  project_domain_name = Default
  project_name = service
  auth_type = password
  user_domain_name = Default
  auth_url = http://controller:5000/v3
  username = placement
  password = PLACEMENT_PASS
  ```

### 6.3 Restart and Verify Nova

Restart the compute service on `compute3`.

```
> service nova-compute restart
```

The last step (for the `compute` Nova service) is to add your new compute node to the cell database. 

**On the controller** node, run the following commands.

```
> source admin-openrc
> openstack compute service list --service nova-compute
> sudo -i
> su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova
```

Hopefully, everything should run just fine. More exactly, restarting the service must have generated a series of message exchanges between the various OpenStack services running on the `controller` (via the appropriate endpoints) and the `nova-compute` service running on the `compute3` host. It is relatively easy to check whether the configuration is right in Open Stack. Each service contains some commands that list everything this service knows about the environment. In the case of Nova, these commands are especially useful. 

**On the controller** node, source the admin credentials to enable admin-only CLI commands and run the following commands.

```
> openstack compute service list
> openstack endpoint list --service nova
> openstack image list
```

The first command provides the running Nova services in the data-center. If everything works properly, you should see a `nova-compute` service running on `compute3`. The second command provides all Identity settings for the services. The last command provides the list of images that are available for starting on one of the compute nodes.

### 6.4 Install the Neutron service

We need a right network configuration to enable the virtual machines running on the compute nodes to communicate with their peers and from/to the Internet with the service provider. We must thus install and configure the Neutron service. The main Neutron service is called `neutron-server`. But Neutron also features many additional plug-ins and agents, which allow in particular plugging and unplugging ports, creating networks, subnets, and providing IP addressing. These plug-ins and agents differ depending on the vendor and technologies used in the particular cloud. Neutron ships with plug-ins for Cisco virtual and physical switches, NEC OpenFlow products, Open vSwitch, Linux bridging, and the VMware NSX product. The common agents are L3 (layer 3), DHCP (dynamic host IP addressing), and a plug-in agent.

You will configure the `compute3` node with the following services:

- OpenvSwitch agent
- DHCP agent
- Metadata agent

#### 6.4.1 Install OpenvSwitch

OpenvSwitch is an open-source project that implements a virtual switch to interconnect virtual machines. To load kernel modules and install the OpenvSwitch daemon, please execute the following command (as root):

```
> apt install openvswitch-common openvswitch-switch
```

#### 6.4.2 Install Neutron Services

```
> apt install neutron-openvswitch-agent neutron-dhcp-agent neutron-metadata-agent
```

#### 6.4.3 Configure Neutron.conf

Edit the `/etc/neutron/neutron.conf` file and complete the actions below.

- In the `[database]` section, comment out any connection options because compute nodes do not directly access the database.

- In the `[DEFAULT]` section, configure RabbitMQ message queue access:

  ```
  [DEFAULT]
  # ...
  transport_url = rabbit://openstack:RABBIT_PASS@controller
  ```

- In the `[DEFAULT]` and `[keystone_authtoken]` sections, configure Identity service access:

  ```
  [DEFAULT]
  # ...
  auth_strategy = keystone
  core_plugin = ml2
  
  [keystone_authtoken]
  # ...
  www_authenticate_uri = http://controller:5000
  auth_url = http://controller:5000
  memcached_servers = controller:11211
  auth_type = password
  project_domain_name = default
  user_domain_name = default
  project_name = service
  username = neutron
  password = NEUTRON_PASS
  ```

- In the `[oslo_concurrency]` section, configure the lock path

  ```
  [oslo_concurrency]
  # ...
  lock_path = /var/lib/neutron/tmp
  ```

#### 6.4.4 Create the OVS provider bridge br-provider

```
ovs-vsctl add-br br-provider
```

#### 6.4.5 Add the provider network interface as a port on the OVS provider bridge br-provider

Be very careful during this step when typing the name of the provider interface and ask your partner to confirm your action. Ask also the lab supervisor to validate your action.

```
ovs-vsctl add-port br-provider PROVIDER_INTERFACE
```

#### 6.4.6 Configure the OpenVSwitch agent

Edit the `/etc/neutron/plugins/ml2/openvswitch_agent.ini` file and complete the actions below.

- In the `[ovs]` section, map the provider virtual network to the provider physical network interface and configure the IP address of the physical network interface that handles overlay networks:

  ```
  [ovs]
  bridge_mappings = provider:br-provider
  local_ip = OVERLAY_INTERFACE_IP_ADDRESS
  ```

- In the `[agent]` section, enable VXLAN overlay networks and enable layer-2 population:

  ```
  [agent]
  tunnel_types = vxlan
  l2_population = True
  ```

- In the `[securitygroup]` section, configure the iptables firewall driver:

  ```
  [securitygroup]
  firewall_driver = iptables_hybrid
  ```

#### 6.4.7 Configure the DHCP agent:

- In the `dhcp_agent.ini` file, configure the DHCP agent:

  ```
  [DEFAULT]
  interface_driver = openvswitch
  enable_isolated_metadata = True
  force_metadata = True
  ```

#### 6.4.8 Configure the metadata agent

- In the `metadata_agent.ini` file, configure the metadata agent:

  ```
  [DEFAULT]
  nova_metadata_host = controller
  metadata_proxy_shared_secret = METADATA_SECRET
  ```

  `METADATA_SECRET` has been configured on the `controller` node in the `neutron` section of `nova`. Its value is `usr`.

### 6.5 Restart and Verify Neutron

Restart the following services on `compute3`.

```
> service neutron-openvswitch-agent restart
> service neutron-dhcp-agent restart
> service neutron-metadata-agent restart
> service nova-compute restart
```

As for Nova, many command lines allow the administrator to check whether the Neutron service is properly configured. It has to be done on the `controller` node. 

**On the controller** node, source the admin credentials to enable admin-only CLI commands and run the following command.

```
> openstack network agent list  
```

You should see the following agent types running on `compute3`:

- `Open vSwitch agent`
- `DHCP agent`
- `Metadata agent`

If you can see these agents on `compute3`, congratulations, you did it!

## 7 Launch a VM on your new Compute Node

It is not so infrequent that, according to the `controller` , all services are well configured, however, when a command to start an VM is ordered, nothing works as it should. So, please do not cheer too much after the last `agent-list` command, and please run your first image first. This can be done from the `controller` node.

**On the controller** node, source the admin credentials. We will use this account to have the possibility to choose the compute node for our instance. This feature is not accessible to a normal user.

Before creating a virtual machine, it is necessary to create a pair of private/public keys. The public key will be stored on the VM running on OpenStack and the private key will be stored on the `controller` to allow you to establish a secure connection to the virtual machine from the `controller`.

Create a keypair using the following OpenStack command:

```
> openstack keypair create --private-key ~/mykey MYKEY
```

The OpenStack name of your keypair is `MYKEY`, the private key will be stored in a local file named `~/mykey` on the `controller`.

A security group is a set of IP filter rules that define which packets are allowed to pass and which are not. When instanciating a virtual machine, one has to choose the security group that will filter the traffic to this virtual machine. Here, you will use the default security group, called `default` and you will add two rules to authorize SSH and ICMP (ping) traffic to the instance. 

Create the rules using the following OpenStack commands:

```
> openstack security group rule create --ingress --protocol icmp default
> openstack security group rule create --ingress --protocol tcp --dst-port 22 default
```

You have now completed all the preparatory steps. Now, you will be able to do something more rewarding: instanciate a virtual machine and make it run on your new compute node.

Create a virtual machine using the following OpenStack command. Of course, you will replace the `id_of_your_external_network` by the real `id` of your `external` network.

```
> openstack server create --flavor m1.small --image ubuntu18 --availability-zone nova:compute3:compute3  --nic net-id=id_of_your_external_network --key-name MYKEY SERVER
```

The use of the `--availability-zone` allows to select the host where an instance is launched. `nova` is the name of the `zone` (a logical partition of compute and storage services). The first `compute3` is the name of the `host`; the second `compute3` is the hypervisor hostname. Now, you can cheer! … Hold on … Are your sure that your VM can actually communicate with the real Internet? … Does Neutron work properly? 

Try to establish an SSH connection to your Virtual Machine from the `controller` node and, when connected to the VM, ping any server.

## 8 Conclusion

At this point, you should have three `compute` nodes in your data-center. During this lab, you have come to manipulate Nova and Neutron, and to interact with many other Open Stack services. Of course, in a large-scale data-center, these configuration tasks should be automated. You also had to actually connect the machine to the data-center, which is a frequent cause of troubles in data-centers.

Author: Alberto Blanc, Christophe Couturier, Gwendal Simon, Jean-Pierre Le Narzul

Created: 2022-10-10 Mon 21:06

[Validate](https://validator.w3.org/check?uri=referer)