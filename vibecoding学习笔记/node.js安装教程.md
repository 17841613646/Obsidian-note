---
aliases:
  - node
---


### ⚙️ 第一步：访问官网，下载安装包

1. **打开官网**：在浏览器中访问 Node.js 官方网站：[https://nodejs.org/](https://nodejs.org/)
2. **选择版本**：官网通常会推荐两个版本[](https://grapecity.csdn.net/68662570080e555a88cc9767.html)：
    
    - **LTS（长期支持版）**：**推荐绝大多数用户下载**。这个版本更稳定、安全，适合学习和生产环境[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)[](https://cloud.tencent.com.cn/developer/article/2723884?from=15425&frompage=seopage#1)。       
3. **开始下载**：点击醒目的 **LTS** 按钮，网站会自动识别你的操作系统并开始下载对应的安装包[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)。Windows 用户通常会下载到一个 `.msi` 文件。
    

### 💻 第二步：运行安装程序 (以 Windows 为例)

1. **启动安装**：双击下载好的 `.msi` 文件，开始安装[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)。
    
2. **同意协议**：勾选 “**I accept the terms in the License Agreement**”，然后点击 `Next`[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)[](https://grapecity.csdn.net/68662570080e555a88cc9767.html)。
    
3. **选择路径 (可选)**：你可以选择安装路径。如果不想占用 C 盘空间，可以点击 `Change...` 修改到其他盘（如 `D:\DevTools\nodejs\`）[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)。**注意：路径中不要包含中文或空格**[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)。
    
4. **保持默认组件**：在 `Custom Setup` 界面，保持默认勾选即可。**务必确保 `npm package manager` 和 `Add to PATH` 选项被勾选**，这能自动配置环境变量[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)。
    
5. **安装工具 (可选)**：如果弹出 “**Tools for Native Modules**” 窗口，询问是否安装 Python 和 Visual Studio Build Tools[](https://cloud.tencent.com.cn/developer/article/2723884?from=15425&frompage=seopage#1)[](https://grapecity.csdn.net/68662570080e555a88cc9767.html)。
    
    - 对于**大多数初学者**，可以先**不勾选**，直接点击 `Next` 跳过，这不会影响你使用 Node.js[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)[](https://cloud.tencent.com.cn/developer/article/2723884?from=15425&frompage=seopage#1)。
        
    - 如果你之后需要用到需要编译原生代码的 npm 包（例如 `node-sass`），可以再回来安装[](https://cloud.tencent.com.cn/developer/article/2723884?from=15425&frompage=seopage#1)。
        
6. **完成安装**：点击 `Install` 开始安装，等待进度条走完，最后点击 `Finish` 完成[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)[](https://grapecity.csdn.net/68662570080e555a88cc9767.html)。
    

### ✅ 第三步：验证安装是否成功

1. **打开命令行**：按下键盘的 `Win + R` 键，输入 `cmd`，然后回车，打开命令提示符[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)[](https://cloud.tencent.cn/developer/article/2654766?policyId=1004#1)。
    
2. **检查版本**：在命令行中分别输入以下两个命令并回车：
    
    - `node -v`[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)
        
    - `npm -v`[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)
        
3. **判断结果**：如果命令行正确显示了 Node.js 和 npm 的版本号（例如 `v20.11.0` 和 `10.2.4`），就说明安装成功了！[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)[](https://grapecity.csdn.net/68662570080e555a88cc9767.html)
    
    - **如果提示“不是内部或外部命令”**：说明环境变量可能没配置好。可以尝试**重启一下命令行窗口**，或者检查安装时是否勾选了 `Add to PATH`[](https://cloud.tencent.com.cn/developer/article/2644554?from=15425&policyId=undefined&traceId=&frompage=seopage#1)。
### ✅ 第四步：修改 PowerShell 执行策略
1. **以管理员身份打开 PowerShell**：
    
    - 在 Windows 搜索框输入 `PowerShell`。
        
    - 在搜索结果中右键点击 “Windows PowerShell”，选择 **“以管理员身份运行”**。
        
2. **执行修改命令**：
    
    - 在打开的 PowerShell 窗口中，输入以下命令并回车：
        
        powershell
        
        Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
        
    - 系统会提示确认更改，输入 `Y` 或 `A` 并回车[](https://no8gs.blog.csdn.net/article/details/160375257)。
        
3. **验证并重试**：
    
    - 关闭当前的 PowerShell 窗口，重新打开一个普通的 PowerShell（不需要管理员）。
        
    - 再次输入 `npm -v`，应该就能正常显示版本号了。