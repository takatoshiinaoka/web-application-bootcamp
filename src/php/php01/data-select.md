# パラメータ送信による検索機能の実装

サーバへのデータ送信を用いて，ユーザの入力値によるデータ検索を実装してみよう．

## 処理の流れ

1. ユーザがデータを入力する画面を準備する．
2. 検索ボタンクリックでサーバにユーザが入力したデータを送信する．
3. サーバ側では，受け取ったキーワードを用いて予め用意されたデータの中から該当するものを抽出し，画面を構成する．

## データの確認

今回は下記のデータを使用する（すでにファイル内にデータ用意済）．

入力画面でキーワードを入力し，このデータの中からキーワードが含まれるものだけを抽出して画面に表示する．

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

まずは検索フォームを用意し，POST でサーバにデータを送信する．


```php
// data_input.php

<form action="data_select.php" method="post">
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

var_dump($_POST);
exit();

```

## キーワードを用いた検索と画面の構成

1. 受け取ったキーワードを用いて，該当するデータのみ含まれる配列を作成する．`id`，`name`，`hero`，`rival`いずれかにキーワードが含まれていれば OK とする．
2. 作成した配列から，画面を構成するためのタグを構成する．

```php
// data_select.php

$keyword = $_POST['keyword'];

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
        <?= $output ?>
      </tbody>
    </table>
  </fieldset>
</body>

// 省略

```

## 練習

`data_input.php`と`data_select.php`に上記の処理を記述して動作を確認しよう！

- キーワードが含まれるデータのみ抽出された表示されれば OK！