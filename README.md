# docker_training
このrepositryは，Docker入門の練習をするために使用したものです．  
以下，Dockerを使用する際のコマンドなどを記述していきます．  
(備忘録を兼ねているので，何か間違いなどがあればご指摘を頂けると幸いです．)  



## Dockerhubへログイン
```
$ docker login
```
## imageをrepositryへpull
```
$ docker pull {image:tag}
```
.
.
.

## Dockerfile

### Dockerfileとは
Docker imageの設計図的なもの，Dockerfileに記述した内容をbuildすることでDocker imageを作成することが可能．

### Dockerfileの構成
```dockerfile
FROM <Docker image>
RUN <>
COPY <>
ADD <>
```
上記のようなファイルを作成し，同じフォルダで```$ docker build . ```でビルドできる．  
(名前をつけてビルドするには```$ docker build -t <image名>:<tag名> . ```とすると良い．)

## 参考
- [docker](http://datawokagaku.com/ "docker")

## DockerCompose


### docker-composeの起動
```
#!/bin/sh

# build docker container using docker-compose file
docker-compose up -d

# enter the DB2 container
# docker-compose exec db2 /bin/bash
```

### docker-composeの停止
```
#!/bin/sh

# stop container using docker-compose file
docker-compose down
```                                           