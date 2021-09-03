# SQLによるDB操作

## SQLとは

DBの操作には「SQL（言語）」を使用する．

PHPでDBを操作するときは，コード内でSQL文を実行する．

フレームワークなどではコード内では実行しない場合もある．ただし，フレームワークが関数の実行結果としてSQLを生成して実行するため，DB操作は必ずSQLで行われる．


## 基本のSQL4種

まずはデータ操作の基本となる4種類を押さえましょう！

- `INSERT`：データの「作成」
- `SELECT`：データの「参照」
- `UPDATE`：データの「更新」
- `DELETE`：データの「削除」

※SQL文は大文字で記載していますが，小文字でも動作する．他の言語（本講座ではPHP）と組み合わせる際に区別しやすいよう大文字で記載している．他の言語でも大文字で扱うことが多い．


## INSERT（データの作成）

指定したテーブルに対して新規データの作成を行う．

ポイントは以下！

- 「カラム名の数と順序」と「値の数と順序」を一致させる必要がある．
- `id`カラムはDB側でAuto incrementの設定をしているため`NULL`を送信する．
- 作成日時や更新日時は`now()`を使用して実行時の日付時刻が入力されるようにする．

```sql

-- INSERT文の基本構造
INSERT INTO テーブル名 (カラム1, カラム2, ...) VALUES (値1, 値2, ...);

-- 例
INSERT INTO todo_table (id, todo, deadline, created_at, updated_at) VALUES(NULL, 'SQL練習', '2021-12-31', now(), now());

```

## SELECT（データの参照）

SELECT文はデータの参照に使用されるが，フィルタリングやソートなど多様なオプションが用意されている．

### SELECT文の基本

```sql
-- SELECT文の基本構造
SELECT 表示するカラム名 FROM テーブル名;

-- 例

-- 「*」で全て指定
SELECT * FROM todo_table;

-- 1つのカラムを指定
SELECT todo FROM todo_table;

-- 複数カラム指定
SELECT todo, deadline FROM todo_table;

```

### フィルタリング

データの条件を設定してフィルタリングをおこなうことができる．フィルタリングには`WHERE`を使用する．

```sql
-- 「WHERE」を使用して値の条件を指定できる
SELECT * FROM todo_table WHERE deadline='2021-12-31';

-- 演算子の使用
SELECT * FROM todo_table WHERE deadline <= '2021-12-31';
SELECT * FROM todo_table WHERE deadline >= '2021-12-01' AND deadline <= '2021-12-31';

-- あいまい検索
SELECT * FROM todo_table WHERE todo LIKE 'PHP%';
SELECT * FROM todo_table WHERE todo LIKE '%提出';
SELECT * FROM todo_table WHERE todo LIKE '%課題%';

```

### ソート

並び替えには`ORDER BY`を使用する．

- 基準となるカラム名を指定し，
- 昇順（`ASC`）か降順（`DESC`）を指定する．

```sql
-- ORDER BYを使用した並び替え
-- `deadline`カラムの値で降順に並び替え
SELECT * FROM todo_table ORDER BY deadline DESC;
-- `todo`カラムの値で昇順に並び替え
SELECT * FROM todo_table ORDER BY todo ASC;

```

### 取得するデータ件数の制限

`LIMIT`を用いてデータ件数の制限を行うことができる．最新10件，などの使い方がメジャー．

```sql
-- LIMITで表示件数の制限
SELECT * FROM todo_table LIMIT 5;

-- 並び替えとの組み合わせ
SELECT * FROM todo_table ORDER BY deadline DESC LIMIT 5;

```

## UPDATE（データの更新）

UPDATEは次回の講義で扱う．

```sql
-- UPDATE文の基本構造
UPDATE テーブル名 SET 変更データ WHERE 選択データ;

-- 例
UPDATE todo_table SET todo='PHP課題' WHERE id = 1;
-- 【重要】必ずWHEREを使用！！（忘れると全てのデータが更新されます．．！）

```

## DELETE（データの削除）

DELETEは次回の講義で扱う．

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


## 【参考】SQLの練習

[https://sqlzoo.net/](https://sqlzoo.net/)

初歩から応用までSQL問題が出題．当面は最初の方の基本的なデータ取得ができればOK．


## 練習

SQLはphpmyadminの「SQLタブ」から記述&実行できる．

1. INSERT文を用いて`todo_table`にデータを10件程度作成しよう．
2. SELECT文を用いて作成したデータを取り出そう．
    - WHERE, ORDER BY, LIMITを試して，想定どおりのデータを取得できるかどうか確認！
