### 💻 第一步：安装 Git

#### 1. Windows 系统

1. **下载安装包**：访问 Git 官网下载页面[]([Git - Windows 安装](https://git-scm.com/install/windows))，网站会自动识别系统并提供下载。点击 **“Download for Windows”** 下载 `.exe` 安装包[](https://cloud.tencent.cn/developer/article/2551615?from=15425&frompage=seopage#1#1)。
    
2. **运行安装**：双击下载的安装包，一路点击 “Next”[](https://cloud.tencent.cn/developer/article/2551615?from=15425&frompage=seopage#1#1)。
    
3. **关键配置**：
    
    - **选择安装路径**：建议修改到非系统盘，如 `D:\Git`[](https://developer.aliyun.com/article/1746255#1#1)。
        
    - **选择组件**：保持默认即可[](https://cloud.tencent.cn/developer/article/2551615?from=15425&frompage=seopage#1#1)[](https://developer.aliyun.com/article/1746255#1#1)。
        
    - **选择默认编辑器**：推荐选 **“Use Visual Studio Code as Git‘s default editor”**，对新手更友好[](https://cloud.tencent.cn/developer/article/2551615?from=15425&frompage=seopage#1#1)。
        
    - **选择 PATH 环境变量**：**务必选择 “Git from the command line and also from 3rd-party software”**，这样才能在系统的 CMD 或 PowerShell 中使用 Git[](https://cloud.tencent.cn/developer/article/2551615?from=15425&frompage=seopage#1#1)。
        
    - **其他配置**：对于 HTTPS 传输方式、换行符处理等，**全部保持默认选项**，一路点击 “Next” 即可[](https://cloud.tencent.cn/developer/article/2551615?from=15425&frompage=seopage#1#1)。
        
4. **验证安装**：安装完成后，打开 CMD 或 PowerShell，输入 `git --version`。若显示版本号（如 `git version 2.45.1`），则安装成功[](https://developer.aliyun.com/article/1685916#1)。
### ⚙️ 第二步：初始配置

安装完成后，需要配置你的身份信息，这样每次提交代码时，Git 就知道是谁做的修改。

打开命令行，输入以下命令，将引号内的内容替换为你自己的用户名和邮箱[](https://developer.aliyun.com/article/1685916#1)[](https://developer.aliyun.com/article/1625896#1#1)：

bash

git config --global user.name "你的用户名"
git config --global user.email "你的邮箱@example.com"

> `--global` 参数表示全局配置，对当前电脑上的所有 Git 仓库生效。

---

### 🚀 第三步：基本工作流程

初始化一个项目并完成一次提交，通常遵循以下步骤[](https://developer.aliyun.com/article/1685916#1)：

1. **初始化仓库**：在项目文件夹中，运行 `git init`。
    
2. **添加文件**：将文件添加到暂存区，运行 `git add .`（添加所有文件）或 `git add <文件名>`（添加指定文件）[](https://developer.aliyun.com/article/1685916#1)。
    
3. **提交文件**：将暂存区的修改提交到本地仓库，运行 `git commit -m "提交信息"`[](https://developer.aliyun.com/article/1685916#1)。
    
4. **查看状态**：随时运行 `git status` 查看当前仓库的状态[](https://developer.aliyun.com/article/1685916#1)。
    

---

### 🔗 第四步：连接远程仓库（以 Gitee 为例）

#### 1. 生成并配置 SSH 公钥

这是为了安全地连接远程仓库，避免每次操作都输入密码[](https://developer.aliyun.com/article/1625896#1#1)。

1. **生成密钥**：在命令行中输入（替换邮箱地址）[](https://developer.aliyun.com/article/1625896#1#1)：
    
    bash
    
    ssh-keygen -t rsa -C "你的邮箱@example.com"
    
    然后一路回车，使用默认设置。
    
2. **获取公钥**：运行以下命令，复制输出的全部内容[](https://developer.aliyun.com/article/1625896#1#1)：
    
    bash
    
    cat ~/.ssh/id_rsa.pub
    
3. **添加公钥**：登录 Gitee -> 右上角头像 -> “设置” -> “SSH公钥”[](https://developer.aliyun.com/article/1625896#1#1)。将复制的公钥粘贴进去，点击“确定”[](https://developer.aliyun.com/article/1685916#1)。
    
4. **测试连接**：运行 `ssh -T git@gitee.com`，若出现 “successfully authenticated” 提示，则配置成功[](https://developer.aliyun.com/article/1625896#1#1)。
    

#### 2. 关联本地与远程仓库

1. **在 Gitee 上创建仓库**：登录 Gitee，点击 “+” -> “新建仓库”，填写信息后创建[](https://developer.aliyun.com/article/1625896#1#1)。
    
2. **关联并推送**：在本地项目目录下，运行以下命令（将 `你的用户名` 和 `仓库名` 替换为实际内容）：
    
    bash
    
    git remote add origin git@gitee.com:你的用户名/仓库名.git
    git push -u origin main
    
    执行后，本地代码就推送到了 Gitee 远程仓库。