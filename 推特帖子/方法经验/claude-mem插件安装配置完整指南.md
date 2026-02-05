## 背景

用户需要一个能够搜索历史对话记录的工具，用于聊天场景（非项目开发）。已经在使用 Obsidian 做知识管理，需要与之配合使用。

**核心需求**：
- 搜索很久之前的对话记录
- 自动化记录，无需手动整理
- 与 Obsidian 配合使用

## 解决方案：claude-mem 插件

claude-mem 是 Claude Code 的持久化记忆系统插件，可以自动记录所有对话并提供搜索功能。

### 插件信息
- **版本**：v9.0.12
- **安装位置**：`C:\Users\HP\.claude\plugins\marketplaces\thedotmack\claude-mem\`
- **数据目录**：`C:\Users\HP\.claude-mem\`
- **Web 界面**：http://localhost:37777

## 安装步骤

### 1. 启用插件

编辑 `C:\Users\HP\.claude\settings.json`，添加插件启用配置：

```json
{
  "enabledPlugins": {
    "glm-plan-usage@zai-coding-plugins": true,
    "glm-plan-bug@zai-coding-plugins": true,
    "claude-mem@thedotmack": true
  }
}
```

### 2. 安装依赖和构建

```bash
# 安装项目依赖
cd C:/Users/HP/.claude/plugins/marketplaces/thedotmack/claude-mem
npm install

# 构建插件
npm run build
```

**构建产物**：
- `plugin/scripts/worker-service.cjs` - Worker 服务
- `plugin/scripts/mcp-server.cjs` - MCP 搜索服务器
- `plugin/ui/viewer-bundle.js` - React Web 界面

### 3. 启动 Worker 服务

```bash
cd C:/Users/HP/.claude/plugins/marketplaces/thedotmack/claude-mem/plugin
bun scripts/worker-service.cjs run
```

**验证服务**：
```bash
curl http://localhost:37777/health
# 预期输出：{"status":"ok","timestamp":...}
```

### 4. 配置优化（针对聊天场景）

编辑 `C:\Users\HP\.claude-mem\settings.json`：

```json
{
  "CLAUDE_MEM_CONTEXT_OBSERVATIONS": "100",
  "CLAUDE_MEM_CONTEXT_SESSION_COUNT": "20",
  "CLAUDE_MEM_MODE": "code--zh",
  "CLAUDE_MEM_LOG_LEVEL": "INFO"
}
```

**配置说明**：
- `CONTEXT_OBSERVATIONS: 100` - 上下文观察数（默认 50 → 100）
- `CONTEXT_SESSION_COUNT: 20` - 记忆会话数（默认 10 → 20）
- `MODE: code--zh` - 中文模式，支持中文摘要

## 遇到的问题与解决

### 问题 1：Worker 服务启动失败

**错误**：`Failed to spawn worker daemon`

**原因**：项目依赖未安装，构建产物缺失

**解决**：
```bash
# 1. 安装根目录依赖（235个包）
npm install

# 2. 构建插件
npm run build

# 3. 启动 Worker
bun scripts/worker-service.cjs run
```

### 问题 2：Web 界面显示空白

**原因**：不是界面问题，而是数据库为空（observations: 0, sessions: 0）

**分析**：
- 刚安装的插件还没有历史数据
- 钩子需要在特定条件下触发（SessionStart, UserPromptSubmit 等）
- 当前 Plan Mode 会话可能不触发钩子

**解决**：重启 Claude Code 开始新会话，钩子会自动触发并开始记录

### 问题 3：能否搜索历史对话？

**答案**：不能

**原因**：
- claude-mem 只记录从插件启用后的对话
- 不会回溯扫描历史 transcript 文件
- 历史对话继续用 `/resume` 查看

**解决方案**：
| 需求 | 工具 | 方法 |
|------|------|------|
| 搜索历史对话 | `/resume` | Claude Code 内置 |
| 搜索新对话 | claude-mem | 自然语言搜索 |

## 使用方法

### 自动化工作（推荐）

1. **启动会话** → Worker 自动启动（如未运行）
2. **正常对话** → 所有内容自动记录
3. **工具执行** → 自动捕获观察
4. **会话结束** → 自动生成摘要

**无需任何手动操作！**

### 自然语言搜索

直接在 Claude Code 中询问：
```
"我们上次聊了什么？"
"关于 XXX 的讨论内容"
"最近一周的对话记录"
```

### Web 可视化界面

访问 http://localhost:37777

**功能**：
- 📜 Memory Stream - 查看所有对话历史
- 🔍 Search - 搜索特定内容
- ⚙️ Settings - 调整配置
- 📊 Stats - 查看统计数据

### 隐私控制

使用 `<private>` 标签排除敏感信息：
```
<private>
这里的内容不会被存储
API 密钥：sk-xxx
</private>
```

## 启动脚本（便捷使用）

已创建快捷脚本：

**启动 Worker**：`C:\Users\HP\.claude-mem\start-worker.bat`
```batch
@echo off
cd /d "C:\Users\HP\.claude\plugins\marketplaces\thedotmack\claude-mem\plugin"
bun scripts\worker-service.cjs run
```

**停止 Worker**：`C:\Users\HP\.claude-mem\stop-worker.bat`

## 与 Obsidian 配合

### 推荐工作流

| 工具 | 用途 | 特点 |
|------|------|------|
| **claude-mem** | 自动记录所有对话 | 快速搜索、自动化 |
| **Obsidian + /summary** | 手动保存重要内容 | 结构化、长期沉淀 |

### 最佳实践

1. **日常对话** → claude-mem 自动捕获
2. **重要结论** → `/summary` 到 Obsidian
3. **快速查找** → 直接问 Claude
4. **深度回顾** → 在 Obsidian 查看笔记

**两者互补，各司其职！**

## 插件工作原理

### 钩子系统

claude-mem 通过 5 个生命周期钩子捕获对话：

1. **SessionStart** - 会话启动（匹配 startup|clear|compact）
   - 安装依赖（smart-install.js）
   - 启动 Worker 服务
   - 注入历史上下文

2. **UserPromptSubmit** - 用户提交提示
   - 初始化会话
   - 启动 Worker（如未运行）

3. **PostToolUse** - 工具使用后
   - 捕获工具执行观察
   - 保存到数据库

4. **Stop** - Claude 响应完成
   - 生成会话摘要
   - 压缩观察结果

5. **SessionEnd** - 会话结束
   - 清理资源

### 数据存储

**SQLite 数据库**：`C:\Users\HP\.claude-mem\claude-mem.db`

**数据表**：
- `sdk_sessions` - 会话记录
- `session_summaries` - 会话摘要
- `observations` - 观察数据（工具使用等）
- `user_prompts` - 用户提示

### MCP 工具

插件注册 3 个 MCP 工具供 Claude 调用：

1. **search** - 全文搜索记忆索引
2. **timeline** - 获取时间线上下文
3. **get_observations** - 批量获取观察详情

## 关键文件路径

### 配置文件
- `C:\Users\HP\.claude\settings.json` - Claude Code 主配置
- `C:\Users\HP\.claude-mem\settings.json` - 插件运行配置

### 数据文件
- `C:\Users\HP\.claude-mem\claude-mem.db` - SQLite 数据库
- `C:\Users\HP\.claude-mem\logs\` - 日志目录
- `C:\Users\HP\.claude-mem\worker.pid` - Worker 进程信息

### 插件文件
- `C:\Users\HP\.claude\plugins\marketplaces\thedotmack\claude-mem\plugin\` - 插件主体
- `plugin\hooks\hooks.json` - 钩子配置
- `plugin\.mcp.json` - MCP 配置
- `plugin\scripts\worker-service.cjs` - Worker 服务脚本

### 文档
- `C:\Users\HP\.claude-mem\使用指南.md` - 详细使用文档
- `C:\Users\HP\.claude-mem\README.txt` - 快速参考

## 验证清单

安装完成后检查：

- [x] `settings.json` 中添加了 `"claude-mem@thedotmack": true`
- [x] `C:\Users\HP\.claude-mem\` 目录已创建
- [x] `claude-mem.db` 文件存在
- [x] `curl http://localhost:37777/health` 返回 `{"status":"ok"}`
- [x] 浏览器可以访问 http://localhost:37777
- [x] GLM 插件仍然正常工作

## 故障排除

### Worker 未启动

**检查**：
```bash
curl http://localhost:37777/health
```

**解决**：
```bash
# 手动启动
cd C:\Users\HP\.claude\plugins\marketplaces\thedotmack\claude-mem\plugin
bun scripts\worker-service.cjs run
```

### 端口 37777 被占用

**编辑配置**：`C:\Users\HP\.claude-mem\settings.json`
```json
{
  "CLAUDE_MEM_WORKER_PORT": "38000"
}
```

### 数据库未创建

**原因**：插件未正确启用或钩子未触发

**解决**：
1. 确认 `settings.json` 配置正确
2. 重启 Claude Code
3. 检查日志：`C:\Users\HP\.claude-mem\logs\`

## 关键要点

- claude-mem 是**实时捕获**系统，不会回溯历史
- **从现在开始**记录所有对话和工具使用
- 提供**自然语言搜索**，比手动查找更便捷
- **自动化运行**，无需手动操作
- **Web 界面**提供可视化查看和搜索
- 与 **Obsidian 互补**：一个自动全记录，一个手动精整理
- **中文友好**：支持中文模式和中文摘要

## 相关

- 项目路径：`C:\Users\HP\.claude\plugins\marketplaces\thedotmack\claude-mem\`
- 数据目录：`C:\Users\HP\.claude-mem\`
- 工作目录：`D:\`
- 相关技术：
  - Bun - JavaScript 运行时
  - SQLite - 数据库
  - React - Web 界面
  - MCP (Model Context Protocol) - 工具协议
  - Node.js, TypeScript
- 相关工具：
  - Claude Code - CLI 工具
  - Obsidian - 知识管理
  - `/resume` - 查看历史对话
  - `/summary` - 保存精华到 Obsidian
