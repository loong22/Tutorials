### 1. 安装

```
apt-get install subversion

yum install subversion
```

### 2. 建立文件夹

```
#建立库位置
mkdir /home/svn

#建立版本库
svnadmin create /home/svn/repos
```

### 3. 修改鉴权文件

```
#打开版本库文件夹
cd /home/svn/repos/conf
#authz : 权限控制文件， passwd : 账号密码文件， svnserve.conf : 服务器配置文件

vim passwd
///////////////////添加内容(用户名 = 密码)
[users]
admin = admin
user1 = user1
user2 = user2
///////////////////

vim authz
#方式1
///////////////////添加内容[/]表示仓库下所有文件，*= r 表示任何用户都有读取权限
[/]
admin = rw
user1 = rw
* = r
///////////////////
#方式2 分组形式
///////////////////
group1 = admin
group2 = user1,user2

[/]
@group1 = rw
@group2 = r
* = r
///////////////////

vim svnserve.conf
///////////////////取消注释并修改
anon-access = read
auth-access = write
password-db = /home/svn/repos/conf/passwd
authz-db = /home/svn/repos/conf/authz
realm = /home/svn/
///////////////////
```

### 4. 启动/终止命令

```
#启动命令(默认端口3690) 
#指定端口--listen-port <port>
svnserve -d -r /home/svn/repos --listen-port 3691

#终止命令
killall svnserve

#查看是否启动
lsof -i:3691

#查看svn进程
ps -ef | grep svn
```

### 5. 本地添加文件到本地的svn服务器

```
#导入test目录下的所有文件到svn://127.0.0.1:3691
svn import /home/test/ svn://127.0.0.1:3691 -m "upload files"

#myproj 是一个目录
svn import -m "New import" myproj  http://svn.red-bean.com/repos/trunk/misc/myproj

#导入单个文件需要指定svn路径下的文件名
svn import -m "Adding just a file" file_name http://path/to/svn/repo/file_name

#登录svn本地库更新文件到最新
svn update
```

### 6. 其他

```
svn list svn://127.0.0.1:3691
# 导出到本地
svn co svn://localhost/dir /home/testtools --username wzhnsc

svn co http://localhost/test/testapp --username wzhnsc

svn checkout svn://localhost/dir /home/testtools --username wzhnsc

svn checkouthttp://localhost/test/testapp --username wzhnsc

# SVN更新使用指定用户名和密码，不保存cache

svn update --username user1 --password password123 --no-auth-cache

```

[SVN命令使用详解](https://www.cnblogs.com/hk315523748/p/6178273.html)

```
1、检出
svn co http://路径(目录或文件的全路径)　[本地目录全路径] --username 用户名 --password 密码
svn co svn://路径(目录或文件的全路径)　[本地目录全路径] --username 用户名 --password 密码
svn checkout http://路径(目录或文件的全路径)　[本地目录全路径] --username　用户名
svn checkout svn://路径(目录或文件的全路径)　[本地目录全路径] --username　用户名
注：如果不带--password 参数传输密码的话，会提示输入密码，建议不要用明文的--password 选项。
　　其中 username 与 password前是两个短线，不是一个。
　　不指定本地目录全路径，则检出到当前目录下。
例子：
svn co svn://localhost/测试工具 /home/testtools --username wzhnsc
svn co http://localhost/test/testapp --username wzhnsc
svn checkout svn://localhost/测试工具 /home/testtools --username wzhnsc
svn checkouthttp://localhost/test/testapp --username wzhnsc

2、导出(导出一个干净的不带.svn文件夹的目录树)
svn export [-r 版本号] http://路径(目录或文件的全路径) [本地目录全路径]　--username　用户名
svn export [-r 版本号] svn://路径(目录或文件的全路径) [本地目录全路径]　--username　用户名
svn export 本地检出的(即带有.svn文件夹的)目录全路径 要导出的本地目录全路径
注：第一种从版本库导出干净工作目录树的形式是指定URL，
　　　如果指定了修订版本号，会导出相应的版本，
　　　如果没有指定修订版本，则会导出最新的，导出到指定位置。
　　　如果省略 本地目录全路径，URL的最后一部分会作为本地目录的名字。
　　第二种形式是指定 本地检出的目录全路径 到 要导出的本地目录全路径，所有的本地修改将会保留，
　　　但是不在版本控制下(即没提交的新文件，因为.svn文件夹里没有与之相关的信息记录)的文件不会拷贝。
例子：
svn export svn://localhost/测试工具 /home/testtools --username wzhnsc
svn export svn://localhost/test/testapp --username wzhnsc
svn export /home/testapp /home/testtools

3、添加新文件
svn　add　文件名
注：告诉SVN服务器要添加文件了，还要用svn commint -m真实的上传上去！
例子：
svn add test.php ＜－ 添加test.php
svn commit -m “添加我的测试用test.php“ test.php
svn add *.php ＜－ 添加当前目录下所有的php文件
svn commit -m “添加我的测试用全部php文件“ *.php

4、提交
svn　commit　-m　“提交备注信息文本“　[-N]　[--no-unlock]　文件名
svn　ci　-m　“提交备注信息文本“　[-N]　[--no-unlock]　文件名
必须带上-m参数，参数可以为空，但是必须写上-m
例子：
svn commit -m “提交当前目录下的全部在版本控制下的文件“ * ＜－ 注意这个*表示全部文件
svn commit -m “提交我的测试用test.php“ test.php
svn commit -m “提交我的测试用test.php“ -N --no-unlock test.php ＜－ 保持锁就用–no-unlock开关
svn ci -m “提交当前目录下的全部在版本控制下的文件“ * ＜－ 注意这个*表示全部文件
svn ci -m “提交我的测试用test.php“ test.php
svn ci -m “提交我的测试用test.php“ -N --no-unlock test.php ＜－ 保持锁就用–no-unlock开关

5、更新文件
svn　update
svn　update　-r　修正版本　文件名
svn　update　文件名
例子：
svn update ＜－ 后面没有目录，默认将当前目录以及子目录下的所有文件都更新到最新版本
svn update -r 200 test.cpp ＜－ 将版本库中的文件 test.cpp 还原到修正版本（revision）200
svn update test.php ＜－ 更新与版本库同步。
　　　　　　　　　　　 提交的时候提示过期冲突，需要先 update 修改文件，
　　　　　　　　　　　 然后清除svn resolved，最后再提交commit。

6、删除文件
svn　delete　svn://路径(目录或文件的全路径) -m “删除备注信息文本”
推荐如下操作：
svn　delete　文件名
svn　ci　-m　“删除备注信息文本”
例子：
svn delete svn://localhost/testapp/test.php -m “删除测试文件test.php”
推荐如下操作：
svn delete test.php
svn ci -m “删除测试文件test.php”

７、加锁/解锁
svn　lock　-m　“加锁备注信息文本“　[--force]　文件名
svn　unlock　文件名
例子：
svn lock -m “锁信测试用test.php文件“ test.php
svn unlock test.php

8、比较差异
svn　diff　文件名
svn　diff　-r　修正版本号m:修正版本号n　文件名
例子：
svn diff test.php＜－ 将修改的文件与基础版本比较
svn diff -r 200:201 test.php＜－ 对 修正版本号200 和 修正版本号201 比较差异

9、查看文件或者目录状态
svn st 目录路径/名
svn status 目录路径/名＜－ 目录下的文件和子目录的状态，正常状态不显示
　　　　　　　　　　　　　【?：不在svn的控制中； M：内容被修改；C：发生冲突；
　　　　　　　　　　　　　　A：预定加入到版本库；K：被锁定】
svn -v 目录路径/名
svn status -v 目录路径/名＜－ 显示文件和子目录状态
　　　　　　　　　　　　　　【第一列保持相同，第二列显示工作版本号，
　　　　　　　　　　　　　　　第三和第四列显示最后一次修改的版本号和修改人】
注：svn status、svn diff和 svn revert这三条命令在没有网络的情况下也可以执行的，
　　原因是svn在本地的.svn中保留了本地版本的原始拷贝。

10、查看日志
svn　log　文件名
例子：
svn log test.php＜－ 显示这个文件的所有修改记录，及其版本号的变化

11、查看文件详细信息
svn　info　文件名
例子：
svn info test.php

12、SVN 帮助
svn　help ＜－ 全部功能选项
svn　help　ci ＜－ 具体功能的说明

13、查看版本库下的文件和目录列表
svn　list　svn://路径(目录或文件的全路径)
svn　ls　svn://路径(目录或文件的全路径)
例子：
svn list svn://localhost/test
svn ls svn://localhost/test ＜－ 显示svn://localhost/test目录下的所有属于版本库的文件和目录

14、创建纳入版本控制下的新目录
svn　mkdir　目录名
svn　mkdir　-m　"新增目录备注文本"　http://目录全路径
例子：
svn mkdir newdir
svn mkdir -m "Making a new dir." svn://localhost/test/newdir
注：添加完子目录后，一定要回到根目录更新一下，不然在该目录下提交文件会提示“提交失败”
svn update
注：如果手工在checkout出来的目录里创建了一个新文件夹newsubdir，
　　再用svn mkdir newsubdir命令后，SVN会提示：
　　svn: 尝试用 “svn add”或 “svn add --non-recursive”代替？
　　svn: 无法创建目录“hello”: 文件已经存在
　　此时，用如下命令解决：
　　svn add --non-recursive newsubdir
　　在进入这个newsubdir文件夹，用ls -a查看它下面的全部目录与文件，会发现多了：.svn目录
　　再用 svn mkdir -m "添hello功能模块文件" svn://localhost/test/newdir/newsubdir 命令，
　　SVN提示：
　　svn: File already exists: filesystem '/data/svnroot/test/db', transaction '4541-1',
　　path '/newdir/newsubdir '

15、恢复本地修改
svn　revert　[--recursive]　文件名
注意: 本子命令不会存取网络，并且会解除冲突的状况。但是它不会恢复被删除的目录。
例子：
svn revert foo.c ＜－ 丢弃对一个文件的修改
svn revert --recursive . ＜－恢复一整个目录的文件，. 为当前目录

16、把工作拷贝更新到别的URL
svn　switch　http://目录全路径　本地目录全路径
例子：
svn switch http://localhost/test/456 . ＜－ (原为123的分支)当前所在目录分支到localhost/test/456

17、解决冲突
svn　resolved　[本地目录全路径]
例子：
$ svn update
C foo.c
Updated to revision 31.
如果你在更新时得到冲突，你的工作拷贝会产生三个新的文件：
$ ls
foo.c
foo.c.mine
foo.c.r30
foo.c.r31
当你解决了foo.c的冲突，并且准备提交，运行svn resolved让你的工作拷贝知道你已经完成了所有事情。
你可以仅仅删除冲突的文件并且提交，但是svn resolved除了删除冲突文件，还修正了一些记录在工作拷贝管理区域的记录数据，所以我们推荐你使用这个命令。

18、不checkout而查看输出特定文件或URL的内容
svn　cat　http://文件全路径
例子：
svn cat http://localhost/test/readme.txt
19、新建一个分支copy
svn copy branchA branchB -m "make B branch" // 从branchA拷贝出一个新分支branchB
20、合并内容到分支merge
svn merge branchA branchB // 把对branchA的修改合并到分支branchB

原文详情：[http://www.roncoo.com/article/detail/124725](http://www.roncoo.com/article/detail/124725)
```

### 7. 配置http方式访问svn库

```
# 安装apache2
apt-get install apache2
apt-get install libapache2-mod-svn

# 修改svn所在文件夹所有者
chown -R www-data:www-data /root/svn/

# 修改/etc/apache/mods-enabled/dav_svn.load文件
# 添加mod_dav_svn.so动态库

# LoadModule dav_svn_module /usr/lib/apache2/modules/mod_dav_svn.so

```

修改文件 `/etc/apache2/ports.conf`

```
root@u22:~/svn# more /etc/apache2/ports.conf
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80

<IfModule ssl_module>
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

修改文件`/etc/apache2/mods-enabled/dav\_svn.conf`

```

<Location /svn>

  DAV svn
  SVNPath /root/svn/repos
  AuthType Basic
  AuthName "Subversion Repository"
  AuthUserFile /etc/apache2/dav_svn.passwd
  AuthzSVNAccessFile /root/svn/repos/conf/authz
  Require valid-user
  Satisfy ALL
  SVNAutoversioning on
  ModMimeUsePathInfo on
</Location>
```

修改文件`/etc/apache2/mods-enabled/dav\_svn.load`

```
# Depends: dav
<IfModule !mod_dav_svn.c>
    <IfModule !mod_dav.c>
        Include mods-enabled/dav.load
    </IfModule>
    LoadModule dav_svn_module /usr/lib/apache2/modules/mod_dav_svn.so
    LoadModule authz_svn_module /usr/lib/apache2/modules/mod_authz_svn.so
    LoadModule dav_module /usr/lib/apache2/modules/mod_dav.so
    LoadModule dav_fs_module /usr/lib/apache2/modules/mod_dav_fs.so
</IfModule>
```

添加密文的passwd

```

htpasswd -c /etc/apache2/dav_svn.passwd saber
htpasswd -m /etc/apache2/dav_svn.passwd user1

#输入两次密码
sudo chmod 644 /etc/apache2/dav_svn.passwd
```
