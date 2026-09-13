### 🖥️ 方案一：安装独立IDE（Qoder CN IDE）

如果你想获得开箱即用、功能完整的AI开发工作台，可以直接下载独立IDE[](https://developer.aliyun.com/article/1754778#1)。

- **支持系统**：支持 **Windows 10/11 (x64)**、**macOS 11.0+** 以及 **Linux (x64, .deb/.rpm)**[](https://developer.aliyun.com/article/1754778#1)。
    
- **下载与安装**：访问Qoder CN官方下载页面 [https://qoder.com.cn/download](https://qoder.com.cn/download)[](https://developer.aliyun.com/article/1754778#1)，下载对应操作系统的安装包，然后按提示完成安装即可。
    

### 🔌 方案二：在现有IDE中安装插件

如果你习惯使用现有的开发工具，可以在IDE中安装Qoder插件[](https://developer.aliyun.com/article/1754778#1)。

#### 1. 在 JetBrains IDE 中安装 (如 IntelliJ IDEA, PyCharm 等)

- **支持版本**：支持 **2020.3 及以上版本**的各类JetBrains IDE[](https://developer.aliyun.com/article/1754778#1)。
    
- **安装方法**：
    
    - **从插件市场安装（推荐）**：
        
        1. 打开IDE，进入 `Settings` (Windows/Linux: `Ctrl + Alt + S`，macOS: `⌘ + ,`)。
            
        2. 选择 `Plugins`，在搜索框中输入“**Qoder CN**”[](https://developer.aliyun.com/article/1754778#1)。
            
        3. 找到插件后点击 **Install** 安装。
            
    - **从本地安装包安装（适用于网络受限环境）**：
        
        1. 下载离线安装包：[Qoder CN - JetBrains](https://tongyi-code.oss-cn-hangzhou.aliyuncs.com/jetbrain/tongyi-jetbrains-latest.zip)[](https://developer.aliyun.com/article/1754778#1)。
            
        2. 在IDE的插件管理页面（`Settings > Plugins`），点击齿轮图标⚙️，选择“**Install Plugin from Disk...**”[](https://developer.aliyun.com/article/1754778#1)。
            
        3. 选择刚下载的zip文件并安装。
            

#### 2. 在 Visual Studio Code 中安装

- **安装方法**：打开VS Code，进入扩展面板（快捷键 `Ctrl+Shift+X` 或 `Cmd+Shift+X`），搜索“**Qoder CN**”，找到后点击安装即可。
    

### ⌨️ 方案三：安装命令行工具 (CLI)

如果你需要在无图形界面的服务器（如阿里云ECS）或CI/CD流水线中使用，可以选择CLI工具[](https://developer.aliyun.com/article/1761076)。

- **一键安装脚本 (Linux/macOS)**：  
    在终端中执行以下命令：
    
    bash
    
    curl -fsSL https://install.qoder.cn/install.sh | bash
    
- **通过 pip 安装 (Python)**：  
    Qoder CLI 也可以通过 Python 的包管理工具 pip 进行安装。
    
    bash
    
    pip install qoder-cli
    

### 📦 其他安装方式

- **npm安装**：对于Node.js开发者，可以通过npm全局安装相关命令行工具。
    
    bash
    
    npm install -g qoder-continues
    
- **企业私有化部署**：Qoder CN也支持**VPC私有化部署**，以满足金融、政务等对数据安全有更高要求的场景[](https://developer.aliyun.com/article/1761076)。