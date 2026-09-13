### 为什么要查帮助

![[Pasted image 20260912215257.png]]

### 快速上升： --help选项
tar（命令） --help（选项）

![[Pasted image 20260912215330.png]]

### man命令权威百科全书

![[Pasted image 20260912215446.png]]

### 现代效率神器：tldr
全中查阅帮助
![[Pasted image 20260912215530.png]]

![[Pasted image 20260912215548.png]]

### tldr实战：tar命令对比

![[Pasted image 20260912215705.png]]

### 总结

![[Pasted image 20260912215903.png]]

### tldr帮助命令安装

### 方法一：通过 npm 安装（推荐）

如果你的系统里还没有 Node.js，需要先安装它。

**1. 安装 Node.js 和 npm**  
在终端执行以下命令，这会从 Rocky Linux 的官方仓库中安装 Node.js 和 npm[](https://tw.console-linux.com/?p=227)[](https://www.tecmint.com/tldr-easy-to-understand-linux-man-pages/#1)：

bash

sudo dnf install -y nodejs npm

**2. 使用 npm 全局安装 tldr**  
安装完成后，用 npm 的全局安装命令来安装 `tldr`[](https://tw.console-linux.com/?p=227)[](https://www.tecmint.com/tldr-easy-to-understand-linux-man-pages/#1)：

bash

sudo npm install -g tldr

### 方法二：通过 pip 安装（Python 方式）

Rocky Linux 9 默认可能没有安装 Python 的 pip 工具，需要先配置。

**1. 安装 python3-pip**

bash

sudo dnf install -y python3-pip

**2. （可选但推荐）配置国内 pip 源**  
为了加快下载速度，可以将 pip 源设置为清华源[](https://www.bravexist.cn/posts/75a0f705.html#post-comment)：

bash

python3 -m pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

**3. 安装 tldr**

bash

python3 -m pip install tldr

### 安装后的必要步骤：更新缓存

`tldr` 的说明内容来自一个在线仓库，安装后需要先更新本地缓存才能使用。

**1. 更新缓存**

bash

tldr -u

或者使用完整命令 `tldr --update`[](https://tw.console-linux.com/?p=227)。

**2. 测试使用**  
更新完成后，就可以用它来查看命令的简洁示例了。例如查看 `ls` 命令：

bash

tldr ls

### 设置中文显示（可选）

如果你希望 `tldr` 显示中文说明，可以设置环境变量。编辑你的 `~/.bashrc` 文件，添加以下行：

bash

export TLDR_LANGUAGE="zh"

然后执行 `source ~/.bashrc` 使其生效。之后再次运行 `tldr ls` 就会显示中文内容。

### 补充说明

如果后续想卸载，可以根据你的安装方式选择对应命令：

- **npm 安装**：`sudo npm uninstall -g tldr`
    
- **pip 安装**：`python3 -m pip uninstall tldr`