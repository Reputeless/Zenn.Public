---
title: "開発の準備"
free: true
---

# 1. Windows で Siv3D を始める

## 1.1 SDK のインストール

- **[OpenSiv3D v0.6 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.0Installer.exe)** をダウンロードして実行します
- インストーラが自動的に以下のことを行います
  - SDK のコピー（デフォルトではドキュメントフォルダ）
  - SDK のパスへのユーザ環境変数の設定
  - Siv3D プロジェクト用の Visual Studio プロジェクトテンプレートのコピー (通常は `ドキュメント\Visual Studio 2019\Templates\ProjectTemplates\`)
  - アンインストーラの登録

> OpenSiv3D SDK を削除するには、Windows の設定からアンインストールします。
> ![](/images/doc_v6/manual/uninstall.png)

## 1.2 Siv3D アプリのビルド

https://www.youtube.com/watch?v=O0XtvulXSOk

1. Visual Studio 2019 のスタート画面で **新しいプロジェクトの作成** をクリックします。
1. プロジェクト テンプレートのリストから **OpenSiv3D** を選択し、**次へ** を押します (⚠️表示されない場合は[こちら](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/troubleshooting-setup))
1. プロジェクト名と保存場所を入力し（任意）、**作成** を押します
1. サンプルプログラム (Main.cpp) が最初から用意されています
1. **ビルド** メニューからプロジェクトをビルドします
1. **デバッグ** メニューの **デバッグの開始** でビルドしたプログラムを実行します

# 2. macOS で Siv3D を始める

## 2.1 プロジェクトテンプレートのダウンロード

- **[OpenSiv3D Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.0_macOS.zip)** をダウンロードしてファイルを展開します

- macOS Catalina 以降の場合、プログラム実行時に、ファイルアクセス許可のダイアログが出現するのを防ぐため、`ユーザ/デスクトップ` や `ユーザ/ダウンロード` フォルダではなく、`ユーザ/アプリケーション` フォルダへ移動させます

## 2.2 Siv3D アプリのビルド
1. プロジェクトファイル `examples/empty/empty.xcodeproj` を Xcode で開きます
1. **実行ボタン ▶️** を押すと、プログラムをビルドして実行します。
1. macOS Catalina 以降でファイルアクセス許可のダイアログが出現する場合、プロジェクトフォルダ全体を、`ユーザ/アプリケーション` フォルダ以下へ移動させることで回避できます

# 3. Linux で Siv3D を始める

## 3.1 OpenSiv3D の最新コードを OpenSiv3D 公式リポジトリから入手する

[OpenSiv3D 公式リポジトリの v6_master ブランチ](https://github.com/Siv3D/OpenSiv3D/tree/v6_master) が v0.6 の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://storage.googleapis.com/zenn-user-upload/nc8tfa4gj60oyu134d99tboqtla8)

## 3.2 依存パッケージをインストールする
次を参考に必要なパッケージをインストールします。  
https://github.com/Siv3D/OpenSiv3D/blob/v6_master/.github/workflows/ci.yml#L30-L53

## 3.3 OpenSiv3D ライブラリをビルドする
次を参考に Siv3D ライブラリをビルドし、`libSiv3D.a` を作成します。 
https://github.com/Siv3D/OpenSiv3D/blob/v6_master/.github/workflows/ci.yml#L55-L64

## 3.4 OpenSiv3D アプリをビルドする
次を参考に Siv3D アプリをビルドします。 
https://github.com/Siv3D/OpenSiv3D/blob/v6_master/.github/workflows/ci.yml#L66-L75

---

次のページでは、Siv3D の機能を体験するいくつかの小さなプログラムを実行します。
