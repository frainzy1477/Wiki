?> 教程使用的环境：Arch Linux/x86_64 架构

## 安装

Arch Linux 官方网站：https://archlinux.org/ 。
本文会从0开始安装SSPanel UIM，使用包管理安装的软件包如下

- Nginx
- Composer
- MariaDB

## 安装需要的包

```bash
sudo pacman -S mariadb nginx-mainline base-devel git composer
```

Mariadb 安装后需要初始化

```bash
mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

启动数据库和开机自启

```bash
systemctl enable mariadb --now
```

提高初始安全性

```bash
mariadb-secure-installation
```

进入数据库

```bash
mysql -u root
```

创建 SSPanel 用户以及数据库

```sql
MariaDB> CREATE USER `sspanel`;
MariaDB> CREATE DATABASE 'sspanel'@'localtion' CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci IDENTIFIED BY 'PASSWD';
MariaDB> GRANT ALL PRIVILEGES ON sspanel.* TO 'sspanel'@'localhost';
```

安装 PHP 相关

安装 Yay（Yet Another Yogurt） 包管理器

```bash
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

安装 PHP82

```bash
yay -S $(yay -Ssq php82)
```

启动 PHP-FPM 和开机自启

```bash
systemctl enable php82-fpm --now
```

## 部署 SSPanel UIM

任意位置创建 SSPanel 网站根目录文件夹，执行以下命令：

```bash
git clone -b 2023.5 https://github.com/Anankke/SSPanel-Uim.git .
composer
composer install
```

?> 这里的 2023.5 代表的是 SSPanel UIM 的版本，你可以在 [Release](https://github.com/Anankke/SSPanel-Uim/releases) 页面中查看当前的最新稳定版本或者是输入 dev 使用开发版。请注意，dev 分支可能在使用过程中出现不可预知的问题。

修改 Nginx 配置文件

```bash
vim /etc/nginx/nginx.conf
# vim 可以替换为你喜欢的任何编辑器如 vi nano
systemctl reload nginx
```

示例配置

```nginx
server {
        listen 80;
        listen [::]:80;

        root /path/to/your/site/public; #你的站点文件路径 + /public
        index index.php index.html;
        server_name 你设置的网站域名;

        location / {
            try_files $uri /index.php$is_args$args;
        }

        location ~ \.php$ {
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include        fastcgi_params;
                fastcgi_pass unix:/run/php82-fpm/php-fpm.sock;
        }

}
```

然后设置网站目录的整体权限

```bash
chmod -R 755 /path/to/your/site
chown -R http:http /path/to/your/site
```

配置数据库，将上文的 SQL 填写到配置文件

```bash
cp config/.config.example.php config/.config.php
cp config/appprofile.example.php config/appprofile.php
vi config/.config.php
```

?> 按 i 键进入编辑模式，使用 :x 保存并退出 vi，使用 :q! 放弃任何改动并退出 vi。

接下来执行如下站点初始化设置

```bash
php82 xcat Migration new
php82 xcat Tool importAllSettings
php82 xcat Tool createAdmin
php82 xcat ClientDownload
```

如果你希望使用 Maxmind GeoLite2 数据库来提供 IP 地理位置信息，首先你需要配置 `config/.config.php` 中的 `maxmind_license_key` 选项，然后执行如下命令：

```bash
php82 xcat Update
```

使用 `crontab -e` 指令设置 SSPanel 的基本 cron 任务：

```bash
*/5 * * * * /usr/bin/php82 /path/to/your/site/xcat  Cron
```
