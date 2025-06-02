# 1. **使用iptables统计端口流量**：
iptables是Linux内核自带的防火墙工具，可以通过添加规则来统计特定端口的流量。

   - **添加统计规则**：

     - 统计入站流量（以端口8080为例）：

       ```bash
       sudo iptables -A INPUT -p tcp --dport 8080
       ```

     - 统计出站流量：

       ```bash
       sudo iptables -A OUTPUT -p tcp --sport 8080
       ```

   - **查看统计数据**：

     ```bash
     sudo iptables -L -v -n
     ```

     该命令将显示每条规则的流量统计信息，包括数据包数量和字节数。

   - **注意事项**：iptables的统计数据在系统重启或iptables服务重启时可能会被重置，因此适用于短期监控。
	
	在使用 `iptables` 时，如果你想清除流量统计信息（例如数据包计数和字节计数），可以通过以下方法实现。`iptables` 的流量统计信息通常与规则相关联，清除统计信息并不会删除规则本身，只是将计数器重置为零。

	### 方法：重置计数器
	你可以使用 `iptables` 的 `-Z` 参数（zero）来清除流量统计信息。具体步骤如下：

	1. **清除所有规则的计数器**  
	   如果你想清除所有 `iptables` 规则的流量统计信息，可以直接运行：
	   ```bash
	   iptables -Z
	   ```
	   这会将所有链（例如 INPUT、FORWARD、OUTPUT 等）的计数器重置为零。

	2. **清除特定链的计数器**  
	   如果只想清除某个链（例如 INPUT）的统计信息，可以指定链名：
	   ```bash
	   iptables -Z INPUT
	   ```

	3. **清除特定规则的计数器**  
	   如果你只想清除某条具体规则的统计信息，需要指定链和规则编号。  
	   - 先用以下命令查看规则编号：
		 ```bash
		 iptables -L -v -n --line-numbers
		 ```
		 输出会显示每条规则的编号（第一列）。
	   - 然后用 `-Z` 指定链和规则编号，例如清除 INPUT 链中第 3 条规则的计数器：
		 ```bash
		 iptables -Z INPUT 3
		 ```

	### 注意事项
	- `-Z` 只会重置计数器，不会影响规则的正常工作。
	- 如果你的规则是由防火墙管理工具（如 `ufw` 或 `firewalld`）生成的，建议检查这些工具是否有自己的重置统计信息的方法，避免直接操作 `iptables` 导致冲突。
	- 执行这些命令需要 root 权限，通常需要加上 `sudo`（例如 `sudo iptables -Z`）。

	### 验证
	清除后，你可以再次运行以下命令检查计数器是否归零：
	```bash
	iptables -L -v -n
	```
	`-v` 参数会显示详细的流量统计信息（数据包数和字节数）。

	如果你有更具体的需求（例如只清某类流量统计），可以告诉我，我再帮你细化！


# 2. 保存iptables规则
```
#安装iptables-persistent组件

apt-get install iptables-persistent

#执行下面的命令自动保存当前的规则，重启自动生效

sudo dpkg-reconfigure iptables-persistent

#保险起见，执行下列2条命令，覆盖rules.v4文件iptables-save > iptables.conf#保存规则到文件
cp iptables.conf /etc/iptables/rules.v4#覆盖文件，重启自动生效
#恢复保存的iptables.conf文件iptables-restore < iptables.conf
```


# 3. iptables常见命令

放行所有端口
```
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
```

设置端口仅允许指定ip访问
```
#如果是ssh端口则必须在VNC界面执行，否则ssh连接会直接断开
#设置只允许1.1.1.1访问本机的10028端口
iptables -A INPUT -p tcp --dport 10028 -s 1.1.1.1 -j ACCEPT
iptables -A INPUT -p tcp --dport 10028 -j DROP
```

```
iptables -L -n --line-number #查看到每个规则chain的序列号。
iptables -D INPUT 3 #删除INPUT的第三条已添加规则，这里3代表第几行规则#iptables防火墙
service iptables status #查看iptables防火墙状态
service iptables start #开启防火墙
service iptables stop #停止防火墙#firewall防火墙
systemctl status firewalld #查看firewall防火墙服务状态
service firewalld start #开启防火墙
service firewalld stop #关闭防火墙
```

限制指定端口网速
```
#hashlimit-name必须是唯一的
#限速
iptables -A INPUT -p tcp --dport 10000:65500 -m hashlimit --hashlimit-name conn_limitA --hashlimit-htable-expire 30000 --hashlimit-upto 3500kb/s --hashlimit-mode srcip --hashlimit-burst 3575kb -j ACCEPT
iptables -A INPUT -p tcp --dport 10000:65500 -j DROP
iptables -A OUTPUT -p tcp --sport 10000:65500 -m hashlimit --hashlimit-name conn_limitB --hashlimit-htable-expire 30000 --hashlimit-upto 3500kb/s --hashlimit-mode dstip --hashlimit-burst 3575kb -j ACCEPT
iptables -A OUTPUT -p tcp --sport 10000:65500 -j DROP

#限速
iptables -A INPUT -p tcp --dport 10000:65500 -m hashlimit --hashlimit-name conn_limitA --hashlimit-htable-expire 30000 --hashlimit-upto 15000kb/s --hashlimit-mode srcip --hashlimit-burst 15010kb -j ACCEPT
iptables -A INPUT -p tcp --dport 10000:65500 -j DROP
iptables -A OUTPUT -p tcp --sport 10000:65500 -m hashlimit --hashlimit-name conn_limitB --hashlimit-htable-expire 30000 --hashlimit-upto 15000kb/s --hashlimit-mode dstip --hashlimit-burst 15010kb -j ACCEPT
iptables -A OUTPUT -p tcp --sport 10000:65500 -j DROP
```

iptables禁icmp&ping
```
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
iptables -A OUTPUT -p icmp --icmp-type echo-request -j DROP

```
