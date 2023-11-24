## why
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
1. 準備(任意にローカル上にディレクトリを作成して移動)
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
__eg.__ AWSのEC2にサーバーインスタンスを建てて、そこにログイン。(以下AWSの説明)

### 1. SSHでクラウドに接続（セキュアにログイン）
```sh
$ ssh -i hoge.pem ubuntu@ec2-00-100-200-300.ap-northeast-1.compute.amazonaws.com
```
__nb.__
* 予めAWSにインスタンスを作成しておく。
* `hoge.pem`はAWSのアクセス認証鍵で名前は任意。（鍵のあるディレクトリで実行）
* `ec2-{~}.compute.amazonaws.com`PublicDNSは都度取得。

### 2. クラウド上にDockerCE(ComunicatEdition)をインストール
```sh
(aws)$ sudo apt-get update
(aws)$ sudo apt-get install docker.io
(aws)$ docker --version
(aws)$ sudo gpasswd -a ubuntu docker #sudo無しでdockerコマンド使用する為
```
__nb.__ DockerEE(EnterprizeEdition)はガチの奴だから今回はCE

### 3. Dockerイメージをクラウドに送る
#### (case1) クラウドがネットワークに繋がれるなら。
* SFTPでローカルからクラウドへファイル転送（セキュアに）
```sh
$ sftp -i hoge.pem ubuntu@ec2-00-100-200-300.ap-northeast-1.compute.amazonaws.com
(aws)$ put ~/docker/dsenv_build/Dockerfile home/ubuntu
```
* buildcontextを作成して、ファイル移動
```sh
$ ssh -i hoge.pem ubuntu@ec2-00-100-200-300.ap-northeast-1.compute.amazonaws.com
(aws)$ mkdir dsenv_build
(aws)$ mv Dockerfile dsenv_build
```
* dockerイメージ作成
```sh
(aws)$ cd dsenv_build
(aws)~/dsenv_build$ docker build .
```
* dockerコンテナ、JupyterLab起動。
```sh
(aws)~dsenv_build$ docker run -v ~:/work -p 8888:8888 {imageID}
```

`@ec2-00-100-200-300.ap-northeast-1.compute.amazonaws.com:8888`
にブラウザーからアクセスする`。


#### (case2) クラウドがネットワークに繋がれないなら。
* ローカルでDockerimageをtarで圧縮
```sh
$ docker build .
$ docker save {imageID} > myimage.tar
```
__nb.__ MacのM1チップ使用時は、`$ docker build --platform linux/amd64.`

* SFTPでローカルからクラウドへファイル転送（セキュアに）
```sh
$ sftp -i hoge.pem ubuntu@ec2-00-100-200-300.ap-northeast-1.compute.amazonaws.com
(aws)$ put ~/docker/myimage.tar home/ubuntu
(aws)$ docker load < myimage.tar #tarファイルを解凍
```
__nb.__ `$ docker load`で解凍され、同時にイメージが作られる。

### 4. クラウド上でDockerコンテナ起動
```sh
(aws)$ docker run -it {imageID} bash
```

