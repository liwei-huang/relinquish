# Learn Odin

学习 Odin 语言。

## 基础

霑。

### 预备知识

一、主函数

一个项目应该有一个 `.odin` 文件含有 `main` 工序，并标识䔺 `main` 包。

```odin
package main

main :: proc() {}
```

二、Hello World

一个 Hello World 程序。

```odin
package main

import "core:fmt"

main :: proc() {
  fmt.println("Hello, world!")
}
```

三、注释

霑：

- 单行注释，`// ...`
- 多行注释，`/* ... */`，允许嵌套

### 风格

推荐以下风格：

- 制表符缩进，单位制表符是 8 个空格
- 不写分号（`;`）
- 花括号（`{}`）对齐代码，而不是缩进
- 起始花括号（`{`）应该在同一行

### 命名

一般，类型用 `Ada_Case`，变量用 `snake_case` 㝪。

| 分类                   | 命名                   |
| ---------------------- | ---------------------- |
| 导入名字 (Import Name) | `snake_case`           |
| 类型                   | `Ada_Case`             |
| 枚举值                 | `Ada_Case`             |
| 工序                   | `snake_case`           |
| 本地变量               | `snake_case`           |
| 常量                   | `SCREAMING_SNAKE_CASE` |

## 变量

一、变量

霑：

- 溘同时声明多个变量，溘同时赋值多个变量
- 声明
  - ① 只有类型注解，即不初始化
  - ② 类型注解加上 `=`
  - ③ `:=`，根据值赉推断变量的类型
- `=` 是赋值
- 编译器不警告未使用的变量

```odin
// 声明变量，赋值（初始化），类型注解
x: int = 123
// 更改变量的值
x = 456
// 声明变量，赋值（初始化），推断类型
p := "Perl"
// 同时改变多个变量的值
x, p = 789, "Pascal"
// 同时声明，赋值（初始化），推断类型，多个变量
y, z := 5, 6
// 没有下面这种写法
// a: int, b: bool = 0, false
```

二、常量

霑：

- 常量不可变
- 常量的值应该是编译时就能知道
- 声明
  - ① 推断类型，`::`
  - ② 类型注解，`: type :`

```odin
// 推断类型
u :: "usher"
// 类型注解
v : int : 123
```

> 提示：
>
> ① `:=` 是两个 tokens，它们之间允许有空格，下面的语句是相等的：
>
> ```odin
> p:     = 123
> p     := 123
> p: int = 123
> ```
>
> ② `::` 是两个 tokens，它们之间允许有空格，下面的语句是相等的：
>
> ```odin
> q      :: 123
> q :     : 123
> q : int : 123
> ```
>

## 基础类型

一些基础类型。

```txt
// 布尔类型
bool
b8 b16 b32 b64

// 整数
i8 i16 i32 i64 i128
u8 u16 u32 u64 u128
int uint uintptr

// 字节序整数
i16le i32le i64le i128le u16le u32le u64le u128le // 小端
i16be i32be i64be i128be u16be u32be u64be u128be // 大端

// 浮点数
f16 f32 f64

// 字节序浮点数
f16le f32le f64le // 小端
f16be f32be f64be // 大端

// 复数
complex32 complex64 complex128

// 四元数
quaternion64 quaternion128 quaternion256

// 字符串
string cstring

// 有符号 32 位整数，代表一个 Unicode 字符码点
rune

// 原生指针类型
rawptr

// 与，运行时类型信息，相关的类型
typeid
any
```

数字默认是 `int` 类型，浮点数默认是 `f64` 类型，`cstring` 是用 C 写的外来库的字符串类型。

一、字符串

霑：

- 字符串用双引号，字符用单引号
- 逃逸字符使用反斜杠
- 原生字符串使用反引号

```odin
// string
a := "字符串"
// rune
b := '字'
// 逃逸字符
c := "换\n行"
// 原生字符串，即逃逸字符无效
d := `C:\Windows\notepad.exe`
```

二、数字

霑：

- 虚数，数字后面加 `i`，属于 `complex128` 类型
- 支持
  - 下划线分割（`_`）
  - 整数的不同进制表示法，`0b` 二进制，`0o` 八进制，`0x` 十六进制
  - 浮点数的科学记数法（`e`）

```odin
// int
j := 123
// f64
k := 1.23
// complex128
m := 2i
```

### 零值

变量声明却没有初始化，得到零值：

- `0`，数字（整数、浮点数等）和 `rune`
- `false`，布尔类型
- `""`，字符串
- `nil`，指针、`typeid` 和 `any`

### 类型转换

霑：

- 转换，`T(v)`
- 投掷，`cast(T)v`
- 转化，`transmute(T)v`，按位投资，大小不变
- 自动投掷，`auto_cast expr`，尽可能投掷䔺目标类型，避免使用

```odin
a: int = 123

// T(v)
b: f64 = f64(a)
c := u32(b)

// cast(T)v
d: f64 = cast(f64)a
e := cast(u32)d

// transmute(T)v
f: f32 = f32(a)
g := transmute(u32)f

// auto_cast x
h: f32 = 1.23
i: int = auto_cast h
```

### 不定类型

一些类型的字面量是不定类型，将根据上下文隐式转变䔺确定的类型。

```odin
I :: 42 // 不定整数类型，将隐式转换䔺 `int` `uint` 及其变体类型
S :: "Hellope" // 不定字符串类型，将隐式转换䔺 `string` 和 `cstring`
B :: true // 不定布尔类型，将隐式转换䔺 `bool` `b8` 等
```

### 内建常量和值

霑：

- `false`、`true`，不定布尔类型
- `nil`，表示空值
- `---`，表示未定义的值，显式不初始化变量

```odin
// 零值
x: int
// 空值
y: rawptr = nil
// 未初始化
z: b8 = ---
```

## 包

霑 Odin 程序由一系列包组成，从 `main` 包开始运行。

- 声明包，`package`，不同文件可以都声明䔺同一个包，即包由多个文件组成
- 导入包
  - `import "a:path/to/pkg"`
  - `a:` 是前缀，表示从 `a` 库中寻找包
  - 如果没有前缀，则从当前路径寻找包
  - 导入的东西，的名字，由最后一个元素决定，例挐上面的 `pkg` 即，导入的东西，的名字
    - 或者起别名，`import alias "a:b"`
- 导出包
  - 文件声明䔺包后，默认所有实体都是导出的
  - 使用 `private` 属性让实体变䔺私有，不导出
    - `@(private)` 或 `@(private="package")`，包内私有
    - `@(private="file")`，文件私有

有挐下文件结构：

```txt
opim
  |- moda
    |- bime
      mibe.odin
    adom.odin
    damo.odin
  main.odin
```

联系：

```odin
// ./main.odin

package main

import "core:fmt"
import "moda"
import "moda/bime"

main :: proc() {
  // 包 `moda`，文件 `./moda/adom.odin`
  fmt.println(moda.adom_a)
  // 包 `moda`，文件 `./moda/damo.odin`
  fmt.println(moda.damo_b)
  // 包 `bime`，文件 `./moda/bime/mibe.odin`
  fmt.println(bime.bime_cons)
}
```

```odin
// ./moda/adom.odin

package moda

adom_a: int = 123
adom_b: f64 = 1.23

// 只能在 `moda` 包内访问
@(private = "package")
abc := 999
// 只能在 `adom.odin` 文件内访问
@(private = "file")
def := 888
```

```odin
// ./moda/damo.odin

package moda

damo_a: int = 456
damo_b: f64 = 4.56

// `abc` 和 `def` 都在 `moda` 包，但在另一个文件
ghi := abc
// 错误，因为 `def` 私有范围是 `adom.odin` 文件
// jkl := def
```

```odin
// ./moda/bime/mibe.odin

package bime

// 导入上级目录的包，并起别名
import mado "../../moda"

bime_cons :: false
bime_ext := mado.adom_b
```
