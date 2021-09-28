# Like機能の実装4（データの結合）

## 画面表示のためにデータをまとめる．

- 現状，画面の表示は`todo_table`からデータなので，集計結果を組み込めない．．．
- テーブルを結合させることで，集計結果もまとめて表示できる！

## SQLの考え方

- 「`todo_table`」と「集計結果のテーブル」をつなげる．
- 「`todo_table`の`id`」と「集計結果のテーブルの`todo_id`」を対応させる．
- 「集計結果のテーブル」は前項で取得したアレ


## テーブルの結合（JOIN）

JOINを使うとテーブルの結合ができる！

### 例

JOINを使う場合の書式は以下のとおり．この場合は以下の条件で結合する．

- 別々のテーブルで管理している「ユーザ」と「ユーザが所属するカテゴリ」を1つの画面に表示したい．
- 「`user_table`」「`category_table`」を結合する．
- 結合する際には，「`user_table`の`category_id`カラム」と「`category_table`の`category_id`カラム」を対応させる．

### user_table

|user_id|user_name|category_id|
|-:|-|-:|
|1|ディオ・ブランドー|1|
|2|カーズ|2|
|3|DIO|3|
|4|花京院典明|3|
|5|ジャイロ・ツェペリ|7|

### category_table

|category_id|category_name|
|-:|-|
|1|ファントムブラッド|
|2|戦闘潮流|
|3|スターダストクルセイダース|
|4|ダイヤモンドは砕けない|
|5|黄金の風|
|6|ストーンオーシャン|
|7|スティール・ボール・ラン|

### SQL文

```sql
SELECT
  *
FROM
  user_table
  LEFT OUTER JOIN
    category_table
  ON  user_table.category_id = category_table.category_id
```

### JOINしたテーブル

このテーブルがあれば，画面に必要な情報を全て表示することができる．SQL文の「ON」の後にどのカラムとどのカラムを対応させるのかと記述する．

|user_id|user_name|category_id|category_name|
|-:|-|-:|-|
|1|ディオ・ブランドー|1|ファントムブラッド|
|2|カーズ|2|戦闘潮流|
|3|DIO|3|スターダストクルセイダース|
|4|花京院典明|3|スターダストクルセイダース|
|5|ジャイロ・ツェペリ|7|スティール・ボール・ラン|


## 今回のtodoリストで結合したいテーブルの状況

「`todo_table`」と「集計結果のテーブル」を結合する．

### `todo_table`

|id|todo|deadline|created_at|updated_at|
|-:|-|-|-|-|
|1|SQL練習|2021-06-01|2021-06-01 11:58:44|2021-06-01 11:58:44|
|2|PHP課題|2021-06-04|2021-06-01 11:59:25|2021-06-01 11:59:25|
|4|ビールを買う|2021-06-01|2021-06-01 12:00:59|2021-06-04 17:18:19|
|5|ウイスキーを買う|2021-06-10|2021-06-01 12:01:11|2021-06-04 17:17:56|
|6|ワインを買う|2021-06-04|2021-06-01 12:06:14|2021-06-04 17:18:34|
|7|良い食材を買う|2021-06-03|2021-06-01 12:06:39|2021-06-04 17:18:25|
|8|肉を焼く|2021-06-09|2021-06-01 12:06:59|2021-06-01 12:06:59|
|10|質問投稿する|2021-06-01|2021-06-01 12:07:45|2021-06-01 12:07:45|

### 集計結果のテーブル

|todo_id|like_count|
|-:|-:|
|1|1|
|2|1|
|4|2|
|5|2|

### SQL文

- 今回は「`todo_table`の`id`」と「集計結果の`todo_id`」が対応させる．
- 「集計結果のテーブル」は前項で`GROUP BY`して出力したテーブル．このテーブルには`result-table`という名前をつける．

```sql
SELECT
  *
FROM
  todo_table
  LEFT OUTER JOIN
    (
      SELECT
        todo_id,
        COUNT(id) AS like_count
      FROM
        like_table
      GROUP BY
        todo_id
    ) AS result_table
  ON  todo_table.id = result_table.todo_id
  ```

### 出力結果

|id|todo|deadline|created_at|updated_at|todo_id|like_count|
|-:|-|-|-|-|-:|-:|
|1|SQL練習|2021-06-01|2021-06-01 11:58:44|2021-06-01 11:58:44|1|1|
|2|PHP課題|2021-06-04|2021-06-01 11:59:25|2021-06-01 11:59:25|2|1|
|4|ビールを買う|2021-06-01|2021-06-01 12:00:59|2021-06-04 17:18:19|4|2|
|5|ウイスキーを買う|2021-06-10|2021-06-01 12:01:11|2021-06-04 17:17:56|5|2|
|6|ワインを買う|2021-06-04|2021-06-01 12:06:14|2021-06-04 17:18:34|NULL|NULL|
|7|良い食材を買う|2021-06-03|2021-06-01 12:06:39|2021-06-04 17:18:25|NULL|NULL|
|8|肉を焼く|2021-06-09|2021-06-01 12:06:59|2021-06-01 12:06:59|NULL|NULL|
|10|質問投稿する|2021-06-01|2021-06-01 12:07:45|2021-06-01 12:07:45|NULL|NULL|

### 【参考】INNER JOIN

`LEFT OUTER JOIN`の代わりに`INNER JOIN`を使用すると下記のテーブルが得られる．

（結合元のどちらかのテーブルでデータが欠損している場合は結合結果に表示されない）

|id|todo|deadline|created_at|updated_at|todo_id|like_count|
|-:|-|-|-|-|-:|-:|
|1|SQL練習|2021-06-01|2021-06-01 11:58:44|2021-06-01 11:58:44|1|1|
|2|PHP課題|2021-06-04|2021-06-01 11:59:25|2021-06-01 11:59:25|2|1|
|4|ビールを買う|2021-06-01|2021-06-01 12:00:59|2021-06-04 17:18:19|4|2|
|5|ウイスキーを買う|2021-06-10|2021-06-01 12:01:11|2021-06-04 17:17:56|5|2|

### 画面に結合したデータを表示

一覧画面で上記のデータを参照し，画面に集計結果を表示する．

```php
// todo_read.php

// 省略

$sql = 'SELECT * FROM todo_table LEFT OUTER JOIN (SELECT todo_id, COUNT(id) AS like_count FROM like_table GROUP BY todo_id) AS result_table ON todo_table.id = result_table.todo_id';

// タグ生成部分

foreach ($result as $record) {
  $output .= "
    <tr>
      <td>{$record["deadline"]}</td>
      <td>{$record["todo"]}</td>
      // ↓ここに`$record["like_count"]`を追加！
      <td><a href='like_create.php?user_id={$user_id}&todo_id={$record["id"]}'>like{$record["like_count"]}</a></td>
      <td><a href='todo_edit.php?id={$record["id"]}'>edit</a></td>
      <td><a href='todo_delete.php?id={$record["id"]}'>delete</a></td>
    </tr>
  ";
}

```


## 練習

下記の処理を実装しよう！

1. `GROUP BY`を使ってlike_tableのデータを集計しよう（phpmyadminで確認）．
2. `JOIN`を使用して`todo_table`と集計結果のテーブルを結合しよう（phpmyadminで確認）．
3. 2で結合したテーブルを用いて，todo一覧画面に集計結果を表示しよう．
