
## 背景
用户希望将 Claude Code CLI 的对话精华内容自动保存到 Obsidian 笔记库中，每个对话生成一个新的 Markdown 文档。

## 核心内容

### 需求
- **Obsidian Vault 路径**: `E:\obsidian`
- **目标子文件夹**: `Claude Code对话精华内容`
- **环境**: Claude Code CLI
- **要求**: 每个对话生成一个新的 Markdown 文档

### 架构设计
```
Claude Code CLI
    ↓
/summary skill (自定义命令)
    ↓
Obsidian MCP Server (obsidian-mcp)
    ↓
创建笔记到 E:\obsidian\Claude Code对话精华内容\
```

## 解决方案

### Phase 1: 配置 Obsidian MCP Server
使用 stdio 方式配置 obsidian-mcp：

```bash
claude mcp add --transport stdio obsidian -- npx -y obsidian-mcp "E:\obsidian"
```

配置后需要重启 Claude Code，使用 `/mcp` 验证连接。

### Phase 2: 创建 `/summary` Skill

**Skill 文件位置**: `C:\Users\HP\.claude\skills\summary\SKILL.md`

**核心功能**:
1. 分析对话内容，识别核心主题
2. 提取精华内容（问题、解决方案、代码片段、决策原因）
3. 过滤无关内容（过渡语、调试输出）
4. 调用 MCP 工具创建 Obsidian 笔记

**笔记模板格式**:
```markdown
---
tags: [对话精华, auto-generated]
date: YYYY-MM-DD
created_by: Claude Code
project: <项目名>
---

# <对话主题标题>

## 背景
<简述对话背景和问题>

## 核心内容
<主要讨论内容>

## 解决方案
<代码片段>

## 关键要点
- 要点列表

## 相关
- 项目路径
- 相关技术/工具
```

### Phase 3: MCP 工具调用

**可用工具**:
- `mcp__obsidian__create_directory`: 创建子文件夹
- `mcp__obsidian__create_note`: 创建笔记文件
- `mcp__obsidian__search-vault`: 搜索现有笔记

**调用示例**:
```javascript
// 创建目录
mcp__obsidian__create_directory({
  vault: "obsidian",  // 使用配置名称，不是路径
  path: "Claude Code对话精华内容",
  recursive: true
})

// 创建笔记
mcp__obsidian__create_note({
  vault: "obsidian",
  filename: "对话主题标题.md",
  folder: "Claude Code对话精华内容",
  content: "<Markdown内容>"
})
```

## 关键要点

1. **MCP Vault 参数**: 使用配置时指定的名称（如 "obsidian"），而不是完整路径
2. **重启生效**: 配置 MCP Server 和创建 Skill 后需要重启 Claude Code
3. **触发词**: `/summary`、`保存对话`、`导出精华`、`沉淀到 Obsidian`
4. **文件名**: 从对话中提取核心主题作为文件名，保持简洁有意义

## 相关
- **项目路径**: `D:\`（当前工作目录）
- **配置文件**: `C:\Users\HP\.claude.json`
- **Skill 文件**: `C:\Users\HP\.claude\skills\summary\SKILL.md`
- **相关技术**: Claude Code CLI, MCP Protocol, obsidian-mcp, YAML Frontmatter