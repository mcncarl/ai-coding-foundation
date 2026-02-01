## 背景
用户在使用 Claude Code 的 `/ide` 命令连接 Obsidian 时遇到 `onInstall is not defined` 错误，导致无法建立 IDE 集成连接。

## 核心内容

### 问题诊断
- Obsidian MCP 插件目录存在但未在社区插件列表中显示
- MCP 服务器端口（22360、54474）未正常监听
- 旧的 lock 文件指向已不存在的进程

### 解决过程

1. **重新安装插件**
```bash
cd "E:/obsidian/.obsidian/plugins"
git clone https://github.com/iansinnott/obsidian-claude-code-mcp.git
cd obsidian-claude-code-mcp
npm install
npm run build
```

2. **清理过期 lock 文件**
```bash
rm "C:\Users\HP\.claude\ide\60602.lock"
```

3. **在 Obsidian 中启用插件**
   - 设置 → 社区插件 → 启用 "Claude Code MCP"

### 关键发现

- lock 文件以**端口号命名**（如 `54474.lock`），不是固定 ID
- `/ide` 命令需要**交互式终端**才能显示选择菜单
- 即使 `/ide` 无响应，lock 文件存在即表示连接已建立
- **WebSocket 服务器**使用随机端口，通过 lock 文件实现自动发现

## 命令区别

| 命令 | 用途 |
|------|------|
| `/ide` | 连接 IDE（Obsidian/VS Code），建立文件访问能力 |
| `/summary` | 将当前对话精华保存到 Obsidian 笔记 |

## 验证连接

检查 MCP 服务器状态：
```bash
# 检查端口
netstat -ano | grep -E "22360|54474"

# 检查 lock 文件
ls "C:\Users\HP\.claude\ide\"
cat "C:\Users\HP\.claude\ide\*.lock"
```

## 相关
- 插件仓库: iansinnott/obsidian-claude-code-mcp
- Claude Code 版本: 2.1.23
- Obsidian vault: E:\obsidian
