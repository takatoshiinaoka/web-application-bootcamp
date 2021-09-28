# Favorite機能1（多対多のデータ・中間テーブル）


```txt
users
    id - integer
    name - string

tweets
    id - integer
    tweet - string

tweet_user
    user_id - integer
    tweet_id - integer

```

中間テーブルの作成

```bash
$ php artisan make:migration create_tweet_user_table
```

```php
public function up()
{
  Schema::create('tweet_user', function (Blueprint $table) {
    $table->id();
    $table->unsignedBigInteger('user_id');
    $table->unsignedBigInteger('tweet_id');
    $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
    $table->foreign('tweet_id')->references('id')->on('tweets')->onDelete('cascade');
    $table->unique(['user_id', 'tweet_id']);
    $table->timestamps();
  });
}

```

```bash
# php artisan migrate
Migrating: 2021_09_24_072924_create_tweet_user_table
Migrated:  2021_09_24_072924_create_tweet_user_table (451.37ms)
```

テーブルを確認する

```bash
$ docker-compose exec mysql bash
root@2989c482f70d:/# mysql -u sail -p
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


ユーザモデル

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

  /**
   * The attributes that are mass assignable.
   *
   * @var string[]
   */
  protected $fillable = [
    'name',
    'email',
    'password',
  ];

  /**
   * The attributes that should be hidden for serialization.
   *
   * @var array
   */
  protected $hidden = [
    'password',
    'remember_token',
  ];

  /**
   * The attributes that should be cast.
   *
   * @var array
   */
  protected $casts = [
    'email_verified_at' => 'datetime',
  ];

  public function mytweets()
  {
    return $this->hasMany(Tweet::class)->orderBy('updated_at', 'desc');
  }

  // ↓追加
  public function tweets()
  {
    return $this->belongsToMany(Tweet::class)->withTimestamps();
  }
}

```

tweetもでる
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Tweet extends Model
{
  use HasFactory;

  // アプリケーション側でcreateなどできない値を記述する
  // ↓以下の処理を記述

  protected $guarded = [
    'id',
    'created_at',
    'updated_at',
  ];

  public static function getAllOrderByDeadline()
  {
    return self::orderBy('updated_at', 'desc')->get();
  }

  public function user()
  {
    return $this->belongsTo(User::class);
  }

  // ↓追加
  public function users()
  {
    return $this->belongsToMany(User::class)->withTimestamps();
  }
}


```

