# CLAUDE.md - 用户级工作原则与技术偏好

## 命令规范
- 禁止直接执行 rm -rf / 之类的命令

## 核心工作原则

### 1. 第一性原理思维
- 始终从物理本质和基本事实出发解构问题，剥离既有假设和惯性思维
- 在分析任何方案前，先追问：**“问题的根本边界条件是什么？”** 和 **“哪些是确定无疑的底层约束？”**
- 避免类比推理的陷阱，优先基于基本原理推导解决方案
- 当遇到矛盾时，回归第一性原理重新审视问题定义
- 有问题需要修复时，使用遵循从根本上解决问题，不要轻易使用兜底方案

### 2. 规划先行，确认后动
- **每次执行任务前**，必须完成以下动作：
  1. 理解目标与约束
  2. 分解为可执行的子任务（步骤清晰、顺序合理）
  3. 标注关键依赖和潜在风险点
  4. 输出完整规划给用户
- **等待用户明确确认后**方可开始执行
- 若用户未回复确认，默认不开始任何实质性代码编写或系统变更

### 3. 执行总结与反馈
- 每次执行结束后，必须输出结构化的执行总结，包含：
  - **已完成事项**：按子任务逐一列出完成情况
  - **关键技术决策**：本次执行中做出的重要选择及理由
  - **产出物清单**：新增/修改的文件、函数、配置等
  - **遗留问题与下一步建议**：未完成项或待优化点
- 总结应简洁、清晰、可追溯

## 技术栈偏好

### 后端开发框架
- **首选：Python 常用框架**
  - FastAPI（优先推荐，现代化、高性能、自动文档）
  - Django（适合复杂业务系统、需要全栈能力时）
  - Flask（轻量级原型或微服务）
- 遵循 PEP 8 编码规范
- 使用类型注解（Type Hints）提升代码可维护性

### 后端日志与可观测性
- **日志库：优先使用 loguru**
  - 配置方式：在项目入口处完成 `logger.add()` 配置（文件轮转、JSON 格式可选）
  - 日志级别规范：DEBUG（开发调试）、INFO（关键业务流程）、WARNING（异常但可恢复）、ERROR（错误需人工介入）
  - 必须记录的关键节点：
    - 请求入口与出口（方法、路径、耗时、状态码）
    - 外部调用（数据库、API、AI 模型等）的开始与结束
    - 异常捕获（需包含完整堆栈上下文）
    - 业务决策点（例如路由分支、状态变更、降级触发）
  - 使用结构化日志：`logger.bind()` 注入业务字段（如 user_id, request_id, session_id）
- 保证业务代码的可观测性：开发过程中主动思考“如果这个环节出问题，日志能否帮我快速定位”

### AI 应用开发框架
- **优先使用 LangChain 框架**
  - 链式调用（LCEL）优先
  - 合理使用 Runnable、工具（Tools）、代理（Agents）等抽象
  - 注重提示词模板（PromptTemplate）的工程化管理
  - 关键 LangChain 调用节点必须加日志（模型请求/响应、工具调用结果）
- 备选：LlamaIndex（针对检索场景明显更优时）

### 前端开发框架
- **首选：React**
  - 函数式组件 + Hooks
  - 状态管理：Zustand 或 Context + useReducer
  - UI 库：Ant Design / Material-UI（按需选择）
  - 构建工具：Vite（推荐）或 Create React App
- 组件设计遵循单一职责原则
- 合理使用 TypeScript（可选但推荐）

### 前端可观测性与日志
- **前端日志记录原则**
  - 开发环境：使用 `console.debug` / `console.info` / `console.warn` / `console.error` 分级输出
  - 生产环境建议接入远程日志服务（如 Sentry），但开发阶段同样要保持结构化习惯
  - 必须记录的关键节点：
    - 页面/组件挂载与卸载
    - 用户关键交互（提交表单、跳转、触发异步请求）
    - API 请求发起与响应（含耗时、状态码、错误信息）
    - 状态机/全局状态的关键变更（例如登录态切换、购物车更新）
    - 异常边界捕获的错误（Error Boundary）
  - 日志格式示例：`[组件名] 操作描述 { key: value }`
- 保持可观测性：开发过程中主动思考用户反馈问题时，能否根据前端日志复现场景

## 其他约束

### 代码风格
- 注释：解释“为什么”，而不是“做什么”
- 命名：清晰、自解释，避免缩写
- 测试：关键路径需包含单元测试（pytest / Jest）

### 文档习惯
- 每个模块/函数保留 docstring 说明用途、参数、返回值
- API 接口自动生成文档（FastAPI 自动生成 Swagger）

### 沟通偏好
- 使用中文进行规划、总结和日常沟通
- 代码内注释可用英文或中文，保持一致即可

## 初始化提示

当用户提出新需求时，Claude Code 应首先回复：
> “我已收到任务。现在将按照第一性原理进行问题分析，并制定执行计划。请稍候，我会在完成规划后提交给您确认。”

然后输出规划，等待用户确认后执行。

## gstack 技能配置
- 所有网页浏览操作必须使用gstack提供的`/browse`技能，禁止使用`mcp__claude-in-chrome__*`系列工具
- 可用的gstack技能列表：
  - /office-hours
  - /plan-ceo-review
  - /plan-eng-review
  - /plan-design-review
  - /design-consultation
  - /design-shotgun
  - /design-html
  - /review
  - /ship
  - /land-and-deploy
  - /canary
  - /benchmark
  - /browse
  - /connect-chrome
  - /qa
  - /qa-only
  - /design-review
  - /setup-browser-cookies
  - /setup-deploy
  - /retro
  - /investigate
  - /document-release
  - /codex
  - /cso
  - /autoplan
  - /plan-devex-review
  - /devex-review
  - /careful
  - /freeze
  - /guard
  - /unfreeze
  - /gstack-upgrade
  - /learn