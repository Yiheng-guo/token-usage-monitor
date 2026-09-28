# Token洞察 · Token Usage Insight

[![CI](https://github.com/Yiheng-guo/token-usage-monitor/actions/workflows/ci.yml/badge.svg)](https://github.com/Yiheng-guo/token-usage-monitor/actions/workflows/ci.yml)
[![MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

一个本地优先的 Codex Token 监测器与 macOS 菜单栏应用。它在 [w93139/token-usage-monitor](https://github.com/w93139/token-usage-monitor) 的 MIT 开源项目基础上开发；原作者版权声明保留在 [LICENSE](LICENSE)。

## 改版增加了什么

- **7／30 天 API 用量分析**：按天绘制实际收到的 API `usage`，显示调用次数和用量最高的渠道／模型组合。
- **隐私友好的 CSV 导出**：只导出按天、渠道、模型汇总的计数，不包含任务名称、请求 ID、对话正文或密钥。模型名称会经过 CSV 公式转义。
- **清楚的数据口径**：Codex 订阅额度、本地设置的 API Token 预算、已收到的 API 用量和任务累计 Token 分开显示。未上报的 API 调用不会被估算为零。
- **与原版并行运行**：本应用使用独立的数据目录、应用标识和本机 `47822` 端口。已有原版 API 记录不会自动复制；Codex 本地任务数据仍从 Codex 自身读取。

原版已有的菜单栏余量、额度窗口、刷新倒计时、14 天 Codex 用量图、任务搜索、上下文快照、阈值通知和自定义渠道继续保留。

## 运行与构建

要求 Apple Silicon Mac、macOS 13+、本地 Codex/ChatGPT 登录环境。可以从[本仓库最新 Release](https://github.com/Yiheng-guo/token-usage-monitor/releases/latest)下载 `Token-Insight-macOS-arm64-v1.8.0.zip`，解压并将 `Token洞察.app` 移入“应用程序”。首次打开时，macOS 可能要求在“系统设置 → 隐私与安全性”中确认。

也可以使用 Swift 工具链从源码构建：

```bash
macos/TokenUsageMonitor/scripts/build_app.sh
open 'macos/TokenUsageMonitor/dist/Token洞察.app'
```

应用为本地 ad-hoc 签名；公开下载若需免除 Gatekeeper 手动确认，仍需要 Apple Developer ID 签名和公证。当前应用默认关闭自动更新检查，可在设置中开启。更新链接指向此 Fork 的 Releases。

Python 测试与 Swift 模型检查：

```bash
python3 -m unittest discover -s scripts/tests -v
macos/TokenUsageMonitor/scripts/test_models.sh
```

## API 用量接入

应用运行时将真实 API 响应中的 `usage` 元数据发送到：

```text
POST http://127.0.0.1:47822/v1/usage
Content-Type: application/json
```

```json
{
  "provider": "openai",
  "model": "your-model",
  "request_id": "unique-response-id",
  "usage": {"input_tokens": 120, "output_tokens": 30, "total_tokens": 150}
}
```

这是接收端，不会自动拦截 API 流量。只有客户端实际提交的响应计数才会入库；流式调用需要最终 `usage` 和稳定的 `request_id` 才能去重。不要发送提示词、回复、API Key 或上传文件。详见 [API 接入说明](docs/API_INTEGRATION.md)。

数据目录：macOS 为 `~/Library/Application Support/Token Usage Insight/`；Linux 插件为 `${XDG_DATA_HOME:-~/.local/share}/token-usage-insight/`。端口只绑定 `127.0.0.1`。

## 口径与限制

- Codex 订阅额度按服务返回的百分比和刷新时间显示，不换算成虚构的 Token 总额。
- 任务累计 Token 来自本地 Codex 数据库，可能包含重复计入的历史输入；不能将任务总量解释为某一天的增量。
- 上下文卡片显示最近一次可读取的运行时快照，不保证与当前正在输入的上下文完全同步。
- API 预算是用户填写的本地目标，不是供应商余额或账单；API 趋势只覆盖已收到的上报。
- CSV 中可能含自定义渠道名和模型名。分享文件前请检查这些名称。

## 开源与安全

遵循 [MIT License](LICENSE)。欢迎通过 [Issue](https://github.com/Yiheng-guo/token-usage-monitor/issues) 反馈功能，通过仓库的 [私密漏洞报告](https://github.com/Yiheng-guo/token-usage-monitor/security/advisories/new) 报告安全问题。请勿在公开 Issue 中粘贴密钥或私人用量数据。参见 [贡献指南](CONTRIBUTING.md) 与 [安全说明](SECURITY.md)。
