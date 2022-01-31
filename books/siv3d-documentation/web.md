---
title: "Web 向けの開発"
free: true
---

Siv3D Web 版 (experimental) を利用すると、Web ブラウザで動作可能な Siv3D アプリケーションを C++ だけで開発できます。

# 1. Visual Studio で Web 向け開発を始める (Windows)

## 1.1 必要な環境
|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit)<br>Windows 8.1 (64-bit)<br>Windows 10 (64-bit)<br>Windows 11 |
| CPU | Intel もしくは AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |
| 開発環境 | [Microsoft Visual C++ 2022 17.0](https://visualstudio.microsoft.com/ja/downloads/)<br>(インストーラ内で「C++ によるデスクトップ開発」を追加インストール) |

## 1.2 Web 向けビルドに必要な Emscripten 関連ツールのインストール

1. https://github.com/nokotan/EmscriptenInstaller/releases/tag/v0.0.3 から Emscripten.exe をダウンロードして実行します
2. 実行時に「Windows によって PC が保護されました」と表示された場合は、**詳細情報** を押して **実行** を押します

:::details インストーラが自動的に行うこと
- Emscripten 2.0.22 のインストール
- Clang Compiler Front End のインストール
- Node のインストール
- Python のインストール
- アンインストーラの登録
:::

:::details インストールした Emscripten 関連ツールを削除するには
通常のアプリケーションと同様、Windows の「設定」→「アプリ」→「アプリと機能」からアンインストールします。
:::

## 1.3 Visual Studio 拡張機能のインストール

1. Visual Studio Market Place から [Emscripten Extension Pack for Visual Studio](https://marketplace.visualstudio.com/items?itemName=KamenokoSoft.emscripten-extensions) をダウンロードして実行します
2. インストールする項目のすべてにチェックを付け、**Modify** を押します  
![](/images/doc_v6/manual/web_vsix.png =400x)
3. 一部の項目のインストールでエラーが表示されることがありますが、無視して構いません

::: message
インストーラは Visual Studio のメニュー「拡張機能」→「拡張機能の管理」を開き、「Emscripten Extension Pack for Visual Studio」で検索して入手することもできます。
:::

## 1.4 SDK のインストール

1. OpenSiv3D for Web インストーラを [OpenSiv3D for Web ダウンロードページ](https://siv3d.kamenokosoft.com/ja/download) からダウンロードして実行します
2. 実行時に「Windows によって PC が保護されました」と表示された場合は、**詳細情報** を押して **実行** を押します

## 1.5 Web 向け Siv3D アプリのビルド

1. Visual Studio のスタート画面で **新しいプロジェクトの作成** をクリックします
1. プロジェクト テンプレートのリストから **OpenSiv3D Web** を選択し、**次へ** を押します
1. プロジェクト名と保存場所を入力し（任意）、**作成** を押します
1. サンプルプログラム (Main.cpp) が最初から用意されています
1. **ビルド** メニューからプロジェクトをビルドします（初回はビルドに失敗することがあります。その場合は再度ビルドします）
1. **デバッグ** メニューの **デバッグの開始** でビルドしたプログラムを実行します
1. Web ブラウザが起動して、Siv3D の Web アプリケーションが実行されます

::: message
ここまでの手順で失敗する場合は、OpenSiv3D for Web[「うまくいかないときは (Visual Studio)」](https://siv3d.kamenokosoft.com/ja/building/trouble-shooting) を参照してください。
:::

## 1.6 ファイルサイズの削減とその他の注意事項

1. デフォルトでは、`engine\` と `example\` のすべてのファイルが最終出力に同梱されるため、Web アプリケーションに関連するファイルのサイズは Release ビルドで合計数十 MB 程度になります。そうした状態のアプリケーションを Web で公開すると、アクセスした利用者がファイルのダウンロードに時間がかかってしまいます。実際にアプリケーションを公開する際は、不要なファイルを削除する必要があります（参考: [チュートリアル 41 | アプリの公開](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/tutorial-release#41.9-%E5%90%8C%E6%A2%B1%E3%81%99%E3%82%8B%E5%BF%85%E8%A6%81%E3%81%8C%E7%84%A1%E3%81%84%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB))。Web 版の出力ファイルのサイズは、**最小で数 MB 程度** まで削減することができます
1. `engine\` と `example\` 以外のフォルダを同梱対象にする方法は OpenSiv3D for Web[「Web 固有の注意点」](https://siv3d.kamenokosoft.com/ja/building/web-specific-notes) を参照してください
1. シーンのリサイズモードはデフォルトで `ResizeMode::Virtual` であるため、ブラウザの拡大縮小に応じてシーンのサイズが変化します。これを防ぐには `Scene::SetResizeMode(ResizeMode::Keep);` でシーンサイズを固定します
1. 通常の Siv3D と Web 版の差異については OpenSiv3D for Web[「Web 固有の注意点」](https://siv3d.kamenokosoft.com/ja/building/web-specific-notes) を参照してください



# 2. Visual Studio Code で Web 向け開発を始める (Windows / macOS / Linux)

[OpenSiv3D for Web](https://siv3d.kamenokosoft.com/ja/index) の「はじめる - Visual Studio Code」を参照してください。


