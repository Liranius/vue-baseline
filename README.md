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
`Checks` 执行检查并核对必需步骤的原始结果，全部成功才输出验证标记；PR 的 `Quality` 汇总门禁同时要求任务成功、标记有效且运行未取消。
`main` 推送和手动运行的汇总名为 `CI result`，不提供 PR 所需的 `Quality`；各事件的并发取消相互隔离。
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

当前能力与验收状态：

- 本地工具链和 Chromium 冒烟已接通；远端完整 CI、失败 PR 拦截、修复后合并及 `main` 推送触发已于 2026-10-14 验证，见 [验收 PR #1](https://github.com/Liranius/vue-baseline/pull/1)。验收时 `main` 要求 `Quality` 通过且与目标分支同步，管理员同样受约束。
- 提交信息由本地 commit-msg hook 和 CI 的 `Quality` 检查，覆盖 PR 将保留的全部新增提交。2026-10-15 已验证非末尾违规提交会使 `Quality` 失败，见 [失败记录](https://github.com/Liranius/vue-baseline/actions/runs/38722391975)；整理后的完整 CI 结果见 [PR #5](https://github.com/Liranius/vue-baseline/pull/5)。仓库已核验为仅允许 rebase merge。
- `Quality` 为 PR 专用汇总门禁。2026-10-15 已在临时受保护分支验证失败、取消、跳过、缺失和旧结果均被拒合，手动运行不能替代 PR 门禁，修复后合并成功，见 [门禁验收 PR #7](https://github.com/Liranius/vue-baseline/pull/7)。临时分支已清理，`main` 保护规则未改动。
- [Dependabot](.github/dependabot.yml) 每周提出 GitHub Actions 更新候选，由维护者按上述条件审查；应用及开发依赖暂不自动创建版本更新 PR，不采用自动合并。
- 依赖漏洞和秘密扫描未接入当前 CI；平台内建能力的启用范围待核查。
- 架构边界、Knip、SAST、体积 / 性能 / 可访问性预算暂未启用；出现实际需求时评估接入。多浏览器可本地执行，CI 仅运行 Chromium。

配置例外应在相关配置注释或 PR 中记录规则与范围、原因、责任人和复查条件；依赖或规则升级时确认保留、收窄或移除。
