## 準備
最新版のDockerをインストール
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
最新版のdocker-composeをインストール
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
```
$ docker-compose -v
docker-compose version 1.26.2, build eefe0d31
```
