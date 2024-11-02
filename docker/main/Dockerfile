# Dockerfile for systemd base image
# https://hub.docker.com/_/centos Systemd integration
FROM local/centos84-systemd

# サポート切れリポジトリの置換
RUN sed -i -e 's/^mirrorlist/#mirrorlist/g' -e 's/^#baseurl=http:\/\/mirror/baseurl=http:\/\/vault/g' /etc/yum.repos.d/CentOS-Linux*repo
RUN dnf -y update --nobest

# centos 8.5以降は公式imageがないのでcentos streamに乗り換える
# centos stream
#RUN dnf -y install centos-release-stream &&\
#    sed -i -e 's/^mirrorlist/#mirrorlist/g' -e 's/^#baseurl=http:\/\/mirror/baseurl=http:\/\/vault/g' /etc/yum.repos.d/CentOS-Stream*repo &&\
#    dnf -y swap centos-{linux,stream}-repos &&\
#    sed -i -e 's/^mirrorlist/#mirrorlist/g' -e 's/^#baseurl=http:\/\/mirror/baseurl=http:\/\/vault/g' /etc/yum.repos.d/CentOS-Stream*repo &&\
#    dnf clean all

# remi repository
RUN dnf -y install https://rpms.remirepo.net/enterprise/remi-release-8.4.rpm

# php7.4
RUN dnf -y install php74 php74-php-{gd,xml,mbstring,mysqlnd,embedded,opcache,pecl-apcu,pecl-memcached,zip,fpm}

# php8.1
RUN dnf -y install php81 php81-php-{gd,xml,mbstring,mysqlnd,embedded,opcache,pecl-apcu,pecl-memcached,zip,fpm}

# php-fpmをnginx用に設定変更
#RUN sed -i -e 's/^;listen.owner = nobody/listen.owner = nginx/g' /etc/opt/remi/php*/php-fpm.d/www.conf
#RUN sed -i -e 's/^;listen.group = nobody/listen.group = nginx/g' /etc/opt/remi/php*/php-fpm.d/www.conf
RUN sed -i -e 's/^;listen.mode = 0660/listen.mode = 0660/g' /etc/opt/remi/php*/php-fpm.d/www.conf
RUN sed -i -e 's/^listen.acl_users = apache/listen.acl_users = nginx/g' /etc/opt/remi/php*/php-fpm.d/www.conf
RUN sed -i -e 's/^;listen.acl_groups = /listen.acl_groups = nginx/g' /etc/opt/remi/php*/php-fpm.d/www.conf
RUN sed -i -e 's/^user = apache/user = nginx/g' /etc/opt/remi/php*/php-fpm.d/www.conf
RUN sed -i -e 's/^group = apache/group = nginx/g' /etc/opt/remi/php*/php-fpm.d/www.conf

# php-fpmを起動設定
RUN systemctl enable php74-php-fpm
RUN systemctl enable php81-php-fpm
RUN systemctl list-unit-files --type=service |grep php-fpm

# nginx
RUN dnf -y install nginx

# nginx.conf
COPY ./nginx/nginx.conf.php74 /etc/nginx/nginx.conf.php74
COPY ./nginx/nginx.conf.php81 /etc/nginx/nginx.conf.php81

# php7.4用confを適用
RUN cp /etc/nginx/nginx.conf.php74 /etc/nginx/nginx.conf
# php8.1用confを適用
#RUN cp /etc/nginx/nginx.conf.php81 /etc/nginx/nginx.conf

# nginxを自動起動
RUN systemctl enable nginx

# 使用PHPバージョン切り替えスクリプトの配置
COPY ./scripts /scripts 

#CMD ["/bin/bash"]
#CMD ["nginx", "-g", "daemon off;"]
CMD ["/usr/sbin/init"]
