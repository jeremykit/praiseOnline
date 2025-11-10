# 赞美诗播放器

基于 Cloudflare Pages 和 Workers 构建的在线 MP3 播放器项目。

## 项目配置

- **Pages 项目名**：my-mp3-web  
- **Pages 访问地址**：https://m.ours24.dpdns.org  
- **Worker 项目名**：my-mp3-api  
- **Worker 访问地址**：https://mapi.ours24.dpdns.org  
- **R2 存储桶**：jiamingzh

## 项目结构

- `pages/` - 前端页面（HTML/CSS/JS），部署到 Cloudflare Pages
- `worker/` - 后端 API Worker，部署到 Cloudflare Workers

## 部署步骤

### 1️⃣ 准备工作

1. 在 Cloudflare 控制台中创建 R2 存储桶并上传 MP3 文件
2. 确保已安装 Wrangler CLI：`npm install -g wrangler` 或 `npm i -D wrangler`

### 2️⃣ 配置 Worker

Worker 配置已设置：
- Worker 名称：my-mp3-api
- R2 存储桶：jiamingzh
- 域名绑定：mapi.ours24.dpdns.org

配置文件 `worker/wrangler.toml` 内容：
```toml
name = "my-mp3-api"
main = "index.js"
compatibility_date = "2024-10-23"

[[r2_buckets]]
binding = "R2_BUCKET"
bucket_name = "jiamingzh"
preview_bucket_name = "jiamingzh"

routes = [
  { pattern = "mapi.ours24.dpdns.org/*", zone_name = "ours24.dpdns.org" }
]
```

### 3️⃣ 部署 Worker

```bash
cd worker
npx wrangler deploy
```

部署完成后，Worker 可通过以下地址访问：
- Worker 原生 URL：https://my-mp3-api.yourname.workers.dev
- 自定义域名：https://mapi.ours24.dpdns.org

### 4️⃣ 配置 Pages 前端

Pages 前端已配置好 Worker API 地址，`pages/app.js` 中已设置为：
```javascript
const API_BASE = "https://mapi.ours24.dpdns.org";
```

### 5️⃣ 初始化 Pages 项目（仅首次）

在项目根目录执行：

```bash
npx wrangler pages project create my-mp3-web
```

### 6️⃣ 部署 Pages

```bash
npx wrangler pages deploy pages --project-name=my-mp3-web
```

部署完成后，访问地址：https://m.ours24.dpdns.org

## 注意事项

- 确保 R2 存储桶中的 MP3 文件按以下目录结构组织：
  - `praise/附录/`
  - `praise/大本/`
  - `praise/新编/`
- Worker 的 `list` 接口默认限制 1000 条，如需修改请编辑 `worker/index.js`
- 首次部署后需要在 Cloudflare Dashboard 中配置 Pages 的自定义域名（可选）
- Worker 已配置 CORS 支持，允许跨域访问
- 修改 Worker 代码后需要重新部署：`cd worker && npx wrangler deploy`

## 常见问题

### CORS 错误
如果遇到 CORS 错误，确保已部署带有 CORS 头部的 Worker 代码。Worker 已配置允许所有来源的跨域请求。
