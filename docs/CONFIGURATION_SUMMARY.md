# PicX 项目配置总结

## ✅ 已完成的配置

### 1. 环境变量配置

#### 开发环境（`.env.development`）
```env
VITE_USE_PWA = false
VITE_CLIENT_ID = Iv1.274fe6f96551b91f
VITE_REDIRECT_URI = http://localhost:4000
VITE_AUTHORIZE_URI = https://github.com/login/oauth/authorize
VITE_AUTHORIZE_API = https://apis.xpoet.cn/api/github-authorize
VITE_INSTALL_URL = https://github.com/apps/picx-app/installations/select_target
VITE_INSTALL_URL_USER = https://github.com/apps/picx-app/installations/new/permissions?target_id=
```

#### 生产环境（`.env.production`）
```env
VITE_USE_PWA=true
VITE_CLIENT_ID=Ov23lil1wHkjil6WWxAb
VITE_REDIRECT_URI=https://imgly.charliiai.com
VITE_AUTHORIZE_URI=https://github.com/login/oauth/authorize
VITE_AUTHORIZE_API=https://apis.xpoet.cn/api/github-authorize
VITE_INSTALL_URL=https://github.com/apps/picx-app/installations/select_target
VITE_INSTALL_URL_USER=https://github.com/apps/picx-app/installations/new/permissions?target_id=
```

### 2. 代码改进

- ✅ 添加了 `VITE_AUTHORIZE_API` 环境变量支持
- ✅ 授权 API 可从环境变量配置，默认使用官方 API
- ✅ 增强了配置灵活性

## 🔧 如何使用

### 本地开发

1. **启动开发服务器**
   ```bash
   npm run dev
   ```
   
2. **访问应用**
   - 地址：http://localhost:4000

3. **登录方式**

   **方式一：使用 Token 登录（推荐）**
   - 访问：https://github.com/settings/tokens/new
   - 生成一个有 `repo` 权限的 Token
   - 在 PicX 登录页面使用 Token 登录
   
   **方式二：GitHub OAuth 授权**
   - 注意：需要配置 GitHub OAuth App
   - 详见：`docs/GITHUB_OAUTH_CONFIG.md`

### 生产部署

1. **环境变量**
   - 确保 `.env.production` 中的域名配置正确
   - 修改 `VITE_REDIRECT_URI` 为你的实际域名

2. **构建项目**
   ```bash
   npm run build
   ```

3. **部署**
   - 将 `dist` 目录部署到你的服务器
   - 配置 Nginx/Apache 等服务器

## 📝 重要说明

### OAuth 配置问题

当前项目使用官方的 PicX GitHub App (`Client ID: Iv1.274fe6f96551b91f`)，如果你遇到 `redirect_uri_mismatch` 错误，原因是：

**原因：** 官方 OAuth App 只允许特定的回调 URL

**解决方案：**
1. **最简单：使用 Token 登录**（推荐）
   - 生成 GitHub Token
   - 使用 Token 登录即可

2. **使用自己的 OAuth App**
   - 创建 GitHub OAuth App
   - 修改 `.env.development` 和 `.env.production`
   - 替换 `VITE_CLIENT_ID`
   - 部署自己的授权服务
   - 修改 `VITE_AUTHORIZE_API`

### 配置自定义授权 API

如果你想使用自己的授权后端服务：

1. **修改环境变量**
   ```env
   VITE_AUTHORIZE_API = https://你的服务器.com/api/github-authorize
   ```

2. **实现授权服务**
   - 参考：`docs/GITHUB_OAUTH_CONFIG.md`
   - 示例代码已提供

3. **重新启动**
   ```bash
   npm run dev
   ```

## 📚 相关文档

- **快速开始**：`docs/QUICK_START.md`
- **OAuth 配置指南**：`docs/GITHUB_OAUTH_CONFIG.md`
- **项目 README**：`README.md`

## 🚀 下一步

1. ✅ 项目已成功运行在 http://localhost:4000
2. ✅ 使用 Token 登录即可开始使用
3. ⚠️  如需 OAuth 登录，参考配置文档

## 💡 提示

- 个人使用建议使用 Token 登录
- 公开服务需要配置 OAuth App
- 确保 GitHub Token 或 OAuth App 有 `repo` 权限

## 🔗 相关链接

- GitHub 仓库：https://github.com/kenchikuliu/picx
- PicX 官方：https://picx.xpoet.cn
- GitHub Token 生成：https://github.com/settings/tokens/new
- GitHub OAuth Apps：https://github.com/settings/developers

