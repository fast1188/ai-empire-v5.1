# AI Empire Factory v5.1 — 部署指南

> 一份文档，5 分钟跑通 v5.1 全自动系统（每天 02:00 cron 自动生成 + 推 GitHub + 触发 Vercel）。

---

## 第一步：在 GitHub 仓设置 Secrets

浏览器开 `https://github.com/fast1188/ai-empire-v5.1/settings/secrets/actions` → **New repository secret**

| Secret 名 | 值 | 必填 | 怎么拿 |
|-----------|----|------|--------|
| `GH_PUSH_TOKEN` | `ghp_你的token` | ✅ 必填 | https://github.com/settings/tokens → Generate new token (classic) → 勾 `repo` + `workflow` |
| `ANTHROPIC_AUTH_TOKEN` | `sk-cp-你的key` | ⚠️ 强烈推荐 | 从你 `~/.claude/settings.json` 抄（MiniMax 官方） |
| `OPENAI_API_KEY` | `sk-你的key` | ❌ 可选 | OpenAI 备用，ANTHROPIC 不通时 fallback |
| `VERCEL_DEPLOY_HOOK` | `https://api.vercel.com/...` | ❌ 可选 | Vercel 项目 → Settings → Git → Deploy Hook |

**没有 token** 时，cron 跑会失败但不会破坏（脚本会报错然后停）。

---

## 第二步：第一次手动跑（验证）

浏览器开 `https://github.com/fast1188/ai-empire-v5.1/actions` → 选 "AI Empire Factory v5.1 Deploy" → **Run workflow** → 选 main → 点绿色按钮

**看 30 秒**：
- ✅ 绿色 = 通
- ❌ 红色 = 看 log，多半是 Secret 没设对

跑通后会：
1. 生成 1 个 `v51-auto-2026-06-XX/` 子目录（带 README + index.html + v51_info.json）
2. 自动 commit + push 到当前仓
3. （如果设了 VERCEL_DEPLOY_HOOK）触发 Vercel 部署

---

## 第三步：等自动跑（02:00 UTC = 10:00 北京时间）

cron `0 2 * * *` 每天触发。但 GitHub Actions **对 fork/公开仓的 cron 有延迟**（5-30 分钟），别慌。

**看 log**：仓 → Actions 标签 → 每次运行有详细 log。

---

## 怎么停 / 暂停

1. 暂停自动跑：仓 → Actions → 选 workflow → 右上角 "..." → Disable workflow
2. 立即停：直接点运行的 "Cancel workflow" 按钮
3. 改时间：编辑 `.github/workflows/v5_1_deploy.yml` 改 cron 表达式

---

## 故障排查

| 症状 | 原因 | 修法 |
|------|------|------|
| 跑失败 "Bad credentials" | GH_PUSH_TOKEN 无效或过期 | 重生 token 更新 secret |
| 跑失败 "rate limit" | GitHub API 60/时（无 token）/ 5000/时（有 token） | 减少 cron 频率 或用 token |
| 跑成功但没生成 | fetch_trending 没数据 | 等 GitHub trending 页面或换关键词 |
| 推送到 fast1188 失败 | 该仓不是 fast1188 名下 | 改 remote URL 或在仓设置加 collaborator |
| Vercel 没部署 | DEPLOY_HOOK 没设 | Vercel 项目设置加 deploy hook |

---

## 设计取舍（v5.1 vs v5.0）

| | v5.0 手工 | v5.1 自动 |
|---|---------|---------|
| 触发 | 你/我手动 | GitHub Actions cron |
| 内容 | 精品手工 | trending 抓取 + AI 选 + 生成骨架 |
| 推送 | 双仓手动 | 1 token 推 GitHub，可选 Gitee |
| 质量 | 高 | 骨架级，需人工 review |
| 适合 | A 档（每天 1 精品）| B 档（每天 1 自动化）|

**每天 2 个 = A 档 1 个（v5.0 流程）+ B 档 1 个（v5.1 自动跑）**

---

## License
MIT © fast118
