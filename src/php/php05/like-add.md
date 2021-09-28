# Like機能の実装1（データの追加1）

前回までに作成したtodoリストにLike機能（Many to Manyの関係）を追加する！


## アプリケーションの動作

- 各ユーザは複数のtodoにLikeし，各todoは複数のユーザからLikeされる．
- 一覧画面のLikeボタンをクリックしたらLikeすることができ，Like数が一覧画面に表示される．
- Likeは1ユーザ1Likeに制限する（自演禁止）．


## 必要な作業・処理

- ユーザテーブルといいねテーブルを作成．
- Likeボタン追加．
- Likeボタンクリック時にLikeデータを作成．
- Like数を一覧画面に表示する．

## Likeテーブルを作成

新しくテーブルを作成する．テーブル名は「`like_table`」とする．

このテーブルでは「どのユーザが」「どのtodoに」Likeしたのかを記録する役割を持つ．

前項の「中間テーブル」にあたる．

### カラム構造

|カラム名|データ型|長さ|その他設定項目|
|---|---|---|---|
|`id`|INT|12|インデックスを「PRIMARY」に設定．</br>「A_I」にチェック．|
|`user_id`|INT|12||
|`todo_id`|INT|12||
|`created_at`|DATETIME|-||

## todoリストのLike機能実装

### やりたいこと

いいねボタンをクリックしたら．．．

- like_tableに「誰が」「何に」いいねをしたのかを追加

実装の方針

- todo_read.phpにいいねボタンを設置
- GETでtodoのidとユーザのidを送信する
- 受け取り側のファイル（like_create.php）で受け取ったデータをdbに登録

## todo一覧画面にLikeボタンの設置

一覧画面のタグ生成部分にLikeボタンを追記する．

user_idはログイン時にセッション変数に保存している値を使用する（`todo_login_act.php`に追記）．

```php
// todo_login_act.php

if (!$val) {
  echo "<p>ログイン情報に誤りがあります</p>";
  echo "<a href=todo_login.php>ログイン</a>";
  exit();
} else {
  $_SESSION = array();
  // ↓ここを追記
  $_SESSION['user_id'] = $val['id'];
  $_SESSION['session_id'] = session_id();
  $_SESSION['is_admin'] = $val['is_admin'];
  $_SESSION['username'] = $val['username'];
  header("Location:todo_read.php");
  exit();
}

```

`user_id`と`todo_id`を`like_create.php`にGETで送信する．

```php
// todo_read.php

$user_id = $_SESSION['id'];

// ↓タグ生成部分
foreach ($result as $record) {
  $output .= "
    <tr>
      <td>{$record["deadline"]}</td>
      <td>{$record["todo"]}</td>
      // ↓ここ1行追記
      <td><a href='like_create.php?user_id={$user_id}&todo_id={$record["id"]}'>like</a></td>
      <td><a href='todo_edit.php?id={$record["id"]}'>edit</a></td>
      <td><a href='todo_delete.php?id={$record["id"]}'>delete</a></td>
    </tr>
  ";
}

```

## Likeデータの追加

LikeテーブルにGETで送信されてきた内容を追加する．

処理の流れ自体はシンプルなINSERT処理．

```php
// like_create.php

include('functions.php');

$user_id = $_GET['user_id'];
$todo_id = $_GET['todo_id'];

$pdo = connect_to_db();

$sql = 'INSERT INTO like_table (id, user_id, todo_id, created_at) VALUES (NULL, :user_id, :todo_id, now())';

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

## 練習

todoリストにLike機能を追加しよう．

- `todo_read.php`にLikeボタンを追加．
- `like_create.php`でテーブルにデータ作成．

Likeボタンをクリックして，Likeテーブルにデータが作成されていればOK．

（phpmyadminでデータを確認しよう）
