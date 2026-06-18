# Node.js TypeScript 后端代码编写规范

## 核心架构原则
- 异步控制：严禁使用同步阻塞 API（如 `fs.readFileSync`），一律使用 Promises 版本的异步 API。
- 异常防御：所有异步逻辑、数据库查询、RPC 调用必须使用 `try-catch` 捕获异常。严禁出现未捕获的 Promise（Unhandled Rejection）。
- 内存安全：严禁在全局作用域缓存大量数据或闭包，防范内存泄漏。

## 类型与数据库约束
- 严禁使用 `any`，所有数据库查询结果（如 Prisma, TypeORM, Mongoose）必须显式定义 DTO（数据传输对象）或 TS 接口。
- 外部输入（`req.body`, `req.query`, `req.params`）必须在入口处使用 Zod 或 class-validator 进行运行时类型校验。
- 严禁拼接原生 SQL 字符串。必须使用 ORM 的安全参数化查询（Parameterized Queries），从根源杜绝 SQL 注入。

## 日志与监控
- 严禁使用 `console.log`，必须使用统一的 Logger 模块（如 Winston, Pino 或框架自带 Logger）。
- 记录日志时，敏感信息（如密码、身份证、Token）必须脱敏处理。