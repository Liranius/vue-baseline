# vue-baseline

Vue 3 + Vite + TypeScript 应用基准，包含 Vue Router、Pinia、组件测试和浏览器冒烟测试。

## 初始化

先安装 fnm 并完成 shell 初始化，然后在仓库根目录执行：

```sh
fnm install
fnm use
npm install --global pnpm@12.4.2
pnpm install --frozen-lockfile
pnpm hooks:install
pnpm dev
```

Node 版本见 [.node-version](.node-version)，pnpm 版本与完整脚本见 [package.json](package.json)。
编辑器扩展推荐见 [.vscode/extensions.json](.vscode/extensions.json)。
公共默认环境变量由 [.env](.env) 提供，本地开发、构建和 CI 共用。
如需个人覆盖，可参考 [.env.example](.env.example) 创建不提交的 `.env.local`；无需复制即可使用默认配置。

## 常用命令

| 命令              | 用途                                      |
| ----------------- | ----------------------------------------- |
| `pnpm dev`        | 启动开发服务                              |
| `pnpm build`      | 类型检查与生产构建，两项均成功才通过      |
| `pnpm build-only` | 仅构建                                    |
| `pnpm type-check` | 仅检查类型                                |
| `pnpm preview`    | 预览已有构建                              |
| `pnpm test:unit`  | 单次运行单元测试                          |
| `pnpm test:watch` | 开发时监听变更并重跑单元测试              |
| `pnpm lint`       | 执行 Oxlint、ESLint、Stylelint 并自动修复 |
| `pnpm format`     | 格式化文件                                |
| `pnpm ci:format`  | 只读检查格式                              |
| `pnpm ci:lint`    | 只读执行 Oxlint、ESLint、Stylelint        |

## 浏览器测试

测试会自动构建应用并启动本地预览服务，默认使用 Chromium。
首次运行前安装浏览器：

```sh
pnpm exec playwright install chromium
pnpm test:e2e
```

需要同时验证 Chromium、Firefox 和 WebKit 时：

```sh
pnpm exec playwright install chromium firefox webkit
pnpm test:e2e:all
```

## CI

[GitHub Actions](.github/workflows/ci.yml) 在 PR 更新、`main` 推送和手动触发时运行：
冻结安装、只读格式检查、只读 Lint、单元测试，以及 Chromium 冒烟。
PR 额外检查目标分支尚未包含的全部非 merge 提交信息；检查对象为事件中的 base / head SHA，不检查 PR 标题。
PR 创建、追加或重写提交、重新打开、编辑标题或目标分支时都会重新运行。
冒烟入口已包含类型检查和生产构建，CI 不重复执行；Lint 的 warning 默认不阻断。
浏览器失败报告保留 7 天。`main` 的必需检查及实际验收状态见下文；使用本基准创建新仓库时需单独配置平台规则。

## 提交与合并

提交前运行 `pnpm lint`、`pnpm test:unit` 和 `pnpm build`；
页面或构建配置变更后补充浏览器测试。

`lint`、`format` 和提交钩子会改写文件；提交钩子还会重新暂存修复结果，请审查差异。
提交信息采用 Conventional Commits，例如 `fix: correct page title`。

远端仅允许 rebase merge。单一完整变更在本地 squash 为一个提交；多个值得保留的逻辑步骤整理为几个提交，再通过 PR 合入。
正式审查前将零散修补归并到对应提交；每个保留提交应包含对应的必要测试并保持可构建。

## 维护与验收

配置维护、依赖升级和例外审查由仓库维护者负责。
每季度复查依赖支持状态、已知风险及配置例外，安全公告或兼容性问题出现时及时评估。

依赖按实际需要升级：安全修复、必要的兼容性适配、支持周期将结束、可验证的显著性能收益或项目所需功能。
升级时需同步相关版本声明与锁文件，执行完整 CI，并验证受影响的接口、行为及失败路径；PR 记录升级理由与验证结果。

配置例外应在相关配置注释或 PR 中记录规则与范围、原因、责任人和复查条件；依赖或规则升级时确认保留、收窄或移除。
