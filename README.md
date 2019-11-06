# docker_amazonlinux_base

## 概要

docker開発環境を想定して用意しました。apache-php + mysql構成で、apache-phpコンテナのイメージにはamazonlinuxを使用しています。

## 構成

No|種別|内容|備考
-|-|-|-
1|web|apache + php7|
2|db|mysql8|

## 導入方法

### 事前作業

#### ローカルPC作業（mac想定）

- ドメインみたいに名前付きURLを開発環境で使用したいので、下記の作業を実施しておく

##### ローカルPCのhostsに追記

```shell
sudo sh -c "cat <<EOF >> /private/etc/hosts
# local develop local
127.0.0.1    smx.local
127.0.0.1    stone.smx.local
127.0.0.1    smxweb.smx.local
EOF"
```

- サービスが増える毎にここの追記も増えて行きます

##### mkcertを使用しssl証明書を発行

```shell
brew install mkcert # chrome
brew install nss    # firefox
```

###### mkcert実行前に注意点

- mkcertはjavaを使用するので入っていない場合は事前にjavaを入れて**JAVA_HOME**の設定をしておいて下さい
- 下記のechoで何も返ってこない場合は、**JAVA_HOME**の設定かそもそもjavaが入っていない可能性があります

```shell
echo $JAVA_HOME
ls -al $JAVA_HOME/bin/keytool  # keytoolを使用するらしい
```

###### mkcert実行

- 上の注意点がクリアになっている前提

```shell
cd /tmp    # 一応tmpで作業

mkcert -install    # ローカル証明書登録
mkcert smx.local "*.smx.local"    # ドメインの鍵作成
```

- もう一度鍵を再作成したい場合は、`mkcert -uninstall`して`mkcert -install`からやり直して下さい

```shell
ls -al /tmp/*.pem
```

- **smx.local+1-key.pem**と**smx.local+1.pem**が作成されていると思います
- 別名で作成された場合は、/docker_amazonlinux_base/docker/apache下にあるconfファイル内の設定を書き換える必要があります

###### 作成されたpemファイルを配置

```shell
mv /tmp/*.pem (このリポジトリをクローンした場所)/docker_amazonlinux_base/docker/apache/
```

- ローカルでの作業を以上になります

### docker-compose起動

#### dockerを起動する前準備

```shell
git clone git@gitlab.skymatix.jp:SkymatiX/docker_amazonlinux_base.git
cd docker_amazonlinux_base
```

##### 個別情報設定

```shell
vi .env
```

- SET_USER : webサーバのユーザー ユーザー名がパスワードになります
- SET_AWS_***** : aws cliの設定です

###### mkcert実行で個別にドメインを作成した場合の対応

- `smx.local "*.smx.local"` とは違うドメインを作成した場合下記の対応が必要になります
    1. docker/apache/下にあるpemファイルを差し替えて下さい
    2. docker/apache/vhost__default.conf内にあるpemファイル名も変更して下さい

#### dockerコンテナ作成

```shell
docker-compose up -d

# 下記コマンドで、dev_db_1, dev_web_1のStateが正常であれば成功です
docker-compose ps
```

#### dockerコンテナに入る方法

- webサーバのコンテナ名 : web
- dbサーバのコンテナ名 : db

```shell
# webサーバ
docker-compose exec web bash

# webサーバにユーザーで入りたい場合
docker-compose exec -u {SET_USERで設定したユーザー名} web bash

# dbサーバ
docker-compose exec db bash
```

- 抜ける場合は、`exit` で抜けられます

#### 各アプリの配置

```shell
cd server/skymatix

# 各プロジェクトをクローンする
# 例). stoneプロジェクトであれば
git clone git@gitlab.skymatix.jp:SkymatiX/stone.git
```

- stoneシステムの起動方法は、stoneプロジェクトのreadme参照
