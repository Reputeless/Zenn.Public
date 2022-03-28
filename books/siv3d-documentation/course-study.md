---
title: "Siv3D 勉強会コース"
free: true
---

# 0. はじめに

### 勉強会の基本事項
- カメラはオフで参加して問題ありません
- 勉強会の Twitter ハッシュタグは [#Siv3D勉強会](https://twitter.com/hashtag/Siv3D%E5%8B%89%E5%BC%B7%E4%BC%9A?src=hashtag_click&f=live) です
- 運営のトラブルで万が一配信が中断された場合、少し時間をおいて再アクセスしてみてください
- 配信のチャット欄を使って質問やコメントができます
- ほかの参加者を不快にさせる言動をした場合は配信からブロックします

### 勉強会開始前・休憩時間中に遊べる Siv3D プログラム

勉強会開始まで、次のプログラムで遊んでいます。

- [遊べるサンプルプログラム](course-study-sample)
  - インストールの手順の最後に作ったプログラムを、別のプログラムで上書きして試せます
  - `# include <Siv3D.hpp>`～ で始まるコードを上書きするだけで OK です
  - 記事中のサンプルコードの右上をクリックするとソースコードをコピーできて便利です


### イントロダクション

- スライド: [Siv3D / C++ イントロダクション](https://www.dropbox.com/s/0swkt5zd4iezi5b/introduction.pdf?dl=0)

### Siv3D を使う 7 つの理由

####  1. ⚡ 非常に短いコード
Siv3D のコードは最短で 2 行です。描画やインタラクションを実現するための便利な関数とクラスが揃っているため、アプリケーションのほとんどは 1 つの .cpp ファイルだけで完成します。あなたのアイデアを、[GitHub Gist](https://gist.github.com/) などのコード共有サイトを使って手軽に保存・シェアして、世界中の Siv3D ユーザと技術を交換し学び合いましょう。

#### 2. 🛸 最新の C++
Siv3D のサンプルとライブラリ API は、最新の C++20 スタイルで書かれているため、Siv3D を使っているだけで、モダンな C++ の書き方やテクニックが身に付きます。Siv3D の作者は、日本最大のゲーム開発カンファレンス CEDEC で [最新 C++ の活用に関する講演](https://speakerdeck.com/cpp/cedec2020) をしたり、[C++ の情報ポータル](https://cppmap.github.io/) を作成したりするなど、最先端の C++ の普及に努めています。

#### 3. 🏬 一貫した API
Siv3D は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアが実現する、可視化やインタラクションのための様々な機能を、一貫した API で提供します。Siv3D のルールを覚えるだけで、ありとあらゆる機能が思いのままです。

#### 4. ⛰️ オープンソース
Siv3D は MIT ライセンスのもと [GitHub 上で開発](https://github.com/Siv3D/OpenSiv3D) されているため、いつでも内部のコードを調べたり改造したりできます。サードパーティ・ライブラリを含め、商用利用を妨げる条件は無く、開発したゲームやアプリケーションの収益は 100% 開発者が獲得できます。

#### 5. 🛩️ 軽量・迅速
Windows 版の OpenSiv3D SDK のインストーラのサイズは 200 MB 未満です。インストールはわずかなクリックで自動的に終わり、Visual Studio を起動すると、メニューには Siv3D プロジェクトを作成するアイテムが追加されていて、すぐにプログラミングを始められます。

#### 6. 💗 ユーザコミュニティ
Siv3D で困ったことがあれば、[オンラインコミュニティ](community) が役に立ちます。毎月開催されるイベントで、熱心なユーザや Siv3D の作者と交流できます。オープンソースソフトウェア開発に貢献したい学生には、Siv3D を練習場にしたサポートプログラムを毎年実施しています。

#### 7. 🌐 Web ブラウザで動く
現在試験的に提供される Web 版を使うと、Siv3D で作った C++ アプリケーションを、Web ブラウザ上で実行可能なプログラムに変換できます。世界中の人がスマホやタブレットからあたなの作品を体験できます。

#### Web ブラウザで動いている Siv3D プログラムの例
ツイート内のリンクをクリックすると、Web ブラウザで動く Siv3D プログラムが開きます

https://twitter.com/Reputeless/status/1451829140332560389

https://twitter.com/Reputeless/status/1499360860632141824

https://twitter.com/Reputeless/status/1445377010197426180


### Siv3D の機能

- グラフィックス（図形や画像、テキスト、アイコン、動画、3Dモデルなど）
- オーディオ（BGM や効果音、テキスト読み上げなど）
- 入力デバイス（マウスやキーボード、Webカメラ、マイク、ゲームパッドなど）
- ウィンドウ、ファイルシステム、ネットワーク
- 画像処理、音声処理、物理演算、経路探索、幾何などの計算

これらをサポートする API を組み合わせて、2D / 3D ゲーム、メディアアート、ビジュアライザ、シミュレータなどのアプリケーションを効率的に開発できます。

::: details 詳細な機能一覧
## グラフィックス
- 発展的な 2D グラフィックス
- 基本的な3Dグラフィックス (Wavefront OBJ, 基本形状)
- カスタム頂点・ピクセルシェーダ (HLSL, GLSL)
- テキストレンダリング (Bitmap, SDF, MSDF)
- 画像形式 (PNG, JPEG, BMP, SVG, GIF, Animated GIF, TGA, PPM, WebP, TIFF)
- Unicode 14.0 emojis と 7,000種類以上のアイコン
- 画像処理
- ビデオレンダリング

## オーディオ
- 音声形式 (WAVE, MP3, AAC, OggVorbis, Opus, MIDI, WMA, FLAC, AIFF)
- 音量やパン，スピード，ピッチの調整
- ストリーミング再生
- フェードイン，フェードアウト
- ループ
- ミキシングバス
- フィルタ処理 (ローパスフィルタ，ハイパスフィルタ, エコー, リバーブ)
- FFT
- サウンドフォントレンダリング
- テキスト読み上げ

## 入力デバイス
- マウス
- キーボード
- ゲームパッド
- ウェブカメラ
- マイク
- Joy-Con / Pro Controller
- XInput ゲームパッド
- ペンタブレット
- Leap Motion

## ウィンドウ
- フルスクリーンモード
- 高 DPI サポート
- ウィンドウのスタイル
- ファイルダイアログ
- ドラッグ & ドロップ
- メッセージボックス
- トースト通知

## ネットワークと通信
- HTTP クライアント
- TCP 通信
- シリアル通信
- プロセス間通信 (pipe)

## 数学
- ベクトルと行列クラス (`Point`, `Float2`, `Vec2`, `Float3`, `Vec3`, `Float4`, `Vec4`, `Mat3x2`, `Mat3x3`, `Mat4x4`, `SIMD_Float4`, `Quaternion`)
- 2D 形状クラス (`Line`, `Circle`, `Ellipse`, `Rect`, `RectF`, `Triangle`, `Quad`, `RoundRect`, `Polygon`, `MultiPolygon`, `LineString`, `Spline2D`, `Bezier2`, `Bezier3`)
- 3D  形状クラス (`Plane`, `InfinitePlane`, `Sphere`, `Box`, `OrientedBox`, `Ray`, `Line3D`, `Triangle3D`, `ViewFrustum`, `Disc`, `Cylinder`, `Cone`)
- 色クラス (`Color`, `ColorF`, `HSV`)
- 曲座標系クラス
- 2D / 3D 幾何計算
- Rectangle packing
- 平面細分割
- 色空間
- 疑似乱数生成器
- 補間，イージング，スムージング
- パーリンノイズ
- 数式パーサ
- ナビメッシュ
- 拡張数値型 (`HalfFloat`, `int128`, `uint128`, `BigInt`, `BigFloat`)

## 文字列処理
- 文字列クラス (`String`, `StringView`)
- Unicode 変換
- 正規表現
- 文字列フォーマット
- テキストファイル読み書き
- CSV / INI / JSON / XML / TOML パーサ
- CSV / INI / JSON 出力

## その他
- 基本的なGUI (ボタン，スライダー，ラジオボタン，チェックボックス，テキストボックス，リストボックス，カラーピッカー)
- 2D 物理エンジン (Box2D)
- 配列クラス (`Array`, `Grid`)
- Kd-tree
- 非同期ファイルロード
- データ圧縮 (zlib, Zstandard)
- シーン遷移
- ファイルシステム
- ディレクトリ監視
- QR コード
- GeoJSON
- 日付と時刻
- 時間計測
- ロギング
- シリアライズ
- UUID
- 子プロセス管理
- クリップボード
- 電源管理
- スクリプティング (AngelScript)
:::

::: details 来月のアップデート (v0.6.4) で追加されるおもな機能
- 再生中のオーディオバッファへのリアルタイム波形書き込み
- Union-Find 木（アルゴリズムとデータ構造）
- 最新の Xcode 13.3 対応
- マルチプレイヤーゲームを作りやすくする機能 (Photon というサービスを利用)
:::


### お役立ちリンク

- [Siv3D リファレンス](https://zenn.dev/reputeless/books/siv3d-documentation)
  - リファレンスのトップページ
- [Siv3D のソースコード (GitHub)](https://github.com/Siv3D/OpenSiv3D)
  - `Siv3D/include/` がヘッダ
  - `Siv3D/src/` がソース
- [Siv3D ゲーム典型](https://github.com/Reputeless/games)
  - ゲームサンプルを掲載していくプロジェクト（100 個を目標に、先月からスタート）
- [Twitter #Siv3D #OpenSiv3D タグ検索](https://twitter.com/search?q=Siv3D%20OR%20OpenSiv3D&src=typed_query&f=live)

# 1. Siv3D プログラムの基本構造

## 1.1 インクルードするヘッダ
Siv3D のプログラムを書くときは、いつも `<Siv3D.hpp>` ヘッダをインクルードします。
```cpp
# include <Siv3D.hpp>
```

これだけで、Siv3D のすべての機能を使ってプログラムを書けるようになります。  

C++ の経験者であれば、ほかにも `<iostream>` や `<vector>` などの C++ 標準ライブラリヘッダをインクルードしたくなるかもしれませんが、その必要はありません。すでに `<Siv3D.hpp>` の中で、Siv3D のプログラミングでよく使われる主要な C++ 標準ライブラリヘッダがインクルードされているためです。また、標準ライブラリの機能の多くが、より便利な Siv3D の関数やクラスで再実装されているため、Siv3D の学習の序盤で C++ 標準ライブラリの関数を使うことはめったにありません。


## 1.2 エントリーポイント
通常、C++ のプログラムのエントリーポイントは `int main()` です。しかし、Siv3D ではこの `main()` 関数はエンジン内部ですでに実装されていて、次のようにユーザの見えないところで、ウィンドウやグラフィックスの初期化処理を行うプログラムがすでに用意されています。
```cpp
// 説明のため簡略化したコード
int main()
{
	Siv3D の初期化...

	Main(); // この関数をユーザがプログラムする

	Siv3D の終了処理...
}
```
ユーザが実装するプログラムは、このプログラムの `void Main()` 関数です。

`Main()` が実行される前に初期化処理が完了しているため、ユーザは `Main()` の中で最初から Siv3D の機能を使うことができ、`Main()` の実行が終了したら、ウィンドウの後片付けなどサブシステムの終了処理を Siv3D が自動的に行ってくれます。


## 1.3 最小限のプログラム
これが Siv3D の最小のプログラムです。
```cpp
# include <Siv3D.hpp>

void Main()
{

}
```

ただし、この `Main()` は一瞬で終了してしまうので、このプログラムを実行しても、何も起こっていないように見えるでしょう。


## 1.4 ウィンドウを表示し続ける
プログラムがすぐに終了してしまっては、ユーザとインタラクションをするアプリケーションが作れません。`Main()` がずっと続くように **メインループ** を実装します。次のプログラムを実行するとウィンドウが表示され続けます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	// メインループ
	while (System::Update())
	{
		// ここに書いた内容が毎フレーム実行される
	}
}
```
`while` 文によってプログラムが半永久的に続くようになります。くり返しのたびに `System::Update()` がウィンドウの表示や音楽の再生、マウスやキーボードの入力情報などを更新するので、グラフィックスの表示やユーザとのやり取りをプログラムできるようになります。

`System::Update()` は普段は `true` を返すため、メインループはいつまでも続きますが、ウィンドウが閉じられたり、エスケープキーが押されたりするなど、アプリケーションを終了させる特別な **ユーザアクション** が実行されると、以降はずっと `false` を返すようになります。 アプリケーションはこの状態になったら速やかにメインループから抜け、`Main()` を終了させる必要があります。上記のプログラムのように書いていれば、自然にこの通りに動作します。

デフォルトでは次の 3 つのユーザアクションが、アプリケーションを終了させる操作として設定されています

- ウィンドウを閉じる
- エスケープキーを押す
- `System::Exit()` を呼ぶ

メインループは、特に指定しない限り、使用しているモニターのリフレッシュレートと同じ速度で繰り返されます。一般的なモニタのリフレッシュレートは毎秒 60, 120, または 144 フレームです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// ウィンドウタイトルに直近のフレームレートを表示
		Window::SetTitle(Profiler::FPS());
	}
}
```


## 1.5 デバッグ表示
画面にメッセージを表示してみましょう。`Print` に向かって、出力の記号 `<<` でテキストを送ると、そのテキストが画面に表示されます。
![](/images/doc_v6/tutorial/1/1.5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"Hello, Siv3D!";

	while (System::Update())
	{
		
	}
}
```

テキストをプログラムに登場させるときは `U" "` で囲みます。Siv3D では UTF-32 という、日本語を扱いやすい形式でテキストデータを扱うため、ダブルクォーテーション `"` の先頭には常に `U` というプレフィックスを付け、`U"Hello"` のようにします。


## 1.6 さまざまな値の表示
Siv3D で提供される型のほとんどは `Print` で内容を表示できます。
![](/images/doc_v6/tutorial/1/1.6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 文字列リテラル
	Print << U"Hello, Siv3D!";

	// << の連続
	Print << U"This" << U" is " << U"fun!";

	// 文字リテラル
	Print << U'あ';

	// 整数
	Print << 100 * 5 + 5;
	
	// 浮動小数点数
	Print << U"π = " << Math::Pi;

	// bool
	Print << Math::IsPrime(65537);

	// 時間型
	Print << 1h + 3s;

	// 数式パーサの結果 (double 型）
	Print << Eval(U"10^3 + sqrt(81) + 0.001");

	// 範囲
	Print << Range(0, 10);

	// 日付と時刻クラス
	Print << DateTime::Now();

	while (System::Update())
	{

	}
}
```


## 1.7 デバッグ表示のあふれ
次のように `Print` をメインループの中で使うと、毎フレーム新しいメッセージが追加され、古いメッセージは画面の外に追いやられます。画面外に出た古いメッセージは自動的に消去されます。
![](/images/doc_v6/tutorial/1/1.7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		Print << count;

		++count;
	}
}
```


## 1.8 デバッグ表示の消去
`ClearPrint()` を使うと、`Print` でデバッグ表示した内容を即座に消去します。メインループの先頭で `ClearPrint()` すると、現在のフレームで `Print` した内容だけが表示されるようになります。
![](/images/doc_v6/tutorial/1/1.8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		// Print したメッセージを消去
		ClearPrint();

		Print << count;

		++count;
	}
}
```


## 1.9 基本的なデータ型
Siv3D でよく使う基本的なデータ型は次のとおりです。

Siv3D で整数を扱うときは、`int` や `unsigned long long` のような標準の型名の代わりに、`int32` や `uint64` のように明示的なサイズを持つ型名を使います。これらの型名を使うことで、プラットフォーム間での移植性が高まり、一貫性のある読みやすいコードになります。

|型名|説明|
|:--:|--|
|`bool`|ブーリアン型 (`false` または `true`)|
|`int8`|符号付き 8-bit 整数型 (-128 ～ 127)|
|`uint8`|符号無し 8-bit 整数型 (0 ～ 255)|
|`int16`|符号付き 16-bit 整数型 (-32,768 ～ 32,767)|
|`uint16`|符号無し 16-bit 整数型 (0 ～ 65,535)|
|`int32`|符号付き 32-bit 整数型 (-2,147,483,648 ～ 2,147,483,647)|
|`uint32`|符号無し 32-bit 整数型 (0 ～ 4,294,967,295)|
|`int64`|符号付き 64-bit 整数型 (-9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807)|
|`uint64`|符号無し 64-bit 整数型 (0 ～ 18,446,744,073,709,551,615)|
|`int128`|符号付き 128-bit 整数型|
|`uint128`|符号無し 128-bit 整数型|
|`size_t`|オブジェクトのサイズを表現する符号なし 64-bit 整数型 (0 ～ 18,446,744,073,709,551,615)|
|`BigInt`|任意精度多倍長整数型|
|`HalfFloat`|半精度浮動小数点数型|
|`float`|単精度浮動小数点数型|
|`double`|倍精度浮動小数点数型|
|`BigFloat`|有効数字100桁の浮動小数点数型|
|`Byte`|ビット列としてのバイトデータを表す型|
|`char32`|UTF-32 のコードポイント（文字）|
|`String`|文字列クラス (C++ の `std::u32string` 相当)|
|`std::array<Type, N>`|固定長配列|
|`Array<Type>`|動的配列 (C++ の `std::vector<Type>` 相当)|
|`Grid<Type>`|二次元配列|
|`Optional<Type>`|無効値を取りうる型 (C++ の `std::optional<Type>` 相当)|
|`HashSet<Key>`|ハッシュセット (C++ の `std::unordered_set<Key>` 相当)|
|`HashTable<Key, Value>`|ハッシュテーブル (C++ の `std::unordered_map<Key, Value>` 相当)|
|`FilePath`|`String` のエイリアス|
|`StringView`|所有権を持たない文字列クラス (C++ の `std::string_view` 相当)|
|`FilePathView`|`StringView` のエイリアス|

![](/images/doc_v6/tutorial/1/1.9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	bool a = true;
	int32 b = 123;
	double c = 0.5;
	BigInt d = 111222333444555666777888999000_big;
	BigFloat e = 0.1234567890123456789_bigF;
	Byte f{ 0xF7 };
	char32 g = U'あ';
	String h = U"Hello!";
	Array<int32> i = { 10, 20, 30, 40 };
	Array<String> j = { U"aaa", U"bbb" };
	HalfFloat k = 3.333333f;

	Print << a;
	Print << b;
	Print << c;
	Print << d;
	Print << e;
	Print << f;
	Print << g;
	Print << h;
	Print << i << U" : " << i.size();
	Print << j << U" : " << j.size();
	Print << k << U" : " << sizeof(k);

	while (System::Update())
	{

	}
}
```


## 1.10 `Print` 以外の出力
`Print` 以外にも、コンソール出力 `Console`, ログ出力 `Logger`, 音声読み上げ出力 `Say` を使えます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	// コンソール画面に出力
	Console << U"Hello, Console!";

	// ログに出力 (Visual Studio の場合「出力」ウィンドウ）
	Logger << U"Hello, Logger!";

	// 必要に応じて読み上げ言語を設定（OS に読み上げエンジンがインストールされていない場合は使えない）
	TextToSpeech::SetDefaultLanguage(LanguageCode::EnglishUS);
	//TextToSpeech::SetDefaultLanguage(LanguageCode::Japanese);

	// 音声読み上げ出力
	Say << U"Hello, Say!";

	while (System::Update())
	{

	}
}
```


## 1.11 画面の座標系
ウィンドウ内の黒い部分が **画面（シーン）** で、Siv3D はこの領域に文字や図形、画像を表示できます。

画面のサイズは、基本の状態では **幅 800 ピクセル、高さ 600 ピクセル** です。画面上の位置を表す座標系は、一番左上のピクセルを「X 座標 0」「Y 座標 0」を表す `(0, 0)` と表記し、右に進むと X 座標が大きく、下に進むと Y 座標が大きくなります。画面の一番右下のピクセルの座標は `(799, 599)` です。 

`Cursor::Pos()` を使うと、現在のマウスカーソルの座標を `Point` 型で取得できます。`Point` 型の値は X 座標を表す `int32 x` と Y 座標を表す `int32 y` の 2 つの成分を持っています。`Point` 型の値をそのまま丸ごと `Print` に送って表示することもできます。
![](/images/doc_v6/tutorial/1/1.11.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		Print << Cursor::Pos(); // 現在のマウスカーソル座標を表示

		Print << U"X: " << Cursor::Pos().x; // X 座標だけを表示

		Print << U"Y: " << Cursor::Pos().y; // Y 座標だけを表示
	}
}
```


# 2. 図形を描く

## 2.1 円を描く
シーンに図形を描く方法を学びましょう。Siv3D では、図形オブジェクトを作成し、その `draw()` メンバ関数を呼んで描画を行います。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。
![](/images/doc_v6/tutorial/2/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心座標 (400, 300), 半径 20 の円を描く
		Circle{ 400, 300, 20 }.draw();
	}
}
```


## 2.2 円の大きさを変える
`Circle{}` の最後に指定するパラメータは円の半径です。この値を大きくすれば、描画される円も大きくなります。
![](/images/doc_v6/tutorial/2/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心座標 (400, 300), 半径 100 の円を描く
		Circle{ 400, 300, 100 }.draw();
	}
}
```


## 2.3 X 座標がマウスカーソルと連動する円を描く
円がマウスカーソルの座標に連動して動くようにしてみましょう。`Circle{}` の最初に指定するパラメータは円の中心の X 座標です。この値をマウスカーソルの X 座標にしてみます。
![](/images/doc_v6/tutorial/2/3.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心座標 (マウスの X 座標, 300), 半径 100 の円を描く
		Circle{ Cursor::Pos().x, 300, 100 }.draw();
	}
}
```

前章で、`Print` を使って表示したメッセージはいつまでも画面に残りましたが、それは例外的なルールです。通常、`Print` 以外のすべての描画は `System::Update()` のたびに背景の色でリセットされます。


## 2.4 マウスカーソルと連動する円を描く
Siv3D で X 座標、Y 座標 2 つの値を受け取る関数は、多くの場合、1 つの `Point` 型、もしくは `Vec2` 型を受け取る別バージョンの関数（オーバーロード）を提供しているケースがよくあります。`Circle` も、「X 座標」「Y 座標」「半径」の 3 つの引数ではなく、「中心座標 (`Vec2` 型)」「半径」の 2 つの引数を受け取るオーバーロードがあります。これを使って円をマウスカーソルと連動させます。
![](/images/doc_v6/tutorial/2/4.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心座標 (マウスの X 座標, マウスの Y 座標), 半径 100 の円を描く
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```


## 2.5 色を付ける
図形に色を付けたいときは `draw()` 関数に色を渡します。色の指定の方法は大きく 4 通りあります。

| 色の表現               | 値の範囲                                                                                                  |
|--------------------|-------------------------------------------------------------------------------------------------------|
| Palette::色名        | [Web カラー](https://www.w3.org/wiki/CSS/Properties/color/keywords) の名前で色を指定  |
| ColorF{ r, g, b, a } | 0.0 - 1.0 の範囲で RGBA の各成分を指定 |
| Color{ r, g, b, a }  | 0 - 255 の整数の範囲で RGBA の各成分を指定 |
| HSV{ h, s, v, a }    | 色相 `h`, 彩度 `s`, 明度 `v` とアルファ値 `a` の各成分を指定。<br>h は 0.0 - 360.0 (370.0 は 10.0 と同じ). s, v, a は 0.0 - 1.0,の範囲 |

**Palette::色名** は、`Palette::Orange`, `Palette::Yellow` のように、RGB 値がわからなくても使えます。

**ColorF** は、Siv3D で最も使われる色の表現形式です。

**Color** は、`Image` 型の要素で、Siv3D で画像処理をするときに使われる形式です。

**HSV** は、赤っぽい、青っぽいなど色の種類を表す色相 (hue) と、色の鮮やかを表す彩度 (saturation), 色の明るさを表す明度 (value) の 3 要素を使った HSV 色空間で色を表現します。

`ColorF`, `Color`, `HSV` はいずれも **アルファ値** `a` を持ちます。アルファ値は「不透明度」を表し、最大値 (`ColorF`, `HSV` の場合 1.0, `Color` の場合 255) ではまったく透過しませんが、値を小さくするとそれに応じて背景を透過する半透明になり、0 になると完全に透明になります。

色の指定はプログラムでよく使われるので、次のような短い書き方も用意されています。例えば `ColorF{ 0.5 }` は `ColorF{ 0.5, 0.5, 0.5, 1.0 }` と同等です。

| 短い書き方           | 意味                         |
|-----------------|----------------------------|
| `ColorF{ r, g, b }` | `ColorF{ r, g, b, 1.0 }`       |
| `ColorF{ rgb, a }`  | `ColorF{ rgb, rgb, rgb, a }`   |
| `ColorF{ rgb }`     | `ColorF{ rgb, rgb, rgb, 1.0 }` |
| `Color{ r, g, b }`  | `Color{ r, g, b, 255 }`        |
| `Color{ rgb, a }`   | `Color{ rgb, rgb, rgb, a }`    |
| `Color{ rgb }`      | `Color{ rgb, rgb, rgb, 255 }`  |
| `HSV{ h, s, v }`    | `HSV{ h, s, v, 1.0 }`          |
| `HSV{ h, a }`       | `HSV{ h, 1.0, 1.0, a }`        |
| `HSV{ h }`          | `HSV{ h, 1.0, 1.0, 1.0 }`      |

色の付いたいくつかの円を描いてみましょう。`.draw()` に色を指定しなかった場合のデフォルトの色は `Palette::White` (`ColorF{ 1.0, 1.0, 1.0, 1.0 }`) です。
![](/images/doc_v6/tutorial/2/5.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 左から順に 7 つの円を描く
		Circle{ 100, 200, 40 }.draw();

		Circle{ 200, 200, 40 }.draw(Palette::Green);

		Circle{ 300, 200, 40 }.draw(Palette::Skyblue);

		Circle{ 400, 200, 40 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 500, 200, 40 }.draw(Color{ 255, 127, 127 });

		Circle{ 600, 200, 40 }.draw(HSV{ 160.0, 1.0, 1.0 });

		Circle{ 700, 200, 40 }.draw(HSV{ 160.0, 0.75, 1.0 });

		// 半透明の円
		Circle{ Cursor::Pos(), 80 }.draw(ColorF{ 0.0, 0.5, 1.0, 0.8 });
	}
}
```


## 2.6 背景の色を変える
シーンの背景色を変えるには `Scene::SetBackground()` に色を渡します。新しい背景色は、それ以降の `System::Update()` で画面の描画内容をリセットするときから反映されます。背景色は一度設定すると、再度変更されるまで同じ設定が使われます。
![](/images/doc_v6/tutorial/2/6.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を ColorF{ 0.3, 0.6, 1.0 } に設定
	Scene::SetBackground(ColorF{ 0.3, 0.6, 1.0 });

	while (System::Update())
	{
		Circle{ Cursor::Pos(), 80 }.draw();
	}
}
```


## 2.7 背景の色を時間の経過とともに変える
`Scene::Time()` はプログラムの経過時間（秒）を `double` 型の値で返します。これを用いて、時間に応じて背景色の色相を変化させてみましょう。
![](/images/doc_v6/tutorial/2/7.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 色相 hue = 経過時間 (秒) * 60
		const double hue = (Scene::Time() * 60);

		Scene::SetBackground(HSV{ hue, 0.6, 1.0 });
	}
}
```


## 2.8 長方形を描く
長方形を描くときは `Rect` を作成して `.draw()` します。
![](/images/doc_v6/tutorial/2/8.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (20, 40) を左上の基準位置にして、幅 400, 高さ 100 の長方形を描く 
		Rect{ 20, 40, 400, 100 }.draw();

		// 座標 (100, 200) を左上の基準位置にして、幅が 80 の正方形を描く 
		Rect{ 100, 200, 80 }.draw(Palette::Orange);

		// 座標 (400, 300) を中心の基準位置にして、幅 80, 高さ 40 の長方形を描く
		Rect{ Arg::center(400, 300), 80, 40 }.draw(Palette::Pink);

		// マウスカーソルの座標を中心の基準位置にして、幅が 100 の正方形を描く 
		Rect{ Arg::center(Cursor::Pos()), 100 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });

		// 座標や大きさを浮動小数点数 (小数を含む数）で指定したい場合は RectF
		RectF{ 200.4, 450.3, 390.5, 122.5 }.draw(Palette::Skyblue);
	}
}
```

図形は `draw()` した順番に描画されます。このプログラムの、マウスカーソルに追従する赤い正方形と画面の下にある水色の大きな長方形を比べると、後者のほうがあとから描画されます。

`Rect` 型は左上の座標と幅、高さをそれぞれ `int32 x`, `int32 y`, `int32 w`, `int32 h` というメンバ変数で表します。整数ではなく浮動小数点数で扱いたい場合は、すべての要素が `double` 型である `RectF` を使います。


## 2.9 枠を描く
図形の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame()` を使います。`.drawFrame()` の第 1 引数には図形の内側方向への太さを、第 2 引数には外側方向への太さを指定します。図形の `.draw()` や `.drawFrame()` の戻り値はその図形自身なので、`rect.draw().drawFrame()` のように関数を続けて書くこともできます。
![](/images/doc_v6/tutorial/2/9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の内側に 3px の枠を描く
		Rect{ 100, 100, 100, 30 }
			.drawFrame(3, 0);

		// 長方形の外側に 3px の枠を描く
		Rect{ 220, 100, 100, 30 }
			.drawFrame(0, 3);

		// 長方形と、その内側 3px と外側 3px に枠を描く
		Rect{ 200, 200, 400, 100 }
			.draw(Palette::White)
			.drawFrame(3, 3, Palette::Orange);

		// 円の内側 1px と外側 1px に枠を描く
		Circle{ Cursor::Pos(), 40 }
			.drawFrame(1, 1, Palette::Seagreen);
	}
}
```


## 2.10 線分を描く
始点と終点を指定して線分を描くときは `Line` を作成して `.draw()` します。`.draw()` のパラメータには描画する線分の太さと色を指定します。線分の両端を丸くしたり、点線にしたりするなど、スタイルの変更もできます。
![](/images/doc_v6/tutorial/2/10.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (100, 100) から (400, 150) まで太さ 4px の線分を描く
		Line{ 100, 100, 400, 150 }.draw(4, Palette::Yellow);

		// 座標 (400, 300) からマウスカーソルの座標まで太さ 10px の線分を描く
		Line{ 400, 300, Cursor::Pos() }.draw(10, Palette::Skyblue);

		// 通常の線
		Line{ 100, 400, 700, 400 }.draw(12, Palette::Orange);

		// 両端が丸い線
		Line{ 100, 450, 700, 450 }.draw(LineStyle::RoundCap, 12, Palette::Orange);

		// 四角いドットの線
		Line{ 100, 500, 700, 500 }.draw(LineStyle::SquareDot, 12, Palette::Orange);

		// 丸いドットの線
		Line{ 100, 550, 700, 550 }.draw(LineStyle::RoundDot, 12, Palette::Orange);
	}
}
```


## 2.11 様々な図形
Siv3D にはこれ以外にも様々な図形クラスが用意されていて、複雑な形状を短いコードで記述できます。

|クラス|図形|
|--|--|
|`Line`|線分|
|`Rect`|長方形 (int32)|
|`RectF`|長方形 (double)|
|`Circle`|円|
|`Ellipse`|楕円|
|`Triangle`|三角形|
|`Quad`|凸な四角形|
|`RoundRect`|角丸四角形|
|`Polygon`|多角形|
|`MultiPolygon`|多角形の集合|
|`Bezier2`|2 次ベジェ曲線|
|`Bezier3`|3 次ベジェ曲線|
|`LineString`|連続する複数の線分|
|`Spline2D`|スプライン曲線|
|`Shape2D`|正多角形や星、ハート、十字などのコレクション|

![](https://storage.googleapis.com/zenn-user-upload/vl799b9wdvudg7iz3f1aq1zl7jiz)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double angle = (Scene::Time() * 60_deg);

		Line{ 20, 20, 80, 80 }.draw(4);
		Rect{ 120, 40, 60, 40 }.draw();
		Circle{ 250, 50, 30 }.draw();
		Ellipse{ 350, 50, 30, 20 }.draw();	
		Triangle{ 450, 60, 70, angle }.draw();
		Quad{ {520, 20}, {580, 40}, {580, 80}, {520, 80} }.draw();
		RoundRect{ 620, 20, 80, 60, 10 }.draw();
		Rect{ 720, 30, 60, 40 }.rounded(20, 0, 20, 0).draw();

		Rect{ 20, 130, 60, 40 }.rotated(angle).draw();
		Polygon{ {120, 120}, {150, 110}, {180, 150}, {150, 140}, {120, 180} }.draw();
		Bezier2{ {220, 180}, {250, 100}, {280, 120} }.draw(3);
		Bezier3{ {320, 180}, {350, 180}, {350, 120}, {380, 120} }.draw(3);
		LineString{ {420, 180}, {450, 120}, {460, 180}, {480, 120} }.draw(3);
		LineString{ {520, 180}, {540, 150}, {560, 160}, {580, 120} }.asSpline().draw(3);
		Shape2D::Plus(40, 20, { 650, 150 }, angle).draw();
		Shape2D::Cross(40, 15, { 750, 150 }, angle).draw();

		Shape2D::Pentagon(40, { 50, 250 }, angle).draw();
		Shape2D::Hexagon(40, { 150, 250 }, angle).draw();
		Shape2D::Ngon(8, 40, { 250, 250 }, angle).draw();
		Shape2D::Star(40, { 350, 250 }, angle).draw();
		Shape2D::NStar(8, 40, 30, { 450, 250 }, angle).draw();
		Line{ 520, 220, 580, 280 }.drawArrow(4, { 20, 30 });
		Shape2D::Rhombus(60, 40, { 650, 250 }, angle).draw();
		Shape2D::RectBalloon(Rect{ 720, 220, 60, 40 }, { 710, 290 }).draw();

		Shape2D::Stairs({ 80, 380 }, 60, 60, 4).draw();
		Shape2D::Heart(40, { 150, 350 }, angle).draw();
		Shape2D::Hexagon(40, { 250, 350 })
			.asPolygon().addHole({ {260, 340}, {240, 340}, {240, 360}, {260, 360} }).draw();
		Rect{ 320, 320, 60, 60 }.drawFrame(5);
		Circle{ 450, 350, 40 }.drawFrame(5);
		Circle{ 550, 350, 40 }.drawPie(angle, 120_deg);
		Circle{ 650, 350, 40 }.drawArc(angle, 240_deg, 10, 0);
		Circle{ 750, 350, 40 }.drawArc(LineStyle::RoundCap, angle, 240_deg, 10, 0);

		Line{ 20, 420, 80, 480 }.draw(LineStyle::RoundCap, 6);
		Line{ 120, 420, 180, 480 }.draw(LineStyle::SquareDot, 6);
		Line{ 220, 420, 280, 480 }.draw(LineStyle::RoundDot, 6);
	}
}
```


## 2.12 グラデーションを描く
`Line` や `Triangle`, `Rect`, `RectF`, `Quad` には、頂点ごとに色を指定し、グラデーションで塗りつぶすオプションがあります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/21-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Line{ 100, 100, 500, 150 }
			.draw(6, Palette::Yellow, Palette::Red);

		Triangle{ 200, 200, 100 }
			.draw(HSV{ 0 }, HSV{ 120 }, HSV{ 240 });

		// 左から右へのグラデーション
		Rect{ 400, 200, 200, 100 }
			.draw(Arg::left = Palette::Skyblue, Arg::right = Palette::Blue);
		
		// 上から下へのグラデーション
		Rect{ 200, 400, 400, 100 }
			.draw(Arg::top = ColorF{ 1.0, 1.0 }, Arg::bottom = ColorF{ 1.0, 0.0 });
	}
}
```


# 3. 画像を描く

## 3.1 絵文字を描画する
シーンに画像を描きたいときは `Texture` を作成し、`.draw()` または `.drawAt()` します。`Texture` は、次のような方法で作成できます。

- 画像ファイルから画像データを読み込む
- プログラムで作成した画像データ (`Image`) から作成する
- Siv3D が標準で提供する絵文字コレクションから作成する
- Siv3D が標準で提供するアイコンコレクションから作成する

`Texture` はテクスチャのリソースをデストラクタで自動的に解放するため、リソースの解放処理を明示的に書く必要はありません。

まず最初に、絵文字コレクションから好きな絵文字を選んで `Texture` を作成し、それを描画するプログラムを書いてみましょう。`Texture` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Texture` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

絵文字コレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数に `U"🐈"_emoji` のように絵文字を渡します。

### Texture::drawAt()
`.drawAt()` では、テクスチャの中心をどこに据えるかをシーンの座標で指定します。絵文字やアイコンを描く場合はこの指定方法が便利です。
![](/images/doc_v6/tutorial/8/1.1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// テクスチャを座標 (0, 0) を中心に描画
		texture.drawAt(0, 0);

		// テクスチャを座標 (200, 200) を中心に描画
		texture.drawAt(200, 200);

		// テクスチャを、シーン中央を中心にして描画
		texture.drawAt(Scene::Center());
	}
}
```

### Texture::draw()
`.draw()` では、テクスチャの左上をどこに据えるかをシーンの座標で指定します。背景画像や UI などを描くときにはこの指定方法が便利な場合があります。
![](/images/doc_v6/tutorial/8/1.2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// テクスチャを座標 (400, 0) から描画
		texture.draw(400, 0);

		// 座標を指定しない場合 (0, 0) から描画
		texture.draw();

		// テクスチャをシーン中心から描画
		texture.draw(Scene::Center());
	}
}
```

### Siv3D で使える絵文字の一覧
Siv3D で使える絵文字は約 3,600 種類あります。絵文字を探すときは [emojipedia](https://emojipedia.org/) の Categories から調べるのが便利です。 OpenSiv3D v0.6.3 はオープンソースの絵文字フォント Noto Color Emoji (Unicode 14.0 版) を内蔵しているので、Siv3D アプリはどのプラットフォームでも同じ見た目の絵文字を表示できます。


## 3.2 テクスチャを拡大縮小して描画する

### Texture::scaled()
`Texture::scaled()` に拡大縮小倍率を指定すると、`Texture` にサイズ情報が付加された `TextureRegion` を作成できます。`TextureRegion` は `Texture` のように `.draw()` または `.drawAt()` できます。`Texture` を作成するのと異なり、既存の `Texture` から `TextureRegion` を作成するのは非常に軽い実行時負荷です。サンプルでは示していませんが、縦横で異なる倍率に設定することもできます。
![](/images/doc_v6/tutorial/8/2.1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	const Texture dog{ U"🐕"_emoji };

	while (System::Update())
	{
		// 2 倍に拡大して描画
		cat.scaled(2.0).drawAt(200, 300);

		// 1.5 倍に拡大して描画
		cat.scaled(1.5).drawAt(400, 300);

		// 半分のサイズに縮小して描画
		dog.scaled(0.5).drawAt(600, 300);
	}
}
```

### Texture::resized()
`Texture::resized()` は、拡大縮小後のサイズを倍率ではなくピクセル単位で指定します。Siv3D 標準の絵文字のサイズは幅が 136 ピクセルなので、136 より大きい数を指定すると拡大、小さい数を指定すると縮小表示になります。サンプルでは示していませんが、縦横で異なるサイズに設定することもできます。
![](/images/doc_v6/tutorial/8/2.2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	const Texture dog{ U"🐕"_emoji };

	while (System::Update())
	{
		// 300px に拡大して描画
		cat.resized(300).drawAt(200, 300);

		// 200px に拡大して描画
		cat.resized(200).drawAt(400, 300);

		// 20px に縮小して描画
		dog.resized(20).drawAt(600, 300);
	}
}
```


## 3.3 テクスチャを回転して描画する
`Texture::rotated()` または `Texture::rotatedAt()` によって、テクスチャに回転情報を付加した `TexturedQuad` を作成できます。`TexturedQuad` は `Texture` のように `.draw()` または `.drawAt()` できます。`TextureRegion` と同様、`TexturedQuad` を作成するのは非常に軽い実行時負荷です。

### Texture::rotated()
テクスチャの中心を軸にして回転します。回転角度はラジアンで指定します。
![](/images/doc_v6/tutorial/8/3.1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 毎秒 90° 時計回りに回転
		cat.rotated(Scene::Time() * 90_deg).drawAt(Scene::Center());
	}
}
```

### Texture::roatedAt()
テクスチャ上の指定した座標を軸にして回転します。`Vec2{ 0, 0 }` を指定すればテクスチャの左上が軸になります。回転角度はラジアンで指定します。
![](/images/doc_v6/tutorial/8/3.2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 画像中の (40, 20) を軸に回転させて描画
		cat.rotatedAt(Vec2{ 40, 20 }, Scene::Time() * 90_deg).draw(Scene::Center());
	}
}
```


## 3.4 テクスチャを上下・左右反転して描画する
`Texture::flipped()` で上下反転、`Texture::mirrored()` で左右反転した `TextureRegion` を作成できます。それぞれ、引数をとらずに反転する関数、引数の `bool` 値が `true` のときだけ反転する関数の 2 種類のオーバーロードがあります。
![](/images/doc_v6/tutorial/8/4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 上下反転
		cat.flipped().drawAt(200, 300);

		// 左右反転
		cat.mirrored().drawAt(400, 300);

		// 反転するかを bool 値で指定するオーバーロード
		cat.mirrored(false).drawAt(600, 300);
	}
}
```


## 3.5 アイコンを描画する
アイコンコレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数にアイコンオブジェクトを渡します。アイコンは全部で約 7,700 種類用意されています。

`Texture` のコンストラクタには、[Font Awesome アイコン一覧](https://fontawesome.com/icons?d=gallery&s=brands,solid&m=free) または [Material Design Icons](https://pictogrammers.github.io/@mdi/font/6.1.95/) で調べられる 16 進数コードに `_icon` を付けた値と、アイコンの基本サイズ（ピクセル）を渡します。
![](/images/doc_v6/tutorial/8/5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.92 });

	// 歯車 (f013) のアイコン
	const Texture icon0{ 0xf013_icon, 40 };

	// 南京錠 (f023) のアイコン
	const Texture icon1{ 0xf023_icon, 80 };

	// 拡大鏡 (f00e) のアイコン
	const Texture icon2{ 0xf00e_icon, 120 };

	while (System::Update())
	{
		icon0.drawAt(200, 300, ColorF{ 0.25 });

		icon1.drawAt(400, 300, ColorF{ 0.25 });

		icon2.drawAt(600, 300, ColorF{ 0.25 });
	}
}
```


## 3.6 画像ファイルを読み込んで描画する
画像ファイルから `Texture` を作成するには、`Texture` のコンストラクタ引数に、読み込みたい画像ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。
![](/images/doc_v6/tutorial/8/6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 風車の画像
	const Texture textureWindmill{ U"example/windmill.png" };

	// Siv3D くん（Siv3D の公式マスコットキャラクター）の画像
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		textureWindmill.draw(40, 20);

		textureSiv3DKun.draw(400, 100);
	}
}
```

### 対応している画像フォーマット
OpenSiv3D v0.6.3 では、9 種類の画像フォーマットの読み込みがサポートされています。

| フォーマット   | 拡張子             | 対応状況       |
|----------|-----------------|:----------:|
| PNG      | png             | ✔          |
| JPEG     | jpg/jpeg        | ✔          |
| BMP      | bmp             | ✔          |
| SVG      | svg             | ✔          |
| GIF      | gif             | ✔          |
| TGA      | tga             | ✔          |
| PPM      | ppm/pgm/pbm/pnm | ✔          |
| WebP     | webp            | ✔          |
| TIFF     | tif/tiff        | ✔          |
| JPEG2000 | jp2             | (将来のバージョン) |
| DDS      | dds             | (将来のバージョン) |


## 3.7 テクスチャの一部を描画する
テクスチャの全部ではなく、特定の長方形の領域だけを描画したい場合は `Texture::operator()` で、テクスチャ上の描画したい領域を指定して `TextureRegion` を作成します。
![](/images/doc_v6/tutorial/8/8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture textureWindmill{ U"example/windmill.png" };

	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		// 画像の (250, 100) から幅 200, 高さ 150 の長方形部分
		textureWindmill(250, 100, 200, 150).draw(40, 20);

		// 画像の (100, 0) から幅 100, 高さ 150 の長方形部分
		textureSiv3DKun(100, 0, 100, 150).draw(400, 100);
	}
}
```


## 3.8 動画をテクスチャとして扱う
シーンに動画を描きたいときは `VideoTexture` を作成し、`.draw()` または `.drawAt()` します。`VideoTexture` は `Texture` とほぼ同じインタフェースを持ち、動画ファイルを `Texture` のように扱えます。`VideoTexture` は毎フレーム `.advance()` を呼ぶことで再生位置を進めます。Siv3D はバックグラウンドのスレッドで動画の次のフレームの先読みを行っています。

対応する動画フォーマットはプラットフォームによって異なりますが、MP4 ファイルは Windows, macOS, Linux でサポートされています。音声付き動画の音声を再生する方法は OpenSiv3D v0.6.3 では用意されていません。映像のみです。

https://youtu.be/B4WPEdA4vMI
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ループする場合は Loop::Yes, ループしない場合は Loop::No
	const VideoTexture videoTexture{ U"example/video/river.mp4", Loop::Yes };

	while (System::Update())
	{
		ClearPrint();

		// 再生位置（秒） / 動画の長さ（秒）
		Print << videoTexture.posSec() << U" / " << videoTexture.lengthSec();

		// 動画の時間を進める (デフォルトでは Scece::DeltaTime() 秒)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();

		videoTexture
			.scaled(0.5)
			.rotated(Scene::Time() * 30_deg)
			.drawAt(Cursor::Pos());
	}
}
```


# 4. フォントを使う

## 4.1 Font
前章までテキストの表示に使ってきた `Print` は、フォントのサイズや種類、描画位置に自由度がありませんでした。自由にカスタマイズしたフォントを使ってテキストを描きたいときは `Font` を作成し、描画したい内容を `()` でつなげたあと、`.draw()` または `.drawAt()` します。

`Texture` と同じように、`Font` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Font` を作成するのは避け、作成が 1 回だけになるようにしましょう。
![](/images/doc_v6/tutorial/14/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 基本サイズ 50 のフォントを作成
	const Font font{ 50 };

	while (System::Update())
	{
		// 左上位置 (20, 20) からテキストを描く
		font(U"Hello, Siv3D!").draw(20, 20);

		// テキストの中心座標が画面の中心になるようにテキストを描く
		font(U"C++").drawAt(Scene::Center(), Palette::Skyblue);

		// 文字列以外を渡すと Format される
		font(Cursor::Pos()).draw(50, 300);

		// 複数渡すと、それぞれを Format した文字列をつなげる
		font(123, U"ABC").draw(50, 400, ColorF{ 0.5, 1.0, 0.5 });

		font(U"{}/{}/{}"_fmt(2021, 12, 31)).draw(50, 500, ColorF{ 1.0, 0.5, 0.0 });
	}
}
```


## 4.2 改行する
テキストの中に改行文字 `'\n'` が含まれていると、そこで改行されます。
![](/images/doc_v6/tutorial/14/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50 };

	while (System::Update())
	{
		font(U"Hello,\nSiv3D\n\n!!!").draw(20, 20);
	}
}
```


## 4.3 フォントのサイズ
`Font` のコンストラクタの第 1 引数にはフォントの基本サイズを指定します。単位はピクセルです。基本サイズはあとから変更できません。1 つの `Font` からさまざまなサイズのテキストを描く方法はのちほど紹介します。
![](/images/doc_v6/tutorial/14/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 基本サイズ 20 のフォント
	const Font font20{ 20 };

	// 基本サイズ 40 のフォント
	const Font font40{ 40 };

	// 基本サイズ 60 のフォント
	const Font font60{ 60 };

	// 基本サイズ 80 のフォント
	const Font font80{ 80 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font20(text).draw(20, 20);

		font40(text).draw(20, 60);

		font60(text).draw(20, 120);

		font80(text).draw(20, 200);
	}
}
```


## 4.4 フォントの種類
Siv3D には異なる太さの 7 種類の日本語フォントと、5 地域向けの CJK（中国語・韓国語・日本語対応）フォント、白黒絵文字フォント、カラー絵文字フォントが同梱されています。`Font` のコンストラクタにおいて `Typeface::` で書体を指定することで、それらの書体を利用できます。何も指定しなかった場合 `Typeface::Regular` が選択されます。


|Typeface|説明|
|--|--|
|`Typeface::Thin`|細い日本語フォント|
|`Typeface::Light`|やや細い日本語フォント|
|`Typeface::Regular`|通常日本語フォント|
|`Typeface::Medium`|やや太い日本語フォント|
|`Typeface::Bold`|太い日本語フォント|
|`Typeface::Heavy`|とても太い日本語フォント|
|`Typeface::Black`|最も太い日本語フォント|
|`Typeface::CJK_Regular_JP`|日本語デザインの CJK フォント|
|`Typeface::CJK_Regular_KR`|韓国語デザインの CJK フォント|
|`Typeface::CJK_Regular_SC`|簡体字デザインの CJK フォント|
|`Typeface::CJK_Regular_TC`|台湾繁体字デザインの CJK フォント|
|`Typeface::CJK_Regular_HK`|香港繁体字デザインの CJK フォント|
|`Typeface::MonochromeEmoji`|モノクロ絵文字フォント|
|`Typeface::ColorEmoji`|カラー絵文字フォント|

![](/images/doc_v6/tutorial/14/4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font fontThin{ 36, Typeface::Thin };
	const Font fontLight{ 36, Typeface::Light };
	const Font fontRegular{ 36, Typeface::Regular };
	const Font fontMedium{ 36, Typeface::Medium };
	const Font fontBold{ 36, Typeface::Bold };
	const Font fontHeavy{ 36, Typeface::Heavy };
	const Font fontBlack{ 36, Typeface::Black };

	const Font fontJP{ 36, Typeface::CJK_Regular_JP };
	const Font fontKR{ 36, Typeface::CJK_Regular_KR };
	const Font fontSC{ 36, Typeface::CJK_Regular_SC };
	const Font fontTC{ 36, Typeface::CJK_Regular_TC };
	const Font fontHK{ 36, Typeface::CJK_Regular_HK };

	const Font fontMono{ 36, Typeface::MonochromeEmoji };

	// カラー絵文字フォントは、サイズの指定が無視される仕様
	const Font fontEmoji{ 36, Typeface::ColorEmoji };

	const String s0 = U"Hello, Siv3D!";
	const String s1 = U"こんにちは 你好 안녕하세요 骨曜喝愛遙扇";
	const String s2 = U"🐈🐕🚀";

	while (System::Update())
	{
		fontThin(s0).draw(20, 20);
		fontLight(s0).draw(20, 60);
		fontRegular(s0).draw(20, 100);
		fontMedium(s0).draw(20, 140);
		fontBold(s0).draw(20, 180);
		fontHeavy(s0).draw(20, 220);
		fontBlack(s0).draw(20, 260);

		fontJP(s1).draw(20, 300);
		fontKR(s1).draw(20, 340);
		fontSC(s1).draw(20, 380);
		fontTC(s1).draw(20, 420);
		fontHK(s1).draw(20, 460);

		fontMono(s2).draw(20, 500);
		fontEmoji(s2).draw(20, 540);
	}
}
```


## 4.5 フォントファイルからフォントを読み込んで使う
コンピュータ上にあるフォントファイルから `Font` を作成するには、`Font` のコンストラクタに、読み込みたいフォントファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには、のちの章で説明する「リソース」パスの使用を推奨します。
![](/images/doc_v6/tutorial/14/6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// RocknRollOne-Regular.ttf をロードして使う
	const Font font{ 50, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!\nこんにちは！").draw(20, 20);
	}
}
```


# 5. GUI

## 5.1 ボタン
ボタンの表示と入力の取得を実装するときは `SimpleGUI::Button()` 関数を使うと便利です。関数にはボタンのテキストや位置、幅、状態などを設定できます。`SimpleGUI::Button()` は自身が押されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/1.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Red", Vec2{ 100, 100 }))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.2, 0.2 });
		}

		if (SimpleGUI::Button(U"Green", Vec2{ 100, 150 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.8, 0.2 });
		}

		if (SimpleGUI::Button(U"Blue", Vec2{ 100, 200 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.2, 0.8 });
		}

		// ボタンの幅を 200px に指定
		if (SimpleGUI::Button(U"White", Vec2{ 100, 250 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.9 });
		}

		if (SimpleGUI::Button(U"Black", Vec2{ 100, 300 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.1 });
		}

		// ボタンを無効化
		if (SimpleGUI::Button(U"Gray", Vec2{ 100, 350 }, 200, false))
		{
			Scene::SetBackground(ColorF{ 0.5 });
		}

		// ボタンを無効化、ボタンの幅はテキストに合わせる
		if (SimpleGUI::Button(U"Yellow", Vec2{ 100, 400 }, unspecified, false))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.8, 0.1 });
		}
	}
}
```


## 5.2 スライダー
スライダーの表示と値の取得を実装するときは `SimpleGUI::Slider()` 関数を使うと便利です。関数にはスライダーのテキストや位置、幅、値の範囲などを設定できます。テキストを持たない縦方向のスライダーは `SimpleGUI::VerticalSlider()` を使います。`SimpleGUI::Slider()` と `SimpleGUI::VerticalSlider()` は値が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/2.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	ColorF color1{ 1.0 };
	ColorF color2{ 1.0, 0.5, 0.0 };
	ColorF color3{ 0.2, 0.6, 0.9 };

	double value1 = 5.0;
	double value2 = 7.0;
	double value3 = 2.0;
	double value4 = 4.0;

	while (System::Update())
	{
		SimpleGUI::Slider(color1.r, Vec2{ 100, 40 });
		SimpleGUI::Slider(color1.g, Vec2{ 100, 80 });
		SimpleGUI::Slider(color1.b, Vec2{ 100, 120 });
		Circle{ 50, 100, 30 }.draw(color1);

		SimpleGUI::Slider(U"Red", color2.r, Vec2{ 100, 200 });
		SimpleGUI::Slider(U"Green", color2.g, Vec2{ 100, 240 });
		SimpleGUI::Slider(U"Blue", color2.b, Vec2{ 100, 280 });
		Circle{ 50, 260, 30 }.draw(color2);

		// スライダーの値を表示、ラベルの幅 100px, スライダーの幅 200px
		SimpleGUI::Slider(U"R {:.2f}"_fmt(color3.r), color3.r, Vec2{ 100, 360 }, 100, 200);
		SimpleGUI::Slider(U"G {:.2f}"_fmt(color3.g), color3.g, Vec2{ 100, 400 }, 100, 200);
		SimpleGUI::Slider(U"B {:.2f}"_fmt(color3.b), color3.b, Vec2{ 100, 440 }, 100, 200);
		Circle{ 50, 420, 30 }.draw(color3);

		// 値の範囲が 0.0～10.0
		SimpleGUI::Slider(U"{:.2f}"_fmt(value1), value1, 0.0, 10.0, Vec2{ 500, 40 }, 60, 150);

		// スライダーを無効化
		SimpleGUI::Slider(U"{:.2f}"_fmt(value2), value2, 0.0, 10.0, Vec2{ 500, 100 }, 60, 150, false);

		// 縦のスライダー
		SimpleGUI::VerticalSlider(value3, 0.0, 10.0, Vec2{ 500, 160 }, 200);
		SimpleGUI::VerticalSlider(value4, 0.0, 10.0, Vec2{ 560, 160 }, 200, false);
	}
}
```


## 5.3 チェックボックス
チェックボックスの表示と入力の取得を実装するときは `SimpleGUI::CheckBox()` 関数を使うと便利です。関数にはチェックボックスのテキストや位置、幅、状態などを設定できます。`SimpleGUI::CheckBox()` は値が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/3.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	bool checked0 = false;
	bool checked1 = true;
	bool checked2 = false;
	bool checked3 = false;
	bool checked4 = false;
	bool checked5 = false;

	while (System::Update())
	{
		SimpleGUI::CheckBox(checked0, U"Label0", Vec2{ 100, 40 });
		SimpleGUI::CheckBox(checked1, U"Label1", Vec2{ 100, 80 });
		SimpleGUI::CheckBox(checked2, U"Label2", Vec2{ 100, 120 });

		// 幅 200px
		SimpleGUI::CheckBox(checked3, U"Label3", Vec2{ 100, 180 }, 200 );

		// 無効化
		SimpleGUI::CheckBox(checked4, U"Label4", Vec2{ 100, 220 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::CheckBox(checked5, U"Label5", Vec2{ 100, 260 }, unspecified, false);
	}
}
```


## 5.4 ラジオボタン
ラジオボタンの表示と入力の取得を実装するときは `SimpleGUI::RadioButtons()` 関数を使うと便利です。関数にはラジオボタンのテキストや位置、幅、状態などを設定できます。`SimpleGUI::RadioButtons()` は値が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/4.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	size_t index0 = 0;
	size_t index1 = 2;
	size_t index2 = 0;
	size_t index3 = 1;
	size_t index4 = 0;

	const Array<String> options = { U"Red", U"Green", U"Blue" };
	constexpr std::array<ColorF, 3> colors = { ColorF{ 0.8, 0.2, 0.2 }, ColorF{ 0.2, 0.8, 0.2 }, ColorF{ 0.2, 0.2, 0.8 } };

	Scene::SetBackground(colors[index1]);

	while (System::Update())
	{
		SimpleGUI::RadioButtons(index0, { U"Option1", U"Option2", U"Option3" }, Vec2{ 100, 40 });

		// 選択肢を Array<String> で指定
		if (SimpleGUI::RadioButtons(index1, options, Vec2{ 100, 180 }))
		{
			Scene::SetBackground(colors[index1]);
		}

		// 幅 200px
		SimpleGUI::RadioButtons(index2, { U"A", U"B" }, Vec2{ 400, 40 }, 200);

		// 無効化
		SimpleGUI::RadioButtons(index3, { U"A", U"B" }, Vec2{ 400, 140 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::RadioButtons(index4, { U"A", U"B" }, Vec2{ 400, 240 }, unspecified, false);
	}
}
```


## 5.5 テキストボックス
テキストボックスを実装するときは `SimpleGUI::TextBox()` 関数を使うと便利です。関数にはテキストボックスの位置、幅、文字数の上限、状態などを設定できます。テキストは `TextEditState` 型のオブジェクトによって管理します。`SimpleGUI::TextBox()` はテキストが変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/5.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	TextEditState te0;
	TextEditState te1;
	te1.text = U"Siv3D"; // デフォルトの文字列
	TextEditState te2;
	TextEditState te3;

	while (System::Update())
	{
		ClearPrint();
		Print << te0.active; // アクティブかどうか
		Print << te0.text; // 入力されたテキスト (String)

		SimpleGUI::TextBox(te0, Vec2{ 100, 140 });

		SimpleGUI::TextBox(te1, Vec2{ 100, 200 });

		if (SimpleGUI::Button(U"Clear", Vec2{ 320, 200 }))
		{
			// テキストを消去
			te1.clear();
		}

		// 幅 100px, 文字数を 4 文字までに制限
		SimpleGUI::TextBox(te2, Vec2{ 100, 260 }, 100, 4);

		// 無効化
		SimpleGUI::TextBox(te3, Vec2{ 100, 320 }, 100, 4, false);
	}
}
```


## 5.6 カラーピッカー
カラーピッカーは `SimpleGUI::ColorPicker()` 関数を使うと便利です。関数にはカラーピッカーの位置、状態などを設定できます。`SimpleGUI::ColorPicker()` は色が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/7.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	HSV color0 = Palette::Orange;
	HSV color1 = Palette::Skyblue;

	while (System::Update())
	{
		SimpleGUI::ColorPicker(color0, Vec2{ 100, 100 });
		Rect{ 100, 300, 100 }.draw(color0);

		SimpleGUI::ColorPicker(color1, Vec2{ 300, 100 }, false);
		Rect{ 300, 300, 100 }.draw(color1);
	}
}
```

## 5.7 リストボックス

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	ListBoxState ls1{
		{
			U"北海道", U"青森県", U"岩手県", U"宮城県", U"秋田県", U"山形県", U"福島県", U"茨城県",
			U"栃木県", U"群馬県", U"埼玉県", U"千葉県", U"東京都", U"神奈川県", U"新潟県", U"富山県",
			U"石川県", U"福井県", U"山梨県", U"長野県", U"岐阜県", U"静岡県", U"愛知県", U"三重県",
			U"滋賀県", U"京都府", U"大阪府", U"兵庫県", U"奈良県", U"和歌山県", U"鳥取県", U"島根県",
			U"岡山県", U"広島県", U"山口県", U"徳島県", U"香川県", U"愛媛県", U"高知県", U"福岡県",
			U"佐賀県", U"長崎県", U"熊本県", U"大分県", U"宮崎県", U"鹿児島県", U"沖縄県",
		}
	};

	ListBoxState ls2{
		{
			U"吾輩は猫である（1905年1月 - 1906年8月、『ホトトギス』/1905年10月 - 1907年5月、大倉書店・服部書店）",
			U"坊っちゃん（1906年4月、『ホトトギス』/1907年、春陽堂刊『鶉籠』収録）",
			U"草枕（1906年9月、『新小説』/『鶉籠』収録）",
			U"二百十日（1906年10月、『中央公論』/『鶉籠』収録）",
			U"野分（1907年1月、『ホトトギス』/1908年、春陽堂刊『草合』収録）",
			U"虞美人草（1907年6月 - 10月、『朝日新聞』/1908年1月、春陽堂）",
			U"坑夫（1908年1月 - 4月、『朝日新聞』/『草合』収録）",
			U"三四郎（1908年9 - 12月、『朝日新聞』/1909年5月、春陽堂）",
			U"それから（1909年6 - 10月、『朝日新聞』/1910年1月、春陽堂）",
			U"門（1910年3月 - 6月、『朝日新聞』/1911年1月、春陽堂）",
			U"彼岸過迄（1912年1月 - 4月、『朝日新聞』/1912年9月、春陽堂）",
			U"行人（1912年12月 - 1913年11月、『朝日新聞』/1914年1月、大倉書店）",
			U"こゝろ（1914年4月 - 8月、『朝日新聞』/1914年9月、岩波書店）",
			U"道草（1915年6月 - 9月、『朝日新聞』/1915年10月、岩波書店）",
			U"明暗（1916年5月 - 12月、『朝日新聞』/1917年1月、岩波書店）",
		}
	};

	ls2.selectedItemIndex = 3;

	ListBoxState ls3 = ls2;

	while (System::Update())
	{
		if (SimpleGUI::ListBox(ls1, Vec2{ 20, 20 }, 120, 156) && ls1.selectedItemIndex)
		{

		}

		if (SimpleGUI::ListBox(ls2, Vec2{ 180, 20 }, 240, 156, false) && ls2.selectedItemIndex)
		{

		}

		if (SimpleGUI::ListBox(ls3, Vec2{ 20, 200 }, 1020, 480) && ls3.selectedItemIndex)
		{

		}
	}
}
```


# 6. キーボード入力

## 6.1 キーの入力状態を調べる
キーボードのキーには「Key～」と名付けられた `Input` 型の値が割り当てられています。

- A, B, C, ... は `KeyA`, `KeyB`, `KeyC` , ...
- 1, 2, 3, ... は `Key1`, `Key2`, `Key3`, ...
- F1, F2, F3, ... は `KeyF1`, `KeyF2`, `KeyF3`, ...
- ↑, ↓, ←, → は `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight`
- スペースキーは `KeySpace`
- エンターキーは `KeyEnter`
- バックスペースキーは `KeyBackspace`
- Tab キーは `KeyTab`
- Esc キーは `KeyEscape`
- PageUp, PageDown は `KeyPageUp`, `KeyPageDown`
- Delete キーは `KeyDelete`
- Numpad の 0, 1, 2, ... は `KeyNum0`, `KeyNum1`, `KeyNum2`, ...
- シフトキーは `KeyShift`
- 左シフトキー、右シフトキーは `KeyLShift`, `KeyRShift`
- コントロールキーは `KeyControl`
- (macOS) コマンドキーは `KeyCommand`
- 「,」「.」「/」キーは `KeyComma`, `KeyPeriod`, `KeySlash`
- 上記以外のキーは [`<Siv3D/Keyboard.hpp>`](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Keyboard.hpp) を参照

`Input` 型の値はメンバ関数を持ち、押した瞬間であるかを `.down()`, 押し続けているかを `.pressed()`, 離した瞬間であるかを `.up()` を使って `bool` 値で取得できます。

| 関数 | 押していないとき | 押した瞬間 | 押し続けている | 離した瞬間 | 離し続けている |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.up()` | false | false | false | **✔ true** | false |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		const double delta = (Scene::DeltaTime() * 200);

		// 上下左右キーで移動
		if (KeyLeft.pressed())
		{
			pos.x -= delta;
		}

		if (KeyRight.pressed())
		{
			pos.x += delta;
		}

		if (KeyUp.pressed())
		{
			pos.y -= delta;
		}

		if (KeyDown.pressed())
		{
			pos.y += delta;
		}

		// [C] キーが押されたら中央に戻る
		if (KeyC.down())
		{
			pos = Scene::Center();
		}

		Circle{ pos, 50 }.draw();
	}
}
```


## 6.2 キーが押されている時間を調べる
`Input` の `.pressedDuration()` は、その入力が押され続けている時間を `Duration` 型の値で返します。

押され続けている時間は `.up()` が `true` になるフレームまで有効です。`.up()` されたときに `.pressedDuration()` を調べると、そのキーが離されるまで何秒間押され続けていたかを取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << KeyA.pressedDuration();

		if (1s <= KeySpace.pressedDuration())
		{
			Print << U"Space";
		}
	}
}
```


## 6.3 複数のキーの組み合わせ

### A または B
`|` を使って複数のキーを組み合わせると、そのいずれかが押されているかどうかを判定できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// [スペース] または [エンター] が押されている
		if ((KeySpace | KeyEnter).pressed())
		{
			Print << U"KeySpace / KeyEnter";
		}
	}
}
```

### A を押しながら B
`+` を使って 2 つのキーを組み合わせると、左のキーが押されながら、右のキーが押されたかどうかを判定できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// [Ctrl + C] または [Command + C] が押された
		if ((KeyControl + KeyC).down()
			|| (KeyCommand + KeyC).down())
		{
			Print << U"Ctrl + C / Command + C";
		}
	}
}
```


## 6.4 テキスト入力
`TextInput::UpdateText()` に `String` 型の変数を渡すことで、テキスト入力を処理できます。`TextInput::GetEditingText()` は未変換の文字入力を取得できます。

![](/images/doc_v6/tutorial/16/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 30 };

	String text;

	constexpr Rect area{ 50, 50, 700, 300 };

	while (System::Update())
	{
		// キーボードからテキストを入力
		TextInput::UpdateText(text);

		// 未変換の文字入力を取得
		const String editingText = TextInput::GetEditingText();

		area.draw(ColorF{ 0.3 });

		font(text + U'|' + editingText).draw(area.stretched(-20));
	}
}
```


# 7. マウス入力

## 7.1 マウスカーソルの座標
マウスカーソルの座標は `Cursor::Pos()` を使うと `Point` 型で取得できます。シーンが実ウィンドウサイズと異なる (チュートリアル 15 参照) 場合、`Cursor::PosF()` を使うと `Vec2` 型で小数点数以下の座標も取得できます。

`Cursor::Pos()` で取得できるマウスカーソル座標は、最後の `System::Update()` の呼び出し時点での座標のため、実際画面に見えているマウスカーソルよりも古い座標を示す場合があります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Cursor::Pos();
		Print << Cursor::PosF();

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Skyblue);
	}
}
```


## 7.2 マウスカーソルの移動量
1 フレーム前のマウスカーソル座標は `Cursor::PreviousPos()` / `Cursor::PreviousPosF()` で取得できます。1 フレーム前からのマウスカーソルの移動量は `Cursor::Delta()` / `Cursor::DeltaF()` で取得できます。

`Cursor::Delta() == (Cursor::Pos() - Cursor::PreviousPos())` です。

![](/images/doc_v6/tutorial/17/2.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 円をつかんでいるか
	bool grab = false;

	Circle circle{ Scene::Center(), 50 };

	while (System::Update())
	{
		if (grab)
		{
			// 移動量分だけ円を移動
			circle.moveBy(Cursor::Delta());
		}

		if (circle.leftClicked()) // 円を左クリックしたら
		{
			grab = true;
		}
		else if (MouseL.up()) // マウスの左ボタンが離されたら
		{
			grab = false;
		}

		if (grab || circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw(Palette::Skyblue);
	}
}
```

図形の `.moveBy()` 関数は、与えられた x, y 成分だけ自身の座標を移動します。似たような名前の `.movedBy()` 関数は、与えられた x, y 成分だけ座標を移動した新しい図形を返し、自身の座標は変更しません。


## 7.3 マウスのボタンの入力状態
マウスのボタンには、以下の `Input` 型の値が割り当てられています。

| 定数      | 対応するボタン |
|---------|---------|
| `MouseL`  | 左ボタン    |
| `MouseR`  | 右ボタン    |
| `MouseM`  | 中央ボタン   |
| `MouseX1` | 拡張ボタン 1 |
| `MouseX2` | 拡張ボタン 2 |
| `MouseX3` | 拡張ボタン 3 |
| `MouseX4` | 拡張ボタン 4 |
| `MouseX5` | 拡張ボタン 5 |

キーボードと同様に、押した瞬間であるかを `.down()`, 押し続けているかを `.pressed()`, 離した瞬間であるかを `.up()` を使って `bool` 値で取得できます。

| 関数 | 押していないとき | 押した瞬間 | 押し続けている | 離した瞬間 | 離し続けている |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.up()` | false | false | false | **✔ true** | false |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << MouseL.pressed();
		Print << MouseM.pressed();
		Print << MouseR.pressed();
	}
}
```


## 7.4 マウスホイールの回転量
直前のフレームからのマウスホイールのスクロール量は、`Mouse::Wheel()` によって `double` 型で取得できます。水平ホイールのスクロール量は、`Mouse::WheelH()` によって `double` 型で取得できます。

マウスホイールのスクロール量はフレームレートに依存しないため、`Scene::Delta()` で調整する必要はありません。

![](/images/doc_v6/tutorial/17/7.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		ClearPrint();

		// マウスホイールのスクロール量
		Print << Mouse::Wheel();

		// マウスの水平ホイールのスクロール量
		Print << Mouse::WheelH();

		pos.y -= (Mouse::Wheel() * 10);
		pos.x += (Mouse::WheelH() * 10);

		RectF{ Arg::center = pos, 200 }.draw();
	}
}
```


# 演習 | Siv3D アプリを作ってみよう

[Siv3D 勉強会コース | 演習](course-study-try) に進む。


# お役立ちリンク

- [Siv3D リファレンス](https://zenn.dev/reputeless/books/siv3d-documentation)
  - リファレンスのトップページ
- [Siv3D のソースコード (GitHub)](https://github.com/Siv3D/OpenSiv3D)
  - `Siv3D/include/` がヘッダ
  - `Siv3D/src/` がソース
- [Siv3D ゲーム典型](https://github.com/Reputeless/games)
  - ゲームサンプルを掲載していくプロジェクト（100 個を目標に、先月からスタート）
- [Twitter #Siv3D #OpenSiv3D タグ検索](https://twitter.com/search?q=Siv3D%20OR%20OpenSiv3D&src=typed_query&f=live)
- [Siv3D コミュニティ](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/community)
- [Siv3D 作者の Twitter アカウント](https://twitter.com/Reputeless)
