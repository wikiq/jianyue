> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.rustwiki.org.cn](https://www.rustwiki.org.cn/zh-CN/book/)

> Rust 程序设计语言中文也译为 Rust 权威指南，是 Rust 官方推出的学习 Rust 的必备教程。

[Rust 程序设计语言](#rust-程序设计语言)
===========================

_由 Steve Klabnik 和 Carol Nichols，以及来自 Rust 社区贡献者撰写_

> 中文翻译注（Chinese translation of the [The Rust Programming Language](https://doc.rust-lang.org/book)）：
> 
> 1.  👉 查看更多 [Rust 官方文档中英文双语教程](https://www.rustwiki.org.cn/)，本站还提供了 [Rust 标准库中文版](https://www.rustwiki.org.cn/zh-CN/std/)。
> 2.  **本站翻译已参照最新的 [Rust 1.58.0 版](https://doc.rust-lang.org/1.58.0/book/)及[开发版](https://doc.rust-lang.org/nightly/book/)进行调整**，这是目前网上最新的中文版本，最后更新时间 2022 年 2 月 6 日。
> 3.  《Rust 程序设计语言》(The Rust Programming Language 中文版) 翻译自 [The Rust Programming Language](https://doc.rust-lang.org/book)，查看此书的 [GitHub 翻译项目和源码](https://github.com/rust-lang-cn/book-cn)。
> 4.  《Rust 程序设计语言》中文出版书名为《Rust 权威指南》，参见 [“为什么 The Rust Programming Language 在线版书名翻译成《Rust 程序设计语言》”](https://www.rustwiki.org.cn/wiki/translate/other-translation/#the-rust-programing-language)。
> 5.  本书已有由 [KaiserY 翻译完的版本](https://github.com/KaiserY/trpl-zh-cn)，Rust 中文翻译项目组将把之前未翻译完的内容直接采用 KaiserY 版内容，后续 Rust 中文翻译项目组将跟随 Rust 官方的英文版本更新，进一步查看[本书翻译说明](https://www.rustwiki.org.cn/docs/book/)。
> 6.  [本站支持文档中英文切换](https://www.rustwiki.org.cn/en/book)，点击页面右上角语言图标可切换到相同章节的英文页面，**英文版每天都会自动同步一次官方的最新版本**。
> 7.  若发现当前页表达错误或帮助我们改进翻译，可点击右上角的编辑按钮打开该页对应源码文件进行编辑和修改，Rust 中文资源的开源组织发展离不开大家，感谢您的支持和帮助！

本书的版本假定你使用 Rust 1.58（2022 年 1 月 13 日发布）或更高版本。请参阅[第 1 章的 “安装” 章节](ch01-01-installation.html)来安装或更新 Rust。

本文档的 HTML 格式在线版为 [https://www.rustwiki.org.cn/zh-CN/book/](https://www.rustwiki.org.cn/zh-CN/book/) （英文版为：[https://doc.rust-lang.org/stable/book/](https://doc.rust-lang.org/stable/book/)）；而离线版在使用 `rustup` 安装 Rust 后附带（注：目前此命令附带的文档只包含英文版，中文离线版可拉取[本书的中文翻译 GitHub 仓库](https://github.com/rust-lang-cn/book-cn)生成） ，运行 `rustup docs --book` 来打开本书。

本文档还提供了一些社区[翻译版本](appendix-06-translation.html)。

可以从 [No Starch Press 获得纸质图书和电子书](https://nostarch.com/rust)（注：中文出版书名为《Rust 权威指南》，可从购书平台中购买）。