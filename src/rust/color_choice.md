# 用 Rust 刷 Codewars 之 Color choice

### [问题描述](https://www.codewars.com/kata/55be10de92aad5ef28000023)

> 以下为本人简单翻译的描述

组合数: 举个例子, 从 52 个卡牌中抽取 5 个卡牌, 会有 2,598,960 种情况, 这个数就是组合数.

在数学中, 从 n 个元素中取出的 x 个组合被称为 n 和 x 的二项式系数, 或者称为组合数 `C(n, x)`. n 个数计算第 x 个数 大小的公式:
`m = n! / ( x! * (n - x)!)`.
其中 `!` 是阶乘运算符.

现在假设你是一个着名的海报设计师和画家. 你被要求提供 6 张具有相同设计的海报, 每张海报用 2 种颜色. 海报必须有不同的颜色组合, 你有 4 种可供选择的颜色: 红色, 蓝色, 黄色, 绿色. 那么你可以为每张海报选择多少种颜色?

答案是 2, 因为组合数 `C(4, 2) = 6`. 组合将是: {红, 蓝}, {红, 黄}, {红, 绿}, {蓝, 黄}, {蓝, 绿}, {黄, 绿 }.

现在同样的问题, 但是你要提供 35 张海报, 有 7 种颜色可用. 每张海报有多少种颜色? 如果你用上述公式计算 `C(7, 2)`, 会得到 21.  但是 35 张海报如果只有 21 种组合是不够用的. 如果你采取 `C(7, 5)`, 你同样会得到21. 幸运的是, 如果你采用 `C(7, 3)` 或者 `C(7, 4)`, 正好是 35, 所以每个海报将有 3 种或 5 种不同颜色的组合. 这种情况你会采取 3 种颜色, 因为它更便宜.

问题如下:

知道 m (海报的数量), 知道 n (可用颜色的总数), 找出一个 x (每个海报的颜色数量, 使得每个海报具有唯一的颜色组合, 并且组合的数量与海报的数量是完全相同的).

换句话说, 对于给定的 m, n, 我们必须找到满足公式 `C(n, x) = m ★` 的 x`(m >= 0且 n > 0)`. 如果 x 有多个解, 则给出最小的 x. 当给出的 m 没有 x 满足`等式 ★` 时, 返回 -1.

例子:

```Rust
checkchoose(6, 4) --> 2
checkchoose(4, 4) --> 1
checkchoose(4, 2) --> -1
checkchoose(35, 7) --> 3
checkchoose(36, 7) --> -1

a = 47129212243960
checkchoose(a, 50) --> 20
checkchoose(a + 1, 50) --> -1
```

`clojure: Use big integers in the calculation of C(n, k)!`

### 题解 1

看到这题的时候感觉很简单, 一个阶乘公式, 一个组合数公式, 然后在 main 函数中组合起来就行了.

代码如下:

```Rust
fn check_choose(m: u64, n: u64) -> i64 {
    let mut result: i64 = -1;

    for x in 1..n {
        if get_choices(n, x) == m {
            result = x as i64;
            break;
        }
    }
    result
}

fn get_choices (n: u64, x: u64) -> u64 {
    factorial(n) / factorial(x) / factorial(n - x)
}

fn factorial(number: u64) -> u64 {
    fn fac_iter(nth: u64, product: u64) -> u64 {
        if nth <= 1 {
            product
        } else {
            fac_iter(nth - 1, nth * product)
        }
    }
    fac_iter(number, 1)
}
```

很简单嘛, 连阶乘我都优化了下递归.
复制粘贴, 跑下例子, 没问题. 提交!
然后就报 Overflow 了. ಥ_ಥ

### 题解 2

自己测试一下, 发现数字小的时候没问题, 大了之后乘法就会溢出.
这个简单, 改成用大数就行了, 然后发现 `u64` 在标准库里是最大的, 但是 crates 上有 bigint, 问题的提示也这么说. 我就改成了下边的代码(只列出了 factorial).

```Rust
fn factorial(number: u64) -> BigUint {
    let number = number.to_biguint().unwrap();
    fn fac_iter(nth: BigUint, product: BigUint) -> BigUint {
        if nth <= One::one() {
            product
        } else {
            fac_iter(nth.clone().sub(1.to_biguint().unwrap()), nth * product)
        }
    }
    fac_iter(number, One::one())
}
```

提交测试, 发现用不了第三方库, 想想也知道. ಥ_ಥ

### 题解 3

既然用不了第三方库, 那容我来优化一下 `factorial` 函数.
本来想实现 `factorial` 返回一个惰性值, 然后在 `get_choices` 函数里怎么计算不会溢出. 然后发现自己想错了, 直接优化 `get_choices`, 不要用 `factorial` 函数就行了. 几个加减乘除, `u64` 不会溢出.

代码如下 (未列出 `check_choose`):

```Rust
fn get_choices (n: u64, x: u64) -> u64 {
    if x == 1 {
        n
    } else {
        get_choices(n, x - 1) * (n - x + 1) / x
    }
}
```

递归实现了, 复制粘贴, 测试通过, 提交!

### 题解 4

上边用递归实现了, 但是本着完美主义的心, 必须优化一下啊.
下边是优化后的代码, `check_choose` 也稍优化.

最终代码:

```Rust
fn check_choose(m: u64, n: u64) -> i64 {
    let mut result: i64 = -1;
    let h = (n + 2) / 2;

    for x in 1..h {
        if get_choices(n, x) == m {
            result = x as i64;
            break;
        }
    }
    result
}

fn get_choices(n: u64, x: u64) -> u64 {
    fn get_choices_iter(n:u64, x: u64, c: u64, r: u64) -> u64 {
        let r = r * (n - c + 1) / c;
        if c < x {
            get_choices_iter(n, x, c + 1, r)
        } else {
            r
        }
    }
    get_choices_iter(n, x, 1, 1)
}
```

### 总结

上边 `get_choices` 依然不完美, `get_choices_iter` 参数使用了外部变量 n, x. 总感觉可以去掉, 写成 closure.
我使用匿名函数时发现没法递归了, 暂时不知道该怎么办了.

代码其实就是一点一点优化过来的, 有些书上告诉我们要想好再写代码, 有些书上又警告我们不要过度设计, 但是这个度在哪里
只能靠多谢代码来锻炼了, 增加一些经验, 更容易发现问题的关键所在.
