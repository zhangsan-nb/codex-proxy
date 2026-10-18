<p align="center">
  <svg width="120" height="120" viewBox="0 0 120 120" role="img" aria-label="OpenAPI Multi-Key Proxy logo">
    <defs><linearGradient id="g" x1="0" x2="1"><stop stop-color="#2563eb"/><stop offset="1" stop-color="#7c3aed"/></linearGradient></defs>
    <rect x="6" y="6" width="108" height="108" rx="28" fill="url(#g)"/>
    <path d="M30 42h60M30 60h60M30 78h36" stroke="#fff" stroke-width="8" stroke-linecap="round"/>
    <circle cx="82" cy="78" r="10" fill="#fbbf24"/>
  </svg>
</p>

<h1 align="center">OpenAPI Multi-Key Proxy</h1>
<p align="center"><strong>Protocol conversion, intelligent Key scheduling, and an observable AI gateway.</strong><br/>多协议转换、智能 Key 调度与可观测 AI 网关</p>

<p align="center">
  <a href="https://github.com/aipayim/codex-proxy/releases"><img src="https://img.shields.io/github/v/release/aipayim/codex-proxy?display_name=tag&sort=semver&style=flat-square&label=release" alt="latest release"></a>
  <a href="https://github.com/aipayim/codex-proxy/stargazers"><img src="https://img.shields.io/github/stars/aipayim/codex-proxy?style=flat-square&logo=github" alt="GitHub stars"></a>
  <a href="https://github.com/aipayim/codex-proxy/network/members"><img src="https://img.shields.io/github/forks/aipayim/codex-proxy?style=flat-square&logo=github" alt="GitHub forks"></a>
  <a href="https://github.com/aipayim/codex-proxy/issues"><img src="https://img.shields.io/github/issues/aipayim/codex-proxy?style=flat-square" alt="open issues"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="MIT license"></a>
</p>

<p align="center">
  <a href="#about">About</a> ·
  <a href="#highlights">Highlights</a> ·
  <a href="#quick-start">Quick Start</a> ·
  <a href="#evolution">Evolution</a> ·
  <a href="#documentation">Documentation</a>
</p>

<p align="center">🌐 <a href="README_EN.md">English Full README</a> · <a href="README_CN.md">中文完整版</a></p>

## About

OpenAPI Multi-Key Proxy is a local, transparent gateway for AI coding clients and API applications. It accepts OpenAI Responses, OpenAI Chat Completions, and Anthropic Messages traffic, then routes or converts requests to heterogeneous upstream providers without forcing every client to speak the same protocol.

It is designed for operators who need more than a configuration switcher: multiple API Keys, health-aware scheduling, cooldown and recovery, protocol adaptation, model mapping, queues, dashboards, metrics, and safe process supervision in one small Node.js service.

## Highlights

| | Capability | What it provides |
|---|---|---|
| 🔁 | **3×3 protocol matrix** | Responses ↔ Chat ↔ Messages conversion, including streaming terminal-state protection |
| 🧠 | **Model-aware routing** | Runtime upstream model probing and nearest compatible model adaptation |
| 🎯 | **Intelligent Key scheduling** | Reset-period priority, health score, cooldown, latency, round-robin, groups, and direct `#N` routing |
| 🛡️ | **Resilience controls** | Quota classification, transient capacity backoff, request queues, auto-lock, auto-recovery, and no-progress watchdogs |
| 📊 | **Operations dashboard** | Key cards, trends, logs, events, cost estimates, concurrency ticker, WebSocket updates, and Prometheus metrics |
| 🧩 | **Task Insight** | Optional local task-session aggregation, tool/file signals, usage, cost, and LLM distillation |
| 🔐 | **Safe administration** | In-memory Dashboard auth, authenticated management APIs, graceful restart, drain/cancel/force controls |
| 🌍 | **Multi-port groups** | Isolated Key pools for daily, advanced, and low-cost model tiers |

## Architecture

<div align="center">

```text
  Codex CLI       Claude Code       Chat clients       SDKs
       \              |                  |               /
        +-------------+------------------+--------------+
                              |
                 OpenAPI Multi-Key Proxy
             auth · routing · queues · observability
                              |
       +----------------------+----------------------+
       |                      |                      |
   Responses API        Chat Completions        Messages API
       |                      |                      |
       +----------------------v----------------------+
        OpenAI · Anthropic · DashScope · relays · custom upstreams
 ```

| Downstream \ Upstream | Responses | Chat | Messages |
|---|:---:|:---:|:---:|
| **Responses** | ✅ pass-through | 🔄 convert | 🔄 convert |
| **Chat** | 🔄 convert | ✅ pass-through | 🔄 convert |
| **Messages** | 🔄 convert | 🔄 convert | ✅ pass-through |

</div>

<p align="center"><img src="https://skillicons.dev/icons?i=nodejs,js,bash,python,git&theme=light" alt="Node.js JavaScript Bash Python Git" /></p>

## Quick Start

### 1. Install

```bash
git clone https://github.com/aipayim/codex-proxy.git
cd codex-proxy
bash install.sh
```

### 2. Configure Keys

Edit the local `keys.json` created by the installer. Runtime configuration and credentials are intentionally local files and are not part of the public repository.

### 3. Start and Open the Dashboard

```bash
bash start-proxy.sh
curl http://localhost:3456/__status
```

Open `http://localhost:3456/` in a browser. The dashboard defaults to Chinese and can switch to English from its language control; the browser remembers the selection.

### 4. Connect a Client

Point an OpenAI-compatible client at `http://localhost:3456/v1`. Add groups such as `B=3457` or `C=3458` when different Key pools should serve different model tiers.

## Evolution

This timeline records every tagged release from `v2.26.0` through `v2.61.0`. Each entry intentionally keeps an English and Chinese explanation together so the public landing page remains English-first without losing release context for Chinese readers.

### v2.61.0 🛡️ Static Chat-native whitelist, tool_choice degrade, and stream terminal fixes
- 🇬🇧 **English:** Added a static Chat-native host whitelist (`CHAT_NATIVE_HOSTS`, e.g. `anytokens.cc`) so Chat-only relays are always routed as `chat` and can never be misrouted as Messages/Responses by the dynamic protocol cache; a confirmed protocol now survives transient probe failures; broadened `unsupported_path` matching with `only allows access to` so non-inference-path 403s no longer poison `reset:"never"` keys; request converters drop `tool_choice` when `tools` is absent/empty (strict-relay 400), converted streams auto-degrade `reasoning_effort=none` once for the tools+reasoning rejection and surface real 4xx errors instead of fake EOF terminals; pure tool-call responses no longer emit an empty `response.output_text.done`; license unified to MIT with a LICENSE file; regression tests aligned.
- 🇨🇳 **中文：** 新增静态 Chat-only 中继白名单（`CHAT_NATIVE_HOSTS`，如 `anytokens.cc`），此类主机恒定按 `chat` 路由，动态协议缓存误判也无法将其改道为 Messages/Responses；瞬时探测失败不再覆盖已确认协议；`unsupported_path` 正则追加 `only allows access to`，非推理路径 403 不再毒化 `reset:"never"` Key；四个请求转换器在无 `tools`/`tools:[]` 时同步丢弃 `tool_choice`（避开 strict 上游 400），转换流遇 tools+reasoning 拒绝时同 Key 自动降级 `reasoning_effort=none` 重试一次，其余 4xx 直接返回真实上游错误而非假 EOF 终态；纯工具调用响应不再发空 `response.output_text.done`；许可证统一为 MIT 并新增 LICENSE；回归测试对齐。
- 🔗 [Full changelog](README_CN.md#更新日志)
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.60.1 🧯 Queue drain re-entrancy guard
- 🇬🇧 **English:** Fixed a queue drain re-entrancy guard so concurrent drain triggers cannot double-process the same queued request, and aligned the capacity-backoff lifecycle test with the new guard.
- 🇨🇳 **中文：** 修复队列排水重入保护，防止并发触发排水时同一排队请求被重复处理；同步对齐容量退避生命周期测试。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.60.0 🔧 Messages→Chat tool-call chain fix (Claude Code CLI)
- 🇬🇧 **English:** Fixed the broken tool-call chain when `/v1/messages` (Claude Code CLI) forwards to a Chat upstream: the request-direction converter now preserves full tool context by mapping `tool_use` → assistant `tool_calls`, `tool_result` → `role:"tool"` messages, removing the stray empty `user` message, and mapping `tool_choice` correctly (`any` → `"required"`, `tool` → `function`), so multi-turn tool loops never break.
- 🇨🇳 **中文：** 修复 `/v1/messages`（Claude Code CLI）→ Chat 上游时工具调用链断裂：请求方向转换器现完整保留工具上下文，`tool_use` → assistant `tool_calls`、`tool_result` → `role:"tool"` 消息，移除残留空白 user 消息，并正确映射 `tool_choice`（`any` → `"required"`、`tool` → `function`），多轮工具循环不再断裂。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.59.1 📝 README sync for unsupported_path docs
- 🇬🇧 **English:** Synchronized the README documentation for the v2.59.0 `unsupported_path` classification (protocol detection, FAQ, and changelog) across the Chinese full text, English full text, and the landing page.
- 🇨🇳 **中文：** 同步 v2.59.0 `unsupported_path` 错误分类的 README 文档（协议检测说明、FAQ 与更新日志）至中文全文、英文全文与首页。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.59.0 🛡️ unsupported_path error classification protects shared keys
- 🇬🇧 **English:** Added `unsupported_path` error classification: when an upstream rejects a request because the path/protocol is unsupported (e.g. anytokens' `does not allow /v1/messages dispatch`), the error is returned to the caller unchanged but is never written to the Key's `failCode`, so the key is not cooled down, locked, or judged invalid — other tasks sharing the key (local codex, LAN devices) keep working unaffected.
- 🇨🇳 **中文：** 新增 `unsupported_path` 错误分类：上游因路径/协议不支持而拒绝请求时（如 anytokens 的 `does not allow /v1/messages dispatch`），错误如实回传调用方，但**不写入 Key 的 `failCode`**，不冷却、不锁定、不判失效——共享该 Key 的其他任务（本机 codex、局域网设备）不受影响。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.58.1 🧹 Removed sk- prefix validation
- 🇬🇧 **English:** Removed the local `sk-` prefix validation for pasted Keys to stay aligned with the source implementation, so upstream tokens in any format are accepted.
- 🇨🇳 **中文：** 移除粘贴 Key 时本地的 `sk-` 前缀校验，与源码实现对齐，任意格式的上游 token 均可录入。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.58.0 📡 Responses SSE streaming protocol compliance
- 🇬🇧 **English:** Major overhaul of Responses SSE streaming lifecycle: added proper `output_item.added` / `output_item.done` message item lifecycle with `openMessageItem` / `closeMessageItem`, persistent `outputItems` tracking with correct `output_index` assignment, `_itemClosed` flag to prevent duplicate close events, `toolCallNames` / `toolOutputIndices` for tool call tracking, and `item_id` / `output_index` / `content_index` fields in all text delta events. Bare path normalization removed; clients should use standard `/v1/...` paths.
- 🇨🇳 **中文：** 大幅改进 Responses SSE 流式生命周期：新增 `output_item.added` / `output_item.done` 消息项生命周期（`openMessageItem` / `closeMessageItem`）、持久 `outputItems` 追踪与正确 `output_index` 分配、`_itemClosed` 标志防止重复关闭、`toolCallNames` / `toolOutputIndices` 工具调用追踪、所有文本 delta 事件包含 `item_id` / `output_index` / `content_index` 字段。移除裸路径归一化，客户端应使用标准 `/v1/...` 路径。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.7 🛤️ Bare path auto-normalization
- 🇬🇧 **English:** Auto-normalized bare `/responses`, `/messages`, `/chat/completions`, and `/models` requests to their `/v1/...` equivalents.
- 🇨🇳 **中文：** 裸路径 `/responses`、`/messages`、`/chat/completions`、`/models` 自动归一化为 `/v1/...` 等价路径。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.6 🔁 /v1 dedup for probes and models endpoint
- 🇬🇧 **English:** Extended `/v1` path deduplication to protocol probes and the models endpoint, preventing double-`/v1` upstream paths.
- 🇨🇳 **中文：** 将 `/v1` 路径去重扩展至协议探测与模型列表端点，避免上游出现双 `/v1` 路径。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.5 🚫 Double `/v1/v1` forwarding fix
- 🇬🇧 **English:** Prevented double `/v1/v1` paths when forwarding requests upstream.
- 🇨🇳 **中文：** 修复转发上游时出现 `/v1/v1` 双重路径的问题。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.4 ⚖️ Round-robin selection cleanup
- 🇬🇧 **English:** Simplified round-robin Key selection and removed the redundant fallback branch.
- 🇨🇳 **中文：** 简化轮询选 Key 逻辑，移除冗余的回退分支。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.3 🧹 LAN auth dedup and probe filtering
- 🇬🇧 **English:** Removed the duplicate LAN auth middleware and filtered 403/402 responses out of protocol probing so dead endpoints are not misclassified.
- 🇨🇳 **中文：** 移除重复的局域网认证中间件，并在协议探测中过滤 403/402 响应，避免失效端点被误判。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.2 🔐 Server-side LAN API key enforcement
- 🇬🇧 **English:** Added the missing LAN auth middleware so the server-side API key is actually enforced for LAN clients.
- 🇨🇳 **中文：** 补上缺失的局域网认证中间件，服务端 API 密钥对局域网客户端真正生效。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.1 🔑 Restart-status auth fix
- 🇬🇧 **English:** Added admin authentication to the restart-status fetch endpoint.
- 🇨🇳 **中文：** 为重启状态查询接口补充管理员认证。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.57.0 🌐 LAN access mode
- 🇬🇧 **English:** Added network mode setting (localhost / LAN) and LAN API key protection, allowing other devices on the same LAN to use this proxy for AI requests. Dashboard settings panel includes mode selection and key input; saving automatically restarts all port groups to apply the new bind address. LAN clients authenticate via Bearer token constant-time comparison. Local requests always bypass the LAN key.
- 🇨🇳 **中文：** 新增网络模式设置（仅本机 / 局域网可用）与 LAN 连接密码保护，允许同一局域网内其他设备通过本机代理访问 AI 模型。Dashboard 设置面板提供网络模式切换和密码输入，保存后自动重启所有端口组以切换监听地址（localhost → 0.0.0.0）。局域网客户端通过 Bearer token 恒时比较进行认证，本机请求不受密码限制。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.56.0 🛡️ Safer update dialog and Release notes
- 🇬🇧 **English:** Added conditional safe-upgrade guidance, explicit sensitive-file backup warnings, and escaped restricted Markdown rendering for GitHub Release notes in the Dashboard.
- 🇨🇳 **中文：** 新增按版本状态显示的安全升级指引、敏感文件备份警告，并为 Dashboard GitHub Release 说明加入 HTML 转义的受限 Markdown 渲染。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.55.2 🌐 Dashboard localization fix
- 🇬🇧 **English:** Restored localized Dashboard labels for batch Key actions, durations, task insight, database maintenance, group controls, and resume-project controls in both Chinese and English.
- 🇨🇳 **中文：** 恢复批量 Key 操作、时长、任务洞察、数据库维护、分组控制和恢复项目控制等 Dashboard 文案的中英文国际化显示。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.55.1 📝 README release-status and link fix
- 🇬🇧 **English:** Corrected the published-release wording and replaced the broken Release-asset link to `build-release.js` with a source-repository documentation link.
- 🇨🇳 **中文：** 修正已发布版本状态说明，并将 Release 资产中不存在的 `build-release.js` 链接改为源码仓库文档链接。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.55.0 🌐 Dashboard bilingual interface
- 🇬🇧 **English:** Added Chinese/English Dashboard resources, a top-right language switch, and browser-local persistence of the selected language. Chinese remains the default and changing the language does not require a proxy restart.
- 🇨🇳 **中文：** Dashboard 新增中文/English 双语资源、右上角语言切换按钮和浏览器本地语言记忆。默认仍为中文，切换语言不需要重启代理。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.54.0 🚀 Stop-reason tracking and cost trends
- 🇬🇧 **English:** Added end-to-end `stopReason` tracking for truncated Chat and Responses streams, plus the cost-trend fix that restores correct bar scaling.
- 🇨🇳 **中文：** 新增 Chat/Responses 截断流的 `stopReason` 全链路跟踪，并修复费用趋势图柱状图比例与显示问题。
- 🔗 [Full changelog](README_CN.md#更新日志)

### v2.53.1 🧾 Unified request/actual model display
- 🇬🇧 **English:** Unified request-model and actual-upstream-model labels across logs, trends, model statistics, and cost estimation.
- 🇨🇳 **中文：** 统一日志、趋势图、模型统计和费用估算中的请求模型与实际模型显示口径。

### v2.53.0 🔀 Full protocol matrix
- 🇬🇧 **English:** Added the 3×3 protocol adaptation matrix, dynamic upstream protocol discovery, and protocol-aware Chat fallback.
- 🇨🇳 **中文：** 新增 3×3 全协议适配矩阵、上游协议动态探测及协议感知的 Chat 回退。

### v2.52.0 🧠 Model adaptation
- 🇬🇧 **English:** Added upstream capability probing and nearest compatible model-name mapping.
- 🇨🇳 **中文：** 新增上游模型能力探测与模型名就近映射，自动适配客户端与上游模型差异。

### v2.51.1 🧹 Discussion form cleanup
- 🇬🇧 **English:** Fixed stale fields remaining in the GitHub Discussions session-flow form.
- 🇨🇳 **中文：** 修复 GitHub 会话流发起表单残留字段问题。

### v2.51.0 💬 GitHub Discussions session flow
- 🇬🇧 **English:** Added Dashboard browsing, caching, posting, replying, and fallback handling for repository Discussions.
- 🇨🇳 **中文：** 新增 Dashboard GitHub Discussions 浏览、缓存、发帖、回复及降级处理能力。

### v2.50.1 📦 Self-contained Release dependency
- 🇬🇧 **English:** Bundled the `ws` dependency into Release assets so an extracted package can run immediately.
- 🇨🇳 **中文：** Release 资产内置 `ws` 依赖，解压后即可运行。

### v2.50.0 🧯 WebSocket backpressure and OOM governance
- 🇬🇧 **English:** Throttled and merged Dashboard snapshots, capped WebSocket buffers, and added reconnect recovery.
- 🇨🇳 **中文：** 新增 Dashboard 状态推送节流合并、WebSocket 发送背压上限和自动重连恢复，治理后台标签页 OOM。

### v2.49.0 🧮 Quota-aware recovery
- 🇬🇧 **English:** Exempted quota-exhausted Keys from false auto-recovery and hardened 429 classification.
- 🇨🇳 **中文：** 自动恢复跳过配额耗尽 Key，并加固 429/配额错误分类，避免误恢复后再次失败。

### v2.48.0 ⏱️ No-progress stream watchdog
- 🇬🇧 **English:** Added a no-progress timeout for accepted coding streams and fixed quota-429 queue behavior.
- 🇨🇳 **中文：** 新增编码流无进展看门狗，并修复配额 429 导致的队列风暴与长期挂起。

### v2.47.0 ♻️ Controlled autoResume stall restart
- 🇬🇧 **English:** Added verified runner-stall detection and one controlled restart per idle period.
- 🇨🇳 **中文：** 新增恢复 runner 停滞检测、身份校验及每个闲置周期一次的受控重启。

### v2.46.0 🗄️ SQLite log maintenance
- 🇬🇧 **English:** Added optional Codex SQLite maintenance with bounded cleanup, validation, and busy-database yielding.
- 🇨🇳 **中文：** 新增可选 Codex SQLite 日志维护，支持结构校验、限量清理和数据库忙时让步。

### v2.45.1 ⚡ SSE capacity backoff
- 🇬🇧 **English:** Treated explicit `model_at_capacity` SSE errors as transient capacity backoff instead of hard Key failure.
- 🇨🇳 **中文：** 将 SSE 中明确的 `model_at_capacity` 按瞬时容量退避处理，不再误判为 Key 硬失败。

### v2.45.0 🧷 Stream terminal consistency
- 🇬🇧 **English:** Added consistent terminal-state protection for Responses, Messages, and bidirectional conversions.
- 🇨🇳 **中文：** 统一 Responses、Messages 及双向转换流的终态、EOF、错误和超时保护。

### v2.44.0 🔎 Task Insight
- 🇬🇧 **English:** Added optional task-session aggregation, tool/file signals, usage tracking, reports, and LLM distillation.
- 🇨🇳 **中文：** 新增可选任务洞察，支持任务会话、工具/文件信号、用量、报告和 LLM 蒸馏。

### v2.43.0 🔒 Safe autoResume leases
- 🇬🇧 **English:** Replaced directory scanning with verifiable runner leases and added deterministic fixed-session resume.
- 🇨🇳 **中文：** 用可验证 runner 租约替代目录扫描，并新增固定会话模式，避免误杀和恢复错会话。

### v2.42.0 🌙 Off-peak scheduling preview
- 🇬🇧 **English:** Added live preview support for Key timezone and off-peak scheduling windows.
- 🇨🇳 **中文：** 新增 Key 时区与错峰时间窗口的面板实时预览。

### v2.41.0 🏷️ Source release baseline
- 🇬🇧 **English:** Added `release-baseline.txt` provenance detection for source installations without build metadata.
- 🇨🇳 **中文：** 新增 `release-baseline.txt`，使无构建元数据的源码安装也能识别版本基线。

### v2.40.0 🛠️ Capacity resilience and TTY recovery
- 🇬🇧 **English:** Added transient capacity backoff, bounded queue waiting, PTY-backed autoResume, and safer retry behavior.
- 🇨🇳 **中文：** 新增容量瞬态退避、队列等待上限、PTY 闲置恢复及更安全的重试行为。

### v2.39.1 🪟 WSL launcher path fix
- 🇬🇧 **English:** Converted WSL paths to Windows form before launching visible terminals through `cmd.exe`.
- 🇨🇳 **中文：** 通过 `cmd.exe` 启动可见终端前自动将 WSL 路径转换为 Windows 格式。

### v2.39.0 💰 Model-level pricing
- 🇬🇧 **English:** Added per-model input/output pricing and token-density estimates, plus Codex SQLite maintenance configuration.
- 🇨🇳 **中文：** 新增模型级输入/输出价格、Token 密度估算及 Codex SQLite 日志维护配置。

### v2.38.0 📱 Downstream application trends
- 🇬🇧 **English:** Aggregated Dashboard trends by downstream application and fixed an undefined-client crash loop.
- 🇨🇳 **中文：** 趋势图按下游应用归集，并修复 `client` 未定义导致的崩溃循环。

### v2.37.0 🧹 Runtime data governance
- 🇬🇧 **English:** Added bounded state/log retention, log rotation, upstream error capture, and atomic recovery paths.
- 🇨🇳 **中文：** 新增状态与日志容量治理、日志轮转、上游错误捕获及原子恢复路径。

### v2.36.2 🕒 Idle baseline correction
- 🇬🇧 **English:** Corrected the active-to-idle baseline used by idle-resume timing.
- 🇨🇳 **中文：** 修正闲置恢复从活跃状态切换到闲置状态时的计时基线。

### v2.36.1 ⏲️ Precise idle timing
- 🇬🇧 **English:** Improved live idle countdown precision and completed related `.gitignore` coverage.
- 🇨🇳 **中文：** 提升闲置倒计时精度与实时显示，并完善相关 `.gitignore` 规则。

### v2.36.0 🧾 Log query workers
- 🇬🇧 **English:** Added Worker-based log queries, summaries, event handling, pagination, and exports.
- 🇨🇳 **中文：** 新增 Worker 日志查询、汇总、事件处置、分页和导出能力。

### v2.35.0 📈 Cost and latency trends
- 🇬🇧 **English:** Added cost and latency trend charts with tab-based trend selection.
- 🇨🇳 **中文：** 新增费用与延迟趋势图，以及 Tab 式趋势选择器。

### v2.34.2 🔗 Documentation maintenance
- 🇬🇧 **English:** Updated the documented affiliate link without changing proxy runtime behavior.
- 🇨🇳 **中文：** 更新文档中的推广链接，不改变代理运行行为。

### v2.34.1 🧰 Log performance fixes
- 🇬🇧 **English:** Improved log performance and fixed several Dashboard and lifecycle edge cases.
- 🇨🇳 **中文：** 优化日志性能，并修复多项 Dashboard 与生命周期边界问题。

### v2.34.0 💚 Health trend chart
- 🇬🇧 **English:** Added health trends with 200/4xx/5xx/failure status-code tracking.
- 🇨🇳 **中文：** 新增健康趋势图，跟踪 200、4xx、5xx 与失败状态码。

### v2.33.0 💓 Key heartbeat and watchdog reload
- 🇬🇧 **English:** Made idle-resume decisions depend on persisted actual-Key heartbeats and enabled safe watchdog reloads.
- 🇨🇳 **中文：** 闲置恢复改为依赖实际 Key 持久化心跳，并支持 watchdog 安全重载。

### v2.32.0 🛑 Cancellable restart
- 🇬🇧 **English:** Added restart cancellation, forced-restart confirmation, and active-stream interruption diagnostics.
- 🇨🇳 **中文：** 新增重启取消、强制重启二次确认及活跃流中断诊断。

### v2.31.0 ✅ Responses terminal fix
- 🇬🇧 **English:** Corrected Responses completion handling for `[DONE]`, EOF, close, error, and timeout cases.
- 🇨🇳 **中文：** 修复 Responses 流 `[DONE]`、EOF、关闭、错误和超时场景下的终态处理。

### v2.30.1 🧠 In-memory admin Token
- 🇬🇧 **English:** Kept the Dashboard admin Token in page memory and unified HTTP/WebSocket authentication behavior.
- 🇨🇳 **中文：** 管理 Token 改为页面内存态，并统一 HTTP 与 WebSocket 认证行为。

### v2.30.0 📦 Release toolchain
- 🇬🇧 **English:** Introduced provenance-aware Release building, build metadata, SHA-256 manifests, and safer upgrade guidance.
- 🇨🇳 **中文：** 引入发布来源识别、构建元数据、SHA-256 清单和更安全的升级流程。

### v2.29.1 🔐 WebSocket and metrics hardening
- 🇬🇧 **English:** Protected WebSocket and `/metrics` endpoints and bounded watchdog drain timeout.
- 🇨🇳 **中文：** 加固 WebSocket 与 `/metrics` 认证，并限制 watchdog 排空超时时间。

### v2.29.0 🛡️ Admin hardening
- 🇬🇧 **English:** Hardened admin authentication, added weekly reset-day filtering, and tracked tool-call IDs.
- 🇨🇳 **中文：** 加固管理认证，新增每周重置日筛选，并跟踪工具调用 ID。

### v2.28.0 🔑 Admin authentication
- 🇬🇧 **English:** Added admin Token authentication, tool-call input conversion, and drain-on-restart behavior.
- 🇨🇳 **中文：** 新增管理 Token 认证、工具调用输入转换及重启排空机制。

### v2.27.0 🧰 Tool-call streaming
- 🇬🇧 **English:** Added tool-call SSE events, configurable stream lifetime, and graceful restart support.
- 🇨🇳 **中文：** 新增工具调用 SSE 事件、可配置流生命周期和优雅重启。

### v2.26.1 🧯 Watchdog graceful shutdown
- 🇬🇧 **English:** Fixed lifecycle failure marking and added graceful SIGTERM followed by SIGKILL watchdog shutdown.
- 🇨🇳 **中文：** 修复生命周期失败标记，并新增 watchdog 先 SIGTERM 后 SIGKILL 的优雅关闭流程。

### v2.26.0 🏗️ Responses lifecycle system
- 🇬🇧 **English:** Introduced the Responses conversion lifecycle system and overhauled watchdog supervision.
- 🇨🇳 **中文：** 引入 Responses 转换生命周期系统，并全面重构 watchdog 守护机制。

## Documentation

- 📘 [Full English README](README_EN.md)
- 📙 [完整中文 README](README_CN.md)
- 🧪 [Source repository and release build guide](https://github.com/aipayim/codex-proxy/blob/main/README_EN.md#source-repository-and-release-assets)
- 🌐 [Official site](https://openapi.im)
- 🐛 [Issues and discussions](https://github.com/aipayim/codex-proxy/issues)
- 🐦 [Author: @C_2049s](https://twitter.com/C_2049s)

## Security Boundary

The public source repository contains code, documentation, tests, and build tooling. Local `keys.json`, `config.json`, `state.json`, logs, PID files, backups, and auto-resume runtime state are local runtime data and must never be committed or copied into Release assets.

## License

MIT

<p align="right"><a href="#about">back to top ↑</a></p>
