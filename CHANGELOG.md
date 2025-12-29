# 更新日志

本项目的所有重要变更都将记录在此文件中。

格式基于 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/)，
本项目遵循 [语义化版本](https://semver.org/lang/zh-CN/spec/v2.0.0.html)。

## [1.3.0] - 2025-11-21

### 新增
- **模块化架构** - 重构代码库以提高可维护性
  - 新增 `config.py` - 集中式配置（路径、选择器、超时）
  - 新增 `browser_utils.py` - BrowserFactory 和 StealthUtils 类
  - 所有脚本之间更清晰的关注点分离

### 更改
- **超时时间增加至 120 秒** - 长查询不再提前超时
  - `ask_question.py`: 30秒 → 120秒
  - `browser_session.py`: 30秒 → 120秒
  - 解决问题 #4

### 修复
- **思考消息检测** - 修复显示占位符文本的不完整答案
  - 现在在读取答案之前等待 `div.thinking-message` 元素消失
  - 不再返回"正在查看内容..."或"正在查找答案..."等答案
  - 在所有语言和 NotebookLM UI 更改中可靠工作

- **正确的 CSS 选择器** - 更新以匹配当前的 NotebookLM UI
  - 从 `.response-content, .message-content` 更改为 `.to-user-container .message-text-content`
  - 所有脚本中使用一致的选择器

- **稳定性检测** - 改进答案完整性检查
  - 现在需要 3 次连续稳定的轮询，而不是等待 1 秒
  - 防止流式传输期间响应被截断

## [1.2.0] - 2025-10-28

### 新增
- 首次公开发布
- 通过浏览器自动化集成 NotebookLM
- 基于 Gemini 2.5 的会话式对话
- 笔记本库管理
- 知识库准备工具
- 持久会话的 Google 身份验证
