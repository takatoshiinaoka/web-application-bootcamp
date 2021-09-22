# Laravel事前準備

Laravelで開発をするためには，開発環境を用意する必要がある．一人で開発するときだけでなく，複数人で開発する際には「同じ開発環境」を揃えることが大切である．

更に，フレームワークやライブラリを多用する場合，個々のPC環境によって細かいバージョンの差異や動作具合に影響が生じることがある．

主なLaravelの開発環境には以下のようなものがある．

- ローカル環境（XAMPPなど）
- ローカル上の仮想マシン（vagrantなど）
- 仮想コンテナ（Dockerなど）
- クラウド上の仮想マシン（AWS Cloud9など）

今回はLaravel公式が準備する機能を用い，仮想マシン上で開発を行う．

## 多用するコマンドまとめ

※ここでは実行しません！！ 開発中に忘れたら見よう！

### 仮想コンテナを立ち上げる

```bash
$ ./vendor/bin/sail up -d
```

### 仮想コンテナを終了させる

```bash
$ ./vendor/bin/sail down
```

### Laravelコンテナへログインする

```bash
$ docker-compose exec laravel.test bash
```

### MySQLコンテナへログインする

```bash
$ docker-compose exec mysql bash
```

### コンテナからログアウトする

```bash
$ exit
```

