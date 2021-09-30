# ルーティングとコントローラ

Laravel では，クライアントからリクエストされた URL に対して，どの関数を実行するかを決める．この対応を決定するのが「ルーティング」であり，実際に実行される関数を記述するのが「コントローラ」である．

## ルーティングとコントローラの作成

> 📦 **Laravel コンテナ内の操作**
>
> ```bash
> $ docker-compose exec laravel.test bash
> root@8544d96d2334:/var/www/html#
> ```

コントローラとルーティングを作成する．今回のコントローラ名は`TweetController`とする．

`--resource`をつけることで，よく使用する処理（代表的な CRUD 処理）を一括して作成することができる．

下記コマンドを実行する．

```bash
$ php artisan make:controller TweetController --resource

# 実行結果
Controller created successfully.

```

これでコントローラファイルが作成された．続いてルーティングを作成する．

**ルーティングファイルは`routes`以下に配置される．**

`routes/web.php`を以下のように編集する．

```php
// routes/web.php

<?php

use Illuminate\Support\Facades\Route;
// ↓追加
use App\Http\Controllers\TweetController;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

// ↓追加
Route::resource('tweet', TweetController::class);

Route::get('/', function () {
    return view('welcome');
});

Route::get('/dashboard', function () {
    return view('dashboard');
})->middleware(['auth'])->name('dashboard');

require __DIR__.'/auth.php';

```

ルーティングの一覧は下記で確認できる．

```bash
$ php artisan route:list

# 実行結果（ユーザ操作などのルーティングは省略）
+--------+-----------+-----------------------+----------------+-----------------------------------------------------+-----------------+
| Domain | Method    | URI                   | Name           | Action                                              | Middleware      |
+--------+-----------+-----------------------+----------------+-----------------------------------------------------+-----------------+
|        | GET|HEAD  | tweet                 | tweet.index    | App\Http\Controllers\TweetController@index          | web             |
|        | POST      | tweet                 | tweet.store    | App\Http\Controllers\TweetController@store          | web             |
|        | GET|HEAD  | tweet/create          | tweet.create   | App\Http\Controllers\TweetController@create         | web             |
|        | GET|HEAD  | tweet/{tweet}         | tweet.show     | App\Http\Controllers\TweetController@show           | web             |
|        | PUT|PATCH | tweet/{tweet}         | tweet.update   | App\Http\Controllers\TweetController@update         | web             |
|        | DELETE    | tweet/{tweet}         | tweet.destroy  | App\Http\Controllers\TweetController@destroy        | web             |
|        | GET|HEAD  | tweet/{tweet}/edit    | tweet.edit     | App\Http\Controllers\TweetController@edit           | web             |
+--------+-----------+-----------------------+----------------+-----------------------------------------------------+-----------------+

```

> 【解説】
>
> **この表は非常に重要．**
>
> - 例えば，`/tweet/`に`GET`でリクエストが来た場合，`TweetController`の`index`関数が動作することを示している．
> - このように，URL と動作する関数の対応を決めているのがルーティングである．

これで，ルーティング部分は完了である．

## コントローラの実装（一部）

URL にリクエストが来た場合に実行される関数はコントローラに記述される．まずはリクエストが来た場合に特定の画面を表示するよう処理を記述する．

**コントローラは`app/Http/Controllers`以下に配置される．**

`app/Http/Controllers/TweetController.php`の内容を以下のように編集する．

`view()`関数は指定したビューファイル（画面）を表示する．`tweet.index`は`tweet`フォルダの`index.blade.php`の意味．ビューファイルは次項で作成する．

```php
// app/Http/Controllers/TweetController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
// ↓2行追加
use Validator;
use App\Models\Tweet;

class TweetController extends Controller
{
  /**
   * Display a listing of the resource.
   *
   * @return \Illuminate\Http\Response
   */
  public function index()
  {
    // ↓追加
    return view('tweet.index');
  }

  /**
   * Show the form for creating a new resource.
   *
   * @return \Illuminate\Http\Response
   */
  public function create()
  {
    // ↓追加
    return view('tweet.create');
  }

  // 以降は変更なし

}

```
