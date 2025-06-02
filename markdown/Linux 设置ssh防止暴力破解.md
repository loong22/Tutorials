1. 编写脚本定期将登录失败的IP添加进入黑名单中
```
touch ssh.sh

chmod +x ssh.sh

vim ssh.sh
```
2. ssh.sh内容如下

```

#!/bin/bash

iplist=$(/bin/lastb | awk '{print $3}' | sort | uniq -c | awk '{if ($1>3) print $2}')
#                                                                      ^
#                                                                      3(输错密码次数,可自行修改)
#

#追加到黑名单并清空登录日志
for ip in ${iplist}
do
	echo ALL: ${ip} >> /etc/hosts.deny
	echo > /var/log/btmp
done


```

3. 设置定时执行

编辑
```
crontab -e

```

添加定时脚本命令，每分钟执行一次
```
0 */1 * * * sh /root/ssh.sh
```
