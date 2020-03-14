# 2.1.1
> ∧および∨を条件式を用いて定義せよ．

論理積(/\\)、論理和(\\/) を以下のように定義する。

```hs
(/\), (\/)  :: Bool -> Bool -> Bool
x /\ y      = if x == False then False else y
x \/ y      = if x == True then True else y
```

# 2.1.2
> 論理において⇒で表される含意は，x⇒yが False になるのはxが真でかつyが偽の場合に限ると定義されている． 含意を Bool 上の演算として形式的な定義を与えよ．

含意(=>>)を以下のように定義する。

```hs
(=>>)           :: Bool -> Bool -> Bool
True =>> False  = False
_ =>> _         = True
```

# 2.1.3
> 型クラス Eq の宣言を書き換えて， (/=) のデフォルト定義を与えよ．

```hs
class Eq a where
  (==), (/=)  :: a -> a -> Bool
  x /= y      = not (x == y)
```

# 2.1.4
> analyse の定義を書き換えて，場合分けがその順序に依存しないようにせよ．

```hs
data Triangle = Failure | Isosceles | Equilateral | Scalene deriving Show

analyse :: (Int, Int, Int) -> Triangle
analyse (x, y, z)
  | x + y <= z                          = Failure       -- 失敗
  | x == z                              = Equilateral   --　正三角形
  | (x + y > z) && (x /= z) && (x == y) = Isosceles     -- 二等辺三角形
  | (x + y > z) && (x /= z) && (y == z) = Isosceles     -- 二等辺三角形
  | (x + y > z) && (x /= y) && (y /= z) = Scalene       -- 不等辺三角形
```

# 2.1.5
> 3つの整数を非減少順に並べる関数 sort3 を定義せよ． これにより関数 analyse' を定義して，引数が非減少順になっているという仮定に依存しないようにせよ．

```hs
sort3 :: (Int, Int, Int) -> (Int, Int, Int)
sort3 (x, y, z)
  | (x > y)   = sort3(y, x, z)
  | (y > z)   = sort3(x, z, y)
  | otherwise = (x, y, z)

analyse'        :: (Int, Int, Int) -> Triangle
analyse'        = analyse . sort3
```

# 2.1.6
> Triangle を型クラス Ord のインスタンスとして定義するにはいくつの等式が必要になるか（2.3節に同じことを実現する別の方法について説明がある）．

Triangleの値の種類は4種である。Ordのインスタンスとして定義するには(<=)の定義を網羅すれば良い。
結果がTrueとなるパターンは6通り、それ以外はFalseとなる。従って7つの等式が必要。
実際にコードとして動作させるにはスーパークラスの(Eq Triangle)も定義する必要がある。
※IFPHの解説においては、Ordのインスタンス定義には(<)を定義するだけで良いとしているが、["Haskell 2010 Language Report"](https://www.haskell.org/onlinereport/haskell2010/haskellch6.html#x13-1160006) によると、Ordのインスタンス定義にはcompare関数または、演算子(==), (<=)を定義する必要があるとしている。
実際に GHCi ver 7.10.3 において演算子(<)のみを定義したOrdクラスのインスタンス定義は受理されなかった。

> The default declarations allow a user to create an Ord instance either with a type-specific compare function or with type-specific == and <= functions.

>デフォルト宣言により、ユーザが、Ord インスタンスを作るには その型専用の compare 関数を定義するか、あるいは == および <= 関数を定義するかどちらかでよい。
(日本語訳は「[Haskell 98 言語とライブラリ改訂レポート](http://www.sampou.org/haskell/haskell2010-report-htja/haskellch6.html#x13-1160006)」より引用)

```hs
instance Eq Triangle where
  Failure == Failure          = True
  Isosceles == Isosceles      = True
  Equilateral == Equilateral  = True
  Scalene == Scalene          = True
  _ == _                      = False

instance Ord Triangle where
  Failure <= Isosceles     = True
  Failure <= Equilateral   = True
  Failure <= Scalene       = True
  Isosceles <= Equilateral = True
  Isosceles <= Scalene     = True
  Equilateral <= Scalene   = True
  _ <= _                   = False
```

# 2.1.7
> (==) での比較には意味があるが， (＜) での比較には意味がないような数は存在するか．

代数分野なら「複素数」
統計分野なら「カテゴリ変数」

# 2.1.8
> データ型上の (==) は，以下の3つの性質を満たすことが保証されなければならない． 
反射律： すべての x に対して x == x
推移律： x == y かつ y == z ならば x == z
対称律： x == y ならば y == x
きちんと定義された Bool 値の上に定義された (==) でこれらの性質が保持されていることを示せ．

Bool が Eq クラスのインスタンスである宣言は以下のとおり。

```hs
  instance Eq Bool where
    False == False  = True    -- (1)
    False == True   = False   -- (2)
    True == False   = False   -- (3)
    True == True    = True    -- (4)
```

以上より下記が成り立つ。
反射律：式(1)、(4)より成り立つ。
推移律：式(1)、(2)より常に下記条件が成り立つ。
        `x == y` かつ `y == z`　ならば `x == z`
対象律：式(1)、(2)より常に下記条件が成り立つ。
        `x == y` ならば `y == x`


# 2.1.9
> すべてのインスタンス宣言で (<) が満たすべき性質とは何か．

すべてのインスタンス宣言で(<)は下記の3つの性質を満たすべきである
反射律を満たさない（非反射律）
  `(x < x) == False`
推移律を満たす
  `x < y` かつ `x < z` ならば `x < z`
対象律を満たさない（非対称律）
  `x < y   /= y < x`

# 2.2.1
> アルファベット1文字を引数としてとり，アルファベット列でその文字の直後にある文字を返す関数 nextlet を定義せよ．ただし， 'A' は 'Z' の直後であると仮定せよ．

```hs
import Data.Char

nextlet   :: Char -> Char
nextlet 'Z' = 'A'
nextlet 'z' = 'a'
nextlet c   = chr (ord c + 1)
```

# 2.2.2
> 数字1文字を引数としてとり，それに対応する数値を返す関数 digitval を定義せよ．

```hs
import Data.Char

digitval    :: Char -> Int
digitval c  = if isDigit c then (ord c) - (ord '0') else undefined
```

# 2.3.1
> 与えられた曜日の前日の曜日を返す関数 dayBefore を定義せよ．

```hs
data Day  = Sun | Mon | Tue | Wed | Thu | Fri | Sat
            deriving (Eq, Ord, Enum, Show)

dayBefore   :: Day -> Day
dayBefore d = toEnum ((fromEnum d - 1) `mod` 7)
```

# 2.3.2
> 値が方位磁石の四方位を表すデータ型 Direction を定義し，与えられた方位の反対方向を示す方位を返す関数 reverse を定義せよ．

```hs
data Direction  = North | East | South | West
                  deriving (Eq, Ord, Enum, Show)
                  
dirReverse     :: Direction -> Direction
dirReverse d   = toEnum ((fromEnum d + 2) `mod` 4)
```

# 2.3.3
> 明示的にインスタンスを与えて， Bool を型クラス Enum のメンバーとして宣言せよ．

```hs
data MyBool = MyFalse | MyTrue deriving (Show)

instance Eq MyBool where
  MyFalse == MyFalse  = True
  MyTrue  == MyTrue   = True
  _ == _              = False

instance Enum MyBool where
  fromEnum MyFalse  = 0
  fromEnum MyTrue   = 1
  toEnum 0          = MyFalse
  toEnum 1          = MyTrue
```

型推論出来ない文脈で toEnum を使う場合は、型指定が必要

```
> fromEnum (toEnum 0 :: MyBool)
0
> fromEnum (toEnum 1 :: MyBool)
1
```

型推論出来る文脈では、型指定は不要

```
> MyFalse == (toEnum 0)
True
> MyFalse == (toEnum 1)
False
```

# 2.4.1
> cross (f, g) . cross (h, k) = cross (f . h, g . k) であることを証明せよ．

```
左辺：
  cross (f, g) . cross (h, k)
= {cross の定義}
  pair (f . fst, g . snd) . pair (h . fst, k . snd)
= {pair の性質 pair (f, g) . h = pair (f . h, g . h) より}
  pair (f . fst (h . fst, k . snd), g . snd (h . fst, k . snd))
= {fst および、snd を1つずつ簡約}
  pair (f . h . fst, g . k . snd)
右辺：
  cross (f . h, g . k)
= {cross の定義}
  pair (f . h . fst, g . k . snd)
```

# 2.4.2
> 3つ組に対応する型 Triple のデータ型宣言を与えよ．

```hs
data Triple a b c = MkTriple a b c
```

# 2.4.3
> 日付は整数の3つ組 (d,m,y) を使って表現されているものとせよ．ただし， d は日， m は月， y は年とする． 2つの日付（1つめは現在の日付，2つめは誰か P の生年月日）を引数としてとり， P の満年齢を返す関数を定義せよ．

```hs
data Date a b c = MkDate Int Int Int deriving Show
day   :: Date Int Int Int -> Int
day (MkDate d m y)  = d
month :: Date Int Int Int -> Int
month (MkDate d m y) = m
year  :: Date Int Int Int -> Int
year (MkDate d m y) = y

calcAge :: Date Int Int Int -> Date Int Int Int -> Int
calcAge toDay brtDay  = dy
  where dd = (day toDay) - (day brtDay)
        dm = (month toDay) - (month brtDay) + if dd < 0 then -1 else 0
        dy = (year toDay) - (year brtDay) + if dm < 0 then -1 else 0
```

# 2.4.4
> a および b がともに型クラス Enum のインスタンスであるとき， (a,b) を Enum のインスタンスとして宣言することは可能か．

Enum の構成子を(x, y)、それらを列挙する整数を i とした時に、下記の仕様を満たす
関数 toEnum, fromEnum の定義を与えられれば、宣言可能。

```
toEnum (fromEnum (x, y)) = i
  where toEnum :: Enum (x, y) -> Integer
        fromEnum :: (x, y) -> Enum (x, y)
```

けど、具体例は示せなかったので、以下のURLを引用しときます。
`http://ifph.sampou.org/Exer020404.html`

# 2.5.1
> 変換元の型が Either Bool Char で， Left ⊥ ， Right ⊥ ， ⊥ の3種類の引数に対して別々の振る舞いになるような関数を定義せよ．

```hs
isRight  :: Either Bool Char -> Bool
isRight (Left _)   = False
isRight (Right _)  = True
```

# 2.5.2
> case (f,g) . plus (h,k) = case (f.h,g.k) を証明せよ．

```
左辺：
  case (f, g) . plus (h, k)
= {plus の定義より}
  case (f, g) . case (Left . h, Right . k)
= {h . case (f, g) の性質より}
  case (case (f, g) . Left . h, case (f, g) . Right . k)
= {case (f, g) . Left と case (f, g) . Right の性質より}
  case (f . h, g . k)
```

# 2.6.1
> 差が10マイル未満の距離は互いに等しいとみなしたいと仮定する． Distance を型シノニムとして定義している場合， Distance 上の相等性検査演算を定義できるか．できるとすると，それを == とすることはできるか．

Distance上の相等性検査演算を実施する専用の関数は定義できる。

```hs
type Distance = Int
eqOfDistance  :: Distance -> Distance -> Bool
eqOfDistance x y  = (x `div` 10) == (y `div` 10)
```

しかし、型シノニムで定義された型は元となる型と同等であるため、Distance上の(==)は新しく定義できない。

# 2.6.2
> 以下の宣言を考える．
_
data Jane     = MkJane Int
newtype Dick  = MkDick Int
_
適当な関数を定義して， Jane と Dick が異なることを例示せよ． 

例えば以下の様な関数を定義したとする。

```hs
data Jane     = MkJane Int
newtype Dick  = MkDick Int

sureJane            :: Jane -> Bool
sureJane (MkJane _) = True
sureJane'           :: Jane -> Bool
sureJane' _         = True
sureDick            :: Dick -> Bool
sureDick (MkDick _) = True
sureInt             :: Int -> Bool
sureInt _           = True
```

この時data宣言のJane型は ⊥(undefined) をメンバとしないので、以下の様な違いが生じる。

```
> sureJane undefined
*** Exception: Prelude.undefined
> sureDick undefined
True
```

ただし、関数コール時にコンストラクタ付きで引数を渡す場合と、関数定義の左辺にコンストラクタが含まれる場合は、Jane型引数の関数でも ⊥(undefined) を受理する。

```
> sureJane (MkJane undefined)
True
> sureJane' undefined
True
```

# 2.7.1
> 以下の文字列を昇順に出力せよ． "McMillan" ， "Macmillan" ， "MacMillan" ．

```hs
sort3 :: Ord a => (a, a, a) -> (a, a, a)
sort3 (x, y, z)
  | (x > y) = sort3(y, x, z)
  | (y > z) = sort3(x, z, y)
  | otherwise = (x, y, z)

putStr3 :: (String, String, String) -> IO()
putStr3 (x, y, z) = putStr (x ++ "\n" ++ y ++ "\n" ++ z ++ "\n")

-- (putStr3 . sort3) ("McMillan", "Macmillan", "MacMillan")
-- MacMillan
-- Macmillan
-- McMillan

```

# 2.7.2
> 以下の式の値はどうなるか．
_
show (show 42)
show 42 ++ show 42
show "\n"

```
  show (show 42)
= {show Integer を簡約}
  show "42"
= {show String を簡約}
  "\"42\""
  
  show 42 ++ show 42
= {show Integer を簡約}
  "42" ++ "42"
= {リストの結合}
  "4242"
  
  show "\n"
= {show String を簡約}
  "\"\\n\""
```

# 2.7.3
日付は整数の3つ組 (d,m,y) を使って表現されているものとせよ．ただし， d は日にち， m は月， y は年とする． 日付を引数としてとり，対応する日付を印字する関数 showDate を定義せよ． 使用例は以下のとおり． 
_
showDate (10,12,1997) は "10 December, 1997" と印字
_
さらに複雑な版を考え，以下のようになるよう改訂せよ．
_
showDate (10,12,1997) は "10th December, 1997" と印字
showDate (31,12,1997) は "31st December, 1997" と印字

```hs
data MonthName  = January | February | March | Aprill | May | June
                  | July | August | September | October | November | December
                  deriving (Eq, Ord, Enum, Show)
type Day    = Int
type Month  = Int
type Year   = Int
type Date   = (Day, Month, Year)

showDay :: Day -> String
showDay d
  | mod10 == 1 && d /= 11 = show d ++ "st "
  | mod10 == 2 && d /= 12 = show d ++ "nd "
  | mod10 == 3 && d /= 13 = show d ++ "rd "
  | otherwise             = show d ++ "th "
    where mod10 = d `mod` 10

showMonth :: Month -> String
showMonth m = show (toEnum (m - 1) :: Month)

showDate :: Date -> String
showDate (d, m, y) = show d ++ " " ++ showMonth m ++ ", " ++ show y

showDate2 :: Date -> String
showDate2 (d, m, y) = showDay d ++ " " ++ showMonth m ++ ", " ++ show y
```
