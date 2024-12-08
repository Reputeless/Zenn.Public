---
title: "Reducing Dangling References in C++ with [[lifetimebound]]"
emoji: "⌛"
type: "tech"
topics: ["cpp"]
published: false
---

## Key Points
- Modern C++ compilers are increasingly capable of detecting dangling references—references to objects that have already been destroyed.
- Some compilers support using the `[[lifetimebound]]` attribute as a compiler extension. Applying this attribute in certain situations can enable compile-time detection of dangling references.
- While this attribute cannot prevent all dangling references, library authors can significantly reduce the risk of such issues in user code by judiciously applying `[[lifetimebound]]` to appropriate functions and constructors.

## 1. Overview
Starting with GCC 13, the `-Wdangling-reference` warning can detect issues related to dangling references derived from the lifetime of temporary objects.

- [GCC Add warnings for common dangling problems](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=106393)

Additionally, Visual Studio 2022 (17.7 and later) and Clang 7 and later support the `[[lifetimebound]]` attribute as a compiler extension. When used, the compiler checks cases where the returned reference is expected to remain valid beyond the call, issuing warnings if it detects references to temporary objects.

- [Warning C26815 | Microsoft Learn](https://learn.microsoft.com/en-us/cpp/code-quality/c26815?view=msvc-170)
- [LLVM: [P0936R0] add [[clang::lifetimebound]] attribute](https://reviews.llvm.org/D49922)

In the example below, recent GCC versions and compilers supporting `[[lifetimebound]]` produce warnings when a reference could end up pointing to a destroyed temporary object.

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
    #define LIFETIMEBOUND [[msvc::lifetimebound]]
    #include <CppCoreCheck/Warnings.h>
    #pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS) // Enable lifetimebound warnings
#elif defined(__clang__) // Clang
    #define LIFETIMEBOUND [[clang::lifetimebound]]
#else
    #define LIFETIMEBOUND
#endif

const std::string& GetOption(const std::string& userOption LIFETIMEBOUND, const std::string& defaultOption LIFETIMEBOUND)
{
    if (userOption.empty()) {
        return defaultOption;
    }
    return userOption;
}

std::string MakeDefaultOption()
{
    return "The quick brown fox jumps over the lazy dog.";
}

int main()
{
    const std::string emptyOption = "";
    const std::string userOption = "The quick brown fox jumps over the lazy dog.";
    const std::string defaultOption = MakeDefaultOption();

    const std::string& s1 = GetOption(userOption, defaultOption);
    // ✅ OK

    const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
    // ⚠️ Warning

    const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
    // ⚠️ Warning

    std::cout << s1 << '\n';
    std::cout << s2 << '\n';
    std::cout << s3 << '\n';
}
```

**Example warnings with GCC:**
```sh
prog.cc: In function 'int main()':
prog.cc:38:28: warning: possibly dangling reference to a temporary [-Wdangling-reference]
   38 |         const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
      |                            ^~
prog.cc:38:43: note: 'const std::string' {aka 'const std::__cxx11::basic_string<char>'} temporary created here
   38 |         const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
      |                                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
prog.cc:41:28: warning: possibly dangling reference to a temporary [-Wdangling-reference]
   41 |         const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
      |                            ^~
prog.cc:41:73: note: 'std::string' {aka 'std::__cxx11::basic_string<char>'} temporary created here
   41 |         const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
      |                                                        ~~~~~~~~~~~~~~~~~^~
```

**Example warnings with Clang:**
```sh
prog.cc:38:36: warning: temporary bound to local reference 's2' will be destroyed at the end of the full-expression [-Wdangling]
   38 |         const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
      |                                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
prog.cc:41:49: warning: temporary bound to local reference 's3' will be destroyed at the end of the full-expression [-Wdangling]
   41 |         const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
      |                                                        ^~~~~~~~~~~~~~~~~~~
```

**Example warnings with Visual Studio Code Analysis:**
```
1>Main.cpp
C:\***\Main.cpp(38): warning C26815: The pointer is dangling because it points at a temporary instance that was destroyed.
C:\***\Main.cpp(41): warning C26815: The pointer is dangling because it points at a temporary instance that was destroyed.
```

## 2. Background
C++ often uses references and pointers to avoid unnecessary copies and write efficient code. However, referencing local variables or temporary objects can lead to dangling references once those objects go out of scope.

### 2.1 Referencing Local Variables
In the following example, `Concat` returns a reference to a local variable `result` that is destroyed once the function finishes, leaving a dangling reference.

```cpp
#include <iostream>
#include <string>

const std::string& Concat(const std::string& a, const std::string& b)
{
    const std::string result = (a + b);
    return result; // Returns a reference to a local variable → Dangling
}

int main()
{
    const std::string& result = Concat("Cat", "Dog");
    std::cout << result << '\n'; // Undefined behavior
}
```

This kind of straightforward mistake is easily detected by most compilers, which have long emitted warnings for such cases.

### 2.2 Example with `std::minmax`
A more subtle scenario involves misusing `std::minmax`. This function returns a `std::pair<const T&, const T&>`, holding references to the minimum and maximum arguments. Under certain conditions, this can result in dangling references.

```cpp
#include <iostream>
#include <algorithm>
#include <string>

std::string GetCat()
{
    return "cat cat cat cat cat cat cat cat";
}

std::string GetDog()
{
    return "dog dog dog dog dog dog dog dog";
}

int main()
{
    // Not OK: Returns references to temporaries
    auto result = std::minmax(GetCat(), GetDog());
    std::cout << "Min: " << result.first << ", Max: " << result.second << '\n'; // Undefined behavior
}
```

Here, the temporary objects returned by `GetCat()` and `GetDog()` expire right after `std::minmax` is called, leaving `result.first` and `result.second` as dangling references.

Older compilers could not detect such misuse of `std::minmax`, but new warnings and the application of the `[[lifetimebound]]` attribute to `std::minmax` now enable compilers to issue warnings for these cases.

- [Results on Clang 16](https://wandbox.org/permlink/5ntl9vYpObzTByOj) → [Results on Clang 17](https://wandbox.org/permlink/TDgPdXtwxQUlZDVO)
- [Use of `lifetimebound` on `std::minmax` arguments in libc++](https://github.com/llvm/llvm-project/blob/6b1c357acc312961743bef05f99120e7c68b2e25/libcxx/include/__cxx03/__algorithm/minmax.h#L28)
- [Use of `lifetimebound` on `std::minmax` arguments in MSVC STL](https://github.com/microsoft/STL/commit/7c7cc0c13dd75957b2d23952cb9b99a17193004b)

To avoid these issues, ensure that you use variables with adequate lifetimes rather than temporary objects:

```cpp
#include <iostream>
#include <algorithm>
#include <string>

std::string GetCat()
{
    return "cat cat cat cat cat cat cat cat";
}

std::string GetDog()
{
    return "dog dog dog dog dog dog dog dog";
}

int main()
{
    // OK: Store return values in variables, prolonging their lifetimes
    std::string a = GetCat(), b = GetDog();
    auto result = std::minmax(a, b);
    std::cout << "Min: " << result.first << ", Max: " << result.second << '\n'; // OK
}
```

## 3. How to Use `[[lifetimebound]]` and Its Effects
In Visual Studio 2022 (17.7 and later) and Clang 7 and later, applying `[[lifetimebound]]` to function parameters, return values, member functions, or constructor parameters gives compilers additional hints about object lifetimes:

- (1) When applied to a member function, it indicates that the returned reference is bound to the lifetime of the object.
- (2) When applied to function parameters, it indicates that the returned reference depends on the lifetime of these arguments.
- (3) When applied to constructor parameters, it indicates that the constructed object references the passed-in object’s lifetime.

If a referenced object is a temporary that will be destroyed before the referencing variable goes out of scope, the compiler can warn you.

### 3.1 Applying to Member Functions

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
    #define LIFETIMEBOUND [[msvc::lifetimebound]]
    #include <CppCoreCheck/Warnings.h>
    #pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS)
#elif defined(__clang__) // Clang
    #define LIFETIMEBOUND [[clang::lifetimebound]]
#else
    #define LIFETIMEBOUND
#endif

struct Holder {
    std::string value;

    const std::string& getValue() const LIFETIMEBOUND {
        return value;
    }
};

int main()
{
    {
        Holder holder;
        holder.value = "The quick brown fox jumps over the lazy dog.";
        const std::string& value = holder.getValue();
        // ✅ OK
        std::cout << value << '\n';
    }

    {
        const std::string& value = Holder{ "The quick brown fox jumps over the lazy dog." }.getValue();
        // ⚠️ Warning
        std::cout << value << std::endl;
    }
}
```

### 3.2 Applying to Function Arguments

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
    #define LIFETIMEBOUND [[msvc::lifetimebound]]
    #include <CppCoreCheck/Warnings.h>
    #pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS)
#elif defined(__clang__) // Clang
    #define LIFETIMEBOUND [[clang::lifetimebound]]
#else
    #define LIFETIMEBOUND
#endif

const std::string& GetOption(const std::string& userOption LIFETIMEBOUND, const std::string& defaultOption LIFETIMEBOUND)
{
    if (userOption.empty()) {
        return defaultOption;
    }
    return userOption;
}

std::string MakeDefaultOption()
{
    return "The quick brown fox jumps over the lazy dog.";
}

int main()
{
    const std::string emptyOption = "";
    const std::string userOption = "The quick brown fox jumps over the lazy dog.";
    const std::string defaultOption = MakeDefaultOption();

    const std::string& s1 = GetOption(userOption, defaultOption);
    // ✅ OK

    const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
    // ⚠️ Warning

    const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
    // ⚠️ Warning

    std::cout << s1 << '\n';
    std::cout << s2 << '\n';
    std::cout << s3 << '\n';
}
```

### 3.3 Applying to Constructor Parameters
While GCC’s `-Wdangling-reference` does not cover this scenario, Visual Studio and Clang do warn when `[[lifetimebound]]` is applied to constructor parameters.

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
    #define LIFETIMEBOUND [[msvc::lifetimebound]]
    #include <CppCoreCheck/Warnings.h>
    #pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS)
#elif defined(__clang__) // Clang
    #define LIFETIMEBOUND [[clang::lifetimebound]]
#else
    #define LIFETIMEBOUND
#endif

struct StringPiece {
public:
    StringPiece() = default;
    StringPiece(const std::string& s LIFETIMEBOUND)
        : data{s.data()}, size{s.size()} {}

    const char* data = nullptr;
    size_t size = 0;
};

std::string MakeString()
{
    return "The quick brown fox jumps over the lazy dog.";
}

int main()
{
    {
        const std::string s = MakeString();
        const StringPiece sp{ s };
        // ✅ OK
        std::cout << std::string_view{ sp.data, sp.size } << '\n';
    }

    {
        const StringPiece sp{ MakeString() };
        // ⚠️ Warning
        std::cout << std::string_view{ sp.data, sp.size } << '\n';
    }
}
```

### 3.4 Cases Where `[[lifetimebound]]` Doesn’t Help
`[[lifetimebound]]` only helps with relatively straightforward cases, such as references to temporaries. In more complicated scenarios, such as those involving container operations that invalidate references indirectly, it cannot detect issues:

```cpp
int main()
{
    {
        std::vector<int> v = { 200, 100 };
        auto result = std::minmax(v[0], v[1]);
        v.resize(1000); // Invalidate references by reallocation
        std::cout << result.first << ' ' << result.second << '\n';
    }

    {
        StringPiece sp;
        {
            std::string s = MakeString();
            sp = StringPiece{ s };
        } // s is destroyed here

        std::cout << std::string_view{ sp.data, sp.size } << '\n';
    }
}
```

### 3.5 Real-World Examples
The `[[lifetimebound]]` attribute has already been adopted in well-known libraries such as:

- [MSVC STL](https://github.com/microsoft/STL): Applied to functions like `std::min`, `std::max`, `std::minmax`, `std::clamp`, etc.
- [libc++](https://github.com/llvm/llvm-project): Applied to `std::min`, `std::max`, `std::minmax`, `std::clamp`, `std::move`, `std::forward`, `std::forward_like`, etc.
- [Abseil](https://github.com/abseil/abseil-cpp): Applied to various member functions of `absl::optional`, `absl::FixedArray`, `absl::Span`, and so forth.

## 4. Summary
By using GCC’s new warnings or the `[[lifetimebound]]` attribute supported by Visual Studio and Clang, you can detect certain cases of dangling references related to temporary objects at compile time.

Of course, this alone does not eliminate all dangling references. More powerful static analysis tools or sanitizers may be needed for thorough detection. Still, having compilers provide more information is a significant benefit to developers.

For library authors, adding `[[lifetimebound]]` to appropriate functions and constructors helps reduce the risk of dangling references in the codebases of library users.

## 5. Additional Notes
There is currently no proposal to add `[[lifetimebound]]` to the C++ standard, but related initiatives are underway:

- Clang is exploring lifetime annotations for a wider range of dangling reference scenarios.
    - [LLVM: [RFC] Lifetime annotations for C++](https://discourse.llvm.org/t/rfc-lifetime-annotations-for-c/61377)
- Proposals for more comprehensive lifetime safety, such as borrow checking, have also been published.
    - [D3390: Safe C++](https://safecpp.org/draft.html)

While practical implementation may still be far off, these efforts represent ongoing progress toward more robust lifetime safety in C++.
