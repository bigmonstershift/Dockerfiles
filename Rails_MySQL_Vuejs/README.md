※メモ書き
## 準備
1. 最新版のDockerをインストール
```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce -y
sudo systemctl start docker
```
```
$ docker -v
Docker version 19.03.12, build 48a66213fe
```
2. 最新版のdocker-composeをインストール
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
3. ローカルのイメージ`vuejs_on_rails.tar`を`scp`や`rsync`で実施ホストに転送
4. コンテナ起動前ファイル構成
```
$ tree --charset=C .
.
|-- README.md
`-- vuejs_on_rails.tar
```

## イメージ作成/コンテナ起動
用意したイメージファイルをロード
```
docker load < vuejs_on_rails.tar
```
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
vuejs_on_rails.tar  latest              8e9d5d8843d4        21 hours ago        1.11GB
```
コンテナ起動
```
docker load < vuejs_on_rails.tar
docker-compose up
```
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                         NAMES
be38b02b9ffd        vuejs_on_rails      "/bin/bash"              30 seconds ago      Up 28 seconds       0.0.0.0:3000->3000/tcp                        rails_vue
9c11507afb71        mysql8              "docker-entrypoint.s…"   3 minutes ago       Up 3 minutes        3306/tcp, 0.0.0.0:3006->3006/tcp, 33060/tcp   mysql
```

## config/database.yml編集
`rails`コンテナに入りコンフィグ編集、DB作成、サーバ起動を実施
```
cd /labo
sed -i -e "s/host: localhost/host: 172.17.0.2/" /labo/config/database.yml
sed -i -e "s/password:/password: password/" /labo/config/database.yml
rails db:create
rails server -b 0.0.0.0
```
