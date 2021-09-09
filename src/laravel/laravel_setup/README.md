# Laravel事前準備

1. Docker Desktopをインストールする．
2. Misrosoft Store から `ubuntu20.04` と `Windows Terminal` をインストール
3. 下記の URL から WSL2 をダウンロードしてインストール
    - [https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package)
4. Powershell で以下のコマンドを実行し，WSL2 をデフォルトにする
    - `wsl --set-default-version 2`
5. PC を再起動する．
6. Docker Desktopを起動し，「Setting」 -> 「Resources」 -> 「WSL INTEGRATION」の`Enable Integration ...`にチェックを入れ，「Ubuntu 20.04」のトグルをオンにする．
7. Windows Terminal を Ubuntu20.04 で動かしてプロジェクト作成コマンドを実行する．

