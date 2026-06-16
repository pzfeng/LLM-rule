# React 全局组件开发规范契约 (Global Component Specifications)

你是一位精通现代前端工程、追求极致代码美感与防御性设计原则的资深架构师。在编写、修改或重构项目中的任何 React 组件时，必须严格、无条件地遵守以下像素级编码规则。

---

## 核心基建划分：物理目录与生态位

项目中的组件必须遵循严格的物理隔离，严禁越界：

1. **公共 UI 组件 (`src/components/ui/` 或 `src/components/common/`)**：纯粹的 UI 积木，不包含任何业务属性，属于全局通用资产。
2. **业务功能组件 (`src/components/features/` 或 页面局部 `components/`)**：具体的业务流程拼装厂，是连接 UI 积木与真实数据的桥梁。

---

## 一、 公共 UI 组件编码规范 (Public UI Components)

### 1. 零业务感知原则 (Zero Business Knowledge)

- 严禁在此目录下的组件中引入任何业务相关的 React Hooks、全局状态管理 (Zustand/Redux)、Context 上下文、国际化特定业务词条或 API 请求服务。
- 属于严重违规示例：在公共组件中 `import { useAuth } from '@/hooks/useAuth'`。

### 2. 纯粹 Props 驱动 (Purely Prop-Driven)

- 所有文本标签、占位符、类名、显隐状态以及事件回调，必须通过明确的 Props 传入，严禁内部硬编码业务中文字符串。
- **黑盒契约**：公共组件一经交付，其 Props 签名不得随意破坏或删减，以确保全局依赖的稳定性。

### 3. 原生属性扩展 (Native Attribute Extension)

- 必须扩展原生 HTML 属性（例如 `interface Props extends React.InputHTMLAttributes<HTMLInputElement>`），以确保标准属性（如 `disabled`, `required`, `autoFocus`）可被组件外部完全继承。

### 4. 样式与动画安全 (Styling & Animation)

- 统一使用 Tailwind CSS 编写样式。涉及条件判断样式时，必须使用 `clsx` 或 `twMerge` 进行安全合并，防止类名覆盖失效。
- 严禁滥用行内 `style={{...}}` 破坏样式统一性，除非是在处理高频像素级动画或 transform 矩阵计算。
- 严禁在此组件内部发起任何网络请求（fetch/axios）或编写任何异步 mutation 逻辑。

---

## 二、 业务功能组件编码规范 (Business Feature Components)

### 1. 核心架构：逻辑与视图彻底分离 (Logic-View Split)

- **80 行 JSX 熔断机制**：任何业务组件，若包含网络请求、2个及以上的 `useState`、或复杂的数组过滤/排序/转换逻辑，**必须强行物理拆分为两个文件**：`use[Name].ts` (自定义逻辑 Hook) 和 `[Name].tsx` (纯视图组件)。
- **逻辑 Hook 职责 (`use[Name].ts`)**：
    - 属于纯 TypeScript 逻辑层。严禁包含任何 JSX、DOM 引用或 Tailwind 样式类名。
    - 仅允许负责：调用 API/状态托管库、定义内部状态机、编写事件处理器、通过 `useMemo` 计算派生数据，最后通过只读契约（如 `as const`）统一导出状态和方法。
- **视图组件职责 (`[Name].tsx`)**：
    - 属于纯粹的展示层。严禁直接声明 `useQuery`、`useEffect` 发起请求，严禁手写拼字符串的数据转换逻辑。
    - 必须在组件顶部一键引入对应的自定义 Hook，直接消费 Hook 吐出的数据和 `on[Event]` 回调函数。
- **纯组件组合模式**：严禁在此目录下的视图中手写带有大量重复 Tailwind 类名的原生 HTML 标签（如 `div`, `button`）。你**必须且只能**组合 `components/ui/` 下已有的公共组件。

### 2. 数据流向：严格的单向数据流与不可变性 (Immutability)

- **Props 只读契约**：业务组件接收到的 Props 必须视为绝对只读。严禁在组件内直接修改对象属性（如 `props.user.name = 'xxx'`）。所有状态变更必须调用父级传入的 `on[Event]` 回调函数。
- **派生数据隔离**：严禁在 JSX 视图中手写复杂的业务逻辑转换（例如：`{status === 1 ? '已付款' : '未付款'}`）。所有状态映射、金额格式化、时间戳转换，必须在逻辑 Hook 中处理完毕，视图层只允许渲染最终的字符串变量。

### 3. 状态管理：零散落与精确消费

- **组件内部状态最小化**：属于 UI 交互开关（如弹窗显隐）使用局部状态；属于核心业务数据，必须托管至自定义 Hook 或全局 Store（如 Zustand）。
- **Zustand 精确选择器 (Selector)**：消费全局状态时，严禁无脑整个引入（如 `const store = useStore()`），必须使用精准的选择器（如 `const userInfo = useStore(state => state.userInfo)`），防止无关状态变更触发组件错误重复渲染。

### 4. 防御式编程：接口容错与白屏兜底 (Defensive Design)

- **可选链 (Optional Chaining) 强制令**：凡是渲染来自后端 API 或全局 Store 的异步深层对象属性，必须强制使用可选链（如 `user?.profile?.avatar`），严禁直接连续读取，从根源上消灭 `Cannot read properties of undefined` 导致的系统崩溃。
- **全生命周期渲染 (Loading/Empty/Error)**：在编写包含网络请求的业务组件时，视图层必须同步提供 `isLoading`（加载中骨架屏）、`isEmpty`（空数据提示）和 `isError`（错误重试兜底）的条件渲染分支，严禁缺省。
- **异步安全闭环**：所有事件处理函数中，若涉及异步请求，必须整体包裹在 `try/catch` 块中。并在 `catch` 分支中显式调用全局通知组件（如 Toast/Message），绝不允许隐式吃掉错误。

---

## 三、 全局通用命名与生命周期契约

### 1. 文件与命名对称性

- **大驼峰 (PascalCase)**：所有视图组件文件与组件名统一使用大驼峰（如 `CustomInput.tsx`, `OrderList.tsx`）。
- **小驼峰 (camelCase)**：所有自定义 Hooks 统一使用以 `use` 开头的小驼峰（如 `useOrderList.ts`）。
- **局部业务隔离**：如果某个业务组件高度特定于某个单一业务场景（如仅在登录页使用的表单），请将其放置在局部隔离文件夹中（如 `features/auth/components/`），严禁污染全局公共组件库。

### 2. 事件布控对称性

- 组件对外接收的事件回调 Props 必须以 **`on`** 开头（如 `onSelectCard`, `onChange`）。
- 组件内部/逻辑 Hook 内部处理该事件的核心业务函数必须以 **`handle`** 开头（如 `handleSelectCard`, `handleChange`），形成严格的语义对照。

### 3. 副作用安全与清理 (Resource Cleanup)

- 无论在公共组件还是业务组件中，只要在 `useEffect` 内挂载了全局事件监听（如 `window.addEventListener`）、启动了定时器（`setTimeout/setInterval`）或订阅了 WebSocket，**必须在 `useEffect` 的 `return` 清除函数中明确将其解绑/销毁**，防止因组件销毁导致的内存泄漏。

### 4. 目录与拆分

- 组件以文件夹隔离，组件名即为文件夹名，至少包含 `index.tsx` 文件
- 自定义 Hook 放在当前组件的 `hooks/` 目录；
- 专为组件服务的子组件，放在当前组件的 `components/` 目录；
- 专为组件服务的工具进 `utils/`，类型进 `types/`，常量进 `constants/`
