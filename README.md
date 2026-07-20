# Claude Code 动态偏好记忆系统

让 Claude Code 自动从对话中识别你的偏好，分级存档到不同记忆层里。用三次就回不去了。

## 解决什么问题

Claude Code 的上下文窗口本质上是一块临时缓存。聊几轮之后，前面的对话就被挤出去了。CLAUDE.md 本来是持久化记忆的方案，但大部分人不知道该往里写什么。

这套系统让 Claude **自动**帮你写 CLAUDE.md——从你的对话行为中识别偏好，写进长期记忆。

## 核心机制

### 三类偏好识别

`/commit end` 结档时，自动从对话中识别三类偏好：

| 类型 | 说明 | 例子 |
|------|------|------|
| 明确规则 | 你亲口定下的指令 | "图片只用 docx 嵌入" |
| 行为归纳 | 你在修改中反复表现出的倾向 | 每次都删 emoji → "不喜欢 emoji" |
| 场景化要求 | 只在特定任务类型下生效的规则 | "竞品调研必须每家配截图" |

### 三级存档体系

| 层级 | 触发 | 存什么 | 持久性 |
|------|------|--------|--------|
| Session Log | `/commit` | `[时间] DONE 描述 → 产出物` | 追加记录 |
| /commit 存档 | `/commit` | 任务进度、决策、待办、续做入口 | 项目级文件 |
| CLAUDE.md 偏好 | `/commit end` | 三类偏好 → Long-term Preferences | 跨项目永久 |

## 安装

前置依赖：[Claude Code](https://docs.anthropic.com/en/docs/claude-code) 已安装（`npm install -g @anthropic-ai/claude-code`）

```bash
# 把 commands 目录下的文件复制到 Claude Code 的命令目录
mkdir -p ~/.claude/commands/ && cp commands/*.md ~/.claude/commands/

# 如果还没有 CLAUDE.md，用模板初始化
cp CLAUDE.md.template ~/.claude/CLAUDE.md
```

不需要重启 Claude Code，复制完立刻可用。

## 使用

```
# 聊到一半要走了，阶段性保存
/commit

# 任务做完了，结档 + 写入偏好记忆
/commit end

# 上下文快满了，存档后开新会话
/reset
```

## 文件说明

```
commands/
  commit.md     # 核心 skill：存档 + 偏好识别 + 写入 CLAUDE.md
  reset.md      # 快捷方式：存档 + 提示开新会话
CLAUDE.md.template  # CLAUDE.md 模板（含记忆分区结构）
```

## 工作原理

1. 你正常使用 Claude Code 完成任务
2. 任务完成时输入 `/commit end`
3. Claude 回顾整段对话，识别你的偏好（包括你没明说的那些）
4. 把偏好写进 `~/.claude/CLAUDE.md` 的 Long-term Preferences 区
5. 下次开新会话，Claude 自动加载 CLAUDE.md，带着你的偏好开始工作

越用越懂你。

## License

MIT
