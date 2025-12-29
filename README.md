<div align="center">

# NotebookLM Claude Code 技能

**直接从 [Claude Code](https://github.com/anthropics/claude-code) 查询并上传到 Google NotebookLM。获取基于来源的答案并上传本地文件，无需离开编辑器。**

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-purple.svg)](https://www.anthropic.com/news/skills)
[![Based on](https://img.shields.io/badge/Based%20on-NotebookLM%20MCP-green.svg)](https://github.com/PleasePrompto/notebooklm-mcp)
[![GitHub](https://img.shields.io/github/stars/PleasePrompto/notebooklm-skill?style=social)](https://github.com/cclank/notebooklm-skill)

> 直接从 Claude Code 查询笔记本并将本地文件（PDF、MD、TXT）上传到 Google NotebookLM。创建新笔记本、添加 URL、粘贴文本内容 - 所有这些都通过浏览器自动化实现持久身份验证。大幅减少幻觉 - 答案仅来自您上传的文档。

[安装](#安装) • [快速开始](#快速开始) • [为什么选择 NotebookLM](#为什么选择-notebooklm-而非本地-rag) • [工作原理](#工作原理) • [MCP 替代方案](https://github.com/PleasePrompto/notebooklm-mcp)

</div>

---

## 重要说明：仅限本地 Claude Code

**此技能仅适用于本地 [Claude Code](https://github.com/anthropics/claude-code) 安装，不适用于 Web UI。**

Web UI 在沙盒中运行技能，没有网络访问权限，而此技能需要网络访问来进行浏览器自动化。您必须在本地机器上使用 [Claude Code](https://github.com/anthropics/claude-code)。

---

## 问题所在

当您告诉 [Claude Code](https://github.com/anthropics/claude-code) "搜索我的本地文档"时，会发生以下情况：
- **大量令牌消耗**：搜索文档意味着反复读取多个文件
- **检索不准确**：搜索关键词，遗漏文档之间的上下文和连接
- **幻觉**：当找不到内容时，它会编造看似合理的 API
- **手动复制粘贴**：在 NotebookLM 浏览器和编辑器之间不断切换

## 解决方案

此 Claude Code 技能让 [Claude Code](https://github.com/anthropics/claude-code) 直接与 [**NotebookLM**](https://notebooklm.google/) 聊天 — Google 的**基于来源的知识库**，由 Gemini 2.5 驱动，仅从您上传的文档提供智能、综合的答案。

```
您的任务 → Claude 询问 NotebookLM → Gemini 综合答案 → Claude 编写正确代码
```

**不再有复制粘贴的繁琐操作**：Claude 直接提问并直接在 CLI 中获得答案。它通过自动后续问题建立深入理解，获取具体的实现细节、边缘情况和最佳实践。

---

## 为什么选择 NotebookLM，而非本地 RAG？

| 方法 | 令牌成本 | 设置时间 | 幻觉 | 答案质量 |
|----------|------------|------------|----------------|----------------|
| **向 Claude 提供文档** | 非常高（多次读取文件） | 即刻 | 是 - 填补空白 | 检索可变 |
| **网络搜索** | 中等 | 即刻 | 高 - 不可靠的来源 | 不确定 |
| **本地 RAG** | 中-高 | 数小时（嵌入、分块） | 中 - 检索空白 | 取决于设置 |
| **NotebookLM 技能** | 最小 | 5 分钟 | 最小 - 仅基于来源 | 专家综合 |

### NotebookLM 的优势是什么？

1. **由 Gemini 预处理**：上传文档一次，即时获得专家知识
2. **自然语言问答**：不仅仅是检索 - 实际理解和综合
3. **多来源关联**：连接 50 多个文档中的信息
4. **引用支持**：每个答案都包含来源引用
5. **无需基础设施**：无需向量数据库、嵌入或分块策略

---

## 安装

### 最简单的安装方式：

```bash
# 1. 创建技能目录（如果不存在）
mkdir -p ~/.claude/skills

# 2. 克隆此仓库
cd ~/.claude/skills
git clone https://github.com/cclank/notebooklm-skill notebooklm

# 3. 就是这样！打开 Claude Code 并说：
"我有哪些技能？"
```

当您首次使用该技能时，它会自动：
- 创建隔离的 Python 环境（`.venv`）
- 安装所有依赖项，包括 **Google Chrome**
- 使用 Chrome（而非 Chromium）设置浏览器自动化，以实现最大可靠性
- 所有内容都包含在技能文件夹中

**注意：** 设置使用真正的 Chrome 而不是 Chromium，以实现跨平台可靠性、一致的浏览器指纹识别以及更好的 Google 服务反检测功能。

---

## 快速开始

### 1. 检查您的技能

在 Claude Code 中说：
```
"我有哪些技能？"
```

Claude 将列出您的可用技能，包括 NotebookLM。

### 2. 使用 Google 进行身份验证（一次性）

```
"设置 NotebookLM 身份验证"
```
*Chrome 窗口打开 → 使用您的 Google 账户登录*

### 3. 创建您的知识库

**选项 A：通过 Claude Code 上传（新增！）**
```
"创建一个名为'我的项目文档'的新 NotebookLM 笔记本并上传这些文件：/path/to/doc.pdf"
```

**选项 B：手动上传**
转到 [notebooklm.google.com](https://notebooklm.google.com) → 创建笔记本 → 上传您的文档：
- PDF、Google 文档、Markdown 文件
- 网站、GitHub 仓库
- YouTube 视频
- 每个笔记本多个来源

分享：**设置 → 分享 → 任何拥有链接的人 → 复制**

### 4. 添加到您的库

**选项 A：让 Claude 找出来（智能添加）**
```
"查询这个笔记本关于其内容并将其添加到我的库：[您的链接]"
```
Claude 将自动查询笔记本以发现其内容，然后使用适当的元数据添加它。

**选项 B：手动添加**
```
"将此 NotebookLM 添加到我的库：[您的链接]"
```
Claude 将询问名称和主题，然后保存它以备将来使用。

### 5. 开始研究

```
"我的 React 文档关于 hooks 是怎么说的？"
```

Claude 自动选择正确的笔记本并直接从 NotebookLM 获取答案。

---

## 工作原理

这是一个 **Claude Code 技能** - 一个包含指令和脚本的本地文件夹，Claude Code 可以在需要时使用。与 [MCP 服务器版本](https://github.com/PleasePrompto/notebooklm-mcp)不同，它直接在 Claude Code 中运行，无需单独的服务器。

### 与 MCP 服务器的关键区别

| 功能 | 此技能 | MCP 服务器 |
|---------|------------|------------|
| **协议** | Claude Skills | 模型上下文协议 |
| **安装** | 克隆到 `~/.claude/skills` | `claude mcp add ...` |
| **会话** | 每个问题新浏览器 | 持久聊天会话 |
| **兼容性** | 仅 Claude Code（本地） | Claude Code、Codex、Cursor 等 |
| **语言** | Python | TypeScript |
| **分发** | Git 克隆 | npm 包 |

### 架构

```
~/.claude/skills/notebooklm/
├── SKILL.md              # Claude 的指令
├── scripts/              # Python 自动化脚本
│   ├── ask_question.py   # 查询 NotebookLM
│   ├── upload_sources.py # 上传文件/URL/文本（新增！）
│   ├── notebook_manager.py # 库管理
│   └── auth_manager.py   # Google 身份验证
├── .venv/                # 隔离的 Python 环境（自动创建）
└── data/                 # 本地笔记本库
```

当您提到 NotebookLM 或想要上传文件时，Claude：
1. 加载技能指令
2. 运行相应的 Python 脚本
3. 打开浏览器，执行操作（查询或上传）
4. 直接将结果返回给您
5. 使用该知识帮助您完成任务

---

## 核心功能

### **上传本地文件（新增！）**
直接将文件上传到 NotebookLM，无需离开编辑器：
- **支持的格式**：PDF、TXT、MD、DOCX
- **批量上传**：一次上传整个目录
- **创建笔记本**：在上传期间自动创建新笔记本
- **添加 URL**：添加网站和 YouTube 视频作为来源
- **粘贴文本**：直接将文本内容添加到笔记本

### **基于来源的响应**
NotebookLM 通过仅从您上传的文档回答问题来大幅减少幻觉。如果信息不可用，它会表示不确定性而不是编造内容。

### **直接集成**
浏览器和编辑器之间无需复制粘贴。Claude 以编程方式提问并接收答案。

### **智能库管理**
使用标签和描述保存 NotebookLM 链接。Claude 自动为您的任务选择正确的笔记本。

### **自动身份验证**
一次性 Google 登录，然后身份验证在会话之间持久存在。

### **自包含**
所有内容都在技能文件夹中使用隔离的 Python 环境运行。无需全局安装。

### **类似人类的自动化**
使用真实的打字速度和交互模式来避免检测。

---

## 常用命令

| 您所说的 | 发生的 |
|--------------|--------------|
| *"设置 NotebookLM 身份验证"* | 打开 Chrome 进行 Google 登录 |
| *"将 [链接] 添加到我的 NotebookLM 库"* | 使用元数据保存笔记本 |
| *"显示我的 NotebookLM 笔记本"* | 列出所有保存的笔记本 |
| *"询问我的 API 文档关于 [主题]"* | 查询相关笔记本 |
| *"使用 React 笔记本"* | 设置活动笔记本 |
| *"清除 NotebookLM 数据"* | 重新开始（保留库）|
| **新增上传命令** | |
| *"创建一个名为'X'的 NotebookLM 笔记本"* | 创建新笔记本 |
| *"将 /path/to/file.pdf 上传到 NotebookLM"* | 将文件上传到笔记本 |
| *"将我的笔记文件夹上传到 NotebookLM"* | 批量上传目录 |
| *"将此 URL 添加到我的笔记本：[url]"* | 将网站添加为来源 |

---

## 真实示例

### 示例 1：维修手册查询

**用户询问**："查看我的 Suzuki GSR 600 维修手册，了解制动液类型、发动机油规格和后桥扭矩。"

**Claude 自动**：
- 使用 NotebookLM 进行身份验证
- 询问关于每个规格的综合问题
- 当提示"这就是您需要了解的全部吗？"时进行后续
- 提供准确的规格：DOT 4 制动液、SAE 10W-40 机油、100 N·m 后桥扭矩

![NotebookLM 聊天示例](images/example_notebookchat.png)

### 示例 2：无幻觉构建

**您**："我需要为 Gmail 垃圾邮件过滤构建 n8n 工作流。使用我的 n8n 笔记本。"

**Claude 的内部过程：**
```
→ 加载 NotebookLM 技能
→ 激活 n8n 笔记本
→ 通过后续问题提出综合问题
→ 从多个查询综合完整答案
```

**结果**：第一次尝试即可工作的工作流，无需调试幻觉的 API。

---

## 技术细节

### 核心技术
- **Patchright**：浏览器自动化库（基于 Playwright）
- **Python**：此技能的实现语言
- **隐身技术**：类似人类的打字和交互模式

注意：MCP 服务器使用相同的 Patchright 库，但通过 TypeScript/npm 生态系统。

### 依赖项
- **patchright==1.55.2**：浏览器自动化
- **python-dotenv==1.0.0**：环境配置
- 首次使用时自动安装在 `.venv` 中

### 数据存储

所有数据都本地存储在技能目录中：

```
~/.claude/skills/notebooklm/data/
├── library.json       # 您的笔记本库及元数据
├── auth_info.json     # 身份验证状态信息
└── browser_state/     # 浏览器 Cookie 和会话数据
```

**重要安全提示：**
- `data/` 目录包含敏感的身份验证数据和个人笔记本
- 它通过 `.gitignore` 自动从 git 中排除
- 永远不要手动提交或共享 `data/` 目录的内容

### 会话模型

与 MCP 服务器不同，此技能使用**无状态模型**：
- 每个问题打开一个新浏览器
- 提问，获得答案
- 添加后续提示以鼓励 Claude 提出更多问题
- 立即关闭浏览器

这意味着：
- 无持久聊天上下文
- 每个问题是独立的
- 但您的笔记本库持久存在
- **后续机制**：每个答案包含"这就是您需要了解的全部吗？"以提示 Claude 提出全面的后继问题

对于多步骤研究，Claude 会在需要时自动提出后续问题。

---

## 限制

### 技能特定
- **仅限本地 Claude Code** - 不适用于 Web UI（沙盒限制）
- **无会话持久性** - 每个问题是独立的
- **无后续上下文** - 无法引用"上一个答案"

### NotebookLM
- **速率限制** - 免费层有每日查询限制
- **最大来源** - 每个笔记本 50 个来源，每个来源 500,000 字
- **分享要求** - 笔记本必须公开分享才能查询

---

## 常见问题

**为什么这在 Claude Web UI 中不起作用？**
Web UI 在沙盒中运行技能，没有网络访问权限。浏览器自动化需要网络访问才能到达 NotebookLM。

**这与 MCP 服务器有何不同？**
这是一个更简单的基于 Python 的实现，直接作为 Claude Skill 运行。MCP 服务器功能更丰富，具有持久会话，并适用于多种工具（Codex、Cursor 等）。

**我可以同时使用此技能和 MCP 服务器吗？**
可以！它们有不同的用途。使用技能进行快速 Claude Code 集成，使用 MCP 服务器进行持久会话和多工具支持。

**如果 Chrome 崩溃怎么办？**
运行：`"清除 NotebookLM 浏览器数据"`，然后重试。

**我的 Google 账户安全吗？**
Chrome 在您的本地机器上运行。您的凭据永远不会离开您的计算机。如果您担心，请使用专用的 Google 账户。

---

## 故障排除

### 未找到技能
```bash
# 确保它位于正确的位置
ls ~/.claude/skills/notebooklm/
# 应该显示：SKILL.md、scripts/ 等
```

### 身份验证问题
说：`"重置 NotebookLM 身份验证"`

### 浏览器崩溃
说：`"清除 NotebookLM 浏览器数据"`

### 依赖项问题
```bash
# 如果需要，手动重新安装
cd ~/.claude/skills/notebooklm
rm -rf .venv
python -m venv .venv
source .venv/bin/activate  # 或 Windows 上的 .venv\Scripts\activate
pip install -r requirements.txt
```

---

## 免责声明

此工具自动化与 NotebookLM 的浏览器交互，以使您的工作流程更高效。但是，一些友好的提醒：

**关于浏览器自动化：**
虽然我内置了人性化功能（真实的打字速度、自然的延迟、鼠标移动）以使自动化行为更自然，但我无法保证 Google 不会检测或标记自动化使用。我建议使用专用的 Google 账户进行自动化，而不是您的主要账户——就像网络爬虫一样：可能没问题，但最好还是安全起见！

**关于 CLI 工具和 AI 代理：**
像 Claude Code、Codex 和类似的 AI 驱动助手这样的 CLI 工具功能非常强大，但它们可能会犯错。请谨慎和小心地使用它们：
- 在提交或部署之前始终检查更改
- 首先在安全环境中测试
- 保留重要工作的备份
- 记住：AI 代理是助手，而不是万无一失的神谕

我为自己构建了这个工具，因为我厌倦了 NotebookLM 和编辑器之间的复制粘贴繁琐操作。我希望它也能帮助其他人，但我不能对可能发生的任何问题、数据丢失或账户问题负责。请自行决定和判断使用。

也就是说，如果您遇到问题或有疑问，请随时在 GitHub 上提出问题。我很乐意帮助排除故障！

---

## 致谢

此技能受到我的 [**NotebookLM MCP 服务器**](https://github.com/PleasePrompto/notebooklm-mcp) 的启发，并提供作为 Claude Code 技能的替代实现：
- 两者都使用 Patchright 进行浏览器自动化（MCP 使用 TypeScript，技能使用 Python）
- 技能版本直接在 Claude Code 中运行，无需 MCP 协议
- 针对技能架构优化的无状态设计

如果您需要：
- **持久会话** → 使用 [MCP 服务器](https://github.com/PleasePrompto/notebooklm-mcp)
- **多工具支持**（Codex、Cursor）→ 使用 [MCP 服务器](https://github.com/PleasePrompto/notebooklm-mcp)
- **快速 Claude Code 集成** → 使用此技能

---

## 总结

**没有此技能**：NotebookLM 在浏览器中 → 复制答案 → 粘贴到 Claude → 复制下一个问题 → 回到浏览器...

**拥有此技能**：Claude 直接研究 → 即时获得答案 → 编写正确代码

停止复制粘贴的繁琐操作。开始在 Claude Code 中直接获得准确的、基于来源的答案。

```bash
# 30 秒开始
cd ~/.claude/skills
git clone https://github.com/cclank/notebooklm-skill notebooklm
# 打开 Claude Code："我有哪些技能？"
```

---

<div align="center">

作为我的 [NotebookLM MCP 服务器](https://github.com/PleasePrompto/notebooklm-mcp) 的 Claude Code 技能改编而构建

用于直接在 Claude Code 中进行基于来源的、基于文档的研究

</div>
