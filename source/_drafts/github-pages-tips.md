---
title: GitHub-pages-tips
tags:
---

### GitHub pages 提示

- 个人和组织只可以有一个页面
- 个人和组织如果需要创建GitHub pages仓库的名称必须固定为  `<username>.github.io` 或者 `<organization>.github.io`
- 如果username里面有大写字母，仓库名称需要修改为小写。因为 URL 大小写不敏感

- 仓库站点可以有多个
- 仓库使用仓库名称作为域名，但是不限定仓库的名称。`http(s)://<username>.github.io/<repository>` 或者 `http(s)://<organization>.github.io/<repository>`.
- 用来发布页面内容的分支默认为 `gh-pages` 然后使用的页面 html资源 来自于亘文件夹。也就是整个分支。
- 也可以进行调整 资源文件夹调整从 `/`到`/doc` 文件夹里。 当然分支也可以进行修改。 如果使用 `/doc`  那么`/docs/CNAME`  里面的`CNAME`  文件也会被发布。

- 如果选择任意分支上的 `docs` 文件夹作为发布源，然后从仓库的该分支中删除了 `/docs` 文件夹，则您的站点将不会构建，并且您将收到提示缺失 /docs 文件夹的页面构建错误。

### 使用限制
- 仓库大小限制是 1GB

- 发布的页面 不能超过1GB

- 每个月有100GB的带宽限制

- 每小时最多可以构建发布10次

## 创建
- 如果站点是一个独立项目，可以创建一个 `/doc` 文件夹，然后使用`/doc`进行创建
- 如果是个人站 就设置好仓库名称是自己github 的用户名称
- 先创建好仓库，然后创建站点
- 在发布源的根目录中，创建一个名为 index.md、包含要在网站主页上显示的内容的文件。
- 非常重要 `如果存在 index.html，则将使用它而不是 index.md。 如果没有 index.html 或 index.md，将使用 README.md。`
- 如果仓库是公有的那么就是使用的 actions 进行发布，或者自己配置了CI工具。
- 添加新文件，它就是在分支下面添加文件，然后页面会进行显示。
- 如果项目网站的发布源是 `gh-pages` 分支，并且您在 `gh-pages` 分支上创建了名为 `/about/contact-us.md` 的新文件，该文件将在 `https://<user>.github.io/<repository>/about/contact-us.html` 下。
- 如果使用了其他的CI工具 其实也是构建内容后发布到 `gh-pages` 然后 剩下的部分还是由`GitHub actions` 的CI 进行发布，只是不需要构建了。`github actions` 会检测到不需要构建流程而只执行部署过程。
- `.nojekyll` 如果使用其他CI工具会添加这个文件，用来禁用Github actions的构建和加载主题。

## 选择主题
- 通过pages 页面进行选择主题，然后应用它
- 主题会应用到README.md/index.md 文件上，来对md文件进行布局。
- 如果主文件是   index.html  需要使用config.yaml来进行配置

- [创建 404 页面](https://docs.github.com/cn/pages/getting-started-with-github-pages/creating-a-custom-404-page-for-your-github-pages-site) 其实就是添加一个 `404.html`  只是如果是.md 需要添加 `permalink: /404.html` 在 config.yml  开头好让github 进行构建。

