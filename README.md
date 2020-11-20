# Docker 笔记

## 跑 8 个版本 PHP-FPM

> 自己打镜像时源换来换去总有东西装不上, 干脆站在巨人肩膀上, 罢了罢了.
> 用这位[同学](https://github.com/chialab/docker-php)打的[镜像](https://hub.docker.com/r/chialab/php).

跑起来

```sh
docker run -d --name php-fpm54 -p 9054:9000 -v /var/www:/var/www chialab/php:5.4-fpm
docker run -d --name php-fpm55 -p 9055:9000 -v /var/www:/var/www chialab/php:5.5-fpm
docker run -d --name php-fpm56 -p 9056:9000 -v /var/www:/var/www chialab/php:5.6-fpm
docker run -d --name php-fpm70 -p 9070:9000 -v /var/www:/var/www chialab/php:7.0-fpm
docker run -d --name php-fpm71 -p 9071:9000 -v /var/www:/var/www chialab/php:7.1-fpm
docker run -d --name php-fpm72 -p 9072:9000 -v /var/www:/var/www chialab/php:7.2-fpm
docker run -d --name php-fpm73 -p 9073:9000 -v /var/www:/var/www chialab/php:7.3-fpm
docker run -d --name php-fpm74 -p 9074:9000 -v /var/www:/var/www chialab/php:7.4-fpm
```

比如本地用这个域名 `docker-php.test`, 参考[这个配置](docker-php.conf), 在 nginx 里配置个站点. 然后, 向 `/var/www/docker-php` 目录加个 `index.php` 文件用看运行正常不.

就写个

```php
<?php phpinfo(); ?>
```

访问一下看看:

- http://docker-php.test/php54
- http://docker-php.test/php55
- http://docker-php.test/php56
- http://docker-php.test/php70
- http://docker-php.test/php71
- http://docker-php.test/php72
- http://docker-php.test/php73
- http://docker-php.test/php74

启动时写名字了, 不用哪个容器了这样停掉:

```sh
docker stop php-fpm55
```

## 跑 3 个版本 MySQL


给本地建几个目录, `/var/docker-mysql-datadirs/mysql56-datadir`.
我是在用户目录创建好 `docker-mysql-datadirs` 再软链接到 `/var` 目录去.

```sh
docker run -d --name mysql56 -v /var/docker-mysql-datadirs/mysql56-datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.6
docker run -d --name mysql57 -v /var/docker-mysql-datadirs/mysql57-datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.7
docker run -d --name mysql80 -v /var/docker-mysql-datadirs/mysql80-datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:8.0
```



