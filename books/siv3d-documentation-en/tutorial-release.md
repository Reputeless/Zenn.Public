---
title: "Tutorial 41 | Releasing Your Application"
free: true
---

# 41. Releasing Your Application
この章では、作成したアプリケーションを配布するための手順を学びます。

## 41.1 さまざまなユーザ環境に対応できるかチェックする
よくあるトラブルは次のとおりです。

- ユーザのモニタの解像度が 1366×768 のときに、ウィンドウが画面に収まらない
  - 異なる解像度オプションを用意する
  - フルスクリーンモードで実行する
- ゲームパッドによって `Gamepad` のボタンのインデックスが異なる / 特殊なキーの使用
  - キーコンフィグを用意する
- 120Hz や 144Hz など、高リフレッシュレートのモニタでアニメーションが早く実行されてしまう
  - チュートリアル 03 をもとに、時間ベースのアニメーションに変更する

テスト目的で、`Graphics::SetVSyncEnabled(false);` で VSync をオフに、`System::Sleep(?ms)` で待ち時間を発生させることで、フレームレートが高い環境、低い環境を再現できます。


## 41.2 アプリの使い方がわかるようにする
ゲームやアプリをダウンロードした人が使い方に困らないよう、次のような資料を用意しましょう。

- アプリ内で十分な説明やチュートリアルを表示する
  - `Texture` でマニュアルを表示する
  - `VideoTexture` で動画を再生する
- アプリにマニュアルや README を同梱する
- Web 上にマニュアルや解説動画を用意する
- Web 上のマニュアルや解説動画に、アプリからアクセスできるようにする

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"How to play", Vec2{ 20, 20 }))
		{
			// Web ページをブラウザで開く
			System::LaunchBrowser(U"https://zenn.dev/reputeless/books/siv3d-documentation");
		}
	}
}
```

とくに、マウスを使って操作するのか、キーボードを使って操作するのかは、初めてのユーザが戸惑うポイントです。アプリ内でも表示をするのが良いでしょう。


## 41.3 実行ファイルのアイコンを変更する

### Windows の場合
App フォルダにある `icon.ico` を置き換えてプログラムを再ビルドすると、実行ファイル (.exe) のアイコンを変更できます。なお、Windows OS は古いアイコンをパソコンにキャッシュしているため、新しいアイコンに更新されたあとも、エクスプローラー上での見た目が変わらない場合があります。実行ファイル名を変更するか、Windows 機能の「ディスク クリーンアップ」で「縮小表示」をクリーンアップすることで即座にキャッシュをリフレッシュできます。

### macOS の場合
プロジェクトフォルダにある `icon.icns` を置き換えてプログラムを再ビルドすると、実行ファイル (.app) のアイコンを変更できます。


## 41.4 ウィンドウのタイトルを変更する
`Window::SetTitle(title)` でウィンドウのタイトルを変更できます。


## 41.5 ライセンスの表示手段を用意する
アプリを配布するときは、Siv3D および Siv3D が使用しているサードパーティ・ソフトウェアのライセンス表示が必要です。次のような手段でユーザがライセンス情報にアクセスできるようにしましょう。

- F1 キーを押したときに表示される HTML ファイルを配布アプリに同梱する
- F1 キーを押したときに表示されるライセンス情報を README 内に記述する
- F1 キーを押すことでライセンス情報を表示できることを README に明記する
- `LicenseManager::ShowInBrowser()` を呼ぶことでアプリ内からライセンスをオープンできるようにする

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// "Licenses" ボタンが押されたら
		if (SimpleGUI::Button(U"Licenses", Vec2{ 20, 20 }))
		{
			// ライセンス情報を表示
			LicenseManager::ShowInBrowser();
		}
	}
}
```

- `LicenseManager::EnumLicenses()` で取得できるライセンス情報を、アプリ内で表示する


## 41.6 Release ビルドを行う
配布する実行ファイルは「Release ビルド」でビルドでします。プログラムを Release ビルドすることで、デバッグ情報が埋め込まれない代わりに、小さなファイルサイズで高速に実行される実行ファイルが生成されます。


## 41.7 リソースの埋め込みをする
プログラムで使う画像や音声、テキストファイルを実行ファイルに埋め込むことで、実行ファイル単体でのアプリの配布が可能になります。「チュートリアル ?? リソースの埋め込み」を参照してください。


## 41.8 同梱するファイル
配布ファイルに含める必要があるのは、次のファイルやフォルダです。
- 実行ファイル (.exe または .app)
- プログラムで使う、埋め込みをしていない画像や音声・テキストファイル
- (Windows で `GlobalAudio::BusSetPitchShiftFilter()` を使用している場合) `dll` フォルダ
- (必要に応じて) アプリの説明等を記述したマニュアルや README.txt
- (必要に応じて) ライセンス情報を記述したファイル (41.5 参照)

です。つまり最小の場合、実行ファイル 1 つだけでの配布が可能です。


## 41.9 同梱する必要が無いファイル
次のフォルダやファイルは配布する必要がありません。

| ファイルやフォルダ | 同梱しなくてよい理由 |
|--|--|
|`engine` フォルダ | アプリケーションビルド時に、自動で実行ファイルに埋め込まれます |
|`example` フォルダ | プログラムで使用していない場合、同梱する必要はありません |
| アイコンファイル | アプリケーションビルド時に、自動で実行ファイルに埋め込まれます |
| `AS_DEBUG` フォルダ | スクリプト機能のデバッグログなので不要です |
| `Screenshot` フォルダ | ユーザがスクリーンショットを撮影したときに自動で作成されます |
| `Resource.rc` | アプリの実行には不要です |
| `Info.plist` | アプリの実行には不要です |
| `*.exp` や `*.lib`, `*.pdb` などビルド時に生成されるファイル | アプリの実行には不要です |


## 41.10 （上級者向け）ファイルサイズをさらに小さくする
`engine` フォルダのファイルのうち、いくつかのファイルは、プログラムで特定の機能を使用しない場合に限り、埋め込みが不要です。それらのファイルを埋め込まないことで配布ファイルのサイズを小さくできます。

Windows では　`Resource.rc` から該当ファイルをコメントアウトすることで、macOS / Linux では engine フォルダから該当ファイルを削除することで埋め込みをキャンセルできます。

ただし、誤って本来必要なファイルを埋め込まなかった場合、ユーザがアプリを実行した際にテキストが表示されない、音声が再生されないなどのトラブルの原因になるため、埋め込みのキャンセルは慎重に行ってください。

| ファイル | 埋め込みをキャンセルできるケース |
|--|--|
| `engine/font/mplus/mplus-1p-thin.ttf.zstdcmp` | `Typeface::Thin` を使用していない場合 |
| `engine/font/mplus/mplus-1p-light.ttf.zstdcmp` | `Typeface::Light` を使用していない場合 |
| `engine/font/mplus/mplus-1p-regular.ttf.zstdcmp` | デフォルトのフォント (`Typeface::Regular`) を使用していない場合 |
| `engine/font/mplus/mplus-1p-medium.ttf.zstdcmp` | `Typeface::Medium` を使用していない場合 |
| `engine/font/mplus/mplus-1p-bold.ttf.zstdcmp` | `Typeface::Bold` を使用していない場合 |
| `engine/font/mplus/mplus-1p-heavy.ttf.zstdcmp` | `Typeface::Heavy` を使用していない場合 |
| `engine/font/mplus/mplus-1p-black.ttf.zstdcmp` | `Typeface::Black` を使用していない場合 |
| `engine/font/noto-emoji/noto-cjk/NotoSansCJK-Regular.ttc.zstdcmp` | ASCII 文字以外のテキスト表示をしない場合 (v0.6.3 以降) |
| `engine/font/noto-emoji/NotoColorEmoji.ttf.zstdcmp` | `Typeface::ColorEmoji` や `Emoji` を使用していない場合 |
| `engine/font/noto-emoji/NotoEmoji-Regular.ttf.zstdcmp` | `Typeface::MonochromeEmoji` や `Print` 内での絵文字を使用していない場合 (v0.6.3 以降) |
| `engine/font/fontawesome/fontawesome-brands.otf.zstdcmp` | `Typeface::Icon_Awesome_Brand` や `Icon` を使用していない場合 |
| `engine/font/fontawesome/fontawesome-solid.otf.zstdcmp` | `Typeface::Icon_Awesome_Solid` や `Icon` を使用していない場合 |
| `engine/font/materialdesignicons/materialdesignicons-webfont.ttf.zstdcmp` | `Typeface::Icon_MaterialDesign` や `Icon` を使用していない場合 |
| `engine/soundfont/GMGSx.sf2.zstdcmp` | `GMInstrument` や `MIDI` ファイルの読み込みを使用しない場合 |

これらのファイルは、もともと Siv3D プログラムの実行時にローカルのパソコンにキャッシュされます。例えばプログラムで `Typeface::Heavy` を使っているのに `engine/font/mplus/mplus-1p-heavy.ttf.zstdcmp` を埋め込まなかった際、プログラムはパソコンに保存された以前のキャッシュからフォントを読み込むことで、手元の環境では問題なく表示されることがあることに注意してください。Windows の場合、Siv3D アプリのキャッシュフォルダは `ユーザ名/AppData/Local/Siv3D/` (隠しフォルダ) に作成されます。初使用のユーザの環境を再現する目的で、この Siv3D キャッシュフォルダの中身は安全に削除することができます。


## 41.11 アプリを公開する
必要なファイルを ZIP アーカイブで 1 つにまとめ、配布・ダウンロードしやすい形式にしましょう。アプリケーションを配布するプラットフォームとしては次のような方法があります。[動作環境](https://siv3d.github.io/ja-jp/download/windows/) (Windows 向け、64-bit 必須など) についても分かりやすく表示しておきましょう。

- 「[ふりーむ！](https://www.freem.ne.jp/)」「[itch.io](https://itch.io/)」などのゲーム公開サービスを使う
- 「[Booth](https://booth.pm/ja)」などの販売サイトで無料 / 有料配布する
- GitHub にファイルをアップロードする
- レンタルサーバー上にアップロードする
- Dropbox や Google Drive など、オンラインストレージのファイル共有リンクを使う
  - サービスによっては大量のトラフィックに対する制限がある場合があります
- CD や USB メモリなどの媒体に保存し、イベントで頒布する

大々的に公開する前に、協力者のパソコンで動作確認などをしておくとトラブルを抑制できます。


## 41.12 アプリを宣伝する
アプリをたくさんの人に知ってもらうには、開発中の時期から宣伝を始めておくことが望ましいです。SNS 上で、**スクリーンショット**や**映像**、一貫した**ハッシュタグ**を使い、アプリを PR しましょう。

Twitter で #Siv3D や #OpenSiv3D ハッシュタグを使ったツイートをすると、Siv3D の開発者や公式アカウントがリツイートする場合があります。
