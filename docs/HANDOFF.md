# TrendRadar paibao.ai 试点部署交接

## 部署状态

| 项目 | 值 |
|------|-----|
| GitHub 仓库 | https://github.com/iPythoning/TrendRadar-paibao-pilot |
| Cloudflare Pages 项目 | `trendradar-paibao-pilot` |
| Pages 默认域名 | https://trendradar-paibao-pilot.pages.dev |
| 自定义域名 | https://radar.paibao.ai |
| CF Account ID | `1649a7519a5895b9120c661e7063ad7a` |
| 时区 | Asia/Shanghai |
| 调度系统 | 已启用（morning_evening 预设） |
| AI 分析/翻译 | 试点阶段已关闭，避免必须配置 AI_API_KEY |

## 已配置 GitHub Secrets

- `CLOUDFLARE_API_TOKEN`：存储于本机 vault `CF_API_TOKEN_CANDIDATE_REPOS`
- `CLOUDFLARE_ACCOUNT_ID`：`1649a7519a5895b9120c661e7063ad7a`
- `CLOUDFLARE_PROJECT_NAME`：`trendradar-paibao-pilot`

## 关键配置变更

1. `config/config.yaml`
   - `schedule.enabled: true`
   - `report.mode: daily`
   - `ai_analysis.enabled: false`
   - `ai_translation.enabled: false`

2. `config/frequency_words.txt`
   - 保留原有默认词组
   - 新增试点业务关注词组：`[汽车出口]`、`[AI 获客]`

3. `README.md` / `README-EN.md`
   - 将 Slack Webhook URL 示例替换为占位符，避免 GitHub push protection 拦截

## 部署验证

- 首次 GitHub Actions run：`https://github.com/iPythoning/TrendRadar-paibao-pilot/actions/runs/33380273716`
- 状态：✅ success（58s）
- Cloudflare Pages 部署：✅ success
- 自定义域名 `radar.paibao.ai`：✅ active（validation + verification 均 active）

## 试运行说明

- Actions 按 `.github/workflows/crawler.yml` 每小时第 33 分钟自动运行。
- 每次运行会抓取热榜 → 生成 `index.html` → 自动部署到 Cloudflare Pages。
- 当前未配置通知渠道和远程存储，报告数据仅在每次运行内有效；如需跨运行持久化，需配置 Cloudflare R2（见 README 远程存储章节）。

## 后续扩展

1. **复制到其他客户域名**：
   - fork 本仓库并重命名
   - 修改 `CLOUDFLARE_PROJECT_NAME`
   - 在 CF Pages 项目中添加新的自定义域名（如 `radar.autoglobalai.com`）
   - 在对应 zone 添加 CNAME 到 `<项目>.pages.dev`
   - 调整 `config/frequency_words.txt` 中的关键词

2. **启用 AI 分析**：
   - 配置 `AI_API_KEY`、`AI_MODEL` 等 Secrets
   - 将 `config/config.yaml` 中 `ai_analysis.enabled` 和 `ai_translation.enabled` 改回 `true`

3. **配置 R2 远程存储**：
   - 在 Cloudflare 创建 R2 bucket 和 API token
   - 添加 `S3_BUCKET_NAME`、`S3_ACCESS_KEY_ID`、`S3_SECRET_ACCESS_KEY`、`S3_ENDPOINT_URL` 到 GitHub Secrets

## 注意事项

- CF API Token 曾在对话中出现过，建议到 Cloudflare Dashboard 轮换一次。
- 当前仓库建在 GitHub 账号 `iPythoning` 下；如需迁移到组织账号或其他个人账号，使用 GitHub Transfer repository 功能即可。
