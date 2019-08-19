FROM amazonlinux:2

######################################
# amzn2リポジトリのダウンロード先を日本優先に変更
######################################
RUN echo 'ap-northeast-1' > /etc/yum/vars/awsregion \
######################################
# 基本機能追加
######################################
 && yum update -y \
 && yum install -y \
      vim \
      procps \
      systemd \
      yum-utils \
      unzip \
      wget \
      sudo \
      httpd \
######################################
# リポジトリ追加
######################################
 && yum install -y \
      https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
      http://rpms.remirepo.net/enterprise/remi-release-7.rpm \
      https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm \
######################################
# リポジトリ：amzn2-coreの優先順位を下げる
######################################
 && yum-config-manager --setopt="amzn2-core.priority=99" --save amzn2-core \
######################################
# remi,remi-php72,mysql80をenableにする
######################################
 && yum-config-manager --enable \
      remi \
      remi-php72 \
      mysql80-community \
######################################
# 開発に必要な機能追加
######################################
 && yum install -y \
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
 && yum clean all \
######################################
# composer追加
######################################
 && curl -sS https://getcomposer.org/installer | php \
 && mv composer.phar /usr/local/bin/composer

######################################
# 環境変数
######################################
ENV TZ='Asia/Tokyo'

######################################
# 各ファイルをローカルからコピー
######################################
COPY ./docker/os_setting/root.bashrc /root/.bashrc
COPY ./docker/apache/httpd.conf /etc/httpd/conf/httpd.conf
COPY ./docker/app/php.ini /etc/php.ini

######################################
# working directory
######################################
WORKDIR /var/www/html

######################################
# port指定
######################################
EXPOSE 80

######################################
# 起動
######################################
CMD [ "/usr/sbin/httpd", "-D", "FOREGROUND" ]
