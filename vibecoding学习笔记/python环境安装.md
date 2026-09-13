### 💻 Windows 系统安装步骤

这是最常用的方式，按以下步骤操作即可：

1. **下载安装包**：访问 Python 官网 [https://www.python.org/downloads/](https://www.python.org/downloads/)，网站通常会自动识别你的系统并推荐下载按钮[](https://developer.aliyun.com/article/1703955)[](https://bbs.huaweicloud.com/blogs/483727#1)。点击黄色的 **“Download Python xxx”** 按钮，下载 `.exe` 安装文件[](https://bbs.huaweicloud.com/blogs/483727#1)。
    
2. **运行安装程序**：找到下载好的 `.exe` 文件，**双击运行**[](https://bbs.huaweicloud.com/blogs/483727#1)。
    
3. **勾选关键选项 (最重要一步)**：在安装窗口的最下方，**务必勾选“Add Python to PATH”**[](https://bbs.huaweicloud.com/blogs/483727#1)。这能让你在命令行中直接使用 `python` 命令[](https://bbs.huaweicloud.com/blogs/483727#1)。勾选后，点击 **“Install Now”** 开始安装[](https://bbs.huaweicloud.com/blogs/483727#1)。
    
4. **等待安装完成**：等待进度条走完，看到 “Setup was successful” 的提示后，点击 **“Close”** 关闭窗口即可[](https://bbs.huaweicloud.com/blogs/483727#1)。
### ✅ 验证安装是否成功

无论用哪种系统，安装完成后，都可以通过命令行来验证：

1. **打开命令行工具**：
    
    - **Windows**：按 `Win + R`，输入 `cmd`，回车[](https://bbs.huaweicloud.com/blogs/483727#1)。
        
    - **macOS / Linux**：打开“终端”应用。
        
2. **输入以下命令并回车**：
    
    bash
    
    python --version
    # 或者 python3 --version (macOS / Linux)
    
3. **检查输出**：如果看到类似 `Python 3.12.x` 的版本信息，就说明安装成功了[](https://developer.aliyun.com/article/1703955)。
    

### ⚙️ 环境变量（PATH）手动配置

如果在安装时**忘记勾选“Add Python to PATH”**，在命令行输入 `python` 会报错[](https://bbs.huaweicloud.com/blogs/483727#1)。此时可以手动配置：

1. 右键点击“**此电脑**” → “**属性**”。
    
2. 选择“**高级系统设置**” → “**环境变量**”。
    
3. 在“**系统变量**”列表中找到 `Path` 变量，双击它。
    
4. 点击“**新建**”，分别添加以下两条路径（请将 `你的用户名` 和 `Python312` 替换成你电脑上的实际路径）[](https://bbs.huaweicloud.com/blogs/483727#1)：
    
    - `C:\Users\你的用户名\AppData\Local\Programs\Python\Python312\`
        
    - `C:\Users\你的用户名\AppData\Local\Programs\Python\Python312\Scripts\`
        
5. 点击“确定”保存所有窗口。
#### ✅ 方案一：使用 `python -m pip`（立即生效，推荐）

这是**最快捷**的办法，可以绕过环境变量设置，立即使用 `pip`。

在命令行中，不要直接输入 `pip`，而是输入以下命令：

bash

python -m pip --version

如果这个命令能正常显示版本号，就说明 `pip` 本身是存在的，只是它的命令路径没有被系统识别。**以后你想安装包时，都可以用 `python -m pip install 包名` 来代替 `pip install 包名`。**