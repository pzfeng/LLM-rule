1. 页面组件只做组装、状态注入、权限展示和事件绑定；业务逻辑下沉到 Hook/service/domain/utils。
2. UI 组件只展示和基础交互，不依赖业务 Store、不发业务请求、不引用页面专属常量。
3. 拆分文件必须按职责落地：组件进 `components/`，Hook 进 `hooks/`，service/API 进 `services/` 或 `api/`，工具进 `utils/`，类型进 `types/`，常量进 `constants/`。
4. 禁止把拆分出来的单个组件、Hook、service、util 平铺堆在同一级目录；如果项目已有目录约定，优先遵循项目约定。
5. JSX 嵌套不超过 3 层；列表 key 必须唯一稳定，禁止 index key。
6. 页面必须处理 loading、error、empty、success、disabled 等必要状态。
7. 表单必须有校验、防重复提交、异常提示和必要的防抖/节流。
8. 单个组件内 `useState` 超过 3 个时，必须触发状态建模检查，评估是否改用 `useReducer`、自定义 Hook、表单方案、URL state 或外部状态管理。
9. 单个组件内 `useState` 超过 5 个时，默认必须重构，除非这些状态完全独立且有明确理由。
10. 多个 state 如果总是一起变化，必须合并为一个对象状态或使用 `useReducer`。
11. 多个 state 如果表达同一流程，例如 `idle`、`loading`、`success`、`error`、`submitting`，优先使用 `useReducer`、联合类型或状态机建模。
12. 表单状态必须有明确生命周期：Modal/Drawer 关闭、编辑对象变化、Select/Tab/步骤切换时必须 reset、重新初始化或清理不适用字段、错误和 dirty 状态。
13. 新增/编辑共用表单必须显式区分 mode、初始值、重置策略和提交 payload；异步初始值必须防止旧请求覆盖当前表单。
