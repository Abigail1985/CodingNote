
设置 http_proxy 代理主要是内网与外网ip进行连接
- ip是自身电脑的ip地址
- 端口号是代理的端口


可以通过配置文件进行配置etc/bash.bashrc或者/etc/profile 中添加如下 环境变量:

```
export http_proxy=http://ip:端口号
export https_proxy=http://ip:端口号
```

之后启动配置源即可


也可以通过命令行直接输入命令即可
```bash
sudo export http_proxy=http://ip:端口号 
export https_proxy=http://ip:端口号
```

可以通过curl进行测试，是否可以连接上网址


命令行取消代理：

```bash
unset http_proxy
或：
unset https_proxy
```


例子：imt机器上的/etc/profile ：

```
ubuntu@k8s-cont:~$ cat /etc/profile
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "${PS1-}" ]; then
  if [ "${BASH-}" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
export http_proxy=http://proxy.rennes.enst-bretagne.fr:3128
export https_proxy=http://proxy.rennes.enst-bretagne.fr:3128
export no_proxy="127.0.0.1, localhost, registry.cloud.rennes.enst-bretagne.fr, gitlab-devops.cloud.rennes.enst-bretagne.fr, openstack.imt-atlantique.fr, gitlab.imt-atlantique.fr, 10.233.0.0/16, 192.168.10.1, 192.168.10.2, 192.168.10.3, 192.168.10.4, 192.168.10.5, 192.168.10.6, 192.168.10.7, 192.168.10.8, 192.168.10.9, 192.168.10.10, 192.168.10.11, 192.168.10.12, 192.168.10.13, 192.168.10.14, 192.168.10.15, 192.168.10.16, 192.168.10.17, 192.168.10.18, 192.168.10.19, 192.168.10.20, 192.168.10.21, 192.168.10.22, 192.168.10.23, 192.168.10.24, 192.168.10.25, 192.168.10.26, 192.168.10.27, 192.168.10.28, 192.168.10.29, 192.168.10.30, 192.168.10.31, 192.168.10.32, 192.168.10.33, 192.168.10.34, 192.168.10.35, 192.168.10.36, 192.168.10.37, 192.168.10.38, 192.168.10.39, 192.168.10.40, 192.168.10.41, 192.168.10.42, 192.168.10.43, 192.168.10.44, 192.168.10.45, 192.168.10.46, 192.168.10.47, 192.168.10.48, 192.168.10.49, 192.168.10.50, 192.168.10.51, 192.168.10.52, 192.168.10.53, 192.168.10.54, 192.168.10.55, 192.168.10.56, 192.168.10.57, 192.168.10.58, 192.168.10.59, 192.168.10.60, 192.168.10.61, 192.168.10.62, 192.168.10.63, 192.168.10.64, 192.168.10.65, 192.168.10.66, 192.168.10.67, 192.168.10.68, 192.168.10.69, 192.168.10.70, 192.168.10.71, 192.168.10.72, 192.168.10.73, 192.168.10.74, 192.168.10.75, 
```
