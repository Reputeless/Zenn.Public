---
title: "Getting Started (Installing Siv3D SDK)"
free: true
---

Windows および macOS では 30 秒程度で Siv3D を始める準備が完了します。

# 1. Getting Started with Siv3D in Windows

## 1.1 Installing Siv3D SDK

- **[OpenSiv3D v0.6.2 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.2_Installer.exe)** をダウンロードして実行します。実行時に「Windows によって PC が保護されました」と表示された場合は、「詳細情報」を押して「実行」を押します。
- インストーラが自動的に以下のことを行います
  - SDK のコピー（デフォルトではドキュメントフォルダ）
  - SDK のパスへのユーザ環境変数の設定
  - Siv3D プロジェクト用の Visual Studio プロジェクトテンプレートのコピー (通常は `ドキュメント\Visual Studio 2019\Templates\ProjectTemplates\`)
  - アンインストーラの登録

> OpenSiv3D SDK を削除するには、Windows の設定からアンインストールします。
> ![](/images/doc_v6/manual/uninstall.png)

::: message
インストーラの実行に失敗する場合は、このページの「4. Windows で SDK を手動インストールして Siv3D を始める」の方法で SDK をインストールしてください。
:::

## 1.2 Building your first application with Siv3D

https://www.youtube.com/watch?v=O0XtvulXSOk

1. Visual Studio 2019 のスタート画面で **新しいプロジェクトの作成** をクリックします。
1. プロジェクト テンプレートのリストから **OpenSiv3D** を選択し、**次へ** を押します (⚠️表示されない場合は[こちら](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/troubleshooting-setup))
1. プロジェクト名と保存場所を入力し（任意）、**作成** を押します
1. サンプルプログラム (Main.cpp) が最初から用意されています
1. **ビルド** メニューからプロジェクトをビルドします
1. **デバッグ** メニューの **デバッグの開始** でビルドしたプログラムを実行します

# 2. Getting Started with Siv3D in macOS

## 2.1 Downloading the Siv3D project template

- **[OpenSiv3D v0.6.2 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.2_macOS.zip)** をダウンロードしてファイルを展開します

- macOS Catalina 以降の場合、プログラム実行時に、毎回ファイルアクセス許可のダイアログが出現するのを防ぐため、`ユーザ/デスクトップ` や `ユーザ/ダウンロード` フォルダではなく、`ユーザ/アプリケーション` フォルダへ移動させます

## 2.2 Building your first application with Siv3D
1. プロジェクトファイル `examples/empty/empty.xcodeproj` を Xcode で開きます
1. サンプルプログラム (Main.cpp) が最初から用意されています
1. **実行ボタン ▶️** を押すと、プログラムをビルドして実行します。
1. macOS Catalina 以降でファイルアクセス許可のダイアログが出現する場合、プロジェクトフォルダ全体を、`ユーザ/アプリケーション` フォルダ以下へ移動させることで回避できます

> 新しいプロジェクトを増やしたい場合は、プロジェクトテンプレートフォルダ内にある `empty` フォルダを同じ階層にコピーしてください。  
> Xcode 用のプロジェクトジェネレータは将来提供される予定です。

# 3. Getting Started with Siv3D in Linux

Linux 版は SDK 形式での配布が無いため。コードを自前でビルドするところから始めます。

## 3.1 Getting the latest source code from the official OpenSiv3D repository

[OpenSiv3D 公式リポジトリの main ブランチ](https://github.com/Siv3D/OpenSiv3D) が、最新の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://storage.googleapis.com/zenn-user-upload/nc8tfa4gj60oyu134d99tboqtla8)

## 3.2 Installing the dependent packages
次を参考に必要なパッケージをインストールします。  
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L26-L49

Boost ライブラリについては 1.71.0 / 1.72.0 / 1.73.0 のみサポートしています。それより新しいバージョンではビルドエラーが発生します（将来サポート予定）。

## 3.3 Building the OpenSiv3D library
次を参考に Siv3D ライブラリをビルドし、`libSiv3D.a` を作成します。 
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L51-L60

## 3.4 Building your first application with Siv3D
次を参考に Siv3D アプリをビルドします。 
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L62-L71


# 4. Manually installing the Siv3D SDK (Windows)
If you have trouble with the SDK installer in Windows, you can manually install the Siv3D SDK.

## 4.1 Getting the Siv3D SDK

Download and extract [OpenSiv3D_SDK_0.6.2.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.2/OpenSiv3D_SDK_0.6.2.zip) (File size: 88 MB), and place the contents in your documents folder as follows:

- `.../Documents/OpenSiv3D_SDK_0.6.2/include`
- `.../Documents/OpenSiv3D_SDK_0.6.2/lib`

## 4.2 Setting the SDK folder path to the environment variable

Create a new environment variable `SIV3D_0_6_2` and set the path to the SDK folder (the parent folder of `include/` and `lib/` placed in 4.1).

Example: If you have placed `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.2/include`, set `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.2` to the environment variable `SIV3D_0_6_2`.

![](/images/doc_v6/manual/envvariable.png)

## 4.3 Deploying the OpenSiv3D project template (ZIP)

Visual Studio 用プロジェクトテンプレート [OpenSiv3D_0.6.2.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.2/OpenSiv3D_0.6.2.zip) (サイズ: 約 63 MB) をダウンロードし、**展開せず ZIP ファイルのまま**、Visual Studio 2019 インストール時にドキュメントフォルダに作成される `Documents/Visual Studio 2019/Templates/ProjectTemplates/` フォルダの中に配置します。

![](/images/doc_v6/manual/projecttemplate.png)

以上で手動インストールの手順は完了です。環境変数の適用を確実にするために PC を再起動してください。

---

次のページでは、Siv3D の基本のサンプルプログラムをカスタマイズする方法を紹介します。
