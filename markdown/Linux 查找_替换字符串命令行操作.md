### 使用 `grep` 查找包含 `PPP` 的所有文件

要查看当前目录及其所有子目录下的文件内容是否包含 `PPP`，你可以使用 `grep` 命令，并结合 `-r`（递归）选项来搜索子目录中的所有文件。

```bash
grep -r 'PPP' .
```

- `-r` 或 `--recursive`：递归地搜索当前目录及其所有子目录。
- `'PPP'`：要查找的字符串。
- `.`：当前目录。

如果你希望输出更为详细的信息，可以添加 `-n`，它会显示匹配行的行号。

```bash
grep -rn 'PPP' .
```

- `-n`：显示匹配的行号。

### 替换文件中的 `PPP` 为其他字符

```
#只查找后缀为.cpp和.h的文件
find . -type f \( -name "*.cpp" -o -name "*.h" \) -exec sed -i 's/PPP/FFF/g' {} +

#备份原始文件
find . -type f \( -name "*.cpp" -o -name "*.h" \) -exec sed -i.bak 's/PPP/FFF/g' {} +


#查找所有普通文件
find . -type f -exec sed -i 's/PPP/FFF/g' {} +

#不修改二进制文件
find . -type f -exec grep -Iq . {} \; -exec sed -i 's/PPP/FFF/g' {} +
```

### 输出字符串所在的文件夹，行数，内容
```
grep -rnw NDIM ./ | awk -F: '{print "File:", $1, "Line:", $2, "Content:", $3}'
```
