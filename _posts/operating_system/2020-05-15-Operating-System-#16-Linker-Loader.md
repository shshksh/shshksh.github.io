---
title: "운영체제-16 Linker and Loader"
excerpt: "OS L13 S1 - L13 S2"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-05-15T19:42:00+09:00
toc: true
---

## 개요

Source files → Compile → Object files

Object files → **Linker** → Executable file

Executable file → **Loader** → Process



## Contents in Source and Object Files

**소스 파일**

- 사람이 읽을 수 있도록 작성된 파일(human readable file).

**오브젝트 파일**

- 메모리에 올라갈 이진 값(명령어, 데이터...)
- 이진 값이 로드될 메모리 주소
- Symbol table
  - 프로그램에서 사용하는 Symbol 들에 대한 정보를 저장하는 자료구조
- Relocation table
  - 외부 Symbol 참조에 대한 정보를 저장하는 자료구조(linking 시 사용하는 정보)



## Compiler, Linker, Loader

**Compiler**

- 소스 파일을 오브젝트 파일로 변환

**Linker**

- 각각의 오브젝트 파일을 하나의 오브젝트 파일이나 실행 파일로 결합
- 명령어와 데이터를 로드하기 위한 메모리 주소를 특정

**Loader**

- 실행 파일을 읽고 파일에서 주소를 가져와 해당 위치에 명령어와 데이터를 load
- OS 가 로더의 함수를 구현(fork, exac ..)



## Section(segment)

오브젝트 파일은 여러 개의 section 으로 구성됨.

**What is Section?**

- 오브젝트 파일을 구성하는 unit, 데이터의 단위.

- 각각의 섹션은 메모리에 로드될 때 고유의 주소를 가진다.(segment)
- 각각의 섹션은연속적인 메모리 영역을 할당받고, 해당 영역에는 동일한 프로퍼티가 저장되어 있다.
- 섹션 내의 심볼의 위치는 섹션상의 상대 주소(offset)로 표현된다.

**Section의 구성**

- **Text(code)**: 프로그램이 수행할 instruction
- **Data**: 초기화된 전역 변수
- **BSS**: 초기화되지 않은 전역 변수
- **others**: symbol table, relocation table, ...
- 텍스트와 데이터 영역만이 메모리에 로드된다.

**Linker**

링커는 각각의 오브젝트 파일의 섹션들을 하나로 연결한다. 여러 섹션들을 어떻게 연결할 지는 linker script에 저장되어 있고 linker가 시작될 때 이 script를 읽어들인다.

**Loader**

로더는 실행 파일(linked object files)을 메모리로 읽어 들인다. 각각의 section에 대한 메모리 주소를 결정하고 PC의 값을 text section의 시작 주소로 변경한다.



## Problems in Compiling and Linking

**Problem 1**

- 코드 상의 변수와 함수들은 각각 심볼로 표현되어 있지만 CPU는 심볼을 해석할 수 없다.
- symbolic name → memory address 변환이 필요하다. = **Symbol table**

**Problem 2**

- 컴파일러는 로드된 섹션의 메모리 주소를 알지 못한다.
- 여러 소스파일들의 cross-reference 로 인해 심볼 테이블 만으로는 이 문제를 해결할 수 없다.



## Symbol Table

**함수 호출 또는 전역 변수 사용 시**

- 소스 파일에서는 함수의 호출이나 변수 참조를 위해 symbolic name이 사용된다.
- 오브젝트 파일에서는 심볼릭 네임을 인지할 수 없고 호출이나 참조를 위한 메모리 주소만 인지한다.
- 컴파일러는 이러한 심볼릭 네임을 메모리 주소로 변환한다.

**Symbol table 구성**

- Symbol Name
- symbol의 section
- section 상의 offset



## Relocation Table

컴파일러는 심볼 테이블만으로는 모든 심볼 참조를 메모리 주소로 변환할 수 없다.

- 동일한 섹션의 심볼(가능)
- 다른 섹션의 심볼(cross-reference, 불가능)



**동일한 오브젝트 파일의 심볼(internal symbol)**

- section relative address(offset) 를 PC relative address 로 변환
- PC relative 이므로 section이 load될 주소를 알 필요가 없다. → 컴파일러가 처리 가능



**다른 오브젝트 파일의 심볼(external symbol) **

- single-pass 로는 외부 심볼을 처리할 수 없다.

- Cross-reference: 다른 모듈의 심볼을 참조하는 것. PC relative address 로 표현할 수 없다.
- 컴파일러는 외부 심볼에 대한 참조를 만나면 단순하게 해당 심볼의 메모리 주소를 0으로 지정한다.
- 이후 외부 심볼 참조에 대한 처리가 필요하다는 의미로 해당 심볼을 relocation table에 저장하고 linker에게 제공한다.



**Relocation table**

- cross-reference가 발생하는 명령어의 위치를 저장
- 해당 레퍼런스의 symbolic name 저장
- 링킹 후 심볼에 각각의 심볼에 대한 주소가 결정 되면, relocation table을 확인하여 0으로 저장된 메모리 주소를 정확한 값으로 변경한다.(second-pass)



static storage allocation

- code segment
- data segment
- 프로그램이 실행될 때 한번 할당되고 프로그램이 종료될 때 자원을 반환

dynamic storage allocation

- stack segment
- heap segment
- 런타임상 동적으로 메모리를 관리