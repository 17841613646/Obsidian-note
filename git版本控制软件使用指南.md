Git 是分布式版本控制工具，核心流程是：

**工作区 → `git add` → 暂存区 → `git commit` → 本地仓库 → `git push` → 远程仓库**

下面是一个快速入门。

## 1. 安装与配置

git --version
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
git config --global init.defaultBranch main

查看配置：

git config --list

## 2. 创建或克隆仓库

### 新建本地仓库

bash

mkdir myproject
cd myproject
git init

### 克隆远程仓库

bash

git clone https://github.com/用户名/仓库名.git
cd 仓库名

## 3. 日常基本流程

bash

git status                  # 查看状态
git add .                   # 添加所有修改到暂存区
git add 文件名              # 添加指定文件
git commit -m "提交说明"    # 提交到本地仓库
git pull                    # 拉取远程最新代码
git push                    # 推送到远程仓库

首次推送：

bash

git remote add origin 仓库地址
git push -u origin main

## 4. 常用查看命令

bash

git status                  # 当前状态
git diff                    # 查看未暂存修改
git diff --staged           # 查看已暂存修改
git log                     # 提交历史
git log --oneline --graph --all   # 简洁图形化历史
git remote -v               # 查看远程仓库

## 5. 分支操作

bash

git branch                  # 查看本地分支
git branch -a               # 查看所有分支
git switch -c feature/login # 新建并切换分支
git switch main             # 切换回 main
git merge feature/login     # 合并分支到当前分支
git branch -d feature/login # 删除已合并分支

旧版本 Git 也可以用：

bash

git checkout -b feature/login
git checkout main

## 6. 撤销与回退

bash

git restore 文件名              # 撤销工作区修改
git restore --staged 文件名     # 取消暂存
git reset --soft HEAD~1         # 撤销最近一次 commit，保留修改
git reset --hard HEAD~1         # 撤销最近一次 commit，丢弃修改，慎用
git revert 提交ID               # 生成一个反向提交，安全回退

修改最近一次提交说明：

bash

git commit --amend

临时保存修改：

bash

git stash
git stash pop

## 7. 解决冲突

当 `git pull` 或 `git merge` 出现冲突时：

1. 打开冲突文件，找到 `<<<<<<<`、`=======`、`>>>>>>>` 标记。
    
2. 手动保留需要的代码。
    
3. 然后执行：
    

bash

git add 冲突文件
git commit -m "解决冲突"

如果是 `rebase` 冲突：

bash

git add 冲突文件
git rebase --continue

## 8. `.gitignore` 忽略文件

在项目根目录创建 `.gitignore`：

gitignore

node_modules/
dist/
.env
*.log
.DS_Store

已经提交过的文件需要先移除缓存：

bash

git rm --cached 文件名

## 9. 典型工作流示例

bash

# 1. 克隆项目
git clone 仓库地址
cd 项目目录
# 2. 新建功能分支
git switch -c feature/xxx
# 3. 修改代码
git status
git add .
git commit -m "feat: 完成xxx功能"
# 4. 推送到远程
git push -u origin feature/xxx
# 5. 在 GitHub/GitLab 上发起 Pull Request / Merge Request

## 10. 注意事项

- 提交前先 `git pull`，减少冲突。
    
- 不要提交密码、密钥、`.env` 等敏感文件。
    
- `git reset --hard` 会丢失未提交修改，慎用。
    
- 提交信息写清楚，例如：`feat: 添加登录功能`、`fix: 修复支付 bug`。
    
- `git fetch` 只拉取不合并，`git pull = git fetch + git merge`。
    

如果你有具体场景，比如“回退到某个版本”“合并分支冲突”“推送到 GitHub”，我可以给你对应命令。

本回答由 AI 生成，内容仅供参考，请仔细甄别