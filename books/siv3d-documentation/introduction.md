---
title: "イントロダクション"
free: true
---

![](/images/doc_v6/logo.png)

# 1. Siv3D について
**Siv3D** (シブスリーディー) ^[2016 年まで開発されていた旧世代 Siv3D と区別して OpenSiv3D (オープンシブスリーディー) と呼ぶこともあります。]は、2D / 3D ゲーム、メディアアート、ビジュアライザ、シミュレータなど、可視化やインタラクションに関わるプログラムを、**非常に短い、楽しく簡単なコード**で記述できる**モダンな C++ フレームワーク**です。**MIT ライセンス**で配布され、2021 年 8 月時点における直近 1 年間の SDK ダウンロード数は約 1 万回です。動作プラットフォームは **Windows / macOS / Linux / Web** です。

![](https://raw.githubusercontent.com/Reputeless/Zenn.Public/main/images/doc_v6/siv3d-kun/0.png)
*マスコットキャラクターの Siv3D くん*

## 1.1 機能の概要
Siv3D を導入することで、次のような操作を組み合わせたアプリケーションを非常に短いコードで記述できます。

- 図形や画像、テキスト、動画、3Dモデルなど、グラフィックスの描画 (Direct3D 11 / OpenGL 4.1 / WebGL 2.0)
- マウスやキーボード、Webカメラ、マイク、ゲームパッドなど、ヒューマンインタフェースデバイス (HID) の使用
- ウィンドウ処理、ファイルシステム、ネットワーク
- 画像処理や音声処理
- 物理演算や経路探索、幾何などの計算
- データ構造やアルゴリズム

Siv3D は標準的な C++ で書かれていますが、ユーザはほとんどのプログラムを、Siv3D が提供する便利な型や関数を使って記述します。そのため、描画やインタラクションのための DSL (domain-specific language) としての性格が強いです。例えると Java にとっての [Processing](https://processing.org/) です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	const Texture food{ U"🍿"_emoji };
	const Texture chick{ U"🐥"_emoji };

	while (System::Update())
	{
		Circle{ Scene::Center(), 100 }.draw();
		food.drawAt(Scene::Center());
		chick.drawAt(Cursor::Pos());
	}
}
```

![](/images/doc_v6/demo/chick.gif)

[Unity](https://unity.com/ja) のようなゲームエンジンとの一番の違いは、Siv3D はビジュアルツールを持たず、すべてコードで完結するということです（Siv3D で独自のビジュアルツールを組むことはできます）。

## 1.2 Siv3D のここが最高

### ⚡ 非常に短いコード
Siv3D のコードは最短で 2 行です。様々なインタラクションを実現する便利な機能が揃っているため、アプリケーションのほとんどは 1 つの .cpp ファイルだけで完成します。思いついたアイデアや成果物のソースコードを、[GitHub Gist](https://gist.github.com/) などのコード共有サイトを使って手軽に保存・シェアできるため、世界中の Siv3D ユーザと技術を学び合うことができます。

### 🛸 最新の C++ を学べる
Siv3D のサンプルコードとライブラリ API は、すべて最新の C++20 を活用して書かれています。Siv3D を使っているだけで、モダンな C++ の書き方やテクニックを自然と身につけることができます。Siv3D の作者は、日本最大のゲーム開発カンファレンス CEDEC で[最新 C++ の活用に関する講演](https://speakerdeck.com/cpp/cedec2020)をしたり、[C++ の情報ポータル](https://cppmap.github.io/)を作成したりするなど、最先端の C++ の普及に努めています。

### 🏬 豊富な機能を Siv3D API に統合
画像処理、音楽再生、物理演算、テキストファイル読み込み、シリアル通信・・・。たくさんの処理のために、ライブラリを探し回り、ドキュメントを読んで使い方を調べ、API の要求に従ってデータ形式を変換するコードをひたすら書く必要はもうありません。Siv3D は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアが実現する、可視化やインタラクションの様々な機能を、一貫した API で提供します。Siv3D のルールを覚えるだけで、ありとあらゆる機能が思いのままです。

### ⛰️ オープンソース
Siv3D は MIT ライセンスのもと [GitHub 上でホスティング](https://github.com/Siv3D/OpenSiv3D)されています。内部のコードが気になったらいつでも調べることができ、改造することもできます。サードパーティ・ライブラリを含め商用利用を妨げる条件はありません。Siv3D で開発した Windows, macOS, Linux, Web 向けのゲームやアプリケーションを販売して得た収益は 100% 開発者が獲得できます。Siv3D のビジョンに共感し、より優れた開発体験を心待ちにしている方は [Siv3D の個人スポンサー](https://github.com/sponsors/Reputeless)として Siv3D プロジェクトを応援してください。

### 🐦 軽量・迅速
Windows 版の OpenSiv3D SDK のインストーラのサイズは 200 MB 未満です。数十秒でダウンロードが終わり、インストールはわずかなクリックで自動的に終わります。Visual Studio を起動すると、メニューには Siv3D プロジェクトを作成するアイテムが追加済みです。あっという間に、楽しい C++ プログラミングの世界への入り口があなたのパソコンにセットアップされます。アンインストールしたいときは、通常のアプリケーションと同様に OS の設定からワンクリックです。

### 💗 親切なユーザコミュニティ
Siv3D を使って困ったことがあったら、[Siv3D ユーザコミュニティ Slack](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/community) で質問しましょう。匿名で質問したい場合は BBS も利用できます。毎月オンラインで開催される Siv3D 実装会では、Siv3D の熱心なユーザ達や Siv3D の作者と、Discord 上で雑談や技術的な相談をすることができます。Twitter では定期的に #Siv3D, #OpenSiv3D ハッシュタグを巡回しています。ユーザコミュニティが、作品の宣伝やシェアに協力してくれるでしょう。OSS (オープンソースソフトウェア) 開発に貢献したい学生に対して、Siv3D を練習場にしてサポートするプログラムも毎年実施しています。

### 🌐 Web ブラウザ上で動く（試験的）
現在試験的に提供される Web 版 ([OpenSiv3D for Web](https://siv3d.kamenokosoft.com/ja/index)) を使うと、Siv3D で作った C++ アプリケーションを、WebGL2 をサポートするモダンな Web ブラウザ上で実行可能なプログラムに変換できます。スマホやタブレットから、多くの人があたなの作品を体験できます。


## 1.3 Siv3D の制約

### 最新の C++ コンパイラが必要
Siv3D は最新の C++ を活用するため、モダンな C++ コンパイラが必要です。Windows の場合は無料で利用できる [Visual Studio 2019 コミュニティの最新版](https://visualstudio.microsoft.com/ja/downloads/)、macOS では最新に近い世代の Xcode が必須の開発環境となっています。Windows XP や 32-bit OS などの古い実行環境では利用できません。

### すでに書かれた C++ プログラムに後付けできない
多数のサブシステムを協調させて制御するために、Siv3D はプログラムのエントリーポイント (`int main()`, Windows の場合 `WinMain()`) やウィンドウハンドルなどを独自に管理します。そのため、既存の C++ アプリケーションのプログラムにあとから Siv3D を乗せることはできません。主従関係を逆にして、Siv3D のプログラムに対して、追加のプログラムやライブラリを乗せるようにしましょう。

### まだ完全に API が安定していない
OpenSiv3D はバージョン 0.0.1 から始まり、4 年かけて 0.6.0 までたどり着きました。95% 以上の API は理想形に達していますが、まだ改善の余地のある機能も残っています。将来登場する新しいバージョンの Siv3D に乗り換えるときに一部のコードを修正する手間が発生するかもしれません。2022 年には API 安定版をリリースすることを目標としています。

### GUI 環境が必要（解消予定）
Siv3D で作られたアプリケーションの実行には、ウィンドウを表示できる GUI 環境が必要です。GUI 環境でなくても実行できる headless モードが現在試験的に存在します。

## 1.4 ユーザ事例 (2021)

最近 Twitter に投稿された Siv3D 関連のツイートを紹介します。

https://twitter.com/e869120/status/1425458983154831363

https://twitter.com/Ryoga_exe/status/1429748042169602049

https://twitter.com/takara2314/status/1415649264085069829

https://twitter.com/e869120/status/1428662453785612290

https://twitter.com/agehama_/status/1410563076869394433

https://twitter.com/Ryoga_exe/status/1401098907195559938

https://twitter.com/G4rryS1ngh/status/1394752543364763648

https://twitter.com/azaika_/status/1393421628403380224

https://twitter.com/MASAYA_math/status/1391575153708920832

https://twitter.com/itakawa/status/1389944433177546757

https://twitter.com/Ryoga_exe/status/1388820367922188289

https://twitter.com/discosaan/status/1387061728974839809

https://twitter.com/r193333333/status/1377613363929247746

https://twitter.com/awotere7/status/1363471771836567559

https://twitter.com/agehama_/status/1346062702691565569

https://twitter.com/Shibaken_8128/status/1344967321454931969

## 1.5 ゲーム作品

Siv3D で開発された人気のゲームを 3 つピックアップします。

- [One week, My room](https://www.freem.ne.jp/win/game/14961)
  - [レビュー](http://www.moguragames.com/entry/12858/)
- [ColorfulTone](https://www.freem.ne.jp/win/game/14167)
- [CORGI JUMP](https://voidproc.itch.io/corgi-jump)

---

次のページでは、Siv3D で開発を始めるのに必要な環境を説明します。
