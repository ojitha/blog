

## Local Functions
Local functions are the functions defined inside other functions. Therefore , local functions can access the parameters of their enclosing function.
## First-class Functions
Scala supports first-class functions which can be user defined as well as anonymous literal value, for example
```scala
(x:Int) => x + 1
```
parameters and the function body separated by the "=>". In case if the type can be inference, then no need to define type, this is called **target typing**.
In the **partially applied function**,  no need to provide all the necessary parameters, but partially. For example,
```scala
def sum(a:Int, b:Int):Int = a + b                //> sum: (a: Int, b: Int)Int
  val a = sum _                                   //> a  : (Int, Int) => Int = ex3$$$Lambda$9/1209271652@58ceff1
  a.apply(1,2)                                    //> res1: Int = 3
```
In the second line, you don't need to give parameters. The underscore can be given to replace one or more parameters:
```scala
val f: (Int, Int) => Int =  (_ : Int) + (_ : Int)
k(1,2)
```

## Closures
In the following code segment, function `f3` has two **bound variables** and one **free variable** which is `a`.
```scala
val a = 10  //current value
val f3 = (x:Int, y:Int) => (x+y) + a  
f3(1,2)
```
A function literal with no free variables, is called a **closed term** otherwise called **open term**. Th function value created at runtime from a function literal is called a **closure**.

## Higher-order functions
Functions that takes function as parameters are called higher-order functions. The great benefit of higer-order functions is they enable you to create **control abstractions**. Other benefit that you can put the higher-order functions in an API itself.

```scala
val arr = Array(1,2,3,4)  
def f(x:Int*) = (y:(Int*) => Int) => y(x: _*)  
val s = f(arr: _*)  
s( (x:Seq[Int]) => x.reduceLeft(_ + _) )
```

In the line# 2, the return type (derived from the left side of the definition of the `f` is `(Seq[Int] => Int) => Int`, therefore, in line# 4, you have to pass the compatible function which is with the **repeated parameter**.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA1NDI2MTE4MiwtMTcwNzQwMTEyNywxND
A5ODM3MjkxLDE5NjY4NzI0NjMsLTc4MzY5ODU4MywyODIzMDY3
MzMsMTMxMjI4ODU3NywxOTk3MDk0NjQzLC0zNzE3NTAwMDQsLT
E5ODg3NjAyMjQsODI5NjAxNTgxLC0xMTI5NTk4NDY1XX0=
-->