---
title: "GitHubActionsでBlenderをレンダリング"
emoji: "🐵"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["blender", "ubuntu", "GitHubActions"]
published: true
---

GitHubActions を用いて Blender(2.9)をレンダリングできたので残します。

レポジトリ
https://github.com/OHMORIYUSUKE/blender-rendering

:::message

### 注意

GitHubActions の実行時間には制限があります。
[詳細](https://retrorocket.biz/archives/1689)
**_参考:6 時間で 106 フレームレンダリングすることができました。_**
:::

# 説明

## 注意

:::message
GitHubActions ではウィンドウを開くことができないので EEVEE でレンダリングすることができませんでした。
:::
https://devtalk.blender.org/t/blender-2-8-unable-to-open-a-display-by-the-rendering-on-the-background-eevee/1436

## Blender をインストール

ubuntu で blender(2.9)をインストールするには snapd を用いてインストールします。

https://snapcraft.io/install/blender/ubuntu

そのため、`apt install snapd`で snapd をインストールします。

https://snapcraft.io/docs/installing-snap-on-kubuntu

まとめると、以下のシェルで Blender を ubuntu でインストールできます。

```sh
#!/bin/bash
sudo apt -y update
sudo apt list --upgradable
sudo apt install snapd
sudo snap install blender --classic
```

## Blender をコマンドからレンダリング

### アニメーション

```sh:render_anim.sh
sudo blender --background -noaudio blend/Miraikomachi.blend --threads 0 -E CYCLES --render-output img/anim -s 1000 -e 1030 -a
```

- `--background`バックグラウンドでレンダリング
- `-noaudio`音なしでレンダリング
- `blend/Miraikomachi.blend`レンダリングしたい blend ファイルを指定
- `--threads 0`システムプロセッサ数には 0 を使用
- `-E CYCLES`レンダリングエンジンを指定(`BLENDER_EEVEE`を指定するとエラーになります)
- `--render-output img/anim`画像を出力するときのファイル名(`img/anim0001.png`のように画像が連番で出力される)
- `-s 1000 -e 1030`1000 フレームから 1030 フレームまでをレンダリングする
- `-a`画像を複数枚生成するときにつける

### 画像

```sh:render.sh
sudo blender --background -noaudio blend/Miraikomachi.blend --threads 0 -E CYCLES --render-output img/anim --render-frame 2310
```

- `--render-frame 2310`2310 フレームをレンダリングする

詳しいコマンドの説明

https://docs.blender.org/manual/en/latest/advanced/command_line/arguments.html

コマンド一覧に出力ファイルで動画を指定できなかったので Python で動画を生成しました。

# Python で連番画像から動画を生成

```python:render_anim.sh
# 動画生成
sudo pip install opencv-python-headless==4.4.0.44
sudo python3 pngtomp4.py
```

opencv をインストールして`pngtomp4.py`を実行し、

```sh:render_anim.sh
sudo blender --background -noaudio blend/Miraikomachi.blend --threads 0 -E CYCLES --render-output img/anim -s 1000 -e 1030 -a
```

で作成した img ディレクトリにある PNG の連番画像から動画を作成します。

# 最後に

PC でレンダリングすると、レンダリング中に行う他の作業に支障をきたしてしまう方は GitHubActions でレンダリングしてはどうでしょうか。また、利用する際は GitHubActions の実行時間の制限に注意しましょう。

Blender(2.8)では以下のようにインストールできますが、2.9 ではインストールできませんでした。また、2.8 と 2.9 では`.blend`のデータが異なるため、このレポジトリでは 2.8 のレンダリングはできません。

https://www.kkaneko.jp/tools/ubuntu/ubuntu_blender.html
