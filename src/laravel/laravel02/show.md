# tweet詳細画面の実装

詳細画面はtweetの1件を個別に表示する．

## 一覧画面に詳細画面へのリンクを作成

各tweetの詳細を取得する処理と表示する画面を作成する．

一覧画面のtweet名をクリックすると詳細画面に移動するようにする．

クリックするとコントローラの`show()`関数にリクエストを送るように記述している．

`resources/views/tweet/index.php`を以下のように編集する．

```php
// resources/views/tweet/index.php

<x-app-layout>
  <x-slot name="header">
    <h2 class="font-semibold text-xl text-gray-800 leading-tight">
      {{ __('Tweet Index') }}
    </h2>
  </x-slot>

  <div class="py-12">
    <div class="max-w-7xl mx-auto sm:w-10/12 md:w-8/10 lg:w-8/12">
      <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
        <div class="p-6 bg-white border-b border-gray-200">
          <table class="text-center w-full border-collapse">
            <thead>
              <tr>
                <th class="py-4 px-6 bg-grey-lightest font-bold uppercase text-lg text-grey-dark border-b border-grey-light">tweet</th>
              </tr>
            </thead>
            <tbody>
              @foreach ($tweets as $tweet)
              <tr class="hover:bg-grey-lighter">
                <td class="py-4 px-6 border-b border-grey-light">
                  <a href="{{ route('tweet.show',$tweet->id) }}">
                    <h3 class="text-left font-bold text-lg text-grey-dark">{{$tweet->tweet}}</h3>
                  </a>
                  <div class="flex">
                    <!-- 更新ボタン -->
                    <!-- 削除ボタン -->
                  </div>
                </td>
              </tr>
              @endforeach
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</x-app-layout>

```

## 指定した1件のデータを取得する処理を追加

`show()`関数では，IDを指定して1件のデータを取得したい．

`app/Http/Controllers/TweetController.php`の`show()`を内容を以下のように編集する．

```php
// app/Http/Controllers/TweetController.php

public function show($id)
{
  $tweet = Tweet::find($id);
  return view('tweet.show', ['tweet' => $tweet]);
}

```

ここでは，受け取ったIDの値でテーブルからデータを取り出し，`tweet`という名前で`show.blade.php`に渡している．


## 詳細表示画面の作成

詳細画面の`resources/views/tweet/show.blade.php`を以下のように編集する．

```php
// resources/views/tweet/show.php`

<x-app-layout>
  <x-slot name="header">
    <h2 class="font-semibold text-xl text-gray-800 leading-tight">
      {{ __('Show Tweet Detail') }}
    </h2>
  </x-slot>

  <div class="py-12">
    <div class="max-w-7xl mx-auto sm:w-8/12 md:w-1/2 lg:w-5/12">
      <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
        <div class="p-6 bg-white border-b border-gray-200">
          <div class="mb-6">
            <div class="flex flex-col mb-4">
              <p class="mb-2 uppercase font-bold text-lg text-grey-darkest">Tweet</p>
              <p class="py-2 px-3 text-grey-darkest" id="tweet">
                {{$tweet->tweet}}
              </p>
            </div>
            <div class="flex flex-col mb-4">
              <p class="mb-2 uppercase font-bold text-lg text-grey-darkest">Description</p>
              <p class="py-2 px-3 text-grey-darkest" id="description">
                {{$tweet->description}}
              </p>
            </div>
            <a href="{{ route('tweet.index') }}" class="block text-center w-full py-3 mt-6 font-medium tracking-widest text-white uppercase bg-black shadow-lg focus:outline-none hover:bg-gray-900 hover:shadow-none">
              Back
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</x-app-layout>

```

一覧画面で各tweetをクリックし，下記のように詳細画面が表示されればOK．

![詳細画面](./img/laratter_show.png)

