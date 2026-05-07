# 博客配置指南

所有网站配置都在 `src/config/site-content.json` 文件中，修改后推送代码即可生效。

## 基本信息

```json
{
  "meta": {
    "title": "网站标题（显示在浏览器标签页）",
    "description": "网站描述（SEO 用）",
    "username": "显示的用户名（首页问候语用）"
  }
}
```

## 社交链接

在 `socialButtons` 数组中添加或修改：

```json
"socialButtons": [
  {
    "id": "github",
    "type": "github",
    "value": "https://github.com/你的用户名",
    "label": "Github",
    "order": 1
  },
  {
    "id": "email",
    "type": "email",
    "value": "你的邮箱@example.com",
    "label": "邮箱",
    "order": 2
  },
  {
    "id": "juejin",
    "type": "juejin",
    "value": "https://juejin.cn/user/你的ID",
    "label": "稀土掘金",
    "order": 3
  },
  {
    "id": "bilibili",
    "type": "bilibili",
    "value": "https://space.bilibili.com/你的ID",
    "label": "B站",
    "order": 4
  },
  {
    "id": "twitter",
    "type": "twitter",
    "value": "https://x.com/你的用户名",
    "label": "Twitter",
    "order": 5
  },
  {
    "id": "wechat",
    "type": "wechat",
    "value": "微信号或公众号名称",
    "label": "微信",
    "order": 6
  }
]
```

支持的 `type`：`github`、`email`、`juejin`、`bilibili`、`twitter`、`wechat`、`qq`、`telegram`、`discord`、`link`（通用链接）

## 主题颜色

```json
"theme": {
  "colorBrand": "#2fcbe7",        // 品牌主色（按钮、强调色）
  "colorPrimary": "#5B423F",      // 主要文字颜色
  "colorSecondary": "#8b7667",    // 次要文字颜色
  "colorBrandSecondary": "#eec25e", // 辅助品牌色
  "colorBg": "#d4e8f3",           // 页面背景色
  "colorBorder": "#ffffff",       // 边框颜色
  "colorCard": "#ffffff99",       // 卡片背景色（带透明度）
  "colorArticle": "#ffffffcc"     // 文章区域背景色
}
```

常用配色方案：

| 风格 | colorBrand | colorBg |
|------|-----------|---------|
| 天蓝（默认） | `#2fcbe7` | `#d4e8f3` |
| 粉色 | `#ff6b9d` | `#ffe4ef` |
| 绿色 | `#4ade80` | `#dcfce7` |
| 紫色 | `#a78bfa` | `#ede9fe` |
| 橙色 | `#fb923c` | `#ffedd5` |
| 暗黑 | `#60a5fa` | `#1e293b` |

## 背景渐变色

```json
"backgroundColors": [
  "#f7da3987",  // 渐变色1
  "#8fdbe9",    // 渐变色2
  "#fffef8"     // 渐变色3
]
```

## 首页卡片开关

首页每个卡片都可以独立开关：

```json
"cardStyles": {
  "artCard": { "enabled": true },
  "hiCard": { "enabled": true },
  "clockCard": { "enabled": true },
  "calendarCard": { "enabled": true },
  "socialButtons": { "enabled": true },
  "shareCard": { "enabled": true },
  "articleCard": { "enabled": true },
  "writeButtons": { "enabled": true },
  "likePosition": { "enabled": true },
  "hatCard": { "enabled": true },
  "beianCard": { "enabled": true }
}
```

> 卡片样式配置在 `src/config/card-styles.json` 中

## 其他选项

| 字段 | 说明 | 值 |
|------|------|-----|
| `clockShowSeconds` | 时钟显示秒数 | `true` / `false` |
| `enableCategories` | 启用博客分类 | `true` / `false` |
| `enableChristmas` | 圣诞节雪花特效 | `true` / `false` |
| `hideEditButton` | 隐藏编辑按钮 | `true` / `false` |
| `currentHatIndex` | 首页头像帽子样式 | `0`-`24`（0=无帽子） |
| `hatFlipped` | 翻转帽子 | `true` / `false` |

## 备案信息

如果需要显示 ICP 备案号：

```json
"beian": {
  "text": "京ICP备XXXXXXXX号",
  "link": "https://beian.miit.gov.cn/"
}
```

## 头像图片

替换 `public/images/avatar.png` 文件即可更换头像。

## 修改生效方式

1. 编辑 `src/config/site-content.json`
2. 提交并推送代码：
   ```bash
   git add src/config/site-content.json
   git commit -m "更新网站配置"
   git push origin main
   ```
3. 等待 Netlify/Vercel 自动部署完成（约1-2分钟）
4. 刷新网站查看效果

## 网页端配置

部署后也可以直接在网站上修改配置：
- 首页右上角点击配置按钮
- 或按 `Ctrl + ,` 打开配置面板
- 需要先输入 Private Key 认证
