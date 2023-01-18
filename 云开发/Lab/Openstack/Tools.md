# Tools, Platforms and Work Organization

Throughout this UE, you will use the following tools/platforms:

- A VM -Virtual Machine- (**one VM per student in a pair**)
- A GitLab project (**one project per pair**)
- An OpenStack project (**one project per pair**).

## 1 Virtual Machine

### 1.1 Using a Virtual Machine

Most of the time, during the labs, you will be using a Linux virtual machine running on top of the RHV (RedHat Virtualization) platform. The name of this virtual machine is **TP-DevOps-U22**. You will us the SPICE console to access the RHEV VM remotely. You can access this VM from any machine. The SPICE console will forward the keyboard input and mouse movements to the RHEV VM and display locally the screen of the VM.

For this to work, you must have a SPICE client (virt-viewer) installed on your Operating System. If this is not already the case, the following page will give you links to install it on various operating systems. https://intranet.imt-atlantique.fr/assistance-support/informatique/didacticiels/fiche-synthetique-des-solutions-de-virtualisation-utilisees-en-tp/

Open the firefox browser and type the following address in the address bar: [http://vdi.imt-atlantique.fr](http://vdi.imt-atlantique.fr/). Next, authenticate with your usual school login/password and click on the "VM Portal" link. Locate the TP-DevOps-U22-?? virtual machine and click on the **Run**button. A number (xx) will be assigned to your VM and you will keep this number until the end of the DevOps UE. Click on the refresh icon (top right of the page), locate the TP-DevOps-U22-xx (xx being the number assigned to your VM) and select "Spice Console" from the scroll-down menu. Click on "OK" to run the console. Select "Plein écran" from the scroll-down menu named "Affichage". You are now working on your TP-DevOps-U22 virtual machine in a seamless way.

### 1.2 Configuring your Virtual Machine

#### 1.2.1 Update and Upgrade

The first thing to do is to update the list of available packages and to install the newer versions of theses packages. Launch a Terminal and run the following commands (be patient … upgrading the system takes a while).

```
> sudo apt update
> sudo apt -y upgrade
```

`sudo` is a program that allows you to run programs with the privileges of the superuser (more exactly, of another user who, by default, is the superuser). When running `sudo`, you type your password (remember, on the virtual machine, you are `user` and the password is `usr`) and then, you are allowed to run privileged commands. The password is retained for 15 minutes. It means that the next time you will issue `sudo`, you will not have to type the password again.

#### 1.2.2 Install Python Packages

You will use three tools to install and manage your python environment: `pip`, `pipx` and `pyenv` (a python version management tool).

As stated in the `pipx` home page (https://github.com/pipxproject/pipx), `pipx` is a tool to help you install and run end-user applications written in Python. It's roughly similar to macOS's brew, JavaScript's npx, and Linux's apt. You will use `pipx` mainly for installing tools.

You will also use python virtual environments. A virtual environement is a self-contained directory tree that contains a Python installation for a particular version of Python, plus a number of additional packages. Typically, to work on the development of your photo sharing application, you will create a dedicated virtual env in which you will install the required packages with `pip`.

In short:

- use `pipx` for installing tools
- create and activate a virtual environment (`pyenv virtualenv` and `pyenv activate`) and then, use `pip` for installing packages

All this will be explained to you in the texts of the labs as and when needed.

## 2 OpenStack

You are all members of an OpenStack project whose name respects this schema: `devops-s23-xy`, `xy` being your group number (check the moodle page to know your group number). Yes, the name is a bit weird but we will have to live with it. Your username on the OpenStack platform is your school username. The password is `changeit`. You will change it when you will start to learn how to use the OpenStack platform.

### 2.1 Getting Started

You will mainly use the CLI (Command Line Interface) called OSC (OpenStackClient) to interact with the OpenStack platform. However, using OSC requires some preliminary actions on your part through the OpenStack dashboard, the Graphical User Interface to the OpenStack platform.

Open the Firefox browser (inside the virtual machine) and ask the OpenStack URL to the teaching staff. Next, type your OpenStack login/password and click on Connect (Select **Keystone credentials** in the first field and use `default` for the domain.). Remember that your login is your school login and the password is `changeit`.

The first thing to do will be to change your OpenStack password. 

Click on the menu entitled with your username (top-right of the window) and select `Settings`. Select `Change Password` on the left hand pane and change your password. For security reasons, do not choose the same password as the one you are using at school to connect on the machines in the labs. **Please, remember your OpenStack** **password !** If you forget it, we will be obliged to ask the platform administrator to reset it and you will lose time.

### 2.2 Overview of the User Interface

The Web browser shows the Graphical User Interface (called Horizon) of the OpenStack platform. Thanks to this interface, you can instantiate virtual machines, create networks, routers and much more. By using the left hand pane, you may select the tab do be displayed on the right hand pane. On the top left of the window, you may select the OpenStack project you are working on. You may have access to several projects. Make sure to use the project, named `devops-s23-xy` (please, replace `xy` by the number that has been attributed to your group, group 0 is `devops-s23-00`, group 1 is `devops-s23-01`, etc.).

Make sure that the selected OpenStack project is `devops-s23-xy` by pulling down the top-left menu.

An external network (that you can reach from the outside) has already been created for you by the administrator of the OpenStack platform. Soon, you will create a Floating IP on this network and you will assign it to a virtual machine so that your VM could be reached from outside the OpenStack platform. Do not pay attention to the `common` network. You will not use it.

#### 2.2.1 Download the `OpenStack RC File v3`

In order to be able to use the command-line interface (OSC), a few environment variables must be set. These variables are defined in a file that is downloadable from the OpenStack dashboard.

Pull the menu whose title is your username (top-right of the window) and select `OpenStack RC File v3` on the top right of the window. Save the file in your home directory. The name of the file is `devops-s23-xy-openrc.sh`

Every shell used to issue openstack commands must preliminarily source this file.

```
> source devops-s23-xy-openrc.sh
```

When prompted for the password, enter your OpenStack password.

### 2.3 Using `OSC`

#### 2.3.1 Shell or Command mode

You may use the CLI in two modes: Shell or Command. When using the Command mode, you run the CLI each time you need to issue a command, like

```
> openstack network list
```

When using the Shell mode, you run the CLI once and then you issue commands as many times as required.

```
> openstack
(openstack) network list
(openstack) server list
(openstack) help
```

### 2.4 Create a Key Pair

Before creating a virtual machine and attaching it to a subnet, it is necessary to create a pair of private/public keys. The public key will be stored on the virtual machine in OpenStack (as well as the machine that you are using) and the private key will be stored only in your local machine to allow you to establish a secure connection to the virtual machine (running on OpenStack) from your local machine.

Create a keypair using the following command (you are strongly encouraged to use a non-empty passhphrase):

```
> ssh-keygen -t rsa -b 4096 -f ~/.ssh/osvm
```

Do not forget your passhphrase, if you do, you will not be able to "unlock" the key and you will you prevented from connecting to your VMs.

The previous command has generated two files in `~/.ssh`, one with the public key (whose extension is `.pub`) and another one with the (encrypted) private key.

Upload the public key to OpenStack with the following command:

```
> openstack keypair create --public-key ~/.ssh/osvm.pub mykey
```

Use the dashboard to locate the keypair that you just created.

## 3 GitLab

### 3.1 GitLab

A GitLab instance has been installed at the following address: [https://gitlab.imt-atlantique.fr](https://gitlab.imt-atlantique.fr/). You will use this instance of GitLab to:

- host your OpenStack Heat and Ansible playbook files
- host the source files of your photo sharing application
- store your docker images in a private registry
- implement continuous integration, delivery and deployment for the services of your photo sharing application

Log in to GitLab using your usual school login/password (select the **LDAP** tab).

You are the manager of a GitLab project that you can clone with SSH using the following URL: `git@gitlab.imt-atlantique.fr:devops-s23/devops-s23-gxy.git`. (of course, you will replace `xy` with your group number).

Before cloning your project (**using the SSH URL**) on your local VM, you need to to set up a SSH key pair to establish a secure connection channel between your local VM and GitLab. You will use the same key pair as the one used to connect to your OpenStack VMs.

Choose the **Settings** for your account (top-right of the window) and select **SSH Keys** from the panel on the left side of the window. Paste your public SSH key (the contents of the file `osvm.pub` located in `~/.ssh`) in the text box. Choose a **Title** and click on **Add key**.

Now you're going to make sure that you can clone your project using the **git** command from the Terminal.

To determine the url for your project, choose your project on the GitLab interface and pull down the menu under the **clone** button. Copy the URL under the "Clone with SSH" string Type the following command on the Terminal (replace UrlOfYourProject with the paste url).

```
> git clone UrlOfYourProject
```

### 3.2 Git

If you have never used Git before, please refer to the Git documentation: https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup

As stated in the documentation, the first thing you should do is to set your user name and email address. This is important because every Git commit uses this information, and it’s immutably baked into the commits you start creating:

```
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

If you want Git stops prompting you for password, execute the following commands (set the cache to timeout after 1 hour):

```
> git config --global credential.helper cache
> git config --global credential.helper 'cache --timeout=3600'
```

Author: Alberto Blanc, Jean-Pierre Le Narzul

Created: 2023-01-03 Tue 00:03

[Validate](https://validator.w3.org/check?uri=referer)