# 个人博客搭建指南

基于 [2025-blog-public](https://github.com/YYsuni/2025-blog-public) 开源项目。

## 一、克隆项目

```bash
git clone https://github.com/YYsuni/2025-blog-public.git my-blog
cd my-blog
```

## 二、安装依赖

```bash
pnpm install
```

> 需要先安装 pnpm：`npm install -g pnpm`

## 三、创建 GitHub 仓库

1. 打开 https://github.com/new
2. **Repository name**：自定义（如 `my-blog`）
3. 选择 **Public**
4. **不要勾选** 任何初始化选项（README、.gitignore、License 都不选）
5. 点击 **Create repository**

## 四、创建 GitHub App

1. 打开 https://github.com/settings/apps
2. 点击 **New GitHub App**
3. 填写：
   - **GitHub App name**：随便起名
   - **Homepage URL**：填你的仓库地址
   - **Webhook**：取消勾选
4. 设置权限：
   - **Repository permissions** → **Contents** → **Read and write**
5. 点击 **Create GitHub App**
6. 记下 **App ID**（页面上方的数字）
7. 点击 **Generate a private key**，下载 `.pem` 文件（保管好）
8. 点击 **Install App** → 选择你的账号 → **Only select repositories** → 选择你的仓库 → **Install**

## 五、修改项目配置

编辑 `src/consts.ts`：

```ts
export const GITHUB_CONFIG = {
  OWNER: process.env.NEXT_PUBLIC_GITHUB_OWNER || '你的GitHub用户名',
  REPO: process.env.NEXT_PUBLIC_GITHUB_REPO || '你的仓库名',
  BRANCH: process.env.NEXT_PUBLIC_GITHUB_BRANCH || 'main',
  APP_ID: process.env.NEXT_PUBLIC_GITHUB_APP_ID || '你的AppID',
  ENCRYPT_KEY: process.env.NEXT_PUBLIC_GITHUB_ENCRYPT_KEY || '自定义加密密钥',
} as const
```

## 六、清理原作者数据

```bash
# 删除原作者博客文章（保留 JSON 配置文件）
ls public/blogs/ | grep -v "\.json$" | while read dir; do rm -rf "public/blogs/$dir"; done

# 删除原作者图片
rm -rf public/images/art public/images/blogger public/images/pictures public/images/share public/images/christmas public/images/hats public/images/misc

# 删除音乐
rm public/music/*
```

清空 `public/blogs/index.json`：

```json
[]
```

清空 `public/blogs/categories.json`：

```json
{
  "categories": []
}
```

编辑 `src/config/site-content.json`，修改以下字段：

```json
{
  "meta": {
    "title": "你的博客标题",
    "description": "你的博客描述",
    "username": "你的用户名"
  },
  "socialButtons": [
    {
      "id": "github",
      "type": "github",
      "value": "https://github.com/你的用户名",
      "label": "Github",
      "order": 1
    }
  ]
}
```

## 七、推送代码到 GitHub

```bash
# 关联远程仓库
git remote set-url origin https://github.com/你的用户名/你的仓库名.git

# 创建干净分支并推送（避免浅克隆问题）
git checkout --orphan fresh-main
git add -A
git commit -m "初始化个人博客"
git push origin fresh-main:main --force

# 切回 main
git checkout main
git reset --hard origin/main
git branch -D fresh-main
```

## 八、部署

### 方案 A：Netlify（国内可访问，推荐）

1. 打开 https://app.netlify.com/，用 GitHub 登录
2. 点击 **Add new site** → **Import an existing project**
3. 选择 GitHub，选择你的仓库
4. 构建设置：
   - **Build command**：`pnpm build`
   - **Publish directory**：`.next`
5. 点击 **Deploy site**
6. 等待部署完成，访问 `*.netlify.app` 域名

> 需要项目根目录有 `netlify.toml`：
> ```toml
> [build]
>   command = "pnpm build"
>   publish = ".next"
>
> [[plugins]]
>   package = "@netlify/plugin-nextjs"
> ```
>
> 并安装插件：`pnpm add -D @netlify/plugin-nextjs`

### 方案 B：Vercel（需 VPN 访问）

1. 打开 https://vercel.com/new
2. 用 GitHub 登录，导入仓库
3. 直接点击 **Deploy**
4. 部署完成后访问 `*.vercel.app` 域名

### 方案 C：Cloudflare Pages（需自定义域名）

1. 打开 https://dash.cloudflare.com/
2. 左侧 **Workers & Pages** → **Create** → **Pages** → **Connect to Git**
3. 选择仓库，配置：
   - **Framework preset**：`Next.js`
   - **Build command**：`pnpm run build:cf`
   - **Build output directory**：`.open-next`
   - 环境变量添加 `NODE_VERSION` = `20`
4. 点击 **Save and Deploy**

> Windows 用户需以管理员身份运行 PowerShell 执行构建。

## 九、配置 Private Key

1. 访问部署后的网站
2. 网站会提示输入 **Private Key**
3. 粘贴之前下载的 `.pem` 文件内容
4. 验证通过后即可通过网页端编辑博客

## 十、日常使用

- **写博客**：访问 `/write` 页面
- **编辑首页**：首页右上角配置按钮（或 `Ctrl + ,`）
- **删除博客**：博客列表页批量删除
- **修改设置**：大部分页面右上角有编辑按钮

## 项目结构

```
my-blog/
├── public/
│   ├── blogs/           # 博客内容
│   │   ├── index.json   # 博客索引
│   │   ├── categories.json  # 分类
│   │   └── {slug}/      # 每篇文章的目录
│   │       ├── config.json
│   │       ├── index.md
│   │       └── *.webp   # 文章图片
│   ├── images/          # 全局图片
│   └── music/           # 音乐文件
├── src/
│   ├── config/
│   │   ├── site-content.json  # 网站内容配置
│   │   └── card-styles.json   # 首页卡片样式
│   ├── consts.ts        # GitHub 配置
│   └── app/             # 页面
├── netlify.toml         # Netlify 配置
├── wrangler.toml        # Cloudflare 配置
└── package.json
```

## 环境变量（可选）

也可通过环境变量配置，不改代码：

| 变量名 | 说明 |
|--------|------|
| `NEXT_PUBLIC_GITHUB_OWNER` | GitHub 用户名 |
| `NEXT_PUBLIC_GITHUB_REPO` | 仓库名 |
| `NEXT_PUBLIC_GITHUB_BRANCH` | 分支名 |
| `NEXT_PUBLIC_GITHUB_APP_ID` | GitHub App ID |
| `NEXT_PUBLIC_GITHUB_ENCRYPT_KEY` | 加密密钥 |
