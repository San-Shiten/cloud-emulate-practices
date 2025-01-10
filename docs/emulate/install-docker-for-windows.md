# Windows 端末(WSL2)に Dockerを導入する

## Ubuntu-20.04 インストール

### ホストマシンのコマンドライン(powershellなど)

- Ubuntu-20.04のインストール

  ```ps1
  wsl --install -d Ubuntu-20.04
  ```

  実行後以下のメッセージが出力された場合、端末を再起動する。

  ```ps1
  要求された操作は正常に終了しました。変更を有効にするには、システムを再起動する必要があります。
  ```

- Ubuntu-20.04のバージョン確認

  ```ps1
  wsl -l -v
  ```

- Ubuntu-20.04のバージョンが1だった場合、2に変更する。

  ```ps1
  wsl --set-version Ubuntu-20.04 2
  ```

- 以降WSLにインストールしたUbuntu-20.04で作業したいときは以下のコマンドを実行する(複数のディストリビューションがある場合は名称を指定すること)

  ```ps1
  wsl
  ```

- Ubuntu-20.04内で変更を行ったときは再起動する

  ```ps1
  wsl --shutdown
  ```

### WSL

Ubuntuの初回起動時UserとPasswordを登録する。

#### Docker Engine のインストール

- 以下のコマンドを実行してインストールする(Docker Desktopのインストールを進められるが無視して実行開始を待つ)

  ```bash
  curl -fsSL https://get.docker.com -o get-docker.sh
  sudo sh get-docker.sh
  ```

- get-docker.shを削除する

  ```bash
  rm get-docker.sh
  ```

#### docker-compose のインストール

- ルート以外のユーザーでdockerコマンドを利用する設定を加える

  ```bash
  sudo usermod -aG docker $USER
  ```

#### ディストリビューション の設定変更(実行後ディストリビューションを再起動する)

- ルート以外のユーザーでdockerコマンドを利用する設定を加える

  ```bash
  sudo vi /etc/wsl.conf
  ```

  [/etc/wsl.conf]

  ```vi
  [boot]
  systemd=true
  ```

#### Dockerの動確

```bash
docker run hello-world
```
