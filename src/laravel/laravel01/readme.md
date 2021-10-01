# Day06

## コンテナの起動と vs code の準備

laratter ディレクトリに移動して下記コマンドでコンテナを起動！

```bash
$ ./vendor/bin/sail up -d
```

↓ 起動したときのターミナル

![コンテナ起動時のターミナル](./img/laratter_start_terminal.png)

↓ ブラウザは `localhost` にアクセス！

![localhost画面](./img/laratter_start_browser.png)

↓ vs code で laratter フォルダを開く（エディタ画面左のディレクトリ構成を確認）！

![vs code 画面](./img/laratter_start_vscode.png)

## 本日の内容

- MVC フレームワークについての解説．
- 開発環境についての解説．
- SNS アプリケーションの実装（データ作成，一覧表示まで）．

## 本日の目標

- MVC フレームワークである Laravel の動き方や考え方を学ぶ．
- SNS アプリケーションの実装を通して，開発の手順や流れに慣れる．
- PHP での実装と Laravel での実装について，共通点や相違点を把握する．

## ルーティング表

後ほど解説します！

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
