# WinReg v1.1A
## High-level C++ Wrappers Around Low-level Windows Registry C-interface APIs

by Giovanni Dicanio

The Windows Registry C-interface API is  _very low-level_ and _hard_ to use.

I developed some **C++ wrappers** around this low-level Win32 API, to raise the semantic level, using C++ classes like `std::wstring`, `std::vector`, etc. instead of raw C-style buffers and low-level mechanisms. 

For example, the `REG_MULTI_SZ` registry type associated to double-NUL-terminated C-style strings is handled using a much easier higher-level `vector<wstring>`. My C++ code does the _translation_ between high-level C++ STL-based stuff and low-level Win32 C-interface APIs.

Moreover, Win32 error codes are translated to C++ exceptions.

The Win32 registry value types are mapped to C++ higher-level types according the following table:

| Win32 Registry Type  | C++ Type                     |
| -------------------- |:----------------------------:| 
| `REG_DWORD`          | `DWORD`                      |
| `REG_QWORD`          | `ULONGLONG`                  |
| `REG_SZ`             | `std::wstring`               |
| `REG_EXPAND_SZ`      | `std::wstring`               |
| `REG_MULTI_SZ`       | `std::vector<std::wstring>`  |
| `REG_BINARY`         | `std::vector<BYTE>`          |


I initially developed this code using Visual Studio 2015 with Update 3. 
Then, in this branch, I added **support for Visual Studio 2010 with SP1**, removing some C++11/14 features from
the WinReg code, to make it compilable with VS2010.

For example: instead of `constexpr`, I used `static const` for declaring compile-time constants.
Moreover, to ban copy I declared a private copy constructor and copy assignment operator, instead of using the C++11 `=delete` syntax, which isn't available in VS2010.
And so on.

The code compiles cleanly at `/W4` in both 32-bit and 64-bit builds. 

The library's code is contained in a **reusable** _header-only_ [`WinReg.hpp`](../vs2010-support/WinReg/WinReg/WinReg.hpp) file.

`WinRegTest.cpp` contains some demo/test code for the library: check it out for some sample usage.

The library exposes two main classes:

* `RegKey`: a tiny efficient wrapper around raw Win32 `HKEY` handles
* `RegException`: an exception class to signal error conditions

There are many member functions inside the `RegKey` class, that wrap raw Win32 registry C-interface APIs
in a convenient C++ way.

For example, you can simply open a registry key and get registry values with C++ code like this:

```c++
RegKey key{ HKEY_CURRENT_USER, L"SOFTWARE\\Connie" };
DWORD dw = key.GetDwordValue(L"SomeDwordValue");
wstring s = key.GetStringValue(L"SomeStringValue");
```

Or you can enumerate all the values under a key with this simple code:
```c++
auto values = key.EnumValues();
```
 
The library stuff lives under the `winreg` namespace.

See the [**`WinReg.hpp`**](../vs2010-support/WinReg/WinReg/WinReg.hpp) header for more details and **documentation**.
