---
title: "チュートリアル 33 | 画像処理"
free: true
---

# 33. 画像処理
この章では、画像処理プログラミングを行い、結果をシーンに表示する方法を学びます。

## 33.1 Image クラスの基本
Siv3D で画像データを扱うときは `Image` クラスを使うのが便利です。`Image` クラスでは、画像データを二次元配列クラス `Gird` のようなインタフェースで扱えます。

`Image` クラスは実質的には次のような構造です。
```cpp
// 説明のため簡略化
class Image
{
	Array<Color> m_data;
	uint32 m_width;
	uint32 m_height;
};
```

`Image` 型の変数 `image` に対する、インデックスによる要素へのアクセス `image[y][x] = value;` は、内部では `m_data[y * m_width + x] = value;` になります。また、`Point` 型の値 `pos` による `image[pos] = value;` は、`m_data[pos.y * m_width + pos.x] = value;` になります。

なお、`Color` 型は r, g, b, a の各色を `uint8` 型で保持する、4 バイトの構造体です。

```cpp
// 説明のため簡略化
struct Color
{
	uint8 r;
	uint8 g;
	uint8 b;
	uint8 a;
};
```


```cpp

```


## 33.2

```cpp

```


## 33.3

```cpp

```


## 33.4

```cpp

```


## 33.6

```cpp

```


## 33.7

```cpp

```


## 33.8

```cpp

```


## 33.9

```cpp

```


## 33.10

```cpp

```


## 33.11

```cpp

```
