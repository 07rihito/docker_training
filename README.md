# docker_training

このrepositryは，Docker入門の練習をするために使用したものです．  
以下，Dockerを使用する際のコマンドなどを記述していきます．  
(備忘録を兼ねているので，何か間違いなどがあればご指摘を頂けると幸いです．)  

## Dockerhubへログイン

```sh
docker login
```

## imageをrepositryへpull

```sh
docker pull {image:tag}
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

上記のようなファイルを作成し，同じフォルダで```$ docker build .```でビルドできる．  
(名前をつけてビルドするには```$ docker build -t <image名>:<tag名> .```とすると良い．)

## 参考資料

- [docker](http://datawokagaku.com/ "docker")

## DockerCompose

### docker-composeの構成

docker-compose.yamlファイルのテンプレ的なものを書きに書いておく．
dbとwebアプリの構成としてmysqlとwordpressコンテナの組み合わせを例に書いてみる．
environmentの項目はファイルにベタ書きせずに別ファイルなどに格納した方が良いが今回はcomposeファイルに記載する．

linksでservice同士をリンクさせていたが，現在は非推奨らしい．変わりにnetworksで同一のnetworkに所属させる．

ディレクトリ構成は下記のようにする．

```
work
├── docker-compose.yml
├── db/
│   ├── Dockerfile
│   ├── data/
│   ├── my.cnf
│   ├── log/
|   └── sql/
└── web/
    └── volume/
```



```yaml
version: '3.8'

services:
  db:  # mysqlの場合を参考に
    build:
      context: ./db  # Dockerfileの場所
      dockerfile: Dockerfile  # Dockerfileの名前
    image: my_db_image  # image名の指定
    container_name: my_db_container  # container名の指定
    hostname: my_db_host  # host名の指定
    restart: always
#    privileged: true
    environment:  # 環境変数の指定(下記は一例)
      MYSQL_ROOT_PASSWORD: rootPass
      MYSQL_DATABASE: testdb  # optional
      MYSQL_USER: user1  # optional
      MYSQL_PASSWORD: user1Pass  # optional
#      MYSQL_ALLOW_EMPTY_PASSWORD: "yes"  # optional
#      MYSQL_RANDOM_ROOT_PASSWORD: "yes"  # optional
#      MYSQL_ONETIME_PASSWORD: "yes"  # optional (MySQL 5.6 or above)
#      MYSQL_INITDB_SKIP_TZINFO: ""  # optional
      TZ: "Asia/Tokyo"
#    ports:  # portの指定(host:container)
#      - "3306":"3306"
    volumes:
      - ./db/data:/var/lib/mysql
      - ./db/my.cnf:/etc/mysql/conf.d/my.cnf
      - ./db/sql:/docker-entrypoint-initdb.d
      - ./db/log:/var/log/mysql
    networks:
      - my_network
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --default-authentication-plugin=mysql_native_password # mysql:5.7の場合は不要
  web:  # wordpressの場合を参考に
    build:
      contexts: ./web
      dockerfile: Dockerfile
    image: my_web
    networks:
      - my_network
    volumes:
      - ./web/volume:/var/www/html
    ports:
      - "8080":"80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_NAME: testdb
      WORDPRESS_DB_USER: user1
      WORDPRESS_DB_PASSWORD: user1Pass
    depends_on:
      - db
networks:
  my_network:
# volumes:  # バインドボリュームを使う場合は不要
#     mysqlVol1:
#     wordpressVol1
```

### docker-composeの起動

```sh
#!/bin/sh

# build docker container using docker-compose file
docker-compose up -d

```

### docker-composeの停止

```sh
#!/bin/sh

# stop container using docker-compose file
docker-compose down
```
