# パラメータ送信による検索機能の実装

サーバへのデータ送信を用いて，ユーザの入力値によるデータ検索を実装してみよう．

細かい処理はググりながら書けるので，まずは全体の流れを押さえるのが大事ッ！！

## 処理の流れ

1. ユーザがデータを入力する画面を準備する．
2. 検索ボタンクリックでサーバにユーザが入力したデータを送信する．
3. サーバ側では，受け取ったキーワードを用いて予め用意されたデータの中から該当するものを抽出し，画面を構成する．

## データの確認

今回は下記のデータを使用する（すでにファイル内にデータ用意済）．

入力画面でキーワードを入力し，下記のデータの中からキーワードが含まれるものだけを抽出して画面に表示する．

|id|name|hero|rival|
|-|-|-|-|
|1|ファントムブラッド|ジョナサン・ジョースター|ディオ・ブランドー|
|2|戦闘潮流|ジョセフ・ジョースター|カーズ|
|3|スターダストクルセイダース|空条承太郎|DIO|
|4|ダイヤモンドは砕けない|東方仗助|吉良吉影|
|5|黄金の風|ジョルノ・ジョバァーナ|ディアボロ|
|6|ストーンオーシャン|空条徐倫|エンリコ・プッチ|
|7|スティール・ボール・ラン|ジョニィ・ジョースター|ファニー・ヴァレンタイン|
|8|ジョジョリオン|東方定助|透龍|

## ユーザ入力データの送信

まずは検索フォームを用意し，GET でサーバにデータを送信する．

送信側では下記を設定！
- 宛先（`action="data_select.php"`）
- メソッド（`method="get"`）
- データ名（`name="keyword"`）

```php
// data_input.php

<form action="data_select.php" method="get">
  <fieldset>
    <legend>検索キーワード入力画面</legend>
    <div>
      keyword: <input type="text" name="keyword">
    </div>
    <div>
      <button>検索</button>
    </div>
  </fieldset>
</form>

```


## データの受け取り

データを受け取る場合には必ず`var_dump()`を用いてデータを受け取れていることを確認する．

```php
// data_select.php

var_dump($_GET);
exit();

```

## キーワードを用いた検索と画面の構成

1. 受け取ったキーワードを用いて，該当するデータのみ含まれる配列を作成する．`id`，`name`，`hero`，`rival`いずれかにキーワードが含まれていれば OK とする．
2. 作成した配列から，画面を構成するためのタグを構成する．

```php
// data_select.php

$keyword = $_GET['keyword'];

// 用意されたデータからキーワードがヒットするものを抽出する処理
$results = array_filter($data, function ($x) use ($keyword) {
  return str_contains($x['id'], $keyword)
    || str_contains($x['name'], $keyword)
    || str_contains($x['hero'], $keyword)
    || str_contains($x['rival'], $keyword);
});

$output = '';

foreach ($results as $result) {
  $output .= "<tr><td>{$result['id']}</td><td>{$result['name']}</td><td>{$result['hero']}</td><td>{$result['rival']}</td></tr>";
}

```

構成したタグを HTML 部分に埋め込み．

```php
// data_select.php

// 省略

<body>
  <fieldset>
    <legend>検索結果</legend>
    <a href="data_input.php">入力画面</a>
    <table>
      <thead>
        <tr>
          <th>id</th>
          <th>name</th>
          <th>hero</th>
          <th>rival</th>
        </tr>
      </thead>
      <tbody>
        <!-- ここに`<tr><td>'id'</td><td>'name'</td><td>'hero'</td><td>'rival'</td></tr>`の形式でデータが表示する -->
        <?= $output ?>
      </tbody>
    </table>
  </fieldset>
</body>

// 省略

```

### 💡 Key Point

>処理の流れを把握するのが大事！！
>
>1. ユーザが画面に入力したキーワードをサーバに送信する．
>2. サーバでキーワードを受け取り，もともと存在するデータからヒットするものを抽出する．
>3. ヒットしたデータを元にHTMLをつくる．
>4. HTMLのデータがブラウザに送信される．
>5. 画面にHTMLが表示される．


## 練習

`data_input.php`と`data_select.php`に上記の処理を記述して動作を確認しよう！

1. `data_input.php`のフォーム部分にデータ送信のためのコードを記述しよう．
2. `data_select.php`でデータが受け取れることを確認！
3. `data_select.php`で受け取ったデータを用いてデータを検索し，画面に表示する処理を記述！

`data_input.php`から検索キーワードを送信し，キーワードが含まれるデータのみ抽出されて表示されれば OK！
