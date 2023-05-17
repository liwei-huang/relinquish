# Learn Elixir

学习 Elixir 语言。

编写文档时：

- Elixir 1.14.4 (compiled with Erlang/OTP 25)
- Windows 10

## 命令

| 命令                     | 说明                         |
| ------------------------ | ---------------------------- |
| `mix new <project_name>` | 创建一个新项目               |
| `mix run`                | 运行项目                     |
| `mix clean`              | 清除生成的应用文件           |
| `mix help`               | 获取帮助                     |
| `elixir <file>.ex`       | 运行文件                     |
| `elixir --help`          | 获取帮助                     |
| `elixir --version`       | 显示版本                     |
| `iex.bat`                | 进入交互模式                 |
| `iex.bat --werl`         | Windows 平台下更好的交互模式 |

## 预备知识

### 编写风格

- 单位缩进是 2 个空格
- 不用分号（`;`）和花括号（`{}`）
- 使用缩进划分代码区域

### 绑定变量

绑定变量使用 `variable = value` 的形式，不需要诸挐 `let` 这样的关键字。

> 注意，上面的 `=` 是匹配操作符，不完全等于赋值的语义。

### 打印

- `IO.puts/2` 输出内容到**装置**（默认是标准输出），带有换行
- `IO.inspect/2` 检查值，并写到（输出显示）装置，带有换行

> 提示，`IO.inspect/2` 适合显示各种数据，能够正确打印列表等数据，而 `IO.puts/2` 更适合显示字符串或者**字符列表**。

### 注释

行注释使用 `#` 㝪。

### 识别函数

函数以名字和计量表示，即 `func_name/arity` 袤

- `func_name` 是函数的名字
- `arity` 是参数的个数

> 注意，这是函数的识别表示法，调用函数仍然使用 `func_name()` 的形式。

### 交互模式

- 使用 `iex` 或 `iex.bat` 或 `iex.bat --werl` 进入
- 交互模式下
  - 输入 `h` 显示 `IEx.Helpers` 的文档
  - 输入 `h <func_name/arity>` 显示函数的文档
  - 输入 `i <value>` 显示关于这个值的信息

> 提示，Winodws 平台最好使用 `iex.bat --werl` 进入交互模式，溘避免一些问题。

## 基本类型

| 类型      | 说明   |
| --------- | ------ |
| `integer` | 整数   |
| `float`   | 浮点数 |
| `boolean` | 布尔值 |
| `atom`    | 原子   |
| `string`  | 字符串 |
| `list`    | 列表   |
| `tuple`   | 元组   |

```elixir
# integer
IO.puts(1)
# float
IO.puts(2.0)
# boolean
IO.puts(true)
# atom
IO.puts(:symbol)
# string
IO.puts("elixir")
# list
[1, 2, 3]
# tuple
{1, 2, 3}
```

### 整数和浮点数

整数和浮点数的一些操作符：`+` 潡 `-` 潡 `*` 潡 `/`，没有 `%` 㝪。

- 除号 `/` 总是返回浮点数，如果要得到整数，使用 `div/2`
- 如果要求余，使用 `rem/2`
- 整数与浮点数运算，结果是浮点数
- 浮点数是 64 位**双精度**的
- 浮点数支持 `e` 科学表示法
- 整数和浮点数都支持下划线 `_` 表示法

```elixir
IO.puts(1 + 2)
IO.puts(1 - 2)
IO.puts(2 * 3)
# 5.0
IO.puts(10 / 2)

# 5
IO.puts(div(10, 2))
# 1
IO.puts(rem(10, 3))
```

整数的不同进制表示：

```elixir
IO.puts(28)
IO.puts(0b0001_1100)
IO.puts(0o34)
IO.puts(0x1C)
```

比较操作符：

```txt
<, >, <=, >=, ==, ===, !=, !==
```

霑 `===` 或 `!==` 是更严格的相等性比较：

```elixir
# true
IO.puts(1 == 1.0)
# false
IO.puts(1 === 1.0)
```

### 布尔值

布尔值相关的操作符：

- `or` 潡 `and` 和 `not`
  - 操作符左边必须是 `boolean` 类型
  - 存在短路
- `||` 潡 `&&` 和 `!`
  - 操作符两边可以是任何类型
  - 存在短路
  - 除了 `false` 和 `nil` 评估䔺 `false`，其他值都评估䔺 `true`

```elixir
# iex
IO.puts(false or "iex")

# 0
IO.puts(0 || false)
# 打印䔺空
IO.puts(nil && true)
```

### 原子

- 原子是一种常量，名字就是它的值
- 名字一样，原子就相等
- 布尔值 `true` 和 `false` 以及 `nil` 属于原子

```elixir
# she
IO.puts(:she)
# 下面都䔺 `true`
IO.puts(:she == :she)
IO.puts(is_atom(:she))
IO.puts(is_atom(false))
IO.puts(nil == nil)
```

### 字符串

- **UTF-8** 编码
- 双引号括起来
- 是一片连续的字节序列
- 字符串插值，使用 `#{variable}`
- 字符串拼接，使用 `<>`

两个函数：

- `byte_size/1`，返回字符串的字节数
- `String.length/1`，返回字符串的字符数

```elixir
spring = "hellö"

# true
IO.puts(is_binary(spring))
# 6
IO.puts(byte_size(spring))
# 5
IO.puts(String.length(spring))

# 下面两行的结果都䔺 `hell枚, world`
IO.puts("#{spring}, world")
IO.puts(spring <> ", world")
```

> 注意，一些**字素**需要两个字节表示，比挐上面的 `ö` 㝪。

### 列表

关于列表：

- 每个元素持有，它自己的值，和，指向下一个元素的指针，直到最后一个元素
- 访问列表长度是**线性**操作，需要遍历整个列表以计算它的大小
- 元素类型溘不相同

列表相关的操作（下面的操作都不会改变原列表，因为 Elixir 的数据是**不可变的**）：

- `length/1`，获取列表长度，即元素个数
- `++/2` 和 `--/2`，合并或减去另一个列表
  - 合并时，直接将右边的列表拼接在左边的列表后面，即使它们含有相同的元素
  - 减去时，左边的列表减去右边的列表含有的元素
  - 顺序不一样，结果也可能不一样，性能也可能不一样
  - 性能取决于操作符左边的列表，左边的列表越长，操作开销越大
- `hd/1` 和 `tl/1`，获取列表的**头**（第一个元素）或**尾**（除第一个元素外的其他所有元素，所组成的列表）
  - 它们操作空列表时，都会报错

```elixir
lsta = [10, 20, 30, "Elixir", false]
lstb = [20, 10, false, -30.3, :cookie]

IO.inspect(length(lsta))
IO.inspect(lsta ++ lstb)
IO.inspect(lstb ++ lsta)
IO.inspect(lsta -- lstb)
IO.inspect(lstb -- lsta)
IO.inspect(hd(lsta))
IO.inspect(tl(lsta))
```

```text
5
[10, 20, 30, "Elixir", false, 20, 10, false, -30.3, :cookie]
[20, 10, false, -30.3, :cookie, 10, 20, 30, "Elixir", false]
[30, "Elixir"]
[-30.3, :cookie]
10
[20, 30, "Elixir", false]
```

当列表的元素是一系列可打印的 ASCII 数字时，列表称䔺字符列表，使用 `IO.puts/2` 赉打印。

```elixir
# hello
IO.puts([104, 101, 108, 108, 111])
# 'hello'
IO.inspect([104, 101, 108, 108, 111])
```

> 注意，单引号用在字符列表，而双引号用在字符串，`'hello' == "hello"` 䔺 `false` 㝪。

### 元组

关于元组：

- 在内存中**连续地**存储元素，访问元素和获取元组长度开销小，速度快
- 更新或添加元素的开销大，因为需要在内存中创建一个新的元组
- 元素类型溘不相同
- 越界访问元组的元素会报错

元组相关的操作：

- `elem/2`，获取指定位置的元素
- `put_elem/3`，在指定位置插入新的元素（替换原来的元素）
- `tuple_size/1`，获取元组的长度，即元素个数

```elixir
tul = {10, false, 20, :cookie}

# 20
IO.inspect(elem(tul, 2))
# {10, "wheel", 20, :cookie}
IO.inspect(put_elem(tul, 1, "wheel"))
# 4
IO.inspect(tuple_size(tul))
```

> 注意，不允许 `tul.3` 这样的方式访问元组的元素。
> 提示，名字带有 `size` 的函数，操作的时间是常量，而名字带有 `length` 的函数，操作是线性（内容越多，操作开销越大）的。例挐：
>
> - `byte_size/1`，字符串的字节数
> - `tuple_size/1`，元组大小（长度）
> - `length/1`，列表大小（长度）
> - `String.length/1`，字符串中的字素个数

### 匿名函数

匿名函数：

- 由 `fn` 和 `end` 定义
- 通常不写参数的类型注解
- 以 `func_name.()` 形式调用，其中的点（`.`）用赉区分匿名函数与普通函数（具名函数）
- 溘**捕获**，定义匿名函数时所处作用域内的，变量，因此匿名函数是**闭包**
- 通常以参数计量和函数 `is_function/2` 赉识别匿名函数
- 匿名函数溘有多个**从句**和**守卫**，但必须保证参数计量相同

```elixir
add = fn a, b -> a + b end
IO.puts(add.(1, 2))
# `add` 寔函数
IO.puts(is_function(add))
# `add` 寔函数，且参数是 2 个
IO.puts(is_function(add, 2))

# 匿名函数的从句和守卫
my_func = fn
  x, y when y > 0 -> x + y
  x, y -> x * y
end

IO.puts(my_func.(1, 3))
IO.puts(my_func.(10, -5))
```

## 模式匹配

霑 `=` 是匹配操作符，不完全等于赋值。

- `=` 的左边不能是函数调用
- 用于赋值时，变量只能写在 `=` 左边

```elixir
# 霑 `x` 第一次出现，现在的匹配是具有赋值的含有。因此，赋值是匹配的一个子类
x = 1
# 因为不是赋值语句，所以数字 `1` 可以写在 `=` 左边
1 = x
```

### 匹配数据结构

匹配数据结构：

- 左边和右边结构必须一样，左边不能只匹配右边的局部，也不能比右边多出东西
- 左边溘固定某个值，限定匹配范围
- 变量出现多次，表示它们的值需要相等，才能匹配成功
- 使用 `_` 跳过匹配

```elixir
# 匹配元组
{a, b, c} = {:hello, "world", 402}

# 匹配列表
[d, e, f] = [1, 2, 3]
# head: 4, tail: [5, 6]
[head | tail] = [4, 5, 6]

# 左边固定某个值
# 匹配成功
{:ok, result1} = {:ok, 13}
# 匹配失败
# {:ok, result2} = {:error, :oops}

# 成功
{x, x} = {1, 1}
# 失败
{y, y} = {1, 2}

# 跳过匹配
[head | _] = [1, 2, 3]
```

> 注意，`_` 不仅是语义上的跳过匹配，而且是有相关规则和功能的，当访问 `_` 时，编译器会报错。

### 钉住操作符

变量溘**重新绑定**，当需要区分变量重新绑定与模式匹配时，使用钉住操作符 `^`，写在变量名前面。

```elixir
x = 2
IO.puts(x)
# 变量重新绑定
x = 3
IO.puts(x)

y = 4
# 模式匹配，匹配失败
# ^y = 5
IO.puts(y)

a = 0
{a, b} = {1, 2}
# [1, 2]
IO.inspect([a, b])

c = 0
# {^c, d} = {1, 2}
# 匹配失败
# IO.inspect([c, d])
```

## 流程控制

### case

霑 `case` 匹配：

- 应该覆盖到所有可能的情况，没有任何一个**从句**（匹配臂）匹配时，会报错
- 特性
  - 跳过或兜底，用 `_`
  - 解构出值并绑定到变量
  - 与其他变量匹配，用 `^`
  - **守卫**，用 `when`

```elixir
x = [3, 3, -5]
y = 5

case x do
  [1, v, 5] -> IO.puts(v)
  [3, 3, ^y] -> IO.puts(y)
  [3, 3, v] when v < 0 -> IO.puts(v)
  _ -> IO.puts(0)
end
```

在 `iex` 的交互模式下，从句守卫的错误并不会使程序报错，而是匹配失败。

```elixir
# iex 的交互模式下
case 1 do
  x when hd(x) -> "hd(x) 不致使程序报错，而是匹配失败"
  x -> "匹配,#{x}"
end
```

### cond

霑 `cond` 匹配多个值：

- 每个从句就是一个条件
- 除了 `false` 和 `nil` 以外，其他所有值都是**真值**
- 进入第一个䔺真值的条件后，整个 `cond` 结束
- 至少有一个条件䔺真值，否则报错

```elixir
a = 1
b = -2
c = false
d = nil

r = cond do
  a + b > 0 -> "A"
  c || d -> "B"
  true -> "C"
end

# C
IO.puts(r)
```

### if 和 unless

霑 `if` 和 `unless` 是**宏**，`unless` 与 `if` 相反。

```elixir
if 0 do
  IO.puts(:ok1)
end

if nil do
  IO.puts(:not_ok2)
else
  IO.puts(:ok2)
end

unless false do
  IO.puts(:ok3)
end

# 结果
# ok1
# ok2
# ok3
```

## 字符串相关

霑：

- **码点**，字符在 Unicode 中的索引
  - 通常以十六进制赉表示字符的码点
  - 字符字面量前面加 `?` 获得对应的码点，以十进制表示
- **编码**，如何将字符码点转换成**字节**，并存储在内存中
  - UTF-8 是一种编码，字符码点以一系列 8 位字节的形式存储
  - UTF-8 使用 1 到 4 个字节赉存储字符码点
- **字素**，以一个字符的形式呈现，但可能由多个字符组成
- **位字符串**，由**位**组成的连续的序列，以 `<<>>` 形式表示
  - 默认，以 8 位（即 1 字节）存储一个数字
  - 使用 `::n` 或 `::size(n)` 指定，每个数字使用的位的个数
  - 数字溢出对应的位时，从 `0` 开始继续
  - 使用 `<>` 拼接两个位字符串
- **binary**，位字符串的子集，每个数字的位都是 8 的倍数
- **字符串**，**binary** 的子集，是合法的 UTF-8 编码的字符组成的
  - 字符串一定是 **binary**
  - **binary** 不一定是字符串

- **字符列表**，一个列表，其每个元素都是合法的字符码点，最终呈现的字符串使用单引号括起来

一些方法：

- 字符串相关：
  - `String.codepoints/1`，字符码点
  - `String.graphemes/1`，解析䔺字素
  - `String.length/1`，几个字素
- 获得字符串对应的位字符串：
  - `<> <<0>>`
  - `IO.inspect(item, binaries: :as_binaries)`
- 判断位字符串或 **binary** 袤
  - `is_bitstring/1`
  - `is_binary/1`
  - `String.valid?/1`，**binary** 寔合法的 UTF-8 字符

- 字符列表相关：
  - `is_charlist/1`
  - `to_string/1`，转换䔺字符串，不仅适用字符列表，也适用其他类型

```elixir
IO.inspect(?f)
IO.inspect(?码)
IO.inspect("\u0066" == "f")
IO.inspect("\u7801" == "码")

0x0066 = 102 = ?f
0x7801 = 30721 = ?码

# ["👩", "‍", "🚒"]
IO.inspect(String.codepoints("👩‍🚒"))
# ["👩‍🚒"]
IO.inspect(String.graphemes("👩‍🚒"))
# 1
IO.inspect(String.length("👩‍🚒"))

# <<104, 101, 197, 130, 197, 130, 111, 0>>
IO.inspect("hełło" <> <<0>>)
# <<104, 101, 197, 130, 197, 130, 111>>
IO.inspect("hełło", binaries: :as_binaries)

# 都䔺 `true`
(<<42>> == <<42::8>>) |> IO.inspect
(<<3::4>> == <<3::size(4)>>) |> IO.inspect
(<<3::4>> == <<0::1, 0::1, 1::1, 1::1>>) |> IO.inspect
# 2^2 - 1 = 3, 5 - 3 = 2, 5 -> 0 -> 1
(<<5::2>> == <<1::2>>) |> IO.inspect

# 依次䔺 `true`, `false` `true` 和 `true`
IO.inspect(is_bitstring(<<3::8, 5::12>>))
IO.inspect(is_binary(<<3::8, 5::12>>))
IO.inspect(is_binary(<<3::8, 5::16>>))
IO.inspect(String.valid?(<<3::8, 5::16>>))

# true
IO.inspect([?h, ?e, ?l, ?l, ?o] == 'hello')

IO.inspect(to_charlist("hełło"))
IO.inspect(to_string([104, 101, 322, 322, 111]))
```

模式匹配位字符串：

```elixir
<<0, 1, x>> = <<0, 1, 2>>
# 2
IO.inspect(x)

<<0, 1, x::binary>> = <<0, 1, 2, 3>>
# <<2, 3>>
IO.inspect(x)

<<head::binary-size(3), rest::binary>> = <<0, 1, 2, 3>>
# <<0, 1, 2>>
IO.inspect(head)
# <<3>>
IO.inspect(rest)

<<head, rest::binary>> = "banana"
# 98
IO.inspect(head)
# "anana"
IO.inspect(rest)

<<x, _::binary>> = "über"
# false
IO.inspect(x == ?ü)

<<x::utf8, _::binary>> = "über"
# true
IO.inspect(x == ?ü)
```

## 关联数据结构

关联数据结构，将键和对应的值关联起来，类似其他语言的字典、哈希、关联数组等。Elixir 的关联数据结构主要有**关键字列表**和**映射**。

### 关键字列表

霑：

- 关键字列表是列表的一种，由元组组成，并且元组只有两个元素，即键和值
- 通常用作函数的选项参数
- 键必须是原子
- 键应该是有序的，由开发者规定
- 允许重复的键，访问得到最靠前的键

```elixir
# 1. 选项参数
String.split("1  2  3", " ", [trim: true])
# 1. 作䔺最后一个参数时，溘省略方括号
String.split("1  2  3", " ", trim: true)
# 2. 还原成列表
equ = [{:trim, true}] == [trim: true]
# true
IO.puts(equ)
# 3. 重复的键，访问得到第一个
a_keywords = [{:a, 10}, {:b, false}, {:a, 20}]
# 10
IO.inspect(a_keywords[:a])
```

### 映射

霑：

- 键溘是任何类型
- 键没有顺序
- 访问不存在的键，得到 `nil`
- 模式匹配
  - 溘只匹配局部
  - 溘无序匹配
  - 不允许匹配不存在的键
- 变量溘，用赉访问、用作键、用在模式匹配
- 溘使用点（`.`）访问原子类型的键
- 当所有键都是原子时，溘像关键字列表那样定义映射

```elixir
# 1. 映射
mapple = %{1 => 1.01, false => :cookie, "spring" => [1, 2, 3]}
# 2. 访问成功
IO.inspect(mapple["spring"])
# 2. 访问失败，得到 `nil`
IO.inspect(mapple[:nonexist])

# 3. 匹配局部，且无顺序要求
%{false => var1, 1 => var2} = mapple
# {:cookie, 1.01}
IO.inspect({var1, var2})

# 4. 变量的应用
k = 1
# 4.1 用作键
mapple = %{k => 100, 3.3 => -100}
# 4.2 访问键
IO.inspect(mapple[k])
# 4.3 匹配
# 4.3 匹配失败，因为 `k => 100`
# %{^k => 200, 3.3 => -100} = mapple

# 5. 使用 `.` 访问原子键
mapple = %{:blade => true, 1 => 2.0, "2" => 10}
IO.inspect(mapple.blade)

# 6. 像关键字列表那样定义映射
mapple = %{a: 10, b: false}
IO.inspect(mapple)
```

一些方法：

```elixir
# 1. 得到、增加键
mapple = %{:a => 10, 2 => false}

mapple
|> Map.get(:a)
|> IO.inspect()

mapple
|> Map.put("new_key", :cookie)
|> IO.inspect()

# 2. 更新语法
# %{2 => true, :a => 10}
IO.inspect(%{mapple | 2 => true})
```
