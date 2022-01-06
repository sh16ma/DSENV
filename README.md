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
  └ work/ (「ds_python」と紐づいている)

```

---
## 手順

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
