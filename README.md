準備
```sh
$ mkdir docker
$ cd docker
```

Dockerイメージの作成
```sh
$ docker build .
```

Dockerコンテナ起動
```sh
$ docker run -p 8888:8888 -v ~/docker/ds_python:work --name my_lab {image}
```
