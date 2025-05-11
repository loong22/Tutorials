## Tutorials
Some collected and summarized tutorials

### Git 配置 HTTP(S) 代理
```
git config --global http.proxy "socks5://127.0.0.1:1080"
git config --global https.proxy "socks5://127.0.0.1:1080"
```
### Git 配置私钥
```
Host github.com
        IdentityFile ~/.ssh/keyFile
        User git
```

### Git 提交代码
```
#1.先在github上面创建仓库, 默认分支为mian
#2.打开本地文件夹, 使用git clone 下载仓库

#添加新建文件夹
git add ./dir 

#查看文件状态
git status

#Commit
git commit -m "第一次提交"

#提交到github, 分支为main, origin是默认的github远程仓库的地址, 不需要改
git push -u origin main

#拉取代码到本地
git clone [仓库地址]
```