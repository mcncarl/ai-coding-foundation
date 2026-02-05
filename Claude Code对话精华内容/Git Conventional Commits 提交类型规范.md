## 背景
需要了解 Git 提交信息中 `fix:`、`feat:` 等前缀的标准规范和含义。

## Conventional Commits 规范

### 核心类型
- **feat**: 新功能（feature）
- **fix**: 修复 bug

### 常用类型
- **docs**: 仅文档修改
- **style**: 代码格式修改（不影响代码运行）
- **refactor**: 重构代码（既不是新功能也不是修复 bug）
- **perf**: 性能优化
- **test**: 添加或修改测试
- **build**: 影响构建系统或外部依赖的修改
- **ci**: 修改 CI 配置文件和脚本
- **chore**: 其他不修改 src 或测试文件的修改
- **revert**: 回滚之前的提交
## 完整格式

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### 示例
```
feat(auth): 添加 OAuth2 登录支持

实现了 Google 和 GitHub 的 OAuth2 认证流程

Closes #123
```

## 关键要点
- **破坏性变更**：在类型后添加 `!`，如 `feat!: 重构 API 接口`
- **作用域**：可选的圆括号内容，指定影响范围
- **优势**：可自动生成 CHANGELOG，支持语义化版本控制，提升提交历史可读性

## 类型选择指南
| 类型 | 使用场景 | 是否影响版本 |
|------|----------|--------------|
| feat | 新增功能 | MINOR |
| fix | 修复问题 | PATCH |
| perf | 性能优化 | PATCH |
| refactor | 代码重构 | - |
| docs | 文档更新 | - |
| style | 格式调整 | - |
| test | 测试相关 | - |
| build/ci | 构建/CI | - |
| chore | 其他杂项 | - |

## 相关
- 标准规范：[Conventional Commits](https://www.conventionalcommits.org/)
- 配合工具：commitizen、commitlint、standard-version
