# Imgly 图床工具使用指南

## 快速开始

### 1. 安装 slash command

已自动安装到：`~/.claude/commands/imgly-upload.md`

### 2. 首次配置

运行以下命令进行配置：

```bash
/imgly-upload --setup
```

你需要提供：
- **GitHub Token**（必需）
- **GitHub 用户名**（必需）
- **仓库名称**（可选，默认: imgly-images）

### 3. 上传图片

#### 上传本地图片
```bash
/imgly-upload /path/to/image.jpg
```

#### 上传网络图片
```bash
/imgly-upload https://example.com/image.png
```

#### 批量上传
```bash
/imgly-upload /path/to/img1.jpg /path/to/img2.png https://example.com/img3.jpg
```

## 详细功能

### 配置选项

配置文件位置：`~/.claude/imgly-config.json`

```json
{
  "github_token": "ghp_xxxxxxxxxxxx",
  "owner": "your-github-username",
  "repo": "imgly-images",
  "branch": "master",
  "default_dir_mode": "date",
  "cdn_provider": "jsdelivr",
  "hash_filename": true,
  "compress_image": false
}
```

### 命令参数

- `--setup`: 配置向导
- `--repo <name>`: 指定仓库名称
- `--dir <path>`: 指定存储目录
- `--branch <name>`: 指定分支（默认: master）
- `--no-hash`: 不使用哈希文件名
- `--cdn <provider>`: 指定 CDN (jsdelivr/raw/custom)
- `--compress`: 压缩图片
- `--force`: 覆盖已存在的文件

### 示例

#### 1. 上传到指定目录
```bash
/imgly-upload /path/to/image.jpg --dir photos/vacation
```

#### 2. 使用原始文件名
```bash
/imgly-upload /path/to/image.jpg --no-hash
```

#### 3. 压缩并上传
```bash
/imgly-upload /path/to/large-image.jpg --compress
```

#### 4. 使用不同 CDN
```bash
/imgly-upload /path/to/image.jpg --cdn raw
```

## CDN 提供商

### jsDelivr (默认)
```
https://cdn.jsdelivr.net/gh/username/repo@branch/path/to/image.jpg
```
- ✅ 全球 CDN 加速
- ✅ 免费无限流量
- ✅ 自动缓存优化

### GitHub Raw
```
https://raw.githubusercontent.com/username/repo/branch/path/to/image.jpg
```
- ✅ 直连 GitHub
- ⚠️ 可能较慢
- ✅ 无缓存延迟

### 自定义域名
如果你配置了 GitHub Pages：
```
https://username.github.io/repo/path/to/image.jpg
```

## 目录模式

### 1. 根目录模式 (root)
所有图片存储在仓库根目录：
```
repo/
  ├── image1.jpg
  ├── image2.png
  └── image3.gif
```

### 2. 日期目录模式 (date) - 默认
按日期自动分类：
```
repo/
  ├── 20251031/
  │   ├── image1.jpg
  │   └── image2.png
  └── 20251101/
      └── image3.jpg
```

### 3. 自定义目录模式
指定目录：
```bash
/imgly-upload image.jpg --dir my-folder/subfolder
```

## 文件命名

### 哈希模式（默认）
使用 MD5 哈希确保唯一性：
```
a1b2c3d4e5f6g7h8i9j0.jpg
```

### 原始文件名模式
保持原始文件名：
```
my-photo.jpg
```

如果文件已存在，会自动添加时间戳：
```
my-photo-1730383200.jpg
```

## 返回链接格式

上传成功后，会返回多种格式的链接：

```markdown
✅ 图片上传成功！

📎 **Markdown 格式：**
![image](https://cdn.jsdelivr.net/gh/username/imgly-images@master/20251031/abc123.jpg)

🔗 **HTML 格式：**
<img src="https://cdn.jsdelivr.net/gh/username/imgly-images@master/20251031/abc123.jpg" alt="image">

📋 **直接链接：**
https://cdn.jsdelivr.net/gh/username/imgly-images@master/20251031/abc123.jpg

📂 **BBCode 格式：**
[img]https://cdn.jsdelivr.net/gh/username/imgly-images@master/20251031/abc123.jpg[/img]

📁 **GitHub 仓库：**
https://github.com/username/imgly-images/blob/master/20251031/abc123.jpg
```

## 故障排查

### Token 无效
```bash
# 重新配置
/imgly-upload --setup

# 或直接编辑配置文件
vim ~/.claude/imgly-config.json
```

### 上传失败
1. 检查网络连接
2. 确认 Token 权限（需要 `repo` 权限）
3. 确认仓库存在且可访问
4. 查看详细错误信息

### 图片无法访问
1. 检查仓库是否为公开（Public）
2. 等待 CDN 缓存更新（jsDelivr 约 1-5 分钟）
3. 尝试使用 GitHub Raw 链接

## 最佳实践

### 1. 仓库组织
```
imgly-images/
  ├── 202510/          # 按月份分类
  │   ├── 20251031/    # 按日期分类
  │   └── 20251101/
  ├── blog/            # 按用途分类
  ├── projects/
  └── screenshots/
```

### 2. 使用 .gitignore
如果需要本地测试，添加：
```gitignore
# 本地临时文件
/tmp/
*.tmp
```

### 3. Token 安全
- ✅ 使用最小权限（只勾选 `repo`）
- ✅ 定期轮换 Token
- ✅ 不要分享配置文件
- ✅ 使用环境变量（可选）

### 4. 性能优化
- 大图片建议使用 `--compress` 压缩
- 批量上传时，会自动并行处理
- jsDelivr CDN 提供全球加速

## 高级用法

### 环境变量配置
不想保存配置文件？使用环境变量：

```bash
export IMGLY_GITHUB_TOKEN="ghp_xxxx"
export IMGLY_GITHUB_OWNER="username"
export IMGLY_GITHUB_REPO="imgly-images"

/imgly-upload image.jpg
```

### 集成到脚本
```bash
#!/bin/bash
# 批量上传目录中的所有图片

for img in /path/to/images/*.{jpg,png,gif}; do
  /imgly-upload "$img" --dir "batch-upload-$(date +%Y%m%d)"
done
```

### 自定义 CDN 域名
编辑配置文件：
```json
{
  ...
  "cdn_provider": "custom",
  "custom_cdn_template": "https://my-cdn.com/{owner}/{repo}/{branch}/{path}"
}
```

## 支持的图片格式

- ✅ JPEG/JPG
- ✅ PNG
- ✅ GIF
- ✅ WebP
- ✅ BMP
- ✅ SVG
- ✅ TIFF

## 限制

- GitHub 单文件大小限制：**100 MB**
- 建议单张图片大小：**< 5 MB**（使用压缩）
- jsDelivr 缓存更新时间：**1-5 分钟**
- GitHub API 频率限制：**5000 次/小时**（已认证）

## 获取帮助

```bash
# 查看帮助
/imgly-upload --help

# 查看当前配置
/imgly-upload --config

# 测试配置
/imgly-upload --test
```

## 更新日志

### v1.0.0 (2025-10-31)
- ✅ 初始版本
- ✅ 支持本地和网络图片上传
- ✅ 多种 CDN 选项
- ✅ 批量上传功能
- ✅ 配置向导

---

**注意**: 此工具基于 GitHub API，请遵守 GitHub 服务条款，不要上传违规内容。
