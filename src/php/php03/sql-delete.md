# PHPとDBの連携5（データ削除処理）

## データ削除のSQL

```sql
-- DELETE文の基本構造
DELETE FROM テーブル名;

-- 例

-- 全消去
DELETE FROM todo_table;
-- 指定データのみ
DELETE FROM todo_table WHERE id = 2;

-- WHEREで指定しないとテーブルのデータが全滅する！！
-- DELETEすると復旧できないので注意！！

```


## 処理の流れ

1. ✅ 一覧画面に削除ページへのリンクを作成（urlにidを追加`todo_delete.php?id=**`）
2. 削除処理の作成（`todo_delete.php`）
3. テーブルのデータを削除したら一覧画面に戻る


## 削除の実装

削除に必要なのは`id`のみである．編集画面に移動する場合と同様にGETで`id`を受け取る．

データを受け取ったら，下記の順で処理を記述する．
  - DB接続
  - SQL実行（DELETE文）
  - 一覧画面へ移動

### 💡 Key Point

>⚠️ DELETE文を実行する場合には必ずWHEREで`id`を指定すること．

```php
$id = $_GET['id'];

include('functions.php');
$pdo = connect_to_db();

$sql = 'DELETE FROM todo_table WHERE id=:id';

$stmt = $pdo->prepare($sql);
$stmt->bindValue(':id', $id, PDO::PARAM_STR);
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

以下の処理を実装しよう！

1. ✅ 一覧画面に`todo_delete.php`へのリンクを追加！
2. `todo_delete.php`ではデータをIDで検索し，該当するデータを削除！
3. 完了したら`todo_read.php`へ戻る．

削除処理実行後，一覧ページでデータが削除されていればOK！

（phpmyadminでも確認しよう）
