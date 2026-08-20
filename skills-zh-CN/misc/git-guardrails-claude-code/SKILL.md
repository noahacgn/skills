---
name: git-guardrails-claude-code
description: 配置 Claude Code 钩子，在危险 Git 命令（push、reset --hard、clean、branch -D 等）执行前对其进行拦截。当用户希望防止破坏性 Git 操作、添加 Git 安全护栏、或在 Claude Code 中拦截 git push/reset 时使用。
---

# 配置 Git 安全护栏 (Setup Git Guardrails)

配置一个 PreToolUse 钩子，在 Claude 执行危险 Git 命令前对其进行拦截并阻断。

## 将被拦截的操作

- `git push`（所有变体，包括 `--force`）
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

当被拦截时，Claude 会看到一条提示消息，告知其无权执行此类命令。

## 执行步骤

### 1. 询问作用范围

询问用户：仅针对**当前项目**（`.claude/settings.json`）安装，还是针对**全局所有项目**（`~/.claude/settings.json`）安装？

### 2. 复制钩子脚本

内置脚本位于：[scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

根据作用范围复制到目标路径：

- **当前项目**: `.claude/hooks/block-dangerous-git.sh`
- **全局**: `~/.claude/hooks/block-dangerous-git.sh`

通过 `chmod +x` 赋予执行权限。

### 3. 将钩子添加到配置中

添加到相应的设置文件中：

**当前项目** (`.claude/settings.json`):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**全局** (`~/.claude/settings.json`):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

若配置文件已存在，将该钩子合并到现有的 `hooks.PreToolUse` 数组中，切勿覆盖其他既有配置项。

### 4. 询问定制化需求

询问用户是否需要向拦截清单中添加或移除任何命令模式。据此编辑复制后的脚本。

### 5. 验证生效

执行一次快速测试：

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

应当以退出码 2 退出，并在 stderr 上打印 BLOCKED 拦截消息。
