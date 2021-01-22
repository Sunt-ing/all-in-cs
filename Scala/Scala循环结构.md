###  **for** 循环

**to 语法**：for( a <- 1 **to** 10)

**until 语法**：for( a <- 1 **until** 10) (不包含 j) 

**多重循环**：for( **a** <- 1 to 3**;**  **b** <- 1 to 3)

**for 循环集合**：for( x <- List )

**循环多重过滤**：for( var x <- List  **if** condition1; **if** condition2...)

**yield**：*retVal* 是list：

```scala
var retVal = for{ var x <- List
     if condition1; if condition2...
}yield x
```

















