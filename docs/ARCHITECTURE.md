# 见面聊（FaceTo）架构说明

> 用途：记录**实际代码结构**、模块职责、关键技术方案与依赖关系。本文只描述已存在的实现；计划中的内容见 `docs/PROJECT_STATUS.md` 与 `docs/IMPLEMENTATION_PLAN.md`。
> 更新时间：2026-10-31。

## 一、技术底座

| 项 | 实际取值 | 依据 |
|---|---|---|
| 框架 | UniApp x + Vue 3 | `manifest.json` `vueVersion: 3` |
| 语言 | UTS，页面 `<script setup lang="uts">`，文件后缀 `.uvue` | 全部页面文件 |
| 渲染 | Vapor，`vapor-render-target: bytecode` | `manifest.json` |
| 样式 | `.uvue` 内 `<style lang="scss">` + `uni.scss` 全局变量（`$facetof-*` 品牌令牌） | 页面与 `uni.scss` |
| 平台目标 | `platformConfig.json` 当前仅 `APP-ANDROID` | 配置文件 |
| 依赖管理 | 无 `package.json`、无 npm 依赖；全部使用内置 API 与自有代码 | 目录检查 |
| 构建 | HBuilderX 图形界面（CLI 在本机不可用） | 环境实测 |

## 二、目录结构与职责

```
faceTo_app/
├── App.uvue                 应用生命周期（含 Android/HarmonyOS 返回键退出逻辑）
├── main.uts                 createSSRApp 入口
├── pages.json               47 个页面注册 + 5 项原生 tabBar + 页面导航栏配置
├── manifest.json            应用/平台配置（Vapor 已开启）
├── platformConfig.json      编译目标（当前 APP-ANDROID）
├── uni.scss                 品牌主题令牌（$facetof-color-*、$facetof-radius-*、$facetof-space-*）
├── components/              跨页面公共组件
│   ├── facetof-button.uvue  品牌按钮（tone computed 切换样式）
│   ├── status-pill.uvue     状态标签
│   ├── segment-tabs.uvue    横向可滚动 Tab/筛选（pill/underline/chip 三形态）
│   └── primary-tab-bar.uvue 自定义底部导航（已不被页面引用，保留备查）
├── types/models.uts         全部共享业务类型（约 920 行，页面与服务层共同引用）
├── state/session-state.uts  轻量共享状态：登录/身份/申请/权限分离 + 本地持久化
├── router/route-guard.uts   路由常量 + 登录意图保存/恢复（saveIntent/openLogin/afterLogin）
├── services/                数据访问层（页面唯一数据入口，页面禁止直连 mock-*）
│   ├── api-config.uts       API 域名/前缀/超时（改域名只动这一处）
│   ├── api-client.uts       HTTP 封装：Token、幂等键、超时、JSON 解析、401 统一处理、上传
│   ├── auth-api.uts         登录/验证码/刷新/退出/`/users/me/`/首次身份选择
│   ├── home-api.uts         首页 feed、类目映射、游标分页
│   ├── profile-api.uts      个人资料读写、头像上传、兴趣标签/城市选项
│   ├── provider-api.uts     服务者入驻申请（创建/查询/修改/媒体上传）
│   ├── provider-business-api.uts  服务者资料、服务管理、服务上下架/提审
│   ├── provider-moments-api.uts   服务者动态管理（列表/草稿/修改/发布/删除/挂载服务）
│   ├── service-container.uts 统一业务门面（约 94 个导出函数；页面只 import 这里）
│   └── mock-*.uts            各业务模块 Mock 实现（34 个文件）
├── pages/                   页面（按业务域分组，见下表）
├── static/                  本地素材（brand/avatars/content/following/moments/order/payment/search/settings/tabbar/video/wallet 等目录）
└── docs/                    规范与状态文档
```

## 三、页面分组（47 页）

| 业务域 | 页面 | 说明 |
|---|---|---|
| 一级导航 | index、moments、news、messages、profile | 原生 `tabBar` 五项；切换不重建页面（历史闪烁问题的解法） |
| 认证与身份 | login、identity、provider/apply | 登录、身份选择、服务者入驻申请 |
| 发现与搜索 | search/search、search/results | 搜索与结果页 |
| 内容详情 | provider/detail、service/detail、moment/detail、news/detail | 服务者主页、服务详情、抖音式动态详情、资讯详情 |
| 交易 | order/confirm、payment/result、order/list、order/detail | 下单、支付结果、订单列表/详情 |
| 用户资产 | favorites/list、following/list、moments/mine、wallet、coupons、membership、reviews/reviews、profile/edit、review/review、verification/id-verify | 收藏/关注/我的动态/钱包/优惠券/会员/评价/资料编辑/实名 |
| 消息 | chat/detail | 聊天详情 |
| 服务者工作台 | provider/dashboard、profile、moment-manage、services、service-edit、schedule、orders、order-detail、earnings、withdrawal、reviews、analytics、certification | P01–P14 服务者侧 |
| 通用 | settings、about、help、common/document、moment/publish | 设置/关于/帮助/协议模板/发布动态 |

## 四、分层与依赖方向

依赖严格单向：**页面 → service-container → 领域服务（\*-api.uts 或 mock-\*.uts）→ api-client → api-config**。

- 页面（`.uvue`）只允许 import `services/service-container.uts`、`types/models.uts`、`state/session-state.uts`、`router/route-guard.uts`、`components/*`。
- `service-container.uts` 是唯一门面：按业务域导出函数，对每个函数决定走真实 API 还是 Mock。
- Mock/真实 API 切换规则（现状）：
  - `sessionState.userId` 以 `test-`/`mock-` 开头（开发测试账号）→ 走 `mock-*.uts`；
  - 真实账号 → 走 `*-api.uts`；
  - 动态流、资讯、消息、搜索、订单、收藏、关注、钱包、优惠券、会员、聊天、评价、排期、服务者订单/财务/数据看板/认证等模块**尚未接后端**，目前全部走 Mock（矩阵见 `docs/PROJECT_STATUS.md`）。
- HTTP 层约定：后端响应 `{code, message, data}`，`payload()` 兼容直接返回业务对象；错误信息可含 `details` 字段（已用于申请表单字段级校验提示）；写操作带 `Idempotency-Key` 防重复；401 统一清态并跳登录页（登录/退出接口自身除外）；媒体展示用后端短期签名 `display_url`。

## 五、关键技术方案

### 1. 共享状态（state/session-state.uts）
- 单个 `reactive` 对象 `sessionState`，但**概念上分离管理**：`loggedIn`（登录）、`identity`（身份选择）、`applicationStatus`（入驻申请状态）、`providerPermission`（服务者权限）、`primaryRole/providerStatus`（后端角色）。
- 本地持久化 key：`faceto.session.v1`；退出登录清理身份/申请/权限与待恢复意图，下一次登录以后端 `/users/me/` 为准。
- `providerPermission` 只对测试账号可本地置真；真实账号必须以服务端结果为准（本地记录不能当权限凭证）。

### 2. 路由与登录引导（router/route-guard.uts）
- 集中导出全部路由常量；`saveIntent()` 保存「原始操作 + 目标路由 + 参数」，登录成功后 `afterLogin()` 恢复。
- 游客可浏览公开内容；预约/聊天/收藏等私有操作经 `openLogin()` 引导。

### 3. API 客户端（services/api-client.uts）
- `requestGet/Post/Patch/Delete/Upload` 统一返回 `{statusCode, body}`；业务层再转业务结果类型（`XxxResult = {ok, code, message, ...}`），网络错误/业务错误/权限错误分别处理。
- `api-config.uts` 单点配置域名（当前 Cloudflare 测试域名），换正式域名只改 `origin`。

### 4. 媒体
- 上传：`uni.uploadFile` → `/api/v1/media/uploads/`（`purpose` 区分用途，如 `PROVIDER_APPLICATION_AVATAR/LIFE_PHOTO`、`PROVIDER_SERVICE_COVER/GALLERY`、`MOMENT_IMAGE`）。
- 展示：优先后端签名 `display_url`（免鉴权、有有效期），无则回退 `content_url`；相对路径拼 `API_CONFIG.origin`。

### 5. 版本与并发
- 申请、服务、动态等资源带 `version/expected_version`，修改、发布、删除、提审均传预期版本防并发覆盖；动态创建/上传使用幂等键。

### 6. 渲染安全（Android 真机经验，务必遵守）
- 响应式数组迭代是已知崩溃源：**禁止用返回数组的 `computed` 做派生列表**；统计/派生值在数据刷新函数中显式生成并赋值。
- 不在模板中对静态数组做 `v-for`（P06 曾因此闪退）；`v-for` 避免用可能重复的字符串（图片路径等）做 `:key`。
- 已知未解决问题：`ConcurrentModificationException`（Vapor `x9.onComplete`）偶发闪退，与数据加载相关，详见 `docs/PROJECT_STATUS.md` 已知问题 #1。

### 7. UI 规范
- 品牌令牌集中在 `uni.scss`；尺寸用 `rpx`；禁止后代/相邻兄弟/`:last-child` 等选择器；`display` 仅 `flex/none`；`align-items` 仅 `center/flex-start/flex-end/stretch`。
- 固定底部操作栏（提交/支付/发布）必须放在纵向 `scroll-view` 外层；横向滚动用 `scroll-view direction="horizontal"` 且内容项不压缩。
- 页面视觉以设计源文件为准，Figma 仅补充；冲突先报告。

## 六、测试与验证方式（现状）

- 无自动化测试脚本；验证方式为：静态约束扫描（选择器/显示属性/数组用法）、导出引用交叉检查、括号平衡、`git diff --check`，加上 HBuilderX Android 真机人工验收。
- 验证结论必须标注覆盖范围（Android 真机已验证 / 代码完成待验证 / 未验证），详见 `docs/PROJECT_STATUS.md` 第六节。
