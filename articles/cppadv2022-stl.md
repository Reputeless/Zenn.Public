---
title: "MSVC STL の最近の改良"
emoji: "🚅"
type: "tech"
topics: ["cpp"]
published: false
---

> この記事は [C++ Advent Calendar 2022](https://qiita.com/advent-calendar/2022/cxx) 22 日目の参加記事です。

Visual Studio の C++ 開発環境に含まれる C++ 標準ライブラリ (MSVC STL) は、2019 年 9 月にオープンソース化され、GitHub リポジトリで更新の様子を追跡できるようになりました。

https://github.com/microsoft/STL

リポジトリ上の最新コードは、リリース版の Visual Studio よりも数ヶ月ほど先行している点に注意が必要です。更新が反映されるタイミングは [Changelog](https://github.com/microsoft/STL/wiki/Changelog) で確認できます。

本記事では、MSVC STL のリポジトリで確認できる、最近実装された興味深い改良を 2 つ紹介します。

## 1. アルゴリズム関数のベクトル演算対応

https://github.com/microsoft/STL/pull/2434

範囲に対して検索の操作を行うアルゴリズム関数の一部が、適切な条件を満たす場合に、ベクトル演算 (SIMD) を用いて実行されるようになりました。

記事執筆時点での最新の MSVC STL では、`std::find()` は内部で次のような関数を呼びます。

https://github.com/microsoft/STL/blob/cae666016151ec3392fb7170639e0e4fcb9c548c/stl/inc/xutility#L5678-L5721

メモリ非連続な範囲（例えば `std::deque`）や非 Trivial な要素型 (例えば `std::string`）にも対応する汎用的な `find()` の実装は下記の部分です。

https://github.com/microsoft/STL/blob/cae666016151ec3392fb7170639e0e4fcb9c548c/stl/inc/xutility#L5714-L5720

しかし、検索する範囲がメモリ連続であり、なおかつ要素が 1 バイトである場合は、副作用なしで単純な `memchr()` に置き換えることができ、コンパイラによる効率的なコードの生成を助けることができます。下記の部分が、その考えに基づいて従来まで実装されていたコードです。

https://github.com/microsoft/STL/blob/cae666016151ec3392fb7170639e0e4fcb9c548c/stl/inc/xutility#L5699-L5708

しかし、1 バイトの要素を検索するような機会は多くなく、この最適化が役に立つケースは限定的でした。VS 2022 17.3 では、1, 2, 4, 8 バイトの整数型、ポインタ型、`std::byte` を検索する際に、SSE2 または AVX2 命令を用いる実装が新たに追加されました。

https://github.com/microsoft/STL/blob/cae666016151ec3392fb7170639e0e4fcb9c548c/stl/inc/xutility#L5682-L5697

`__std_find_trivial()` は最終的に次のような関数を呼び出します。

https://github.com/microsoft/STL/blob/8ddf4da23939b5c65587ed05f783ff39b8801e0f/stl/src/vector_algorithms.cpp#L1270-L1315

該当ソースファイルを開いて全体を読むと、`_mm_cmpeq_epi32()` や `_mm256_cmpeq_epi32()` のような SSE2 / AVX2 の整数比較命令が呼ばれることがわかります。

#### ベンチマーク

配列 `T[8192]` から `std::ranges::find()` で要素を検索する処理 1,000,000 回の所要時間を計測した [ベンチマーク](https://github.com/microsoft/STL/pull/2434#:~:text=%F0%9F%8F%81,Perf%20benchmark) によると、ベクトル演算の対応によって 2～9 倍の実行速度向上効果が報告されています。

|配列 | 従来 | SIMD 実装 | 速度向上 |
|:--|--|--|--|
| `std::uint8_t[8192]` | 0.123933s | 0.0566276s | 2.19 倍 |
| `std::uint16_t[8192]` | 0.911876s | 0.0979886s | 9.31 倍 |
| `std::uint32_t[8192]` | 0.917381s | 0.185901s | 4.93 倍 |
| `std::uint64_t[8192]` | 0.975016s	 | 0.357151s | 2.73 倍 |


#### ベクトル演算に対応した関数
`std::find()` 以外の関数についても、ベクトル演算対応が進められています。下記に現時点での状況をまとめます。

| 関数 | 対応バージョン |
|--|--|
|`std::swap_ranges()` | VS 2022 以前 |
|`std::ranges::swap_ranges()` | VS 2022 以前 |
|`std::array::swap()` | VS 2022 以前 |
|`std::reverse()` | VS 2022 以前 |
|`std::ranges::reverse()` | VS 2022 以前 |
|`std::reverse_copy()` | VS 2022 以前 |
|`std::ranges::reverse_copy()` | VS 2022 以前 |
|`std::find()` | VS 2022 17.3 |
|`std::ranges::find()` | VS 2022 17.3 |
|`std::count()` | VS 2022 17.3 |
|`std::ranges::count()` | VS 2022 17.3 |
|`std::min_element()` | VS 2022 17.4 |
|`std::ranges::min_element()` | VS 2022 17.4 |
|`std::max_element()` | VS 2022 17.4 |
|`std::ranges::max_element()` | VS 2022 17.4 |
|`std::minmax_element()` | VS 2022 17.4 |
|`std::ranges::minmax_element()` | VS 2022 17.4 |

リポジトリの Issues から、次のような関数についてもベクトル演算対応が検討されていることがわかります。

| 関数 | 関連 Issue |
|--|--|
|`std::search()` | [#2453](https://github.com/microsoft/STL/issues/2453) |
|`std::ranges::search()` | [#2453](https://github.com/microsoft/STL/issues/2453) |
|`std::ranges::min()` | [#2803](https://github.com/microsoft/STL/issues/2803) |
|`std::ranges::max()` | [#2803](https://github.com/microsoft/STL/issues/2803) |
|`std::ranges::minmax()` | [#2803](https://github.com/microsoft/STL/issues/2803) |
|`std::find_last()` | [#3274](https://github.com/microsoft/STL/issues/3274) |
|`std::ranges::find_last()` | [#3274](https://github.com/microsoft/STL/issues/3274) |


## 2. 乱数の一様分布アルゴリズムの高速化

乱数生成器から、一定の範囲に一様分布する整数を得るときには、C++11 で導入された `std::uniform_int_distribution` を使います。その内部で使えるアルゴリズムについて、2019 年に新しい高速な方法が発表されました。

https://lemire.me/blog/2019/06/06/nearly-divisionless-random-integer-generation-on-various-systems/

- 論文: https://arxiv.org/abs/1805.10941

基本的には除算を減らしたことが高速化に貢献しています。次の記事に概要がまとまっています。

https://lemire.me/blog/2019/09/28/doubling-the-speed-of-stduniform_int_distribution-in-the-gnu-c-library/

この改良アルゴリズムは、2020 年に [libstdc++ にマージ](https://gcc.gnu.org/git/?p=gcc.git;a=blobdiff;f=libstdc%2B%2B-v3/include/bits/uniform_int_dist.h;h=ecb8574864aee10b9ea164379fffef27c7bdb0df;hp=6e1e3d5fc5fe8f7f22e62a85b35dc8bfa4743372;hb=98c37d3bacbb2f8bbbe56ed53a9547d3be01b66b;hpb=6ce2cb116af6e0965ff0dd69e7fd1925cf5dc68c) され、[約 2 倍の速度向上](https://lemire.me/blog/2019/09/28/doubling-the-speed-of-stduniform_int_distribution-in-the-gnu-c-library/)が報告されました。

MSVC STL においてもそのアルゴリズムを採用することになり、VS 2022 17.5 であたらしい実装がマージされました。下記の Conversation では、およそ 1～2 倍前後の高速化、とくに x86 では `std::mt19937`, x64 では `std::mt19937_64` とターゲットプラットフォームに合った乱数生成器を使ったときに効果が顕著であったことが報告されています。

https://github.com/microsoft/STL/pull/3012


## おわりに
今回紹介したように、標準ライブラリの関数は高度な最適化が行われていて、自前でループを書くよりも数倍高速に実行されるケースがあります。こうした標準ライブラリ実装の改良の恩恵を受けられるよう、自身のコードを見直してみるとよいでしょう。

本記事では、標準ライブラリ実装の高速化に注目しましたが、MSVC STL の Changelog および関連 Issues は、変更内容やその目的などが読みやすく整理されていて、C++ 規格の学習や、C++ におけるライブラリ設計の良い参考資料になります。ぜひ活用してみてください。

https://github.com/microsoft/STL/wiki/Changelog

https://github.com/microsoft/STL/issues

