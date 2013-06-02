bookstudy.programming_in_haskell
================================

# 하스켈로 배우는 프로그래밍.

## 배우게된 동기.
 * [LISP 사용자모임]에서 [Haskell 스터디 모임 준비](http://cafe.naver.com/lisper/1852)라는 글이 올라와서, 함 해보기로함.

## 셋팅
 * https://github.com/netpyoung/netpyoung.Notes/blob/master/enviroment-setting/setting-haskell/emacs-haskell-win.md

## 01. 소개
 - 소제목 그대로, 이 책에 대한 전반적인 소개 및 하스켈 역사에 대해 간략하게 나옴.

## 02. 첫걸음 떼기
 - [Hugs][Hugs - haskellwiki] = Haskell User´s Gofer System, one of the Haskell implementations.
 - 생짜 [GHC] 설치법을 보여주지만, [haskell platform]을 설치하는게 좋을듯.
 - Emacs셋팅까지 해주면 금상첨화.

````haskell
{-
멀티라인 코멘트.
-}
-- 단일라인 코멘트.
````

## 03. 타입과 클래스
 - 소재목 그대로, 타입과 기본 클래스를 설명함. 어느정도 아는거라 딱히 별 흥미를 끌진 못함.

## 04. 함수 정의
 - 패턴매칭이 쩌는구나!!

## 05. 리스트 조건 제시식
 - 조건제시식.. 이거 눈으로 볼때는 편한데.. 중간에 `|`, `<-`, `,` 이것들이 섞여서 코딩할때는 짜증나네..
 - 젠장. 생각해보니 타입언어라 mixed list (ex. [1, 2, "123"]) 이런게 없잖아.
 - 이거 볼만한듯 [Robozzle Kata in Haskell]

```haskell
import Test.HUnit

main = do
  runTestTT tests

tests = TestList [
  test_generator
                 ]

test_generator =
  [x ^ 2 | x <- [1..5]] ~?= [1,4,9,16,25]
  
-- Cases: 1  Tried: 0  Errors: 0  Failures: 0
```
## 06. 되도는 함수
recursion

 - 아는내용.


```haskell
qsort :: Ord a => [a] -> [a]
qsort [] = []
qsort (x:xs) = qsort smaller ++ [x] ++ qsort bigger
               where
                 smaller = [a | a <- xs, a <= x]
                 bigger = [b | b <- xs, b > x]

```

## 07. 함수를 주고받는 함수
* higher-order
* curring

http://en.wikipedia.org/wiki/Fold_(higher-order_function)

### foldr - right
```haskell
foldr :: (a -> b -> b) -> b -> [a] -> b
foldr f v [] = v
foldr f v (x:xs) = f x (foldr f v xs)
```

foldr (+) 0 = (1 + (2 + (3 + 0)))

### foldl - left
```haskell
foldl :: (a -> b -> a) -> a -> [b] -> a
foldl f v [] = v
foldl f v (x:xs) = foldl f (f v x) xs
```

foldl (+) 0 =(((0 + 1) + 2) + 3)


- 아 역시 이 부분이 어렵네..
- map까지는 구상이되는데 foldr, foldl, unfold들어가니 점점 산으로..

## 08. 함수형 문법 분석기
- 이번장부터 급 난이도 상승??


```haskell
return :: a -> Parser a
return v = \inp -> [(v, inp)]

return v inp = [(v, inp)]
-- inp를 람다식을 써서 몸체 안으로 돌리는 것이, 그 타입 a -> Parser a에서 볼 수 있듯, 하나의 인자를 받아 문법 분석기를 돌려주는 함수임을 드러나 보이게 한다는 점에서는 더 좋다.
```

* `모나드`는 무엇인가?
* `do`는 무엇인가?
* `module Parsing where`은?
 - 모듈 이름을 `Parsing`이라 정한 것임.
* `import`도 햇갈뎌
* `infixr 5 +++`는 무얼 의미하는가?
 - `+++`연산자가 오른쪽에서부터 묶이며, 우선순위단계가 5라는 것을 지정함.
* `newtype`키워드는?
 - 항수가 1인 생성자 하나만을 갖는 타입을 좀 더 효율적으로 처리하기 위한 하스켈 기능.
* `instance Monad Parser where`와 `instance MonadPlus Parser where`의 차이점?

## 09. 대화식 프로그램
- 딱히 별 어려움 없음.
- 설정만 좀 까다로울뿐?

## 10. 타입과 클래스 선언

* type선언: 기존에 존재하는 타입에 새로운 이름을 붙여주는 것. (되돌아 정의할 수 없음)
* data선언: 새로운 타입을 정의. (되돌아 정의할 수 있음)

```haskell
type Pos   = (Int, Int)
type Trans = Pos -> Pos

data Bool = False | True
data Nat  = Zero | Succ Nat
```

* class선언:
* instance선언: data로 정의된 것만, class instance로 선언할 수 있다.

```haskell
class Eq a where
  (==), (/=) :: a -> a -> Bool
  x /= y = not (x == y)
  x == y = not (x /= y)

instance Eq Bool where
  False == False = True
  True  == True  = True
  _     == _     = False

-- class Ord를 =>를 이용, 확장시켰다.
class Eq a => Ord a where
  (<), (<=), (>), (>=) :: a -> a -> Bool
  min, max :: a -> a
  min x y | x <= y    = x
          | otherwise = y
  max x y | x >= y    = x
          | otherwise = y

instance Ord Bool where
  False <  True = True
  _     <  _    = False
  b     <= c    = (b < c) || (b == c)
  b     >  c    = c < b
  b     >= c    = c <= b

-- data Bool을 deriving을 이용, instance를 유도했다.
data Bool = False | True deriving (Eq, Ord, Show, Read)
```

* 모나드 : 다른 타입을 인자로 받는 타입.

```haskell
class Monad m where
  return :: a -> m b
  (>>=)  :: m a -> (a -> m b) -> m b
```

## 11. 카운트다운 문제
- 인자: 여러개의 수들, 목표로하는 결과 값
- 반환: 여러개의 수들을 조합하여, 목표로하는 결과값이 나올수 있는지 여부.
- 조건:
 - 덧셈, 뺄셈, 곱셈, 나눗셈 괄호식을 이용
 - 주어진 숫자들은 한번씩만 써야함.
 - 양의 정수만 가능(음수나, 0, 분수가 나와서는 안됨.)

---------

- 접근법이 좀 신기하다.
 - bottom-up방식이긴한데,
 - 연산자, 표현식이라는 데이터를 정의해서 문제를 풀어나감.

- 내가볼땐, 이 책은 절때 프로그래밍 입문서로하면 안될듯 -_-; 러닝커브가 급격함.

## 12. 느그한 계산법

## 13. 프로그램에 대한 논리적 증명

## 정리.
:TODO

# 느낀점.
- 추천: 8장, 10장, 11장....

# Emacs 단축키
 - `C-cz` : Inf 모드로 진입.
 - `C-cl` : 현재파일 로드.
 - `C-M-i` : 자동완성.

# 참고자료.
 - [저자-사이트]
 - [역자-사이트]

http://davidtran.doublegifts.com/blog/?cat=7

 [저자-사이트]: http://www.cs.nott.ac.uk/~gmh/book.html
 [역자-사이트]: http://pl.pusan.ac.kr/~haskell/wiki/
 [LISP 사용자모임]: http://cafe.naver.com/lisper
 [Hugs - haskellwiki]: http://www.haskell.org/haskellwiki/Hugs
 [GHC]: http://www.haskell.org/ghc/
 [haskell platoform]: http://www.haskell.org/platform/
 [Robozzle Kata in Haskell]: http://vimeo.com/8445870
