## 念頭
ローカルや、クラウド環境下でデータ分析環境を容易に構築するためのDockerfile。


## 構成
```
# Host (Local or AWS/GCP)
docker/
  ┝ dsenv_build/
  │　　└ Dockerfile
  └ ds_python/ (任意)

# Docker Containar
  /
  ┝ bin/
  ~
  ┝ opt/
  │　└ anaconda3
  └ work/ (「ds_python」と紐づいている。)
     └ (eg.「test.ipnb」　Jupyterlabで分析する場所。)
```

## 構築手順（ローカル）
1. 準備
任意にローカル上にディレクトリを作成して移動。
```sh
$ mkdir docker
$ cd docker
```

2. Dockerイメージの作成
```sh
$ docker build .
```

3. Dockerコンテナ起動
```sh
$ docker run -p 8888:8888 -v ~/docker/ds_python:work --name my_lab {image}
```


## 構築手順（クラウド）
eg. AWSのEC2にサーバーインスタンスを建てて、そこにログイン。

1. SSHでクラウドに接続（セキュアにログイン）
```sh
$ ssh -i hoge.pem ubuntu@ec2-00-100-200-300.ap-northeast-1.compute.amazonaws.com
```
nb.
* `hoge.pem`はAWSのアクセス認証鍵で名前は任意。
* `ec2-{~}.compute.amazonaws.com`PublicDNSは都度取得。

2. クラウド上にDockerCE(ComunicatEdition)をインストール
```sh
(aws)$ sudo apt-get update
(aws)$ sudo apt-get install docker.io
(aws)$ docker --version
(aws)$ sudo gpasswd -a ubuntu docker #sudo無しでdockerコマンド使用する為
```
nb. DockerEE(EnterprizeEdition)はガチの奴だから今回はCE

3. Dockerイメージをクラウドに送る
 * ローカルでDockerimageをtarで圧縮
```sh
$ docker build .
$ docker save {imageID} > myimage.tar
```
  nb. MacのM1チップ使用時は、`$ docker build --platform linux/amd64.`
 * SFTPでローカルからクラウドへファイル転送（セキュアに）
```sh
$ sftp -i hoge.pem ubuntu@ec2-00-100-200-300.ap-northeast-1.compute.amazonaws.com
(aws)$ put ~/docker/myimage.tar home/ubuntu
```

4. クラウド上でDockerイメージを解凍
```sh
(aws)$ docker load < myimage.tar
```

5. クラウド上でDockerコンテナ起動
```sh
(aws)$ docker run -it myimage bash
```

