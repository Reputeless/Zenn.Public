---
title: "System Requirements"
free: true
---

# 1. OpenSiv3D v0.6 を使う開発に必要な環境

OpenSiv3D v0.6 でプログラミングをするには、プラットフォームに応じて次の開発環境が必要です。

## 1.1 Windows
|  |  |
|--|--|
| OS | Windows 10 (64-bit)<br>Windows 11 |
| CPU | Intel もしくは AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |
| 開発環境 | [Microsoft Visual C++ 2022 17.2](https://visualstudio.microsoft.com/downloads/)<br>(Install "Desktop development with C++" from the Visual Studio Installer) |

## 1.2 macOS
|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur |
| CPU | Intel 製の CPU / Apple Silicon (Rosetta mode) |
| Graphics | OpenGL 4.1 compatible hardware |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 開発環境 | Xcode 11.3 以降<br>(Big Sur requires Xcode 12.5 or newer) |

- Native Apple Silicon support will be added in the future release
- 2012 年以前の Mac 製品では GPU が OpenGL 4.1 をサポートしていない場合があります

## 1.3 Linux
|  |  |
|--|--|
| OS | Ubuntu 20.04 LTS / 22.04 LTS |
| CPU | Intel もしくは AMD 製の CPU |
| Graphics | OpenGL 4.1 compatible hardware |
| 開発環境 | GCC 9.3.0 (with Boost 1.71.0) / GCC 11.2 (with Boost 1.74.0) |

## 1.4 Web
Web 版は現在試験的な実装が提供されています。  
最新の情報は [OpenSiv3D for Web プロジェクトページ](https://siv3d.kamenokosoft.com/index) を確認してください。  
将来のバージョンで公式サポート対象に含まれる予定です。


# 2. OpenSiv3D v0.6 で開発したアプリの実行に必要な環境

OpenSiv3D v0.6 で開発されたアプリケーションを実行するには、次の環境が必要です。

## 2.1 Windows
|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit)<br>Windows 8.1 (64-bit)<br>Windows 10 (64-bit)<br>Windows 11 |
| CPU | Intel もしくは AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |

## 2.2 macOS
|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur |
| CPU | Intel 製の CPU |
| Graphics | OpenGL 4.1 compatible hardware |
| 映像出力 | モニタなど、何らかの映像出力装置があること |

- Native Apple Silicon support will be added in the future release
- 2012 年以前の Mac 製品では GPU が OpenGL 4.1 をサポートしていない場合があります

## 2.3 Linux
|  |  |
|--|--|
| OS | Ubuntu 20.04 LTS / 22.04 LTS |
| CPU | Intel もしくは AMD 製の CPU |
| Graphics | OpenGL 4.1 compatible hardware |

## 2.4 Web
Web 版は現在試験的な実装が提供されています。  
最新の情報は [OpenSiv3D for Web プロジェクトページ](https://siv3d.kamenokosoft.com/index) を確認してください。  
将来のバージョンで公式サポート対象に含まれる予定です。


# 3. 開発環境のインストールガイド

## 3.1 Installing Visual Studio (Windows)
Windows 7 や Windows 8.1, Windows 10 のパソコンで Siv3D プログラミングをする場合は「Visual Studio Community 2019（ビジュアル・スタジオ・コミュニティ 2019）」を使うのが便利です。これは世界中のプロフェッショナルのソフトウェア開発者が使う「Visual Studio」というプログラミングツールの無料版です。学生、個人、少人数の開発であれば、Visual Studio の有料版と同じ機能を無料で使えます。

### 手順

https://visualstudio.microsoft.com/downloads/ から **「Visual Studio 2022 コミュニティ」** を選択してインストーラをダウンロードし、実行します。

インストーラを実行すると、インストールするプログラミング言語や開発ツールを選択する次のような画面が出てきます。インストール項目の選択画面から **「C++ によるデスクトップ開発」** を選択します（右側の「インストールの詳細」に表示される項目は Visual Studio のバージョンによって異なるため、気にする必要はありません）。

![](https://i.gyazo.com/thumb/1000/34b2cf39108e55cb534fc9d9cb8282ac-png.png)

そのまま右下の **「インストール」** ボタンを押せば、C++ プログラミングに必要なツールのインストールがはじまります。

## 3.2 Installing Xcode (macOS)
MacBook や iMac など macOS のパソコンで Siv3D プログラミングをする場合は「Xcode（エックスコード）」を使います。アプリケーションを開発するための開発環境で、アップル社が無料で提供しています。

### 手順

App Store から **Xcode** をインストールします。使用している macOS のバージョンによっては、App Store にある最新の Xcode をインストールできないことがあります。その場合は https://developer.apple.com/download/more/ から 過去の Xcode 11.3.1 以降のバージョンをダウンロードしてください。

---

次のページでは、Siv3D SDK を導入して開発を始める方法を説明します。
