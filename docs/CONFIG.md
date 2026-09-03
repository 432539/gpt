# 配置说明（可公开）

本文只记录**字段名、含义、默认值/占位符**。不要把真实密码、API Key、License、JWT/AES、代理账号、邮箱明文、`.env` 实际值、数据库或用户数据提交到仓库。

本地复制模板：`cp .env.example .env`，再改密钥。运行时设置保存在加密后的 `user_settings`（SQLite），API 对密钥只回传 `*_configured`，不回显明文。

---

## 1. 环境变量（`.env`）

根目录 `.env.example` 是唯一可提交的模板。`.env` 已被 gitignore。

### 必填 / 核心

| 变量 | 含义 | 示例/默认（非真实值） |
| --- | --- | --- |
| `PORT` | HTTP 端口 | `8585` |
| `JWT_SECRET` | JWT 签名密钥 | `change_me_to_a_long_random_string` |
| `AES_KEY` | AES-256-GCM，64 位 hex（32 字节）。写入数据后不可更换 | 全 `0` 占位；生产用 `crypto.randomBytes(32)` |
| `GPT_PLUS_DB_PATH` | SQLite 路径 | 默认 `server/data/app.db` |

### 可选：库存 / 探测

| 变量 | 含义 | 默认 |
| --- | --- | --- |
| `NVTOKENS_API_KEY` | nvtokens 库存 API Key；也可在设置页保存 | 空 |
| `NVTOKENS_SESSION` | 价格查询 session（Cookie `scm_session`） | 空 |
| `NVTOKENS_BASE_URL` | nvtokens 站点 | `https://nvtokens.com` |

### 可选：流水线 / 轮询 / 日志

| 变量 | 含义 | 默认 |
| --- | --- | --- |
| `DISABLE_POLLERS` | `1`/`true`/`yes` 时不启动流水线、验活、提链轮询 | 关闭 |
| `OP_LOG_LATEST_LIMIT` | 操作日志最近条数上限 | `100`（最大 5000） |
| `OP_LOG_VERBOSE` | 支付/出码更详细日志 | 关闭 |

### 可选：Cloak 浏览器

| 变量 | 含义 | 默认 |
| --- | --- | --- |
| `CLOAK_PROFILE_ROOT` | 浏览器配置目录 | 代码内 runtime 目录 |
| `CLOAK_RESOURCE_CONCURRENCY` | 资源并发覆盖 | 代码默认 |
| `CLOAK_LAUNCH_CONCURRENCY` | 启动并发 | `5` |
| `CLOAK_PROFILE_CLEANUP_CONCURRENCY` | 配置清理并发 | `4`（最大 32） |

### 可选：提链 HTTP / Python

| 变量 | 含义 | 默认 |
| --- | --- | --- |
| `EXTRACT_PYTHON` | Python 解释器路径 | 系统 `python` |
| `EXTRACT_HTTP_ENGINE` | `impit` 时走 impit 引擎 | 空 |
| `EXTRACT_IMPERSONATE` | TLS 指纹浏览器标识 | 代码内置 |

### 可选：MK_GCASH（开源 GCash 模块）

`MK_GCASH=1`（或 `true`/`yes`）启用独立 GCash 模块；未设置则走原有路径。

| 变量 | 含义 | 默认（上限） |
| --- | --- | --- |
| `MK_GCASH` | 开关 | 关闭 |
| `MK_GCASH_PYTHON` | 模块用 Python，回退 `EXTRACT_PYTHON` | 空 |
| `MK_MAX_CONCURRENCY` | 出码并发 | `4`（上限 20） |
| `MK_MAX_SESSION_CONCURRENCY` | 会话并发 | `min(4, 出码并发)`（上限 20） |
| `MK_MAX_ACCOUNTS` | 账号上限 | `50` |
| `MK_MAX_QUEUE` | 队列 | `50`（上限 200） |
| `MK_EXTRACT_BASE` | 本地提取服务基址（辅助脚本） | `http://127.0.0.1:8595` |
| `GCASH_MAX_CONCURRENCY` | 旧路径并发帽 | 代码常量 |
| `GCASH_MAX_ACTIVE_MONITORS` | 支付墙监视上限 | `12`（上限 24） |
| `GCASH_PAY_DEBUG` | 支付调试 | 关闭 |
| `GCASH_EXTRACT_DEBUG` | 出码调试 | 关闭 |
| `GCASH_EXTRACT_DEBUG_TRACE` | 出码 trace 路径 | 空 |
| `GCASH_QR_SAVE_PATH` / `GCASH_QR_DUMP_DIR` | 二维码落盘（调试） | 空 |
| `SENTINEL_NODE` / `SENTINEL_CURL` / `SENTINEL_SDK_PATH` | Sentinel 相关可执行文件/SDK | 空或内置路径 |

### 可选：Mailpit / mail.com

| 变量 | 含义 | 默认 |
| --- | --- | --- |
| `MAILPIT_RECIPIENTS_ENV` | Mailpit 收件人 env 文件 | `/opt/mailpit/recipients.env` |
| `MAILPIT_SKIP_RESTART` | `1` 时不重启 Mailpit（测试用） | 关闭 |
| `MAILCOM_DEBUG` | mail.com 轮询调试 | 关闭 |
| `MAILCOM_IMPERSONATE` | `chrome142` / `chrome136` / `chrome131` | 空 |
| `MAIL_OAUTH_PUBLIC_SECRET` | mail.com OAuth 公钥覆盖 | 内置占位 |

### 可选：PayPal 地址生成

| 变量 | 含义 | 默认 |
| --- | --- | --- |
| `OPENAI_PAY_ADDRESSGEN_API_URL` | 地址生成 API | 公开默认 URL |
| `OPENAI_PAY_ADDRESSGEN_API_TIMEOUT` | 超时（秒） | `5` |

### 仅部署脚本（不要写入应用 `.env` 并提交）

脚本通过环境变量读取，**值留在本机或 CI secret**。

| 变量 | 用途 |
| --- | --- |
| `DEPLOY_SSH_PASSWORD` | SSH 部署密码 |
| `DEPLOY_REMOTE_ROOT` | 远端目录，默认 `/opt/gpt-plus` |
| `DEPLOY_ALLOW_ACTIVE_REGISTRATION` | `1` 时允许部署时远端仍在自动注册 |
| `BACKUP_DB` | 部署前数据库备份路径 |
| `CFWORKER_MAIL_API_URL` / `CFWORKER_MAIL_API_KEY` / `CFWORKER_MAIL_DOMAINS` | 远端写入 CF 邮箱设置 |
| `CLOAK_LICENSE_KEYS` | 远端写入 Cloak 授权（多行） |
| `APPLY_MAIL_MIX` | `1` 时同时写 mail.com/CF 比例 |
| `CLOUDFLARE_ACCOUNT_ID` / `CLOUDFLARE_API_TOKEN` / `ADMIN_TOKEN` | CF Worker 部署 |
| `CF_MAIL_DOMAINS` | Worker 绑定域名列表（逗号分隔） |

前端可选：`VITE_LOG_ROW_LIMIT`（日志行数，构建时注入）。

---

## 2. 用户设置（Settings / `user_settings`）

源：`server/src/utils/settings.js` 的 `DEFAULTS()`。密钥类字段在 `publicView` 中变成 `*_configured`，不回传原文。

### 2.1 CloakBrowser（设置页「Cloak / 验活」）

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `cloak.license_key` / `license_keys` | 授权，每行一个；加密存储 | 空 |
| `cloak.headless` | 无头 | `true` |
| `cloak.max_concurrency` | 最大并发 | `3` |
| `cloak.humanize` | 拟人鼠标键盘 | `true` |
| `cloak.human_preset` | 拟人预设 | `careful` |

公开视图：`license_configured`、`license_count`，无明文 license。

### 2.2 注册默认（同页）

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `registration.set_password` | 注册时设独立 ChatGPT 密码 | `true` |
| `registration.enable_totp_2fa` | 启用 TOTP | `true` |
| `registration.asset_optimization` | 资源优化 | `true` |
| `registration.mail_provider` | `mailcom` / `cfworker` / `mix` / `herosms` / `imported` | `herosms` |
| `registration.mailcom_share` / `cfworker_share` | mix 比例份数 0–100 | `5` / `5` |
| `registration.performance_profile` | `safe` 或 `throughput` | `safe` |
| `registration.attempts` | 默认业务尝试次数 1–3 | `3` |

### 2.3 账号验活

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `accounts.detect_poll_minutes` | 验活间隔分钟，`0`=关 | `15` |
| `accounts.detect_proxy_group` | 已废弃；验活走 CliProxy 动态池 | `""` |

GB 验活用 region-GB，GC 资格检测用 region-PH。动态池未配置则本轮失败，不回退手工代理组。

### 2.4 流水线上限（`pipeline` 数值项）

开关本身在账号页（见第 3 节）。设置页只改上限：

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `daily_register_limit` | 每天成功入库上限，`0`=不限 | `0` |
| `register_concurrency` | 自动注册并发 1–200 | `5` |
| `extract_attempts` | 每号最多提链次数 1–50 | `5` |
| `extract_interval_minutes` | 同号两次提链最短间隔，`0`=不等 | `10` |
| `pay_attempts` | 支付最多重试 | `5` |
| `rt_attempts` | 拿 RT 最多重试 | `5` |

### 2.5 nvtokens 手工上架

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `nvtokens.auto_list_plus` | 预留自动上架；当前 UI 为手工推送 | `false` |
| `nvtokens.min_price_yuan` | 无看板价时的最低价兜底（元） | `20.00` |
| `nvtokens.api_key` | 库存 Key | 空 |
| `nvtokens.session_cookie` | `scm_session` | 空 |

公开：`api_key_configured`、`session_configured`、`session_last4`。也可被环境变量覆盖。

### 2.6 import_dump（无试用普号 / 全部账号导出）

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `import_dump.url` | 远程导入 API 地址 | 空 |
| `import_dump.api_key` | `X-API-Key` | 空 |

公开仅 `configured`（url 与 key 都非空才为 true）。**不要把真实 url/key 写进文档或仓库。** 不会自动导出，需点设置页按钮。

### 2.7 接码 `sms`

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `provider` / `phone_provider` | 平台标识 | `auto` |
| `api_url` | 接口 | `https://hero-sms.com/stubs/handler_api.php` |
| `api_key` | 密钥 | 空 |
| `country` | 国家码 | `usa` |
| `product` | 业务码 | `openai` |

PayPal 开通任务会固定英国号码与对应服务码，不受此表其它国家影响。

### 2.8 注册供应商

**HeroSMS**（`herosms`）

| 字段 | 默认 |
| --- | --- |
| `api_url` | `https://hero-sms.com` |
| `api_key` | 空 |
| `country` / `service` / `site` / `domain` | `usa` / `openai` / `openai.com` / `outlook.com` |
| `max_reorders` | `4` |
| `acquire_attempts` | `3` |
| `requests_per_second` | `12` |
| `max_request_concurrency` | `16` |
| `max_active_activations` | `20` |
| `ban_cooldown_ms` | `1800000` |
| `rate_cooldown_ms` | `12000` |
| `max_transient_retries` | 公开视图默认 `2` |
| `transient_retry_base_ms` | 公开视图默认 `500` |

**SMSBower**（`smsbower`）

| 字段 | 默认 |
| --- | --- |
| `api_url` | `https://smsbower.app/stubs/handler_api.php` |
| `api_key` | 空 |
| `country` / `service` / `domain` / `max_price` | `gb` / `dr` / `""` / `""` |

**CF Worker 邮箱**（`cfworker_mail`）

| 字段 | 默认 |
| --- | --- |
| `api_url` / `api_key` / `domain` | 空 |
| `disabled_suffixes` / `domains` | `[]` |

公开：`api_key_configured`、`ready`、域名目录与启用数量。

**导入邮箱**（`imported_mail`）

| 字段 | 默认 |
| --- | --- |
| `mailboxes` | 空（明文列表，不回传） |
| `allow_unsafe_url` | `false` |

公开仅 `configured`。

### 2.9 动态代理池

`dynamic_proxy.active_pool` 默认 `1024proxy`。供应商 id：

| id | UI 名 | `sticky_minutes` 默认 |
| --- | --- | --- |
| `1024proxy` | 1024proxy | `30` |
| `b2proxy` | b2proxy | `30` |
| `cliproxy` | CliProxy | `30` |
| `iprocket` | IPRocket | `5` |

每家：`username`、`password`（公开为 `password_configured`）。业务流量（注册/提链/支付/验活/换绑/拿 RT）走当前池；GB→region-GB，GC→region-PH，按任务 sticky SID。

### 2.10 手工代理表（设置页「代理管理」）

库表字段：`group_name`、`proxy_type`（`http` / `https` / `socks5`）、`host`、`port`、`username`、`password`、`remark`、`status`。

用途：兼容旧数据或 mail.com 指定分组。未配置动态池时业务任务失败，**不回退**到本表。导入文本不要提交到 git。

### 2.11 邮箱域名策略

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `email_domains.mode` | `all` / `whitelist` / `blacklist` | `all` |
| `email_domains.allow` / `deny` | 后缀列表 | `[]` |

`suffix_quota.hourly` / `daily`：每后缀每小时/每天额度，`0`=不限。

### 2.12 Mailpit

| 字段 | 含义 | 代码默认（部署请改成本地值） |
| --- | --- | --- |
| `url` | Mailpit HTTP | `http://127.0.0.1:8025` |
| `username` / `password` / `webhook_secret` | 鉴权 | 空 |
| `smtp_host` / `mx_host` / `ui_url` | SMTP / MX / UI | 代码内示例主机名，勿当生产密钥 |
| `domains` | 收信域列表 | `[]` |

### 2.13 mail.com 轮询

| 字段 | 含义 | 默认 |
| --- | --- | --- |
| `oauth_secret` | 一般不填（公钥内置） | 空 |
| `proxy_group` | 空=直连，不回退动态池 | `""` |
| `poll_minutes` | `0`=关闭轮询 | `0` |
| `auto_delete_used` / `auto_repull` | 删已用子号 / 再拉满 | `false` |
| `cap` | 每母号地址上限（含母号） | `10` |
| `enabled_suffixes` / `disabled_suffixes` | 拉子号后缀 | 内置默认目录 / `[]` |
| `unavailable_suffixes` | 平台拒绝记录 | `{}` |

---

## 3. CB / GB / GC 流水线

不是三套独立 env，而是**通道 + 开关**：

| 通道 | UI | 设置对象 | 代理 region |
| --- | --- | --- | --- |
| GB（英国 PayPal） | 账号管理（非 GC） | `pipeline` | `__auto__:GB`（换绑等同） |
| GC（菲律宾 GCash） | 账号管理(GC) | `pipeline_gc` | `__auto__:PH` |
| Cloak（CB） | 设置授权 + 注册任务 | `cloak` + 注册任务 | 动态池 / 任务指定 |

每套开关默认全 `false`：

| 开关 | GB（`pipeline`） | GC（`pipeline_gc`） |
| --- | --- | --- |
| `register` | 持续注册 GB | 持续注册 GC，不启动 PayPal |
| `rebind` | 有试用后换绑 | 有试用后换绑 PH |
| `extract` | 提 PayPal 支付链 | 出 GCash 码（`gcash_*`） |
| `pay` | PayPal 支付 | GCash 支付墙轮询 |
| `acquire_rt` | 支付成功拿 RT | 支付成功拿 RT（PH） |

`global_extract`（全局提链监视，另页）：

| 字段 | 默认 |
| --- | --- |
| `enabled` | `false` |
| `interval_minutes` | `60`（5–1440） |
| `concurrency` | `1`（最大 20，引擎 `PROCESS_CAP=20`） |
| `enabled_channels` | `[]`（公开只给 `enabled_count`） |

---

## 4. 前端设置页标签

`web/src/views/Settings.vue`：

1. 账户 / 密码  
2. Cloak / 验活（含流水线上限、nvtokens、import_dump）  
3. 接码平台  
4. 注册供应商 / 动态代理  
5. 代理管理  
6. 邮箱域名（策略、mail.com/CF 比例、后缀额度、CF 域）  
7. 邮箱轮询 / 后缀  

账号页流水线开关见第 3 节。个人密码修改走 `/api/profile`，不写入 `user_settings`。

---

## 5. 默认管理员（安装后立刻改密）

README：首次可登录 `admin` / `admin123`。生产用户（含任意业务账号）的真实密码**禁止**写入仓库或本文。

---

## 6. 明确不入库

- `.env` 及任何真实密钥、token、license、TOTP 种子  
- `server/data/`（`app.db`、会话、卡哈希等）  
- Cloak runtime 配置目录 `server/src/automation/runtime/`  
- 代理账号密码、import_dump 真实 URL/Key、cloak license 明文  
- `DEPLOY_SSH_PASSWORD`、Cloudflare token、Worker `ADMIN_TOKEN`  
- `web/dist/`、`node_modules/`、日志、`debug/`  

密钥生成示例（只生成随机串，不要提交结果）：

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```
