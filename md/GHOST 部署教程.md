> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [test.hosteye.net](https://test.hosteye.net/ghostbu-shu-jiao-cheng/#%E9%83%A8%E7%BD%B2%E8%87%AA%E6%89%98%E7%AE%A1%E6%96%B9%E6%A1%88)

> 前言 在我自己搭建 HOSTEYE 博客系统的过程中，经历了许多挑战和困惑。

前言
--

在我自己搭建 HOSTEYE 博客系统的过程中，经历了许多挑战和困惑。虽然网上有很多优秀的教程可以参考，但我决定将自己的搭建经验整理成一个教程，希望踏上 Ghost 博客路上朋友们提供一些帮助。

本教程不仅会介绍 Ghost 的安装与配置，更会针对我在搭建过程中所遇到的各种问题进行详细的解答。从域名购买到服务器搭建，再到主题选择和插件优化，我将全面详细的写出教程。无论你是技术上的小白，还是有一定经验的用户，都希望本教程能够对你有所启发。

> 在开始构建你的 GHOST 博客之旅之前，请务必心怀耐心和热情。技术世界中的探索与发现永无止境，并且建立自己的博客也是一段愉快而神奇的过程。

如果您在阅读过程中有任何疑问或困惑，请随时向我提问。

<table><thead><tr><th>特点</th><th>自托管</th><th>托管</th></tr></thead><tbody><tr><td>技术自由度</td><td>需要较高的技术知识，可以自定义设置和优化</td><td>相对较少技术要求，依赖托管服务提供商</td></tr><tr><td>成本控制</td><td>可以根据需求选择合适的服务器和套餐</td><td>套餐价格固定，可能相对较贵</td></tr><tr><td>技术挑战</td><td>需要处理服务器的安装、更新、备份等任务</td><td>免去繁琐的服务器管理，专注于内容创作</td></tr><tr><td>稳定性和可靠性</td><td>取决于自己服务器和配置的稳定性</td><td>由 Ghost 官方或可靠托管服务商提供稳定性和可靠性</td></tr></tbody></table>

aa

1.  创建一个新用户，用户名需自行替换

```
adduser <user>


```

```
New password:            
# 看到这个提示后，输入你想设置的密码。这里要注意，输入的密码是隐藏 不可见的，输入完毕后回车即可


```

```
Retype new password: 
# 重新输入上一步的密码


```

```
Enter the new value, or press ENTER for the default
# 这里按回车默认
        Full Name []: 
        Room Number []: 
        Work Phone []: 
        Home Phone []: 
        Other []: 


```

```
Is the information correct? [Y/n] 
# 这里确认信息输入正确后输入 y 并按下回车


```

2.  为新增用户添加 sudo 权限

```
usermod -aG sudo <user>
# 将<user>替换成你最开始设置的用户名


```

```
su - <user>
# 切换到这个用户


```

```
root@ns348668:~# adduser hosteye
Adding user `hosteye' ...
Adding new group `hosteye' (1001) ...
Adding new user `hosteye' (1001) with group `hosteye' ...
Creating home directory `/home/hosteye' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for hosteye
Enter the new value, or press ENTER for the default
Full Name []: 
Room Number []: 
Work Phone []: 
Home Phone []: 
Other []: 
Is the information correct? [Y/n] y
root@ns348668:~# usermod -aG sudo hosteye
root@ns348668:~# su - hosteye
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.


```

```
# 安装nginx
sudo apt-get install nginx


```

```
# 检查防火墙是否开启
sudo ufw status


```

```
# 如果防火墙开启，输入此命令允许 HTTP 和 HTTPS 连接。
sudo ufw allow 'Nginx Full'


```

```
Rules updated
Rules updated (v6)
# 开启成功后会输出此信息


```

```
# 安装MySQL
sudo apt-get install mysql-server

# 进入mysql
sudo mysql

# 重置mysql root 密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '<password>';

# 退出mysql
quit;


```

```
# 从 NodeSource 添加 Node.js 18 下载源
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash

# 安装 Node.js
sudo apt-get install -y nodejs



```

```
# 使用npm安装 Ghost-CLI
sudo npm install ghost-cli@latest -g

# 如果出现以下提示，这是 npm 存在新版本可升级的提示。这里可以自行选择是否需要升级。
npm notice 
npm notice New minor version of npm available! 9.6.7 -> 9.8.1
npm notice Changelog: https://github.com/npm/cli/releases/tag/v9.8.1
npm notice Run npm install -g npm@9.8.1 to update!
npm notice 


```

```
# 创建目录： 将 `sitename` 更改为站点名，或其他
sudo mkdir -p /var/www/sitename

# 设置目录所有者： 将 <user> 替换为一开始设置的用户名。 ！注意区分站点名和用户名
sudo chown <user>:<user> /var/www/sitename

# 设置正确的权限
sudo chmod 775 /var/www/sitename

# 然后进入
cd /var/www/sitename



```

```
ghost install


```

```
# 以下是安装示例，请参考。
hosteye@ns348668:/var/www/hosteye$ ghost install

Love open source? We’re hiring JavaScript Engineers to work on Ghost full-time.
https://careers.ghost.org


# 检查系统环境
✔ Checking system Node.js version - found v18.17.0
✔ Checking current folder permissions
✔ Checking memory availability
✔ Checking free space
✔ Checking for latest Ghost version
✔ Setting up install directory
✔ Downloading and installing Ghost v5.55.1
✔ Finishing install process

# 输入你希望绑定的确切 URL，包括 HTTP 或 HTTPS 协议。例如，https://example.com. 如果使用 HTTPS，Ghost-CLI 将会设置 SSL。使用 IP 地址会导致错误。
? Enter your blog URL: https://hosteye.net

# 输入MySQL主机名。如果MySQL安装在其他服务器上，请手动输入对应主机名。
? Enter your MySQL hostname: localhost

# 输入 root ，然后输入 root 用户的密码。如果你已经有一个 MySQL 数据库，请输入对应的用户名和密码。
? Enter your MySQL username: root
? Enter your MySQL password: [hidden]

# 如果还没有创建过数据库，可以直接使用默认值： db_ghost 或者输入想要设置的数据库名。随后系统会开始自动设置。如果在上一步中使用的是非 root 的 MySQL用户名/密码，需要确保该数据库已经存在，并且具有正确的权限。
? Enter your Ghost database name: db_hosteye

✔ Configuring Ghost
✔ Setting up instance
+ sudo useradd --system --user-group ghost
+ sudo chown -R ghost:ghost /var/www/hosteye/content
✔ Setting up "ghost" system user

# 如果在此前提供了 root MySQL 用户，Ghost-CLI 可以自动创建一个属于 Ghost 数据库的自定义 MySQL 用户，该用户只能访问/编辑新的 Ghost 数据库，而不能执行其他操作。
# 输入y确定创建
? Do you wish to set up "ghost" mysql user? Yes
✔ Setting up "ghost" mysql user

# 自动设置NGINX
? Do you wish to set up Nginx? Yes
+ sudo mv /tmp/hosteye-net/hosteye.net.conf /etc/nginx/sites-available/hosteye.net.conf
+ sudo ln -sf /etc/nginx/sites-available/hosteye.net.conf /etc/nginx/sites-enabled/hosteye.net.conf
+ sudo nginx -s reload
✔ Setting up Nginx

# 自动设置SSL。要注意在第一步要输入带 https 的地址作为URL，并且正确配置了记录
? Do you wish to set up SSL? Yes

# SSL 认证设置需要一个电子邮件地址，以便你可以在证书出现任何问题（包括续订期间）时收到通知。
? Enter your email (For SSL Certificate) w1ntercoat.miro@gmail.com
+ sudo mkdir -p /etc/letsencrypt
+ sudo ./acme.sh --install --home /etc/letsencrypt
+ sudo /etc/letsencrypt/acme.sh --issue --home /etc/letsencrypt --server letsencrypt --domain hosteye.net --webroot /var/www/hosteye/system/nginx-root --reloadcmd "nginx -s reload" --accountemail w1ntercoat.miro@gmail.com --keylength 2048
+ sudo openssl dhparam -dsaparam -out /etc/nginx/snippets/dhparam.pem 2048
+ sudo mv /tmp/ssl-params.conf /etc/nginx/snippets/ssl-params.conf
+ sudo mv /tmp/hosteye-net/hosteye.net-ssl.conf /etc/nginx/sites-available/hosteye.net-ssl.conf
+ sudo ln -sf /etc/nginx/sites-available/hosteye.net-ssl.conf /etc/nginx/sites-enabled/hosteye.net-ssl.conf
+ sudo nginx -s reload
✔ Setting up SSL

# systemd是推荐的进程管理器工具，以保持 Ghost 顺利运行。建议选择yes。但也可以设置自己的流程管理。
? Do you wish to set up Systemd? Yes
+ sudo mv /tmp/hosteye-net/ghost_hosteye-net.service /lib/systemd/system/ghost_hosteye-net.service
+ sudo systemctl daemon-reload
✔ Setting up Systemd
+ sudo systemctl is-active ghost_hosteye-net

# 确定启动Ghost
? Do you want to start Ghost? Yes
+ sudo systemctl start ghost_hosteye-net
+ sudo systemctl is-enabled ghost_hosteye-net
+ sudo systemctl enable ghost_hosteye-net --quiet
✔ Starting Ghost

Ghost uses direct mail by default. To set up an alternative email method read our docs at https://ghost.org/docs/config/#mail

------------------------------------------------------------------------------

# Ghost安装完成
Ghost was installed successfully! To complete setup of your publication, visit: 

    https://example/ghost/


```

### 8. 如果安装失败怎么办

如果安装出现严重错误，请使用`ghost uninstall`将其删除并重试。这比删除文件夹更可取，以确保不留下任何痕迹。

如果安装中断或连接丢失，请用于`ghost setup`重新启动配置过程。

对于故障排除和错误，请使用站点搜索和常见问题[解答部分](https://ghost.org/docs/faq/?ref=test.hosteye.net)来查找有关常见错误消息的信息。

站点配置 - 简单美化篇
------------

### 1. 初始设置

https:// 你的域名 / ghost 进入后台

[![](https://test.hosteye.net/content/images/2023/07/ghost------.png)](https://test.hosteye.net/content/images/2023/07/ghost------.png)

按照提示，输入网站标题，用户名，电子邮箱，密码等信息。

[![](https://test.hosteye.net/content/images/2023/07/------.png)](https://test.hosteye.net/content/images/2023/07/------.png)

设置完成后，这就是你的初始网站页面了。

### 2. 后台页面介绍

[![](https://test.hosteye.net/content/images/2023/07/-----1.png)](https://test.hosteye.net/content/images/2023/07/-----1.png)[![](https://test.hosteye.net/content/images/2023/07/-------1.png)](https://test.hosteye.net/content/images/2023/07/-------1.png)