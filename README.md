## Tutorials
Some collected and summarized tutorials

### Git 配置 HTTP(S) 代理
```
git config --global http.proxy "socks5://127.0.0.1:10808"
git config --global https.proxy "socks5://127.0.0.1:10808"

#验证是否配置成功
git config --global --get http.proxy
git config --global --get https.proxy

#移除代理配置
git config --global --unset http.proxy
git config --global --unset https.proxy
```
### Git 配置用户名和邮箱
```
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

#验证是否配置成功
git config --global user.name
git config --global user.email
```

### Git 配置私钥
`vim ~/.ssh/config`

添加以下内容
```
Host github.com
        IdentityFile ~/.ssh/keyFileName
        User git
```
# Git
…or create a new repository on the command line
```
#HTTPS
echo "# ForzaHorizon5-GameSaves" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/cs135711/ForzaHorizon5-GameSaves.git
git push -u origin main

#SSH
echo "# ForzaHorizon5-GameSaves" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:cs135711/ForzaHorizon5-GameSaves.git
git push -u origin main
```
…or push an existing repository from the command line
```
#HTTPS
git remote add origin https://github.com/cs135711/ForzaHorizon5-GameSaves.git
git branch -M main
git push -u origin main

#SSH
git remote add origin git@github.com:cs135711/ForzaHorizon5-GameSaves.git
git branch -M main
git push -u origin main
```

### Git 提交代码
```
#先在github上面创建仓库, 默认分支为mian
create repo

#打开本地文件夹, 使用git clone 下载仓库
git clone https://github.com/[name]/[repoName].git

#打开git clone 下载下来的代码所在的文件夹, 建立远程连接
git remote add origin git@github.com:[name]/[repoName].git

#查看远程仓库地址, 如果是https则需要更改为ssh

git remote -v
#https方式输出如下
origin  https://github.example.com/username/Tutorials.git (fetch)
origin  https://github.example.com/username/Tutorials.git (push)

#修改为ssh方式
git remote set-url origin git@github.com:username/Tutorials.git

#添加新建文件夹
git add ./dir 

#查看文件状态
git status

#Commit
git commit -m "init"

#提交到github, 分支为main, origin是默认的github远程仓库的地址, 不需要改
git push -u origin main

#拉取代码到别的文件夹
git clone [仓库地址]
```
