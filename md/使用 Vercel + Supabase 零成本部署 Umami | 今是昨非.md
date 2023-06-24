> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zuofei.net](https://zuofei.net/5018.html)

> 早些年用过友盟、51.la、百度统计、Google Analytics，各有各的优缺点，百度统计目前只允许备案网站使用，友盟和 51.la 体验效果不喜欢，Google Analytics 访问也不太好，权衡之下使用这款开源的网站统计服务——Umami。

早些年用过友盟、51.la、百度统计、Google Analytics，各有各的优缺点，百度统计目前只允许备案网站使用，友盟和 51.la 体验效果不喜欢，Google Analytics 访问也不太好，权衡之下使用这款开源的网站统计服务——Umami。

![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/20230424085849.6uue596hp3k0.webp)

根据官网介绍，[Umami](https://github.com/umami-software/umami) 是一款简单、易用、美观、轻量、快速、隐私、安全的开源免费网站统计工具，从部署到使用，都是简单、快速，体验也很满意。

[官方文档](https://umami.is/docs/)介绍了多种部署方式，包括使用自己服务器，或者第三方平台，例如 Netlify、Heroku、Railway 等等，不过这些第三方平台一般都不提供数据库服务，所以我选择使用 Supabse 提供的免费数据库服务，以及 Vercel 的部署。

在使用前，先注册并登录好 [GitHub](https://github.com/)、[Vercel](https://vercel.com/)、[Supabase](https://supabase.com/) 三个平台，并在 GitHub 平台 Fork Umami 的项目仓库。

在 Supabase 建立数据库
----------------

![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/2023_9130_supabase.3hyoeg3aqp00.webp)

在官网选择 [Free](https://supabase.com/pricing) 方案，进入 Create a new project 页面，按要求填写相关内容。Name 填写任意项目名，Database Password 可以使用下方工具 Generate a password 生成，并保存到记事本备用。

![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/20230424085956.7a3bvdm3cpg0.webp)

等待数据库建立，需要几分钟的时间。

建立之后，点击左下方的 Project Settings，选择 Database，找到 Connection string 中的 URL 一栏，复制内容，并将 **[YOUR-PASSWORD]** 替换为上一步生成的密码。

![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/0230424090454.3uya78wr9zi0.webp)

Supabase 平台的操作就结束了。

在 Vercel 部署 Umami
-----------------

登录 Vercel 之后，点击右上角 Add New Project，并导入事先 Fork 的项目仓库。

![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/20230424093134.7j39y4ynm6o0.webp) ![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/20230424090338.5hx60tko8lk0.webp)

在 Configure Project 中需要设置两个环境变量（Environment Variables）。

![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/20230424090758.4wooxtytmu40.webp)

分别添加 **DATABASE_URL** 和 **HASH_SALT**。前者是上一步在 Subabase 复制的 URL，记得替换自己的 Password；后者需要自己随意生成一长串字符串。最后点击 Deploy，等待两分钟。

![](https://cdn.staticaly.com/gh/huhexian/img@master/20230424/20230424090827.hyj4gf67hmw.webp)

如果有需要，可以先绑定好自己的域名，因为 Vercel 提供的域名在大陆无法访问。

使用 Umami
--------

按照上述步骤，Umami 已经部署成功了，通过绑定的域名进入网站，默认用户名和密码分别是 **admin** 和 **umami** ，进入后台可以修改密码、设置语言，然后就可以添加网站了。

提示：如果需要删除网站，先将语言切换至 English，中文状态无法删除。