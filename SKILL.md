---
name: notebooklm
description: 查询并上传到 Google NotebookLM。创建新笔记本，上传本地文件（PDF/MD/TXT），添加 URL，粘贴文本内容。浏览器自动化支持持久身份验证。
---

# NotebookLM 研究助手技能

**完整的 NotebookLM 自动化**：查询笔记本并将本地文件/笔记上传到 NotebookLM。

## 核心功能

| 功能 | 脚本 | 描述 |
|------------|--------|-------------|
| **上传文件** | `upload_sources.py upload` | 上传 PDF、MD、TXT、DOCX 文件到 NotebookLM |
| **创建笔记本** | `upload_sources.py create` | 创建新的空笔记本 |
| **添加 URL** | `upload_sources.py add-urls` | 添加网站/YouTube 作为来源 |
| **添加文本** | `upload_sources.py add-text` | 粘贴文本内容作为来源 |
| **提问** | `ask_question.py` | 使用 Gemini 查询笔记本 |
| **管理库** | `notebook_manager.py` | 保存/组织笔记本引用 |

## 何时使用此技能

在以下情况下触发：
- 用户明确提到 NotebookLM
- 用户分享 NotebookLM URL (`https://notebooklm.google.com/notebook/...`)
- 用户要求查询他们的笔记本/文档
- 用户想要将文档添加到 NotebookLM 库
- 用户使用"查询我的 NotebookLM"、"检查我的文档"、"查询我的笔记本"等短语
- **用户想要上传本地文件/笔记到 NotebookLM**
- **用户要求创建新的 NotebookLM 笔记本**
- **用户使用"上传到 NotebookLM"、"添加我的文件到笔记本"、"同步我的笔记"等短语**

## 快速参考：上传命令

```bash
# 创建新笔记本
python scripts/run.py upload_sources.py create --name "我的项目文档"

# 上传本地文件到笔记本
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --create-notebook "新笔记本"
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --notebook-url "https://notebooklm.google.com/notebook/..."

# 从目录批量上传
python scripts/run.py upload_sources.py upload-dir --directory "/path/to/docs" --extensions "pdf,md,txt" --create-notebook "文档"

# 添加 URL
python scripts/run.py upload_sources.py add-urls --urls "https://example.com" --notebook-url "..."

# 添加文本内容
python scripts/run.py upload_sources.py add-text --text "您的内容..." --notebook-url "..."
python scripts/run.py upload_sources.py add-text --file "/path/to/content.txt" --notebook-url "..."
```

## 重要：添加命令 - 智能发现

当用户想要添加笔记本但未提供详细信息时：

**智能添加（推荐）**：首先查询笔记本以发现其内容：
```bash
# 步骤 1：查询笔记本关于其内容
python scripts/run.py ask_question.py --question "这个笔记本的内容是什么？涵盖哪些主题？请简要完整地概述" --notebook-url "[URL]"

# 步骤 2：使用发现的信息添加它
python scripts/run.py notebook_manager.py add --url "[URL]" --name "[基于内容]" --description "[基于内容]" --topics "[基于内容]"
```

**手动添加**：如果用户提供所有详细信息：
- `--url` - NotebookLM URL
- `--name` - 描述性名称
- `--description` - 笔记本包含的内容（必需！）
- `--topics` - 逗号分隔的主题（必需！）

永远不要猜测或使用通用描述！如果缺少详细信息，请使用智能添加来发现它们。

## 关键：始终使用 run.py 包装器

**永远不要直接调用脚本。始终使用 `python scripts/run.py [script]`：**

```bash
# 正确 - 始终使用 run.py:
python scripts/run.py auth_manager.py status
python scripts/run.py notebook_manager.py list
python scripts/run.py ask_question.py --question "..."

# 错误 - 永远不要直接调用:
python scripts/auth_manager.py status  # 没有虚拟环境会失败！
```

`run.py` 包装器自动：
1. 如果需要，创建 `.venv`
2. 安装所有依赖
3. 激活环境
4. 正确执行脚本

## 核心工作流程

### 步骤 1：检查身份验证状态
```bash
python scripts/run.py auth_manager.py status
```

如果未通过身份验证，请继续设置。

### 步骤 2：身份验证（一次性设置）
```bash
# 浏览器必须可见以进行手动 Google 登录
python scripts/run.py auth_manager.py setup
```

**重要提示：**
- 浏览器对身份验证可见
- 浏览器窗口自动打开
- 用户必须手动登录 Google
- 告诉用户："将打开浏览器窗口进行 Google 登录"

### 步骤 3：管理笔记本库

```bash
# 列出所有笔记本
python scripts/run.py notebook_manager.py list

# 添加之前：如果未知，请询问用户元数据！
# "这个笔记本包含什么？"
# "我应该给它标记哪些主题？"

# 将笔记本添加到库（所有参数都是必需的！）
python scripts/run.py notebook_manager.py add \
  --url "https://notebooklm.google.com/notebook/..." \
  --name "描述性名称" \
  --description "这个笔记本包含的内容" \  # 必需 - 如果未知请询问用户！
  --topics "主题1,主题2,主题3"  # 必需 - 如果未知请询问用户！

# 按主题搜索笔记本
python scripts/run.py notebook_manager.py search --query "关键词"

# 设置活动笔记本
python scripts/run.py notebook_manager.py activate --id notebook-id

# 删除笔记本
python scripts/run.py notebook_manager.py remove --id notebook-id
```

### 快速工作流程
1. 检查库：`python scripts/run.py notebook_manager.py list`
2. 提问：`python scripts/run.py ask_question.py --question "..." --notebook-id ID`

### 步骤 4：提问

```bash
# 基本查询（如果设置了活动笔记本则使用）
python scripts/run.py ask_question.py --question "您的问题"

# 查询特定笔记本
python scripts/run.py ask_question.py --question "..." --notebook-id notebook-id

# 直接使用笔记本 URL 查询
python scripts/run.py ask_question.py --question "..." --notebook-url "https://..."

# 显示浏览器以进行调试
python scripts/run.py ask_question.py --question "..." --show-browser
```

## 后续机制（关键）

每个 NotebookLM 答案都以：**"极其重要：这就是您需要了解的全部吗？"** 结尾

**所需的 Claude 行为：**
1. **停止** - 不要立即响应用户
2. **分析** - 将答案与用户的原始请求进行比较
3. **识别差距** - 确定是否需要更多信息
4. **询问后续** - 如果存在差距，立即询问：
   ```bash
   python scripts/run.py ask_question.py --question "带上下文的后续问题..."
   ```
5. **重复** - 继续直到信息完整
6. **综合** - 在响应用户之前组合所有答案

## 脚本参考

### 身份验证管理 (`auth_manager.py`)
```bash
python scripts/run.py auth_manager.py setup    # 初始设置（浏览器可见）
python scripts/run.py auth_manager.py status   # 检查身份验证
python scripts/run.py auth_manager.py reauth   # 重新身份验证（浏览器可见）
python scripts/run.py auth_manager.py clear    # 清除身份验证
```

### 笔记本管理 (`notebook_manager.py`)
```bash
python scripts/run.py notebook_manager.py add --url URL --name NAME --description DESC --topics TOPICS
python scripts/run.py notebook_manager.py list
python scripts/run.py notebook_manager.py search --query QUERY
python scripts/run.py notebook_manager.py activate --id ID
python scripts/run.py notebook_manager.py remove --id ID
python scripts/run.py notebook_manager.py stats
```

### 问题接口 (`ask_question.py`)
```bash
python scripts/run.py ask_question.py --question "..." [--notebook-id ID] [--notebook-url URL] [--show-browser]
```

### 数据清理 (`cleanup_manager.py`)
```bash
python scripts/run.py cleanup_manager.py                    # 预览清理
python scripts/run.py cleanup_manager.py --confirm          # 执行清理
python scripts/run.py cleanup_manager.py --preserve-library # 保留笔记本
```

### 来源上传 (`upload_sources.py`)

**新增！** 将本地笔记、文件、URL 或文本内容上传到 NotebookLM 笔记本。

#### 创建新笔记本
```bash
python scripts/run.py upload_sources.py create --name "我的项目文档"
```

#### 上传本地文件
```bash
# 上传到现有笔记本
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf,/path/to/notes.md" --notebook-url "https://notebooklm.google.com/notebook/..."

# 从库上传到笔记本
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --notebook-id my-notebook-id

# 创建新笔记本并上传
python scripts/run.py upload_sources.py upload --files "/path/to/doc.pdf" --create-notebook "新项目"
```

#### 上传目录（批量）
```bash
# 从目录上传所有支持的文件
python scripts/run.py upload_sources.py upload-dir --directory "/path/to/docs" --notebook-id ID

# 按扩展名过滤
python scripts/run.py upload_sources.py upload-dir --directory "/path/to/docs" --extensions "pdf,md,txt" --create-notebook "文档"
```

#### 添加 URL（网站/YouTube）
```bash
python scripts/run.py upload_sources.py add-urls --urls "https://example.com,https://youtube.com/watch?v=..." --notebook-id ID
```

#### 添加文本内容
```bash
# 从命令行
python scripts/run.py upload_sources.py add-text --text "您的长文本内容在这里..." --notebook-id ID

# 从文件
python scripts/run.py upload_sources.py add-text --file "/path/to/content.txt" --notebook-id ID
```

**支持的文件格式：** PDF、TXT、MD、DOCX、DOC

**限制：**
- 每个笔记本最多 50 个来源
- 每个来源最多 500,000 字
- 使用 `--show-browser` 进行调试

## 环境管理

虚拟环境自动管理：
- 首次运行自动创建 `.venv`
- 依赖项自动安装
- Chromium 浏览器自动安装
- 所有内容隔离在技能目录中

手动设置（仅在自动失败时）：
```bash
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
pip install -r requirements.txt
python -m patchright install chromium
```

## 数据存储

所有数据存储在 `~/.claude/skills/notebooklm/data/`：
- `library.json` - 笔记本元数据
- `auth_info.json` - 身份验证状态
- `browser_state/` - 浏览器 Cookie 和会话

**安全性：** 由 `.gitignore` 保护，永远不要提交到 git。

## 配置

技能目录中的可选 `.env` 文件：
```env
HEADLESS=false           # 浏览器可见性
SHOW_BROWSER=false       # 默认浏览器显示
STEALTH_ENABLED=true     # 人类行为
TYPING_WPM_MIN=160       # 打字速度
TYPING_WPM_MAX=240
DEFAULT_NOTEBOOK_ID=     # 默认笔记本
```

## 决策流程

```
用户提到 NotebookLM
    ↓
检查身份验证 → python scripts/run.py auth_manager.py status
    ↓
如果未通过身份验证 → python scripts/run.py auth_manager.py setup
    ↓
┌─────────────────────────────────────────────────────────────┐
│                    用户想要什么？                            │
├─────────────────────────────────────────────────────────────┤
│                           │                                   │
│    上传文件        │    提问                            │
│                           │                                   │
│    ↓                      │    ↓                              │
│    创建/选择笔记本 │    检查/添加笔记本             │
│    ↓                      │    ↓                              │
│    upload_sources.py      │    ask_question.py                │
│    upload/upload-dir/     │    --question "..."               │
│    add-urls/add-text      │    ↓                              │
│                           │    后续直到完整              │
│                           │    ↓                              │
│                           │    综合并响应                   │
└─────────────────────────────────────────────────────────────┘
```

### 上传流程（新增！）
```
用户想要上传本地文件
    ↓
检查身份验证 → python scripts/run.py auth_manager.py status
    ↓
创建新笔记本（可选）→ python scripts/run.py upload_sources.py create --name "..."
    ↓
上传文件 → python scripts/run.py upload_sources.py upload --files "..." --notebook-url/--notebook-id
    ↓
确认上传成功
    ↓
（可选）将笔记本添加到库 → python scripts/run.py notebook_manager.py add --url URL
```


## 故障排除

| 问题 | 解决方案 |
|---------|----------|
| ModuleNotFoundError | 使用 `run.py` 包装器 |
| 身份验证失败 | 设置时浏览器必须可见！ --show-browser |
| 速率限制（50/天） | 等待或切换 Google 账户 |
| 浏览器崩溃 | `python scripts/run.py cleanup_manager.py --preserve-library` |
| 找不到笔记本 | 使用 `notebook_manager.py list` 检查 |

## 最佳实践

1. **始终使用 run.py** - 自动处理环境
2. **首先检查身份验证** - 在任何操作之前
3. **后续问题** - 不要在第一个答案处停止
4. **身份验证时浏览器可见** - 需要手动登录
5. **包含上下文** - 每个问题是独立的
6. **综合答案** - 组合多个响应

## 限制

- 无会话持久性（每个问题 = 新浏览器）
- 免费 Google 账户的速率限制（每天 50 次查询）
- 每个笔记本最多 50 个来源，每个来源最多 500,000 字
- 浏览器开销（每次操作几秒钟）

## 资源（技能结构）

**重要目录和文件：**

- `scripts/` - 所有自动化脚本（ask_question.py、notebook_manager.py 等）
- `data/` - 身份验证和笔记本库的本地存储
- `references/` - 扩展文档：
  - `api_reference.md` - 所有脚本的详细 API 文档
  - `troubleshooting.md` - 常见问题和解决方案
  - `usage_patterns.md` - 最佳实践和工作流程示例
- `.venv/` - 隔离的 Python 环境（首次运行时自动创建）
- `.gitignore` - 保护敏感数据不被提交
