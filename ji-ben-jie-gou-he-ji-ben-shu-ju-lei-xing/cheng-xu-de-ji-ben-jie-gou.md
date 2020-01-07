# 程序的基本结构

## hello，world

```go
package main

import "fmt"

func main() {
	fmt.Println("hello, world")
}
```

## 包package

包是结构化代码的一种方式：每个程序都由包（通常简称为 pkg）的概念组成，可以使用自身的包或者从其它包中导入内容。

一个包由一个或多个以 `.go` 为扩展名的源文件组成，文件名和包名一般来说都是不相同的。

1. 每个 Go 文件都属于且仅属于一个包。
2. 必须在源文件中非注释的第一行指明这个文件属于哪个包，如：`package utils`。
3. `package main`表示一个可独立执行的程序，每个 Go 应用程序都包含一个名为 `main` 的包。
4. 编译包名不是为 main 的源文件，如 `pack1`，编译后产生的对象文件将会是 `pack1.a` 而不是可执行程序。
5. 所有的包名都应该使用小写字母。

### 标准库

在 Go 的安装文件里包含了一些可以直接使用的包，即标准库。

> 在 Windows 下，标准库的位置在 Go 根目录下的子目录 `pkg\windows_386` 中；在 Linux 下，标准库在 Go 根目录下的子目录 `pkg\linux_amd64` 中（如果是安装的是 32 位，则在 `linux_386` 目录中）。一般情况下，标准包会存放在 `$GOROOT/pkg/$GOOS_$GOARCH/` 目录下。

Go 的标准库包含了大量的包（如：fmt 和 os），

* **包的依赖关系决定了其构建顺序。**如果想要构建一个程序，则包和包内的文件都必须以正确的顺序进行编译。
* 属于同一个包的源文件必须全部被一起编译，一个包即是编译时的一个单元，因此根据惯例，**每个目录都只包含一个包。**
* **如果对一个包进行更改或重新编译，所有引用了这个包的客户端程序都必须全部重新编译。**

Go 中的包模型采用了显式依赖关系的机制来达到快速编译的目的，编译器会从后缀名为 `.o` 的对象文件（需要且只需要这个文件）中提取传递依赖类型的信息。

如果 `A.go` 依赖 `B.go`，而 `B.go` 又依赖 `C.go`：

* 为了编译 `A.go`, 编译器读取的是 `B.o` 而不是 `C.o`.
* 顺序为：编译 `C.go`, `B.go`, 然后是 `A.go`.

这种机制对于编译大型的项目时可以显著地提升编译速度。

### 引包

一个 Go 程序是通过 `import` 关键字将一组包链接在一起。

`import "fmt"` 告诉 Go 编译器这个程序需要使用 `fmt` 包（的函数，或其他元素），`fmt` 包实现了格式化 IO（输入/输出）的函数。

#### 引入多个包：

```go
import "fmt"
import "os"

// 或者
import "fmt"; import "os"

// 或者
import (
   "fmt"
   "os"
)

//或者
import ("fmt"; "os")
```

#### 查找规则：

1. 如果包名以 `./` 开头，则 Go 会在相对目录中查找；
2. 如果包名以 `/` 开头（在 Windows 下也可以这样使用），则会在系统的绝对路径中查找。
3. 其他： `"fmt"` 或者 `"container/list"`，则 Go 会在全局文件进行查找；

导入包即等同于包含了这个包的所有的代码对象。

* 除了符号 `_`，包中所有代码对象的标识符必须是唯一的，以避免名称冲突。
* 但是相同的标识符可以在不同的包中使用，因为可以使用包名来区分它们。

> 如果你导入了一个包却没有使用它，则会在构建程序时引发错误，如 `imported and not used: os`，这正是遵循了 Go 的格言：“没有不必要的代码！“。

#### 可见性规则：

*  public：当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，使用这种形式的标识符的对象就可以被外部包的代码所使用。
* private ：以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的。

> 大写字母可以使用任何 Unicode 编码的字符，比如希腊文，不仅仅是 ASCII 码中的大写字母

#### 调用规则：

1. 在导入一个外部包后，能够且只能够访问该包中导出的对象。
2. 假设在包 pack1 中我们有一个变量或函数叫做 Thing（以 T 开头，所以它能够被导出），那么在当前包中导入 pack1 包，Thing 就可以像面向对象语言那样使用点标记来调用：`pack1.Thing`（pack1 在这里是不可以省略的）。
3. 包也可以作为命名空间使用，帮助避免命名冲突（名称冲突）：两个包中的同名变量的区别在于他们的包名，例如 `pack1.Thing` 和 `pack2.Thing`。

#### 别名：

通过使用包的别名来解决包名之间的名称冲突

```go
package main

import fm "fmt" // alias

func main() {
   fm.Println("hello, world")
}
```

## 函数

这是定义一个函数最简单的格式：

`func funcName()`

你可以在括号 `()` 中写入 0 个或多个函数的参数（使用逗号 `,` 分隔），每个参数的名称后面必须紧跟着该参数的类型。

### main 函数

启动后第一个执行的函数,每一个可执行程序所必须包含的，（如果有 init\(\) 函数则会先执行该函数）。该函数一旦返回就表示程序已成功执行并立即退出。

* main 包的源代码没有包含 main 函数，则会引发构建错误 `undefined: main.main`。
* main 函数既没有参数，也没有返回类型。为 main 函数添加了参数或者返回类型，将会引发构建错误：`func main must have no arguments and no return values results.`

> 程序正常退出的代码为 0 即 `Program exited with code 0`；如果程序因为异常而被终止，则会返回非零值，如：1。这个数值可以用来测试是否成功执行一个程序。

### 函数结构：

* 函数里的代码（函数体）使用大括号 `{}` 括起来。
* 左大括号 `{` 必须与方法的声明放在同一行，
* 右大括号 `}` 需要被放在紧接着函数体的下一行。如果函数非常简短，也可以将它们放在同一行：

> **Go 语言虽然看起来不使用分号作为语句的结束，但实际上这一过程是由编译器自动完成。**

符合规范的函数一般写成如下的形式：

```go
func functionName(parameter_list) (return_value_list) {
   …
}
```

其中：

* parameter\_list 的形式为 `(param1 type1, param2 type2, …)`
* return\_value\_list 的形式为 `(ret1 type1, ret2 type2, …)`

只有当某个函数需要被外部包调用的时候才使用大写字母开头，并遵循 Pascal 命名法；否则就遵循骆驼命名法，即第一个单词的首字母小写，其余单词的首字母大写。

下面这一行调用了 `fmt` 包中的 `Println` 函数，可以将字符串输出到控制台，并在最后自动增加换行字符 `\n`：

```text
fmt.Println（"hello, world"）
```

使用 `fmt.Print("hello, world\n")` 可以得到相同的结果。

## 注释

注释不会被编译，但可以通过 godoc 来使用

* 单行注释，以 `//` 开头的单行注释。
* 多行注释，也叫块注释，均已以 `/*` 开头，并以 `*/` 结尾，且不可以嵌套使用，多行注释一般用于包的文档描述或注释成块的代码片段。

每一个包应该有相关注释，在 package 语句之前的块注释将被默认认为是这个包的文档说明，其中应该提供一些相关信息并对整体功能做简要的介绍。一个包可以分散在多个文件中，但是只需要在其中一个进行注释说明即可。当开发人员需要了解包的一些情况时，自然会用 godoc 来显示包的文档说明，在首行的简要注释之后可以用成段的注释来进行更详细的说明，而不必拥挤在一起。另外，在多段注释之间应以空行分隔加以区分。

示例：

```go
// Package superman implements methods for saving the world.
//
// Experience has shown that a small number of procedures can prove
// helpful when attempting to save the world.
package superman
```

几乎所有全局作用域的类型、常量、变量、函数和被导出的对象都应该有一个合理的注释。如果注释出现在函数前面，例如函数 Abcd，则要以 `"Abcd..."` 作为开头，称为文档注释。

示例：

```go
// enterOrbit causes Superman to fly into low Earth orbit, a position
// that presents several possibilities for planet salvation.
func enterOrbit() error {
   ...
}
```

godoc 工具会收集这些注释并产生一个技术文档。

## 类型

变量（或常量）包含数据，这些数据可以有不同的数据类型，简称类型。使用 var 声明的变量的值会自动初始化为该类型的零值。类型定义了某个变量的值的集合与可对其进行操作的集合。

类型可以是基本类型，如：int、float、bool、string；结构化的（复合的），如：struct、array、slice、map、channel；只描述类型的行为的，如：interface。

结构化的类型没有真正的值，它使用 nil 作为默认值。

> Go 语言中不存在类型继承。



函数也可以是一个确定的类型，就是以函数作为返回类型。这种类型的声明要写在函数名和可选的参数列表之后，例如：

```text
func FunctionName (a typea, b typeb) typeFunc
```

你必须在函数体中的某处返回类型为 typeFunc 的变量 var：`return var`

一个函数可以拥有多返回值，返回类型之间需要使用逗号分割，并使用小括号 `()` 将它们括起来，如：

```go
func FunctionName (a typea, b typeb) (t1 type1, t2 type2){
    // 省略非关键代码。。。
    // 其中， var1为type1类型， var2为type2类型
    return var1,var2
}
```

这种多返回值一般用于判断某个函数是否执行成功（true/false）或与其它返回值一同返回错误消息。

使用 type 关键字可以定义你自己的类型，但是也可以定义一个已经存在的类型的别名，如：

```text
type IZ int
```

如果你有多个类型需要定义，可以使用因式分解关键字的方式，例如：

```text
type (
   IZ int
   FZ float64
   STR string
)
```

