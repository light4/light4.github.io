# 一个有趣的 fibnoacci 函数

> 用 python 求 fibnoacci, 要求不用 if/else/for/while 等关键字

```python
def fib(n):
    return ((n == 1 or n == 2) and 1 or (fib(n - 1) + fib(n - 2)))
```

### 解释

python 可以用 *and* *or* 两个关键词实现三元运算符    
    
`True and a or b` 返回 a    
`False and a or b` 返回 b    

