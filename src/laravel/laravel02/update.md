# tweet 更新処理の実装

更新処理は

1. 一覧画面に編集ボタンを設置．
2. 編集ボタンをクリックしたら編集画面に移動.
3. 編集画面でデータを書き換え，送信ボタンクリックで DB のデータを更新する．

## 一覧画面に編集ボタンを追加

`resources/views/tweet/index.blade.php`を以下のように編集する．

```php
// resources/views/tweet/index.blade.php

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
                    <form action="{{ route('tweet.edit',$tweet->id) }}" method="GET" class="text-left">
                      @csrf
                      <button type="submit" class="mr-2 ml-2 text-sm hover:bg-gray-200 hover:shadow-none text-white py-1 px-2 focus:outline-none focus:shadow-outline">
                        <svg class="h-6 w-6 text-gray-500" fill="none" viewBox="0 0 24 24" stroke="black">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                        </svg>
                      </button>
                    </form>
                    <!-- 削除ボタン -->
                    <form action="{{ route('tweet.destroy',$tweet->id) }}" method="POST" class="text-left">
                      @method('delete')
                      @csrf
                      <button type="submit" class="mr-2 ml-2 text-sm hover:bg-gray-200 hover:shadow-none text-white py-1 px-2 focus:outline-none focus:shadow-outline">
                        <svg class="h-6 w-6 text-gray-500" fill="none" viewBox="0 0 24 24" stroke="black">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                        </svg>
                      </button>
                    </form>
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

一覧画面に編集ボタンが表示されていれば OK．

![編集ボタン追加](./img/laratter_index_added_edit_button.png)

## 更新画面に移動する処理の作成

`app/Http/Controllers/TweetController.php`の`edit()`を内容を以下のように編集する．

```php
// app/Http/Controller/TweetController.php

public function edit($id)
{
  $tweet = Tweet::find($id);
  return view('tweet.edit', ['tweet' => $tweet]);
}

```

やっていることは`create()`関数と同様．

## 更新画面の作成

`resources/views/tweet/edit.blade.php`を以下のように編集する．

```php
// resources/views/tweet/edit.blade.php

<x-app-layout>
  <x-slot name="header">
    <h2 class="font-semibold text-xl text-gray-800 leading-tight">
      {{ __('Edit Tweet') }}
    </h2>
  </x-slot>

  <div class="py-12">
    <div class="max-w-7xl mx-auto sm:w-8/12 md:w-1/2 lg:w-5/12">
      <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
        <div class="p-6 bg-white border-b border-gray-200">
          @include('common.errors')
          <form class="mb-6" action="{{ route('tweet.update',$tweet->id) }}" method="POST">
            @method('put')
            @csrf
            <div class="flex flex-col mb-4">
              <label class="mb-2 uppercase font-bold text-lg text-grey-darkest" for="tweet">tweet</label>
              <input class="border py-2 px-3 text-grey-darkest" type="text" name="tweet" id="tweet" value="{{$tweet->tweet}}">
            </div>
            <div class="flex flex-col mb-4">
              <label class="mb-2 uppercase font-bold text-lg text-grey-darkest" for="description">Description</label>
              <input class="border py-2 px-3 text-grey-darkest" type="text" name="description" id="description" value="{{$tweet->description}}">
            </div>
            <div class="flex justify-evenly">
              <a href="{{ route('tweet.index') }}" class="block text-center w-5/12 py-3 mt-6 font-medium tracking-widest text-black uppercase bg-gray-100 shadow-sm focus:outline-none hover:bg-gray-200 hover:shadow-none">
                Back
              </a>
              <button type="submit" class="w-5/12 py-3 mt-6 font-medium tracking-widest text-white uppercase bg-black shadow-lg focus:outline-none hover:bg-gray-900 hover:shadow-none">
                Update
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</x-app-layout>

```

一覧画面の更新ボタンをクリックし，現在のデータが表示されていれば OK．

![編集画面](./img/laratter_edit.png)

## 指定したデータを更新する処理の作成

編集画面でデータを書き換え，コントローラの`update()`関数にデータを送信し，DB のデータを更新する．

`app/Http/Controllers/TweetController.php`の`update()`を内容を以下のように編集する．

```php
// app/Http/Controllers/TweetController.php

public function update(Request $request, $id)
{
  //バリデーション
  $validator = Validator::make($request->all(), [
    'tweet' => 'required | max:191',
    'description' => 'required',
  ]);
  //バリデーション:エラー
  if ($validator->fails()) {
    return redirect()
      ->route('tweet.edit', $id)
      ->withInput()
      ->withErrors($validator);
  }
  //データ更新処理
  // updateは更新する情報がなくても更新が走る（updated_atが更新される）
  $result = Tweet::find($id)->update($request->all());
  // fill()save()は更新する情報がない場合は更新が走らない（updated_atが更新されない）
  // $redult = Tweet::find($id)->fill($request->all())->save();
  return redirect()->route('tweet.index');
}

```

一覧画面のデータが，編集画面で書き換えたデータに更新されれば OK．

![データ更新結果](./img/laratter_index_updated.png)
