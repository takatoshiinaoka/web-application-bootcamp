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

追記場所は87行目付近．

```yml
  selenium:
    image: "selenium/standalone-chrome"
    volumes:
      - "/dev/shm:/dev/shm"
    networks:
      - sail
# この下に追記
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
