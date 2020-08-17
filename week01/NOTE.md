# 类型
- Number
- String
- Boolean
- Object
- Null
- Undefined
- Symbol

## Number
- IEEE754 双精度浮点类型
  - 1个符号位
  - 11个指数位
  - 52个精度位
- 语法
  - 十进制
    - 0 直接写法
    - 0\. 可以带小数点
    - .1 可以省略整数位
    - 1e3 可以使用科学计数法
    - 0 .toString() 使用toString需要加空格
  - 二进制: 0b开头
  - 八进制：0o开头
  - 十六进制：0x开头

## String
- Character：字符
- Code Point：字符在编码表里面的数字
- Encoding
- 字符集
  - ASCII：
  - Unicode：包含所有字符
  - UCS：0000 到 FFFF
  - GB：国(G)标(B)
    - GB2312：第一版
    - GBK(GB13000)：国标扩(K)充
    - GB18030：第三版，几乎包含所有字符
  - ISO-8859：东欧的一种
  - BIG5：早年台湾的一种
- 编码集
  - UTF
    - UTF-8：8位表示一个字符
    - UTF-16：16位表示一个字符

### UTF8
字节表示：
- 一个字节：0XXXXXXX
  - 范围：7个X就是 2^7 = 128 = 0x7f + 1
- 两个字节：110XXXXX 10XXXXXX
  - 范围：11个X就是 2^11 = 2048 = 0x7ff + 1
- 三个字节：1110XXXX 10XXXXXX 10XXXXXX
  - 范围：16个X就是 2^16 = 65536 = 0xffff + 1
- 四个字节：11110XXX 10XXXXXX 10XXXXXX 10XXXXXX
  - 范围： 21个X就是 2^21 = 2097152 = 0x1fffff + 1
- 五个字节：111110XX 10XXXXXX 10XXXXXX 10XXXXXX 10XXXXXX
- 六个字节：1111110X 10XXXXXX 10XXXXXX 10XXXXXX 10XXXXXX 10XXXXXX
```js
function encodeUTF8(str) {
  const codePoint = str.charCodeAt(0)
  const result = []
  if (codePoint < 2**7) {
    result.push(codePoint)
  } else if(codePoint < 2**(5 + 6)) {
    result.push(0b11000000 | (0b11111 & (codePoint >> 6))) // [1, 3]
    result.push(0b10000000 | (0b111111 & codePoint)) // [6, 11]
  } else if(codePoint < 2**(4 + 6 + 6)) {
    result.push(0b11100000 | (0b1111 & (codePoint >> 12))) // [1, 4]
    result.push(0b10000000 | (0b111111 & (codePoint >> 6))) // [5, 10]
    result.push(0b10000000 | (0b111111 & codePoint)) // [11, 16]
  } else if(codePoint < 2**(3 + 6 + 6 + 6)) {
    result.push(0b11110000 | (0b111 & (codePoint >> 18))) // [1, 3]
    result.push(0b10000000 | (0b111111 & (codePoint >> 12))) // [4, 9]
    result.push(0b10000000 | (0b111111 & (codePoint >> 6))) // [10, 15]
    result.push(0b10000000 | (0b111111 & codePoint)) // [16, 21]
  }
  return result
}
```
## Boolean
- true
- false

## Null & Undefined
- Null
  - 表示有值但为空
  - 是关键字
- Undefined
  - 表示没有设置过这个值
  - 是一个全局变量，可以重新赋值
  - 可以用void 0 代替

## Object
### 通用定义
- 任何一个对象都是唯一的，这与它本身的状态无关
- 即使状态完全一致的两个对象，也并不相等
- 我们用状态来描述对象
- 我们用状态的改变来描述行为

### Class
- 归类：多个小类提取共性抽象出高级类，自下而上
- 分类：一个大类自上而下分

### Prototype
- 不试图用严谨的分类，用“相似”这样的方法去描述对象

### Object in JavaScript
#### 值属性
  - Data Property：数据属性
    - \[\[value]]
    - writable
    - enumerable
    - configurable
  - Accessor Property：访问器属性
    - get
    - set
    - enumerable
    - configurable
#### 原型机制

#### 对象 API / Grammar
- {}、.、[]、 Object.defineProperty
  - 基本的对象能力
- Object.create / Object.serPrototypeOf / Object.getProtytypeOf
  - 基于原型的对象描述方式
- new / class / extends
  - 基于类的方式描述对象
- new / function / prototype
  - 使用 prototype 来基于类的方式描述对象 （不推荐使用）

#### 特殊对象
- Function Object
  - 会有 \[\[call]] 这个行为
- Array Object
  - 会有 \[\[length]] 这个属性
- Object.prototype
  - 作为所有正常对象的默认原型，不能再给它设置原型了
- String
  - 为了支持下标运算，String 的正整数属性访问回去字符串里查找
- Arguments
  - arguments 的非负整数型下标属性跟对应的变量联动
- 模块的 namespace 对象
  - 特殊的地方非常多，更一般对象完全不一样，尽量只用于 import
- 类型数组和数组缓冲区
  - 跟内存相关联，下标运算比较特殊
- bind 后的 function
  - 跟原来的函数相关联

#### Host Object
- 不是由JS标准定义，由宿主环境定义的对象，比如 window, setTimeout 之类
- 可以实现 JS 语言不支持，但 JS 语法支持的一些特性

- Atom
- Expression
- Statement
- Structure
- Program / Module

# 表达式
## Atom
- Grammar
  - Grammar Tree vs Priority：语法树和运算符优先级的关系
  - Left hand side & Right hand side：左值和右值运算的区别
- Runtime
  - Type Convetion：类型转换
  - Reference：引用类型
## Grammar
### Expressions 表达式
#### Member 成员运算 运算优先级0
  - a.b
  - a[b]
  - foo\`string`
  - super.b
  - super['b']
  - new.target
  - new Foo()
#### New New表达式 运算优先级1
  - new Foo
#### Call Call运算 运算优先级2
  - foo()
  - super()
  - foo()['b']
  - foo().b
  - foo()`abc`
#### Left Handside & Right Handside
  - 表达式不是Left就是Right，只有 Left Handside Expression 才能放在等号左边
#### Update 自增自减运算 运算优先级3
  - 在JS中所有 Update Expression 都是 Right Handside Expression
  - a ++
  - a --
  - -- a
  - ++ a
#### Unary 单目运算 运算优先级4
  - delete a.b
  - void foo()
  - typeof a
  - \+ a: 正号
  - \- a: 负号
  - ~ a
  - ! a
  - await a
#### Exponental 运算优先级5
  - \*\*：这是右结合运算符，优先处理右边
  - 例：3\*\*2\*\*3 === 3\*\*(2\*\*3)
  - 例：-1**2，浏览器会报错，要求用括号来消除歧义
#### Multiplication 乘除运算 运算优先级6
  - \* 
  - /
  - %
#### Additive 加减运算 运算优先级7
- \+
- \- 
#### Shift 移位运算 运算优先级8
- <<
- \>>
- \>>> 
#### Relationship 关系比较运算 运算优先级9
- <
- \>
- <=
- \>=
- instanceof
- in
#### Equality 相等运算 运算优先级10
  - ==
  - !=
  - ===
  - !==
#### Bitwise 按位运算 运算优先级11
  - &
  - ^
  - |
#### Logical 逻辑运算 运算优先级12
  - &&
  - ||
#### Conditional 三目运算 运算优先级13
  - ?:
  


## Runtime
### Reference 引用类型，需要内存地址
- Object
- Key
- delete
- assion

### Type Convetion 类型转换

||Number|String|Boolean|Undefined|Null|Object|Symbol|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Number|-||0 false|X|X|Boxing|X|
|String||-|"" false|X|X|Boxing|X|
|Boolean|true 1<br>false 0|'true'<br>'false'|-|X|X|Boxing|X|
|Undefined|NaN|'Undefined'|false|-|X|X|X|
|Null|0|'null'|false|X|-|X|X|
|Object|valueOf|valueOf toString|true|X|X|-|X|
|Symbol|X|X|X|X|X|Boxing|-|

#### unboxing 拆箱转换
将 Object 类型转成普通基本类型
- ToPremitive
  - JS 中的内部方法
  - 当 Object 参与运算时，都会调用此方法
- toString vs valueOf：按照需求分配优先级
- Symbol.toPrimitive：优先级最高，只要存在就调用他
```js
// 当对象需要发生类型转换时，会按照是否定义该方法来按照优先级调用
var o = {
  toString() {return "3"},
  valueOf() {return 2},
  // [Symbol.toPrimitive]() {return 1}
}
console.log(`${o}`) // "3"
console.log("x" + o) // "x2"
```

#### 装箱转换
当一些基础类型需要转换成对象时，就会调用对象的包装类型方法进行装箱转换
|类型|对象|值|
|:-:|:-|:-|
|Number|new Number(1)|1|
|String|new String("a")|"a"|
|Boolean|new Boolean(true)|true|
|Symbol|new Object(Symbol("a"))|Symbol("a")|



# 语句

## Grammar
- 简单语句
- 组合语句
- 声明

### 简单语句
- ExpressionStatement：简单语句
- EmptyStatement：空语句
- DebuggerStatement：debuggre语句
- ThrowStatement：异常语句
- ContinueStatement：循环语句
- BreakStatement：循环语句
- ReturnStatement：返回语句

### 复合语句
- BlockStatement
- ifStatement
- SwitchStatement
- IterationStatement
- WithStatement
- LabelledStatement
- TryStatement

### statement

#### Block
- \[\[type]]: normal
- \[\[value]]: --
- \[\[target]]: --

#### Iteration
- while() {}
- do{}while()
- for() {}
- for( in ) {}
- for( of ) {}

#### 标签、循环、break、continue
- \[\[type]]: break continue
- \[\[value]]: --
- \[\[target]]: label

包含
- LabelledStatement
- IterationStatement
- ContinueStatement
- BreakStatement
- SwitchStatement

#### try
- \[\[type]]: return
- \[\[value]]: --
- \[\[target]]: label

### 声明
- FuctionDeclaration
  - function a()
- GeneratorDeclaration
  - function* a()
- AsyncFunctionDeclaration
  - async function a()
- AsyncGeneratorDeclaration
  - async function* a()
- VariableStatement
  - var
- ClassDeclaration
  - class
- LexicalDeclaration
  - const
  - let

**预处理（pre-process）**
所有变量在函数中都会被预处理，即声明提升
```js
// demo1
var a = 2
void function () {
  a = 1
  return
  var a
}()
console.log(a) // 2
// demo2
var a = 2
void function () {
  a = 1
  return
  const a
}()
console.log(a) // error
```

**作用域**
```js
// demo1
var a = 2
void function () {
  a = 1
  { var a }
}()
console.log(a) // 2
// demo2
var a = 2
void function() {
  a = 1
  { const a = 3 }
}()
console.log(a) // 1
```

## Runtime
- Completion Record：
- Lexcal Environment

### Completion Record
- \[\[type]]：类型
  - normal
  - break
  - continue
  - return
  - throw
- \[\[value]]: 返回值
  - 基本类型
- \[\[target]]：
  - label

# 结构化
JS执行粒度（Runtime）
- 宏任务
- 微任务(Promise)
- 函数调用(Execution Context)
- 语句/声明(Completion Record)
- 表达式(Refrence)
- 直接量/变量/this......
  
## 宏任务与微任务
```js

```
## 事件循环
过程
1. get code：获取代码
2. execute：执行代码
3. wait：等待
   - 可以是等待一个时间或者一个事件，一般可以设计为一个锁，用不同条件去触发这个锁 
4. get code：获取代码
5. ......
   
## 函数调用
```js
// index.js
import {foo} from './foo.js'
var i = 1
console.log(i)// 当运行这一行时，执行上下文栈中有一个执行上下文
foo() // 当运行这一行时，会产生一个新的执行上下文，并且添加到栈顶
console.log(i)
i++
// foo.js
import {foo2} from './foo2.js'
var x = 2
export function foo() {
  console.log(x) // 当运行这一行时，执行上下文栈中有两个执行上下文
  foo2() // 当运行这一行时，会产生一个新的执行上下文，并且添加到栈顶
  console.log(x)
}
// foo2.js
var y = 3
export function foo2() {
  console.log(y) // 当运行这一行时，执行上下文栈中有三个执行上下文
}
```
- Execution Context：执行上下文
  - 上例中每个文件都能产生一个执行上下文，每个执行上下文能访问到的变量分别为 i、x、y
- Execution Context Stack: 执行上下文栈
  - 保存执行上下文的栈
- Running Execution Context: 运行时执行上下文
  - 执行上下文栈的栈顶元素，即是当前正在运行的执行上下文

### Execution Context
Execution Context 会由以下类别中其中的几个组成
- code evaluation state
  - 用于 async 和 generator 函数，用于评估代码执行到哪里
- Function
  - 使用 Function 初始化的执行上下文会有
- Script or Module
  - 在 script 或者 module 中的代码就会有这个部分
- Generator
  - 只有 Generator 函数创建的执行上下文才会有，是 Generator 函数执行后所生成的隐藏在背后的 Generator
- Realm
  - 保存所有使用的内置对象的领域
- LexicalEnvironment
  - 执行代码中所需要访问的环境，即保存变量的
  - this
  - new.target
  - super
  - 变量
- VariableEnvironment
  - 决定使用 var 声明变量会声明到哪里的一个环境，仅仅用于处理 var 声明

执行上下文分为两类

**ECMAScript Code Execution Context**
- code evaluation state
- Function
- Script or Module
- Realm
- LexicalEnvironment
- VariableEnvironment

**Generator Execution Context**
- code evaluation state
- Function
- Script or Module
- Realm
- LexicalEnvironment
- VariableEnvironment
- Generator

### Environment Record 作用域
- Declarative Environment Record
  - 一般当有 {} 的时候会生成，即块级作用域、函数作用域这些产生的时候
  - Function Environment Record
  - module Environment Record
- Global Environment Record
  - 全局作用域
- Object Environment Record
  - 专门给 with 使用的作用域

### Function-Closure
- 代码部分
  - 即函数中的代码
- 环境部分
  - 由一个 object 和一个变量的序列组成

在 JS 中每一个函数都会有一个在定义时所在的 Environment Records，并且会保存为自己的属性

### Realm（ES 2018 标准中的提案）
在 JS 引擎的实例中，所有的内置对象会被放进一个 Realm 里面去
在 JS 中，函数表达式和对象直接量均会创建对象，使用.做隐式转换也会创建对象。这些对象也是由原型的，如果没有 Realm，就不知道它们的原型是什么