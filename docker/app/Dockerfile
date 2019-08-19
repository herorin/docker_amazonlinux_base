FROM amazonlinux:latest

# .bashrcをローカルからコピー
COPY ../os_setting/.bashrc /root/.bashrc
RUN source /root/.bashrc

# タイムゾーンファイルの変更(即座に変わります)
RUN ln -sf  /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
 && echo 'ZONE="Asia/Tokyo"' > /etc/sysconfig/clock \
 && echo 'UTC=false' >> /etc/sysconfig/clock \
 && echo "ap-northeast-1" > /etc/yum/vars/awsregion

# 基本機能追加
RUN yum update -y \
 && yum install -y \
      vim
      procps \
      systemd \
      yum-utils \
      unzip \
      wget \
      sudo \
      httpd

# リポジトリ追加
RUN yum install -y \
      https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
      http://rpms.remirepo.net/enterprise/remi-release-7.rpm \
      https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm

# リポジトリ：amzn2-coreの優先順位を下げる
# remi,remi-php72,mysql80をenableにする
RUN yum-config-manager --setopt="amzn2-core.priority=99" --save amzn2-core \
 && yum-config-manager --enable \
      remi \
      remi-php72 \
      mysql80-community

# 開発に必要な機能追加
RUN yum install -y \
      php \
      php-devel \
      php-mbstring \
      php-pdo \
      php-gd \
      php-xml \
      php-mcrypt \
      php-zip \
      php-json \
      php-mysqlnd \
      mysql-community-client \
      mysql-community-devel \
 && yum clean all

# composer追加
RUN curl -sS https://getcomposer.org/installer | php \
 && mv composer.phar /usr/local/bin/composer

# php設定ファイルをローカルからコピー
COPY ./php.ini /etc/php.ini

# port指定
EXPOSE 80

# 起動
CMD [ "/usr/sbin/apachectl", "-D", "FOREGROUND" ]
