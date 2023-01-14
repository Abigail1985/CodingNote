
## 1 Introduction

You are in charge of the implementation of a new exciting project, namely **DaCirco**, which is a transcoding-on-demand (ToD) online service: it receives a request for transcoding a given video, and it is expected to output the same video, which has been re-encoded with new parameters. *Transcoding* is typically used by a video provider (hereafter named **client**) to deliver the movie to a given end-user whose connection to Internet is limited and/or whose device has limited displaying capabilities.

DaCirco is expected to be a *cloud service*. Your company has invested into a brand new data-center, with no less than 3 Compute nodes and 1 Controller node. Each Compute node features 6 CPU cores, 600 GBytes hard disk storage, and 16 GBytes RAM. A team of great professional devops has installed an up-to-date version of OpenStack for the management of this technology marvel, which can be used to process and store data.

You are in the middle of a fight in the company. On the one hand, the data-center director is concerned all the resources of the data-center could be jeopardized by DaCirco. Video transcoding can indeed be a seriously resource-hungry operation. You have to be **lean**. On the other hand, DaCirco's manager would like the *transcoding jobs* to be processed as fast as possible. Indeed, the video content providers are interested by getting the newly encoded videos to be ready for streaming before the quick-tempered end-users quit the service. You have to be **fast**.

Your mission is to satisfy both bosses by implementing a **Job Scheduling and Dispatching** service for DaCirco.

## 2 Your mission

Note: For a better understanding of the following text, you can refer to the [Overview Page of the Project's documentation](http://www.cloud.rennes.enst-bretagne.fr/da_circo_doc/system-architecture.html).

### 2.1 What you already have

Somebody has designed a REST API to receive the job requests from the client. It is the **DaCirco Job API**. A *job request* contains the filename of the movie to be transcoded, the target bit-rate of the transcoded video, and the expected speed of the encoding, which can be either *ultrafast* or *fast*. You don not have to worry about this API any longer, you just have to run `dacirco_rest_api` to have the DaCirco Job API available at `http://127.0.0.1:8000/` and its documentation at `http://127.0.0.1:8000/docs`. As usual, typing `--help` offers a description of the arguments that you may like to add when running the program `dacirco_rest_api`.

Somebody has identified patterns in the way a typical client generate job requests. This good friend has developed a program called`dacirco_scenario`, which generates and sends job requests to your DaCirco job API. This is the **DaCirco Job Generator**, which is quite useful to test your scheduler. Of course, type `--help` for more info. The simplest usage is `dacirco_scenario -n N` where `N`is the number of requests you would like to send.

Somebody has stored a dozen of movies in the data-center. The **DaCirco Movie Catalog** is available through the MinIO object storage under the `minio` account (password: `pass4minio`). Each movie is called `bbb_X.mp4` where *X* ranges from 0 to 9. The popularity of these movies is not uniform: the larger is *X*, the more popular is the movie.

Somebody has prepared a **DaCirco OS Image**, which is called *dacirco*. This OS image includes all what is necessary to transcode a video. When started, the image automatically connects to the controller to register and wait work incoming transcode jobs. The DaCirco OS image can execute on various flavors. In your Openstack configuration, you have the choice between three *flavors*:

- `s10.tiny` with 1 VCPU
- `s10.small` with 2 VCPUs
- `s10.medium` with 4 VCPUs

### 2.2 What you have to do

Implement the **DaCirco Job Scheduler**, which is essentially the program `scheduler.py`. Fortunately, another friend has already implemented a naive version of the program that can serve as a tutorial to understand the system.

1. Run the naive version. Identify strengths and weaknesses of this solution.
2. When we say strengths and weaknesses, we want real numbers based on a serious evaluation. Please provide evidences of these strengths and weaknesses by measuring at least (1) the amount of resources that are actually used by DaCirco in the data-center (average CPU and RAM utilization, 95th percentile); and (2) the delay for a transcoding job to be processed (average time, 95th percentile).
3. Implement one or several solutions that actually satisfy both bosses. Explain your intuition and your approach. Be smart.
4. Demonstrate in an internal competition. The best team wins, the others are sacked. Cruel and unfair world.

### 2.3 How to start

This section lists what you have to do to initialize and test your work environment. Please refer to other provided resources for more information.

Configure your environment:

- Get the source files and configure your Git environment as explained in the

   

  Git And GitLab section

   

  below

  - Configure Git
  - Clone the code provided by the teachers in your git repository

- Install required packages and Python modules on your development computer as described in the [Development Environment Setup section](http://www.cloud.rennes.enst-bretagne.fr/da_circo.html#org953ba6b).

- Add entries for `controller`, `compute1`, `compute2` and `compute3` in `/etc/hosts`.

- Configure your Openstack environment (using Horizon or

   

  ```
  openstack
  ```

   

  command line):

  - Download your RC (`admin` and `demo`) AND `clouds.yaml` (`admin` and `demo`) profile files from Horizon (http://controller/horizon): after you log in, click on Project/API access on the left and then on the `Download OpenStack RC File` on the right to download the `RC` and `clouds.yaml` files.
  - Copy the content of the `demo` `clouds.yaml` into `$HOME/.config/openstack/clouds.yaml`. Edit this file to add the line `password: "usr"` at the same level as the `username: "xxxx"` entry.
  - Add the content of the `admin` `clouds.yaml` file at the end of the `$HOME/.config/openstack/clouds.yaml`. Change the name of the cloud from `openstack` to `openstack_admin` (in the line above the `auth:` line
  - Add the password for the `admin` account in the line below the one containing `username: "admin"`.
  - Create an ssh key with the command `ssh-keygen -f ~/.ssh/os-vms` on your VDI VM.
  - Upload the public key to OpenStack with the command `openstack keypair create --public-key ~/.ssh/os-vms.pub mykey`
  - Add Security rules, called `ssh` to enable SSH and ICMP on all VMs (see "Add a compute node" lab).

- Test that Openstack is working by using the `openstack` command line to list available images list.

- Test that the provided scripts are working:

  - Open a console and run `dacirco_controller --minio-server <Your_MinIO_IP> ...`. Note that your MinIO entry point is the fourth node of your cluster (compute3). Note that `--minio-server` may not be the only configuration option you have to pass to the controller. Take a careful look at the output of `dacirco_controller --help` to identify these parameters. You may also have to configure additional stuff on Openstack (keypair, security group, etc).

  - Open a second console to run `dacirco_rest_api`.

  - And, in a third console run `dacirco_scenario` to run a simple scenario (e.g., with one transcoding request).

  - Instead of `dacirco_scenario`, you might use `curl` or the web test interface (at [http://127.0.0.1:8000](http://127.0.0.1:8000/)) of DaCirco API. (for instance: `curl -v -H "Content-Type: application/json" -d '{"id_video":"bbb_5.mp4", "bitrate": 7000, "speed": "ultrafast"}' -X POST 127.0.0.1:8000/jobs`)

  - You can also use

     

    ```
    curl
    ```

     

    to get information about current jobs status. For instance:

    - the `/jobs` URI returns the list of jobs;
    - `/jobs/<jobID>` returns the details of a given job;
    - `/jobs/<jobID>/state` returns the status of a given job;
    - the `/workers` URI returns the list of workers;
    - `/workers/<workerID>` returns the details of a given worker;
    - `/workers/<workerID>/state` returns the status of a given worker.

  - You can also use the `dacirco_rest_monitor` command to get a list of all the workers (and their status) and of all the jobs (and their status). This script keeps printing the lists every three seconds, until you stop it with `Ctrl-C`.

Note:

- All provided scripts are written in Python 3. Please use this version of Python (Python 2.x is deprecated)!
- Take the time to look at the command line parameters of the utility scripts (`dacirco_rest_api`, `dacirco_controller`, `dacirco_scenario`, etc) using the `--help` option.
- All Python developments should take place on your local machine (i.e. the VDI VM). You do not need to run any code on your Openstack cluster, except if you want to customize your Openstack cluster, for instance to add monitoring or supervision features. In this case, please ask the teachers before.
- You will probably need to develop additional software to analyze the performances of you implementation. Don't hesitate to install additional modules/software if needed.

### 2.4 Kubernetes

In order to use the DaCirco controller with Kubernetes you must:

- Copy the `~/.kube/config` file from the controller and put it in the same location (i.e., `~/.kube/config`) on your VDI VM.
- Edit this file on your VDI VM and replace `127.0.0.1` with the IP address of your controller (e.g., 10.30.1.12 for bench number 1 of room B130).
- Start the DaCirco controller with the `-r pod` option.

The rest is exactly the same. The `-r pod` option tells the controller to use the "pod manager" rather than the "VM manager" to start/stop the transcoding workers.

## 3 Resources and Tips

### 3.1 Documentation

- The documentation of provided Python modules [provided Python modules](http://www.cloud.rennes.enst-bretagne.fr/da_circo_doc/index.html)

### 3.2 Tips

- You can access the dashboard of your data-center at `http://10.room.bench.12/horizon`. Connect as `demo` (or `admin`) with password `usr` (for both logins). The API configuration file (`clouds.yaml`) is located in the *API Access* section on the top of the left hand side menu. Don't forget to add the `password` entry to this file after downloading it.
- Do not forget that starting a VM may take a while. Indeed, the VM has to be created, booted and the internal worker service has to be started before the resource is available for transcoding. This creation time may be longer the first time a given image is used on a compute node. Try to figure out why and discuss this point with the teachers.
- You can follow these steps to analyze the two (naive) algorithms:
  - Under which conditions do you expect them to be equivalent;
  - Under which conditions do you expect each one to do better than the other?
  - Do you expect both of them to be able to successfully handle the same number of requests per hour?
  - Do you think that the configuration parameters (maximum number of workers for both of them and the grace period length for one of them) can have an impact on the overall performance (e.g., number of requests per hour that can be successfully processed, time needed to complete each request).

### 3.3 Development Environment Setup

The teachers have prepared a virtual machine for you in which a set of development tools have already been provisioned (eg. VSCode). We call this machine the *VDI VM* because it is hosted on the VDI infrastructure of the school. You MUST use this VM and not your own computer because it is the only solution to guaranty that your configuration is appropriate, especially regarding networking parameters.

Your VDI VM is available at the following address ([https://vdi-rennes.imt-atlantique.fr](https://vdi-rennes.imt-atlantique.fr/)). Log in with your school's credentials. Once connected to this VM, the configuration steps bellow still have to be applied.

Some Python modules are needed to run the provided software. In order to keep your environment as clean as possible, we strongly encourage you to use the virtual environment feature of Python. Your VDI development VM already embeds the `virtualenvwrapper`software to ease the management of this virtual environment.

First, create a virtual environment called *dacirco*. This command has to be run ***\*only once\****.

```
mkvirtualenv dacirco
```

Then, ***\*each time you start a new terminal\****, your virtual environment will have to be activated with:

```
workon dacirco
```

After the first checkout, the dependencies and the executable scripts should be installed. For this, go to the `dacirco` directory and run the following command ***\*once\****.

```
pip install -e .
```

### 3.4 Analyzing the DaCirco Controller Event Logs

The DaCirco controller generates two files every time it starts: `tc-events-xy.txt` and `tc-events-xy.config.txt`. Both files are in the directory from where the DaCirco controller was started. The `xy` part is a two-digit counter that is incremented automatically by the controller, based on the content of the directory from where it is started, so that no file will be overwritten (up to 99 files with this name-pattern in the same directory).

The `tc-events-xy.config.txt` file contains the configuration parameters of the DaCirco controller (you can think of it as a copy of the command line options used, including default values). 

The `tc-events-xy.txt` file contains all the events generated by the DaCirco controller, such as: request received, VM created, worker registered, etc. Each line is an event, with a list of key-value pairs.

You are free to analyze this file directly or to use the commands described below to convert this file to csv files. 

#### 3.4.1 Converting the Event Log to csv Files

You can use the command `dacirco_process_logs tc-events-xy.txt` to convert the event log to two csv files (replace `xy`with the specific value for the file that you want to convert):

- `tc-events-xy-workers.csv` with a line for each worker (VM or pod);
- `tc-events-xy-ok-requests.csv` with a line for each transcoding request.

The first line of each csv file contains a header with the name of the columns, which should be self-explanatory. The columns whose name starts with `timediff` contain the difference between the event in the name of the column and the previous event. For example,`timediff_registered` column (`workers.csv`) contains the difference (time elapsed) between the creation of a worker and its registration (i.e., the boot time). The `timediff_assigned` column (`ok-requests.csv`) contains the time elapsed between the request submission and the time it was assigned to a worker.

If you want to use the `Pandas` library to read these csv files, you can refer to the code (lines 57-85) in the `get_node_stats.py` file (in the `src/dacirco/analytics` folder).

Finally, you can use the command `dacirco_get_node_stats tc-events-xy-ok-requests.csv` to query the Prometheus database. This command crates a new csv file (`tc-events-xy-ok-requests-node-stats.csv` file, whose content is the same as `tc-events-xy-ok-requests.csv` file plus four additional columns: one with the name of the physical machine that hosted the worker that transcoded the request, and three columns with the minimum, average, and maximum value of the load (over one minute) of that machine while it was transcoding that request.

Roughly speaking, the load is the number of jobs that can run at any given time. It is commonly accepted that a machine is overloaded whenever this number is greater than the number of CPUs.

## 4 Git and GitLab

### 4.1 GitLab

A GitLab instance has been installed at the following address: [https://gitlab-devops.cloud.rennes.enst-bretagne.fr](https://gitlab-devops.cloud.rennes.enst-bretagne.fr/). You will use this instance of GitLab to save your source code and share it with the other members of your group and the teaching staff.

Log in to GitLab using your usual school login/password (select the **LDAP** tab). A project has been created for you with name `cloud-pf-f21-gxx` (`xx` is the number that has been attributed to your group). If you are group 1, the name of your project is `cloud-pf-f21-g1`, not `cloud-pf-f21-g01` (of course, the same goes for groups 2, 3, …).

It is recommended to use the **SSH URL** to access your Gitlab repository. You need to setup SSH Keys dedicated for that purpose. Obviously, these keys are different from those used to access Openstack VMs!

Read the following documentation: https://docs.gitlab.com/ce/ssh/README.html to learn how to 1) generate SSH keys on your local machine and 2) add the public key to GitLab (via the **User Settings** interface tab).

### 4.2 Git

If you have never used Git before, please refer to the Git documentation: https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup or [this documentation](http://formations.imt-atlantique.fr/sar-res/doc/git-tutorial/) written by the teachers for another course.

As stated in the documentation, the first thing you should do is to set your user name and email address. This is important because every Git commit uses this information, and it is immutably baked into the commits you start creating (Obviously replace John Doe by your own name!):

```
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

This instance of GitLab uses a self-signed certificate. You need to tell Git to accept this situation silently. 

- Note: If you're using your own computer, you may want that this setting apply only locally. Ask the teachers (or look by yourself!) how to do it (you have first to clone the repository with and the environment variable `GIT_SSL_NO_VERIFY=true` and then apply the configuration to the repository only (removing the `--global` parameter)

```
git config --global http.sslVerify false
```

If you want Git to stop prompting you for password, execute the following commands (set the cache to timeout after 1 hour):

```
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'
```

Author: Alberto Blanc, Christophe Couturier, Gwendal Simon, Jean-Pierre Le Narzul

Created: 2022-11-16 Wed 00:13

[Validate](https://validator.w3.org/check?uri=referer)