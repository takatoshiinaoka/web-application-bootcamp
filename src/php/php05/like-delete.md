# Like機能の実装2（データの追加2）

## ここまでのLike機能の問題点

### 現状

- 連打すれば無限にLikeできてしまう．．．
- Likeしている状況であれば，Likeを取り消す処理にしたい！

### 実装の方針

- Likeボタンをクリックしたら，まずtodoとuserでテーブルを検索する．
- データが1件以上存在すればDELETEのSQLを実行する．
- データが存在しなければINSERTのSQL（前項で実装したもの）を実行する．


## Like状態の調査

前項で作成した`like_create.php`のINSERT処理の前にデータの件数を確認したい．

件数の確認には`COUNT()`関数を使用する．

```php
// like_create.php

// 省略

$sql = 'SELECT COUNT(*) FROM like_table WHERE user_id=:user_id AND todo_id=:todo_id';
$stmt = $pdo->prepare($sql);
$stmt->bindValue(':user_id', $user_id, PDO::PARAM_STR);
$stmt->bindValue(':todo_id', $todo_id, PDO::PARAM_STR);
$status = $stmt->execute();

if ($status == false) {
  $error = $stmt->errorInfo();
  echo json_encode(["error_msg" => "{$error[2]}"]);
  exit();
} else {
  $like_count = $stmt->fetchColumn();
  // まずはデータ確認
  var_dump($like_count);
  exit();
}

```

## Like数で条件分岐（削除or作成）

`$like_count`で条件に該当するデータの件数が取得できるのでまずは確認する．

データの件数が確認できたら，件数が0かそれ以外で条件分岐する．0件でなければDELETE文が作成され，0件の場合はINSERT文が作成される．

```php
// like_create.php

if ($like_count != 0) {
  // いいねされている状態
  $sql = 'DELETE FROM like_table WHERE user_id=:user_id AND todo_id=:todo_id';
} else {
  // いいねされていない状態
  $sql = 'INSERT INTO like_table (id, user_id, todo_id, created_at) VALUES (NULL, :user_id, :todo_id, sysdate())';
}

// 以下は前項と変更なし
$stmt = $pdo->prepare($sql);
$stmt->bindValue(':user_id', $user_id, PDO::PARAM_STR);
$stmt->bindValue(':todo_id', $todo_id, PDO::PARAM_STR);
$status = $stmt->execute();

if ($status == false) {
  $error = $stmt->errorInfo();
  echo json_encode(["error_msg" => "{$error[2]}"]);
  exit();
} else {
  header("Location:todo_read.php");
  exit();
}

```

このように記述することで，1ユーザ1Likeの状態を実現することができる．


## 練習

todoリストのLike機能を編集しよう．

- `like_create.php`でLike数カウント -> 条件分岐してDELETE or INSERT．

以下2条件で動作確認！

- 「Likeされていない状態」でLikeボタンをクリックして，Likeテーブルにデータが作成されていればOK．
- 「Likeされている状態」でLikeボタンをクリックして，Likeテーブルのデータが削除されていればOK．

phpmyadminでデータを確認し，10件程度データが入っている状態にしておこう．
