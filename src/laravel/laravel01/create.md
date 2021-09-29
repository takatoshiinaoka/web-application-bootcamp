# tweet 作成処理の実装

必要な画面を揃えたので，作成ページから実際に DB にデータを保存できるようにする．

## Model の編集

Model の役割は「DB とのデータをやり取り」である．Model には DB からどのようにデータを取得するか，などの処理を記述する．

ただし，Model には予め使用頻度の高い処理（基本的な CRUD）が用意されているため，今回記述するコードはそれほど多くない．

まず，テーブル対して，Laravel 側からデータの作成や編集が行えないカラムを設定する．

これはクライアントから送信されてくるデータが ID やユーザ権限などの情報を書き換えてしまうリスクを低減させるためである．

`app/Models/Tweet.php`を以下のように編集する．

```php
// app/Http/Models/Tweet.php

<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Tweet extends Model
{
  use HasFactory;

  // アプリケーション側でcreateなどできない値を記述する
  // ↓以下の処理を記述

  protected $guarded = [
    'id',
    'created_at',
    'updated_at',
  ];
}


```

> 【解説】
>
> - `$guarded`はアプリケーション側から変更できないカラムを指定する（ブラックリスト）．
> - 対して，`$fillable`はアプリケーション側から変更できるカラムを指定する（ホワイトリスト）．
> - どちらを使用しても良いが，どちらかを使用する必要がある．

## Controller の編集

続いて，コントローラに「Model に対しての命令」を記述する．DB とやり取りする関数はすでに定義されているのでモデル側に新たな記述は必要ない．

`app/Http/Controllers/TweetController.php`の`store()`を内容を以下のように編集する．

```php
// app/Http/Controllers/TweetController.php

public function store(Request $request)
{
  // バリデーション
  $validator = Validator::make($request->all(), [
    'tweet' => 'required | max:191',
    'description' => 'required',
  ]);
  // バリデーション:エラー
  if ($validator->fails()) {
    return redirect()
      ->route('tweet.create')
      ->withInput()
      ->withErrors($validator);
  }
  // create()は最初から用意されている関数
  // 戻り値は挿入されたレコードの情報
  $result = Tweet::create($request->all());
  // ルーティング「todo.index」にリクエスト送信（一覧ページに移動）
  return redirect()->route('tweet.index');
}

```

> 【解説】
>
> - `create.blade.php`から送信されていたデータは`$request`に入っている．`$request->all()`とすることで全部取得することができる．
> - `redirect()->route('tweet.index')`は指定したコントローラの関数を動かす．今回は`TweetController`の`index()`関数．
