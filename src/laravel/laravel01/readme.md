# Day06

## 多用するコマンドまとめ

※ここでは実行しません！！ 開発中に忘れたら見よう！

### 仮想コンテナを立ち上げる

```bash
$ ./vendor/bin/sail up -d
```

### 仮想コンテナを終了させる

```bash
$ ./vendor/bin/sail down
```

### Laravelコンテナへログインする

```bash
$ docker-compose exec laravel.test bash
```

### MySQLコンテナへログインする

```bash
$ docker-compose exec mysql bash
```

### コンテナからログアウトする

```bash
$ exit
```

