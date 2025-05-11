### 1. 开机默认进入命令行模式
输入命令：
```
sudo systemctl set-default multi-user.target 
```
重启：
```
reboot
```
要进入图形界面，只需要输入命令`startx`, 从图形界面切换回命令行：`ctrl+alt+F7`

### 2. 开机默认进入图形用户界面
输入命令：
```
sudo systemctl set-default graphical.target 
```
重启：
```
reboot
```
要进入命令行模式：`ctrl+alt+F2`, 从命令行切换到图形界面：`ctrl+alt+F7`

转自:

https://blog.csdn.net/qq_42955378/article/details/86673976
