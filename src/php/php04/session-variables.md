# セッション変数

セッション領域に保存される変数を使用することで，複数のファイルで横断的にデータを扱うことができる．


## セッション変数の形式

セッション変数は`$_SESSION`で記述する．

連想配列形式（JavaScriptのオブジェクトに相当）で扱われる．

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
        $_SESSION['array'] = ['JavaScript', 'PHP', 'Swift', 'Rust'];</td>
    </tr>
    <tr>
      <td>fuga.php</td>
    </tr>
    <tr>
      <td>piyo.php</td>
    </tr>
  </tbody>
</table>


## セッション変数の定義

セッション変数を扱う際にも`session_start();`は必須！

セッション変数は`$_SESSION['キー名']`の形式で宣言するが，使い方は通常の変数と同様である．

```php
// session01.php

<?php
session_start();

// セッション変数に値を代入
$_SESSION['keyword'] = 'PHP';

echo $_SESSION['keyword'];
exit();

?>

```


## セッション変数の使用

セッション変数はセッション領域に保存されているので，ファイル内で定義していなくても呼び出すことができる．

使い方は通常の変数と同様．

```php
<?php
// session02.php

session_start();

// `$_SESSION['keyword']`はセッション変数なので定義していなくても呼び出せる
$string = $_SESSION['keyword'] . '&MySQL';
echo $_SESSION['keyword'];
exit();

?>

```


## 練習

下記の処理を記述して，セッション変数の動き方を確認しよう．

- `session01.php`でsession変数を定義しよう！
- `session02.php`で定義した変数を呼び出して出力しよう！
