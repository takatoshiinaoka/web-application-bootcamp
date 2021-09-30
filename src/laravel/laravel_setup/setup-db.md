# 【今回は不要】データベース準備

## MySQL の状態を確認

今回の SNS アプリケーションでは，DB として MySQL を使用する．

すでに MySQL 自体は動作する状態になっているが，自分で確認したい場合もある．下記手順を実行し，MySQL にログインする．

**コンテナへログインしている場合は一旦`exit`で通常のターミナルに戻って実行すること**

### DB 用のコンテナへログイン

```bash
$ docker-compose exec mysql bash
root@d984f6614597:/#
```

### MySQL へログイン

ユーザ名`sail`，パスワード`password`でログインできる．

パスワードを求められるので「`password`」を入力して Enter（パスワードは画面上に表示されないので注意）．

```bash
root@d984f6614597:/# mysql -u sail -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 29
Server version: 8.0.25 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

### DB 状況の確認

すでに今回のアプリケーションで使用するデータベースを作成されている．データベース名は `laratter`（プロジェクト名を同じ）．

下記コマンドを実行してデータベースの一覧を表示する．

```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| laratter           |
+--------------------+
2 rows in set (0.03 sec)
```

確認できたらログアウトする．

```bash
mysql> exit;
Bye
```

## Laravel から DB に接続するための設定

続いて，Laravel から MySQL にアクセスするための設定を行う．今回はプロジェクト作成の時点で設定されているため項目の確認のみ行う．

エディタから`.env`ファイルを開く．`.env`ファイルは`laratter`ディレクトリの直下に配置されている．

```env
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laratter
DB_USERNAME=sail
DB_PASSWORD=password
```

それぞれ下記の意味となっている．デプロイする場合などはサービス提供者側からそれぞれ情報が提供されるため，必要に応じて編集する．

| 項目          | 意味                              |
| ------------- | --------------------------------- |
| DB_CONNECTION | DB の種類                         |
| DB_HOST       | DB のホスト名                     |
| DB_PORT       | DB のポート                       |
| DB_DATABASE   | DB 名                             |
| DB_USERNAME   | DB にログインするときのユーザ名   |
| DB_PASSWORD   | DB にログインするときのパスワード |

もし`.env`ファイルを更新する場合はキャッシュをクリアする．

> 設定ファイルはサーバ起動時にキャッシュに保存されるため，変更した場合は「キャッシュをクリアする」「コンテナを立ち上げ直す」のどちらかが必要になる．
>
> キャッシュクリアのコマンドを実行する場合は Laravel の仮想コンテナにログインした状態で行うこと．

```bash
$ php artisan config:cache
```

実行結果

```bash
Configuration cache cleared!
Configuration cached successfully!
```
