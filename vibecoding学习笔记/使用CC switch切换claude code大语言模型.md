### 📥 第一步：下载与安装

`cc-switch` 是一个独立的桌面应用，并非 npm 包[](https://ofox.ai/zh/blog/cc-switch-claude-code-tutorial-2026/#%e5%b0%8f%e7%bb%93)。你需要根据操作系统，从它的 GitHub 发布页面下载对应的安装包。

- **下载地址**：[https://github.com/farion1231/cc-switch/releases](https://github.com/farion1231/cc-switch/releases)[](https://cloud.tencent.com.cn/developer/article/2704792?policyId=1003)
    

**Windows 用户**：

1. 在发布页面中找到最新版本，下载 `CC-Switch-v{version}-Windows.msi` 安装包[](https://developer.aliyun.com/article/1755306#1)。
    
2. 双击 `.msi` 文件，按照提示完成安装[](https://cloud.tencent.com.cn/developer/article/2704792?policyId=1003)。
    
3. **或者**，下载 `CC-Switch-v{version}-Windows-Portable.zip` 便携版，解压后即可直接运行，无需安装[](https://developer.aliyun.com/article/1755306#1)。
### 🚀 第二步：配置 DeepSeek 供应商

安装完成后，就可以启动 `cc-switch` 来配置 DeepSeek 了。

1. **启动应用**：在开始菜单（Windows）、启动台（macOS）或应用程序列表中找到并打开 `CC Switch`。
    
2. **选择工具**：在 CC Switch 界面顶部，确认当前选中的是你想配置的 AI 工具。例如，如果是为了 Claude Code，就确保顶部标签是 `Claude`。
    
3. **添加供应商**：点击界面右上角的 **“Add Provider”**（或“+”号）按钮[](https://cloud.tencent.com.cn/developer/article/2689830?from=15425&frompage=seopage#1#1)。
    ![[Pasted image 20260904184808.png]]
4. **选择 DeepSeek 预设**：在弹出的“添加新供应商”页面中，找到“预设供应商”下拉菜单，选择 **`DeepSeek`**[](https://cloud.tencent.com.cn/developer/article/2689830?from=15425&frompage=seopage#1#1)。`cc-switch` 已经内置了 DeepSeek 的请求地址、默认模型等参数，你不需要手动填写。
    
5. **填入 API Key**：在对应的输入框中，粘贴你之前从 DeepSeek 平台获取的 **API Key**[](https://cloud.tencent.com.cn/developer/article/2689830?from=15425&frompage=seopage#1#1)。
    
6. **保存配置**：检查信息无误后，点击 **“保存”** 按钮[](https://cloud.tencent.com.cn/developer/article/2689830?from=15425&frompage=seopage#1#1)。
![[Pasted image 20260904184734.png]]
### 🔀 第三步：切换与使用

添加完供应商后，就可以在 `cc-switch` 中一键切换了。

- **启用供应商**：在供应商列表中，找到你刚创建的 DeepSeek 配置，点击其卡片上的 **“启用”**（Enable）按钮。
    ![[Pasted image 20260904184826.png]]
- **快速切换**：你还可以右键点击系统托盘（Windows）或菜单栏（macOS）中的 CC Switch 图标，在弹出菜单中直接选择要使用的供应商[](https://cloud.tencent.com.cn/developer/article/2694095#1#1)。
    
- **生效**：切换后，对于 Claude Code，**通常不需要重启终端**，新的配置会立即生效。对于其他工具，可能需要重启一下。
    

之后，当你再启动 Claude Code（或其他你配置的工具）时，它就会使用你通过 `cc-switch` 选中的 DeepSeek 模型来工作了[](https://cloud.tencent.com.cn/developer/article/2694095#1#1)。