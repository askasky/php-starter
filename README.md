# 描述
此项目是基于Laravel5.6的一个脚手架，用Dockerfile来生成我们使用的容器镜像，默认开启xdebug，方便php调试。
源于：https://github.com/richarvey/nginx-php-fpm

## 准备工作

#### 在相应的系统上安装Docker CE，如CentOS 
https://docs.docker.com/install/linux/docker-ce/centos/

#### 是否启用XDebug调试
修改Dockerfile的15-17行
ENV XDEBUG_CONFIG="remote_host=192.168.1.106" 这是宿主机的ip地址
ENV PHP_IDE_CONFIG="serverName=localhost" 这个localhost要与phpstorm中servers里的配置的name一致，与nginx里配置的server_name一致

注意：在启动容器的时候一定要关闭phpstorm的debug listening，否则composer install 会超时。

#### 修改 docker-compose.yml 的环境变量

## 使用docker-compose来启动服务
```bash
docker-compose up -d --build # 建立并启动服务
docker-compose down # 关闭服务
docker-compse down -v # 关闭服务并卸载卷
```

## 访问
#### 网站
http://localhost
#### 数据库管理phpMyAdmin
http://localhost:8080

## docker相关命令行操作
#### 建立镜像
```bash
docker build -t mariadb:latest -f docker/mariadb/Dockerfile docker/mariadb
docker build -t nginx-php-fpm:latest -f docker/nginx-php-fpm/Dockerfile docker/nginx-php-fpm
docker pull phpmyadmin/phpmyadmin
```

#### 启动容器
```bash
# 环境参数跟项目的.env中的对应
docker run --name mariadb -d -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=123456 \
    -e MYSQL_DATABASE=sample_db \
    -e MYSQL_USER=docker \
    -e MYSQL_PASSWORD=123456 \
    mariadb
docker run -d -p 80:80 --link mariadb:db -v /Users/sky/project/php/mytest/src:/var/www/html nginx-php-fpm
docker run -d -p 8080:80 --link mariadb:db phpmyadmin/phpmyadmin
```

#### 显示日志和进入容器操作
```bash
docker ps -a # 列出所有容器
docker logs xxx # 查看某个容器的日志
docker exec -it xxx bash  # 进入容器
docker rm xxx # 删除容器
docker rmi xxx # 删除镜像
```
