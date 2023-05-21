# Docker 笔记

> 重新 build 时 `docker build --no-cache ...`

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


给本地建几个目录, `/var/docker-mysqls/mysql56/datadir`.
我是在用户目录创建好 `docker-mysql-datadirs` 再软链接到 `/var` 目录去.

```sh
docker run -d --name mysql56 -v /var/docker-mysqls/mysql56/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.6
docker run -d --name mysql57 -v /var/docker-mysqls/mysql57/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.7
docker run -d --name mysql80 -p 3380:3306 -h 127.0.0.1 -v /var/docker-mysqls/mysql80/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root mysql:8.0
```
## 不要在 docker 里跑 nginx


## 跑一个 jenkins

先跑 dind

```sh
docker run \
  --name jenkins-docker \
  --rm \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind
```

再跑 jenkins

```sh
docker run \
  --name jenkins-blueocean \
  --rm \
  --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client \
  --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:1.1
```

取下布署密码

```sh
docker exec jenkins-docker cat /var/jenkins_home/secrets/initialAdminPassword
```

参考 [`jenkins.conf`](jenkins.conf) 在 nginx 里加个反向代理, 访问时就不写端口号了.

## 跑 Gitlab

.zshrc 里加一行

```sh
export GITLAB_HOME=/srv/gitlab
```

```sh
docker run --detach \
  --hostname gitlab.linwise.com \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  gitlab/gitlab-ee:latest
```



