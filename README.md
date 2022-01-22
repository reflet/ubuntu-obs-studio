## OBS Studio - Open Broadcaster Software
Linux環境でコマンド実行にてOBS Studioが使えるかを試す環境として作成してみる。

## ミドルウェア
* Ubuntu (OS)
* crontab
* OBS Studio (27.1.3)
* VLC Media Player

## 前提条件
* MacOS / bashが使えるwindows10 (※ [WSL2](https://docs.microsoft.com/ja-jp/windows/wsl/install-win10))
* [Gitコマンド](https://git-scm.com/) をインストール済み
* [Docker for Desktop](https://hub.docker.com/editions/community/docker-ce-desktop-mac) をインスール済み

## 環境構築
dockerでローカル環境(Ubuntu)を構築します。

### ソースコード配置

```bash
mkdir -p ~/workspace/obs-studio
cd ~/workspace/obs-studio
git clone git@github.com:reflet/ubuntu-obs-studio.git .
```

### Dockerイメージ
Dockerイメージを作成します。

```bash
docker-compose build
```

### サーバ起動
Dockerコンテナを起動します。

```bash
docker-compose up -d
```

### SSH接続
起動したDockerコンテナへSSH接続したい場合は下記コマンドで可能です。

```bash
docker-compose exec ubuntu bash
```

### OBS Studio
バージョンを確認してみる。
※ 確認したときは、OBS Studio - 27.1.3 (linux)

```bash
docker-compose exec ubuntu bash -c "obs --version"
```

ヘルプを見てみる。

```bash
$ docker-compose exec ubuntu bash -c "obs --help"
--help, -h: Get list of available commands.

--startstreaming: Automatically start streaming.
--startrecording: Automatically start recording.
--startreplaybuffer: Start replay buffer.
--startvirtualcam: Start virtual camera (if available).

--collection <string>: Use specific scene collection.
--profile <string>: Use specific profile.
--scene <string>: Start with specific scene.

--studio-mode: Enable studio mode.
--minimize-to-tray: Minimize to system tray.
--portable, -p: Use portable mode.
--multi, -m: Don't warn when launching multiple instances.

--verbose: Make log more verbose.
--always-on-top: Start in 'always on top' mode.

--unfiltered_log: Make log unfiltered.

--disable-updater: Disable built-in updater (Windows/Mac only)

--disable-missing-files-check: Disable the missing files dialog which can appear on startup.

--disable-high-dpi-scaling: Disable automatic high-DPI scaling

--version, -V: Get current version.
```

こんな感じでパラメータ指定して起動できるようではあるが、どこまで動くかは試してみないと...

```bash
$ docker-compose exec ubuntu bash -c 'obs --collection "youtube" --profile "Youtube" --scene "scene-01" --studio-mode --startstreaming --minimize-to-tray'
```

### Python
動画を加工するためにPythonをインストールしています。

バージョンを確認してみる。

```bash
$ docker-compose exec ubuntu bash -c "python2 --version"
```

※ 確認したときは、Python 2.7.18

### Crontab
Pythonのコードを定期実行できるようにCronを用意しているので状態を確認してみる。

```bash
docker-compose exec ubuntu bash -c "service cron status"
 * cron is not running
```

※ 初期状態は停止させています

cronで実行するサンプルコードに実行権限を付与しておく。

```bash
docker-compose exec ubuntu bash -c "chmod +x /var/www/html/example.sh"
```

cronを起動する。

```bash
docker-compose exec ubuntu bash -c "service cron start"
 * Starting periodic command scheduler cron          [ OK ]

docker-compose exec ubuntu bash -c "service cron status"
 * cron is running
```

実行状況を確認してみる。

```bash
$ docker-compose logs ubuntu
Attaching to obs-studio_ubuntu_1
ubuntu_1  | root@014ad45dab13:/var/www/html# Hello World
ubuntu_1  | Hello World
ubuntu_1  | Hello World
ubuntu_1  | Hello World
ubuntu_1  | Hello World
ubuntu_1  | Hello World
ubuntu_1  | Hello World
```

※ Dockerのログに実行結果が出力されています

### dockerコンテナ破棄

```bash
$ docker-compose down
```

## 補足
OBS Studioのコマンドラインで使えるパラメータはこれかな？
※ [OBS Launch Parameters](https://obsproject.com/wiki/Launch-Parameters)

|Parameter											|Description|
| ---- | ---- |
|--help, -h											|Get list of available parameters.|
|--version, -v									|Get OBS version.*|
|--startstreaming								|Automatically start streaming.|
|--startrecording								|Automatically start recording.|
|--startvirtualcam							|Automatically start virtual camera.|
|--startreplaybuffer						|Automatically start Replay Buffer.|
|--collection "name"						|Start with given scene collection.|
|--profile "name"								|Start with given profile.|
|--scene "name"									|Start with given scene.|
|--studio-mode									|Start with Studio Mode active.|
|--minimize-to-tray							|Start minimized to system tray.|
|--portable, -p									|Use portable mode.|
|--multi, -m										|Don't warn when launching multiple instances.|
|--always-on-top								|Start in 'always on top' mode.|
|--verbose											|Make log more verbose.|
|--unfiltered_log								|Disable log filter (do not suppress repeated lines).|
|--disable-updater							|Disable built-in updater (Windows/macOS only).|
|--allow-opengl									|Allow OpenGL renderer on Windows.|
|--disable-missing-files-check	|Disable missing file check dialog on startup.|

## 参考資料
* [OBS Studio ver27.0.0](https://newreleases.io/project/github/obsproject/obs-studio/release/27.0.0-rc3)
* [Ubuntu 20.04 - VLC Playerのインストール](https://codechacha.com/ja/ubuntu-install-vlc/)

以上
