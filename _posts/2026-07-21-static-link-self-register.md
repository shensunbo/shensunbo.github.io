---
layout:       post
title:        "C++ 静态链接时自注册对象被裁剪的问题"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - c++
    - linker
---

## 现象

某 TCP 命令系统注册了一个全局静态对象，期望在 main() 之前自动注册到命令注册表：

```cpp
class Command {
    Command(const std::string& name) {
        Registry::get().add(name, this);
    }
};

// 全局静态变量，构造函数会执行注册
Command g_myCmd("hello");
```

代码编译为静态库（`.a`），最终链接到可执行文件或共享库（`.so`）。运行时发送 `hello` 命令，返回：

```
"hello" is not recognized as a valid command.
Type "help" to get a list of all available commands
```

命令没有被注册。

## 为什么

### 静态库的"按需提取"机制

`.a` 文件本质是目标文件（`.o`）的归档包。链接器处理静态库时，不会把整个 `.a` 都链接进去，而是遍历每个 `.o`，只提取那些**提供了被其他已链接代码引用的符号**的目标文件。

```python
# 伪代码
for obj in library.a:
    if obj 定义了 needed 中任意符号:
        提取 obj → 链接到最终产物
    else:
        跳过 obj  # 没被任何人引用
```

链接器只看符号，不看副作用。

### C++ 自注册模式的盲区

```cpp
// 编译为 cmd_registry.cpp → cmd_registry.o
Command g_myCmd("hello");

int main() {
    // 从未直接引用 g_myCmd
    Registry::get().dispatch(std::cin, std::cout);
}
```

- `main.o` 引用了 `Registry::get()` 和 `Registry::dispatch()`
- `cmd_registry.o` 定义了 `g_myCmd` 的构造函数，但 **没有其他 .o 引用 `g_myCmd` 这个符号**

链接器视角：没人要这个符号 → 跳过 `cmd_registry.o` → 构造函数永远不执行。

注册的副作用对链接器完全透明。

### 加上 `--gc-sections` 更彻底

Android NDK 等工具链默认开启：

```
-ffunction-sections -fdata-sections -Wl,--gc-sections
```

每个函数和全局变量被分配到独立段。即使之前被"按需提取"的 `.o`，如果其中某个段没有被其他段引用，该段也可能被逐个裁剪。双重过滤下，纯副作用的自注册对象更难存活。

> 注：debug 和 release 构建都默认开启此选项。

## 解决方案

### 方案一：`--whole-archive`（推荐）

链接时强制包含整个静态库：

```cmake
target_link_libraries(my_target PRIVATE
    "-Wl,--whole-archive" my_lib "-Wl,--no-whole-archive"
    other_libs...)
```

`--whole-archive` 告诉链接器：不要检查引用，把这个 `.a` 里所有 `.o` 都包含进来。

### 方案二：显式引用

在入口点代码中引用关键符号，让链接器知道它有用：

```cpp
// main.cpp — 在任意被引用的编译单元中
extern Command g_myCmd;
void* keep_myCmd = &g_myCmd;  // 强制包含 cmd_registry.o
```

### 方案三：改用 OBJECT 库

CMake OBJECT 库的所有 `.o` 总是会被包含：

```cmake
# 改为 OBJECT 库
add_library(my_lib OBJECT ${SOURCES})
# 链接时直接引用对象
target_link_libraries(my_target PRIVATE $<TARGET_OBJECTS:my_lib>)
```

## 如何验证

```bash
# 检查静态库中是否有该符号
nm libmy_lib.a | grep g_myCmd

# 检查最终产物中是否有该符号
nm libmy_target.so | grep g_myCmd
# 如果 .a 中有但 .so 中没有 → 被裁剪了
```

## 总结

| 情况 | 结果 |
|------|:----:|
| 同一编译单元内直接调用 | ✅ 正常 |
| 被其他 .o 引用的函数 | ✅ 正常 |
| 仅通过全局静态对象构造注册 | ❌ 被裁剪 |
| 加上 `--whole-archive` | ✅ 强制包含 |

自注册模式是 C++ 中常见的"隐式约定"，但链接器只承认符号引用。理解静态库的按需提取行为，才能在设计插件系统、命令框架、工厂注册等场景时避开这个坑。
