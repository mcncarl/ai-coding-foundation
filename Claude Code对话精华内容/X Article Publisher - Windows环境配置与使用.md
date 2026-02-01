---
tags: [对话精华, auto-generated, x-article-publisher, claude-skill]
date: 2026-01-27
created_by: Claude Code
project: x-article-publisher-skill
---

# X Article Publisher - Windows 环境配置与使用

## 背景
用户想使用 Claude Skill 将 Markdown 文章自动发布到 X (Twitter) Premium Articles，但项目原只支持 macOS。需要分析项目并进行 Windows 平台适配。

## 核心内容

### 项目介绍
**x-article-publisher-skill** 是一个自动化工具，将 Markdown 文章完美发布到 X Articles 编辑器，保留所有格式（H1、H2、粗体、链接、列表等）。

**核心原理**：
1. Python 脚本将 Markdown 转换为 HTML 富文本
2. 通过系统剪贴板一次性注入"骨架"到编辑器
3. 使用 Playwright 自动化技术精准插入图片

**使用限制**：
- 需要 X Premium Plus 订阅（只有这个级别才开放 Articles 功能）
- 原项目仅支持 macOS（使用 `pyobjc-framework-Cocoa`）

### Windows 平台适配

#### 问题定位
代码中 `copy_html_to_clipboard_windows` 函数依赖不存在的 `clip-util` 库：
```python
# 原代码（第 173-189 行）
def copy_html_to_clipboard_windows(html: str) -> bool:
    try:
        from clipboard import Clipboard  # 这个库不存在！
        with Clipboard() as clipboard:
            clipboard["html"] = html
```

#### 解决方案
改用 `pywin32` 原生 API 实现 HTML 剪贴板复制：

```python
def copy_html_to_clipboard_windows(html: str) -> bool:
    """Copy HTML to Windows clipboard using pywin32."""
    try:
        import win32clipboard

        # CF_HTML format requires a specific header
        html_prefix = (
            "Version:0.9\r\n"
            "StartHTML:{:010d}\r\n"
            "EndHTML:{:010d}\r\n"
            "StartFragment:{:010d}\r\n"
            "EndFragment:{:010d}\r\n"
            "<html><body><!--StartFragment-->"
        )
        html_suffix = "<!--EndFragment--></body></html>"

        fragment_start = len(html_prefix.encode('utf-8'))
        fragment_end = fragment_start + len(html.encode('utf-8'))
        html_start = 0
        html_end = fragment_end + len(html_suffix.encode('utf-8'))

        html_prefix = html_prefix.format(html_start, html_end, fragment_start, fragment_end)
        cf_html = html_prefix + html + html_suffix

        # Copy to clipboard
        win32clipboard.OpenClipboard()
        try:
            win32clipboard.EmptyClipboard()
            cf_html_format = win32clipboard.RegisterClipboardFormat("HTML Format")
            win32clipboard.SetClipboardData(cf_html_format, cf_html.encode('utf-8'))
            win32clipboard.SetClipboardData(win32clipboard.CF_UNICODETEXT, html)
        finally:
            win32clipboard.CloseClipboard()

        return True
    except Exception as e:
        print(f"Error copying HTML: {e}", file=sys.stderr)
        return False
```

## 安装步骤

### 1. 克隆项目
```bash
git clone https://github.com/wshuyi/x-article-publisher-skill.git
```

### 2. 复制到全局 skills 目录
```powershell
# Windows
Copy-Item -Path "x-article-publisher-skill\skills\x-article-publisher\*" -Destination "$env:USERPROFILE\.claude\skills\x-article-publisher" -Recurse -Force
```

### 3. 安装 Python 依赖
```bash
pip install Pillow pywin32
```

**注意**: 文档中提到的 `clip-util` 库不存在，已修复为使用 `pywin32` 原生 API。

## 使用方式

在 Claude Code 中说：
> 帮我把 `D:\Documents\my-article.md` 发布到 X

或者直接调用 skill：
```
x-article-publisher@wshuyi/x-article-publisher-skill <文章路径>
```

## 关键要点

### CF_HTML 格式
Windows 剪贴板的 HTML 格式需要特定头部，包含版本号和各部分的偏移量：
- `StartHTML/EndHTML`: 整个 HTML 内容的范围
- `StartFragment/EndFragment`: 实际要粘贴的片段范围
- `<!--StartFragment-->` 和 `<!--EndFragment-->`: 标记片段边界

### 项目已支持 Windows
项目代码中已经包含完整的 Windows 实现，只需要：
1. 修复 `copy_html_to_clipboard_windows` 函数（已完成）
2. 安装正确的依赖（`Pillow` + `pywin32`）

### 浏览器自动化问题
遇到 `Browser is already in use` 错误时：
```bash
# 方案1：关闭 Chrome
taskkill /F /IM chrome.exe

# 方案2：重启 MCP 服务器
# 在 Claude Code 中执行 /mcp -> playwright -> Restart
```

## 相关文件

- **项目仓库**: https://github.com/wshuyi/x-article-publisher-skill
- **修复文件**: `skills/x-article-publisher/scripts/copy_to_clipboard.py` (第 173-189 行)
- **安装路径**: `C:\Users\HP\.claude\skills\x-article-publisher`
- **原始文章**: https://x.com/wshuyi/status/2006174274436907389

## 相关技术/工具
- Python: pywin32, Pillow
- Claude Code Skills
- Playwright MCP
- X (Twitter) Premium Plus