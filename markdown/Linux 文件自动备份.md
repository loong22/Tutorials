### 1.写保存文件和自动删除久文件的脚本
```
#!/bin/bash

# 设置源文件夹和目标文件夹
SOURCE_FOLDER="/path/to/source_folder"
BACKUP_FOLDER="/path/to/backup_folder"

# 获取当前日期，格式为 YYYY-MM-DD
DATE=$(date +%Y-%m-%d)

# 设置压缩文件名
BACKUP_FILE="backup_$DATE.tar.gz"

# 压缩文件夹
tar -czf "$BACKUP_FOLDER/$BACKUP_FILE" -C "$SOURCE_FOLDER" .

# 删除备份文件夹中超过 5 个的最旧的文件
cd "$BACKUP_FOLDER"
BACKUPS_COUNT=$(ls -1 backup_*.tar.gz | wc -l)

# 如果文件数量超过 5 个，删除最旧的一个
if [ "$BACKUPS_COUNT" -gt 5 ]; then
    # 删除最旧的备份文件
    OLDEST_BACKUP=$(ls -1t backup_*.tar.gz | tail -n 1)
    rm "$OLDEST_BACKUP"
fi
```

### 2. 设置crontab任务自动执行
要先给上面这个脚本添加可执行权限！
```
chmod +x /path/to/backup.sh
```
执行`crontab -e` 添加任务
```
0 2 * * * /bin/bash /path/to/backup.sh
#上面这个表示每天凌晨2点0分执行这个脚本, 可以按需设置不同时间间隔
#分钟0-59 小时0-23 日1-31 月1-12 星期0-6
```