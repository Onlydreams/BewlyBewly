# AGENTS.md

## 语言规范
- 始终使用简体中文与用户对话，并保持专业、简洁。
- 所有新增或修改的文档使用中文。
- 新增代码注释使用中文；仅在复杂逻辑需要解释时添加注释。

## 项目概览
- BewlyBewly 是一个面向 Bilibili 的浏览器扩展，用于重新设计和优化 B 站页面体验。
- 技术栈：Vue 3、TypeScript、Vite、Pinia、UnoCSS、WebExtension Manifest V3。
- 包管理器：pnpm，版本见 `package.json` 的 `packageManager` 字段。

## 关键目录
- `src/contentScripts/`：内容脚本入口和注入到 B 站页面的主应用。
- `src/contentScripts/views/`：首页、搜索、番剧、历史、稍后再看、收藏等 Bewly 页面。
- `src/background/`：扩展后台脚本、消息监听、跨域 API 请求封装。
- `src/background/messageListeners/api/`：B 站 API 定义集合。
- `src/components/`：通用 UI、顶部栏、Dock、设置面板、视频卡片等组件。
- `src/logic/`：通用初始化、存储和设置默认值。
- `src/stores/`：Pinia 状态。
- `src/styles/`：全局样式、字体、页面适配样式。
- `src/_locales/`：多语言文案，修改文案时手动维护对应语言文件。
- `scripts/`：构建前准备、manifest 生成和开发辅助脚本。

## 常用命令
- 安装依赖：`pnpm install`
- Chrome/Edge 开发：`pnpm dev`
- Firefox 开发：`pnpm dev-firefox`
- Chrome/Edge 构建：`pnpm build`
- Firefox 构建：`pnpm build-firefox`
- 运行测试：`pnpm test`
- 类型检查：`pnpm typecheck`
- 代码检查：`pnpm lint`
- 自动修复：`pnpm lint:fix`

## 构建结构
- `vite.config.ts` 构建 `popup` 和 `options` 页面。
- `vite.config.content.ts` 将 content script 打包为 IIFE，输出到 `dist/contentScripts/index.global.js`。
- `tsup.config.ts` 构建 background，并复制 `src/inject/index.js`。
- `src/manifest.ts` 生成扩展 manifest；按 Chrome、Firefox、Safari 和开发环境做条件配置。

## 开发约定
- 优先遵循现有 Vue `<script setup lang="ts">`、组合式 API、Pinia 和 `~/` 路径别名风格。
- 样式优先复用现有 UnoCSS、SCSS 变量和组件样式，不随意引入新的 UI 体系。
- 图标优先使用项目已有的 Iconify/UnoCSS 图标写法。
- 设置项新增时，同步检查：
  - `src/logic/storage.ts` 的类型和默认值。
  - 设置面板组件。
  - i18n 文案。
  - 相关 watcher 或迁移逻辑。
- 修改 B 站 API 时，优先沿用 `src/background/messageListeners/api/` 中的 API 定义模式，避免在 content script 直接请求接口。
- 修改页面注入逻辑时，重点检查 iframe、Shadow DOM、首页替换和非首页适配之间的差异。

## 代码质量
- 项目启用 TypeScript strict、`noUnusedLocals` 和 ESLint。
- import 排序由 `eslint-plugin-simple-import-sort` 约束。
- 不要提交 `extension/`、`extension-firefox/`、`extension-safari/`、`dist/`、`node_modules/` 等生成物。
- 若改动涉及核心逻辑，优先补充或更新 `src/tests/` 下的 Vitest 测试。

## 浏览器扩展注意事项
- content script 会在 `document_start` 注入，改动时注意首屏闪烁、原站 DOM 尚未就绪和样式加载顺序。
- 首页可能清空原 B 站页面并挂载 Bewly 应用，非首页通常是增强和样式适配。
- background 负责统一处理 B 站 API 请求、cookie 和浏览器差异。
- Firefox 有多账号容器和请求头特殊处理，涉及 background 请求逻辑时必须考虑。

## Git 与协作
- 不要回滚用户已有改动，除非用户明确要求。
- 分支命名可参考项目贡献文档：`feat/`、`fix/`、`doc/` 等。
- Commit 类型参考：`feat`、`fix`、`docs`、`style`、`refactor`、`test`、`chore`、`perf`、`ci`。
