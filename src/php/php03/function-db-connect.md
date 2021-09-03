# DB接続関数の作成

## DB接続は常に同じコード

実は．．．

- `todo_create.php`と`todo_read.php`で記述したDB接続のコードは全く同じ！
- 今回作成する`todo_edit.php`, `todo_update.php`, `todo_delete.php`も同じ記述！

であれば．．！

一つの関数にまとめられる！

=> 関数用のファイルを作成しよう！（`functions.php`）


## 関数の定義

DB接続の処理は様々なファイルで実行されるため，関数専用のファイル（`functions.php`）に記述して他のファイルから読み込むと効率が良い．

関数を実行すると，DB接続情報を出力するように実装すると良い（`return new PDO...`部分）．

```php
// functions.php

function connect_to_db()
{
  $dbn='mysql:dbname=YOUR_DB_NAME;charset=utf8;port=3306;host=localhost';
  $user = 'root';
  $pwd = '';
  try {
    return new PDO($dbn, $user, $pwd);
  } catch (PDOException $e) {
    exit('dbError:'.$e->getMessage());
  }
}

```

## 関数の実行

上で定義した関数はDB接続が必要なファイルで`functions.php`を読み込むことで実行可能．

別ファイルの読み込みは`include()`関数を用いる．

```php
// DB接続したいファイル（todo_create.php, todo_read.php, など）

include('functions.php');
$pdo = connect_to_db();

// 他のDB接続が必要なファイルでも上記の2行でOK！

```

## 練習

1. `functions.php`にDB接続の関数を定義しよう！
2. `todo_create.php`と`todo_read.php`で`functions.php`を`include()`し，関数を実行しよう！

今まで通りの動きが確認できればOK！

（これまでやっていた処理を関数にしただけなので，実行される内容はこれまでと変化なし）

