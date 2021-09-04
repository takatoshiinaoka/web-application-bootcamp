# PHPとDBの連携（データ作成処理）

## 必要なファイル

- `todo_input.php`：データ入力画面．
- `todo_create.php`：DB接続，データ作成処理を実行．
## 処理の流れ

1. `todo_input.php`で入力されたデータを`todo_create.php`へ送信（post）．
2. `todo_create.php`でデータを受け取り，DBへの新規データ作成．
3. 作成完了後，`todo_input.php`（入力画面）へ移動．（`todo_create.php`では画面表示なし）

## 送信側の処理

フォームからデータを送信する．以下3点の漏れやスペルミスがないよう確認しよう．

- action
- method
- name

```php
// todo_input.php

<form action="todo_create.php" method="POST">
  <fieldset>
    <div>
      todo: <input type="text" name="todo">
    </div>
    <div>
      deadline: <input type="date" name="deadline">
    </div>
    <div>
      <button>submit</button>
    </div>
  </fieldset>
</form>

```

## 受信側の処理

データ受け取り側では以下の処理を実装する．

- 必須項目の入力チェック
- データの受け取り
- DB接続
- SQL作成&実行
- SQL実行後の処理

### 入力チェック

DBにデータを格納する場合，基本的にデータの欠損は許されない（データなしを許可する設定もある）．

そのため，以下の条件に合致する場合は以降の処理を中止してエラー画面を表示する．

- 必須項目（todoとdeadline）のデータが送信されていない．
- 必須項目（todoとdeadline）が空で送信されている．

```php
// todo_create.php

if (
  !isset($_POST['todo']) || $_POST['todo']=='' ||
  !isset($_POST['deadline']) || $_POST['deadline']==''
) {
  exit('ParamError');
}

```

### 【参考】エラーメッセージを出力する意味

どこで失敗したのかをわかるようにする！

- PHPではエラーを見つけづらい．．．
- データを扱うので，異常なデータなどが作成されるとまずい．
- どこでエラーが出ているのかわからないと詰む．
- エラーにも種類がある！
    - どこでうまくいっていないのかを把握できるようにエラーの処理を記述！

### データ受け取り

前回講義のtxtファイルの場合と同様．POSTで送信しているため`$_POST`で受け取る．

```php
// todo_create.php

$todo = $_POST['todo'];
$deadline = $_POST['deadline'];

```

### DB接続

DBに接続するコードは決まった形式（`PDO`）．

接続の際には以下の情報が必要になる．今回は`dbname`のみ設定が必要．

- `dbname`：DBの名前（今回はここを`dec_todo`に設定する！）
- `port`：接続ポート
- `host`：DBのホスト名
- `username`：DB接続時のユーザ名
- `password`：DB接続時のパスワード

```php
// todo_create.php

// 各種項目設定
$dbn ='mysql:dbname=YOUR_DB_NAME;charset=utf8;port=3306;host=localhost';
$user = 'root';
$pwd = '';

// DB接続
try {
  $pdo = new PDO($dbn, $user, $pwd);
} catch (PDOException $e) {
  echo json_encode(["db error" => "{$e->getMessage()}"]);
  exit();
}

// 「dbError:...」が表示されたらdb接続でエラーが発生していることがわかる．

```

### SQL作成&実行

SQL（今回はINSERT文）を実行する場合も手順が決まっている．

1. SQL文の記述．
2. バインド変数の設定．
3. SQL実行．

```php
// todo_create.php

// SQL作成&実行
$sql = 'INSERT INTO todo_table (id, todo, deadline, created_at, updated_at) VALUES (NULL, :todo, :deadline, now(), now())';

$stmt = $pdo->prepare($sql);

// バインド変数を設定
$stmt->bindValue(':todo', $todo, PDO::PARAM_STR);
$stmt->bindValue(':deadline', $deadline, PDO::PARAM_STR);

// SQL実行（実行に失敗すると$statusにfalseが返ってくる）
$status = $stmt->execute();

```

### 【補足】バインド変数とSQLインジェクション

SQLインジェクションと呼ばれるハッキング手法が存在する．

例えば，下記のようにコードを記述されている場合．．．

```php
$query = "SELECT * FROM user WHERE id = '$user_id'";
```
`$user_id`に「`' or 'A' = 'A`」を入れると，下記と同じ意味になってしまう！

```sql
SELECT * FROM user;
```

-> 不正にユーザのデータを取得できてしまう！！！

バインド変数を用いることで，SQL文として実行されないようにできる！

ユーザが入力した値をSQL文内で使用する場合には必ずバインド変数を使用すること．

### SQL実行時の処理

実行に失敗すると`$status`に`false`が返ってくるため，条件分岐して失敗を検出する．

`$stmt->errorInfo()`と記述することでエラー内容（配列形式）を取得することができ，2番めのデータにエラーメッセージが格納されている．

SQLが正常に実行された場合は，データ入力画面に移動することとする．

```php
// todo_create.php

if ($status == false) {
  $error = $stmt->errorInfo();
  exit('sqlError:'.$error[2]);
} else {
  header('Location:todo_input.php');
}

```

## 練習

DBにデータを追加する処理を実装しよう！
1. `todo_input.php`にフォームを作成
2. `todo_create.php`で
    - データを受け取る
    - DBに接続
    - SQL文を書いて実行

phpmyadminでテーブルを確認し，データが作成されていればOK！

- phpmyadminの「表示」タブをクリックすると最新のデータを読み込める．
