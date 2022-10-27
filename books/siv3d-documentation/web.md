---
title: "Web 向けの開発"
free: true
---

Siv3D Web 版 (experimental) を利用すると、Web ブラウザで動作可能な Siv3D アプリケーションを C++ だけで開発できます。Web 版はいくつか注意点があるため、Siv3D の使用に慣れた中級者以上を対象としています。利用にあたって困ったことがあれば Siv3D Discord サーバの `#web` チャンネルで質問してください。

## 1.1 セットアップガイド
[OpenSiv3D for Web](https://siv3d.kamenokosoft.com/ja/index) を参照してください。初回のビルドではエラーメッセージが表示されることがありますが、もう一度ビルドすると正常にビルドできます。

## 1.2 Web 版の出力ファイルサイズの削減とその他の注意事項
1. Web 版のビルドでは、デフォルトで `engine/` と `example/` のすべてのファイルを最終出力に同梱するため、最終出力ファイルのサイズは Release ビルドでも合計数十 MB と大きくなります。そうしたアプリケーションを Web で公開すると、アクセスした利用者がファイルのダウンロードに時間がかかってしまうため、実際にアプリケーションを公開する際は、不要なファイルを削除する必要があります（参考: [チュートリアル 41 | アプリの公開](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/tutorial-release#41.9-%E5%90%8C%E6%A2%B1%E3%81%99%E3%82%8B%E5%BF%85%E8%A6%81%E3%81%8C%E7%84%A1%E3%81%84%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB))。また、Emscripten リンカの設定において「追加の依存ファイル」から不要なライブラリを削除することで、Web 版の出力ファイルのサイズは、**最小で数 MB 程度** までコンパクトにできます。詳しくは  Siv3D Discord サーバの `#web` チャンネルでご相談ください
1. `engine/` と `example/` 以外のフォルダを同梱対象にする方法は OpenSiv3D for Web[「Web 固有の注意点」](https://siv3d.kamenokosoft.com/ja/trouble-shooting/web-specific-notes) を参照してください
1. Web 版のシーンのリサイズモードはデフォルトで `ResizeMode::Virtual` であるため、ブラウザの拡大縮小に応じてシーンのサイズが変化します。これを防ぐには `Scene::SetResizeMode(ResizeMode::Keep);` と `Scene::Resize(width, height);` でシーンサイズを固定します
1. 上記以外の Web 版固有の注意点については OpenSiv3D for Web[「Web 固有の注意点」](https://siv3d.kamenokosoft.com/ja/trouble-shooting/web-specific-notes) を参照してください

