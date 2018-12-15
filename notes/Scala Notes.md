

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

In the line# 2, the return type (derived from the left side of the definition of the `f` is `(Seq[Int] => Int) => Int`, therefore, in line# 4, you have to pass the `Seq[Int] => Int` compatible function which is work on the **repeated parameter** passed in the line# 2. Above `s` has used **currying**.  You can create a **control abstraction** as follows:

```scala
f(arr: _*) {  
  (x: Seq[Int]) => x.reduceLeft(_ + _)  
}
```
You can change the right side of the equation inside the `{...}` in the above.

You can create new **control structure** as well because you can pass the function to higher-order function.

```scala
def twice(f: Int => Int):Int => Int = x => f(f(x))
// for example  
twice((x:Int) => x *2)(3)
```

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTEzMjMzMzA1LDEyMjY0MzE1NSwxMjI2ND
MxNTUsLTE5ODU2MTU3ODIsLTE4MDA0NjU0NjgsLTEyODI5NjQw
MDIsMTg5MTY3MTE1MSwtMjA5NzIxODA0NCwzMDY3MjI4MjEsLT
E3MDc0MDExMjcsMTQwOTgzNzI5MSwxOTY2ODcyNDYzLC03ODM2
OTg1ODMsMjgyMzA2NzMzLDEzMTIyODg1NzcsMTk5NzA5NDY0My
wtMzcxNzUwMDA0LC0xOTg4NzYwMjI0LDgyOTYwMTU4MSwtMTEy
OTU5ODQ2NV19
-->