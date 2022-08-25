---
title: "C++ 標準入出力の高速化"
free: true
---

## コード

```cpp
#include <iostream>

int main()
{
	std::cin.tie(0)->sync_with_stdio(0);
}
```

丁寧に書くと

```cpp
#include <iostream>

int main()
{
	std::cin.tie(nullptr);
	std::ios_base::sync_with_stdio(false);
}
```
