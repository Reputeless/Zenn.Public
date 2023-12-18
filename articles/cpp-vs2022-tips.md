---
title: "C++ プログラミングの生産性を少し改善する Visual Studio の機能（2023）"
emoji: "⌨️"
type: "tech"
topics: ["cpp"]
published: true
---

> [C++ Advent Calendar 2023](https://qiita.com/advent-calendar/2023/cxx) および [Siv3D Advent Calendar 2023](https://qiita.com/advent-calendar/2023/siv3d) 18 日目の記事です。

本記事では、Visual Studio での C++ プログラミングの生産性向上に役立つ IDE の機能を、最近のバージョンで追加された無料機能を中心に紹介します。

## 1. コーディング用の合字フォント
日本語環境の Visual Studio のデフォルトのフォントは **MS ゴシック**なので、フォントにこだわりのない人は MS ゴシックを使い続けているかもしれません。英語環境では Visual Studio 2022 から **Cascadia** がデフォルトのフォントになっています。

Cascadia は、Microsoft が 2019 年にリリースしたコーディング用のオープンソースフォントで、合字（リガチャ）の有無に応じて **Cascadia Code** と **Cascadia Mono** があります。合字を有効にすると特定の 2 つ以上の文字が、より洗練された 1 つのグリフで表現されます。人によって好みは分かれますが、一般にコードの可読性を向上させると考えられています。C++ のコードにおいては、`!=` や `>=`, `<=`, `++`, `<<`, `>>`, `^=`, `/=`, `::`, `&&`, `||`, `//`, `///` などの見た目が影響を受けます。

![](https://raw.githubusercontent.com/microsoft/cascadia-code/main/images/ligatures.png)
*▲ Cascadia Code に収録されているおもな合字*

![](https://storage.googleapis.com/zenn-user-upload/b1655934400b-20231218.gif)
*▲ Cascadia Code（合字あり）と Cascadia Mono（合字なし）の比較アニメーション。<br>合字を有効にしても本来の字幅は維持される*

同じコードを、MS ゴシック、以前の英語版 Visual Studio のデフォルトフォント Consolas, そして Cascadia Code で比較してみましょう。

| MS ゴシック | Consolas | Cascadia Code |
|:--:|:--:|:--:|
| ![](https://storage.googleapis.com/zenn-user-upload/a46eaa30efa0-20231218.png) | ![](https://storage.googleapis.com/zenn-user-upload/d7ce83191a5c-20231218.png) | ![](https://storage.googleapis.com/zenn-user-upload/e47b6127db5f-20231218.png) |

#### 設定の手順
Cascadia は標準でインストールされています。**メニュー > ツール > オプション > 環境 > フォントおよび色** からフォントを変更できます。

![](https://storage.googleapis.com/zenn-user-upload/1bf510e2d084-20231218.png)


## 2. Doxygen スタイルのコメントの補助
Visual Studio 2019 16.6 から、**XML コメントおよび Doxygen スタイルのコメントの記述を補助する機能**が追加されました。C++ のコードにおいて `///` で始まるコメントを入力すると、例えば関数の前であれば、引数や戻り値、関数の説明を入力するためのテンプレートが自動的に挿入されます。

![](https://storage.googleapis.com/zenn-user-upload/05a8c7ab8292-20231218.gif)
*▲ XML コメントの補助*

![](https://storage.googleapis.com/zenn-user-upload/d8031a172c69-20231218.gif)
*▲ Doxygen スタイルのコメントの補助*

また、XML コメントと Doxygen スタイルのコメントどちらの内容も、ツールチップによるヒント表示に反映されるようになりました。

![](https://storage.googleapis.com/zenn-user-upload/3459b5d3e49a-20231218.png)
*▲ ツールチップによるヒント表示*

#### 設定の手順
**メニュー > ツール > オプション > テキストエディター > C/C++ > コードスタイル > 全般** から、XML コメントまたは Doxygen スタイルのコメントの補助を有効にできます。

![](https://storage.googleapis.com/zenn-user-upload/bc1a81b42415-20231218.png)


## 3. コンパイラのメッセージの色分け
Visual Studio の拡張機能 [**VSColorOutput64**](https://marketplace.visualstudio.com/items?itemName=MikeWard-AnnArbor.VSColorOutput64) を使うと、コンパイラのメッセージを色分けして表示できます。例えば、エラーメッセージを赤、警告メッセージをオレンジ色で表示するように設定すると、大量のメッセージの中から重要な情報を見つけやすくなります。

![](https://storage.googleapis.com/zenn-user-upload/b6d5f3346408-20231218.png)
*▲ 色分けされたメッセージ*

#### 設定の手順
Visual Studio Marketplace または Visual Studio の拡張機能マネージャーから VSColorOutput64 をインストールします。インストール後、**メニュー > ツール > オプション > VSColorOutput64** から設定画面を開き、各項目の色を変更できます。

![](https://storage.googleapis.com/zenn-user-upload/2fda1e98cfa4-20231218.png)

この拡張を有効にすると、コンパイラのメッセージに作者への寄付を促すテキストが表示されるようになりますが、上記の `Yes I Donated!` オプションを `True` に変更すれば非表示にできます。[筆者は GitHub Sponsors で作者に寄付をした](https://github.com/sponsors/mike-ward)ので、このオプションを `True` にしています。


## 4. スペルチェック
Visual Studio 2022 17.5 から、コードに含まれる英単語のスペルミスを検出する機能が追加されました。ツールチップから修正候補を選択して、一括修正することもできます。

![](https://storage.googleapis.com/zenn-user-upload/edd7e7fb4bee-20231218.png)
*▲ スペルミスが検出された箇所に青波線が表示される*

#### 設定の手順
2023 年 2 月にリリースされた Visual Studio 2022 17.5 から標準で搭載されています。**メニュー > 編集 > 詳細 > スペルチェッカーを切り替える** から有効無効を切り替えられます。Visual Studio のメインツールバー上のボタンからも切り替えられます。

![](https://storage.googleapis.com/zenn-user-upload/a83aa667ac16-20231218.png)


## 5. クラスや union のメモリレイアウトの可視化
Visual Studio 2022 17.8 から、`class` や `struct`, `union` のサイズやアライメントがツールチップ上で表示されるようになりました。また、Visual Studio 2022 17.9 からは各メンバのオフセット（メモリレイアウト）を、ビジュアルで確認できるようになりました。

![](https://storage.googleapis.com/zenn-user-upload/086edc2914fe-20231218.png)
*▲ サイズやアライメントがツールチップ上に表示される*

![](https://storage.googleapis.com/zenn-user-upload/b029ce2fe464-20231218.png)
*▲ 大きい！*

![](https://storage.googleapis.com/zenn-user-upload/f53df9689885-20231218.png)
*▲ 各メンバのオフセットをビジュアルで確認できる*

#### メモリレイアウトの可視化の例

:::details 例 1. 他クラスをメンバに持つクラス
```cpp
struct Point
{
	int x, y;
};

struct Line
{
	Point start, end;
};
```
![](https://storage.googleapis.com/zenn-user-upload/1db46e8e6bee-20231218.png)
![](https://storage.googleapis.com/zenn-user-upload/71dbfa67a084-20231218.png)
:::

:::details 例 2. アライメントの違い
```cpp
struct Float4
{
	float x, y, z, w;
};

struct alignas(16) Float4A
{
	float x, y, z, w;
};
```
![](https://storage.googleapis.com/zenn-user-upload/ff25483569f4-20231218.png)
![](https://storage.googleapis.com/zenn-user-upload/3ad4bcfb434e-20231218.png)
:::

:::details 例 3. メンバの並び順の違い
```cpp
struct Object1
{
	short a;
	int b;
	short c;
	double d;
};

struct Object2
{
	int b;
	short a;
	short c;
	double d;
};
```
![](https://storage.googleapis.com/zenn-user-upload/34876996d3f4-20231218.png)
![](https://storage.googleapis.com/zenn-user-upload/11a90919b760-20231218.png)
:::

:::details 例 4. ビットフィールド
```cpp
struct HeightFieldPixel
{
	int height : 16;
	unsigned materialIndex0 : 7;
	unsigned tessellationFlag : 1;
	unsigned materialIndex1 : 7;
	unsigned unused : 1;
};
```
![](https://storage.googleapis.com/zenn-user-upload/6a05e75cde17-20231218.png)
:::

:::details 例 5. union
```cpp
union FloatUint
{
	float f;
	unsigned i;
};
```
![](https://storage.googleapis.com/zenn-user-upload/21b46760b8fc-20231218.png)
:::


#### 設定の手順
ツールチップでのサイズやアライメントの表示は 2023 年 11 月にリリースされた Visual Studio 2022 17.8 から標準で有効になっています。メモリレイアウトの可視化は Visual Studio 2022 17.9 Preview から標準で有効になっていています。前述のサイズ・アライメントツールチップ上の「メモリレイアウト」をクリックすることで新しいタブに表示されます。


## まとめ
本記事では、Visual Studio での C++ プログラミングの生産性向上に役立つ最近の IDE 機能を 5 つ紹介しました。いずれも無料で利用できます。ぜひ活用してみてください。

