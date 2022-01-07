## 念頭
ローカル環境下で、データ分析環境を容易に構築するためのDockerfile

## 構成

```
# Host
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

## 構築手順

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

