# TypeScript / JavaScript 代码编写规范

## 核心原则
- 严格遵循类型安全，禁止使用 `any`，必须显式定义 Interface 或 Type。
- 优先使用 函数式编程（Functional Programming），多用箭头函数，避免复杂的 Class 类。
- 所有异步操作必须使用 `try-catch` 包裹 `await`，并有明确的错误处理。

## 代码风格
- 单个文件行数不得超过 150 行。超过时必须进行组件或逻辑拆分。
- 必须使用 TypeScript 5.x 的最新特性（如 `satisfies` 操作符）。
- React 组件一律使用 TSX，状态管理优先使用 Hooks。

## 注释规范
- 导出的核心函数和接口必须包含 JSDoc 注释，说明参数与返回值意义。