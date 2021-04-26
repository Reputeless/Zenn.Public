---
title: "開発の準備 | フルビルドの場合（上級者向け）"
free: true
---

Siv3D の内部を理解したい場合や、内部のコードを改造したい場合に、自前でライブラリのソースコードをビルドする手順を説明します。このページは特殊な利用者向けの説明です。通常はスキップしてください。

# 1. Windows の場合

## 1.1 Visual Studio をインストールする
Visual Studio のインストーラをダウンロードし、実行します。  

https://visualstudio.microsoft.com/ja/downloads/ から **「Visual Studio Community（コミュニティ）」** を選択してインストーラをダウンロードし、実行します。

インストーラを実行すると、インストールするプログラミング言語や開発ツールを選択する次のような画面が出てきます。インストール項目の選択画面から **「C++ によるデスクトップ開発」** を選択します（右側の「インストールの詳細」に表示される項目は Visual Studio のバージョンによって異なるため、気にする必要はありません）。

![](https://i.gyazo.com/thumb/1000/34b2cf39108e55cb534fc9d9cb8282ac-png.png)

そのまま右下の **「インストール」** ボタンを押せば、C++ プログラミングに必要な最小限のツールのインストールがはじまります。インストールする項目はあとで追加や変更ができます。ほかのプログラミング言語やツールに興味を持ったときや、アンインストールの際にもこのインストーラを使います。

## 1.2 追加のサードパーティ・ライブラリをダウンロードする
OpenSiv3D のライブラリ本体のビルドに必要な C++ ライブラリ **「Boost」** を準備します。

https://www.boost.org/users/history/version_1_73_0.html から `boost_1_73_0` の圧縮されたソースコードをダウンロードし、展開します。配布されているファイル形式は `.7z` と `.zip` がありますが、もし使用しているコンピュータで `.7z` の展開ができるなら `.7z` を使ったほうが所用時間が短いです。`boost_1_73_0.zip` は巨大なので、Windows OS 標準の ZIP 展開機能を使用すると展開の完了まで数分近くかかりますが、`.7z` はその数分の一以下で終わります。

[Boost](https://www.boost.org/) は 20 年以上の歴史がある、C++ で最も有名なライブラリの 1 つです。様々な目的のために作られた大小さまざま、作者もさまざまなライブラリ群で構成されています。C++11 で標準ライブラリに入った `std::shared_ptr`, C++17 で標準ライブラリに入った `std::optional`, `<filesystem>` はそれぞれ Boost.SmartPtr, Boost.Optional, Boost.Fileystem ライブラリをベースに設計されました。OpenSiv3D では、幾何問題の計算処理のために Boost.Geometry, C++17 をサポートしない環境におけるファイルシステム処理のために Boost.Filesystem, 子プロセスの作成・通信のために Boost.Process, 多倍長計算のために Boost.MultiPrecision, CSV パーサのために Boost.Tokenizer など、いくつかの Boost ライブラリの機能を使用しています。

## 1.3 OpenSiv3D の開発ブランチからソースコードを入手する
OpenSiv3D の最新コードを OpenSiv3D 公式リポジトリから入手します。

[OpenSiv3D 公式リポジトリの v6_master ブランチ](https://github.com/Siv3D/OpenSiv3D/tree/v6_master) が、現在開発中の v0.6 の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

## 1.4 追加のサードパーティ・ライブラリをコピーして追加する
ダウンロードしたプロジェクトのフォルダに Boost の一部をコピーします。

1.3 で入手した OpenSiv3D プロジェクトのフォルダ内に `Dependencies/boost_1_73_0/` フォルダがあります。この中へ 1.2 で準備した Boost ライブラリの一部である `boost_1_73_0/boost/` フォルダ (約 120 MB) をコピーします。つまりコピー後は `Dependencies/boost_1_73_0/boost/` となります。

## 1.5 OpenSiv3D ライブラリをビルドする


## 1.6 OpenSiv3D アプリをビルドする


# 2. macOS の場合

## 2.1 Xcode をインストールする
**Xcode 11.3 以降** をインストールします。

使用している macOS の OS バージョンによっては、App Store にある最新の Xcode をインストールできないことがあります。その場合は https://developer.apple.com/download/more/ から Xcode の 11.3.1 など、過去のバージョンをダウンロードしてください。

## 2.2 追加のサードパーティ・ライブラリをダウンロードする
OpenSiv3D のライブラリ本体のビルドに必要な C++ ライブラリ **「Boost」** を準備します。

https://www.boost.org/users/history/version_1_73_0.html から `boost_1_73_0` の圧縮されたソースコードをダウンロードし、展開します。

## 2.3 OpenSiv3D の開発ブランチからソースコードを入手する
OpenSiv3D の最新コードを OpenSiv3D 公式リポジトリから入手します。

[OpenSiv3D 公式リポジトリの v6_master ブランチ](https://github.com/Siv3D/OpenSiv3D/tree/v6_master) が、現在開発中の v0.6 の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

## 2.4 追加のサードパーティ・ライブラリをコピーして追加する
ダウンロードしたプロジェクトのフォルダに Boost の一部をコピーします。

1.3 で入手した OpenSiv3D プロジェクトのフォルダ内に `Dependencies/boost_1_73_0/` フォルダがあります。この中へ 1.2 で準備した Boost ライブラリの一部である `boost_1_73_0/boost/` フォルダ (約 120 MB) をコピーします。つまりコピー後は `Dependencies/boost_1_73_0/boost/` となります。

## 2.5 OpenSiv3D ライブラリをビルドする


## 2.6 OpenSiv3D アプリをビルドする


# 3. Linux の場合

## 3.1 OpenSiv3D の開発ブランチからソースコードを入手する
OpenSiv3D の最新コードを OpenSiv3D 公式リポジトリから入手します。

[OpenSiv3D 公式リポジトリの v6_master ブランチ](https://github.com/Siv3D/OpenSiv3D/tree/v6_master) が、現在開発中の v0.6 の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

## 3.2 依存パッケージをインストールする
次を参考にパッケージをインストールします。  
https://github.com/Siv3D/OpenSiv3D/blob/v6_winmac_develop/.github/workflows/ci.yml#L30

## 3.3 OpenSiv3D ライブラリをビルドする


## 3,4 OpenSiv3D アプリをビルドする



