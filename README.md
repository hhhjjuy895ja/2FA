# 2FA 验证码生成器

一个纯前端、零依赖的 TOTP（两步验证）动态验证码生成器。输入 Base32 格式的密钥，即可生成 30 秒有效期的 6 位动态验证码，支持明暗主题切换、验证码/密钥一键复制。

## 功能特性

- **本地计算**：基于 Web Crypto API（HMAC-SHA1）在浏览器本地生成验证码，密钥不会上传到任何服务器。
- **自动刷新**：验证码每 30 秒自动更新，并配有环形倒计时进度提示。
- **一键复制**：支持复制验证码与密钥，兼容剪贴板 API 与降级方案。
- **明暗主题**：内置深色 / 浅色两套主题，偏好保存在 `localStorage`。
- **响应式布局**：适配桌面端与移动端。
- **外观可配置**：图标、背景、浏览器标题均可通过变量自定义。

## 目录结构

```
.
├── index.html   # 单文件应用（页面、样式、脚本全部内联）
├── _headers     # Cloudflare Pages 响应头 / 内容安全策略（CSP）
└── README.md
```

## 自定义配置

打开 [index.html](index.html)，在 `</head>` 之前找到 `window.SITE_CONFIG`，修改其中的变量即可：

```js
window.SITE_CONFIG = {
  title: '2FA 验证码生成器',                                        // 浏览器标签标题
  icon: 'https://cloudflare-imgbed-6w6.pages.dev/file/icon/3kgQgyb9.webp', // 网站图标地址
  background: '#0a0f0d',                                         // 深色模式背景
  backgroundLight: '#f8faf9'                                     // 浅色模式背景
};
```

| 变量 | 说明 | 示例 |
| --- | --- | --- |
| `title` | 浏览器标签页显示的标题 | `'我的验证码工具'` |
| `icon` | 网站图标（favicon）地址，支持任意 `https` 图片 | `'https://example.com/icon.png'` |
| `background` | 深色模式下的页面背景，支持颜色、渐变或图片 | `'#0a0f0d'`、`'linear-gradient(135deg,#0f2027,#203a43)'`、`'url(https://example.com/bg.jpg) center/cover no-repeat'` |
| `backgroundLight` | 浅色模式下的页面背景，留空则沿用默认值 | `'#f8faf9'` |

> 背景值会写入 CSS 变量 `--bg`，因此页面原有的网格与光晕动画会叠加在其之上。

### 关于图标不显示的问题

页面通过 `_headers` 设置了 Content-Security-Policy。若 CSP 的 `img-src` 未包含图标所在的域名，浏览器会拦截图标导致其无法显示。当前配置已放开为允许任意 `https` 图片：

```
img-src 'self' https: data:;
```

自定义图标或背景图片时，请确保使用 `https` 地址；如改为固定域名白名单，请记得把新域名一并加入 `img-src`。

## 部署

本项目为静态站点，可直接部署到 Cloudflare Pages：

1. 将仓库连接到 Cloudflare Pages。
2. 构建命令留空，输出目录设为根目录 `/`。
3. `_headers` 会在部署时自动生效，为站点附加 CSP 等安全响应头。

也可使用任意静态服务器本地预览：

```bash
python -m http.server 8000
# 打开 http://localhost:8000
```

## 使用说明

1. 在输入框中粘贴 Base32 格式的密钥（大小写不敏感，会自动忽略空格与非法字符）。
2. 点击「生成验证码」或按回车。
3. 页面将显示当前 6 位验证码，并每 30 秒自动刷新。
4. 点击「复制验证码」即可复制到剪贴板。

## 安全提示

- 密钥仅在当前浏览器内存中使用，页面刷新后即清除，不会被保存或上传。
- 请勿在公共设备上输入长期有效的密钥。
