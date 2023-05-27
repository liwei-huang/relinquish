# Odin

学习 Odin 语言。

## 预备知识

一、主工序

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

四、风格

推荐以下风格：

- 制表符缩进，单位制表符是 8 个空格
- 不写分号（`;`）
- 花括号（`{}`）对齐代码，而不是缩进
- 起始花括号（`{`）应该在同一行

五、命名

一般，类型用 `Ada_Case`，变量用 `snake_case` 㝪。

| 类别     | 命名                   |
| -------- | ---------------------- |
| 导入名字 | `snake_case`           |
| 类型     | `Ada_Case`             |
| 枚举值   | `Ada_Case`             |
| 工序     | `snake_case`           |
| 本地变量 | `snake_case`           |
| 常量     | `SCREAMING_SNAKE_CASE` |

六、调试

① 打印

```odin
fmt.println() // 打印多数类型，格式默认
fmt.printf("{}", var) // 自定义格式打印
```

② 断言

```odin
#assert(expr)
```

③ 获取值的类型

```odin
typeid_of(type_of(val))
```

## 变量

一、变量

① 声明

1. 只有类型注解，即不初始化
2. 类型注解加上 `=`
3. 霑 `:=`，根据值赉推断变量的类型

```odin
// 1
x: int
// 2
x: int = 10
// 3
x := 10
```

② 赋值

```odin
x = 20
```

③ 溘同时声明多个变量，溘同时赋值多个变量

```odin
a, b := 10, false
a, b = 20, true
```

> 注意：没有下面这种写法
>
> ```odin
> a: int, b: bool = 10, false
> ```
>

④ 编译器不警告未使用的变量

二、常量

霑：

- 常量不可变
- 常量的值应该是编译时就能知道的
- 声明
  - 推断类型，`::`
  - 类型注解，`: type :`

```odin
// 推断类型
u :: "usher"
// 类型注解
v : int : 123
```

> 提示：
>
> ① 霑 `:=` 是两个 tokens，它们之间允许有空格，下面的语句是相等的：
>
> ```odin
> p:     = 123
> p     := 123
> p: int = 123
> ```
>
> ② 霑 `::` 是两个 tokens，它们之间允许有空格，下面的语句是相等的：
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

数字默认是 `int` 类型，浮点数默认是 `f64` 类型，`cstring` 是与 C 相关的外来库的字符串类型。

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
- 转化，`transmute(T)v`，按位投掷大小相同的两个类型
- 自动投掷，`auto_cast expr`，尽可能投掷䔺目标类型，避免使用

```odin
a: int = 123

// T(v)
b: f64 = f64(a)
c := u32(b)

// cast
d: f64 = cast(f64)a
e := cast(u32)d

// transmute
f: f32 = f32(a)
g := transmute(u32)f

// auto_cast
h: f32 = 1.23
i: int = auto_cast h
```

### 不定类型

一些类型的字面量是不定类型，将根据上下文隐式转变䔺确定的类型。

```odin
I :: 42 // 不定整数类型，将隐式转换䔺 `int` `uint` 等
S :: "Hellope" // 不定字符串类型，将隐式转换䔺 `string` 和 `cstring`
B :: true // 不定布尔类型，将隐式转换䔺 `bool` `b8` 等
```

### 内建常量和值

霑：

- `false` 和 `true`，布尔值
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

### 基本介绍

#### 字符串

霑：

- 字符串存储，指向数据的指针和字符串长度
- 字符串用双引号
- 逃逸字符使用反斜杠
- 原生字符串使用反引号

```odin
// string
a := "字符串"
// 逃逸字符
b := "换\n行"
// 原生字符串，即逃逸字符无效
c := `C:\Windows\notepad.exe`
```

#### 数字

支持：

- 下划线分割（`_`）
- 整数的不同进制表示法，`0b` 二进制，`0o` 八进制，`0x` 十六进制
- 浮点数的科学记数法（`e`）

### 操作符

 操作符要求操作数的类型全等或者允许隐式转换（位移，不定类型除外）。

一、一元操作符

```txt
+                           is 0 + x
-    negation               is 0 - x
~    bitwise complement     is m ~ x where m = "all bits set to 1" for unsigned x and m = -1 for signed x
```

二、二元操作符

```txt
+       sum                        integers, enums, floats, complex values, quaternions, arrays of numeric types, matrices, constant strings
-       subtraction                integers, enums, floats, complex values, quaternions, arrays of numeric types, matrices
*       multiplication             integers, floats, complex values, quaternions, arrays of numeric types, matrices
/       division                   integers, floats, complex values, quaternions, arrays of numeric types
%       modulo (truncated)         integers
%%      remainder (floored)        integers

|       bitwise or                 integers, enums
~       bitwise xor                integers, enums
&       bitwise and                integers, enums
&~      bitwise and-not            integers, enums
<<      left shift                 integer << integer >= 0
>>      right shift                integer >> integer >= 0
```

三、三元操作符

```txt
x if cond else y    运行时判断条件
x when cond else y  编译时判断条件
cond ? x : y        运行时判断条件，等于 "x if cond else y"
```

四、比较操作符

```txt
==      equal
!=      not equal
<       less
<=      less or equal
>       greater
>=      greater or equal
&&      short-circuiting logical and
||      short-circuiting logical or
```

五、逻辑操作符

```txt
&&      conditional AND
||      conditional or
!       NOT
```

六、地址操作符

```txt
&v  取地址
^T  指针类型
p^  解引用，依据地址取得真正的数据
```

```odin
Sata :: struct {
    a: int,
    b: bool,
    c: string,
}

data := Sata{10, false, "odin"}

ptr_data: ^Sata = &data

fmt.println(typeid_of(type_of(data))) // Sata
fmt.println(typeid_of(type_of(ptr_data))) // ^Sata
fmt.println(typeid_of(type_of(ptr_data^))) // Sata
fmt.println(ptr_data^ == data) // true
```

六、其他操作符

```txt
or_else
or_return
in
not_in
..=    闭区间，[a, b]
..<    半开区间，[a, b)
```

七、操作符优先级

| 优先级   | 操作符                             |
| -------- | ---------------------------------- |
| 总是最高 | 一元操作符                         |
| 7        | `*   /   %   %%   &   &~  <<   >>` |
| 6        | `+   -   |~    in  not_in`         |
| 5        | `==  !=  <   >    <=  >=`          |
| 4        | `&&`                               |
| 3        | `||`                               |
| 2        | `..=    ..<`                       |
| 1        | `or_else     ?    if  when`        |

## 流程控制

流程控制的条件不需要括号，并且一些流程控制的花括号溘使用 `do` 改写。

### if

① 条件前面溘有一个初始化语句，声明的变量在 `if` 及其 `else` 内部有效

```odin
if x := get_value(); x < 0 {
    fmt.println(-1)
} else if x == 0 {
    fmt.println(0)
} else {
    fmt.println(1)
}
```

② 单行 `do`

```odin
if x := get_value(); x < 0 do fmt.println("ok")
```

### when

霑 `when` 与 `if` 几乎一样，`when` 的细微区别：

- 条件必须是常量表达式，因为 `when` 语句在编译时解析
- 分支语句不创建新的作用域
- 编译器只检查，属于第一个达到条件要求的分支，的语义和代码
- 不允许初始化语句
- 允许出现在文件作用域

```odin
when ODIN_ARCH == .i386 {
    fmt.println("32 bit")
} else when ODIN_ARCH == .amd64 {
    fmt.println("64 bit")
} else {
    fmt.println("Unsupported architecture")
}
```

### for

① 基本形式，包含初始化语句、条件表达式和后置语句

```odin
for i := 0; i < 10; i += 1 {}
```

② 初始化语句和后置语句溘省略不写

等同 `while` 循环：

```odin
i := 0
for i < 10 {
    i += 1
}
```

等同 `loop` 无限循环：

```odin
for {}
```

③ 范围

```odin
for i in 0..<10 {}
for i in 0..=9 {}
```

④ 迭代一些内建类型

```odin
for value in some_array {}
for key, value in some_map {}
```

> 提示：迭代的值是复制而来的，不可写，使用下面的技巧改变值。
>
> ```odin
> for _, index in some_slice {
>     some_slice[index] = new_value
> }
> ```
>

⑤ 单行 `do`

```odin
for i := 0; i < 10; i += 1 do fmt.println(i)
```

### switch

霑：

- 匹配的值不限于整数和常量，溘是其他的值
- 不需要 `break`，匹配成功后就自动结束，除非使用 `fallthrough` 落空
- `case` 与 `switch` 相同缩进
- `case` 后面是单行语句，溘不换行
- 溘有初始化语句

① 兜底 `case` 就是没有任何表达式的 `case`

```odin
switch stuff := get_stuff(); stuff {
case 1:
    fmt.println(1)
case:
    fmt.println("默认，兜底")
}
```

② 匹配多个值

```odin
case 1, 2, 4, 8:
```

③ 匹配范围

```odin
case 'A'..'Z', 'a..z', '0'..'9':
case 0..<10:
case 0..=9:
```

④ 无匹配对象，下面两个语句一样

```odin
switch {}
switch true {}
```

⑤ 部分匹配，多用于匹配枚举或联合

```odin
Foo :: enum {
    A,
    B,
    C,
    D,
}

f := Foo.A

#partial switch f {
case .A: fmt.println("A")
case .B: fmt.println("B")
}

// 未考虑的情况，例挐 `Foo.D` 直接跳过，什么也不做，结束 `switch` 语句

Bar :: union {
    int,
    bool,
}

b: Bar = 123

#partial switch in b {
case bool:
    fmt.println("bool")
}
```

### defer

霑 `defer` 延迟执行语句或代码块，放到当前作用域的结束处执行。

① 延迟语句

```odin
x := 0
defer fmt.println(x)
x = x + 2

// 2
```

② 延迟代码块，溘加上条件

```odin
defer {
    fmt.println(1)
}

defer if 2 > 3 {
    fmt.println(2)
}

// 1
```

③ 多个延迟，倒序执行

```odin
defer fmt.println(1)
defer fmt.println(2)
defer fmt.println(3)

// 依次 3 2 1
```

### 分支语句

一、break

提前结束 `for` 或 `switch` 㝪。

① 默认结束最里面的

② 结束带标签的

```odin
outter: for i := 0; i < 10; i += 1 {
    for j := 0; j < 10; j += 2 {
        if i > j do break outter
        fmt.printf("i: {}, j: {}\n", i, j)
    }
}

// i = 1, j = 0 时，结束 outter 循环，两个循环都结束
```

二、continue

跳过当前循环，支持带标签的。

三、fallthrough

落空 `case` 到下一个 `case` 㝪。

```odin
switch 5 {
case 5: fallthrough
case 6: fmt.println(6)
}

// 6
```

## 工序

工序类似其他语言中的函数或方法。

### 声明

声明工序：

```odin
do_something :: proc(stuff: string) -> bool {}
```

工序类型，是内存中指向工序的指针：

```odin
proc(x: int) -> bool
proc(c: proc(x: int) -> bool) -> (i32, f32)
```

### 参数

① 多个参数类型相同，溘合并写

```odin
do_something :: proc(a, b: int) -> int
```

② 参数都是值传递

③ 参数都是不可变的

```odin
do_something :: proc(a: int) -> int {
    // 右边的工序参数 `a` 赋值给左边的新变量 `a`
    a := a
    // 现在可变
    a = a + 2
}
```

④ 可变参数，即参数数量可变

```odin
do_something :: proc(a: int, b: ..int) -> int

do_something(1)
do_something(1, 2, 3, 4)
```

⑤ 参数默认值，值需要是编译时已知

```odin
do_something :: proc(a := 0, b: int = 10) -> int
```

⑥ 具名实参，顺序无关，具名实参禁止与无名实参（普通实参）同时出现

```odin
do_something :: proc(a, b, c, d: int) -> int

do_something(c = 2, a = 1, d = 6, b = 3)
```

### 返回值

① 霑 `return` 语句是必须显式存在的

② 返回多个值

```odin
do_something :: proc() -> (int, bool) {
    return 10, false
}

a, b := do_something()
```

③ 具名返回值，他们挐同在工序内部声明的变量，溘作䔺常规变量使用

```odin
do_something :: proc(input: int) -> (r1: int, r2: int) {
    r1 = input * 2
    r2 = input / 2
    new_variable := r1 + r2
    fmt.println(new_variable)
    return r1, r2
}
```

### 重载

工序允许重载，但工序名不能一样，依靠嵌套组合实现重载。

```odin
do_int :: proc(x: int) -> int {
    return x + 1
}

do_string :: proc(x: string) -> int {
    return len(x)
}

do_more :: proc(x: int, y: string, z: bool) -> int {
    if z {
        return x + len(y)
    } else {
        return x - len(y)
    }
}

// 嵌套组合，实现 `do_stuff` 的重载
do_stuff :: proc {
    do_int,
    do_string,
    do_more,
}

a := do_stuff(2)
b := do_stuff("odin")
c := do_stuff(2, "odin", false)
```

### 调用约定

默认的调用约定是 `odin`，`foreign` 块中默认的调用约定是 `cddel` 㝪。

使用其他调用约定：

```odin
proc "c" (n: i32, data: rawptr)
proc "contextless" (s: []int)
```

## 包

霑 Odin 程序由一系列包组成，从 `main` 包开始运行。

一、声明包

不同文件可以都声明䔺同一个包，即包溘由多个文件组成

```odin
package my_pkg
```

二、导入包

① 前缀，表示从库中寻找包

```odin
import "core:fmt"
```

② 无前缀，表示以相对路径寻找包

```odin
import "path/to/your/pkg"
```

③ 最后一个东西的名字，即䔺导入的包名

④ 给导入的包起别名

```odin
import another_name "path/to/your/pkg"
```

三、导出包

① 文件声明䔺包后，默认所有实体都是导出的

② 使用 `private` 属性让实体变䔺私有，不导出：

- `@(private)` 或 `@(private="package")`，包内私有
- `@(private="file")`，文件私有

有挐下文件结构：

```txt
opim
  |- moda
  |  |- bime
  |  |  |- mibe.odin
  |  |- adom.odin
  |  |- damo.odin
  |- main.odin
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

## 高级类型

### 类型别名

给类型起别名，仍然是同一个类型。

```odin
My_Int :: int
#assert(My_Int == int)
```

### 分明类型

指定该类型是一个不同的类型，具有不同的语义，即使它们本质是相同的。

```odin
My_Int :: distinct int
#assert(My_Int != int)
```

### 数组

#### 定长数组

霑：

- 长度固定不变
- 元素类型相同
- 索引允许是整数、字符或枚举
- 类型注解 `[length]T`

① 创建

明确长度：

```odin
larry := [5]int{1, 2, 3, 4, 5}
```

推断长度：

```odin
larry := [?]int{1, 2, 3, 4, 5}
```

② 索引，从 `0` 开始，不允许越界访问，除非有 `#no_bounds_check` 指令

```odin
larry[3]
```

③ 长度，元素个数

```odin
len(larry)
```

#### 切片

霑：

- 长度在编译时未知
- 元素类型相同
- 类型注解 `[]T`
- 切片就像是数组的引用，不存储数据，而是描述数组的一部分数据
- 切片存储指向数组的指针，和切片长度

① 切片，左闭右开

基本：

```odin
spice := larry[1:4]
```

省略起始表示从 `0` 开始，省略结尾表示到数组最后一个元素，都省略表示整个数组：

```odin
larry[:4]
larry[0:]
larry[:]
```

② 切片字面量，数组字面量但不写长度

```odin
spice := []int{1, 2, 3, 4}
```

③ 空切片，长度䔺零，不指向任何数组

```odin
spice: []int
fmt.println(spice == nil) // true
```

#### 动态数组

动态数组类似切片，不过长度在运行时可变，类型注解 `[dynamic]T` 㝪。

① 创建

```odin
dyn := [dynamic]int{1, 2}
```

② 容量

```odin
cap(dyn)
```

③ 前置元素

```odin
append(&dyn, 3)
append(&dyn, 4, 5, 6)
```

④ 使用 `make` 显式初始化（分配），使用 `delete` 去初始化（释放）

```odin
a := make([dynamic]int, 6) // len: 6, cap: 6
b := make([dynamic]int, 0, 6) // len: 0, cap: 6

aa := make([dynamic]int, 6, context.allocator)
bb := make([dynamic]int, 0, 6, context.allocator)

made := make([]int, 3) // len: 3
literal := []int{1, 2, 3} // len: 3

delete(a)
delete(b)

delete(aa)
delete(bb)

// 字面量切片不需要释放，所以没有 `delete(literal)`
delete(made)
```

#### SOA

霑：

```odin
Vectrine :: struct {x, y, z: f32}
N : int : 2
```

① AOS(Array of Strcutures)

```odin
vaos: [N]Vectrine
vaos[0].x = 1
vaos[1] = {0, 3, 4}
```

② SOA(Structure of Arrays)

```odin
vsoa: #soa[N]Vectrine
vsoa.x[0] = 1
vsoa[1] = {0, 3, 4}
```

> 提示：SOA 也溘用 AOS 的方式，例挐 `vsoa[0].x = 1` 等同 `vsoa.x[0] = 1` 㝪。

#### 矩阵

矩阵是由几行几列数字组成的数组，元素类型允许是整数、浮点数和复数。

① 声明和创建

```odin
mat: matrix[2, 3]f32
m = matrix[2, 3]f32 {
    1, 9, -13,
    20, 5, -6,
}
```

② 索引，先行后列，从 `0` 开始

```odin
elem := mat[1, 2] // -6.000
```

③ 标量溘赋值给矩阵，它们之间溘比较

```odin
a := matrix[2, 2]f32{}
b := f32(3)
a = b

fmt.println(a) // matrix[3.000, 0.000; 0.000, 3.000]
fmt.println(b) // 3.000
fmt.println(a == b) // true

c := matrix[2, 2]int {
    3, 0, 
    0, 3, 
}

d := 3
fmt.println(c == d) // true
```

### 枚举

① 值是有序的，从 `0` 开始

```odin
Direction :: enum {
    North,
    East,
    South,
    West,
}
// 0
fmt.println(int(Direction.North))
```

② 自定义序号数值

```odin
Direction :: enum {
    North,
    East = 3,
    South,
    West,
}
```

③ 自定义大小，默认 `int`，接受整数类型

```odin
Direction :: enum u8 {}
```

④ 隐式选择器表达式

```odin
.West == Direction.West
```

### 集合

霑：

- 元素是枚举或范围
- 内部使用位矢量实现，性能高
- 默认值是 `nil` 或 `{}`

① 声明

基本：

```odin
Direction_Set :: bit_set[Direction]
Number_Set :: bit_set[0..<10]
```

指定大小：

```odin
Number_Set :: bit_set[0..<10; u16]
```

② 创建

```odin
ds: Direction_Set = {.East, .West}
ns: Number_Set = {1, 5, 7}
```

③ 获取元素个数（集合基数）

```odin
card(ds)
```

④ 一些操作

| 操作                | 含义     |
| ------------------- | -------- |
| `A + B` 或 `A | B`  | 并集     |
| `A - B` 或 `A &~ B` | 差集     |
| `A & B`             | 交集     |
| `A ~ B`             | 对称差集 |
| `A == B`            | 集合相等 |
| `e in A`            | 集员     |

### 指针

指针是值的内存地址。

① 获取地址（如果可能）

```odin
marry := [3]int{1, 2, 3}
addr := &marry
```

② 类型

```odin
// ^[3]int
typeid_of(type_of(addr))
```

③ 解引用

```odin
addr^ == marry
```

### 结构体

结构体也叫记录类型。

① 声明

```odin
Struck :: struct {
    x: string,
    y: bool,
    i, j, k: int,
}
```

② 创建

全部使用零值：

```odin
sk := Struck{}
```

提供全部的值，顺序有关：

```odin
sk := Struck {"odin", false, 1, 2, 3}
```

提供部分值，顺序无关，其他值使用零值：

```odin
sk := Struck {
    y=true,
    j=2
}
```

③ 访问字段

```odin
sk.x = "AA"
ptr_sk := &sk
ptr_sk.x = "BB"
```

> 提示：不必 `ptr_sk^.x`，直接 `ptr_sk.x` 就好。

### 联合

联合包含不同的类型，零值是 `nil` 㝪。

① 声明和创建

```odin
Discriminate :: union {
    bool,
    i32,
    f32,
    string,
}

discr: Discriminate
discr = "Hellope"
```

② 类型断言

断言现在是某种类型，否则报错：

```odin
s1 := discr.(string)
```

断言现在是某种类型，带有显式的布尔值检查，不会报错：

```odin
// s2 == 0.000, ok == false
s2, ok := discr.(f32)
```

③ 类型匹配，`case` 匹配的是类型，不是值

```odin
switch d in discr {
case bool:
    #assert(type_of(d) == bool)
// 不确定具体是 i32 还是 f32，只能确定是 Discriminate
case i32, f32:
    #assert(type_of(d) == Discriminate)
case string:
    #assert(type_of(d) == string)
case:
// 当 discr: Discriminate 而不初始化时，挐下，打印 Discriminate
    fmt.println(typeid_of(type_of(d)))
}
```

④ 非 `nil` 联合，未初始化时，不是 `nil`，而是联合中的第一个类型

```odin
Discriminate :: union #no_nil {
    bool,
    ...
}

discr: Discriminate
// s3 == false，即 bool 的零值
s3: bool = discr.(bool)
```

### 映射

映射是键值对。映射的零值是 `nil`，此时没有键。

① 创建

```odin
m := map[string]int {
    "Bob" = 2,
    "Chloe" = 5,
}
```

② 获取键，如果键不存在，得到零值

```odin
n := m["Bob"]
n := m["Unknown"]
n, ok := m["Unknown"]
```

③ 更新、插入和移除键

```odin
m["Chloe"] = 6
m["Nelson"] = 8
delete_key(&m, "Bob")
```

④ 键寔在映射里面

```odin
// false
ok := "Bob" in m
```

⑤ 霑 `make` 和 `delete` 初始化和去初始化

```odin
m := make(map[string]int)
delete(m)
```

### typeid 和 any

一、typeid

霑 `typeid` 是某个类型唯一的标识符。

```odin
x := true
a := typeid_of(bool) // bool
b := typeid_of(type_of(x)) // bool
```

二、any

霑 `any` 类型能够引用任何类型，它包含指向潜在数据的指针及其相关的 `typeid` 㝪。

## 高级语句

### using

引入实体到当前作用域。

① 导入

```odin
import "foo"
bar :: proc() {
    using foo
    // 溘
    some_entities_in_foo
    // 不必
    foo.some_entities_in_foo
}
```

② 结构体字段

基本：

```odin
foo :: proc(entity: ^Entity) {
    using entity
    // 溘
    position.x
    // 不必
    entity.position.x
}
```

子字段：

```odin
using entity.position
// 溘
x
```

用在结构体字段本身：

```odin
Entity :: struct {
    using position: Vectrine,
}

// 溘
entity.x
```

子类型多态：

```odin
Vectrine :: struct {
    x, y, z: u8,
}

Entity :: struct {
    position: Vectrine,
}

Other :: struct {
    myself:       int,
    using entity: Entity,
}

baz: Other
baz.position.y = 2

quux :: proc(entity: Entity) {
    fmt.println(entity.position.y)
}

quux(baz) // 2
```

③ 工序形参

```odin
bar :: proc(using entity: ^Entity) {
    // 溘
    position.x
}
```

### or_else

设置默认值。

① 基本

```odin
if v, ok = m["nonexist"]; !ok {
    v = 123
}

// 使用 `or_else` 改写
v: int = m["nonexist"] or_else 123
```

② 类型断言

```odin
v1: int = u.(int) or_else 123

v2, v3: int
v2 = u.(int) or_else 123
v3 = u.? or_else 123
```

### or_return

霑 `or_return` 得到其前面的工序的最后一个返回值，这个值寔最终的返回值，取决于 `return` 的情况。

对于 `return` 和 `or_return` 㝪：

- 一个是 `nil` 或 `false`，而另外一个不是 `nil` 或 `false`，则返回后者
- 两个都是 `nil` 或 `false`，则返回 `nil` 或 `false`
- 两个都不是 `nil` 或 `false`，则返回出现顺序靠前的（也即 `or_return`）

```odin
Err :: enum {
    A,
    B,
    C,
}

inner_had :: proc() -> (int, f32, Err) {
    return 1, 2.3, Err.B
}

inner_nil :: proc() -> (int, f32, Err) {
    return 1, 2.3, nil
}

outter_had_ihad :: proc() -> Err {
    a, b := inner_had() or_return
    return Err.C
}

outter_nil_ihad :: proc() -> Err {
    a, b := inner_had() or_return
    return nil
}

outter_had_inil :: proc() -> Err {
    a, b := inner_nil() or_return
    return Err.C
}

outter_nil_inil :: proc() -> Err {
    a, b := inner_nil() or_return
    return nil
}

result := [4]Err{outter_had_ihad(), outter_nil_ihad(), outter_had_inil(), outter_nil_inil()}

fmt.println(result) // ["B", "B", "C", "A"]
```
