# 新聊天交接提示词

以下文本可直接复制到新 Codex 聊天开头使用：

---

请继续开发「见面聊（FaceTo）」UniApp x 客户端（项目根目录即当前工作区）。开始前请严格按 `AGENTS.md` 执行：先读 `docs/PROJECT_STATUS.md`（权威状态快照与下一步建议）、`docs/ARCHITECTURE.md`（代码结构与技术方案）、`docs/DECISIONS.md`（已确认决策），需要视觉细节时读 `见面聊_全页面整理与Figma_AI提示词_珊瑚粉暖橙主题版.md`，历史流水见 `docs/DEVELOPMENT_STATUS.md`。先执行 `git status` 与 `git log --oneline -5` 确认分支和未提交修改——当前工作区有未提交的真实 API 联调改动，严禁覆盖或还原。技术要点：这是 UTS + `.uvue` + Vapor 项目，不是普通 Vue Web；页面只通过 `services/service-container.uts` 取数；禁止后代/兄弟选择器，`display` 仅 flex/none，尺寸用 `rpx`，固定底栏放 `scroll-view` 外层，禁止数组型 `computed` 派生列表（Android 真机会闪退）。验证按 `AGENTS.md` 测试清单执行，结论区分「Android 真机已验证/代码完成待验证/未验证」。核心业务规则、共享数据结构、架构大改、新依赖、原生插件、构建配置变更、Git 提交推送必须先征求我同意。本次任务是：（在此填写具体任务）。完成后汇报改动文件、实际验证结果和待验证事项。
