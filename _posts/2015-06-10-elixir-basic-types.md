---
id: 422
title: 'Elixir Basic types (#2/21)'
date: 2015-06-10T17:08:09+00:00
author: jeidee
layout: post
guid: https://erlnote.wordpress.com/?p=422
permalink: /2015/06/10/elixir-basic-types/
categories:
  - elixir
tags:
  - tutorial
---
> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

elixir의 기본 데이터 타입을 살펴보자.

## 기본 산술 표현식과 타입

```
  
iex> 1 + 2
  
3
  
iex> 5 * 5
  
25
  
iex> 10 / 2
  
5.0
  
```

산술 연산자는 각각 +, -, *, / 연산자를 사용하면 된다.
  
주목할 것은 / 연산자인데, 결과값이 항상 float이다.

몫과 나머지를 각각 구하고자 한다면 다음과 같은 기본 산술 함수를 사용하도록 한다.

```
  
iex> div(10, 2)
  
5
  
iex> div 10, 2
  
5
  
iex> rem 10, 3
  
1
  
```

elixir 함수의 호출은 div(10, 2)와 같이 ()안에 매개변수를 ,로 구분해 입력하는 방법과 ()없이 함수명 뒤에 공백을 둔 후 매개변수리스트를 ,로 구분해 나열하는 방식 모두 사용할 수 있다.

div/2함수와 rem/2함수는 각각 몫과 나머지를 interger로 반환한다.

elixir는 이진수(binary), 8진수(octal), 16진수(hexadecimal)를 각각 다음과 같이 표기할 수 있다.

```
  
iex> 0b1010
  
10
  
iex> 0o777
  
511
  
iex> 0x1F
  
31
  
```

float은 다음과 같이 소숫점을 사용한 표기와 지수(e)를 사용한 표기법 모두 사용할 수 있다.

```
  
iex> 1.0
  
1.0
  
iex> 1.0e-10
  
1.0e-10
  
```

float은 elixir에서 64비트 double 정밀도를 갖는다.

반올림(round/1)과 버림(trunc/1) 함수를 다음과 같이 사용할 수 있다.

```
  
iex> round 3.58
  
4
  
iex> trunc 3.58
  
3
  
```

## boolean

elixir는 boolean 타입을 위해 true와 false를 사용할 수 있다.

```
  
iex> true
  
true
  
iex> true == false
  
false
  
```

elixir는 값의 타입을 확인하기 위한 다양한 함수를 제공한다.
  
예를 들어 is_boolean/1 함수는 값이 boolean 타입인지 아닌지 검사한다.
  
(elixir는 erlang과 동일하게 함수의 표현을 함수명과 arity(매개변수의 개수)로 표현한다. 예를 들면 is\_boolean/1과 같은 방식인데 함수명이 is\_boolean이고 매개변수의 개수는 1개라는 의미이다.)

is\_boolean/1외에도 is\_integer/1, is\_float/1, is\_number/1등을 사용할 수 있다.

## atom

atom은 이름 자체를 자신의 값으로 갖는 상수를 말한다. erlang의 atom과 동일한데 표기법만 다르다.
  
(구조적인 언어와 객체지향 언어에는 없는 개념이며, 상수에 값을 대입해 초기화 하는 방식이 아니라 상수명 자체가 값이 되는 방식이다.)

```
  
iex> :hello
  
:hello
  
iex> :hello == :world
  
false
  
```

atom은 콜론(:)으로 시작하며 콜론 뒤에는 반드시 영문대소문자가 위치해야 한다. 그 뒤에는 영문대소문자 | 숫자 | ! | ? 의 조합으로 사용할 수 있다.

```elixir
  
iex> :Hello
  
:Hello
  
iex> :19
  
** (SyntaxError) iex:10: invalid token: :19
  
iex> :hello19
  
:hello19
  
iex> :#hello
  
** (SyntaxError) iex:11: invalid token: :#hello
  
iex> :hello!
  
:hello!
  
iex> :hello?
  
:hello?
  
```

boolean에서 사용하는 true와 false는 사실 atom이다.

```
  
iex> true == :true
  
true
  
iex> is_atom(false)
  
true
  
iex> is_boolean(:false)
  
true
  
```

## string

elixir에서 문자열은 &#8220;&#8221;로 감싸며 UTF-8로 인코딩된다.

```
  
iex> "hellö"
  
"hellö"
  
```

문자열 안에 atom이나 변수값을 삽입할 수 있다.

```
  
iex> world = "hello"
  
"hello"
  
iex> "hellö #{:world}"
  
"hellö world"
  
iex> "hellö #{world}"
  
"hellö hello"
  
```

IO모듈의 puts/1함수를 사용해서 문자열을 출력할 수 있다.

```
  
iex> IO.puts "hellonworld"
  
hello
  
world
  
:ok
  
```

IO.puts/1함수는 문자열을 출력한 후 :ok atom을 반환한다.

elixir에서 문자열은 내부적으로 바이너리(바이트의 순차)로 표현된다.
  
(erlang에서는 문자열은 리스트이며 바이너리는 리스트가 아닌 별개의 타입으로 취급한다.)

```
  
iex> is_binary("hellö")
  
true
  
```

문자열의 길이와 byte 수는 다음과 같이 별개로 취급된다.

```
  
iex> byte_size("hellö")
  
6
  
iex> String.length("hellö")
  
5
  
```

[String 모듈](http://elixir-lang.org/docs/stable/elixir/#!String.html)은 유니코드 표준에서 정의한 다양한 문자열 처리 함수를 포함하고 있다.

```
  
iex> String.upcase("hellö")
  
"HELLÖ"
  
```

## Anonymous function(익명 함수)

익명 함수는 fn과 end 사이에 정의할 수 있다.

```
  
iex> add = fn a, b -> a + b end
  
#Function<12.71889879/2 in :erl_eval.expr/5>
  
iex> is_function(add)
  
true
  
iex> is_function(add, 2)
  
true
  
iex> is_function(add, 1)
  
false
  
iex> add.(1, 2)
  
3
  
```

elixir의 함수는 일급시민(first class citizens)이다.
  
일급시민이란 함수형 언어에서 나오는 용어로 일급객체(first class objects)라고도 불리며, 간단히 다음의 조건을 만족하면 성립한다.

  * 변수나 데이터 구조안에 담을 수 있다.
  * 매개변수로 전달 할 수 있다.
  * 함수의 반환값으로 사용할 수 있다.
  * 변수의 할당에 사용할 수 있다.

위의 예에서 add 변수에 함수를 할당하고, is_function/1의 매개변수로 전달한 예제를 볼 수 있다.
  
익명함수는 add.(1, 2)와 같은 형식으로 사용할 수 있다.(일반함수와는 다르게 .을 사용해야 하고 ()없이 사용 불가)

익명함수는 closure이며 다음과 같이 사용할 수 있다.

```
  
iex> add_two = fn a -> add.(a, 2) end
  
#Function<6.71889879/1 in :erl_eval.expr/5>
  
iex> add_two.(2)
  
4
  
```

위의 예제보다 좀 더 closure를 잘 설명할 수 있는 다음 예제를 살펴보자.

```
  
iex> makeAdder = fn x -> fn y -> x + y end end
  
#Function<6.90072148/1 in :erl_eval.expr/5>
  
iex> add5 = makeAdder.(5)
  
#Function<6.90072148/1 in :erl_eval.expr/5>
  
iex> add10 = makeAdder.(10)
  
#Function<6.90072148/1 in :erl_eval.expr/5>
  
iex> add5.(2)
  
7
  
iex> add10.(2)
  
12
  
```

위의 예제는 다음과 같은 자바스크립트 클로저를 elixir로 변환한 것이다.
  
(출처는 참고 링크의 [클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures))

```javascript
  
function makeAdder(x) {
    
return function(y) {
      
return x + y;
    
};
  
}

var add5 = makeAdder(5);
  
var add10 = makeAdder(10);

print(add5(2)); // 7
  
print(add10(2)); // 12
  
```

add5함수는 makeAdder함수에 5의 값을 할당해 정의한 closure이며 x의 값이 5로 결정되어 add5함수를 사용할 때마다 x의 값은 5가 되고 반환된 내부 익명함수인 function(y)를 호출하게 되는 것이다.
  
즉 add5.(2)는 x + y = 5 + 2 = 7, 즉, 7을 반환한다.

개념이 약간 어렵지만 매우 실용적이고 활용도가 높기 때문에 숙지할 필요가 있어서 부연해 봤다.

또 하나 중요한 개념이 있다.

```
  
iex> x = 42
  
42
  
iex> (fn -> x = 0 end).()
  

  
iex> x
  
42
  
```

위의 예제에서 x는 익명함수안에서 사용된 x = 0에 영향을 받지 않는다.
  
(erlang에서 변수는 대문자로만 표기할 수 있고, 한 번 할당되어 사용된 변수에는 다른 값을 재할당할 수 없다. 하지만 elixir에서는 변수를 소문자로 표기하며 동일한 변수에 여러 번 다른값으로 할당해 사용할 수 있다.)

## (linked) list

elixir는 대괄호([])를 사용해 리스트를 정의한다. 리스트의 요소는 어떤 타입도 사용할 수 있다.

```
  
iex> [1, 2, true, 3]
  
[1, 2, true, 3]
  
iex> length [1, 2, 3]
  
3
  
```

두 개의 리스트의 결합이나 빼기는 각각 ++/2와 &#8211;/2 연산자를 사용한다.

```
  
iex> [1, 2, 3] ++ [4, 5, 6]
  
[1, 2, 3, 4, 5, 6]
  
iex> [1, true, 2, false, 3, true] &#8212; [true, false]
  
[1, 2, 3, true]
  
```

리스트는 head와 tail로 구분되는데 head는 리스트의 첫 번째 요소의 값이고, tail은 첫 번째 요소를 제외한 나머지 요소를 갖는 리스트이다.

head와 tail은 각각 hd/1, tl/1 함수를 사용해 구할 수 있다.

```
  
iex> list = [1,2,3]
  
iex> hd(list)
  
1
  
iex> tl(list)
  
[2, 3]
  
```

hd/1와 tl/1 함수의 입력 리스트는 빈 리스트가 되어서는 안된다.

```
  
iex> hd []
  
** (ArgumentError) argument error
  
```

가끔 리스트가 &#8221;로 감싸인 값을 반환하는 경우가 있다.

```
  
iex> [11, 12, 13]
  
'vfr'
  
iex> [104, 101, 108, 108, 111]
  
'hello'
  
```

elixir는 리스트의 요소가 ASCII 코드인 경우 ASCII문자의 리스트로 출력하게 되고 이를 char list라고 부른다. char list는 기존의 erlang code와 인터페이스할 때 일반적으로 사용된다.

elixir에서 &#8221;와 &#8220;&#8221;은 다르며 다른 타입으로 표현된다.

```
  
iex> 'hello' == "hello"
  
false
  
```

&#8221;는 char list이고, &#8220;&#8221;는 문자열이다. 이 내용은 &#8220;Binaries, strings and char list&#8221; 챕터에서 더 자세하게 다룰 것이다.

## tuple

elixir는 튜플(tuple)을 정의하기 위해 중괄호({})를 사용한다. 튜플은 어떤 값도 포함할 수 있다.

```
  
iex> {:ok, "hello"}
  
{:ok, "hello"}
  
iex> tuple_size {:ok, "hello"}
  
2
  
```

튜플은 요소들을 인접한 메모리에 위치시킨다. 따라서 매우 빠른 속도로 인덱스를 사용해 요소에 접근할 수 있고 사이즈도 빠르게 구할 수 있다.(인덱스는 0부터 시작한다.)

```
  
iex> tuple = {:ok, "hello"}
  
{:ok, "hello"}
  
iex> elem(tuple, 1)
  
"hello"
  
iex> tuple_size(tuple)
  
2
  
```

특정 인덱스에 값을 할당하는 것도 가능하다.(put_elem/3)

```
  
iex> tuple = {:ok, "hello"}
  
{:ok, "hello"}
  
iex> put_elem(tuple, 1, "world")
  
{:ok, "world"}
  
iex> tuple
  
{:ok, "hello"}
  
```

put_elem/3함수는 새로운 튜플을 반환한다는 것을 주지해야 한다. 본래의 튜플은 수정되지 않으며 elixir의 데이터 타입은 불변의 특성을 갖기 때문이다.(erlang도 마찬가지이며 이러한 특성이 의도치 않은 수정으로 발생할 수 있는 오류를 원천에서 차단하게 된다.)

이러한 특성은 병행성 코드(concurrent code)에서 발생할 수 있는 race condition를 제거하는데 도움을 준다.(동시에 동일한 데이터를 변경하는 시도 자체가 성립하지 않는다.)

## list or tuple?

리스트와 튜플의 차이점은 무엇일까?

리스트는 메모리에 링크드 리스트의 형태로 저장되며, 이것이 의미하는 바는 각 요소는 값과 포인터를 갖는 cons cell로 구성되어 있음을 의미한다.

```
  
iex> list = [1|[2|[3|[]]]]
  
[1, 2, 3]
  
```

즉, 리스트의 길이를 구하는 것은 선형 동작이며 리스트의 사이즈를 구하기 위해 전체 요소를 탐색하는 동작이 필요하다.

```
  
iex> [0] ++ list
  
[0, 1, 2, 3]
  
iex> list ++ [4]
  
[1, 2, 3, 4]
  
```

첫 번째 동작은 새로운 cons cell의 포인터에 list를 가리키도록 하면 되기 때문에 빠르다. 하지만 두 번째 동작은 리스트를 재 구성하고 새로운 요소를 뒤에 추가해야 하기 때문에 느리다.

반면에 튜플은, 메모리에 연속된 형태로 저장된다. 이것이 의미하는 바는 인덱스를 사용한 요소 접근이나 사이즈 계산이 빠르다는 것을 말한다. 하지만 튜플에 요소를 추가하거나 수정하는 비용은 메모리에서 해당 튜플을 모두 복사해서 새로 구성하는 동작을 요구하므로 비싸다.

이러한 성능적 특성들은 이들 자료 구조를 어떻게 사용해야 할 지 알려준다. 튜플의 매우 통상적인 사용법 중 하나는 함수에서 추가 정보를 반환하는데 사용하는 것이다. 예를 들어, File.read/1 함수는 파일의 내용을 읽어 튜플로 반환한다:

```
  
iex> File.read("path/to/existing/file")
  
{:ok, "&#8230; contents &#8230;"}
  
iex> File.read("path/to/unknown/file")
  
{:error, :enoent}
  
```

File.read/1에 주어진 경로가 유효하면, 첫 번째 요소로 :ok atom을, 두 번째 요소로 파일 내용을 갖는 튜플을 반환한다. 경로가 유효하지 않다면 :error atom과 에러 설명을 포함한 튜플을 반환한다.

튜플에 있는 elem/2함수와 동일한 내장(built-in) 함수는 리스트에 없다:

```
  
iex> tuple = {:ok, "hello"}
  
{:ok, "hello"}
  
iex> elem(tuple, 1)
  
"hello"
  
```

자료구조에서 요소의 개수를 &#8220;카운팅&#8221;할 때, elixir는 간단한 규칙을 준수한다: 동작이 상수시간을 갖는다면(값이 이미 계산되어진 경우), 함수의 이름은 size이어야 하고 동작을 요구할 때 실제로 카운팅한다면 length이어야 한다.

예를 들어, 지금까지 4개의 카운팅 함수를 사용했는데(byte\_size/1(문자열의 byte 수), tuple\_size/1(튜플 사이즈), length/1(리스트 길이), String.length/1(문자열의 문자 개수)), byte_size/1는 비용이 싼 반면 String.length/1는 비용이 비싼 함수임을 의미한다.

elixir는 Port와 Reference, PID(일반적으로 프로세스 통신에 사용하는) 데이터타입도 제공한다. 이 타입들은 프로세스에 대해 알아볼때 함께 살펴보도록 하겠다.

## 참고

  * [elixir basic types](http://elixir-lang.org/getting-started/basic-types.html)
  * [함수형 언어로 가는 길 &#8211; 일급객체](http://blog.doortts.com/135)
  * [first class citizen](http://en.wikipedia.org/wiki/First-class_citizen)
  * [클로져(closure)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)