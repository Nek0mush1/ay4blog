# Navfolio 修改说明

这份说明根据这四篇指南整理而来：

- <https://astro.navfolio.site/blog/toml-site-config-guide/>
- <https://astro.navfolio.site/blog/markdown-style-guide/>
- <https://astro.navfolio.site/blog/vibe-content-guide/>
- <https://astro.navfolio.site/blog/categories-series-guide/>

目标是快速知道：应该在哪个文件改什么、怎么让内容显示、怎么本地检查。

## 先看这几个位置

多数个性化修改不需要改组件，优先改这些文件和目录：

```text
src/config/site.toml        站点标题、个人资料、导航、首页模块、主题、搜索、评论、Vibe 设置
src/content/about.mdx       About 页面内容
src/content/blog/           博客文章
src/content/projects/       项目页面
src/content/vibe/           Vibe 短记录
public/images/              公开静态图片，页面里用 /images/xxx.png 引用
src/assets/figure/          内容图片，Markdown/MDX 里用相对路径引用
src/components/Icon.astro   可用图标名映射
src/content.config.ts       内容和 TOML 配置的字段校验规则
```

注意：当前项目里 `src/config/site.toml` 和 `README.md` 有部分中文已经显示为乱码。后续修改中文时请用 UTF-8 编辑器重新输入原文，不要在乱码文本上继续复制修改。

## 运行和显示

开发预览：

```sh
bun run dev
```

生产构建：

```sh
bun run build
```

预览生产构建：

```sh
bun run preview
```

搜索功能依赖生产构建生成的 Pagefind 索引。开发环境里如果没有跑过 `bun run build`，搜索弹层可能提示索引不可用。

所有文章、项目、Vibe 的 `draft: true` 都不会公开显示。想让内容出现在页面上，改成：

```yaml
draft: false
```

## 修改站点配置

主要改 `src/config/site.toml`。

常用分区：

```text
[config.site]              站点标题、描述、仓库地址、页脚文字
[config.profile]           作者姓名、身份、邮箱、网站、GitHub、头像
[[config.topNav.links]]    顶部导航链接
[config.theme]             主题色盘
[config.search]            搜索入口、快捷键、占位文案、结果数量
[config.blog]              博客列表分页数量
[config.vibe]              Vibe 时间流显示行为
[config.comments]          评论系统开关和 provider
[config.home]              首页布局和各模块数据
```

首页模块重点：

```text
[config.home.quote]        首页引语和图片
[config.home.intro]        首页介绍文案和图片
[config.home.latest]       首页最新文章数量、热力图周数等
[[config.home.navigation]] 首页导航卡片
[[config.home.connect]]    联系方式入口
[[config.home.showcase]]   首页经历和产品展示卡片
```

图标字段 `icon` 使用 `src/components/Icon.astro` 里的映射。当前可用的常见值包括：

```text
compass, pen, briefcase, github, book, repo, mail, link, star,
folder, layers, calendar, clock, map, building, twitter
```

如果要新增图标，需要在 `src/components/Icon.astro` 从 `lucide-astro` 引入图标，并加入 `icons` 对象，然后才能在 TOML 里使用新名字。

配置会被 `src/content.config.ts` 用 Zod 校验。比如邮箱必须像邮箱，URL 必须像 URL，`postsPerPage` 和 `maxResults` 必须是正整数。字段错了，`bun run build` 会报错。

## 写博客文章

博客文件放在：

```text
src/content/blog/
```

创建新文章：

```sh
bun run post:new my-first-post
```

创建 MDX 文章：

```sh
bun run post:new my-interactive-post --mdx
```

文件名 slug 会决定 URL，例如：

```text
src/content/blog/my-first-post.md
=> /blog/my-first-post/
```

常用 frontmatter：

```yaml
---
title: "文章标题"
description: "文章摘要，会用于列表和 meta 信息"
date: "2026-06-16"
draft: false
showHeroImage: true
heroImage: "../../assets/figure/example.png"
tags:
  - Astro
categories:
  - 内容管理
series:
  - Navfolio 模块手册
comments: true
sidebar:
  enable: true
  toc: true
  relatedPosts: true
---
```

`sidebar` 控制文章右侧阅读工具：

```text
enable        是否启用侧栏
toc           是否显示目录
relatedPosts  是否显示相关文章
```

Markdown 支持常见语法：标题、段落、加粗、斜体、行内代码、链接、图片、引用、表格、代码块、有序/无序列表、脚注，以及部分 HTML 内联标签。

代码块建议写语言名：

````md
```ts
const message = "hello";
```
````

普通图片：

```md
![图片说明](../../assets/figure/example.png)
```

如果图片放在 `public/images/`，页面里直接写：

```md
![图片说明](/images/example.png)
```

## 分类、标签、系列

这三者用途不同：

```text
tags        文章关键词，可以多且自由，例如 Astro、Markdown、Search
categories 稳定栏目，例如 Guide、Essay、Project
series     连续阅读路径，例如 Navfolio 模块手册
```

写法：

```yaml
tags: ["Astro", "Markdown"]
categories:
  - 内容管理
series:
  - Navfolio 模块手册
```

构建时会自动生成：

```text
/blog/categories/
/blog/categories/[category]/
/blog/series/
/blog/series/[series]/
```

系列内文章默认按发布日期从早到晚排序。文章如果属于多个系列，文章页会显示多个系列入口，但上一篇/下一篇系列导航优先使用第一个系列。

## 写 Vibe 短记录

Vibe 文件放在：

```text
src/content/vibe/
```

创建新 Vibe：

```sh
bun run vibe:new today-cloud
```

创建 MDX Vibe：

```sh
bun run vibe:new photo-note --mdx
```

脚本会生成带日期前缀的文件，例如：

```text
src/content/vibe/2026-06-16-today-cloud.md
```

最小格式：

```yaml
---
date: 2026-06-16
draft: false
---

今天的小记录。
```

常用字段：

```yaml
---
title: "window light"
date: 2026-06-16
updatedDate: 2026-06-16
draft: false
type: text
mood: quiet
location: Tianjin
images: []
tags: [daily]
align: left
size: md
---
```

字段说明：

```text
type    text | photo | quote | code | mixed
align   left | right | center；不写时自动左右交替
size    sm | md | lg
images  图片数组，photo 或 mixed 常用
```

Vibe 图片路径相对于 `src/content/vibe/`。如果图片在 `src/assets/figure/`，通常写：

```yaml
images:
  - ../../assets/figure/example.png
```

Vibe 时间流轨迹线在 `src/config/site.toml` 控制：

```toml
[config.vibe]
showTrail = true
```

`false` 会隐藏桌面端轨迹线和节点，也不会加载绘制轨迹线脚本。移动端本来就会隐藏轨迹线。

## 改 About 和 Projects

About 页面：

```text
src/content/about.mdx
```

项目入口或项目说明：

```text
src/content/projects/
```

它们和博客文章共用类似的 frontmatter schema，也支持 `title`、`description`、`date`、`draft`、`heroImage`、`tags`、`comments`、`sidebar` 等字段。

## 评论系统

全局配置在 `src/config/site.toml`：

```toml
[config.comments]
enabled = true
provider = "giscus"
show_on_posts = true
```

可选 provider：

```text
giscus, utterances, waline, none
```

单篇文章关闭评论：

```yaml
comments: false
```

如果用 giscus、utterances、waline，需要继续填写对应 `[config.comments.giscus]`、`[config.comments.utterances]` 或 `[config.comments.waline]` 的仓库/服务端参数。

## 主题、代码块、搜索

主题色盘在：

```toml
[config.theme]
palette = "green-soft"
```

当前 schema 支持：

```text
green-soft, green-vivid, rose-soft, pink-soft, purple-soft,
blue-soft, orange-soft, brown-soft
```

代码块配置：

```toml
[config.code]
lightTheme = "catppuccin-latte"
darkTheme = "catppuccin-macchiato"
lineNumbers = true
wrap = true
preserveIndent = true
collapseStyle = "collapsible-auto"
```

搜索配置：

```toml
[config.search]
enabled = true
shortcut = "mod+k"
placeholder = "Search notes..."
maxResults = 6
```

## 最常见操作流程

改个人站点信息：

```text
1. 打开 src/config/site.toml
2. 改 [config.site]、[config.profile]、[[config.topNav.links]]
3. 改 [config.home] 下面的 quote、intro、navigation、connect、showcase
4. bun run dev 查看
5. bun run build 检查 schema 和生产构建
```

发一篇博客：

```text
1. bun run post:new article-slug
2. 打开 src/content/blog/article-slug.md
3. 填 title、description、date
4. 写正文
5. draft 改成 false
6. 需要分类就填 categories；需要系列就填 series
7. bun run dev 查看 /blog/article-slug/
```

发一条 Vibe：

```text
1. bun run vibe:new note-slug
2. 打开 src/content/vibe/日期-note-slug.md
3. 填 date、type、tags、align、size
4. 写短内容
5. draft 改成 false
6. bun run dev 查看 /vibe
```

新增图片：

```text
公开静态图：放 public/images/，用 /images/xxx.png
内容源图：放 src/assets/figure/，用相对路径引用
Vibe 引用 assets 图：../../assets/figure/xxx.png
```

构建前检查：

```sh
bun run build
```

如果构建失败，优先看报错里提到的 frontmatter 字段、TOML 字段、URL、email、数字类型或图片路径。
