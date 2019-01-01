---
title: "Language Server Protocol"
date: 2019-01-01T15:33:00+09:00
author: jeidee
permalink: /2019/01/01/language-server-protocol
categories:
- programming
tags:
- ide
---

# 개요

Language Server Protocol(언어 서버 프로토콜)은 여러 프로그래밍 IDE에서 다양한 프로그래밍 언어를 지원할 때 발생하는 비표준화 문제를 해결할 수 있도록 표준화한 프로토콜입니다.

LSP는 MS에서 처음 만들었고 다음과 같이 프로토콜 명세를 공개하고 있습니다.

* [LSP명세](https://microsoft.github.io/language-server-protocol/specification)

LSP는 서버와 클라이언트간의 통신 프로토콜이며 [JSON-RPC](http://www.jsonrpc.org/)를 사용합니다.

LSP에 대해 좀 더 자세한 정보가 필요할 경우 다음 사이트의 문서를 참고하면 됩니다.

* [langserver.org](https://langserver.org/)
* [Language Server Protocol?](https://docs.microsoft.com/en-us/visualstudio/extensibility/language-server-protocol?view=vs-2017)

# 주요 LSP별 지원 기능

Langserver.org에는 프로그래밍 언어별로 구현된 Language Server 리스트와 지원 기능을 표로 제공하고 있습니다.
현 시점(2019/01/01) 기준으로 주요 언어별 LS는 다음과 같습니다.

| Language | Maintainer | Repository | Code completion | Hover | Jump to def | Workspace symbols | Find references | Diagnostics |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| C# | OmniSharp | [Repo](https://github.com/OmniSharp/omnisharp-node-client/tree/master/languageserver) | V | V | V | V | V | X |
| C/C++ | LLVM Team | [Repo](https://reviews.llvm.org/diffusion/L/browse/clang-tools-extra/trunk/clangd/) | V | V | V | WIP | X | V |
| Go | Sourcegraph | [Repo](https://github.com/sourcegraph/go-langserver) | V | V | V | V | V | V |
| Javascript | Sourcegraph | [Repo](https://github.com/sourcegraph/javascript-typescript-langserver) | V | V | V | V | V | V |
| Python | Palantir | [Repo](https://github.com/palantir/python-language-server) | V | V | V | V | V | V |

* V : 구현
* X : 구현되지 않음
* WIP : 구현 중

지원 기능은 다음과 같습니다.

* Code completion : 코드 자동 완성
* Hover : 툴팁 지원
* Jump to def : 정의로 이동
* Workspace symbols : 작업 공간(작업문서)에서 심볼 정보 제공
* Find references : 모든 참조 찾기
* Diagnostics : 진단 정보 제공