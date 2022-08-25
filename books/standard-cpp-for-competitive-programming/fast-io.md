---
title: "C++ 標準入出力の高速化"
free: true
---

## コード

C++ の入出力ストリームのみを使用し、複数のスレッドから C++ 入出力ストリームを使用しないプログラムであれば、`std::cin.tie(0)->sync_with_stdio(0);` をすべての入出力より前に呼ぶことで、安全に同期プログラムの実行時間を短縮できます。

- 参考:
  - [&lt;ios&gt;, &lt;iomanip&gt; | 3.1 C 言語の入出力ストリームとの同期を無効にする](https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/library-ios-iomanip#3.1-c-%E8%A8%80%E8%AA%9E%E3%81%AE%E5%85%A5%E5%87%BA%E5%8A%9B%E3%82%B9%E3%83%88%E3%83%AA%E3%83%BC%E3%83%A0%E3%81%A8%E3%81%AE%E5%90%8C%E6%9C%9F%E3%82%92%E7%84%A1%E5%8A%B9%E3%81%AB%E3%81%99%E3%82%8B)

```cpp
#include <iostream>

int main()
{
	std::cin.tie(0)->sync_with_stdio(0);
}
```

丁寧に書くと次のようになりますが、実行されコードは同じです。

```cpp
#include <iostream>

int main()
{
	std::cin.tie(nullptr);
	std::ios_base::sync_with_stdio(false);
}
```
