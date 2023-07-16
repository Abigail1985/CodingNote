
下载electron-ssr的deb

按照[electron-ssr-backup/Ubuntu.md at master · qingshuisiyuan/electron-ssr-backup · GitHub](https://github.com/qingshuisiyuan/electron-ssr-backup/blob/master/Ubuntu.md)先安装依赖，并设置系统代理，最后采用auto方式

看log报错没有liblibcrypto.a,按照[ubuntu 运行electron-ssr报错无liblibcrypto.a_no such file or directory: b'liblibcrypto.a_萧易风船长的博客-CSDN博客](https://blog.csdn.net/qq_35975855/article/details/126255285) 在usr/lib/x86-linux-gnu下找到后运行`sudo ln -s libcrypto.a liblibcrypto.a
`

后来报错# 拒绝连接 can not initialize cipher context

按照
[拒绝连接 can not initialize cipher context · Issue #126 · shadowsocksrr/electron-ssr · GitHub](https://github.com/shadowsocksrr/electron-ssr/issues/126)

[[经验] 关于在 Ubuntu 22.04 和 OpenSSL 3 下的 RC4-MD5 算法的问题 · Issue #90 · TyrantLucifer/ssr-command-client · GitHub](https://github.com/TyrantLucifer/ssr-command-client/issues/90)

[[OpenSSL] How OpenSSL3 enables RC4-MD5 support - Vinsonws](https://blog.vinsonws.cn/2023/05/25/openssl-openssl3-如何开启-rc4-md5-支持/)

成功翻墙

ps：linux查询ip地址命令行可以 curl cip.cc