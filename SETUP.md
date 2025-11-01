# 本地部署使用说明

> 本项目是基于 PicX v3.0 的部署版本

## 🚀 快速开始

### 1. 启动项目

```bash
# 安装依赖（如果还没有安装）
npm install

# 启动开发服务器
npm run dev
```

服务器启动后，访问：**http://localhost:4000**

### 2. 登录使用

#### 方式一：使用 GitHub Token 登录（推荐）

1. 访问：https://github.com/settings/tokens/new
2. 填写：
   - Token name: `PicX-图床工具`
   - 勾选 `repo` 权限
3. 生成并复制 Token
4. 在 PicX 登录页面选择 "使用 GitHub Token 登录"
5. 粘贴 Token 完成登录

#### 方式二：GitHub OAuth 授权

⚠️ **注意**：当前配置使用官方 PicX OAuth App，如果需要在自己的域名部署，需要配置自己的 OAuth App。

### 3. 配置图床

登录后：

1. 创建或选择用于存储图片的仓库
2. 选择分支（通常是 `master` 或 `main`）
3. 选择存储目录（可选）
4. 开始上传图片

## 📁 环境变量配置

### 开发环境（`.env.development`）

```env
VITE_USE_PWA = false
VITE_CLIENT_ID = Iv1.274fe6f96551b91f
VITE_REDIRECT_URI = http://localhost:4000
VITE_AUTHORIZE_URI = https://github.com/login/oauth/authorize
VITE_AUTHORIZE_API = https://apis.xpoet.cn/api/github-authorize
```

### 生产环境（`.env.production`）

```env
VITE_USE_PWA=true
VITE_CLIENT_ID=Ov23lil1wHkjil6WWxAb
VITE_REDIRECT_URI=https://imgly.charliiai.com
VITE_AUTHORIZE_URI=https://github.com/login/oauth/authorize
VITE_AUTHORIZE_API=https://apis.xpoet.cn/api/github-authorize
```

### 修改配置

如果需要使用自己的 OAuth App：

1. 修改 `.env.development` 和 `.env.production` 中的 `VITE_CLIENT_ID`
2. 修改 `VITE_REDIRECT_URI` 为你的实际域名
3. 如果使用自己的授权后端，修改 `VITE_AUTHORIZE_API`
4. 重启开发服务器

## 🔧 构建部署

### 构建生产版本

```bash
npm run build
```

构建产物在 `dist` 目录。

### 部署

将 `dist` 目录内容部署到你的 Web 服务器（Nginx、Apache 等）。

## 📚 详细文档

- **快速使用指南**：`docs/QUICK_START.md`
- **OAuth 配置指南**：`docs/GITHUB_OAUTH_CONFIG.md`
- **配置总结**：`docs/CONFIGURATION_SUMMARY.md`

## ⚠️ 常见问题

### Q: OAuth 授权出现 redirect_uri_mismatch 错误

**A:** 这是因为 GitHub OAuth App 的回调 URL 配置不匹配。

**解决方案：**
1. 最简单：使用 Token 登录
2. 创建自己的 OAuth App 并配置回调 URL

详见：`docs/GITHUB_OAUTH_CONFIG.md`

### Q: 如何配置自己的域名？

**A:** 
1. 修改 `.env.production` 中的 `VITE_REDIRECT_URI`
2. 如果使用自己的 OAuth App，在 GitHub 上配置对应的回调 URL
3. 构建并部署到你的服务器

### Q: 需要使用自己的授权服务吗？

**A:** 
- 如果使用 Token 登录：不需要
- 如果需要 OAuth 授权：需要（或使用官方服务）

## 🔗 相关链接

- GitHub 仓库：https://github.com/kenchikuliu/picx
- PicX 官方：https://picx.xpoet.cn
- PicX 文档：https://picx-docs.xpoet.cn
- GitHub Token 生成：https://github.com/settings/tokens/new

## 📝 License

本项目基于 AGPL-3.0 许可证。

