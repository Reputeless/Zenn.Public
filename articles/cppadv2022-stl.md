---
title: "MSVC STL の最近の改良"
emoji: "🎄"
type: "tech"
topics: ["cpp"]
published: false
---

> この記事は [C++ Advent Calendar 2022](https://qiita.com/advent-calendar/2022/cxx) 22 日目の参加記事です。

Visual Studio の C++ 開発環境に同梱される C++ 標準ライブラリは、2019 年 9 月にオープンソース化され、ライブラリの更新の様子を GitHub リポジトリで追跡できるようになりました。

https://github.com/microsoft/STL

リポジトリのコードは、リリース版の Visual Studio よりも数ヶ月先行している点に注意が必要です。更新が反映されるタイミングは [Changelog](https://github.com/microsoft/STL/wiki/Changelog) にまとめられています。

本記事では、MSVC の標準ライブラリ実装に直近で導入された興味深い改良を 3 つ紹介します。

## 1. アルゴリズム関数の SIMD 対応

https://github.com/microsoft/STL/pull/2434


## 2. 演算子の Hidden friends 対応

https://github.com/microsoft/STL/pull/2797


## 3. 乱数の一様分布アルゴリズムの高速化

https://github.com/microsoft/STL/pull/3012


## おわりに
MSVC STL の　Changelog と、関連 Issues は読みやすく整理されているため、C++ 最新規格の学習や、C++ におけるライブラリ設計の参考資料になります。ぜひ活用してみてください。
