# PHPとDBの連携（データ参照処理）

## 必要なファイル

- `todo_read.php`：DBからデータを取得してデータの一覧画面を作成する．
## 処理の流れ

1. 表示ファイル（`todo_read.php`）へアクセス時，DB接続する．
2. データ参照用SQL作成→実行．
3. 取得したデータをHTMLに埋め込んで画面を表示．

※必要に応じて，並び替えやフィルタリングを実施してみよう．

## DBデータ作成処理の実装

### DB接続

新規データ作成の場合と同様の処理．

```php
// todo_read.php

$dbn ='mysql:dbname=YOUR_DB_NAME;charset=utf8;port=3306;host=localhost';
$user = 'root';
$pwd = '';

try {
  $pdo = new PDO($dbn, $user, $pwd);
} catch (PDOException $e) {
  echo json_encode(["db error" => "{$e->getMessage()}"]);
  exit();
}

```

### SQL作成&実行

データ作成処理と同様にSQLを記述して実行する．今回は「ユーザが入力したデータ」を使用しないのでバインド変数は不要．

また，`$status`には実行結果が入るが，この時点ではまだデータ自体の取得はできていない点に注意．

```php
// todo_read.php

$sql = 'SELECT * FROM todo_table';
$stmt = $pdo->prepare($sql);
$status = $stmt->execute();

```

### SQL実行後の処理

SQLの実行に失敗した場合はエラーを表示して処理を中止する．

SQLが正常に実行された場合は以下の流れで処理が実行される．
- `fetchAll()`関数でデータ自体を取得する．
- 繰り返し処理を用いて，取得したデータからHTMLタグを生成する．
- （HTML内の任意の位置に作成したタグを設置

```php
// todo_read.php

if ($status == false) {
  $error = $stmt->errorInfo();
  exit('sqlError:'.$error[2]);
} else {
  $result = $stmt->fetchAll(PDO::FETCH_ASSOC);
  $output = "";
  foreach ($result as $record) {
    $output .= "
      <tr>
        <td>{$record["deadline"]}</td>
        <td>{$record["todo"]}</td>
      </tr>
    ";
  }
}

```

### HTMLにタグを埋め込み

```php
// todo_read.php

// html部分にデータを追加
<tbody>
  <!-- ↓に<tr><td>deadline</td><td>todo</td><tr>の形でデータが入る -->
  <?= $output ?>
</tbody>

```

### 【参考】JSを用いたタグ生成

上記では「PHP内でタグを生成 → HTML内に埋め込み」という実装を行っているが，タグ生成部分をJSで行うこともできる．

どちらが正解というものではないので，自身のイメージしやすいパターンで実装すれば問題ない．

```php
<?php

// DB接続，SQL実行など

if ($status == false) {
  $error = $stmt->errorInfo();
  exit('sqlError:'.$error[2]);
} else {
  // PHPではデータを取得するところまで実施
  $result = $stmt->fetchAll(PDO::FETCH_ASSOC);
}

?>

<!DOCTYPE html>
  <head>
    // 省略
  </head>
  <body>
    <table>
      <tbody id="output"></tbody>
    <table>

    <script>
      // PHPのデータをJSに渡す
      const resultArray = <?=json_encode($result) ?>;
      // 配列からタグ生成し，#outputに表示する
    </script>
  </body>
</html>


```

## 練習

DBのデータを読み出して表示する処理を実装しよう！

`todo_read.php`で
- DBに接続
- SQL文を書いて実行
- 取得したデータをHTMLに埋め込み

テーブルのデータが画面に一覧で表示されればOK！

できた人はSQL文を編集してフィルタリングやソートなどを実装してみよう．
