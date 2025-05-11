
### 1.Chrome快捷方式后加 `--incognito`

示例
```
"C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe" --incognito
```

### 2\. Win+R 输入regedit打开Windows注册表

找到\\HKEY\_CLASSES\_ROOT\\ChromeHTML\\shell\\open\\command
点开进行编辑

最后参数更改为 `--incognito --single-argument %1`

示例
```
"C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe" --incognito --single-argument %1
```