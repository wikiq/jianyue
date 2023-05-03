> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [eirms.com](https://eirms.com/2866.html#gallery-2)

> 介绍一款一条龙全自动添加豆瓣书影记录的插件。该插件为熊野在基于牧风的项目上开发的 WordPress 插件。

[![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAACbklEQVRoQ+2aMU4dMRCGZw6RC1CSSyQdLZJtKQ2REgoiRIpQkCYClCYpkgIESQFIpIlkW+IIcIC0gUNwiEFGz+hlmbG9b1nesvGW++zxfP7H4/H6IYzkwZFwQAUZmpJVkSeniFJKA8ASIi7MyfkrRPxjrT1JjZ8MLaXUDiJuzwngn2GJaNd7vyP5IoIYY94Q0fEQIKIPRGS8947zSQTRWh8CwLuBgZx479+2BTkHgBdDAgGAC+fcywoyIFWqInWN9BSONbTmFVp/AeA5o+rjKRJ2XwBYRsRXM4ZXgAg2LAPzOCDTJYQx5pSIVlrC3EI45y611osMTHuQUPUiYpiVooerg7TWRwDAlhSM0TuI+BsD0x4kGCuFSRVzSqkfiLiWmY17EALMbCAlMCmI6IwxZo+INgQYEYKBuW5da00PKikjhNNiiPGm01rrbwDwofGehQjjNcv1SZgddALhlJEgwgJFxDNr7acmjFLqCyJuTd6LEGFttpmkYC91Hrk3s1GZFERMmUT01Xv/sQljjPlMRMsxO6WULwnb2D8FEs4j680wScjO5f3vzrlNJszESWq2LYXJgTzjZm56MCHf3zVBxH1r7ftU1splxxKYHEgoUUpTo+grEf303rPH5hxENJqDKQEJtko2q9zGeeycWy3JhpKhWT8+NM/sufIhBwKI+Mta+7pkfxKMtd8Qtdbcx4dUQZcFCQ2I6DcAnLUpf6YMPxhIDDOuxC4C6djoQUE6+tKpewWZ1wlRkq0qUhXptKTlzv93aI3jWmE0Fz2TeujpX73F9TaKy9CeMk8vZusfBnqZ1g5GqyIdJq+XrqNR5AahKr9CCcxGSwAAAABJRU5ErkJggg==)](http://eirms.com/wp-content/uploads/2020/06/2020060911012597-scaled.jpg)

介绍一款一条龙全自动添加豆瓣书影记录的插件。该插件为[熊野](https://eirms.com/wp-content/themes/2012_mtr/r.php?aHR0cHM6Ly9iZWFyeWUuY24v "WordPress添加豆瓣书影记录——bmdb插件介绍 https://bearye.cn/")在基于牧风的项目上开发的 WordPress 插件。

在该页面申请 Secret：[https://bm.weajs.com/](https://bm.weajs.com/ "WordPress添加豆瓣书影记录——bmdb插件介绍 https://bm.weajs.com/") 另外还需要填写你的豆瓣 id 及昵称，在该网站可同步豆瓣平台标注的电影和书籍信息，也可以在上面标注电影。

下一步即是安装插件，填写好 secret 即可。新建页面并填入

[bm db]movies[/bm db] 或 [bm db]books[/bm db]   （使用时请删去空格）

作者的话：WordPress 自带 jQuery 并不支持 $ 关键字，head 设置 meta 也需要通过官方钩子实现，如果你想在自己的 WordPress 的站点上布置读书观影记录，并不能完全按照牧风的教程来做，对没有接触过编程的人来说，这存在一定难度。

下载地址：[https://github.com/ibearye/bmdb-for-wordpress](https://eirms.com/wp-content/themes/2012_mtr/r.php?aHR0cHM6Ly9naXRodWIuY29tL2liZWFyeWUvYm1kYi1mb3Itd29yZHByZXNz "WordPress添加豆瓣书影记录——bmdb插件介绍 https://github.com/ibearye/bmdb-for-wordpress")

牧风的项目地址：[https://github.com/iMuFeng/bmdb/](https://eirms.com/wp-content/themes/2012_mtr/r.php?aHR0cHM6Ly9naXRodWIuY29tL2lNdUZlbmcvYm1kYi8 "WordPress添加豆瓣书影记录——bmdb插件介绍 https://github.com/iMuFeng/bmdb/")

效果演示：[阅读记录](http://eirms.com/book.html) [观影记录](http://eirms.com/movie.html)

作者也介绍了集成到主题的方法。一并转载参考。

集成到主题
-----

在 wordpress 上布置 bmdb，核心基本与 Github 上的 readme 没区别，特别就在于如何在 wordpress 上正确

1.  设置头部 meta；
2.  引入资源文件。

第一点，设置头部 meta，在 `functions.php` 添加代码：

```
function bmdb_head()
{
    echo '<meta >';
}
add_action('wp_head','bmdb_head');

```

第二点，引入资源，在 `functions.php` 添加代码：

```
function bmdb_css_js(){
    wp_enqueue_script("jquery");//如果已引入jquery，就去掉这一行代码
    wp_enqueue_style( 'bmdb', get_template_directory_uri().'/dist/Bmdb.min.css' );//第二个参数填css的地址
    wp_enqueue_script( 'bmdb', get_template_directory_uri().'/dist/Bmdb.min.js' );//第二个参数填js的地址
}
add_action('wp_enqueue_scripts', 'bmdb_css_js');

```

如果你直接把 Github 上下载的 dist 文件夹扔到了主题文件夹里，上面的代码就不用改了。

这两点解决了其他就很简单了，没必要再说了。