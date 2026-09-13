### 📝 第一步：安装 Claude Code

你已经完成了这一步，非常顺利。

1. **确认环境**：确保已安装 **Node.js** 和 **Git**。
    
    - Node.js 是运行环境[](https://cloud.tencent.com.cn/developer/article/2676868#1#1)，Git 是依赖工具[](https://cloud.tencent.com.cn/developer/article/2676868#1#1)。
        
    - 可以通过 `node -v` 和 `git --version` 验证。
        
2. **配置 npm 镜像**：为提高国内下载速度，你已执行：
    
    bash
    
    npm config set registry https://registry.npmmirror.com
    
3. **全局安装**：你已执行安装命令：
    
    bash
    
    npm install -g @anthropic-ai/claude-code
    
4. **验证安装**：安装完成后，可以运行以下命令验证：
    
    bash
    
    claude --version
    
    如果能正常显示版本号，说明安装成功[](https://cloud.tencent.com.cn/developer/article/2676868#1#1)。
    

---

### 🔑 第二步：获取 DeepSeek API Key

1. **访问平台**：打开 DeepSeek 开放平台官网：[https://platform.deepseek.com/](https://platform.deepseek.com/)。
    
2. **注册/登录**：完成注册并登录。
    
3. **创建 Key**：在左侧菜单栏找到 **“API Keys”**，点击 **“创建 API Key”**。
    
4. **复制保存**：为 Key 命名后创建，**立即复制并妥善保存**生成的密钥（形如 `sk-...`）。**关闭页面后将无法再次查看**。
    

> **注意**：新注册用户通常有赠送额度，但建议先确认账户有余额，避免调用失败[](https://cloud.tencent.cn/developer/article/2695549#1#1)。
### 🛠️ 第五步（可选）：使用 `cc-switch` 等工具便捷切换
- [[使用CC switch切换claude code大语言模型]]