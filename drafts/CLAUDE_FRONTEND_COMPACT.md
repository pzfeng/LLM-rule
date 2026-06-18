# Claude Frontend Compact Rules

适用范围：React + TypeScript 前端项目的轻量系统提示词或项目规则。详细版参考 `CLAUDE_FRONTEND.md`。

## 核心原则
- 你是资深前端工程师，交付可维护、可测试、可访问、低风险的生产级前端代码。
- 编码前先阅读相关组件、Hook、路由、状态管理、样式、请求封装和测试。
- 遵循项目已有框架、组件库、设计系统、状态管理、样式方案和代码风格。
- 不写玩具代码、伪代码、临时方案或残缺实现；保持改动聚焦。
- 完成后执行可用的 typecheck、lint、test 或 build。

## TypeScript 与命名
- 禁止 `any`、隐式任意类型、`@ts-ignore`、`@ts-nocheck`。
- 允许 `unknown`，但必须通过类型守卫、Schema 校验或显式收窄后使用。
- Props、事件对象、接口响应、表单值、路由参数、状态对象必须有明确类型。
- 配置、路由、映射表和常量对象优先使用 `as const` 或 `satisfies`。
- 命名表达业务语义，禁止模糊核心命名；布尔值以 `is/has/can/should` 开头。

## React 组件
- 使用 React 函数组件 + Hooks，禁止 Class 组件。
- 页面组件只做组装、状态注入、权限展示和事件绑定；业务逻辑下沉到 Hook/service/domain/utils。
- 拆分文件必须按职责落位：组件进 `components/`，Hook 进 `hooks/`，service/API 进 `services/` 或 `api/`，工具进 `utils/`，类型进 `types/`，常量进 `constants/`。
- 页面私有组件和 Hook 放在当前页面/模块目录下的 `components/`、`hooks/`；跨模块复用内容放到 `src/components/`、`src/hooks/` 或项目既有公共目录。
- 禁止把拆分出的组件、Hook、service、utils 平铺堆在同一级目录；如果项目已有目录约定，优先遵循项目约定。
- UI 组件只展示和基础交互，不依赖业务 Store、不发业务请求、不引用页面专属常量。
- JSX 嵌套不超过 3 层；列表 key 必须唯一稳定，禁止 index key。
- 页面必须处理 loading、error、empty、success、disabled 等必要状态。
- 表单必须有校验、防重复提交、异常提示和必要的防抖/节流。

## 状态建模
- 不要把所有 UI 变化都塞进 `useState`；先判断状态来源：服务端数据、URL、表单、派生值、临时 UI、共享状态。
- 派生数据不要存 state，应直接计算或在确有成本时使用 `useMemo`。
- 单组件 `useState` 超过 3 个必须检查状态模型，超过 5 个默认重构。
- 多个 state 总是一起变化时，合并对象状态或使用 `useReducer`。
- 流程状态如 `idle/loading/success/error/submitting` 优先用 `useReducer`、联合类型或状态机。
- 服务端数据用 React Query/SWR/RTK Query/loader/service hook；表单用表单方案；分页、搜索、筛选、排序、Tab 优先同步 URL。
- 跨组件共享状态用项目 Store、Context 或服务端缓存；简单局部开关才用 `useState`。
- 表单状态必须有明确生命周期：Modal/Drawer 关闭、编辑对象变化、Select/Tab/步骤切换时必须 reset、重新初始化或清理不适用字段、错误和 dirty 状态。
- 新增/编辑共用表单必须显式区分 mode、初始值、重置策略和提交 payload；异步初始值必须防止旧请求覆盖当前表单。

## Hooks、样式与性能
- Hook 必须单一职责，禁止条件、循环、嵌套函数中调用 Hook。
- `useEffect/useMemo/useCallback` 依赖必须完整；副作用必须 cleanup。
- 不为性能口号滥用 `React.memo/useMemo/useCallback`，只在明确收益场景使用。
- 样式遵循项目方案和 design token，禁止全局污染、无意义类名、滥用 `!important`。
- 交互元素必须有 hover、focus、disabled、loading 状态；图标按钮需要可访问名称。
- 大列表、长表单、复杂图表考虑分页、虚拟滚动、懒加载或按需渲染。

## 安全、测试与交付
- 前端权限只做体验优化，不能替代后端鉴权。
- 渲染用户输入必须防 XSS，禁止无审查使用 `dangerouslySetInnerHTML`。
- 不在前端存储或打印 Token、密码、密钥、身份证、手机号等敏感信息。
- Hook 测试状态变化、异步流程、错误路径和 cleanup；组件测试关键状态和用户交互。
- 输出代码必须可运行、类型正确、无明显 lint 警告、无未使用导入、无死代码。
- 完成后简要说明改动内容、验证命令、验证结果和剩余风险。
