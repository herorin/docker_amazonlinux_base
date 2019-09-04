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
      openssl \
      mod_ssl \
      python3 \
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
      php-opcache \
      php-devel \
      php-mbstring \
      php-pdo \
      php-gd \
      php-xml \
      php-mcrypt \
      php-zip \
      php-json \
      php-mysqlnd \
      php-pear \
      mysql-community-client \
      mysql-community-devel \
      ImageMagick7 \
      ImageMagick7-devel \
      php72-php-pear \
      php72-php-devel \
      php72-php-pecl-imagick \
      php72-php-pecl-imagick-devel \
 && yum clean all \
######################################
# composer追加
######################################
 && curl -sS https://getcomposer.org/installer | php \
 && mv composer.phar /usr/local/bin/composer \
######################################
# AWS Client追加
######################################
# yum python3でpip3も入るので個別にpip3は入れなくても大丈夫
 && pip3 install awscli --upgrade
######################################
# 環境変数
######################################
#ENV AWS_ACCESS_KEY_ID=${SET_AWS_ACCESS_KEY} \
#    AWS_SECRET_ACCESS_KEY=${SET_AWS_SECRET_ACCESS_KEY} \
#    AWS_DEFAULT_REGION=${SET_AWS_DEFAULT_REGION} \
#    AWS_DEFAULT_OUTPUT_FORMAT=${SET_AWS_DEFAULT_OUTPUT_FORMAT} \
#    TZ=${SET_TIME_ZONE}

######################################
# 各ファイルをローカルからコピー
######################################
COPY ./docker/os_setting/root.bashrc /root/.bashrc
#COPY ./docker/apache/httpd.conf /etc/httpd/conf/httpd.conf
COPY ./docker/apache/smx.local+1.pem /etc/pki/tls/certs/smx.local+1.pem
COPY ./docker/apache/smx.local+1-key.pem /etc/pki/tls/private/smx.local+1-key.pem
COPY ./docker/app/php.ini /etc/php.ini
COPY ./docker/app/php72.imagick.ini /etc/php.d/imagick.ini
COPY ./docker/apache/ssl.conf /etc/httpd/conf.d/ssl.conf
COPY ./docker/apache/vhost__default.conf /etc/httpd/conf.d/vhost__default.conf
COPY ./docker/apache/vhost_stone.conf /etc/httpd/conf.d/vhost_stone.conf

######################################
# ユーザー作成 (&&を仕様して連続で実行できなかったのでそれぞれRUNで呼んでます)
######################################
ARG SET_USER
RUN useradd -m ${SET_USER} && \
    echo "${SET_USER}:${SET_USER}" | chpasswd && \
    echo "${SET_USER} ALL=(ALL) ALL" >> /etc/sudoers

######################################
# working directory
######################################
WORKDIR /tmp

######################################
# port指定
######################################
EXPOSE 80

######################################
# 起動
######################################
CMD [ "/usr/sbin/httpd", "-D", "FOREGROUND" ]
