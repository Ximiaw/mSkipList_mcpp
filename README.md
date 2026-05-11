# mSkipList_mcpp

使用 [mcpp](https://github.com/mcpp-community/mcpp) 构建工具构建的 [mSkipList](https://github.com/Ximiaw/mSkipList) 跳表数据结构版本。

本项目将原 mSkipList 的单头文件实现适配为 **C++ 模块（Module）** 格式，通过 mcpp 进行依赖管理、构建和打包。

> ⚠️ **重要提示：性能差异**
>
> 本项目是 mSkipList 原项目的 mcpp 构建适配版本。
>
> **本项目在运行性能上不如原项目 [mSkipList](https://github.com/Ximiaw/mSkipList)，如果追求最佳运行性能，请直接使用原项目。**

## 项目简介

mSkipList 是一个基于 C++20 实现的通用跳表（Skip List）数据结构，支持多字段数据存储、任意字段作为主键、以及自定义内存分配策略。本项目是它的 mcpp 模块化构建版本，源代码位于 `src/mSkipList_mcpp.cppm`。

### 原项目特性

- **多字段数据支持**：通过可变参数模板存储任意类型、任意数量的字段
- **灵活的主键选择**：可通过模板参数指定任意字段作为排序和查找主键
- **重复键更新机制**：插入已存在的 key 时自动覆盖数据，而非新增节点
- **STL 风格迭代器**：提供 `begin()` / `end()` 双向迭代器，支持范围遍历
- **范围查询（Range）**：支持按主键范围返回子区间迭代器，用于区间遍历

## 前置要求

- [mcpp](https://github.com/mcpp-community/mcpp) 构建工具（安装方式见 mcpp 项目文档）
- GCC 15（mcpp 默认工具链，已验证可用）

## 快速开始

### 作为依赖使用

在你的 `mcpp.toml` 中添加依赖：

```toml
[dependencies]
mSkipList_mcpp = { git = "https://github.com/Ximiaw/mSkipList_mcpp.git",branch = "main" }
```

然后在你的模块中导入：

```cpp
import mSkipList_mcpp;
import std;

using namespace msl;
using namespace std;

struct task {
    size_t priority = 0;
    size_t number = 0;
    task(size_t p = 0, size_t n = 0) : priority(p), number(n) {};

    bool operator==(const task& other) const {
        return priority == other.priority && number == other.number;
    }
    auto operator<=>(const task& other) const = default;
};

int main() {
    auto sl = make_mSkipList<0, task, int>();

    for (size_t i = 0; i < 5; i++) {
        for (size_t j = 0; j < 10; j++) {
            sl.insert(task{i, j}, i + j);
        }
    }

    for (auto it : sl.range(task{3, 3}, task{4, 2})) {
        cout << "Priority: " << get<0>(it.data()).priority
             << "\tNumber: " << get<0>(it.data()).number
             << "\tTask: " << it.ref<int>(1) << endl;
    }
}
```

### 构建本项目

```bash
# 克隆项目
git clone https://github.com/Ximiaw/mSkipList_mcpp.git
cd mSkipList_mcpp

# 使用 mcpp 构建
mcpp build

# 运行测试
mcpp test
```

## 项目结构

```
mSkipList_mcpp/
├── src/
│   └── mSkipList_mcpp.cppm    # 模块源码
├── tests/
│   ├── test0.cpp              # 功能测试
│   ├── test1.cpp              # 迭代器测试
│   ├── test2.cpp              # 性能测试
│   └── test3.cpp              # 微基准测试
├── mcpp.toml                  # mcpp 构建配置
└── LICENSE                    # MIT 许可证
```

## API 参考

主要接口与原项目保持一致，详见 [mSkipList 原项目文档](https://github.com/Ximiaw/mSkipList)。

| 方法 | 说明 |
|------|------|
| `insert(T_D... args)` | 插入数据；若主键已存在则覆盖 |
| `get<Type>(key, field_index)` | 按主键查询指定字段的引用 |
| `erase(key)` / `erase(it)` | 删除指定主键的节点 |
| `contain(key)` | 检查指定主键是否存在 |
| `begin()` / `end()` | 返回头尾迭代器，支持范围遍历 |
| `range(left, right)` | 按主键范围返回子区间迭代器（闭区间 `[left, right]`） |
| `size()` | 返回当前节点数量 |

## 测试

本项目包含四组测试：

- **test0.cpp** - 功能测试
- **test1.cpp** - 迭代器测试
- **test2.cpp** - 性能对比测试
- **test3.cpp** - 微基准测试

## 许可证

本项目采用 MIT 许可证，详见 [LICENSE](LICENSE) 文件。

## 相关链接

- **原项目**：[Ximiaw/mSkipList](https://github.com/Ximiaw/mSkipList) - mSkipList 的官方实现，追求最佳性能请使用此版本
- **构建工具**：[mcpp-community/mcpp](https://github.com/mcpp-community/mcpp) - 现代 C++ 构建工具
