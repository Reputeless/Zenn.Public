---
title: "開発を始める（SDK のインストール）"
free: true
---

Windows および macOS では 30 秒程度で Siv3D を始める準備が完了します。

# 1. Windows で Siv3D を始める

## 1.1 必要な環境
|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit)<br>Windows 8.1 (64-bit)<br>Windows 10 (64-bit)<br>Windows 11 |
| CPU | Intel もしくは AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |
| 開発環境 | [Microsoft Visual C++ 2019 16.11 または 2022 17.0](https://visualstudio.microsoft.com/ja/downloads/)<br>(インストーラ内で「C++ によるデスクトップ開発」を追加インストール) |

## 1.2 SDK のインストール

- **[OpenSiv3D v0.6.3 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.3_Installer.exe)** をダウンロードして実行します。実行時に「Windows によって PC が保護されました」と表示された場合は、「詳細情報」を押して「実行」を押します。

::: message
インストーラの実行に失敗する場合は、このページの「4. Windows で SDK を手動インストールして Siv3D を始める」の方法で SDK をインストールしてください。
:::

:::details インストーラが自動的に行うこと
- SDK のコピー（デフォルトではドキュメントフォルダ）
- SDK のパスへのユーザ環境変数の設定
- Siv3D プロジェクト用の Visual Studio プロジェクトテンプレートのコピー (通常は `ドキュメント\Visual Studio 2019\Templates\ProjectTemplates\` および `ドキュメント\Visual Studio 2022\Templates\ProjectTemplates\`)
- アンインストーラの登録
:::

:::details OpenSiv3D SDK を削除するには
Windows の設定からアンインストールします。
![](/images/doc_v6/manual/uninstall.png)
:::

## 1.3 Siv3D アプリのビルド

https://www.youtube.com/watch?v=O0XtvulXSOk

1. Visual Studio のスタート画面で **新しいプロジェクトの作成** をクリックします。
1. プロジェクト テンプレートのリストから **OpenSiv3D** を選択し、**次へ** を押します (⚠️表示されない場合は[こちら](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/troubleshooting-setup))
1. プロジェクト名と保存場所を入力し（任意）、**作成** を押します
1. サンプルプログラム (Main.cpp) が最初から用意されています
1. **ビルド** メニューからプロジェクトをビルドします
1. **デバッグ** メニューの **デバッグの開始** でビルドしたプログラムを実行します

# 2. macOS で Siv3D を始める

## 2.1 必要な環境
|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur |
| CPU | Intel 製の CPU |
| GPU | OpenGL 4.1 サポート |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 開発環境 | Xcode 11.3 以降<br>(Big Sur の場合は Xcode 12.5 以降) |

- Apple Silicon は将来のバージョンでサポートが追加されます 
- 2012 年以前の Mac 製品では GPU が OpenGL 4.1 をサポートしていない場合があります

## 2.2 プロジェクトテンプレートのダウンロード
1. **[OpenSiv3D v0.6.3 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.3_macOS.zip)** をダウンロードしてファイルを展開します
1. macOS Catalina 以降の場合、プログラム実行時に、毎回ファイルアクセス許可のダイアログが出現するのを防ぐため、`ユーザ/デスクトップ` や `ユーザ/ダウンロード` フォルダではなく、`ユーザ/アプリケーション` フォルダへ移動させます

## 2.3 Siv3D アプリのビルド
1. プロジェクトファイル `examples/empty/empty.xcodeproj` を Xcode で開きます
1. サンプルプログラム (Main.cpp) が最初から用意されています
1. **実行ボタン ▶️** を押すと、プログラムをビルドして実行します。
1. macOS Catalina 以降でファイルアクセス許可のダイアログが出現する場合、プロジェクトフォルダ全体を、`ユーザ/アプリケーション` フォルダ以下へ移動させることで回避できます

:::details 新しいプロジェクトを増やしたい場合は
プロジェクトテンプレートフォルダ内にある `empty` フォルダを同じ階層にコピーしてください。Xcode 用のプロジェクトジェネレータは将来提供される予定です。
:::

# 3. Linux で Siv3D を始める

Linux 版は SDK 形式での配布が無いため、ライブラリを自前でビルドするところから始めます。

## 3.1 必要な環境
|  |  |
|--|--|
| OS | Ubuntu 20.04 LTS |
| CPU | Intel もしくは AMD 製の CPU |
| GPU | OpenGL 4.1 サポート |
| 開発環境 | GCC 9.3.0<br>Boost 1.71.0 - 1.73.0 |

## 3.2 OpenSiv3D の最新コードを入手する

[OpenSiv3D 公式リポジトリの main ブランチ](https://github.com/Siv3D/OpenSiv3D) が、最新の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://storage.googleapis.com/zenn-user-upload/nc8tfa4gj60oyu134d99tboqtla8)

## 3.3 依存パッケージをインストールする
次を参考に必要なパッケージをインストールします。  
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L26-L49

Boost ライブラリについては 1.71.0 / 1.72.0 / 1.73.0 のみサポートしています。それより新しいバージョンではビルドエラーが発生します（将来サポート予定）。GCC のバージョンも 9.3.0 が必要で、それより新しいバージョンは現在サポートしていません。

## 3.4 OpenSiv3D ライブラリをビルドする
次を参考に Siv3D ライブラリをビルドし、`libSiv3D.a` を作成します。 
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L51-L60

## 3.5 OpenSiv3D アプリをビルドする
次を参考に Siv3D アプリをビルドします。 
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L62-L71

デフォルトの Main.cpp は「すぐ終了する空のプログラム」になっているため、リファレンスのサンプルなどで上書きしてください。


# 4. Windows で SDK を手動インストールする
Windows において OpenSiv3D インストーラが正常に実行されない場合、代わりに手作業で OpenSiv3D をインストールできます。

## 4.1 OpenSiv3D ヘッダ・ライブラリのダウンロードと配置

[OpenSiv3D_SDK_0.6.3.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.3/OpenSiv3D_SDK_0.6.3.zip) (サイズ: 約 88 MB) をダウンロードして展開し、中身をドキュメントフォルダに次のように配置します。

- `.../Documents/OpenSiv3D_SDK_0.6.3/include`
- `.../Documents/OpenSiv3D_SDK_0.6.3/lib`

## 4.2 ヘッダ・ライブラリフォルダのパスを環境変数に設定

ユーザー環境変数 `SIV3D_0_6_3` を新規作成し、4.1 で配置した OpenSiv3D SDK のフォルダ (`include/`, `lib/` の親フォルダ) のパスを設定します。

例: `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.3/include` のように配置した場合、`C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.3` を環境変数 `SIV3D_0_6_3` に設定します。

![](/images/doc_v6/manual/envvariable.png)

## 4.3 OpenSiv3D プロジェクトテンプレート (ZIP) の配置

Visual Studio 用プロジェクトテンプレート [OpenSiv3D_0.6.3.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.3/OpenSiv3D_0.6.3.zip) (サイズ: 約 63 MB) をダウンロードし、**展開せず ZIP ファイルのまま**、Visual Studio 2019 インストール時にドキュメントフォルダに作成される `Documents/Visual Studio 2019/Templates/ProjectTemplates/` フォルダの中に配置します。

![](/images/doc_v6/manual/projecttemplate.png)

以上で手動インストールの手順は完了です。環境変数の適用を確実にするために PC を再起動してください。

---

次のページでは、Siv3D の基本のサンプルプログラムをカスタマイズする方法を紹介します。
