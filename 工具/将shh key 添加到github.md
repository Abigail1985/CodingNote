[参考文档](https://docs.github.com/cn/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

在mac上运行
```shell
ssh-keygen -t ed25519 -C "email@example.com" 
```

ed25519是这个密钥文件的名称,如果不加ed25519会默认名字为id_rsa然后覆盖之前的密钥文件

打开.ssh文件夹下面的config文件,添加

```
Host *
  AddKeysToAgent yes
  IgnoreUnknown UseKeychain
  IdentityFile ~/.ssh/id_ed25519
```

再运行
```shell
ssh-add ~/.ssh/id_ed25519
```
注意这里不要加-K

然后复制id_ed25519.pub里的全部内容到git账户设置下面ssh添加处

#工具 