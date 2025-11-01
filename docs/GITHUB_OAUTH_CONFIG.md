# GitHub OAuth 配置指南

## 问题说明

当点击 GitHub 授权登录时出现 `redirect_uri_mismatch` 错误，这是因为在 GitHub 上配置的 OAuth App 的回调 URL 与项目配置不匹配。

## 解决方案

### 方案一：使用 Token 登录（最简单，推荐）

1. 访问 GitHub Token 生成页面：https://github.com/settings/tokens/new
2. 填写 Token 名称（如：PicX-图床）
3. 选择权限：
   - `repo` - 完整仓库权限（必需）
4. 点击 "Generate token"
5. 复制生成的 token
6. 在 PicX 登录页面选择 "使用 GitHub Token 登录"
7. 粘贴 token 并登录

**优点：** 
- 无需配置 OAuth App
- 立即可用
- 简单快捷

**缺点：**
- 每次需要手动填写 token

### 方案二：创建自己的 GitHub OAuth App（需要服务器）

如果你想让用户直接点击授权登录，需要：

1. **创建 GitHub OAuth App**
   - 访问：https://github.com/settings/developers
   - 点击 "New OAuth App"
   - 填写信息：
     - Application name: `你的应用名称`
     - Homepage URL: `你的网站地址`（如：https://imgly.charliiai.com）
     - Authorization callback URL: `你的网站地址`（如：https://imgly.charliiai.com）
   - 点击 "Register application"
   - 复制 `Client ID` 和生成一个新的 `Client Secret`

2. **修改环境变量**
   
   修改 `.env.development`（本地开发）：
   ```env
   VITE_CLIENT_ID=你的_Client_ID
   VITE_REDIRECT_URI=http://localhost:4000
   ```
   
   修改 `.env.production`（生产环境）：
   ```env
   VITE_CLIENT_ID=你的_Client_ID
   VITE_REDIRECT_URI=https://imgly.charliiai.com
   ```

3. **部署后端的 GitHub 授权服务**

   当前代码使用官方的授权 API：
   ```typescript
   const authorize_api = 'https://apis.xpoet.cn/api/github-authorize'
   ```

   你需要：
   - 将 `src/views/picx-login/picx-login.util.ts` 中的 `authorize_api` 改为你自己的服务器地址
   - 在你的服务器上实现 OAuth 回调处理
   - 使用 GitHub OAuth App 的 Client Secret 交换 access token

**示例后端实现（Node.js）：**

```javascript
// server.js
const express = require('express');
const axios = require('axios');
const app = express();

const CLIENT_ID = '你的_Client_ID';
const CLIENT_SECRET = '你的_Client_Secret';

app.get('/api/github-authorize', async (req, res) => {
  const { code, redirect_uri } = req.query;
  
  try {
    // 使用 code 交换 access token
    const response = await axios.post('https://github.com/login/oauth/access_token', {
      client_id: CLIENT_ID,
      client_secret: CLIENT_SECRET,
      code: code,
      redirect_uri: redirect_uri
    }, {
      headers: {
        'Accept': 'application/json'
      }
    });
    
    if (response.data.access_token) {
      res.json({
        data: response.data.access_token,
        msg: 'Success'
      });
    } else {
      res.status(400).json({
        msg: response.data.error_description || 'Failed to get access token'
      });
    }
  } catch (error) {
    res.status(500).json({
      msg: 'Internal server error'
    });
  }
});

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

### 方案三：使用官方 PicX App（最简单但限制多）

如果你想使用官方的 PicX GitHub App：

1. 保持当前配置不变
2. 使用官方的 `VITE_CLIENT_ID = Iv1.274fe6f96551b91f`
3. 但注意：官方服务可能只支持官方域名
4. 这意味着你只能在自己的域名上使用，需要联系官方配置你的域名

## 快速开始 - 推荐使用 Token 登录

对于个人使用或小团队，**方案一（Token 登录）** 是最简单实用的方案：

1. 生成 GitHub Token（有 `repo` 权限）
2. 在 PicX 使用 Token 登录
3. 配置图床仓库信息
4. 开始使用

## 检查配置

确保配置正确：

1. 检查 `.env.development` 中的 `VITE_REDIRECT_URI` 是否与本地开发地址匹配
2. 检查 `.env.production` 中的 `VITE_REDIRECT_URI` 是否与生产环境地址匹配
3. 如果使用自己的 OAuth App，确保 GitHub 上配置的 Callback URL 完全一致（包括协议 http/https）

## 常见问题

### Q: 为什么 `redirect_uri` 必须完全匹配？

A: 这是 OAuth 2.0 的安全要求，防止重定向攻击。URL 必须完全一致，包括：
- 协议（http vs https）
- 域名
- 端口
- 路径（如果有）

### Q: 本地开发和生产环境需要不同的 Client ID 吗？

A: 不需要，但 Callback URL 必须分别配置。可以在 GitHub OAuth App 中添加多个 Callback URL。

### Q: 可以使用相对路径吗？

A: 不可以，Callback URL 必须是完整的 URL（包含协议、域名等）。

## 参考资料

- [GitHub OAuth App 文档](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)
- [PicX 官方文档](https://picx-docs.xpoet.cn)

