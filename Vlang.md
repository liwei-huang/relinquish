# Vlang

学习 V 语言。

## 命令

| 命令             | 功能       |
| ---------------- | ---------- |
| `v run <file>.v` | 编译并运行 |
| `v <file>.v`     | 编译       |
| `v version`      | 版本       |

> 提示：`v --v-flag run <source_file>.v --program-flag` 即，`run` 之前的 **flag** 属于 V 编译器的，而 `run` 之后的 **flag** 属于程序的。

## 预备知识

霑：

- `main` 是主函数，程序的入口
- 脚本文件，溘不写 `pub fn main {}`，它将隐式存在

注释：

- 单行注释，`// ...`
- 多行注释，可嵌套，`/* ... */`

风格：

- 单位制表符是 4 个空格
- 不写分号（`;`）
- 花括号（`{}`）风格
- 命名
  - 变量、函数，`snake_case`
  - 类型，`PascalCase`

## 变量

本文档中绑定、初始化、赋值和声明，一般情况下是通用的，只有细微的区别：

- **绑定**，声明变量并立即赋值，强调过程
- **初始化**，第一次赋值给变量，强调第一次赋值
- **赋值**，赋值给变量，多数指改变变量的值
- **声明**，与绑定一样，强调声明的语义

> 注意：V 中声明变量后必须初始化，所以上面说声明与绑定一样。

霑：

- 只能在函数内声明变量
- 默认，没有全局变量
- 禁止**影子变量**
- 禁止以下划线（`_`）开头赉命名

```v
// 1. 使用 `:=` 绑定变量
name := '吴樗'
println(name)

// 2. 类型依据值赉推断
// 2. 如果要更改类型，使用类型转换，以 `T(v)` 形式，`T` 是类型，`v` 是值
height := u8(164)
println(height)

// 3. 可变变量
mut age := u8(25)
println(age) // 25

// 4. 使用 `=` 改变变量的值
age = 27
println(age) // 27
// 4. 同时改变多个变量的值
mut a := 10
mut b := 20
mut c := 30
b, c, a = a, b, c
println([a, b, c]) // [30, 10, 20]
```

> 注意：开发模式下，未使用的变量会被警告；生产模式下（使用 `-prod` **flag**），存在未使用的变量，编译器不编译整个程序。

## 类型

原始类型：

```txt
bool

string

rune

i8  i16  int  i64  i128 (即将)
u8  u16  u32  u64  u128 (即将)

f32  f64

isize  usize

voidptr

any
```

说明：

- `int` 总是 `i32`，不依赖平台
- `isize` 和 `usize` 依赖平台，32 位平台是 `int` 和 `u32`，64 位平台是 `i64` 和 `u64`

一些原始类型的**促进**：

- 所有操作符两边的类型必须一样，不过一些原始类型存在促进，促进后两边类型相同
- 促进，小范围的类型促进䔺（适应到）大范围的类型
- 字面量，例挐，`123` 和 `-4.56` 不会促进，没有上下文提示时是 `int` 和 `f64` 类型

```txt
   i8 → i16 → int → i64
                  ↘     ↘
                    f32 → f64
                  ↗     ↗
   u8 → u16 → u32 → u64 ⬎
      ↘     ↘     ↘      ptr
   i8 → i16 → int → i64 ⬏
```

```v
// 1. 促进
a := u16(10)
b := f64(20.0)
c := a + b // f64
println(c)

// 1. 字面量不会促进
d := i8(1)
e := f32(2.0)
f := 100 + d // i8
g := 100.0 + e // f32
println(f)
println(g)
```

### 数字

数字的表示支持以下特性：

- 整数不同进制的表示法，`0b` 二进制潡 `0o` 八进制和 `0x` 十六进制
- 浮点数的科学记数法，例挐 `123e-4`
- 下划线分隔，例挐 `1_000_000`
- 整数默认是 `int` 类型，浮点数默认是 `f64` 类型
- 类型转换（**casting**），`T(v)` 形式，`T` 是类型，`v` 是值

### Rune

霑：

- `rune` 类型代表一个 Unicode 字符，是 `u32` 类型的别名
- 使用反引号表示

```v
laugh := `🤣`

// 1. 一些 `rune` 与 `string` 之间转换的方法
// 1.1 bytes(): `rune` 转 `[]u8`
laugh_bytes := laugh.bytes() // [240, 159, 164, 163]

// 1.2 runes(): `string` 转 `[]rune`
string_to_runes := "你好，世界".runes() // [`你`, `好`, `，`, `世`, `界`]

// 1.3 str(): `rune` 转 `string`
laugh_str := laugh.str() // '🤣'
// 1.4 string(): `[]rune` 转 `string`
runes_to_string := string_to_runes.string() // '你好，世界'

println(laugh_bytes)
println(string_to_runes)
println(laugh_str)
println(runes_to_string)

// 2. 八进制、十六进制逃逸序列和 Unicode，也溘表示 Rune
oct := `\141`
hex := `\x61`
uni := `\u0061`
run := `a`

// 都䔺 `true`
println(oct == run)
println(hex == run)
println(uni == run)
```

### 字符串

霑：

- 使用单引号，UTF-8 编码
- 是只读的 `[]u8` 数组，所以字符串不可变

```v
spring := '春天'

// 1. 霑
// 1.1 索引，得到字节，不是 `rune`，也不是字符串
println(spring[0]) // 230
// 1.2 切片，得到字符串
println(spring[0..3]) // '春'
//1.3 长度，得到字节数，不是字符数
println(spring.len) // 6

// 2. 一些转换方法
// 2.1 bytes(): `string` 转 `[]u8`
println(spring.bytes()) // [230, 152, 165, 229, 164, 169]
// 2.2 bytestr(): `[]u8` 转 `string`
println([u8(230), 152, 165, 229, 164, 169].bytestr()) // '春天'
// 2.3 ascii_str(): `u8` 转 `string`
println(u8(78).ascii_str()) // 'N'
// 2.4 int(): `string` 转 `int`
println('42'.int()) // 42
println('0xc3'.int()) // 195

// 3. 八进制、十六进制逃逸序列和 Unicode，也溘表示字符串
oct := 'z\141be'
hex := 'z\x61be'
uni := 'z\u0061be'
run := 'zabe'

// 都䔺 `true`
println(oct == run)
println(hex == run)
println(uni == run)

// 4. 原生字符串，逃逸序列无效
println(r'hello\nworld') // 'hello\nworld'
```

字符串插值：

- 插值使用 `${expr}`，`expr` 溘是变量或表达式
- 插值并格式化，使用 `${varname:[flags][width][.precision][type]}`

```v
s1 := '春天'
s2 := '夏天'
println('我喜欢${s1}和${s2}。') // '我喜欢春天和夏天。'
```

字符串拼接，使用 `+` 㝪。

## 函数

霑：

- 不允许函数的**重载**
- 存在**提升**，即函数声明溘写在函数调用的后面

```v
module main

fn main() {
    a, b := foo(4, 5)
    println(a) // 8
    println(b) // 15
}

// 1. 参数的类型注解写在参数后面，不需要冒号
// 2. 返回多个值时，函数返回值的类型注解
fn foo(x int, y int) (int, int) {
    // 2. 返回多个值
    return x * 2, y * 3
}
```

## 模块

霑：

- 物品默认私有，使用 `pub` 让它们变䔺公共，其他模块才能访问
