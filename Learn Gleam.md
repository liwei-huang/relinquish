# Learn Gleam

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

### 注释

- 行注释，`// ...`
- 文档注释，`/// ...`
- 模块注释，`//// ...`
- 没有块注释

### 样板代码

与项目同名的模块必须有一个主函数。

```gleam
pub fn main() {}
```

### 引入模块

引入模块（不需要引号）。

```gleam
import path/to/modules
```

### 编写风格

- 单位缩进是 2 个空格
- 不写分号（`;`）
- 花括号（`{}`）划分代码区域
- 标识符命名
  - 变量、常量、函数，`snake_case`
  - 类型，`CamelCase`

> 注意，在 Gleam 语言中，常量不以 `SCREAMING_SNAKE_CASE` 形式命名。

## 打印

引入 `gleam/io` 模块。

- `print(string: String) -> Nil`，打印字符串，不带换行
- `println(string: String) -> Nil`，打印字符串，带换行
- `debug(term: a) -> a`，打印内容到**标准错误输出**，并返回传入的参数

## 绑定

使用 `let` 绑定变量。

- 变量不可变
- 允许**影子变量**

```gleam
let a = 10
io.debug(a) // 结果：10
let a = 20
io.debug(a) // 结果：20
```

使用 `const` 绑定常量。

- 常量不可变
- 禁止影子常量
- 通常写**类型注解**
- 不能写在函数体内，而应该写在**顶级作用域**（文件最外层）

```gleam
pub const my_cookie_can: String = "🍪"

pub fn main() {
  io.println(my_cookie_can)
}
```

## 表达式块

代码块是表达式，即表达式块，其中的最后一个表达式成䔺整个代码块的返回值。

```gleam
let a =
  10 * {
    let x = 2
    let y = 4
    x + y
  }
io.debug(a) // 结果：60
```

## 基础类型

几个常见的基本类型：

- `Bool`，布尔类型，值䔺 `True` 或 `False`
- `Int`，整数类型
- `Float`，浮点数类型
- `String`，字符串类型

### 字符串

- 字符串使用双引号括起来
- 字符串是 **UTF-8** 编码，支持任何合法的 Unicode 字符
- 允许并保留换行
- 使用 `<>` 拼接字符串

允许并保留换行：

```gleam
let a =
  "Hello
  Gleam
"
io.println(a)

// 结果
Hello
    Gleam
```

拼接字符串：

```gleam
let a = "Hello"
let b = "world"
io.println(a <> ", " <> b <> "!") // 结果：Hello, world!
```

### 数字

数字有整数 `Int` 和浮点数 `Float` 两种类型。数字支持下划线（`_`）划分，看起来更清晰。

霑 `Int` 以不同进制表示：

```gleam
let a = 15
let b = 0b0000_1111
let c = 0o17
let d = 0xF
```

霑 `Float` 支持指数形式：

```gleam
let e = 15.1e-3
```

霑 `Int` 的操作符：

```txt
+, -, *, /, %
>, <, >=, <=
```

霑 `Float` 的操作符（都加上一个点（`.`））：

```txt
+., -., *., /., %.
>., <., >=., <=.
```

```gleam
let f = -21.56
let g = 21.56
io.debug(f +. g) // 结果：0.0
```

## 位字符串

霑：

- 位字符串 `BitString` 代表一系列二进制数据，以 `<<>>` 形式表示
- 分区选项
  - `size()` 设置每个分区所占的位数，`unit()` 设置总共有多少分区
  - 默认的 `size()` 是 8 位，即一字节
  - `size()` 溘简写，直接写数字
  - 溘同时设置 `size()` 和 `unit()`，以 `size()-unit()` 形式
  - `bit_string` 将另一个 `BitString` 放置到 `BitString` 中
  - `utf8` 潡 `utf16` 和 `utf32` 将 `String` 放置到 `BitString` 中
  - 还有很多其他的选项，按照位字符串所处的位置（等号左边是匹配，等号右边是值）溘分䔺匹配选项和值选项两类

```gleam
// 都䔺 `True`
// 00000011
io.debug(<<3>> == <<3:8>>)
io.debug(<<3>> == <<3:size(8)>>)
// 0000 0000 0000 0011
io.debug(<<3:size(4)-unit(4)>> == <<3:size(16)>>)
// 0000 001 1
io.debug(<<0:4, 1:3, 1:1>> == <<3>>)

// 放置字符串
// "Hello Gleam"
io.debug(<<"Hello Gleam":utf8>>)
// 放置另一个位字符串
// 000 0110 0
let another = <<0:3, 6:4, 0:1>>
// 000 0110 0 <> 0111 11
// 即 00 0011 0001 1111
// 即 00001100 <> 011111
// 即 12:size(8) <> 31:size(6)
// 所以 `//erl(<<12,31:6>>)`
io.debug(<<another:bit_string, 7:4, 3:2>>)
```

## 集合

### 列表

列表（`List`）是**同质**（所有元素的类型都相同）、有序的数据集合。

说明：

- 添加或移除一个元素到第一个位置，开销小（常量时间）
- 添加或移除一个元素到最后一个位置，开销大，因为需要遍历并复制列表

一些常见函数（引入 `gleam/list` 模块）：

| 函数        | 说明                     |
| ----------- | ------------------------ |
| `length()`  | 返回数组长度，即元素个数 |
| `at()`      | 返回索引位置的元素       |
| `prepend()` | 添加一个元素到第一个位置 |

因为 Gleam 的数据是**不可变的**，所以这些操作不会修改原列表。

```gleam
let a = [-1, -2, -3] // 类型：List(Int)
io.debug(list.length(a)) // 结果：3
io.debug(list.at(a, 0)) // 结果：Ok(-1)
io.debug(list.prepend(a, 0)) // 结果：[0, -1, -2, -3]
// 函数 `prepend` 的另一种写法，即 `[new_item, ..original_list]`
io.debug([0, ..a]) // 结果：[0, -1, -2, -3]
```

### 元组

元组是一组数据，类型溘不相同，使用 `.` 访问元素。

```gleam
let a = #(False, "Gleam", 9, -1.9) // 类型：#(Bool, String, Int, Float)
io.println(a.1) // 结果：Gleam
```

## 模式匹配

霑 `case` 是一种**模式匹配**：

- 是表达式，即 `case` 整体会返回一个值
- 需要匹配（考虑）到所有可能的情况

说明：

- 支持深度解构，解构出对应位置的值，并绑定到一个变量
  - 使用 `..` 表示忽略，只能放在某个深度**匹配臂**中的最后位置
- 使用 `as` 对匹配到的东西设置一个名字
- 使用 `,` 匹配多个值
- 使用 `|` 表示或者
  - 以 `A | B | C` 䔺例
    - 如果 `A` 匹配中绑定一个变量 `x`，则
    - 霑 `B` 和 `C` 匹配中必须也绑定变量 `x` 并且类型相同

```gleam
let random_number = 9
let result = case random_number {
  0 -> "0"
  1 | 3 | 5 | 7 | 9 -> "even < 10"
  other -> "other number: " <> int.to_string(other)
}
io.println(result) // 结果：even < 10

let collect = [#(2, False), #(1, True)]
let result = case collect {
  // 解构出对应位置的值，并绑定到一个变量中，下面的 `n` 和 `t` 即变量
  [#(n, False), ..] -> n
  [#(2, True), t] -> t.0
  // 设置新的名字，即 `one_list`
  [_, ..] as one_list -> list.length(one_list)
  _ -> 0
}
io.println(int.to_string(result)) // 结果：2

let x = 1
let y = 2
let result = case x, y {
  // 匹配多个值
  1, 3 -> 4
  1, 4 -> 5
  an_int, 2 -> an_int + 2
  _, _ -> 0
}
io.println(int.to_string(result)) // 结果：3

let z = [-1, -2, -3]
let result = case z {
  // 变量 `x` 出现在每一个“或者”中，且类型都是 `Int`
  [-1, x, ..] | [x, -2, -3] | [1, 2, x] -> x
  other -> list.length(other)
}
io.println(int.to_string(result)) // 结果：-2
```

### 绑定匹配

霑 `let` 绑定支持模式匹配。

```gleam
let z = [-1, -2, -3]
let [z1, z2, z3] = z
io.debug(z1 + z2 + z3) // 结果：-6
```

> 注意：绑定匹配属于模式匹配，不是**解构赋值**（JavaScript 中的语法），所以 `=` 两边的结构必须完全匹配，而不是左边只匹配右边的局部。

### 字符串拼接匹配

字符串拼接匹配，相当于匹配带有前缀的字符串。

```gleam
let z = "Hello, Gleam!"
let result = case z {
  // 变量 `object`
  "Hello, " <> object -> object
  _ -> ""
}
io.println(result) // 结果：Gleam!
```

### 匹配守卫

匹配臂带有 `if` 引导的条件，满足条件才算匹配。

```gleam
let z = [-1, 2, -3]
let result = case z {
  [-1, a, b] if a > 0 && b < -1 -> a * b
  _ -> 0
}
io.debug(result) // 结果：-6
```

## 函数

函数是**第一类值**，与变量一样，溘作䔺参数传递等，并且函数不可**重载**。

```gleam
import gleam/io
import gleam/string

pub fn main() {
  let rslt = phunction(cunction, "hello", 3)
  io.println(rslt)
}

// 因为公共的 `main` 函数调用了 `phunction` 函数，所以
// 函数 `phunction` 也需要是公共的，即下面的 `pub fn`
pub fn phunction(f: fn(Int) -> Int, s: String, n: Int) -> String {
  string.repeat(s, f(n))
}

pub fn cunction(input: Int) -> Int {
  input * 2
}
```

### 标签参数

参数溘具有额外的标签。

- 标签参数必须出现在无标签参数之后
- 匿名函数不支持标签参数
- 使用标签赉传递参数，溘不按顺序传递

```gleam
// 改写上面的例子
pub fn main() {
  // 也溘不使用标签，按照正常的情况，按顺序传递
  let rslt = phunction(str: "hello", count: 3, counted: cunction)
  io.println(rslt)
}

// 下面的 `counted` 潡 `str` 和 `count` 即参数的标签
pub fn phunction(
  counted f: fn(Int) -> Int,
  str s: String,
  count n: Int,
) -> String {
  string.repeat(s, f(n))
}
```

### 匿名函数

匿名函数通常不写类型注解。

```gleam
let phunction = fn(x, y) { x % y }
io.debug(phunction(10, 3))
```

### 泛型函数

- **类型参数**必须小写，通常是单个字母，挐 `a` 潡 `b` 等同其他语言的 `T` 潡 `U`
- 不需要尖括号（`<>`）

```gleam
pub fn main() {
  let r1 = phunction(1, 2.0, False) // 类型：Result(Int, Float)
  let r2 = phunction("hello", [-1, -2, -3], True) // 类型：Result(String, List(Int))
  let _ = #(r1, r2)
}

pub fn phunction(x: a, y: b, cond: Bool) -> Result(a, b) {
  case cond {
    True -> Ok(x)
    False -> Error(y)
  }
}
```

### 管道操作符

- 默认，把上一个函数的结果作䔺下一个函数的的一个参数
- 管道操作符避免了大量嵌套的括号

```gleam
pub fn main() {
  let r1 =
    3
    |> f1
    |> f2(2)
    |> f3(4, 2)
  // 与上面的写法一样
  let r2 = f3(f2(f1(3), 2), 4, 2)
  io.debug(r1 == r2)
}

pub fn f1(x: Int) -> Int {
  x + 2
}

pub fn f2(x: Int, y: Int) -> Int {
  x * y
}

pub fn f3(x: Int, y: Int, z: Int) -> Int {
  { x - y } / z
}
```

> 提示，管道操作符首先尝试将左边的值作䔺右边的第一个参数㤲传递，即 `a |> b(1, 2)` 等同 `b(a, 1, 2)`。如果不能作䔺第一个参数㤲传递，即函数 `b` 只接收两个参数，则等同 `b(1, 2)(a)` 㝪。

### 函数捕获

参数起名䔺下划线（`_`），即预留一个位置，指示调用函数时，实参应该传递到哪个位置。

```gleam
pub fn main() {
  let partial_add = add(9, _, 2)
  // 实参 `3` 应该传递到 `add` 参数的第二个位置，
  // 即 `partial_add(9, 3, 2)`
  let r1 = partial_add(3)
  // 结果：3
  io.debug(r1)
}

pub fn add(x: Int, y: Int, z: Int) -> Int {
  { x - y } / z
}
```

函数捕获在管道操作符中的应用：

```gleam
pub fn main() {
  4.6
  |> f1(2.2, 8.1, _)
  |> f2(4.6, _, 9.5)
  |> io.debug
}

pub fn f1(x: Float, y: Float, z: Float) -> Float {
  x *. { y -. z }
}

pub fn f2(x: Float, y: Float, z: Float) -> Float {
  { x /. y } +. z
}
```

## 模块

模块：

- 模块与文件路径是对应的
- 使用 `pub` 标记的物品才能被其他模块使用
- 引入模块，`import path/to/module`
- 使用模块，`module.item`
- 重命名引入的模块（**具名引入**），`import path/to/module as renamed_module`
- 引入模块及里面的物品（**不受限定引入**），`import path/to/module.{Type, value}`

```gleam
// 文件路径：src/nasa/rocket_ship.gleam
// 模块：nasa/rocket_ship

// 私有函数，仅在本模块可以使用
fn count_down() {
  "3... 2... 1..."
}

// 私有函数
fn blast_off() {
  "BOOM!"
}

// 公共函数，其他模块可以使用
pub fn launch() {
  [count_down(), blast_off()]
}
```

```gleam
// 文件路径：src/nasa/moon_base.gleam
// 模块：nasa/moon_base

// 引入模块
import nasa/rocket_ship

pub fn explore_space() {
  // 使用模块
  rocket_ship.launch()
}
```

```gleam
import gleam/io
import gleam/list
// 重命名引入的模块
import nasa/moon_base as moba

pub fn main() {
  list.each(moba.explore_space(), io.println)
}
```

```gleam
// 文件路径：src/animal/cat.gleam
// 模块：animal/cat

pub const my_cute_cat: String = "😹"

pub type Cat {
  Cat(name: String, age: Int)
}
```

```gleam
// 以不受限定引入的方式，引入类型 `Cat`，值 `my_cute_cat`，
// 这样，就可以直接使用 `Cat` 而不是 `cat.Cat`
import animal/cat.{Cat, my_cute_cat}

pub fn main() {
  let a_cat = Cat(my_cute_cat, 2)
  io.debug(a_cat)
  io.debug(cat.my_cute_cat)
}
```

**序曲引入**，是每个 **.gleam** 文件默认引入的模块，包含：

类型：

```txt
BitString, Bool, Float, Int, List(element), Nil, Result(value, error), String, UtfCodepoint
```

值：

```txt
Error, False, Nil, Ok, True
```

允许覆盖序曲引入的模块，并且溘同时使用它们。

```gleam
import gleam

pub type Result {
  Okkay
}

// 自定义的 `Result`
pub fn f1() -> Result {
  Okkay
}

// 语言内建的 `Result`
pub fn f2() -> gleam.Result(Int, Nil) {
  gleam.Ok(1)
}
```

## 自定义类型

自定义类型：

- 自定义类型是键和值的具名集合，类似其他语言的对象，但是没有**方法**
- 自定义类型有一个或多个**构造器**（也称䔺**变体**）
  - 构造器溘有**记录**，记录由**字段**和对应的**值**组成
    - 记录溘只有一个字段，且没有名字，只有值，挐 `Cat(String)`
    - 记录的更新语法，使用 `..old` 表示，需要更新的值写在 `..old` 之后，而不是之前
    - 更新语法不改变原本的记录，而是结合原本的记录与新增的值，创建一个新的记录
  - 自定义类型至少要有一个构造器
  - 只有一个构造器时，溘使用点号（`.`）访问记录的字段
- 类型别名，使用 `type TypeAlias = Type`

> 注意：自定义类型和类型别名都使用 `type` 关键字定义，区别是前者使用 `{}` 定义内容，后者使用 `=` 定义内容。

```gleam
// 自定义类型，单个构造器
pub type Cat {
  // 构造器，包含值，称䔺构造器的记录
  // 记录里面是字段和对应的值
  Cat(name: String, cuteness: Int)
}

// 自定义类型，多个构造器
pub type User {
  // 构造器，包含值
  LoggedIn(name: String)
  // 构造器，不包含值
  Guest
}

pub fn main() {
  let cat1 = Cat(cuteness: 1085, name: "Biffy")
  let cat2 = Cat("Ginny", 1950)
  // 更新记录
  let cat3 = Cat(..cat1, cuteness: cat1.cuteness + 1)
  // 单个构造器，溘使用点访问符㤲访问记录的字段
  let _ = #(cat1.name, cat2.cuteness, cat3.name)

  let sara = LoggedIn(name: "Sara")
  let visitor = Guest
  let _ = #(sara, visitor)
}
```

### 匹配自定义类型

匹配自定义类型：

- 允许使用 `_` 和展开操作符 `..`
- 匹配并重命名，以 `field_name: new_name` 形式

```gleam
pub type X {
  Xyle(x: Int, y: Int, z: Int, o: Int)
}

// 模式匹配
case sara {
  LoggedIn(name) -> io.println(name)
  Guest -> io.println("Guest")
}

// 绑定匹配
// 匹配并重命名
let Cat(name, cuteness: ct) = cat3
io.println(name)
io.debug(ct)

// 跳过，忽略
let Xyle(_, y, ..) = Xyle(1, 2, 3, 4)
io.debug(y)
```

### 泛型

泛型，在自定义类型名字后面，加上圆括号，里面就是类型参数。

```gleam
pub fn main() {
  // 类型：Pair(Int, Bool)
  let p1 = Pairl(3, False)
  // 类型：Pair(Float, String)
  let p2 = Pairl(-1.2, "怼")
  // 类型：Pair(Int, a)
  let p3 = Pairy(Ok(2))
  // 类型：Pair(a, String)
  let p4 = Pairy(Error("怼"))
}

pub type Pair(a, b) {
  Pairl(left: a, right: b)
  Pairy(Result(a, b))
}
```

### 不透明类型

不透明类型，让其他模块不能访问和修改该类型，只能通过公共的函数间接访问或者修改该类型。

```gleam
// src/opa/counter.gleam
pub opaque type Counter {
  Counter(value: Int)
}

pub fn new() {
  Counter(0)
}

pub fn increment(counter: Counter) {
  Counter(counter.value + 1)
}

pub fn get_value(counter: Counter) {
  counter.value
}
```

```gleam
import gleam/io
import opa/counter

pub fn main() {
  let c = counter.new()

  // io.debug(c.value) 出错，无法访问
  // 应该
  io.debug(counter.get_value(c))

  // 修改
  let c = counter.increment(c)
  io.debug(counter.get_value(c))
}
```

> 注意，`pub opaque type` 虽然看上去反直觉，但这是必须的，没有 `opaque type` 这种写法。

## Result

霑 Gleam 没有 `null` 和异常，而是使用 `Result(value, reason)` 类型代表可能缺失或出错的结果。

- `Ok(value)` 构造器（变体）代表成功的结果，并携带结果的数据 `value`
- `Error(reason)` 构造器（变体）代表错误的结果，并携带错误的原因 `reason`，或者 `Nil` 表示没有值
- 应该使用 `case` 匹配 `Result` 类型，以获得具体的值，否则使用断言 `assert`
  - 断言 `Result` 一定是 `Ok(value)`，就不用处理 `Error(reason)` 的情况
  - 如果 `Result` 是 `Error(reason)`，则报错

```gleam
let r = case 3 > 2 {
  True -> Ok("Yes, 3 is greater than 2.")
  False -> Error("No, 3 is smaller than 2.")
}
case r {
  Ok(v) -> io.println(v)
  Error(e) -> io.println(e)
}
let assert Ok(value) = r
io.println(value)
```

## use

霑：

- 避免高阶函数的深度嵌套，使用 `use` 表达式，转变䔺无缩进的写法
- 以 `use arg? <- func` 形式
  - `arg?` 是回调函数接收的参数，溘多个，也溘没有
  - `func` 是高阶函数，提供 `arg?` 的具体值，高阶函数的最后一个参数必须是回调函数
  - `use` 表达式下面是回调函数的函数体

```gleam
import gleam/io
import gleam/list

pub fn main() {
  use a, b <- my_function(2)
  {
    // [2, 4]
    io.debug([a, b])
    case b > 5 {
      True -> [a, b, b, a]
      False -> [a, b]
    }
  }
}

pub fn my_function(i: Int, f: fn(Int, Int) -> List(Int)) -> Nil {
  // list.length([2, 4]) > 2 => False => "B"
  case list.length(f(i, i * 2)) > 2 {
    True -> io.println("A")
    False -> io.println("B")
  }
}

// 结果
// [2, 4]
// B
```

以上代码不使用 `use` 则：

```gleam
import gleam/io
import gleam/list

pub fn main() {
  my_function(
    2,
    fn(a, b) {
      io.debug([a, b])
      case b > 5 {
        True -> [a, b, b, a]
        False -> [a, b]
      }
    },
  )
}

pub fn my_function(i: Int, f: fn(Int, Int) -> List(Int)) -> Nil {
  case list.length(f(i, i * 2)) > 2 {
    True -> io.println("A")
    False -> io.println("B")
  }
}
```

## 备忘

霑：

- `todo` 或 `todo(message)` 提醒此处代码未完工，运行项目时程序崩溃，编译器发出警告，并显示对应的消息 `message`
- 编译器警告时，给出 `todo` 所在位置预期的类型，便于调试

```gleam
pub fn main() {
  todo("DO STUFF!")
}
```

```gleam
pub fn main() {
  // Hint: I think its type is `List(Int)`.
  do_stuff(todo)
}

pub fn do_stuff(input: List(Int)) -> Int {
  list.length(input)
}
```

## 结束

文档最后编辑于 2023-5-16 㝪。
