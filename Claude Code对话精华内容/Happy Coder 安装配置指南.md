
## 背景

Happy Coder 是一个开源的移动端和 Web 客户端，让你可以在手机上远程控制 Claude Code 和 Codex。支持端到端加密、推送通知、实时语音等功能。

- 官网: https://happy.engineering/
- GitHub: https://github.com/slopus/happy

## 核心内容

### 安装步骤

**1. 下载手机 App**
- iOS: https://apps.apple.com/app/happy-coder/id6736467621
- Android: https://play.google.com/store/apps/details?id=com.happycoder.app

**2. 安装 CLI 工具**

```bash
npm install -g happy-coder
```

**3. 验证安装**

```bash
happy --version
# 输出: happy version: x.x.x
```

### 认证流程

```bash
happy auth login
```

终端会显示二维码，用手机 App 扫描完成配对。

### 启动 Daemon 服务

**关键步骤**：Daemon 是必需的后台服务，用于让手机发现和控制电脑。

```bash
# 启动服务
happy daemon start

# 验证状态
happy daemon status
```

> **重要**：如果手机显示"无设备"，99% 的原因是 daemon 没有运行。

### 使用方法

**启动 Claude Code**

```bash
happy
```

**启动 Codex**

```bash
happy codex
```

**设备切换**
- 手机控制：打开手机 App，点击会话接管
- 电脑控制：按键盘任意键切回电脑

### 常用命令

| 命令 | 说明 |
|------|------|
| `happy` | 启动 Claude Code |
| `happy codex` | 启动 Codex 模式 |
| `happy auth status` | 查看认证状态 |
| `happy daemon start` | 启动 daemon |
| `happy daemon status` | 检查 daemon 状态 |
| `happy --resume` | 继续上次会话 |

## 故障排除

### 手机显示"无设备"

**原因**：daemon 未运行

**解决**：

```bash
happy daemon start
```

然后刷新手机 App。

### 认证失败

```bash
happy auth login --force
```

清除缓存后重新认证。

### Daemon 启动失败

检查端口占用：

```bash
happy daemon status
```

如有问题，重启电脑后再次尝试。

## 额外知识：创建 Skill 时的编码问题

在创建 Claude Code skill 时遇到 Windows 编码问题：

- **UTF-8**：Linux/Mac 默认，Windows 编辑器可能显示乱码
- **UTF-8 with BOM**：Windows 编辑器需要 BOM 标记来识别编码

**解决方案**：使用 Python 的 `utf-8-sig` 编码创建文件

```python
with open(file_path, 'w', encoding='utf-8-sig') as f:
    f.write(content)
```

## 关键要点

1. **Daemon 是关键**：必须启动 `happy daemon`，否则手机无法发现设备
2. **认证 ≠ 连接**：`happy auth login` 只是身份验证，还需要 daemon 运行才能连接
3. **Windows 编码**：创建中文文件时使用 UTF-8 with BOM，否则编辑器会乱码
4. **手机 App 先下载**：安装 CLI 前先在手机上下载 App，方便扫码配对

## 相关

- 项目路径: `D:\`
- 相关工具: Claude Code, Codex, npm
- 相关技术: 端到端加密, WebSocket, Daemon 服务
