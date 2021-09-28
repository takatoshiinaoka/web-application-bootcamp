# 【Windows編】環境構築

Docker を使用するための準備を進めていく．

必要なもの

- Docker Desktop
- Windows ターミナル
- Ubuntu 20.04
- WSL2
- PowerShell（Windows にもともと入っている）

## Docker Desktop のダウンロードとインストール

下記のURLにアクセスし，必要なファイルをダウンロードする．

[https://hub.docker.com/editions/community/docker-ce-desktop-windows/](https://hub.docker.com/editions/community/docker-ce-desktop-windows/)

1. `Docker Desktop Installer.exe` をダブルクリックし，インストーラを起動する．

2. 確認画面が出たら， `Enable Hyper-V Windows Features` （Hyper V の Windows 機能を有効にする）のオプションが，設定ページで選択されているかどうかを確認する．

3. インストール・ウィザードの指示に従い，利用規約（ライセンス）を承諾し，インストーラに権限を与えてインストールを進める．

4. インストールに成功したら， `Close` （閉じる）をクリックしてインストールを終了する．

## Windows ターミナルと Ubuntu 20.04 のインストール

以下の URL からそれぞれをインストールする．
  - [Windows ターミナル（https://www.microsoft.com/ja-jp/p/windows-terminal/9n0dx20hk701）](https://www.microsoft.com/ja-jp/p/windows-terminal/9n0dx20hk701)
  - [Ubuntu 20.04（https://www.microsoft.com/ja-jp/p/ubuntu-2004-lts/9n6svws3rx71）](https://www.microsoft.com/ja-jp/p/ubuntu-2004-lts/9n6svws3rx71)

## WSL2 のダウンロード，インストールと設定

1. 下記の URL の「Step 4」と「Step 5」を実行する．
    - [https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package](https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)

2. PC を再起動する．（↑で再起動している場合は必要なし）

## Docker の動作確認

1. Docker Desktop を起動する．

2. 「`Setting`（画面上部の⚙アイコン）」 -> 「`General`」 -> 「`Use the WSL2 based engine`」にチェックを入れる．

![docker 設定画面01](./img/docker_setting01.PNG)

3. 「`Setting`（画面上部の⚙アイコン）」 -> 「`Resources`」 -> 「`WSL INTEGRATION`」の `Enable Integration ...` にチェックを入れ，「`Ubuntu 20.04`」のトグルをオンにする．トグルが出てこない場合は PC を再起動する．

![docker 設定画面02](./img/docker_setting02.PNG)

4. Windows ターミナルを Ubuntu 20.04 で動かす．

↓Windows ターミナルを開いて上部の「∨」をクリックするとメニューが出るので，ここで `Ubuntu 20.04` を選ぶ．出てこない場合は PC を再起動する．

![Ubuntu 設定箇所](./img/terminal_image01.png)

↓このようになっていればコマンドが実行できる．**次項以降もこの状態でコマンドを実行していく．**

![Windows ターミナル動作画面](./img/terminal_image02.png)


5. 下記のコマンドを実行し， `docker` のバージョンが表示されれば準備完了（バージョンなどの数値は資料と異なっていて問題ない）．

```bash
$ docker -v
Docker version 20.10.8, build 3967b7d

```

バージョンが表示されたら，「Laravel プロジェクト作成」項に進もう！
