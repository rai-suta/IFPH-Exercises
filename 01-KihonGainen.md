# 概要
「関数プログラミング入門 ―Haskellで学ぶ原理と技法―」[^1]の練習問題を解いて読書メモとする記事です。解答に間違いなどありましたら、ご指摘歓迎です。
翻訳者 山下伸夫様のサポートサイト[^2]もあるので、正確な解答はそちらを御覧ください。

[^1]: Richard Bird, 山下 伸夫 (2012) 関数プログラミング入門 Haskellで学ぶ原理と技法 (`https://books.google.co.jp/books?id=Kdq3MgEACAAJ`)
[^2]: IFPH — 『関数プログラミング入門』 (`http://ifph.sampou.org/index.html`)

# 1.1.1
> 関数 square を用いて引数を4乗する関数 squad を設計せよ．

```hs
square    :: Integer -> Integer
square x  = x * x

quad      :: Integer -> Integer
quad x    = square (square x)
```

# 1.1.2
> 2つの引数のうち大きいほうを返す関数 greater を定義せよ．

```hs
greater         :: (Integer, Integer) -> Integer
greater (x, y)  = if x > y then x else y
```

#1.1.3
> 指定した半径 r を持つ円の面積を計算する関数を定義せよ（πの近似値として 22/7 を使え）．

```hs
square_f    :: Float -> Float
square_f x  = x * x

areaOfCircle    :: Float -> Float
areaOfCircle r  = square_f(r) * (22/7)
```

# 1.2.1
> x×y を評価するためには，式 x と式 y を正規形に簡約してから乗算する． square infinity の評価は停止するか．

square の定義より、簡約プロセスで必ず infinity を評価するため、評価は停止しない。

# 1.2.2
> 式 square (3+4) に対して停止する簡約系列はいくつあるか．

カッコ内の式から簡約する場合（1系列）

```
square (7) = 7 * 7 = 49
```

square の定義から先に簡約する場合（2系列）

```
(3 + 4) * (3 + 4) = 7 * (3 + 4) = 7 * 7 = 49
(3 + 4) * (3 + 4) = (3 + 4) * 7 = 7 * 7 = 49
```

以上より簡約系列は3つある。

# 1.2.3
> 以下の構文規則で定義される整数表現の式言語を想像せよ． （i）zeroは式である．（ii）eが式なら，succ(e)およびpred(e)も式である． この言語で書かれた式は，評価器が以下の規則をこれ以上適用できなくなるまで繰り返し適用しながら簡約する．
succ (pred (e)) = e
pred (succ (e)) = e
式succ (pred (succ (pred (pred (zero)))))を単純化せよ．
この式には何通りの方法で簡約規則を適用できるか． それらはどれも同じ結果になるか． どのような式を与えても簡約プロセスが必ず停止することを証明せよ．

簡約規則 `succ (pred (e)) = e` を先に適用する場合（2系統）

```
succ (pred (succ (pred (pred (zero)))))
^^^^^^^^^^
= succ (pred (pred (zero)))
  ^^^^^^^^^^
= pred (zero)

または

succ (pred (succ (pred (pred (zero)))))
            ^^^^^^^^^^
= succ (pred (pred (zero))) = 以下略
```

簡約規則 `pred (succ (e)) = e` を先に適用する場合（1系統）

```
succ (pred (succ (pred (pred (zero)))))
      ^^^^^^^^^^
= succ (pred (pred (zero))) = 以下略
```

以上より簡約系列は3つある。

# 1.2.4
> 直前の問題の続き．この言語にもう1つ構文規則を追加する． （iii）e1およびe2が式なら，add (e1,e2)も式である． これに対応する簡約規則は以下のとおり．
_
add(zero,e2)     = e2 
add(succ(e1),e2) = succ(add(e1,e2))
add(pred(e1),e2) = pred(add(e1,e2))
_
式 add(succ(pred(zero)),zero) を単純化せよ．
この式に適用できる簡約の方法が何通りあるか数えよ． どの方法でも同じ結果になるか．

`succ (pred (e))`から簡約する場合（1系統）

```
add (succ (pred (zero)), zero)
= {succ (pred (e)) を簡約}
  add (zero, zero)
= {add (zero, e2) を簡約}
  zero
```

`add (succ (e1), e2)`から簡約する場合（2系統）

```
add (succ (pred (zero)), zero)
= {add (succ (e1), e2) を簡約}
  succ (add (pred (zero)), zero)
= {add (pred (e1), e2) を簡約}
  succ (pred (add (zero, zero)))   ・・・(1)
= {add (zero, e2) を簡約}
  succ (pred (zero))
= zero

または(1)より
= {succ (pred (e)) を簡約}
  add (zero, zero)
= zero
```

以上より簡約系列は3つある。

# 1.2.5
> 以下の規則で式の大きさを定義するものとする．
_
size(zero)       = 1
size(succ(e))    = 1 + size(e)
size(pred(e))    = 1 + size(e)
size(add(e1,e2)) = 1 + 2 × (size(e1) + size(e2))
_
先に示した5つの簡約規則のどれを適用しても式の大きさが小さくなることを示せ． これで，どのような式から出発しても簡約プロセスが必ず停止することを証明したことになる．なぜか．

5つの簡約規則に対して、式の大きさを比較する

```
左辺：
  size (succ (pred (e)))
= {size (succ (e)) を簡約}
  1 + size (pred (e))
= {size (pred (e)) を簡約}
  1 + 1 + size (e)
= 2 + size (e)
右辺：
> size (e)
```

```
左辺：
  size (pred (succ (e)))
= {size (pred (e)) と size (succ (e)) を簡約}
  1 + 1 + size (e)
= 2 + size (e)
右辺：
> size (e)
```

```
左辺：
  size (add (zero, e2))
= {size (add (e1, e2)) を簡約}
  1 + 2 * (size (zero) + size (e2))
= {size (zero) を簡約}
  1 + 2 + 2 * size (e2)
= 3 + 2 * size (e2)
右辺：
> size (e2)
```

```
左辺：
  size (add (succ (e1), e2))
= {size (add (e1, e2)) を簡約}
  1 + 2 * (size (succ (e1)) + size (e2))
= {size (succ(e)) を簡約}
  1 + 2 * (1 + size (e1) + size (e2))
= 3 + 2 * (size (e1) + size (e2))
右辺：
  size (succ (add (e1, e2)))
= {size (size (succ (e))) を簡約}
  1 + size (add (e1, e2))
= {size (add (e1, e2)) を簡約}
  1 + 1 + 2 * (size (e1) + size (e2))
= 2 + 2 * (size (e1) + size (e2))
< 左辺
```

よってどの簡約規則でも簡約後に式の大きさは小さくなる。
どのような式から簡約しても簡約プロセスは必ず停止する。

# 1.3.1
> multiply を以下のように定義するものとする．
_
multiply        ::  (Integer,Integer) -> Integer
multiply (x,y)  =   if x == 0 then 0 else x * y
_
記号は2つの整数が等しいかどうかをテストするために使う． e1 == e2 を評価するには，まず e1 と e2 を正規形にまで簡約してから，それぞれの結果が同一であるかどうかをテストすると仮定せよ． 
遅延評価の下で， multiply (0,infinity) の値はどのような値になるか， 

```
multiply (0, infinity)
= {multiply の定義より}
  if 0 == 0 then 0 else 0 * infinity
= {0 == 0 の簡約により、then節を返す}
  0
```

> multiply (infinity,0) の値はどうか．

```
multiply (infinity, 0)
= {multiply の定義より}
  if infinity == 0 then 0 else infinity * 0
= {infinity == 0 の簡約は終了しない}
```

# 1.3.2
> 関数 h を等式 h x = f (g x) で定義したものとせよ． f と g がともに正格であれば， h も正格であることを示せ． 

```
  h ⊥
= {h の定義より}
  f (g ⊥)
= {g は正格であるので}
  f ⊥
= {f は正格であるので}
  ⊥
```

よってhは正格である。

# 1.4.1
> f および g が以下の型を持つとする． 
_
f :: Integer -> Integer
g :: Integer -> (Integer -> Integer)
_
h を以下のように定義してみよう． 
_
h :: WhatType
h x y = f (g x y)
_
h に正しい型を割り当てよ． 

まず`(h x y)`の型を考えると、定義より`(f x)`の型と同じなので
`h x y :: Integer`
x と y の型については、`(g x y)`が評価可能であるとき、g の定義より

```
x :: Integer
y :: Integer
```

であるといえる
以上より h の型定義は下記のとおりとなる。
`h :: Integer -> Integer -> Integer`

> 次に，以下の表明のうち正しいものはどれか（あるだけすべて）答えよ． 
_
(1) h     = f . g
(2) h x   = f . (g x)
(3) h x y = (f . g) x y

`(.)`の型定義を下記に示す
`(.)  :: (b -> c) -> (a -> b) -> (a -> c)`
`(.)`の型定義より、`(x . y)`を評価するためには、xとyの型は下記の通り定義されている必要がある。

```
x :: (b -> c)
y :: (a -> b)
```

表明(1)について、`(f . g)`を評価しようとすると、fとgの型が、`(.)`の定義を満たさないため正しくない表明である。

```
f :: Integer -> Integer
g :: Integer -> (Integer -> Integer)
```

表明(2)について、`f . (g x)`は評価可能である

```
f   :: Integer -> Integer
g x :: Integer -> Integer
```

また、`(h x)`の型と`(f . (g x))`の型は下記の通り同じであるので、正しい表明である。

```
h x       :: Integer -> Integer
f . (g x) :: Integer -> Integer
```

表明(3)について、表明(1)で言及したように`(f . g)`を評価出来ないため、正しくない表明である。

以上より、正しい表明は(2)のみとなる。

# 1.4.2
> 先に定義した関数 delta の引数をカリー化して， delta (a,b,c) と書かずに， delta a b c と書けるようにしたとする． このカリー化された版の型はどうなっているか． 

`delta`の型は下記の通り
`delta  :: (Float, Float, Float) -> Float`
よってカリー化した`deltac`を定義する場合、型は下記の通り定義される
`deltac :: Float -> Float -> Float -> Float`

# 1.4.3
> log2，loge，log10のように，数学ではさまざまな底の対数を使う． 底を引数としてとり，その底で対数を計算する関数を返す関数の正しい型を答えよ．

関数`log_x`として下記の通り型を定義する
`log_x :: Float -> (Float -> Float)`

# 1.4.4
> 数学の解析学で使う（fのaからbまでの）定積分関数に対して適切な型を与えよ．

関数`integ`として下記の通り型を定義する
`integ :: (Float -> Float) -> (Float, Float) -> Float`

# 1.4.5
> 以下の型を持つ関数の例を与えよ．
_
(Integer -> Integer) -> Integer
(Integer -> Integer) -> (Integer -> Integer)

```hs
f1 :: (Integer -> Integer) -> Integer
f1 f = f 1

f2 :: (Integer -> Integer) -> (Integer -> Integer)
f2 f x = (f x) + x
```

# 1.4.6
> 以下の表明のうち真であるものはどれか，あるだけ答えよ．
_
(*) x = (*x)
(+) x = (x+)
(-) x = (-x)

`(*) x = (*x)`について、両辺の型は一致するが、左辺は第一引数、右辺は第二引数にxを適用しているため、演算手続きの観点から見た場合は偽である。ただし、演算子(*)が交換律を持つ場合、外延性の原理の観点から見た場合は真である。

`(+) x = (x+)`について、両辺の式は同じ手続きであるため、表明は真である。

`(-) x = (-x)`について、左辺の(-)は二項演算子だが、右辺の(-)は単項演算子であるため、型が異なる。よって表明は偽である。

# 1.4.7
> カリー化された関数をカリー化されてない版に変換する関数 uncurry を定義せよ．

```
uncurry          :: (a -> b -> c) -> (a, b) -> c
uncurry f (x, y) = f x y
```

> すべての x および y に対して以下が成り立つことを示せ． 
_
curry (uncurry f) x y = f x y
uncurry (curry f) (x,y) = f (x,y)

`curry (uncurry f) x y    = f x y`の簡約系列を下記に示す

```
curry (uncurry f) x y
= {curry の定義より}
  (uncurry f) (x, y)
= {uncurry の定義より}
  f x y
```

`uncurry (curry f) (x, y) = f (x, y)`の簡約系列を下記に示す

```
uncurry (curry f) (x, y)
= {uncurry の定義より}
  (curry f) x y
= {curry の定義より}
  f (x, y)
```

# 1.5.1
> フィボナッチ数f_0,f_1,… は，n ≧ 0であるようなすべてのnに対して，f_0 = 0, f_1 = 1, f_n+2 = f_n+1 + f_nという規則で定義する． 整数nをとり，f_nを返す関数 fib の定義を与えよ．

```hs
fib   :: Integer -> Integer
fib n
  | n > 1   = fib (n-1) + fib (n-2)
  | n == 1  = 1
  | n == 0  = 0
```
ただし上記コードは n>1 の場合計算量は O(2^(n-1)) となるので、計算効率は悪い。

# 1.5.2
> 整数の絶対値を返す関数 abs::Integer -> Integer を定義せよ．

```hs:
abs   :: Integer -> Integer
abs x = if x > 0 then x else -x
```

# 1.6.1
> 以下の関数に適切な多相型を与えよ．
_
const x y = x
subst f g x = f x (g x)
apply f x = f x
flip f x y = f y x

```Haskell:
import Prelude hiding (const, flip)
const :: a -> b -> a
const    x    y =  x
-- const 1 2
-- 1

subst :: (a -> b -> c) -> (a -> b) -> a -> c
subst    f                 g          x =  f x (g x)
-- subst (+) (1+) 2
-- 5

apply :: (a -> b) -> a -> b
apply    f           x =  f x
-- apply (2+) 3
-- 5

flip :: (b -> a -> c) -> a -> b -> c
flip    f                x    y =  f y x
-- filp (/) 1 2
-- 2.0
```
Preludeにて const, flip が定義済みのため、hide指定でimportする。


# 1.6.2
> f :: (a,b) -> c であるようなすべての f に対して，以下を満たす関数 swap を定義せよ． 
_
flip (curry f) = curry (f . swap)

```Haskell:
swap            :: (a, b) -> (b, a)
swap (x, y)     = (y, x)
```


外延性の原理より、下記の等式が成り立つ。
  `flip (curry f) x y = curry (f . swap) x y`
この式を簡約すると下記の通りとなる。

```
flip (curry f) x y
= {flip の定義より}
  (curry f) y x
= {curry の定義より}
  f (y, x)

curry (f . swap) x y
= {curry の定義より}
  (f . swap) (x, y)
= {. の定義より}
  f (swap (x, y))
= {swap の定義より}
  f (y, x)
```

# 1.6.3
> 以下の関数 strange および stranger に対して多相型を割り当てられるか． 
_
strange  f g = g (f g)
stranger f   = f f

`strange f g = g (f g)` の場合。
f g より下記の通り型を仮定する
  `g :: a`
  `f :: a -> b`
g (f g) より下記の通り型を仮定する
  `g :: b -> c`
  `f :: (b -> c) -> b`
上記2通りの型定義は矛盾しない、よって strange に対して下記のように多層型を割り当てられる。
  `strange :: ( (a -> b) -> a ) -> (a -> b) -> b`

`stranger f = f f` の場合。
`f` の型を下記の通り型を仮定する
  `f :: a`
`f f` より `f` はa型を受け取る関数であるので下記のように型定義される
  `f :: a -> b`
上記2通りの`f`の型定義は矛盾するため、strangerに対して多層型を割り当てられない。

# 1.6.4
> 以下の関数に対して多相型を割り当てよ．
_
square x = x * x

演算子(\*) は型クラスNumのメソッドである。

```hs
(*)     :: Num a => a -> a -> a`
```

よってsquareの型は下記の通り定義される。

```hs
square  :: Num a => a -> a`
```

# 1.7.1
> 仕様に合う別の increase の実装を与えよ． 

```Haskell:
increase    :: Integer -> Integer
increase x  = square (x + 1) - 2 * x
```

証明

```
  increase x
= {increase の定義}
  square (x + 1) - 2 * x
= {square の定義}
  x^2 + 2 * x + 1 - 2 * x
= x^2 + 1
> x^2
= {square の定義}
  square x
```

