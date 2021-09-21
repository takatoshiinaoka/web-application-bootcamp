# PHPとDBの連携4（データ更新処理の作成）

## データ更新のSQl

```sql
-- UPDATE文の基本構造
UPDATE テーブル名 SET 変更データ WHERE 選択データ;

-- 例
UPDATE todo_table SET todo='PHP課題' WHERE id = 1;
-- 【重要】必ずWHEREを使用！！（忘れると全てのデータが更新されます．．！）

```

## 更新の処理

前項の編集画面からデータを受け取り，DBのデータを更新する．

処理の流れは`todo_create.php`とよく似ている．

### データチェックと受け取り

まずは`todo`，`deadline`，`id`のデータが揃っていることを確認し，データを受け取る．

```php
// todo_update.php

if (
  !isset($_POST['todo']) || $_POST['todo'] == '' ||
  !isset($_POST['deadline']) || $_POST['deadline'] == '' ||
  !isset($_POST['id']) || $_POST['id'] == ''
) {
  exit('paramError');
}

$todo = $_POST['todo'];
$deadline = $_POST['deadline'];
$id = $_POST['id'];

```

### DB接続とSQL実行

続いてDB接続し，UPDATEのSQLを実行する．SQLが正常に実行された場合は一覧画面に移動する．

### 💡 Key Point

>必ずWHEREでidを指定すること！！！

```php
// todo_update.php

include('functions.php');
$pdo = connect_to_db();

$sql = 'UPDATE todo_table SET todo=:todo, deadline=:deadline, updated_at=now() WHERE id=:id';

$stmt = $pdo->prepare($sql);
$stmt->bindValue(':todo', $todo, PDO::PARAM_STR);
$stmt->bindValue(':deadline', $deadline, PDO::PARAM_STR);
$stmt->bindValue(':id', $id, PDO::PARAM_STR);
$status = $stmt->execute();

if ($status == false) {
  $error = $stmt->errorInfo();
  echo json_encode(["error_msg" => "{$error[2]}"]);
  exit();
} else {
  header('Location:todo_read.php');
  exit();
}

```

## 練習

`todo_update.php`でUPDATEの処理を実装しよう！

更新処理実行後，一覧ページでデータが更新されていればOK！

（phpmyadminでも確認しよう）

