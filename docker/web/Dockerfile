FROM centos:7
MAINTAINER horitya.biriko <horityabiriko@gmail.com>

# TimeZoneの設定
RUN echo 'ZONE"=Asia/Tokyo"' > /etc/sysconfig/clock

# yumアップデート
RUN yum -y update

# wgetのインストール
RUN yum install -y wget
RUN wget http://ftp.jaist.ac.jp/pub/Linux/Fedora/epel//7/x86_64/e/epel-release-7-10.noarch.rpm
RUN yum install -y epel-release-7-10.noarch.rpm
RUN rpm --import http://rpms.famillecollet.com/RPM-GPG-KEY-remi
RUN wget ftp://195.220.108.108/linux/remi/enterprise/7/remi/x86_64/remi-release-7.3-2.el7.remi.noarch.rpm
RUN yum install -y remi-release-7.3-2.el7.remi.noarch.rpm
RUN wget ftp://mirror.switch.ch/pool/4/mirror/epel/7/x86_64/l/libc-client-2007f-16.el7.x86_64.rpm
RUN yum install -y libc-client-2007f-16.el7.x86_64.rpm

# Apacheのインストール
RUN yum install -y httpd

# Apacheの設定
RUN rm -rf /etc/httpd/conf.d/welcome.conf
RUN sed -ri '/<Directory "\/var\/www\/html">/,/<\/Directory>/s/    AllowOverride None/    AllowOverride All/' /etc/httpd/conf/httpd.conf && \
    sed -ri '/<Directory "\/var\/www\/html">/,/<\/Directory>/s/    Options Indexes FollowSymLinks/    Options Indexes FollowSymLinks Includes/' /etc/httpd/conf/httpd.conf && \
    sed -ri 's/DocumentRoot "\/var\/www\/html"/DocumentRoot "\/var\/www\/html\/htdocs"/g' /etc/httpd/conf/httpd.conf && \
    sed -ri 's/DirectoryIndex index.html index.html.var/DirectoryIndex index.html index.shtml index.html.var/' /etc/httpd/conf/httpd.conf

# phpのインストール
RUN yum install --enablerepo=remi-php71 -y php php-cli php-common php-devel php-fpm php-gd php-mbstring php-pgsql php-mcrypt php-mysqlnd       php-pdo php-pear php-pear-DB php-pear-Auth-* php-pecl-apc-devel php-pecl-apcu php-soap php-xml php-xmlrpc

# phpの設定
RUN sed -ri 's/;date.timezone =/date.timezone = Asia\/Tokyo/' /etc/php.ini && \
    sed -ri 's/display_errors = Off/display_errors = On/' /etc/php.ini && \
    sed -ri 's/post_max_size = 8M/post_max_size = 100M/' /etc/php.ini && \
    sed -ri 's/upload_max_filesize = 2M/upload_max_filesize = 100M/' /etc/php.ini && \
    sed -ri 's/;mbstring.language = Japanese/mbstring.language = Japanese/' /etc/php.ini && \
    sed -ri 's/;mbstring.internal_encoding = EUC-JP/mbstring.internal_encoding = UTF-8/' /etc/php.ini && \
    sed -ri 's/;mbstring.http_input = auto/mbstring.http_input = pass/' /etc/php.ini && \
    sed -ri 's/;mbstring.http_output = SJIS/mbstring.http_output = pass/' /etc/php.ini && \
    sed -ri 's/;mbstring.encoding_translation = Off/mbstring.encoding_translation = Off/' /etc/php.ini

# メールサーバのインストール
RUN yum install -y postfix
RUN chkconfig postfix on

# crontabインストール
RUN yum install -y crontabs

# supervisordのインストール
RUN yum install -y supervisor

## supervisordの設定
RUN touch /etc/supervisord.conf
RUN echo '[supervisord]' >> /etc/supervisord.conf
RUN echo 'nodaemon=true' >> /etc/supervisord.conf
#RUN echo '[program:sshd]' >> /etc/supervisord.conf
#RUN echo 'command=/etc/init.d/sshd start' >> /etc/supervisord.conf
RUN echo '[program:apache]' >> /etc/supervisord.conf
RUN echo 'command=/usr/sbin/apachectl -D FOREGROUND' >> /etc/supervisord.conf
RUN echo '[program:postfix]' >> /etc/supervisord.conf
RUN echo 'command=/etc/init.d/postfix start' >> /etc/supervisord.conf
RUN echo '[program:crontab]' >> /etc/supervisord.conf
RUN echo 'command=/etc/init.d/crond start' >> /etc/supervisord.conf

EXPOSE 80
RUN export CI_ENV="development"
WORKDIR /var/www/html

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]
