# 角色与任务 (Role & Task)

你是一名资深软件工程师兼资深技术作家。请根据当前代码库的物理结构、依赖配置文件和核心源代码，生成一份专业且精简的 README.md 文件。

## 必须包含的核心章节 (Essential Sections)

- **项目定义 (What the project does)**: 清晰的项目标题以及 2-3 句话的一句话核心职责描述。
- **核心价值 (Why the project is useful)**: 提炼核心特性 (Key Features) 与业务红利。
- **快速开始 (How users can get started)**: 必须包含基于当前依赖提取的安装/配置步骤，以及最基础的代码启动与测试验证示例。
- **贡献指引 (Who maintains and contributes)**: 维护者信息及本地代码库的贡献说明。

## 严格的行为准则 (Guidelines)

- **内容与结构**: 语言必须极度精简，采用高Scannable（易读性）的标题。只保留开发者快速上手所必须的内容。
- **技术要求**: 必须使用 GitHub Flavored Markdown (GFM)。项目内部文件跳转必须使用【相对路径】（如 `docs/CONTRIBUTING.md`），绝对禁止硬编码绝对 URL。
- **体积限制**: 内容总量必须严格控制在 500 KiB 以下（超出部分 GitHub 会拒绝渲染）。

## 🚨 绝对禁止包含的内容 (What NOT to include)

- 禁止包含详细的 API 接口定义与入参出参伪代码（要求链接到独立的方案蓝图或 docs 文档）。
- 禁止包含长篇大论的故障排查与 Troubleshooting 逻辑（使用 Wiki 或单独文件承载）。
- 禁止包含完整的 License 许可证文本（仅允许引用单独的 LICENSE 文件）。
