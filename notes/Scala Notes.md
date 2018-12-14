

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
In the second line, you don't need to give parameters.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5ODg3NjAyMjQsODI5NjAxNTgxLC0xMT
I5NTk4NDY1XX0=
-->