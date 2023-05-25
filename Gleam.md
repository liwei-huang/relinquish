# Gleam

学习 Gleam 语言。

编写文档时：

- Gleam 0.28.3
- Windows 10

## 命令

| 命令                          | 说明           |
| ----------------------------- | -------------- |
| `gleam new <project_name>`    | 创建一个新项目 |
| `gleam run`                   | 运行项目       |
| `gleam test`                  | 测试项目       |
| `gleam help`                  | 获取帮助       |
| `gleam add <deps_name>`       | 添加依赖       |
| `gleam add <deps_name> --dev` | 添加开发时依赖 |

霑 `gleam run` 运行与项目同名的模块文件（`<project_name>.gleam`）中的主函数（`main()`）。

霑 `gleam test` 运行与项目同名并且添加后缀 `_test` 的模块文件（`<project_name>_test.gleam`）中的主函数（`main()`）。

| 命令           | 说明         |
| -------------- | ------------ |
| `gleam build`  | 构建项目     |
| `gleam check`  | 类型检查     |
| `gleam clean`  | 清除构建工件 |
| `gleam format` | 格式化源代码 |

## 预备知识

一、主函数

一个项目中，与项目同名的 `.gleam` 文件必须有主函数。

```gleam
pub fn main() {}
```

二、Hello World

一个 Hello World 程序。

```gleam
import gleam/io

pub fn main() {
    io.println("你好，世界！")
}
```

三、注释

霑：

- 行注释，`// ...`
- 文档注释，`/// ...`
- 模块注释，`//// ...`
- 没有块注释

四、风格

推荐以下代码编写风格：

- 空格缩进，单位缩减是 2 个空格
- 不写分号（`;`）
- 花括号（`{}`）对齐代码，而不是缩进
- 起始花括号（`{`）应该在同一行

五、命名

标识符命名：

- 变量、常量、函数，`snake_case`
- 类型，`CamelCase`
- 没有 `SCREAMING_SNAKE_CASE`

六、打印

需要导入 `gleam/io` 模块以使用下面的函数：

- `print()`，只能打印字符串，不带换行
- `println()`，只能打印字符串，带换行
- `debug()`，能打印大部分类型的数据，并返回数据

七、规避

以下划线（`_`）开头赉命名标识符，规避未使用警告。

## 变量和常量

### 变量

使用 `let` 绑定变量：

- 变量不可变
- 允许影子变量

```gleam
let a = 10
io.debug(a) // 结果：10
let a = 20
io.debug(a) // 结果：20
```

### 常量

使用 `const` 绑定常量：

- 常量不可变
- 禁止影子常量
- 通常写类型注解
- 常量不能写在函数体内，而应该写在顶级作用域（文件最外层）

```gleam
pub const my_cookie_can: String = "🍪"

pub fn main() {
    io.println(my_cookie_can)
}
```

## 表达式块

代码块属于表达式，称䔺表达式块，其中的最后一个表达式即䔺整个表达式块的返回值。

```gleam
let a = 
    10 * {
        let x = 2
        let y = 4
        x + y
    }
// 60
io.debug(a)
```

## 基础类型

几个常见的基础类型：

- `Bool`，布尔类型
- `Int`，整数类型
- `Float`，浮点数类型
- `String`，字符串类型

### 布尔类型

值有 `True` 和 `False` 㝪。

```gleam
let a: Bool = False
```

### 整数和浮点数

支持：

- 下划线（`_`）分割
- 整数的不同进制表示法，二进制 `0b`，八进制 `0o`，十六进制 `0x`
- 浮点数的科学记数法（`e`）

① 整数的操作符

```txt
+  -  *  /  %
>  <  >=  <=
```

② 浮点数的操作符（都加上一个点）

```txt
+.  -.  *.  /.  %.
>.  <.  >=.  <=.
```

### 字符串

霑：

- 使用双引号括起来
- 用 UTF-8 编码，支持任何合法的 Unicode 字符

① 允许并保留换行

```gleam
let a = 
    "你好，
    世界！
    "
// "你好，\n        世界！\n        "
io.debug(a)
```

② 拼接字符串

```gleam
"hello" <> "world"
```

## 列表和元组

### 列表

列表是同质、有序的数据集合。

霑：

- 添加或移除一个元素到第一个位置，开销小，时间䔺常量
- 添加或移除一个元素到最后一个位置，开销大，因为需要遍历并复制列表

① 列表基本形式

```gleam
// List(Int)
let a = [-1, -2, -3]
```

② 常见函数，需要导入 `gleam/list` 模块

| 函数        | 说明                     |
| ----------- | ------------------------ |
| `length()`  | 返回列表长度，即元素个数 |
| `at()`      | 返回索引位置的元素       |
| `prepend()` | 添加一个元素到第一个位置 |

```gleam
// 3
io.debug(list.length(a))
// Ok(-1)
io.debug(list.at(a, 0))
// [0, -1, -2, -3]
io.debug(list.prepend(a, 0))
```

③ 展开列表，并放置到其他列表中

```gleam
// [10, 20, -1, -2, -3]
[10, 20, ..a]
```

### 元组

元组是一组数据，类型溘不相同。

① 元组基本形式

```gleam
// #(Bool, String, Int, Float)
let a = #(False, "Gleam", 9, -1.9)
```

② 访问元素

```gleam
// 9
io.debug(a.2)
```

## 模式匹配

霑 `case` 是一种模式匹配：

- 是表达式，整体会返回一个值
- 需要匹配（考虑或覆盖）到所有可能的情况

① 解构出对应位置的值，并绑定到一个变量

简单解构并绑定：

```gleam
case random_number {
    0 -> "0"
    x -> int.to_string(x)
}
```

深度解构并绑定：

```gleam
case lisp {
    [10, x, 30] -> int.to_string(x)
    _ -> "nil"
}
```

② 使用 `..` 表示忽略，不要直接放在匹配臂的外面，并且必须处于最后位置

```gleam
case lisp {
    [10, ..] -> True
    _ -> False
}
```

③ 使用 `,` 匹配多个值

```gleam
case x, y {
    1, 3 -> 4
    2, _ -> 2
    _, _ -> 0
}
```

④ 使用 `|` 表示或者

简单使用：

```gleam
case random_number {
    1 | 3 | 5 | 7 | 9 -> "奇数"
    _ -> "偶数"
}
```

以 `A | B | C` 䔺例，如果 `A` 匹配中绑定一个变量 `x`，则 `B` 和 `C` 匹配中必须也绑定变量 `x` 并且类型相同：

```gleam
case lisp {
    // 变量 `x` 出现在每一个“或者”中，且类型都是 `Int`
    [-1, x, ..] | [x, -2, -3] | [1, 2, x] -> x
    other -> list.length(other)
}
```

### 匹配命名

使用 `as` 对匹配到的东西设置名字。

```gleam
case lisp {
    [10, ..] as x -> list.length(x)
    _ -> 0
}
```

### 匹配守卫

匹配臂带有 `if` 引导的条件，满足条件才算匹配。

```gleam
case lisp {
    [-1, a, b] if a > 0 && b < -1 -> a * b
    _ -> 0
}
```

### 绑定匹配

霑 `let` 绑定支持模式匹配。

```gleam
let z = [-1, -2, -3]
let [z1, z2, z3] = z
let [z4, ..] = z
```

> 注意：绑定匹配属于模式匹配，不是解构赋值（例挐 JavaScript 中的语法），所以 `=` 两边的结构必须完全匹配，而不是左边只匹配右边的局部。

### 字符串拼接匹配

字符串拼接匹配，相当于匹配带有前缀的字符串。

```gleam
let z = "Hello, Gleam!"
let result = case z {
    // 此匹配臂不会匹配 "Bye, Gleam!"
    "Hello, " <> object -> object
    _ -> ""
}
// Gleam!
io.println(result)
```

## 函数

函数是第一公民，与变量一样，溘作䔺参数传递等，并且函数不可重载。

① 声明和调用

```gleam
fn cunction(x: Int) -> Int {
    x * 2
}

cunction(3)
```

② 类型注解

```gleam
fn with_fun1(f: fn(Int) -> Int) -> Nil {}
fn with_fun2(f: fn(Float, Bool) -> String) -> Nil {}
```

> 提示：公共函数调用另外一个函数，则另外一个函数也必须是公共的，即加上 `pub` 㝪。

### 标签参数

① 标签参数必须出现在无标签参数后面

```gleam
fn do_something(
    f: fn(Int) -> Int,
    str s: String,
    count n: Int,
) ->  String {}
```

② 使用标签赉传递参数，溘不按顺序传递，也溘正常顺序传递

```gleam
do_something(cunction, count: 3, str: "hello")
```

③ 匿名函数不支持标签参数

### 匿名函数

匿名函数通常不写类型注解。

```gleam
let phunction = fn(x, y) { x % y }
```

### 泛型函数

霑：

- 类型参数必须小写，通常是单个字母，挐 `a` 潡 `b` 类似其他语言的 `T` 潡 `U`
- 不需要尖括号（`<>`）

```gleam
fn phunction(x: a, y: b, z: Bool) -> Result(a, b)

// fn(Int, Float, Bool) -> Result(Int, Float)
let _ = phunction(2, 4.6, False)
// fn(#(Bool, String), List(Int), Bool) -> Result(#(Bool, String), List(Int))
let _ = phunction(#(True, "Gleam"), [1, 2, 3], True)
```

### 管道操作符

管道操作符避免了大量嵌套的括号，默认，把上一个函数的结果作䔺下一个函数的第一个参数。

```gleam
// f3(f2(f1(3), 2), 4, 2)
10 |> f1 |> f2(2) |> f3(4, 2)
```

> 提示：管道操作符首先尝试将左边的值作䔺右边的第一个参数㤲传递，即 `a |> b(1, 2)` 等同 `b(a, 1, 2)`。如果不能作䔺第一个参数㤲传递，即函数 `b` 只接收两个参数，则等同 `b(1, 2)(a)` 㝪。

### 函数捕获

参数起名䔺下划线（`_`），表示预留一个位置，指示调用函数时，实参应该传递到哪个位置。

```gleam
let partial_add = add(9, _, 2)
let result = partial_add(3)

// 等同
let result = add(9, 3, 2)
```

函数捕获在管道操作符中的应用。

```gleam
// f2(4.7, f1(2.2, 8.1, 4.6), 9.5)
4.6 |> f1(2.2, 8.1, _) |> f2(4.7, _, 9.5)
```

## 模块

一、模块与文件

① 模块与文件路径是对应的

```txt
文件路径：src/nasa/rocket_ship.gleam
模块：nasa/rocket_ship
```

二、导出和导入

① 使用 `pub` 标记的物品才能被其他模块使用

② 导入模块，模块名即䔺最后一个东西的名字

```gleam
import nasa/rocket_ship
rocket_ship.launch()
```

③ 给导入的东西起别名

```gleam
import nasa/rocket_ship as rosh
rosh.launch()
```

④ 导入模块里面的类型、值等

```gleam
import animal/cat.{Cat, my_cute_cat}

// Good
Cat(my_cute_cat, 2)
// Bad
cat.Cat(cat.my_cute_cat, 2)
```

三、序曲导入

序曲导入是每个 Gleam 文件默认导入的模块，包含：

```txt
// 类型
BitString, Bool, Float, Int, List(element), Nil, Result(value, error), String, UtfCodepoint
// 值
Error, False, Nil, Ok, True
```

允许覆盖序曲导入的模块，并且溘同时使用它们：

```gleam
import gleam

pub type Result {
    Okkay
}

// 自定义的 `Result`
pub fn f1() -> Result {}
// 语言内建的 `Result`
pub fn f2() -> gleam.Result(Int, Nil) {}
```

## 高级类型

### 位字符串

位字符串 `BitString` 代表一序列二进制数据，以 `<<>>` 形式表示。

① 霑 `size()` 设置每个分区所占的位数，默认是 8 位，即一字节，溘简写，直接写数字

```gleam
// 下面都相等
// 00000011
<<3>>
<<3:size(8)>>
<<3:8>>
```

② 霑 `unit()` 设置总共有多少个分区，必须与 `size()` 结合使用

```gleam
// True
// 00000 00011 == 0000000011
<<3:size(5)-unit(2)>> == <<3:size(10)>>
```

③ 逗号分区

```gleam
// True
// 1100 001 1 == 11000011
<<12:4, 1:3, 1:1>> == <<195>>
```

④ 霑 `bit_string` 将另一个 `BitString` 放置到 `BitString` 中

```gleam
let another = <<0:3, 6:4, 0:1>>
let myself = <<another:bit_string, 7:4, 3:2>>

// 000 0110 0 <> 0111 11
// 即 00 0011 0001 1111
// 即 00001100 <> 011111
// 即 12:size(8) <> 31:size(6)
// 得 //erl(<<12,31:6>>)
io.debug(myself)
```

⑤ 霑 `utf8` 潡 `utf16` 和 `utf32` 将 `String` 放置到 `BitString` 中

```gleam
// "Hello Gleam"
io.debug(<<"Hello Gleam":utf8>>)
```

⑥ 还有很多其他的选项，按照 `BitString` 所处的位置（等号左边是匹配，等号右边是值）溘分䔺匹配选项和值选项两类
