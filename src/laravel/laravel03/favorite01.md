# Favorite 機能 1（多対多のデータ・中間テーブル）

ここからは SNS においてメジャーな機能である「Favorite 機能」を実装していく．

Favorite は多対多のデータ構造となるため，新たに中間テーブルを作成する必要がある．

## 多対多のデータ構造

Laravel では中間テーブルの命名を「関連するテーブル A の単数形\_関連するテーブル B の単数形（テーブル名はアルファベット順）」とする規則がある．

したがって，今回の中間テーブル名は `tweet_user` となる．

↓ テーブルの構造（主要なもののみ記載）

```txt
users
    id - integer
    name - string

tweets
    id - integer
    tweet - string
    description - string

tweet_user
    user_id - integer
    tweet_id - integer

```

> 📦 **Laravel コンテナ内の操作**
>
> ```bash
> $ docker-compose exec laravel.test bash
> root@8544d96d2334:/var/www/html#
> ```

## マイグレーションファイルの作成

テーブルの作成はマイグレーションで行う．まずはマイグレーションファイルを作成する．

```bash
$ php artisan make:migration create_tweet_user_table
```

マイグレーションファイル（`database/migrations/2021_09_24_072924_create_tweet_user_table.php`）を開き，`up()` 関数を以下のように編集する．

```php
// database/migrations/2021_09_24_072924_create_tweet_user_table.php

public function up()
{
  Schema::create('tweet_user', function (Blueprint $table) {
    $table->id();
    // ↓ ここから追加
    $table->unsignedBigInteger('user_id');
    $table->unsignedBigInteger('tweet_id');
    $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
    $table->foreign('tweet_id')->references('id')->on('tweets')->onDelete('cascade');
    $table->unique(['user_id', 'tweet_id']);
    // ↑ ここまで追加
    $table->timestamps();
  });
}

```

## 【補足】マイグレーションファイルの設定項目について

| 関数           | 意味                                                                         |
| -------------- | ---------------------------------------------------------------------------- |
| `foreign()`    | 引数のカラムが外部キー制約であることを示す．                                 |
| `references()` | ↑ で参照する外部キーを設定する．                                             |
| `on()`         | ↑ で参照するテーブルを設定する．                                             |
| `onDelete()`   | ↑ で参照しているデータが削除された場合に中間テーブルのデータも削除する設定． |

上記をまとめると「中間テーブルの `user_id` は `users` テーブルの `id` を参照していて，`users` テーブルのデータが削除されると中間テーブルのデータも削除される」となる．

## マイグレーションの実行

マイグレーションファイルを作成したらマイグレーションを実行する．

```bash
$ php artisan migrate

# 実行結果
Migrating: 2021_09_24_072924_create_tweet_user_table
Migrated:  2021_09_24_072924_create_tweet_user_table (451.37ms)

```

## テーブルの確認

phpmyadmin で，`tweet_user` テーブルが作成されていることを確認する．

```txt
+------------+-----------------+------+-----+---------+----------------+
| Field      | Type            | Null | Key | Default | Extra          |
+------------+-----------------+------+-----+---------+----------------+
| id         | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| user_id    | bigint unsigned | NO   | MUL | NULL    |                |
| tweet_id   | bigint unsigned | NO   | MUL | NULL    |                |
| created_at | timestamp       | YES  |     | NULL    |                |
| updated_at | timestamp       | YES  |     | NULL    |                |
+------------+-----------------+------+-----+---------+----------------+
```

【今回は不要】以下にコマンドで確認する場合も示す．

> 📦 **MySQL コンテナ内の操作**
>
> ```bash
> $ docker-compose exec mysql bash
> root@d984f6614597:/#
> ```

```bash
$  mysql -u sail -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 347
Server version: 8.0.25 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use laratter;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+------------------------+
| Tables_in_laratter     |
+------------------------+
| failed_jobs            |
| migrations             |
| password_resets        |
| personal_access_tokens |
| tweet_user             |
| tweets                 |
| users                  |
+------------------------+
7 rows in set (0.00 sec)

mysql> desc tweet_user;
+------------+-----------------+------+-----+---------+----------------+
| Field      | Type            | Null | Key | Default | Extra          |
+------------+-----------------+------+-----+---------+----------------+
| id         | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| user_id    | bigint unsigned | NO   | MUL | NULL    |                |
| tweet_id   | bigint unsigned | NO   | MUL | NULL    |                |
| created_at | timestamp       | YES  |     | NULL    |                |
| updated_at | timestamp       | YES  |     | NULL    |                |
+------------+-----------------+------+-----+---------+----------------+
5 rows in set (0.01 sec)

mysql>
```

## モデルに多対多の連携を設定

User モデルに `belongsToMany` を設定して，User モデルは Tweet モデルと多対多の連携をすることを示す．

```php
// app/Http/Models/User.php

<?php

namespace App\Models;

use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
  use HasApiTokens, HasFactory, Notifiable;

  // 省略

  // ↓追加
  public function tweets()
  {
    return $this->belongsToMany(Tweet::class)->withTimestamps();
  }
}

```

同様に Tweet モデルにも `belongsToMany` を設定する．

```php
// app/Http/Models/Tweet.php

<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Tweet extends Model
{
  use HasFactory;

  // 省略

  // ↓追加
  public function users()
  {
    return $this->belongsToMany(User::class)->withTimestamps();
  }
}

```

ここまでで，データを連携させる設定は完了となる．
