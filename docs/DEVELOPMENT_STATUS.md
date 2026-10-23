# 见面聊（FaceTo）开发状态

## 当前项目实际状态

- 项目根目录：`/Library/即享/projects/faceTo_app`
- 当前分支：`main`
- 远程分支：`origin/main`
- 最近提交：
  - `9cfc251 新建项目`
  - `8aeeada 修改文件名称`
  - `8ec4c00 初始化项目`
- 本次任务开始时工作区有一个未跟踪的设计源文件：
  - `见面聊_全页面整理与Figma_AI提示词_珊瑚粉暖橙主题版.md`
- 其他 Git 状态：当前分支与 `origin/main` 对齐，没有发现已跟踪文件的未提交修改。
- `.DS_Store` 和 `unpackage/` 被 `.gitignore` 忽略。

## 当前代码结构

- `App.uvue`：应用生命周期；Android/HarmonyOS 返回键退出逻辑。
- `main.uts`：Vue 3 `createSSRApp` 应用入口。
- `pages.json`：仅注册 `pages/index/index`。
- `pages/index/index.uvue`：模板首页，显示 logo 和 `Hello`。
- `manifest.json`：声明 UniApp x、Vue 3、Vapor、微信小程序等基础配置。
- `platformConfig.json`：当前目标仅 `APP-ANDROID`。
- `uni.scss`：UniApp 默认主题变量，尚未映射完整品牌设计系统。
- `static/logo.png`：当前唯一静态资源。
- `README.md`：只有项目技术方向描述。

## 已完成事项


- 检查 Git 分支、工作区状态、近期提交和文件清单。
- 阅读当前 UniApp x、Vue 3、UTS、`.uvue`、主题和平台配置。
- 检查并读取工作区最新版设计源文件。
- 建立项目入口规范：`AGENTS.md`。
- 建立决策记录：`docs/DECISIONS.md`。
- 建立真实状态记录：本文件。
- 建立第一阶段计划：`docs/IMPLEMENTATION_PLAN.md`。
- 实现品牌主题令牌、通用按钮和状态标签组件。
- 实现共享类型、轻量会话/身份/申请状态和本地 Mock 持久化。
- 实现统一数据服务入口、公开首页 Mock 数据、登录 Mock、服务者申请 Mock。
- 实现路由目标保存、登录引导和登录后恢复。
- 实现登录页、身份选择页、普通用户首页、单页服务者申请页。
- 实现游客公开浏览、账号操作登录引导、开发测试登录、身份选择、申请提交、审核状态、拒绝重提历史和工作台入口展示。
- 首页动态封面支持多照片数据模型，并以固定高度的三图组合区域展示，当前使用本地占位图片。

## 当前进行中的事项

- 第一阶段四个页面和基础能力已编写完成，正在等待 UniApp x/HBuilderX 实际编译与运行验证。
- 尚未完成 Android 真机/模拟器流程回归，也未完成 iOS、HarmonyOS、微信小程序验证。

## 待处理事项

- 确认设计源文件的版本和页面视觉细节。
- 确认第一阶段公开数据和登录恢复动作。
- 补充确认服务者申请字段的校验、图片规格、身份证信息形式和隐私安全要求。
- 验证 Android、iOS、HarmonyOS、微信小程序的 UniApp x 编译与平台能力。
- 使用 HBuilderX 完成 UniApp x 实际编译，修复可能存在的 UTS/UVue 语义错误。
- 完成 Android 优先运行验证和四个页面核心流程回归。
- 有条件时验证 iOS、HarmonyOS、微信小程序。
- 后续建立独立个人中心页面，并移除第一阶段首页轻量个人面板的临时替代。

## 已知问题与潜在风险

1. 项目仍为初始化模板，没有业务模块、数据层、共享状态、路由守卫或测试。
2. `platformConfig.json` 仅声明 `APP-ANDROID`，与四平台目标不完全一致。
3. `manifest.json` 中微信小程序 appid 为空，iOS 配置基本为空，未发现 HarmonyOS 独立配置。
4. 项目没有 `package.json`、lockfile、TypeScript 配置、Lint 配置或测试脚本，无法通过依赖声明确认 Vue/UTS/UniApp x 的精确版本。
5. 页面脚本使用 `lang="uts"`，不能直接当作普通 Vue TypeScript 代码处理。
6. `uni.scss` 仍为默认主题变量，不能直接视为最终品牌主题。
7. 尚未验证 Vapor、Apple 登录、微信登录、短信登录、原生插件和小程序能力。
8. 未提交申请表单不能持久化，但审核历史需要持久化；两者必须在数据模型中分离。
9. 服务者申请包含真实姓名、身份证信息、头像和生活照等敏感个人信息；当前开发 Mock 仅保存脱敏身份证摘要，不保存未提交表单，但真实 API 仍需完整安全方案。
10. 开发测试入口已按 `process.env.NODE_ENV == 'development'` 控制展示，但正式打包仍需实际验证入口彻底隐藏。
11. 当前开发环境的 HBuilderX `cli` 启动失败并提示 `Incompatible processor ... neon`，因此尚未完成 HBuilderX 实际编译。

## 最近一次实际验证结果

第一阶段代码编写后的验证：静态 UTS/UVue 脚本、6 个 UVue 模板、样式和 JSONC 语法检查通过；相对导入和页面注册检查通过；Mock 页面隔离、身份证脱敏和开发测试入口条件检查通过；行尾空白和 Git 差异完整性检查通过。HBuilderX 实际编译尚未执行成功，不能报告为编译通过。此前项目基线验证如下：

本次执行的只读基础检查：

- Git 分支和状态检查：通过。规范文档创建前，当前分支为 `main`，工作区除未跟踪设计源文件外无其他变化。
- 文件和目录清单检查：通过。
- 现有配置、入口、页面和主题文件静态阅读：通过。
- 设计源文件存在性、标题和设计系统内容检查：通过；已确认包含 C03 登录、C04 首页、C36 服务者申请页及相关设计系统。
- Node/npm/pnpm/Java/HBuilderX 环境识别：通过；HBuilderX 版本为 `5.26.2026091802`。
- 跟踪文件差异完整性检查：`git diff --check` 通过，无报告错误。

尚未执行且不能报告为通过：

- UniApp x/UTS 编译。
- Android、iOS、HarmonyOS、微信小程序构建。
- 登录授权、短信、Apple 登录、微信登录联调。
- UI 视觉逐页比对。
- 自动化测试、Lint、类型检查（当前项目没有对应脚本或配置）。
- 原生插件和 SDK 验证。

## 下一项建议执行任务

1. 在 HBuilderX 中打开项目并执行 Android 编译/运行，修复真实 UTS、UVue、Vapor 和样式兼容问题。
2. 按九条核心流程完成 Android 优先回归。
3. 记录 iOS、HarmonyOS、微信小程序的真实编译和运行结果。
4. 完成验证后再决定是否进入独立个人中心、工作台及后续业务页面。
