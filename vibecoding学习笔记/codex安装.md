### ⚙️ 第一步：安装 Codex 插件

1. **打开扩展商店**：在 VS Code 里，点击左侧活动栏上的**扩展**（Extensions）图标，或者使用快捷键 `Ctrl+Shift+X` (Windows/Linux) 或 `Cmd+Shift+X` (Mac)。
    
2. **搜索并安装**：在扩展商店的搜索框中输入 **`Codex`**[](https://raw.githubusercontent.com/freestylefly/CodexGuide/master/docs/start/13-ide-vscode.md)。
    
3. **选择官方插件**：在搜索结果中，找到由 OpenAI 官方发布的插件（通常名为 `ChatGPT` 或类似，描述中会包含 Codex 能力[](https://raw.githubusercontent.com/freestylefly/CodexGuide/master/docs/start/13-ide-vscode.md)），点击**安装**（Install）按钮即可。
    

### 🔑 第二步：登录与认证（二选一）

插件安装完成后，需要登录才能使用。VS Code 插件支持两种认证方式[](https://cloud.tencent.com.cn/developer/article/2712987?policyId=1004#3#1)[](https://cloud.tencent.com.cn/developer/article/2699957#1#1)：

- **方式一：ChatGPT 账号登录（推荐）**  
    这是目前最便捷的方式[](https://cloud.tencent.com.cn/developer/article/2699957#1#1)。点击 VS Code 侧边栏或右上角的 Codex 图标，选择 **“Sign in with ChatGPT”** 或 **“ChatGPT 登录”**，浏览器会打开授权页面，登录你的 **ChatGPT Plus、Pro 或 Business** 账号即可[](https://cloud.tencent.com.cn/developer/article/2699957#1#1)。
    
    > **注意**：免费版 ChatGPT 账号无法使用此功能[](https://cloud.tencent.com.cn/developer/article/2699957#1#1)。
    
- **方式二：API Key 配置**  
    如果你有 OpenAI 的 API Key，也可以在插件设置中选择 **“API Key”** 方式，然后填入你的 API Key[](https://developer.aliyun.com/article/1746128#1)[](https://cloud.tencent.com.cn/developer/article/2699957#1#1)。
    

### 🚀 第三步：开始使用 Codex

登录成功后，就可以在 VS Code 里与 Codex 对话了。

1. **打开对话窗口**：在 VS Code 中打开任意项目文件，点击编辑器**右上角**新出现的 Codex 图标，右侧边栏便会展开对话窗口[](https://raw.githubusercontent.com/freestylefly/CodexGuide/master/docs/start/13-ide-vscode.md)。
    
2. **高效提示（Prompt）技巧**：
    
    - **使用 `@` 指定文件**：在对话框中输入 `@` 并选择项目中的具体文件，可以让 Codex 的分析和修改更精准[](https://raw.githubusercontent.com/freestylefly/CodexGuide/master/docs/start/13-ide-vscode.md)。
        
    - **直接提问**：可以像聊天一样，直接向 Codex 提出需求，例如“解释这段代码”、“修复这个 bug”、“生成一个 React 组件”等[](https://developer.aliyun.com/article/1746128#1)。
        

### 🛠️ 高级玩法：命令行工具 (CLI)

除了 VS Code 插件，Codex 还提供了强大的命令行工具（CLI）。你可以在 VS Code 的集成终端中直接使用 `codex` 命令，执行一些插件界面没有的功能，且它会和插件共享同一套配置和登录状态。

你可以通过以下方式安装 Codex CLI[](https://cloud.tencent.com.cn/developer/article/2712987?policyId=1004#3#1)：

- **官方脚本**（推荐）：`curl -fsSL https://chatgpt.com/codex/install.sh | sh` (Mac/Linux)[](https://cloud.tencent.com.cn/developer/article/2712987?policyId=1004#3#1)
    
- **npm**：`npm install -g @openai/codex`[](https://cloud.tencent.com.cn/developer/article/2712987?policyId=1004#3#1)
    
- **Homebrew** (macOS)：`brew install --cask codex`[](https://cloud.tencent.com.cn/developer/article/2712987?policyId=1004#3#1)
    

安装后，在终端输入 `codex --version` 即可验证是否成功[](https://cloud.tencent.com.cn/developer/article/2712987?policyId=1004#3#1)