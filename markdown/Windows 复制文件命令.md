### Copy-Item：
在 Windows PowerShell 中，你可以使用 `Copy-Item` 命令来复制 D 盘的所有文件和文件夹到 E 盘。具体命令如下：

```powershell
Copy-Item -Path D:\* -Destination E:\ -Recurse -Force
```

- `Copy-Item`：复制文件或文件夹的命令。
- `-Path D:\*`：表示复制 D 盘下的所有内容（`*` 代表所有文件和文件夹）。
- `-Destination E:\`：目标路径，即 E 盘。
- `-Recurse`：递归复制所有子文件夹及其内容。
- `-Force`：强制覆盖已有文件（不过 E 盘是空的，这个参数不会影响）。

### robocopy：
使用 `robocopy` 命令，它比 `Copy-Item` 更适合大文件复制：

```powershell
robocopy D:\ E:\ /E /COPYALL /R:0 /W:0 /MT
```

- `/E`：复制所有文件和子目录，包括空目录。
- `/COPYALL`：保留所有文件属性（如时间戳、权限）。
- `/R:0`：遇到错误文件时，不进行重试（默认是 1 百万次）。
- `/W:0`：遇到错误文件时，不等待（默认是 30 秒）。
- `/MT`：多线程复制，提高速度。

`robocopy` 复制速度更快，适用于大批量文件迁移。
