# セッションとは

## セッションとはなにか

- サーバに変数などを保存できる仕組み．
- サーバ自体に変数を定義する．
- サーバ上にあるどのファイルからでも値を取り出せる！

### 普通の変数を使用した例

各ファイルで用意した変数はそのファイル内でしか使用できない．

| file     | data                                             |
| -------- | ------------------------------------------------ |
| hoge.php | $number = 100;                                   |
| fuga.php | $keyword = 'ジーズ';                             |
| piyo.php | $array = ['JavaScript', 'PHP', 'Swift', 'Rust']; |

### セッション変数を活用

セッション領域自体にデータを保存することができ，サーバ上のどのファイルからでもアクセスすることができる．

セッション変数は`$_SESSION`で記述する（後述）．

<table>
  <thead>
    <th>file</th>
    <th>session</th>
  </thead>
  <tbody>
    <tr>
      <td>hoge.php</td>
      <td rowspan="3">
        $_SESSION['number'] = 100;<br>
        $_SESSION['keyword'] = 'ジーズ';<br>
        $_SESSION['array'] = ['JavaScript', 'PHP', 'Swift', 'Rust'];
      </td>
    </tr>
    <tr>
      <td>fuga.php</td>
    </tr>
    <tr>
      <td>piyo.php</td>
    </tr>
  </tbody>
</table>

## セッションの使用と「session_id」

セッションは下記の流れで使用する．

1. セッションの開始．
   - セッションがスタートすると「セッション領域」が作られる．
   - セッション領域識別用の id（`session_id`）が発行される．
   - セッションの機能が使えるようになり，情報を保存できる．
   - session_id の再生成ができる．
2. 必要に応じてセッション変数（`$_SESSION`）にデータを保存．
3. `session_id`の再生成
   - 悪意あるサイトに`session_id`を読まれてしまうとハッキングのリスク．
   - ページ移動などのタイミングで id を再生成し，最新版だけを有効にする．
4. セッションの終了．
   - 保存されている情報などを破棄する．

### session_id の確認

まずはセッションの開始を宣言する．
セッションに関連する機能を使用する場合には必ず記述する必要がある．

```php
session_start();
```

セッションを開始すると自動的に id が発行されてブラウザに id が保存される．

id はサーバとブラウザの双方に保存され，下記の方法で確認することができる．

1. PHP ファイル上で`session_id();`で取得可能．
2. ブラウザで「検証 → Application → Cookies → localhost」
   - 現在有効な session_id が保存されている．

### session_id の再生成

session_id がバレると他の人に session の中身をいじられてしまう可能性がある．．！

`session_regenerate_id();`を使用すると id を再生成して更新できる．

（保存されているデータ自体は変更なし）

使い所

- ログインしたら id 発行してログイン情報を管理．
- ページ移動など特定の操作をしたタイミングで再生成して古い id を無効化する．

```php
// session_regenerate_id.php

<?php
// セッションの開始
session_start();
$old_session_id = session_id();

// 再生成
session_regenerate_id(true);
$new_session_id = session_id();

// 新旧のidを画面に表示して更新されていることを確認
echo "<p>旧id: {$old_session_id}</p>";
echo "<p>新id: {$new_session_id}</p>";
exit();

?>

```

### 💡 Key Point

> `session_regenerate_id(true);`の`true`が大切！！
>
> `true`を設定することで古い id を無効にすることができる．

### セッションの終了

セッションの終了に際しては保存したデータの削除が必要になる．

以下の 3 つの手順でデータを削除する．

1. セッション変数の削除．
2. ブラウザに保存されたセッション id の有効期間操作．
3. セッション領域の削除．

これらの処理は後のステップで実装する．

```php
// 指定したsession変数の削除
unset($_SESSION[key]);

// session情報の全削除
$_SESSION = array();

// ブラウザに保存した情報の有効期限を操作
setcookie(session_name(), '', time() - 42000, '/');

// session領域自体をを破壊
session_destroy();

```

[参考](https://www.php.net/manual/ja/function.session-destroy.php)

## 練習

下記の処理を実装してセッションと session_id の挙動を確認しよう．

- id を発行して確認しよう！
- 再生成して旧 id と新 id を表示しよう！

検証画面で確認し，リロードの度に新 ID と一致すれば OK！
