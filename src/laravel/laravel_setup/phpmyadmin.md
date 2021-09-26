# phpmyadminの追加

## コンテナの停止

コンテナが動いている場合は下記コマンドで停止させる．

```bash
$ ./vendor/bin/sail down
```

## phpmyadminのコンテナを追加

`docker-compose.yml`に下記を追記する．

```yml
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    links:
      - mysql:mysql
    ports:
      - 8080:80
    environment:
      MYSQL_USERNAME: "${DB_USERNAME}"
      MYSQL_ROOT_PASSWORD: "${DB_PASSWORD}"
      PMA_HOST: mysql
    networks:
      - sail
```

追記場所は`selenium`付近．

```yml
  selenium:
    image: "selenium/standalone-chrome"
    volumes:
      - "/dev/shm:/dev/shm"
    networks:
      - sail
# この下に追記
```

yamlファイルはインデントが重要となるため全コピペ推奨．

```yaml
# For more information: https://laravel.com/docs/sail
version: "3"
services:
    laravel.test:
        build:
            context: ./vendor/laravel/sail/runtimes/8.0
            dockerfile: Dockerfile
            args:
                WWWGROUP: "${WWWGROUP}"
        image: sail-8.0/app
        extra_hosts:
            - "host.docker.internal:host-gateway"
        ports:
            - "${APP_PORT:-80}:80"
        environment:
            WWWUSER: "${WWWUSER}"
            LARAVEL_SAIL: 1
            XDEBUG_MODE: "${SAIL_XDEBUG_MODE:-off}"
            XDEBUG_CONFIG: "${SAIL_XDEBUG_CONFIG:-client_host=host.docker.internal}"
        volumes:
            - ".:/var/www/html"
        networks:
            - sail
        depends_on:
            - mysql
            - redis
            - meilisearch
            - selenium
    mysql:
        image: "mysql:8.0"
        ports:
            - "${FORWARD_DB_PORT:-3306}:3306"
        environment:
            MYSQL_ROOT_PASSWORD: "${DB_PASSWORD}"
            MYSQL_DATABASE: "${DB_DATABASE}"
            MYSQL_USER: "${DB_USERNAME}"
            MYSQL_PASSWORD: "${DB_PASSWORD}"
            MYSQL_ALLOW_EMPTY_PASSWORD: "yes"
        volumes:
            - "sailmysql:/var/lib/mysql"
        networks:
            - sail
        healthcheck:
            test: ["CMD", "mysqladmin", "ping", "-p${DB_PASSWORD}"]
            retries: 3
            timeout: 5s
    redis:
        image: "redis:alpine"
        ports:
            - "${FORWARD_REDIS_PORT:-6379}:6379"
        volumes:
            - "sailredis:/data"
        networks:
            - sail
        healthcheck:
            test: ["CMD", "redis-cli", "ping"]
            retries: 3
            timeout: 5s
    meilisearch:
        image: "getmeili/meilisearch:latest"
        ports:
            - "${FORWARD_MEILISEARCH_PORT:-7700}:7700"
        volumes:
            - "sailmeilisearch:/data.ms"
        networks:
            - sail
        healthcheck:
            test:
                [
                    "CMD",
                    "wget",
                    "--no-verbose",
                    "--spider",
                    "http://localhost:7700/health",
                ]
            retries: 3
            timeout: 5s
    mailhog:
        image: "mailhog/mailhog:latest"
        ports:
            - "${FORWARD_MAILHOG_PORT:-1025}:1025"
            - "${FORWARD_MAILHOG_DASHBOARD_PORT:-8025}:8025"
        networks:
            - sail
    selenium:
        image: "selenium/standalone-chrome"
        volumes:
            - "/dev/shm:/dev/shm"
        networks:
            - sail
    phpmyadmin:
        image: phpmyadmin/phpmyadmin
        links:
            - mysql:mysql
        ports:
            - 8080:80
        environment:
            MYSQL_USERNAME: "${DB_USERNAME}"
            MYSQL_ROOT_PASSWORD: "${DB_PASSWORD}"
            PMA_HOST: mysql
        networks:
            - sail
networks:
    sail:
        driver: bridge
volumes:
    sailmysql:
        driver: local
    sailredis:
        driver: local
    sailmeilisearch:
        driver: local

```

## 動作確認

下記コマンドでコンテナを起動する．

```bash
$ ./vendor/bin/sail up -d
```

立ち上がったら，ブラウザで`localhost:8080`にアクセスするとphpmyadminにアクセスできる．

ユーザ名とパスワードは以下の通り．

|ユーザ名|パスワード|
|-|-|
|`sail`|`password`|


## 注意点

Laravelではテーブルの設定などはマイグレーションによって行うため，phpmyadminで不用意に操作すると予期しないエラーが発生する場合がある．

そのため，カラム名の調整などはphpmyadminから行わないことをオススメする．

レコードの追加などは問題ないので適宜活用していきましょう．
