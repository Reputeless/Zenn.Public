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

リポジトリ上のコードは、リリース版の Visual Studio よりも数ヶ月先行している点に注意が必要です。更新が反映されるタイミングは [Changelog](https://github.com/microsoft/STL/wiki/Changelog) にまとめられています。

本記事では、MSVC の標準ライブラリの実装に、直近で導入された興味深い改良を 3 つ紹介します。

## 1. アルゴリズム関数のベクトル演算対応

https://github.com/microsoft/STL/pull/2434

範囲に対して検索や操作を行うアルゴリズム関数の一部が、適切な条件を満たす場合に、ベクトル演算 (SIMD) を用いて実行されるようになりました。

例えば、最新の MSVC STL では `std::find()` の内部で次のような関数が呼ばれます。

https://github.com/microsoft/STL/blob/cae666016151ec3392fb7170639e0e4fcb9c548c/stl/inc/xutility#L5678-L5721

メモリ上で非連続な範囲（例えば `std::deque`）、非 Trivial な要素型 (例えば `std::string`）にも対応するための汎用的な実装は下記の部分です。

```cpp
for (; _First != _Last; ++_First) {
	if (*_First == _Val) {
		break;
	}
}

return _First;
```

しかし、検索する範囲がメモリ連続であり、なおかつ要素が 1 バイトである場合は、副作用なしで単純な `memchr()` に置き換えて、コンパイラによる効率的なコードの生成を助けることができます。下記の部分が、その考えに基づいて従来まで実装されていたコードです。

```cpp
if constexpr (sizeof(_Iter_value_t<_InIt>) == 1) {
	const auto _First_ptr = _To_address(_First);
	const auto _Result    = static_cast<remove_reference_t<_Iter_ref_t<_InIt>>*>(
		_CSTD memchr(_First_ptr, static_cast<unsigned char>(_Val), static_cast<size_t>(_Last - _First)));
	if constexpr (is_pointer_v<_InIt>) {
		return _Result ? _Result : _Last;
	} else {
		return _Result ? _First + (_Result - _First_ptr) : _Last;
	}
}
```

しかし、1 バイトの要素を検索するような機会は多くなく、この最適化が役に立つケースは限定的でした。VS 2022 17.3 では、1, 2, 4, 8 バイトの整数型、ポインタ型、`std::byte` を検索する際に、SSE2 または AVX2 命令を用いるような実装が追加されました。

https://github.com/microsoft/STL/blob/cae666016151ec3392fb7170639e0e4fcb9c548c/stl/inc/xutility#L5682-L5697

ここで呼ばれる `__std_find_trivial()` は最終的に次のような関数を呼び出します。

https://github.com/microsoft/STL/blob/8ddf4da23939b5c65587ed05f783ff39b8801e0f/stl/src/vector_algorithms.cpp#L1270-L1315

配列 `T[8192]` から `std::ranges::count()` で要素を検索する処理を 1,000,000 回行うのにかかった時間を計測した [ベンチマーク](https://github.com/microsoft/STL/pull/2434#:~:text=%F0%9F%8F%81,Perf%20benchmark) によると、実行速度は 2～9 倍向上しています。

|配列 | 従来 | SIMD 実装 | 速度向上 |
|:--|--|--|--|
| `std::uint8_t[8192]` | 0.123933s | 0.0566276s | 2.19 倍 |
| `std::uint16_t[8192]` | 0.911876s | 0.0979886s | 9.31 倍 |
| `std::uint32_t[8192]` | 0.917381s | 0.185901s | 4.93 倍 |
| `std::uint64_t[8192]` | 0.975016s	 | 0.357151s | 2.73 倍 |




## 2. 演算子の Hidden friends 対応

https://github.com/microsoft/STL/pull/2797


## 3. 乱数の一様分布アルゴリズムの高速化

https://github.com/microsoft/STL/pull/3012


## おわりに
MSVC STL の Changelog および関連 Issues は読みやすく整理されているため、C++ 最新規格の学習や、C++ におけるライブラリ設計の参考資料になります。ぜひ活用してみてください。
