# Laravelプロジェクト作成

Mac の人は「ターミナル」，Windowsの人は「Windows ターミナル」を起動する．

（Windows の人は Ubuntu 20.04 で動かしている状態でコマンドを実行する必要があるので，適宜前項の「Docker の動作確認」を確認しておく．）

下記コマンドでプロジェクトのフォルダができる．`laratter`部分がプロジェクト名となる．

**コマンドはデスクトップなどわかりやすい場所で実行する．作成された`laratter`フォルダに必要なファイルが全て含まれている．**

```bash
$ curl -s https://laravel.build/laratter | bash
```


実行結果
```

 _                               _
| |                             | |
| |     __ _ _ __ __ ___   _____| |
| |    / _` | '__/ _` \ \ / / _ \ |
| |___| (_| | | | (_| |\ V /  __/ |
|______\__,_|_|  \__,_| \_/ \___|_|

Warning: TTY mode requires /dev/tty to be read/writable.
    Creating a "laravel/laravel" project at "./laratter"
    Installing laravel/laravel (v8.6.2)
      - Downloading laravel/laravel (v8.6.2)
      - Installing laravel/laravel (v8.6.2): Extracting archive
    Created project in /opt/laratter
    > @php -r "file_exists('.env') || copy('.env.example', '.env');"
    Loading composer repositories with package information
    Updating dependencies
...

```

途中，Laravel側でいろいろ実行するためPCのログインパスワードを求められるので入力する．

```bash
Please provide your password so we can make some final adjustments to your application's permissions.

[sudo] password for taroosg:

Thank you! We hope you build something incredible. Dive in with: cd laratter && ./vendor/bin/sail up

```


## 権限の変更

この方法でプロジェクトを作成するとエディタで操作する場合に権限で引っかかる場合があるので権限を変更する．

```bash
$ sudo chmod -R 777 laratter
```

権限が変更できない場合は下記URLの設定を行い再度コマンドを実行する．

[参考URL](https://gori.me/mac/mac-tips/112082)

## 起動

早速仮想コンテナを立ち上げる．

```bash
$ cd laratter
$ ./vendor/bin/sail up -d
```

実行結果

```bash
Creating network "laratter_sail" with driver "bridge"
Creating volume "laratter_sailmysql" with local driver
Creating volume "laratter_sailredis" with local driver
Creating volume "laratter_sailmeilisearch" with local driver
Creating laratter_mailhog_1     ... done
Creating laratter_redis_1       ... done
Creating laratter_mysql_1       ... done
Creating laratter_selenium_1    ... done
Creating laratter_meilisearch_1 ... done
Creating laratter_laravel.test_1 ... done
```


## 動作確認

ブラウザで`localhost`にアクセスし，下記画面が表示されればOK．

![トップ画面](./img/laravel-firstview.png)

**！！！この画面が表示されたらOK！次項の「終了するとき」を実行して事前準備は完了！！！**

コマンド実行時にエラーが出る場合や，画面が表示されない場合は以下の「エラーが出る場合」を確認しよう．


## 終了するとき

仮想マシン（コンテナ）を終了させる場合は以下のコマンドを実行する．

コンテナが立ち上がっていると XAMPP が立ち上がらなくなるため，開発が終わったら下記コマンドで終了させること．

```bash
$ ./vendor/bin/sail down
```


## エラーが出る場合

### XAMPP が動いている

XAMPP が動いていると立ち上がらない場合があるため，一度 XAMPP を終了させてから再度コマンドを実行する．

また，Laravel の仮想コンテナが立ち上がっていると XAMPP が動かない場合があるので，XAMPP で開発を行う場合には仮想コンテナを終了させておく．

### PC再起動

PC を再起動すると解決する場合が多い．

### Docker Desktopのバージョンアップ

Docker Desktop を以前からインストールしていた場合，バージョンが古いと起動しない場合はあるので最新バージョンにアップデートを行う．

### M1のMacで`ERROR: no matching manifest for linux/arm64/v8 in the manifest list entries`が出る

`docker-compoes.yml`に下記を追加する，

```yml
platform: 'linux/x86_64'
```

追記場所はこのへん．

```yml
mysql:
    image: 'mysql:8.0'
    platform: 'linux/x86_64'
```

[参考：https://neoighodaro.com/posts/3-running-laravel-and-docker-on-the-apple-mac-m1](https://neoighodaro.com/posts/3-running-laravel-and-docker-on-the-apple-mac-m1)

### M1のMacで`laravel.test failed`的なエラーが出る

エディタで`laratter/vendor/laravel/sail/runtimes/8.0/Dockerfile`を開き．34行目と35行目をコメントアウトする．

↓この2行をコメントアウトする（バージョンによって異なるので，該当ファイルの記述内容をよく確認すること）

```
&& curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
&& echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list \
```

## いけてそうだけどブラウザで表示できない

アクセスするときのURLを`https://localhost`から`http://localhost`に変更


