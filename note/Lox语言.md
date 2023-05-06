# Lox 语言

## Hello World

```lox
print "Hello, world!";
```

> print是一个内置语句，而不是库函数，所以字符串周围没有括号
> 

## 特性

### 动态类型

Lox是动态类型的。变量可以存储任何类型的值，单个变量甚至可以在不同时间存储不同类型的值。

如果尝试对错误类型的值执行操作（例如，将数字除以字符串），则会在运行时检测到错误并报告。

### 自动内存管理

在原始内存的层面上工作是有点折磨人的。调试GC的时候会让你在梦中也能看到hex dumps。

但是，请记住，这本书是关于驱散魔法和杀死那些怪物的，所以我们要写出自己的垃圾收集器。

我想你会发现这个算法相当简单，而且实现起来很有趣。

## 数据类型

### 布尔类型

```lox
true;  // Not false.
false; // Not *not* false.
```

### 数字

Lox只有一种数字：双精度浮点数。

```lox
1234;  // An integer.
12.34; // A decimal number.
```

### 字符串

```
"I am a string";
"";    // The empty string.
"123"; // This is a string, not a number.
```

### 空值

```
Nil
```

## 表达式

### 算术运算

加减乘除

```
add + me;
subtract - me;
multiply * me;
divide / me;
```

有一个数学运算符既是中缀运算符也是前缀运算符，-运算符可以对数字取负：

```
-negateMe;
```

所有这些操作符都是针对数字的，将任何其他类型操作数传递给它们都是错误的。

唯一的例外是+运算符——你也可以传给它两个字符串将它们串接起来。

## 判断

```
less < than;
lessThan <= orEqual;
greater > than;
greaterThan >= orEqual;
```

我们可以测试两个任意类型的值是否相等：

```
1 == 2;         // false.
"cat" != "dog"; // true.
```

即使是不同类型也可以，不同类型的值永远不会相等：

```
314 == "pi"; // false.
123 == "123"; // false.
```

## 逻辑运算

取非操作符，是前缀操作符 `!`，如果操作数是 true，则返回 false，反之亦然：

```
!true;  // false.
!false; // true.
```

`and` 表达式用于确认两个操作数是否都是 true。如果左侧操作数是 false，则返回左侧操作数，否则返回右侧操作数：

```
true and false; // false.
true and true;  // true.
```

`or` 表达式用于确认两个操作数中任意一个（或者都是）为 true。如果左侧操作数为 true，则返回左侧操作数，否则返回右侧操作数：

```
false or false; // false.
true or false;  // true.
```

**`and` 和 `or` 之所以像控制流结构，是因为它们会短路。**

如果左操作数为假，`and` 不仅会返回左操作数，在这种情况下，它甚至不会计算右操作数。

反过来，("相对的"?)如果 `or` 的左操作数为真，右操作数就会被跳过。

## 优先级

所有这些操作符都具有与c语言相同的优先级和结合性(当我们开始解析时，会进行更详细的说明)。

在优先级不满足要求的情况下，你可以使用()来分组：

```
var average = (min + max) / 2;
```

我把其他典型的操作符从我们的小语言中去掉了，因为它们在技术上不是很有趣。没有位运算、移位、取模或条件运算符。

## 语句

`print` 语句计算单个表达式并将结果显示给用户

```
print "Hello, world!";
```

表达式后跟分号（;）可以将表达式提升为语句状态。这被称为(很有想象力)表达式语句。

```
"some expression";
```

如果您想将一系列语句打包成一个语句，那么可以将它们打包在一个块中：

```
{
  print "One statement.";
  print "Two statements.";
}
```

## 变量

你可以使用var语句声明变量。如果你省略了初始化操作，变量的值默认为nil

```
var imAVariable = "here is my value";
var iAmNil;
```

一旦声明完成，你自然就可以通过变量名对其进行访问和赋值：

```
var breakfast = "bagels";
print breakfast; // "bagels".
breakfast = "beignets";
print breakfast; // "beignets".
```

## 控制流

```
if (condition) {
  print "yes";
} else {
  print "no";
}
```

```
var a = 1;
while (a < 10) {
  print a;
  a = a + 1;
}
```

```
for (var a = 1; a < 10; a = a + 1) {
  print a;
}
```

这个循环与之前的 while 循环做同样的事情。

大多数现代语言也有某种for-in或foreach循环，用于显式迭代各种序列类型。

在真正的语言中，这比我们在这里使用的粗糙的C-风格for循环要好。Lox只保持了它的基本功能。

## 函数

函数调用表达式与C语言中一样：

```
makeBreakfast(bacon, eggs, toast);
```

不传参

```
makeBreakfast();
```

在Lox里，你可以通过 `fun` 定义函数：

```
fun printSum(a, b) {
  print a + b;
}
```

函数体总是一个块。在其中，您可以使用return语句返回一个值：

```
fun returnSum(a, b) {
  return a + b;
}
```

如果执行到达代码块的末尾而没有return语句，则会隐式返回nil。

## 闭包

在Lox中，函数是一等公民，这意味着它们都是真实的值，你可以对这些值进行引用、存储在变量中、传递等等。下面的代码是有效的：

```
fun addPair(a, b) {
  return a + b;
}

fun identity(a) {
  return a;
}
```

由于函数声明是语句，所以可以在另一个函数中声明局部函数：

```
fun outerFunction() {
  fun localFunction() {
    print "I'm local!";
  }

  localFunction();
}
```

如果将局部函数、头等函数和块作用域组合在一起，就会遇到这种有趣的情况：
在这里，inner()访问了在其函数体外的外部函数中声明的局部变量。

```
fun returnFunction() {
  var outside = "outside";

  fun inner() {
    print outside;
  }

  return inner;
}

var fn = returnFunction();
fn();
```

要做到这一点，inner()必须“保留”对它使用的任何周围变量的引用，这样即使在外层函数返回之后，这些变量仍然存在。

我们把能做到这一点的函数称为闭包。 现在，这个术语经常被用于任何头类函数，但是如果函数没有在任何变量上闭包，那就有点用词不当了。

## 类

```
class Breakfast {
  cook() {
    print "Eggs a-fryin'!";
  }

  serve(who) {
    print "Enjoy your breakfast, " + who + ".";
  }
}
```

类的主体包含其方法。 它们看起来像函数声明，但没有fun关键字。

当类声明生效时，Lox将创建一个类对象，并将其存储在以该类命名的变量中。就像函数一样，类在Lox中也是一等公民：

```
// Store it in variables.
var someVariable = Breakfast;

// Pass it to functions.
someFunction(Breakfast);
```

接下来，我们需要一种创建实例的方法。我们可以添加某种new关键字，
但为了简单起见，在Lox中，类本身是实例的工厂函数。像调用函数一样调用一个类，它会生成一个自己的新实例：

```
var breakfast = Breakfast();
print breakfast; // "Breakfast instance".
```

Lox和其他动态类型语言一样，允许您自由地向对象添加属性：

如果一个字段不存在，那么对它进行赋值时就会先创建。

```
breakfast.meat = "sausage";
breakfast.bread = "sourdough";
```

如果您想从方法内部访问当前对象上的字段或方法，可以使用this：

```
class Breakfast {
  serve(who) {
    print "Enjoy your " + this.meat + " and " +
        this.bread + ", " + who + ".";
  }

  // ...
}
```

在对象中封装数据的目的之一是确保对象在创建时处于有效状态。为此，你可以定义一个初始化器。

如果您的类中包含一个名为init()的方法，则在构造对象时会自动调用该方法。传递给类的任何参数都会转发给它的初始化器：

```
class Breakfast {
  init(meat, bread) {
    this.meat = meat;
    this.bread = bread;
  }

  // ...
}

var baconAndToast = Breakfast("bacon", "toast");
baconAndToast.serve("Dear Reader");
// "Enjoy your bacon and toast, Dear Reader."
```

## 继承

Lox支持单继承。当你声明一个类时，你可以使用小于(<)操作符指定它继承的类

```
class Brunch < Breakfast {
  drink() {
    print "How about a Bloody Mary?";
  }
}
```

这里，Brunch是派生类或子类，而Breakfast是基类或超类。父类中定义的每个方法对其子类也可用：

```
var benedict = Brunch("ham", "English muffin");
benedict.serve("Noble Reader");
```

即使是init()方法也会被继承。在实践中，子类通常也想定义自己的init()方法。但还需要调用原始的初始化方法，以便超类能够维护其状态。

我们需要某种方式能够调用自己实例上的方法，而无需触发实例自身的方法。

与Java中一样，您可以使用super：

```
class Brunch < Breakfast {
  init(meat, bread, drink) {
    super.init(meat, bread);
    this.drink = drink;
  }
}
```

## 标准库

这是Lox中最可悲的部分。它的标准库已经超过了极简主义，接近彻底的虚无主义。
对于本书中的示例代码，我们只需要证明代码在运行，并且在做它应该做的事。为此，我们已经有了内置的print语句。

稍后，当我们开始优化时，我们将编写一些基准测试，看看执行代码需要多长时间。
这意味着我们需要跟踪时间，因此我们将定义一个内置函数clock()，该函数会返回程序启动后的秒数。


