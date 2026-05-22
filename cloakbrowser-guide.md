# 🥷 CloakBrowser — 隐身 Chromium，通过所有 Bot 检测的 Playwright 替代方案

> **Stealth Chromium that passes every bot detection test.** 不是 JS 注入，不是配置魔改——是一个在 C++ 源码级别修改了指纹的真实 Chromium 二进制文件。反爬系统把它当普通浏览器，因为它**就是**普通浏览器。

---

## 为什么需要 CloakBrowser？

传统的反检测方案有两种：

1. **JS 注入型**（playwright-stealth、puppeteer-extra）— 在页面加载后注入 JS 覆盖指纹属性。每次 Chrome 更新都可能失效，而且**反爬系统本身就能检测到这些注入**。
2. **配置补丁型**（undetected-chromedriver）— 通过启动参数修改部分行为。覆盖面有限，同样随 Chrome 更新频繁失效。

CloakBrowser 走的是第三条路：**49 个 C++ 源码级补丁**，直接修改 Chromium 的 canvas、WebGL、音频、字体、GPU、屏幕属性、WebRTC、网络时序、自动化信号和 CDP 输入行为。这些修改被编译进二进制文件——反爬站点看到的是一个真实浏览器，因为它**就是**真实浏览器。

**一句话：CloakBrowser 不解 CAPTCHA，它让 CAPTCHA 根本不出现。**

---

## 安装

### Python

```bash
pip install cloakbrowser
```

首次运行时，隐身 Chromium 二进制文件会自动下载（约 200MB，缓存到 `~/.cloakbrowser`）。无需任何配置。

可选：从代理 IP 自动检测时区和语言：

```bash
pip install cloakbrowser[geoip]
```

### JavaScript / Node.js

```bash
# 使用 Playwright（推荐）
npm install cloakbrowser playwright-core

# 使用 Puppeteer
npm install cloakbrowser puppeteer-core
```

### Docker

```bash
# 快速测试
docker run --rm cloakhq/cloakbrowser cloaktest

# 运行脚本
docker run --rm -v ./my_script.py:/app/my_script.py cloakhq/cloakbrowser python my_script.py
```

---

## 快速开始

### Python — 3 行代码

```python
from cloakbrowser import launch

browser = launch()
page = browser.new_page()
page.goto("https://protected-site.com")  # 不再被拦截
browser.close()
```

### 从 Playwright 迁移 — 改动一行

```python
# 之前
from playwright.sync_api import sync_playwright
pw = sync_playwright().start()
browser = pw.chromium.launch()

# 之后
from cloakbrowser import launch
browser = launch()

# 其余代码不变
page = browser.new_page()
page.goto("https://example.com")
```

### JavaScript（Playwright）

```javascript
import { launch } from 'cloakbrowser';

const browser = await launch();
const page = await browser.newPage();
await page.goto('https://protected-site.com');
await browser.close();
```

### JavaScript（Puppeteer）

```javascript
import { launch } from 'cloakbrowser/puppeteer';

const browser = await launch({ headless: true });
const page = await browser.newPage();
await page.goto('https://example.com');
await browser.close();
```

---

## 核心功能

### 1. 隐身模式（默认开启，零配置）

二进制文件在启动时自动生成随机指纹种子，并自动伪造所有可检测值（GPU、硬件规格、屏幕尺寸、canvas、WebGL、音频、字体）。每次启动都是全新的、连贯的身份。

```python
# 零配置，直接隐身
browser = launch()
```

### 2. 人类行为模拟（humanize=True）

一个参数，让所有鼠标、键盘、滚动交互变得像真人：

```python
browser = launch(humanize=True)
page = browser.new_page()
page.goto("https://example.com")
page.locator("#email").fill("user@example.com")  # 逐字输入，含思考停顿
page.locator("button[type=submit]").click()       # 贝塞尔曲线移动
```

**行为变化：**

| 交互 | 默认 | humanize=True |
|------|------|--------------|
| 鼠标移动 | 瞬间闪现 | 贝塞尔曲线 + 缓动 + 微过冲 |
| 点击 | 瞬间 | 真实瞄准点 + 按压时长 |
| 键盘 | 瞬间填入 | 逐字符计时 + 思考停顿 + 偶尔打错自纠 |
| 滚动 | 跳跃 | 加速 → 巡航 → 减速微步 |

提供 `default` 和 `careful` 两种预设：

```python
browser = launch(humanize=True, human_preset="careful")  # 更慢更谨慎
```

### 3. 代理支持

```python
# HTTP 代理
browser = launch(proxy="http://user:pass@proxy:8080")

# SOCKS5 代理
browser = launch(proxy="socks5://user:pass@proxy:1080")

# 代理字典
browser = launch(proxy={
    "server": "http://proxy:8080",
    "username": "user",
    "password": "pass"
})

# 自动匹配代理 IP 时区/语言 + WebRTC IP 防泄漏
browser = launch(proxy="http://proxy:8080", geoip=True)
```

### 4. 持久化会话

```python
from cloakbrowser import launch_persistent_context

# 首次运行 — 创建配置文件夹
ctx = launch_persistent_context("./my-profile", headless=False)
page = ctx.new_page()
page.goto("https://protected-site.com")
ctx.close()  # 配置已保存

# 再次运行 — cookies、localStorage 自动恢复
ctx = launch_persistent_context("./my-profile", headless=False)
```

### 5. 指纹管理

```python
# 固定指纹种子 — 同站点多次访问保持同一身份
browser = launch(args=["--fingerprint=42069"])

# 完整控制 — 自己做所有设置
browser = launch(stealth_args=False, args=[
    "--fingerprint=42069",
    "--fingerprint-platform=windows",
    "--fingerprint-gpu-vendor=Intel Inc.",
    "--fingerprint-gpu-renderer=Intel Iris OpenGL Engine",
])
```

### 6. Headed 模式（对抗最强反爬）

部分激进站点（DataDome 等）即使在 C++ 补丁下也能检测 headless。使用 headed 模式 + 虚拟显示器：

```bash
# 安装 Xvfb
sudo apt install xvfb

# 启动虚拟显示器
Xvfb :99 -screen 0 1920x1080x24 &
export DISPLAY=:99
```

```python
browser = launch(headless=False)
```

### 7. CDP 服务器模式

启动一个持久的隐身浏览器，通过 Chrome DevTools Protocol 远程连接：

```bash
docker run -d --name cloak -p 127.0.0.1:9222:9222 cloakhq/cloakbrowser cloakserve
```

```python
from playwright.sync_api import sync_playwright

pw = sync_playwright().start()
browser = pw.chromium.connect_over_cdp("http://localhost:9222")
# 所有隐身补丁已生效
```

支持多指纹并行 — 通过 query 参数为每个连接分配独立身份：

```python
b1 = pw.chromium.connect_over_cdp("http://localhost:9222?fingerprint=11111")
b2 = pw.chromium.connect_over_cdp("http://localhost:9222?fingerprint=22222")
```

---

## Docker Compose

```yaml
services:
  cloakbrowser:
    image: cloakhq/cloakbrowser
    command: cloakserve
    restart: unless-stopped
    ports:
      - "127.0.0.1:9222:9222"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9222/json/version"]
      interval: 30s
      timeout: 5s
      retries: 3
```

---

## CLI 工具

```bash
python -m cloakbrowser install       # 预下载二进制文件
python -m cloakbrowser info          # 查看版本、路径、平台
python -m cloakbrowser update        # 检查并下载新版
python -m cloakbrowser clear-cache   # 清除缓存
```

---

## 检测测试结果

| 检测服务 | 原生 Playwright | CloakBrowser |
|----------|:---:|:---:|
| **reCAPTCHA v3** | 0.1（机器人） | **0.9（人类）** |
| **Cloudflare Turnstile** | ❌ | ✅ 自动通过 |
| **FingerprintJS** | 被检测 | ✅ 通过 |
| **BrowserScan** | 被检测 | ✅ NORMAL (4/4) |
| `navigator.webdriver` | `true` | **`false`** |
| CDP 检测 | 被检测 | **不被检测** |
| TLS 指纹 | 不匹配 | **与 Chrome 完全一致** |

**已针对 30+ 检测站点验证通过。** 测试日期：2026 年 4 月（Chromium 146）。

---

## 最佳实践：反爬站点推荐配置

大多数拦截来自**这三点的缺失**，而非浏览器指纹检测：

```python
browser = launch(
    proxy="http://your-residential-proxy:port",  # 住宅 IP — 机房 IP 纯靠信誉就被封
    geoip=True,      # 匹配时区+语言到代理出口 IP（否则 UTC + en-US = 机器人信号）
    headless=False,  # headed 模式 — 部分站点能检测 headless，即使有 C++ 补丁
    humanize=True,   # 人类化鼠标、键盘、滚动行为
)
```

---

## 框架集成

CloakBrowser 可与任何使用 Playwright 或 Chromium 的框架配合：

- **browser-use** (70K⭐) — `from cloakbrowser import launch_async`，然后通过 CDP 连接
- **Crawl4AI** (58K⭐) — 见 `examples/integrations/crawl4ai_example.py`
- **Crawlee** (8.6K⭐) — 见 `examples/integrations/crawlee_example.py`
- **Scrapling** (21K⭐) — 见 `examples/integrations/scrapling_example.py`
- **LangChain** — 见 `examples/integrations/langchain_loader.py`
- **Selenium** — 见 `examples/integrations/selenium_example.py`

---

## 支持的平台

| 平台 | Chromium | 补丁数 | 状态 |
|------|----------|:---:|:---:|
| Linux x86_64 | 146 | 57 | ✅ 最新 |
| Linux arm64 (树莓派/Graviton) | 146 | 57 | ✅ 最新 |
| macOS arm64 (Apple Silicon) | 145 | 26 | ✅ |
| macOS x86_64 (Intel) | 145 | 26 | ✅ |
| Windows x86_64 | 146 | 57 | ✅ 最新 |

---

## 环境变量

| 变量 | 默认值 | 说明 |
|------|--------|------|
| `CLOAKBROWSER_CACHE_DIR` | `~/.cloakbrowser` | 二进制缓存目录 |
| `CLOAKBROWSER_AUTO_UPDATE` | `true` | 设为 `false` 禁用后台更新检查 |
| `CLOAKBROWSER_SKIP_CHECKSUM` | `false` | 设为 `true` 跳过 SHA-256 校验 |
| `CLOAKBROWSER_GEOIP_TIMEOUT_SECONDS` | `5` | GeoIP 解析超时秒数 |

---

## 常见问题

### 仍然被拦截（DataDome、Turnstile）？

使用 **headed 模式 + 虚拟显示器**（见上文 Headed 模式章节）。结合住宅代理 + geoip + humanize。

### Linux 上被 Kasada / Akamai 拦截？

Docker/云 VM 的最小化 Linux 环境缺少 emoji 字体，导致 canvas 哈希无法匹配真实浏览器。安装字体包解决：

```bash
sudo apt install -y fonts-noto-color-emoji fonts-freefont-ttf fonts-unifont \
    fonts-ipafont-gothic fonts-wqy-zenhei fonts-tlwg-loma-otf
```

### 首次访问被质询但第二次就正常？

部分站点对首次访问的无 cookie 会话进行质询。用持久化配置文件夹预热一次，后续复用：

```python
from cloakbrowser import launch_persistent_context

# 预热（使用 --disable-http2 绕过首次质询）
ctx = launch_persistent_context("./profile", args=["--disable-http2"])
page = ctx.new_page()
page.goto("https://example.com")  # 预热 cookies
ctx.close()

# 后续正常使用
ctx = launch_persistent_context("./profile")
```

---

## 资源

- **GitHub**: https://github.com/CloakHQ/CloakBrowser
- **PyPI**: https://pypi.org/project/cloakbrowser/
- **npm**: https://www.npmjs.com/package/cloakbrowser
- **Docker Hub**: https://hub.docker.com/r/cloakhq/cloakbrowser
- **浏览器管理面板**: https://github.com/CloakHQ/CloakBrowser-Manager

---

> **许可**: MIT 开源协议 — 免费、无订阅、无使用限制
