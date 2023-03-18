
```bash
#启动ssh
/usr/sbin/sshd -D &
#检测是否启动成功
netstat -apn | grep ssh
```