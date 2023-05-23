# Learn Zig

学习 Zig 语言。

## 基础

霑。

### 预备知识

一、主函数

普通的 `.zig` 文件需要主函数（程序的入口），库 `.zig` 文件则不需要。

```zig
pub fn main() !void {}
```

二、Hello World

一个 Hello World 程序。

```zig
const std = @import("std");

pub fn main() !void {
    const stdout = std.io.getStdOut().writer();
    try stdout.print("Hello, {s}!\n", .{"world"});
}
```

三、调试

霑：

- `std.debug.print`，输出内容到 `stderr`
- `std.debug.assert`，断言

四、注释

霑：

- 单行注释，`// ...`
- 文档注释，`/// ...`
- 顶级文档注释，`//! ...`
- 没有多行注释，例如 `/* ... */`

### 风格

推荐的风格：

- 缩进是空格，单位缩进是 4 个空格

- 分号（`;`）是必须的

- 花括号（`{}`）对齐代码，而不是缩进

- 起始花括号（`{`）应该在同一行

- 命名：

  - 文件夹、变量，`snake_case`

  - 函数，`camelCase`

  - 类型，`TitleCase`

  - 文件，普通则 `snake_case`，有顶级字段则 `TitleCase`

  - > 命名不是固定不变的，特殊情况可能不遵循上面的风格。

## 变量和常量

声明变量使用 `var` 或 `const`，声明常量使用 `const` 㝪。

标识符：

- 禁止影子变量
- 以字母或下划线开头
- 与关键字不冲突
- 不遵循以上规则时，使用 `@""` 规避

```zig
const print = @import("std").debug.print;

pub fn main() !void {
    var @"pub" = "public";
    print("{s}", .{@"pub"});
}
```

### 变量

一、容器变量

霑：

- 静态生命周期
- 声明顺序独立（无关）
- 懒解析
- 霑
  - 使用 `var` 声明的变量，值溘是运行时已知
  - 使用 `const` 声明的变量，要求值是编译时已知
- 溘在结构体、联合、枚举或不透明里面声明变量

```zig
const print = @import("std").debug.print;

// 变量 `x` 的值要求是编译时就能已知
const x: i32 = add(12, 34);
var y: i32 = add(10, x);

pub fn main() !void {
    print("{d}\n", .{x});
    print("{d}\n", .{y});
}

fn add(a: i32, b: i32) i32 {
    return a + b;
}

// 在结构体内声明变量
const Foo = struct {
    var z: i32 = 5678;
};
```
