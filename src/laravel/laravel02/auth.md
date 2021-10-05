# tweet とユーザ認証の連携

はじめのほうで，ユーザ認証を行う breeze ライブラリをインストールしたため，これを利用してユーザ管理を行う．

まず，認証ユーザのみがアプリケーションの機能を利用できるように処理を変更する．現状ではログインしていない状態でも一覧画面などにアクセスできてしまう（実際はユーザ情報が不足しているためエラー画面となる）．

## ログインしていないユーザはアプリケーションにアクセスできないようにする．

`app/Http/Controllers/TweetController.php`を以下のように編集する．

```php
// app/Http/Controllers/TweetController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use Validator;
use App\Models\Tweet;
// ↓追加
use Auth;

class TodoController extends Controller
{
  // ↓関数を作成
  public function __construct()
  {
    $this->middleware(['auth']);
  }

  // ...省略

}

```

## 動作確認（ログイン必須）

一旦ログアウトした状態で，URL 直打ち（`localhost/tweet`）して tweet 画面にアクセスできずにログイン画面に戻されれば OK！

> 【解説】
>
> - ユーザの認証情報を使用するため，`use Auth;`を記述している．
> - `__construct()`関数は，その他の関数が実行される場合にその前に実行される．
> - `middleware(['auth'])`はログイン状況を確認して，ログインしていない状態ならログインページに戻す処理を実行する．

## tweet テーブルにユーザ ID カラムを追加する

> 📦 **Laravel コンテナ内の操作**
>
> ```bash
> $ docker-compose exec laravel.test bash
> root@8544d96d2334:/var/www/html#
> ```

### マイグレーションファイルの作成

カラムの変更はマイグレーションで行う．

> - 本来はカラムはテーブル作成時点で決定しておくことが望ましい．
> - マイグレーションは実行した内容を取り消せる（ロールバック）ため，不具合が生じても対応できる場合が多い．

下記コマンドを実行する．

```bash
$ php artisan make:migration add_user_id_to_tweets_table --table=tweets

# 実行結果
Created Migration: 2021_09_24_061716_add_user_id_to_tweets_table

```

### マイグレーションファイルの編集

`database/migration/2021_09_24_061716_add_user_id_to_tweets_table.php`を開く．
下記にように編集する．

> 他のテーブルと relation させるためには，カラム名を「`モデル名小文字_id`」とする必要がある．

```php
// database/migrations/2021_09_24_061716_add_user_id_to_tweets_table.php

public function up()
{
  Schema::table('tweets', function (Blueprint $table) {
    // ↓ 1行追加
    $table->integer('user_id')->after('id');
  });
}

```

### マイグレーション実行

以下のコマンドを実行する．

```bash
$ php artisan migrate

# 実行結果
Migrating: 2021_09_24_061716_add_user_id_to_tweets_table
Migrated:  2021_09_24_061716_add_user_id_to_tweets_table (102.98ms)

```

## テーブルの確認

phpmyadmin でテーブルの構造を確認する．`user_id` カラムが追加されていれば OK．

```txt
+-------------+-----------------+------+-----+---------+----------------+
| Field       | Type            | Null | Key | Default | Extra          |
+-------------+-----------------+------+-----+---------+----------------+
| id          | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| user_id     | int             | NO   |     | NULL    |                |
| tweet       | varchar(191)    | NO   |     | NULL    |                |
| description | text            | YES  |     | NULL    |                |
| created_at  | timestamp       | YES  |     | NULL    |                |
| updated_at  | timestamp       | YES  |     | NULL    |                |
+-------------+-----------------+------+-----+---------+----------------+
```

<!-- 【今回は不要】コマンドで確認する場合は以下の手順で行う．

> 📦 **MySQL コンテナ内の操作**
>
> ```bash
> $ docker-compose exec mysql bash
> root@d984f6614597:/#
> ```

mysql にログインしてテーブルを確認する．パスワードは`password`．

```bash
$ mysql -u sail -p

mysql> use laratter
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
| tweets                 |
| users                  |
+------------------------+
6 rows in set (0.01 sec)

mysql> desc tweets;
+-------------+-----------------+------+-----+---------+----------------+
| Field       | Type            | Null | Key | Default | Extra          |
+-------------+-----------------+------+-----+---------+----------------+
| id          | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| user_id     | int             | NO   |     | NULL    |                |
| tweet       | varchar(191)    | NO   |     | NULL    |                |
| description | text            | YES  |     | NULL    |                |
| created_at  | timestamp       | YES  |     | NULL    |                |
| updated_at  | timestamp       | YES  |     | NULL    |                |
+-------------+-----------------+------+-----+---------+----------------+
6 rows in set (0.00 sec)

mysql>

``` -->

## データ追加時に user_id を追加

tweet のデータを作成する際に，「誰が作成したのか」がわかるように，データにログインユーザの ID を追加する．

`app/Http/Controllers/TweetController.php`の`store()`を内容を以下のように編集する．

```php
// app/Http/Controllers/TweetController.php

public function store(Request $request)
{
  // バリデーション
  $validator = Validator::make($request->all(), [
    'tweet' => 'required | max:191',
    'description' => 'required',
  ]);
  // バリデーション:エラー
  if ($validator->fails()) {
    return redirect()
      ->route('tweet.create')
      ->withInput()
      ->withErrors($validator);
  }

  // ↓編集 フォームから送信されてきたデータとユーザIDをマージし，DBにinsertする
  $data = $request->merge(['user_id' => Auth::user()->id])->all();
  $result = Tweet::create($data);

  // tweet.index」にリクエスト送信（一覧ページに移動）
  return redirect()->route('tweet.index');
}

```

> 【解説】
>
> - `$request->merge()`でユーザ ID を追加している．
> - `Auth::user()->id`で現在ログインしているユーザの ID を取得することができる（`Auth::id()`でも可）．
> - `Auth::user()`には他にもデータが入っているので，`dd()`などで確認してみると良いだろう．

## 動作確認

適当なデータを追加し，phpmyadmin で確認する．

tweets テーブルのデータにユーザ ID が一緒に入っていれば OK．既存データの user_id カラムに 0 が入っているので，ログインユーザの ID に変更しておこう．
