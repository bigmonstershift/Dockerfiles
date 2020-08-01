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
|-- memorycard_mysql.tar
`-- memorycard_rails.tar
```

## イメージ作成/コンテナ起動
用意したイメージファイルをロード.
```
docker load < memorycard_mysql.tar
docker load < memorycard_rails.tar
```
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
memorycard_mysql    latest              68e826628769        4 hours ago         544MB
memorycard_rails    latest              30f48c0bbcf4        5 hours ago         1.99GB
```
コンテナ起動
```
sudo docker run -d -p 3006:3006 --name mysql memorycard_mysql
sudo docker run -itd -p 3000:3000 --name rails memorycard_rails
```
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                         NAMES
03b835c7b194        memorycard_rails    "/bin/bash"              4 seconds ago       Up 2 seconds        0.0.0.0:3000->3000/tcp                        rails
c5d6e761bb95        memorycard_mysql    "docker-entrypoint.s…"   44 seconds ago      Up 43 seconds       3306/tcp, 0.0.0.0:3006->3006/tcp, 33060/tcp   mysql
```

## config/database.yml編集
`rails`コンテナに入りコンフィグ編集、DB作成、サーバ起動を実施.  
laboプロジェクトの場所が`/root/labo`となっている.
```
cd /root/labo
sed -i -e "s/host: localhost/host: 172.18.0.2/" /root/labo/config/database.yml
sed -i -e "s/password:/password: password/" /root/labo/config/database.yml
rails db:create
rails server -b 0.0.0.0
```
