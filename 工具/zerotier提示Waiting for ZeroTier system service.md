这是因为系统托盘的 ZeroTier UI 无法连接到 Control Panel(link)[https://blog.ascn.site/post/e807d30218c1/]

zerotier系统托盘只是一个入口(link)[https://github.com/zerotier/ZeroTierOne/issues/1337]

在命令行运行zerotier-cli时候提示port error，参考了这篇(文章)[https://zerotier.atlassian.net/wiki/spaces/SD/pages/7241787/PORT+ERROR+on+Mac]和这篇(t讨论)[https://github.com/zerotier/ZeroTierOne/issues/1337]

在命令行运行了：

```shell
sudo launchctl unload -w /Library/LaunchDaemons/com.zerotier.one.plist
sudo launchctl load -w /Library/LaunchDaemons/com.zerotier.one.plist
```
*注意要加上-w*

成功运行了zerotier并连接上网络，虽然从菜单栏ui还是Waiting for ZeroTier system service

#工具