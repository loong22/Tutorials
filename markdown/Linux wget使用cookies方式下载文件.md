### 1. 手动获取 Cookie 并在 wget 中使用
通过浏览器手动通过人机验证，获取有效的 Cookie，然后在 wget 中使用这些 Cookie。​

步骤：

#### 1.1 在浏览器中访问目标网站，完成所需的人机验证。​

#### 1.2 使用浏览器的开发者工具查看并导出当前会话的 Cookie

#### 1.3 将导出的 Cookie 保存为文本文件，例如 cookies.txt
可以使用浏览器拓展保存，或者手动复制为json格式

### 2. 使用浏览器扩展程序导出 Cookies
为方便地导出 Cookies，您可以使用第三方浏览器扩展程序。以下是使用“Cookie Editor”扩展程序的步骤：​

#### 2.1 安装扩展程序：​访问 Chrome 网上应用店，搜索并安装“Cookie Editor”扩展程序
#### 2.2 打开扩展程序：​在浏览器右上角，点击“Cookie Editor”图标  
#### 2.3 导出 Cookies：​在扩展程序界面中，点击“Export”按钮，将 Cookies 导出为 JSON 格式文件  

注意：​使用第三方扩展程序时，请确保其来源可靠，以保护您的隐私和数据安全。​

### 2. 在执行 wget 时，使用 `--load-cookies` 选项加载这些 Cookie：​

```
wget --load-cookies ./cookies.txt https://example.com/file.zip
```
注意： 这种方法的有效性取决于 Cookie 的有效期和目标网站的验证机制，可能需要定期更新 Cookie。
