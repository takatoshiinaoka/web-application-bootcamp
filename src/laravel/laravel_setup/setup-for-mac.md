# 【Mac 編】環境構築

Docker を使用するための準備を進めていく．

必要なもの

- Docker Desktop
- ターミナル（Mac に入っているもので OK）

## Docker Desktop のダウンロードとインストール

下記の URL にアクセスし，必要なファイルをダウンロードする．

Intel CPU と M1 CPU で異なるので，ダウンロード時は注意すること．

[https://hub.docker.com/editions/community/docker-ce-desktop-mac/](https://hub.docker.com/editions/community/docker-ce-desktop-mac/)

1. ダウンロードした `Docker.dmg` をダブルクリックしてインストーラを起動したら，アプリケーション・フォルダに Docker アイコンをドラッグする．

2. アプリケーション・フォルダ内にある `Docker.app` をダブルクリックし， `Docker` を起動する．

3. トップステータスバーにある `Docker` メニュー（クジラのマーク）で， `Docker Desktop` が実行中で，ターミナルからアクセスできる状態になる．

## Docker の動作確認

ターミナルを開いて下記のコマンドを実行し， `docker` のバージョンが表示されれば準備完了（バージョンなどの数値は資料と異なっていて問題ない）．

```bash
$ docker -v
Docker version 20.10.8, build 3967b7d

```

バージョンが表示されたら，「Laravel プロジェクト作成」項に進もう！
