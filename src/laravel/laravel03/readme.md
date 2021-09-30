# Day08

## 本日の内容

- マイページの実装（1 対多のデータ）．
- ユーザ名の表示（多対 1 のデータ）．
- favorite 機能の実装（多対多）．

## 本日の目標

- SNS アプリケーションを完成させる．
- 様々な形のデータを扱う方法を学ぶ．
- Laravel でアプリケーションを開発するイメージを持つ．

## ルーティング表

都度確認しよう！

```txt
+--------+-----------+-----------------------+----------------+-----------------------------------------------------+-----------------+
| Domain | Method    | URI                   | Name           | Action                                              | Middleware      |
+--------+-----------+-----------------------+----------------+-----------------------------------------------------+-----------------+
|        | GET|HEAD  | tweet                 | tweet.index    | App\Http\Controllers\TweetController@index          | web             |
|        | POST      | tweet                 | tweet.store    | App\Http\Controllers\TweetController@store          | web             |
|        | GET|HEAD  | tweet/create          | tweet.create   | App\Http\Controllers\TweetController@create         | web             |
|        | GET|HEAD  | tweet/{tweet}         | tweet.show     | App\Http\Controllers\TweetController@show           | web             |
|        | PUT|PATCH | tweet/{tweet}         | tweet.update   | App\Http\Controllers\TweetController@update         | web             |
|        | DELETE    | tweet/{tweet}         | tweet.destroy  | App\Http\Controllers\TweetController@destroy        | web             |
|        | GET|HEAD  | tweet/{tweet}/edit    | tweet.edit     | App\Http\Controllers\TweetController@edit           | web             |
+--------+-----------+-----------------------+----------------+-----------------------------------------------------+-----------------+
```

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

### Laravel コンテナへログインする

```bash
$ docker-compose exec laravel.test bash
```

### MySQL コンテナへログインする

```bash
$ docker-compose exec mysql bash
```

### コンテナからログアウトする

```bash
$ exit
```
