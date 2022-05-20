---
title: "Siv3D SDK を自前ビルドする手順"
free: true
---

自前でライブラリのソースコードをビルドする手順を説明します。このページは次のような特殊な利用者向けの説明です。

- 開発中のバージョンの最新のコードを試したい
- Siv3D の内部を理解したい
- 内部のコードを改造したい

# 1. Windows の場合

## 1.1 追加のサードパーティ・ライブラリをダウンロードする
◆ OpenSiv3D のライブラリ本体のビルドに必要な C++ ライブラリ **「Boost」** を準備します

https://www.boost.org/users/history/version_1_74_0.html から `boost_1_74_0` の圧縮されたソースコードをダウンロードし、展開します。配布されているファイル形式は `.7z` と `.zip` がありますが、もし使用しているコンピュータで `.7z` の展開ができるなら `.7z` を使ったほうが所用時間が短いです。`boost_1_74_0.zip` は巨大なので、Windows OS 標準の ZIP 展開機能を使用すると展開の完了まで数分近くかかりますが、`.7z` はその数分の一以下で終わります。

[Boost](https://www.boost.org/) は 20 年以上の歴史がある、C++ で最も有名なライブラリの 1 つです。様々な目的のために作られた大小さまざま、作者もさまざまなライブラリ群で構成されています。C++11 で標準ライブラリに入った `std::shared_ptr`, C++17 で標準ライブラリに入った `std::optional`, `<filesystem>` はそれぞれ Boost.SmartPtr, Boost.Optional, Boost.Fileystem ライブラリをベースに設計されました。OpenSiv3D では、幾何問題の計算処理のために Boost.Geometry, C++17 をサポートしない環境におけるファイルシステム処理のために Boost.Filesystem, 子プロセスの作成・通信のために Boost.Process, 多倍長計算のために Boost.MultiPrecision, CSV パーサのために Boost.Tokenizer など、いくつかの Boost ライブラリの機能を使用しています。

## 1.2 OpenSiv3D の開発ブランチからソースコードを入手する
◆ OpenSiv3D の最新コードを OpenSiv3D 公式リポジトリから入手します

[OpenSiv3D 公式リポジトリの main ブランチ](https://github.com/Siv3D/OpenSiv3D) が、最新の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://storage.googleapis.com/zenn-user-upload/nc8tfa4gj60oyu134d99tboqtla8)

## 1.3 追加のサードパーティ・ライブラリをコピーして追加する
◆ ダウンロードしたプロジェクトのフォルダに Boost の一部をコピーします

1.2 で入手した OpenSiv3D プロジェクトのフォルダ内に `Dependencies/boost_1_74_0/` フォルダがあります。この中へ 1.1 で準備した Boost ライブラリの一部である `boost_1_74_0/boost/` フォルダ (約 120 MB) をコピーします。つまりコピー後は `Dependencies/boost_1_74_0/boost/` となります。

![](/images/doc_v6/manual/boost.png)

## 1.4 OpenSiv3D ライブラリと OpenSiv3D アプリをビルドする
◆ Visual Studio で OpenSiv3D ライブラリと OpenSiv3D アプリをビルドします

1.2 で入手した OpenSiv3D プロジェクトのフォルダ内の `WindowsDesktop/OpenSiv3D.sln` を Visual Studio で開くと、OpenSiv3D ライブラリ本体のプロジェクト「Siv3D」と、テスト用のアプリのプロジェクト「Siv3D-Test」を含むソリューションが開きます。

「Siv3D-Test」プロジェクトをビルドします。初回のビルドでは必要なライブラリファイルが存在しないため、先に自動的に OpenSiv3D のライブラリ本体のプロジェクト「Siv3D」のビルドが始まります。ライブラリのビルドには数分かかります。

Windows 版の OpenSiv3D ライブラリビルドで `error C2039: '​CheckForDuplicateEntries': is not a member of 'Microsoft::WRL::Details'` というエラーが出た場合、Visual Studio Installer を使って新しい Windows 10 SDK (バージョン 10.0.18362.0 以降) をインストールすることで解決します。

# 2. macOS の場合

## 2.1 追加のサードパーティ・ライブラリをダウンロードする
◆ OpenSiv3D のライブラリ本体のビルドに必要な C++ ライブラリ **「Boost」** を準備します

https://www.boost.org/users/history/version_1_74_0.html から `boost_1_74_0` の圧縮されたソースコードをダウンロードし、展開します。

## 2.2 OpenSiv3D の開発ブランチからソースコードを入手する
◆ OpenSiv3D の最新コードを OpenSiv3D 公式リポジトリから入手します

[OpenSiv3D 公式リポジトリの main ブランチ](https://github.com/Siv3D/OpenSiv3D) が、最新の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://storage.googleapis.com/zenn-user-upload/nc8tfa4gj60oyu134d99tboqtla8)

## 2.3 追加のサードパーティ・ライブラリをコピーして追加する
◆ ダウンロードしたプロジェクトのフォルダに Boost の一部をコピーします。

2.2 で入手した OpenSiv3D プロジェクトのフォルダ内に `Dependencies/boost_1_74_0/` フォルダがあります。この中へ 2.1 で準備した Boost ライブラリの一部である `boost_1_74_0/boost/` フォルダ (約 120 MB) をコピーします。つまりコピー後は `Dependencies/boost_1_74_0/boost/` となります。

## 2.4 OpenSiv3D ライブラリをビルドする
◆ Xcode で OpenSiv3D ライブラリをビルドします

2.2 で入手した OpenSiv3D プロジェクトのフォルダ内の `macOS/OpenSiv3D.xcodeproj` を Xcode で開き、「Siv3D」という Target をビルドします。フルビルドには数分前後かかります。ビルドが完了すると `libSiv3D.a` が生成されます。

## 2.5 OpenSiv3D アプリをビルドする
◆ Xcode で OpenSiv3D のテストアプリをビルドします

次に「Siv3D-Test」という Target をビルドします。ソースコードは 1 つだけで、`macOS/Main.cpp` に見つかります。ビルドには数秒かかります。ビルドが完了すると `Siv3D-Test.app` が生成されます。


# 3. Linux の場合

通常のセットアップ手順が、自前ビルドの手順になります。
