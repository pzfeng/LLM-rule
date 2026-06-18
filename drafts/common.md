# 代码抽象与设计模式约束规范

## 抽象原则 (KISS & DRY)
- 禁止过度设计：在逻辑被重复使用 3 次之前，严禁进行泛型、基类或复杂模式的抽象。优先编写平铺直叙的直观代码。
- 单一职责：一个函数只做一件事。如果一个 Service/Controller 方法超过 50 行，必须拆分为私有辅助函数（Helper）。

## 设计模式使用规范
- 禁用复杂继承：严禁使用多层 Class 继承。如果需要复用行为，一律使用“组合（Composition over Inheritance）”或纯函数。
- 推荐模式：在多分支业务中，优先使用「策略模式（Strategy Pattern）」或「工厂模式」，严禁编写超过 4 层的 `if-else` 或 `switch`。
- 架构分层约束：严格遵守「Controller / Service / Repository」三层架构。Controller 只管路由和参数校验，Service 管业务逻辑，Repository 管数据库。严禁跨层调用。

# 专家级 TypeScript/Node.js 后端开发标准

## 1. 命名与语义契约 (Naming & Semantics)
- **严格遵循领域驱动设计 (DDD) 命名**：禁止使用 `data`, `info`, `handle`, `process` 等模糊词。命名必须体现业务领域（如 `issueInvoice`, `revokePermission`）。
- **函数命名**：返回布尔值的函数必须以 `is`, `has`, `can`, `should` 开头；异步函数严禁加 `Async` 后缀（TS 类型系统已足够清晰），但其命名必须是一个“动作”。
- **变量可读性**：严禁使用单字母变量（如 `e`, `i`, `res`），即使在 `catch` 或循环中，也必须写成 `error`, `index`, `response`。

## 2. 专家级抽象原则 (Advanced Abstraction)
- **KISS > DRY**：在代码被重复使用 3 次之前，严禁进行泛型、基类或复杂设计模式的抽象 [2]。优先编写平铺直叙、一眼看透的高阶函数。
- **高内聚低耦合**：单个函数代码不得超过 30 行，单个文件不得超过 150 行。超过此限制，AI 必须主动拒绝继续编写，并要求开发者先进行职责拆分。
- **组合优于继承**：严禁使用多层 Class 继承 [2]。多态行为一律通过“纯函数组合”、“策略地图 (Strategy Map)”或“依赖注入 (DI)”实现。

## 3. 防御性编程与强类型 (Defensive Programming & Typing)
- **零 Anyscript 容忍**：严禁使用 `any`，严禁使用 `as unknown as X`。必须使用严格的泛型、`unknown`（配合类型守卫 `Type Guards`）或 `satisfies` [1, 2]。
- **空值防御**：严格区分 `null`（代表故意留空）和 `undefined`（代表未定义）。处理可选属性时，必须使用类型守卫进行明确定位。
- **不可变性 (Immutability)**：优先使用 `readonly` 修饰符和 `ReadonlyArray`。严禁直接修改函数入参，所有对象变更必须通过解构赋值（`...`）返回新对象。
- **异步安全网**：所有 Promise 调用链必须有 explicit 错误流向，严禁在 `try-catch` 中写空的回调（如 `catch(e) {}` 必须处理或抛出）。



## 1. 语义化命名与 DDD 契约 (Naming & DDD Semantics)
- 【拒绝模糊词】：严禁使用 `data`, `info`, `handle`, `process`, `manage`, `item` 等无业务语义的泛化词。
- 【领域动词化】：函数命名必须体现真实业务领域。
  - 反例：`handleUser()`, `processInvoice()`, `updateStatus()`
  - 正例：`registerUser()`, `auditInvoice()`, `freezeAccount()`
- 【布尔命名】：所有返回布尔值的变量/函数必须且仅能以 `is`, `has`, `can`, `should` 开头（例：`isEnabled`, `hasPermission`）。
- 【拒绝单字母】：严禁使用单字母变量（如 `e`, `i`, `res`）。在循环或捕获中，必须写出完整单词（`error`, `index`, `response`）。

## 2. 模块化与抽象的“度” (Modularization Boundaries)
- 【单一职责】：一个函数只做一件事。单个函数代码不得超过 30 行，单个文件不得超过 150 行。超过此限制，AI 必须主动拒绝继续编写，并提示开发者拆分。
- 【组合优于继承】：严禁使用多层 Class 继承（即超过1层继承）。行为复用一律通过“纯函数组合（Function Composition）”或“组合模式（Composition Pattern）”实现。

## 3. 前端类型安全与状态防线
- 【绝对零 Any】：严禁使用 `any` 和 `as unknown as X` 类型断言。若遇到不确定类型，必须使用 `unknown` 并编写显式类型守卫（Type Guards）。
- 【UI 与业务分离】：组件文件（.tsx/.vue）只负责 UI 渲染和事件绑定。所有复杂的业务逻辑、数据转换、API 请求必须抽离到自定义 Hooks（React）或 Composables（Vue）中。
- 【不可变状态】：严禁直接修改 UI 状态（State）或 Props。所有状态更新必须通过不可变数据结构（Immutability）处理，多用解构赋值（`...`）和只读声明 `ReadonlyArray`。
- 【字面量防护】：定义配置、路由、映射表时，必须使用 `satisfies` 关键字，禁止使用单纯的类型标注。

## 4. 后端稳健性与高可用约束
- 【防进程崩溃】：所有异步调用（Promises, Async/Await）必须包裹在 `try-catch` 中，或有明确的 `.catch()` 链式处理。严禁出现 Unhandled Rejection。
- 【异常即数据模式】：禁止在业务层（Service 层）内部乱扔 `throw new Error`。所有预期的业务错误（如密码错误、余额不足）必须作为函数的返回值（Result Pattern）返回。
- 【消灭嵌套 if-else】：控制流中的 `if-else` 嵌套严禁超过 2 层。多分支业务必须使用“策略模式（Strategy Pattern）”或“对象策略映射表（Strategy Map）”重构。
- 【无状态与内存安全】：严禁在全局作用域声明可变变量。所有定时任务或长连接对象必须具备显式的销毁（Clean-up）机制，严禁产生闭包导致的内存泄漏。

## 5. 数据库持久层硬性红线
- 【防 SQL 注入】：严禁拼接任何原始 SQL 字符串（如 `WHERE id = ${id}`）。必须使用 ORM 的安全参数化查询（Parameterized Queries）或 Query Builder。
- 【显式实体映射】：所有数据库表实体（Entities）和传输对象（DTOs）必须声明字段的 nullability（可空性）。严禁让数据库的 `NULL` 隐式穿透到业务层。
- 【索引与慢查询防御】：AI 在编写查询（Query）时，如果查询条件（WHERE）、排序（ORDER BY）、聚合（GROUP BY）涉及的字段未在 Scheme 中建立索引，必须在代码中追加 `// TODO: Requires Index` 警告注释。
- 【软删除红线】：所有生产业务数据表必须具备 `deleted_at`（或 `is_deleted`）字段。AI 编写删除逻辑时默认必须为“软删除（Soft Delete）”，除非开发者明确下达“硬删除（Hard Delete）”指令。

## 6. 代码复用与重构卡点 (Rule of Three 落地规范)
- 【两点包容】：对于仅在 1-2 处出现的相似业务逻辑，严禁编写任何泛型、高阶函数或基类进行合并。保持代码平铺直叙，优先保障代码的可读性和独立演进能力。
- 【三点熔断】：当且仅当某段代码、数据结构或业务控制流在项目中被重复使用 **3 次** 时，AI 必须触发重构机制：
  1. 停止在当前文件继续堆砌重复代码。
  2. 提取出无状态的纯函数（Pure Function）或独立组件。
  3. 将抽离的函数放入对应的 `utils/`, `helpers/` 或 `hooks/` 目录中。
  4. 必须为重构出的通用函数编写严格的 TS 类型声明与 JSDoc 注释。

## 7. React 公共组件提取与设计规范 (Component Extraction Line)
- 【提取触发时机】：严禁过早组件化。只有当某个 UI 结构或交互在 **3 个或以上不同的独立页面/场景**中被复用，且其核心视觉/交互逻辑一致时，方可提取为通用公共组件（存放于 `src/components/common` 或 `src/components/ui`）。
- 【纯粹性红线 (Pure Component)】：公共组件必须是**纯粹的 UI 渲染器或交互状态机**。
  - 严禁在公共组件内部使用全局状态（如 Redux, Zustand, useContext 等业务 Store）。
  - 严禁在公共组件内部发起业务 API 网络请求（所有数据和网络行为必须通过 Props 由外部传入）。
  - 严禁引入任何特定页面的业务常量或多语言 Key。
- 【Props 专家级设计】：
  - 接口扩展性：公共组件的 Props 必须显式继承原生的 HTML 元素属性（例如：Button 组件的 Props 必须继承 `React.ButtonHTMLAttributes<HTMLButtonElement>`）。
  - DOM 引用支持：所有公共组件必须使用 `React.forwardRef` 包裹，正确向下传递 `ref`。
  - 核心属性收敛：公共 UI 组件原则上不允许接收整个业务对象（如 `user={user}`），应仅接收其所需的原子属性（如 `name={user.name} avatar={user.avatar}`）。
- 【样式彻底隔离】：
  - 严禁在公共组件中使用全局或混淆的 CSS 类名。必须使用 Tailwind CSS、CSS Modules 或 CSS-in-JS。
  - 公共组件必须暴露 `className` 和 `style` 属性，允许外部调用者对其进行微调或布局（使用 `clsx` 或 `tailwind-merge` 进行样式合并）。
- 【状态收敛 (Controlled vs Uncontrolled)】：
  - 如果组件内部有状态（如 Modal 的开关、Input 的值），必须同时支持“受控模式（通过 value/onChange 驱动）”和“非受控模式（通过 defaultValue 驱动）”，或提供明确的受控转换接口。

