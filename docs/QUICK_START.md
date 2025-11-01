# PicX 快速使用指南

## 当前问题

如果你遇到了 GitHub OAuth 授权登录的 `redirect_uri_mismatch` 错误，这是因为项目使用官方的 PicX GitHub App，但你的部署环境不在官方白名单中。

## 最快的解决方案：使用 Token 登录

### 步骤 1：生成 GitHub Token

1. 访问：https://github.com/settings/tokens/new

2. 填写：
   - Token name: `PicX-图床工具`
   - Expiration: `90 days`（或其他期限）
   - Select scopes: 勾选 `repo`（完整仓库权限）

3. 点击 "Generate token"

4. **立即复制 token**（以后无法再次查看）

### 步骤 2：在 PicX 中使用 Token 登录

1. 访问你的 PicX 网站（http://localhost:4000 或你的部署地址）

2. 在登录页面点击 **"使用 GitHub Token 登录"**

3. 粘贴刚才复制的 token

4. 点击登录

### 步骤 3：配置图床

登录成功后：

1. 选择或创建用于存储图片的仓库
2. 选择分支（通常是 `master` 或 `main`）
3. 选择存储目录（可选）
4. 开始上传图片

## 方案二：修复 OAuth 登录（需要服务器）

如果你想使用点击授权登录，需要：

### 1. 创建 GitHub OAuth App

1. 访问：https://github.com/settings/developers

2. 点击 "New OAuth App"

3. 填写：
   - Application name: `你的应用名称`
   - Homepage URL: `https://你的域名.com`
   - Authorization callback URL: `https://你的域名.com`

4. 注册后记录 `Client ID` 和生成 `Client Secret`

### 2. 修改环境变量

#### 本地开发（`.env.development`）：
```env
VITE_CLIENT_ID=你从GitHub获取的_Client_ID
VITE_REDIRECT_URI=http://localhost:4000
```

#### 生产环境（`.env.production`）：
```env
VITE_CLIENT_ID=你从GitHub获取的_Client_ID
VITE_REDIRECT_URI=https://你的域名.com
```

### 3. 部署授权后端服务

由于原项目使用官方 API：`https://apis.xpoet.cn/api/github-authorize`

你需要：

1. **修改代码中的 API 地址**
   
   编辑 `src/views/picx-login/picx-login.util.ts`：
   ```typescript
   // 将这行
   const authorize_api = 'https://apis.xpoet.cn/api/github-authorize'
   
   // 改为你的服务器地址
   const authorize_api = 'https://你的域名.com/api/github-authorize'
   ```

2. **部署后端服务**

   最简单的实现（Node.js + Express）：
   
   ```bash
   npm install express axios dotenv
   ```
   
   ```javascript
   // server.js
   require('dotenv').config();
   const express = require('express');
   const axios = require('axios');
   const app = express();
   
   app.get('/api/github-authorize', async (req, res) => {
     const { code, redirect_uri } = req.query;
     
     try {
       const response = await axios.post('https://github.com/login/oauth/access_token', {
         client_id: process.env.GITHUB_CLIENT_ID,
         client_secret: process.env.GITHUB_CLIENT_SECRET,
         code: code,
         redirect_uri: redirect_uri
       }, {
         headers: { 'Accept': 'application/json' }
       });
       
       if (response.data.access_token) {
         res.json({ data: response.data.access_token, msg: 'Success' });
       } else {
         res.status(400).json({ msg: response.data.error_description || 'Failed' });
       }
     } catch (error) {
       res.status(500).json({ msg: 'Server error' });
     }
   });
   
   app.listen(process.env.PORT || 3000, () => {
     console.log('Auth server running');
   });
   ```
   
   ```env
   # .env
   GITHUB_CLIENT_ID=你的ClientID
   GITHUB_CLIENT_SECRET=你的ClientSecret
   PORT=3000
   ```

3. **重启项目**

   ```bash
   npm run dev
   ```

## 推荐方案对比

| 方案 | 优点 | 缺点 | 适用场景 |
|------|------|------|---------|
| Token 登录 | 简单快速，无需服务器 | 每次登录需输入 | 个人使用，小团队 |
| 自建 OAuth | 用户友好，一次授权 | 需要服务器，需要维护 | 公开服务，多用户 |

## 当前推荐

**对于你现在的情况，强烈推荐使用 Token 登录：**
- ✅ 立即可用，无需额外配置
- ✅ 不需要服务器
- ✅ 安全可靠
- ✅ 一次配置，可以长期使用

## 更多帮助

- 详细配置文档：见 `docs/GITHUB_OAUTH_CONFIG.md`
- 项目 README：见 `README.md`

