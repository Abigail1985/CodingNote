在本地vault里链接上远程仓库

```bash
git init
git add .
git commit -m "commit"
git branch -M master
git remote add origin git@github.com:Abigail1985/xxx.git
git push -u origin master
```

然后再用Obsidian-git的命令