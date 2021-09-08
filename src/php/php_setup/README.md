# PHP事前準備


## サーバとクライアント

サーバで動作する言語（サーバサイド）

- webサーバ上でプログラムが実行される．
- PHP, Ruby, Python, JAVA, (node.js), etc…

クライアント（webブラウザ）で動作する言語（クライアントサイド）

- webブラウザでプログラムが実行される．
- html, css, JavaScript

=>サーバサイド言語の開発にはサーバ上で動作させるアプリケーション（xamppなど）が必要！

（自分のPC上でサーバを動かすイメージ）

## サーバのしくみとXAMPPの役割

### サーバの役割及び構成

Webアプリケーションはクライアントとサーバの組み合わせで構成されている．

サーバには「アプリケーションサーバ」と「DBサーバ」が存在し，相互に連携することでアプリケーションを構築している．

（詳しい仕組みの理解は後からでOK．）

![サーバのしくみ](./img/php_file_about_server.svg)

### サーバサイド言語での開発とXAMPPの役割

上記のとおり，PHP（及び他のサーバサイド言語）を動作させるには「サーバ」が必要となる．

しかし，自身のPCで開発を行う場合に都度サーバを用意することは効率的ではない．

このような場合に自身のPC内で擬似的なサーバを用意してPHPを動作させることができるのが「xampp」である．

xamppは以下の役割を提供する．

- PHPを動作させるアプリケーションサーバ（`Apatch web server`）を用意する．
- データを保存するためのデータベース（`MySQL Database`）を用意する．
- その他いろいろ（今回の開発では使用しない）．

![xampp概要](./img/php_file_about_xampp.svg)

## XAMPP環境構築

### ダウンロード

下記URLにアクセスする．

[https://www.apachefriends.org/jp/download.html](https://www.apachefriends.org/jp/download.html)

>**！！注意！！**
>
>資料作成時時点では`7.3.6 / PHP 7.3.6`となっているが，現時点（2021/09/12）では`8.0.9 / PHP 8.0.9`が最新なのでこちらを用いること！！
>
>上記バージョンより新しいものが表示される場合は最新版をインストールしてください！

↓アクセス画面
![xamppダウンロード画面](./img/php_setup_xampp_download_01.svg)

Macの人は下記からダウンロード．

![xamppダウンロード画面](./img/php_setup_xampp_download_02.svg)

Windowsの人は下記からダウンロード．

![xamppダウンロード画面](./img/php_setup_xampp_download_03.svg)

### インストール

ダウンロードしたらインストールを進める．

![xamppダウンロード画面](./img/php_setup_xampp_download_04.svg)

### 動作確認（サーバ起動）

インストールが済んだらアプリケーションを立ち上げる．

Macの人は以下のようにサーバを起動させる．

![xamppダウンロード画面](./img/php_setup_xampp_download_05.svg)

Windowsの人は以下の画面．

![xamppダウンロード画面](./img/php_setup_xampp_download_06.svg)

### 動作確認（画面表示）

#### アプリケーションサーバ動作確認

ブラウザで[https://localhost/](https://localhost/)にアクセスして下記画面が表示されればOK．

![アプリケーションサーバ動作確認](./img/php_setup_xampp_confirm_01.svg)

#### DBサーバ動作確認

ブラウザで[https://localhost/phpmyadmin](https://localhost/phpmyadmin)にアクセスして下記画面が表示されればOK．

![DBサーバ動作確認](./img/php_setup_xampp_confirm_02.svg)


## まとめ

下記3点が実施できていることを確認しよう！

- XAMPPの起動確認
- [http://localhost/](http://localhost/)のアクセス確認
- [http://localhost/phpmyadmin](http://localhost/phpmyadmin)のアクセス確認
