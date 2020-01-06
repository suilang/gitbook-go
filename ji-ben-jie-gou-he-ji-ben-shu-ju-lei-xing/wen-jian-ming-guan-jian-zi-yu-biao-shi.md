# 文件名、关键字与标识

## 文件名

规则：

1. 文件名均由小写字母组成，如 `scanner.go`
2. 文件名由多个部分组成，则使用下划线 `_` 对它们进行分隔，如 `scanner_test.go`
3. 文件名不包含空格或其他特殊字符

## 标识符

Go 代码中的几乎所有东西都有一个名称或标识符。区分大小写

### 有效的标识符：

以字母（或 `_`）开头，然后紧跟着 0 个或多个字符或 Unicode 数字，如：X56、group1、\_x23、i、өԑ12。

### 无效标识符：

* 1ab（以数字开头）
* case（Go 语言的关键字）
* a+b（运算符是不允许的）

### 空白标识符 \_

> `_` 本身就是一个特殊的标识符，一般用于等号左边的占位。

> 它可以像其他标识符那样用于变量的声明或赋值（任何类型都可以赋值给它），但任何赋给这个标识符的值都将被抛弃，因此这些值不能在后续的代码中使用，也不可以使用这个标识符作为变量对其它变量进行赋值或运算。

### 匿名变量

在编码过程中，没有名称的变量、类型或方法，统称为匿名变量。

## 关键字

关键字不能作为标识符

### 25个关键字

| break | default | func | interface | select |
| :--- | :--- | :--- | :--- | :--- |
| case | defer | go | map | struct |
| chan | else | goto | package | switch |
| const | fallthrough | if | range | type |
| continue | for | import | return | var |

> 关键字不能够作标识符使用。

### 36 个预定义标识符

| append | bool | byte | cap | close | complex | complex64 | complex128 | uint16 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| copy | false | float32 | float64 | imag | int | int8 | int16 | uint32 |
| int32 | int64 | iota | len | make | new | nil | panic | uint64 |
| print | println | real | recover | string | true | uint | uint8 | uintptr |





