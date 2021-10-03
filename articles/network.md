---
title: "ネットワーク(基本設定編)"
emoji: "🌐"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["初心者", "network", "cisco", "スイッチ", "ルーター"]
published: true
---

実際に機器を触り設定を行うことができる機会がありました。そこで学んだことについて書きました。間違えている点があった場合、教えていただけると幸いです。

## 注意点

間違えて設定してしまった場合は`no {打ったコマンド}`で設定を削除できます。また、設定を上書きもできます。

- 設定削除
  例:`switch(config)#hostname DSW`->`DSW(config)#no hostname DSW`->`switch(config)#`
- 設定上書き
  例:`switch(config)#hostname DSW`->`DSW(config)#hostname ASW`->`ASW(config)#`

# 基本設定

以下の設定を行う

- グローバルコンフィグレーションモードの設定
  - ホストネームの変更
  - パスワードの設定
  - 名前解決の無効化
  - ルーティングの有効化
- ラインコンフィグレーションモードの設定
  - パスワードの設定
  - タイムアウトの無効化
  - 途中コマンドの再表示
- ラインコンフィグレーションモードの仮想回線(仮想回線 0~4)の設定
  - パスワードの設定
  - 途中コマンドの再表示

## モードの解説

最初にモードの説明です。モードによって設定できる内容が異なります。

- ユーザモード
- 特権モード
- グローバルコンフィグレーションモード
- インタフェースコンフィグレーションモード
- ラインコンフィグレーションモード
- ルータコンフィグレーションモード

上記のようなモードがあります。各モードの説明をします。

| モード                                   | コマンドプロンプト       | 説明                                                                  |
| ---------------------------------------- | ------------------------ | --------------------------------------------------------------------- |
| ユーザモード                             | `switch>`                | ログイン直後に遷移するモード                                          |
| 特権モード                               | `switch#`                | 機器を操作するモード。設定は行うことができない。                      |
| グローバルコンフィグレーションモード     | `switch(config)#`        | 機器の設定を行う。以下の 3 つのモードに移動するために経由するモード。 |
| インタフェースコンフィグレーションモード | `switch(config-if)#`     | 機器のインターフェースに関する設定を行う                              |
| ラインコンフィグレーションモード         | `switch(config-line)#`   | telnet や SSH に関する設定を行う                                      |
| ルータコンフィグレーションモード         | `switch(config-router)#` | ルーティングプロトコルの設定を行う                                    |

### モードの構成図

![モードの構成](https://storage.googleapis.com/zenn-user-upload/53a9592459e1648391918438.png)

:::message
初期状態ではパスワードは設定されていないので、ログインは不要
:::

## ユーザモードに移動

機器に接続したらこのような表示になる。

```sh
switch>
```

:::message
パスワードを要求された場合は、パスワードを入力するとユーザモードに移動する。
:::

## 特権モードに移動

```sh
switch>enable
switch#
```

:::message
パスワードを要求された場合は、パスワードを入力すると特権モードに移動する。
:::

## グローバルコンフィグレーションモードに移動

```sh
switch# configure terminal
switch(config)#
```

## ラインコンフィグレーションモードに移動

```sh
switch(config)# line console 0
switch(config-line)#
```

## ラインコンフィグレーションモード(仮想回線)に移動

仮想回線 0~4 の設定を行うモード。telnet や SSH での通信時の設定を行う。

```sh
switch(config)# line vty 0 4
switch(config-line)#
```

---

## モードを戻る

例 : ラインコンフィグレーションモードからグローバルコンフィグレーションモードに移動

```sh
switch(config-line)#exit
switch(config)#exit
```

## 機器からログアウトする

特権モードで以下のコマンドでログアウトできる。

```sh
switch#logout
```

---

以上でモードの説明は終了です。

# ホストネームの変更

機器に名前を付けます。**_グローバルコンフィグレーションモード_**で、以下のようにコマンドを入力します。`hostname {ホストネーム}`

```sh
switch(config)#hostname DSW
DSW(config)#
```

# パスワードの設定

## 特権モードにパスワードを設定

モードの説明で以下のようにモードを移動できると説明した。ここのモード移動にパスワードを設定する。

> ## 特権モードに移動
>
> ```sh
> switch>enable
> switch#
> ```

コマンドは以下の通りです。**_グローバルコンフィグレーションモード_**で設定を行う。`enable password {パスワード}`または`enable secret {パスワード}`

```sh
switch(config)#enable password test
```

```sh
switch(config)#enable secret test
```

---

上記の違いについて解説します。

```sh
switch(config)#enable password test
```

は、暗号化されないパスワードの設定方法です。`switch#show running-config`でルーターの情報を確認した際にパスワードが表示されてしまう。しかし、暗号化を後から行うこともできる。以下のコマンドです。

```sh
switch(config)#service password-encryption
```

上記のコマンドでパスワードを暗号化できます。しかし、こちらの暗号の解読は容易なため推奨されていません。

次に、以下のパスワードの設定コマンドです。

```sh
switch(config)#enable secret test
```

こちらは特に設定することなく暗号化されています。また、こちらのパスワードの解読は困難です。よって、こちらのコマンドでパスワードを設定することが推奨されています。

:::message
`enable password test`と設定した場合は`service password-encryption`で暗号化した方がよいです。背後から覗かれたときに暗号化パスワードは覚えにくいためです。
:::

:::message
上記の２つのコマンドでパスワードを設定した場合は`enable secret {パスワード}`で設定されたパスワードが有効になります。
:::

参考
https://shinmeisha.co.jp/newsroom/2020/05/27/cisco-%E6%A9%9F%E5%99%A8%E3%81%AB%E8%A8%AD%E5%AE%9A%E3%81%97%E3%81%9F%E8%84%86%E5%BC%B1%E3%81%AA%E6%9A%97%E5%8F%B7%E5%8C%96%E3%83%91%E3%82%B9%E3%83%AF%E3%83%BC%E3%83%89%E3%81%AE%E8%A7%A3%E8%AA%AD/

---

## ユーザモードにパスワードを設定

**_ラインコンフィグレーションモード_**で設定を行います。機器に入るときに求められるパスワードです。コマンドは以下の通りです。`password {パスワード}`

```sh
switch(config)#line console 0
switch(config-line)#password test
switch(config-line)#login
```

以下のコマンドで暗号化できます。

```sh
switch(config)#service password-encryption
```

## ユーザモードにパスワード(仮想回線)を設定

**_ラインコンフィグレーションモード_**で設定を行います。telnet や SSH で遠くの機器に入るときに求められるパスワードです。コマンドは以下の通りです。`password {パスワード}`

```sh
switch(config)#line vty 0 4
switch(config-line)#password test
switch(config-line)#login
```

`switch(config)#line vty 0 4`で仮想回線 0~4 の設定モードに入ります。

:::message
このパスワードが設定されていない場合は telnet や SSH で接続できません。
:::

以下のコマンドで暗号化できます。

```sh
switch(config)#service password-encryption
```

# タイムアウトの無効化

**_ラインコンフィグレーションモード_**で行います。一定時間操作がない場合ログアウトしてしまいます。そのため、タイムアウトを無効化します。`exec-timeout {分} {秒}`

> デフォルト値は 10 分です。

```sh
switch(config)#line console 0
switch(config-line)#exec-timeout 0 0
```

このように 0 に設定することで無効化できます。`exec-timeout {分} {秒}`と設定すると、指定した時間でタイムアウトします。

# 入力途中のコマンドを再表示

**_ラインコンフィグレーションモード_**で行います。機器から出力されるログでコマンドが打ちにくくなることを防ぎます。`logging synchronous`

```sh
switch(config)#line console 0
switch(config-line)#logging synchronous
```

# 名前解決の無効化

**_グローバルコンフィグレーションモード_**で行います。以下のようにコマンドを入力することで名前解決を行わないようにできます。

```sh
switch(config)#no ip domain-lookup
```

この設定を行わない場合は

```sh
switch#pong
Translating "pong"
```

のように DNS を探し、通信を試みます。通信失敗までに時間がかかります。

# ルーティングの有効化

**_グローバルコンフィグレーションモード_**で行います。以下のようにコマンドを入力することで、インターフェースに IP アドレスやサブネットマスクを設定する事でルーティング可能になります。

```sh
switch(config)# ip routing
```

![ルーティング](https://storage.googleapis.com/zenn-user-upload/9a0338b9de65fc9d2e110a52.png)

---

以上で基本設定は終了です。これで快適に作業できるようになりました。

[次は、**_スタティックルーティング_**です。](https://zenn.dev/u_tan/articles/0e1fc16e4c2e24)

---

参考にしたサイト
https://www.infraexpert.com/study/
https://beginners-network.com/cisco-catalyst-command/
