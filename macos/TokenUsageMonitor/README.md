# Token洞察 for macOS

Native macOS 13+ menu-bar companion for the Token Usage Insight Codex plugin.

## Version 1.8.0

Adds 7/30-day API response usage insights by provider and model, plus aggregate-only CSV export. The fork uses its own data directory, loopback port 47822, and bundle identifier so it can coexist with upstream.

## Upstream 1.7.1

Context switching now uses one searchable history list, excluding the selected task. Automatic mode follows the latest updated non-archived task; manual history selection stays fixed until returning or disappearing from available records. Same-name tasks retain separate short IDs. Context reads run independently and discard obsolete requests during rapid switching.

Task cumulative counters and last-reported context are shown separately. Task/API refresh runs independently of network quota requests, with explicit success and error states. Hover tooltips expose exact values, and animations respect Reduce Motion. Custom relay channels support local budgets, menu-bar selection and per-channel ingestion diagnostics. No API credentials are requested and changing a base URL alone does not integrate a client.

Context reads only usage/reset metadata from a bounded in-memory tail of the selected local Codex log. It is a last-call snapshot; stale, missing and reset data are labeled rather than inferred from cumulative usage.

## Features

- Live Codex remaining-quota percentage and reset countdown
- White circular remaining-quota indicator in the menu bar and quota cards
- Exact consumed percentage beneath each remaining-quota bar
- Cached quota fallback with a friendly automatic-retry status during network interruptions
- Product Logo in the panel and macOS application icon
- Daily token chart and account summary
- Mouse-hover details with exact date and Token count on the recent-usage chart
- Automatic panel opening when the app launches
- Header pin switch for a compact always-front quota badge across macOS Spaces
- Readable date ticks, 14-day total, and hover guide in the recent-usage chart
- Automatic GitHub release checks with an in-app update link
- Standard 1024×1024 black graphite application icon with transparent safe margins
- Standard resizable 420×640 macOS window with a 390×540 minimum size
- Local notifications at configurable thresholds
- Detection of scheduled resets and newly granted reset credits
- Manual refresh, local-only storage, and optional launch at login
- Five-second per-task token totals with local conversation titles
- Local usage ingestion for OpenAI, DeepSeek, and compatible API responses
- OpenAI/DeepSeek Token budgets with used and remaining quota displays
- Selectable Codex, OpenAI, or DeepSeek quota source for the menu-bar ring
- No prompt text, response text, email address, or API key persistence

## Build

```sh
./scripts/build_app.sh
```

The finished app is written to `dist/Token洞察.app`. Move it to the
Applications folder and launch it; the remaining-quota percentage will appear in the
menu bar. The app requires a local Codex CLI installation, an authenticated
Codex session, and macOS 13 or newer.

The app is ad-hoc signed for local use. Public distribution requires an Apple
Developer ID signature and notarization.
