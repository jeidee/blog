---
author: jeidee
categories:
- elixir
date: '2015-06-17'
guid: https://erlnote.wordpress.com/?p=483
id: 483
permalink: /2015/06/17/elixir-io-and-the-file-system-1221/
tags:
- tutorial
title: Elixir IO and the file system (#12/21)
url: /2015/06/17/elixir-io-and-the-file-system-1221
---

> 본 문서는 elixir-lang.org의 [Getting Started](http://elixir-lang.org/getting-started/introduction.html)문서의 한글 번역본입니다.
    
> 자세한 내용은 원문을 참조해 주세요. 

이번 챕터에서는 IO, File, Path 모듈과 관련된 입/출력 매커니즘과 파일 시스템 관련 태스크를 소개하도록 한다.

## The IO module

elixir의 IO 모듈은 표준 입출력, 표준 에러, 파일과 다른 IO 장치들을 읽고 쓸 수 있도록 해주는 주요 매커니즘이다. 모듈의 사용법은 꽤 직관적이다.

```
  
iex> IO.puts "hello world"
  
hello world
  
:ok
  
iex> IO.gets "yes or no? "
  
yes or no? yes
  
"yesn"
  
```

기본적으로, IO 모듈의 함수는 표준 입력에서 읽고 표준 출력에 쓴다. 우리는 다른 장치를 사용할 수도 있다. 예를 들어 매개변수로 :stderr를 사용하는 것으로 표준 출력 장치를 사용할 수 있다.

```
  
iex> IO.puts :stderr, "hello world"
  
hello world
  
:ok
  
```

## The File module

File 모듈은 IO 장치로 파일을 처리할 수 있는 여러 함수들을 포함한다. 기본적으로, 파일은 바이너리 모드로 열리고, IO 모듈의 IO.binread/2와 IO.binwrite/2 함수를 사용하도록 요구한다.

```
  
iex> {:ok, file} = File.open "hello", [:write]
  
{:ok, #PID<0.47.0>}
  
iex> IO.binwrite file, "world"
  
:ok
  
iex> File.close file
  
:ok
  
iex> File.read "hello"
  
{:ok, "world"}
  
```

파일은 :utf8 인코딩을 명시해서 열 수 있고, File 모듈에 UTF-8 인코딩된 바이트를 읽도록 알려줄 수 있다.

파일을 열고/읽고/쓰는 함수들 외에도, File 모듈은 파일 시스템을 처리하는 많은 함수들을 포함한다. 그러한 함수들은 UNIX와 동등한 이름을 갖고 있다. 예를 들어, File.rm/1 함수는 파일을 삭제할 수 있고, File.mkdir/1 함수는 디렉토리를 생성하며, File.mkdir\_p/1 함수는 지정된 경로의 부모 디렉토리 체인(/path/to와 같을 경우 path도 없고 to도 없는 경우 path도 만들고 to도 만든다.)을 포함해 디렉토리를 생성한다. File.cp\_r/2와 File.rm_rf/2 함수는 디렉토리를 재귀적으로 순환하면서 각각의 파일을 복사하거나 삭제한다.(파일 뿐 아니라 디렉토리를 포함한다.)

File 모듈의 함수는 두 개의 형태를 갖는다. !가 뒤에 붙는 것과 그렇지 않은 것인데, 예를 들어 파일을 읽기 위해 File.read/1함수를 사용할 수 있다면 File.read!/1 함수도 역시 다음과 같이 사용할 수 있다.

```
  
iex> File.read "hello"
  
{:ok, "world"}
  
iex> File.read! "hello"
  
"world"
  
iex> File.read "unknown"
  
{:error, :enoent}
  
iex> File.read! "unknown"
  
** (File.Error) could not read file unknown: no such file or directory
  
```

!(bang) 함수의 경우 파일이 없다면 에러가 발생하지만, 일반적인 버전의 함수는 에러대신 튜플을 반환하며, 패턴매칭을 사용해 상황별 대처를 할 수 있다.

```
  
case File.read(file) do
    
{:ok, body} -> # do something with the \`body\`
    
{:error, reason} -> # handle the error caused by \`reason\`
  
end
  
```

하지만, 파일이 존재할 것이라 기대하고 !함수를 사용하게 되면 의미 있는 에러 메세지가 발생하게 되고, 잘못된 쓰기를 방지하는데 좀 더 유용하게 사용할 수 있다.

```
  
{:ok, body} = File.read(file)
  
```

위의 예제에서 에러가 발생할 경우 {:error, reason}이 반환되므로 패턴 매칭에 실패하게 된다. 패턴 매칭 실패에 에러 상황을 의존하게될 경우 애매해지게 되며 실제 에러가 어떤 것인지 알 수 없게 된다.

명확한 에러가 발생하길 원한다면 File.read!/1 함수를 사용하는 것이 더 좋다.

## The Path module

File 모듈의 함수들은 대부분 경로를 매개변수로 원한다. 가장 일반적으로, 이들 경로는 정규 바이너리이다. Path 모듈은 이러한 경로를 쉽게 처리하는 기능들을 제공한다.

```
  
iex> Path.join("foo", "bar")
  
"foo/bar"
  
iex> Path.expand("~/hello")
  
"/Users/jose/hello"
  
```

바이너리를 직접 처리하는 대신에 Path 모듈의 함수들을 사용는 것이 더 좋은데, 이유는 Path 모듈이 제공하는 함수들이 OS에 맞게 처리되기 때문이다. 예를 들어, Path.join/2 함수는 Unix 시스템의 경우 (/)를 사용하며 윈도우 시스템의 경우 (&#092;&#092;)를 사용한다.

elixir는 메인 모듈에서 IO와 파일 시스템을 처리하는 기능을 제공한다. 다음 섹션에서 우리는, IO에 관한 좀 더 전문적인 내용을 살펴볼 것이다. elixir 코드를 작성하기 위해 이러한 전문적인 내용이 필요하진 않으므로, 필요하다면 무시해도 좋다. 그러나 IO 시스템이 VM에서 어떻게 구현되었는지 궁금하다면 훑어볼 것을 권장한다.

## Processes and group leaders

File.open/2 함수는 {:ok, pid} 튜플을 반환한다는 것을 알고 있을 것이다.

```
  
iex> {:ok, file} = File.open "hello", [:write]
  
{:ok, #PID}
  
```

이와 같은 이유는 IO 모듈이 프로세스와 함께 동작하기 때문이다. 우리가 IO.write(pid, binary)를 작성할 때, IO 모듈은 pid로 식별되는 프로세스에 지정된 동작을 수행하라고 메세지를 전송하게 된다. 우리의 프로세스에서 어떤 일이 일어나는지 살펴보자.

```
  
iex> pid = spawn fn ->
  
&#8230;> receive do: (msg -> IO.inspect msg)
  
&#8230;> end
  
#PID
  
iex> IO.write(pid, "hello")
  
{:io\_request, #PID, #PID, {:put\_chars, :unicode, "hello"}}
  
** (ErlangError) erlang error: :terminated
  
```

IO.write/2함수를 사용한 후에, 우리는 IO 모듈에 의해 보내진 요청(4개의 요소를 갖는 튜플)을 볼 수 있다. 그런 후에, 우리는 IO 모듈이 기대하는 어떤 값을 제공해 주지 않았기 때문에 실패했음을 볼 수 있다.

StringIO 모듈은 문자열 처리를 위해 IO 장치의 메세지 구현을 제공한다.

```
  
iex> {:ok, pid} = StringIO.open("hello")
  
{:ok, #PID}
  
iex> IO.read(pid, 2)
  
"he"
  
```

프로세스에 IO 장치를 처리하도록 설계되었기 때문에, Erlang VM은 같은 네트워크 상에서 서로 다른 프로세스간 파일 처리를 가능하도록 허용한다. 모든 IO 장치의 각 프로세스는 특별한 프로세스가 하나 있는데, 그룹리더라고 부른다.

우리가 :stdio에 쓸때, 실제로는 표준 출력 파일 디스크립터를 사용하는 그룹 리더에 메세지를 보내는 것이다.

```
  
iex> IO.puts :stdio, "hello"
  
hello
  
:ok
  
iex> IO.puts Process.group_leader, "hello"
  
hello
  
:ok
  
```

그룹 리더는 프로세스 마다 설정될 수 있고, 다른 상황에서 사용될 수 있다. 예를 들어, 원격 터미널에서 코드가 실행될 때, 원격 노드에 있는 메세지가 요청이 이뤄진 터미널에 재전송되어 출력될 것임을 보장한다.

## iodata and chardata

위의 모든 예제에서, 우리는 파일을 쓸때 바이너리를 사용했다. "Binaries, strings and char lists" 챕터에서 우리는, 문자열이 단순히 코드 포인트로 이뤄진 문자 리스트임을 언급했었다.

IO와 File 모듈의 함수들 역시 매개변수로 리스트를 허용한다. 그 뿐 아니라, 리스트, 정수, 바이너리 등의 조합으로 이뤄진 리스트도 허용한다.

```
  
iex> IO.puts 'hello world'
  
hello world
  
:ok
  
iex> IO.puts ['hello', ?\s, "world"]
  
hello world
  
:ok
  
```

하지만, 이것은 약간의 주의를 필요로한다. 리스트는 바이트나 IO 장치의 인코딩에 의존하는 문자들의 모음이다. 파일이 인코딩 없이 열려있다면, 파일은 raw 모드로 열린 것이고 IO 모듈의 bin* 으로 시작하는 함수가 사용되어야 한다. 이러한 함수들은 매개변수로 iodata를 요구한다. iodata는 바이트와 바이너리를 표현하기 위해 정수로 표현되는 리스트이다.

이와 달리, :stdio와 파일이 :utf8 인코딩으로 열린 경우 IO 모듈의 나머지 함수들(bin*이외의)을 사용할 수 있다. 이러한 함수들은 매개변수로 문자들의 리스트나 문자열인 char_data를 요구한다.

비록 이것이 미묘한 차이일지라도, 함수에 리스트를 넘길 때 이러한 차이에 신경을 써야할 필요가 있다. 바이너리는 이미 바이트 기반으로 표현되고 항상 raw로 처리된다.

## 참고

  * [Elixir IO and the file system](http://elixir-lang.org/getting-started/io-and-the-file-system.html)