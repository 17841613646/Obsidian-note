### 2. Memory 记忆管理

### 2.1 三层记忆体系

Claude Code 的记忆系统分为三个层次：

|   |   |   |   |
|---|---|---|---|
|层次|位置|作用域|加载时机|
|**项目级 CLAUDE.md**|`<project>/CLAUDE.md`|当前项目|每次会话启动|
|**项目级 MEMORY.md**|`<project>/.claude/MEMORY.md`|当前项目|会话中可按需检索|
|**用户级 MEMORY.md**|`~/.claude/MEMORY.md`|所有项目|会话中可按需检索|
### 如何管理记忆

- **`/memory` 命令**：在 Claude Code 终端中输入 `/memory`，可以打开一个全屏编辑器，让你查看、编辑或删除所有记忆文件[](https://mintlify.wiki/killlowkey/claude-code/concepts/memory)。
    
- **开关与配置**：Auto Memory 默认开启[](https://cloud.tencent.com.cn/developer/article/2701676?policyId=1004#1#1)。你可以通过以下方式管理它[](https://cloud.tencent.com.cn/developer/article/2701676?policyId=1004#1#1)：
    
    - **`/memory` 命令**：在界面中切换开关。
    - /comtext命令 ：查看当前模型已使用上下文长度大小
    - /compact 命令：总结压缩当前记忆