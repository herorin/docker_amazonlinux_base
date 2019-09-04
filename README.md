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

（これから追記予定）
