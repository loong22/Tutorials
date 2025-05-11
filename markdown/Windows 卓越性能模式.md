1、按“Win+X”或鼠标右键点击“开始”图标，找到管理员命令并打开：

Win10系统是：“Windows Powershell(管理员)(A)”，

Win11系统是：“Windows 终端(管理员)”

2、输入命令并回车

节能模式: powercfg -duplicatescheme a1841308-3541-4fab-bc81-f71556f20b4a

高性能模式: powercfg -duplicatescheme 8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c

卓越性能: powercfg -duplicatescheme e9a42b02-d5df-448d-aa00-03f14749eb61

3、命令行激活命令

查看当前电源方案: powercfg -getactivescheme

激活卓越性能模式: powercfg -setactive e9a42b02-d5df-448d-aa00-03f14749eb61

