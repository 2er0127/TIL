CPU에 사용되는 명령어 집합구조(Instruction Set Architecture, ISA)는 IA-32, x86-64, APM 등 종류가 다양합니다.
그렇기 때문에 어셈블리어도 많은 종류가 존재합니다. 보통 Windows 운영체제에서 사용되는 x64 어셈블리어를 기준으로 공부했습니다.

## x64 어셈블리 언어
### 기본 구조
x64 어셈블리 언어의 문장은 동사에 해당하는 **명령어(Operation Code, OPcode)** 와 목적어에 해당하는 **피연산자(Operand)** 로 구성되어 있습니다.

### 명령어
매우 많은 명령어들 중의 중요한 21개의 명령어들입니다.
<table>
<tr><th>데이터 이동(Data Transfer)</th><td>mov, lea</td></tr>
<tr><th>산술 연산(Arithmetic)</th><td>inc, dec, add, sub</td></tr>
<tr><th>논리 연산(Logical)</th><td>and, or, xor, not</td></tr>
<tr><th>비교(Comparison)</th><td>cmp, test</td></tr>
<tr><th>분기(Branch)</th><td>jmp, je, jg</td></tr>
<tr><th>스택(Stack)</th><td>push, pop</td></tr>
<tr><th>프로시져(Procedure)</th><td>call, ret, leave</td></tr>
<tr><th>시스템 콜(System call)</th><td>syscall</td></tr>
</table>

### 피연산자
- 상수(Immediate Value)
- 레지스터(Register)
- 메모리(Memory)

메모리 피연산자는 **[ ]** 안에 표현되며 앞의 크기 지정자는 **TYPE PTR**이 추가될 수 있습니다. 
타입의 종류에는 **BYTE, WORD, DWORD, QWORD**가 올 수 있고, 각각 **1바이트/2바이트/4바이트/8바이트**의 크기입니다.

**메모리 피연산자의 예**
<table>
<tr><th>QWORD PTR [0x8048000]</th><td>0x8048000의 데이터를 8바이트만큼 참조</td></tr>
<tr><th>WORD PTR [rax]</th><td>rax가 가르키는 주소에서 데이터를 2바이트만큼 참조</td></tr>
</table>

## x86-64 어셈블리 명령어
### 데이터 이동 명령어
데이터 이동(Data Transfer) 명령어에는 ```mov```와 ```lea```가 존재합니다.

데이터 이동 명령어는 **어떤 값을 레지스터나 메모리에 옮기도록 지시**합니다.

```mov dst, src``` : src에 들어있는 값을 dst에 대입
<table>
<tr><th>mov rdi, rsi</th><td>rsi의 값을 rdi에 대입</td></tr>
<tr><th>mov QWORD PTR[rdi], rsi</th><td>rsi의 값을 rdi가 가리키는 주소에 대입</td></tr>
<tr><th>mov QWORD PTR[rdi+8*rcx], rsi</th><td>rsi의 값을 rdi+8*rcx가 가리키는 주소에 대입</td></tr>
</table>

```lea dst, src``` : src의 유효주소(Effective Address, EA)를 dst에 저장
<table>
<tr><th>lea rsi, [rbx+8*rcx]</th><td>rbx+8*rcx를 rsi에 저장</td></tr>
</table>

### 산술 연산 명령어
산술 연산(Arithmetic) 명령어에는 ```inc``` ```dec``` ```add``` ```sub``` 가 존재합니다.

산술 연산 명령어는 **덧셈, 뺄셈, 곱셈, 나눗셈 연산**을 지시합니다.

```add dst, src``` : dst에 src의 값을 더합니다.
<table>
<tr><th>add eax, 3</th><td>eax += 3</td></tr>
<tr><th>add ax, WORD PTR[rdi]</th><td>ax += *(WORD *)rdi</td></tr>
</table>

```sub dst, src``` : dst에서 src의 값을 뺍니다.
<table>
<tr><th>sub eax, 3</th><td>eax -= 3</td></tr>
<tr><th>sub ax, WORD PTR[rdi]</th><td>ax -= *(WORD *)rdi</td></tr>
</table>

```inc op``` : op의 값을 1 증가
<table>
<tr><th>inc eax</th><td>eax += 1</td></tr>
</table>

```dec op``` : op의 값을 1 감소
<table>
<tr><th>dec eax</th><td>eax -= 1</td></tr>
</table>

**산술 연산 문제 풀이 추가하기**

### 논리 연산 - and & or
논리 연산 명령어는 ```add``` ```or``` ```xor``` ```neg``` 등의 **비트 연산**을 지시합니다. 연산은 비트 단위로 이루어집니다.

```add dst, src``` : dst와 src의 비트가 모두 1이면 1, 아니면 0
```assembly
[Register]
eax = 0xffff0000
ebx = 0xcafebabe

[Code]
and eax, ebx

[Result]
eax = 0xcafe0000
```

```or dst, src``` : dst와 src의 비트 중 하나라도 1이면 1, 아니면 0
```assembly
[Register]
eax = 0xffff0000
ebx = 0xcafebabe

[Code]
or eax, ebx

[Result]
eax = 0xffffbabe
```

### 논리 연산 - xor & not
```xor dst, src``` : dst와 src의 비트가 서로 다르면 1, 같으면 0
```assembly
[Register]
eax = 0xffff0000
ebx = 0xcafebabe

[Code]
xor eax, ebx

[Result]
eax = 0x35014541
```
> xor 연산을 **동일한 값으로 두 번** 실행하면 원래의 값으로 돌아갑니다.

```not op``` : op의 비트 전부 반전
```assembly
[Register]
eax = 0xffffffff

[Code]
not eax

[Result]
eax = 0x00000000
```

### 비교
비교 명령어는 **두 피연산자의 값을 비교하고, 플래그를 설정**합니다.

```cmp op1, op2``` : op1과 op2를 비교

cmp는 두 피연산자를 빼서 대소를 비교합니다. 연산의 결과는 op1에 대입하지 않습니다.

서로 같은 두 수를 빼면 결과가 0이 되기 때문에 ZF플래그가 설정되는데, 이후에 CPU가 이 플래그를 보고 두 값이 같았는지 판단할 수 있습니다.

```test op1, op2``` : op1과 op2를 비교

test는 두 피연산자에 AND 비트연산을 취합니다. 마찬가지로 연산의 결과를 op1에 대입하지 않습니다.

만약 0이된 rax가 op1과 op2로 삼아 test를 수행하면, 결과가 0이기 때문에 ZF 플래그가 설정됩니다. 이후에 CPU는 이 플래그를 보고 rax가 0이었는지 판단할 수 있습니다.

### 분기
분기 명령어를 ```rip```를 이동시켜 **실행 흐름을 바꿉니다.**
분기문은 많은 종류가 있지만 직관적으로 의미를 파악할 수 있는 이름을 가진 분기문이 있습니다.

```jmp addr``` : addr로 rip를 이동

```je addr``` : 직전에 비교한 두 피연산자가 같으면 점프 (jump if equal)

```jg addr``` : 직전에 비교한 두 연산자 중 전자가 더 크면 점프 (jump if greater)

## 스택과 프로시저
운영체제의 핵심 자료구조인 스택에는 ```push``` ```pop``` 이 있고, C언어의 함수에 대응되는 프로시저에는 ```call``` ```leave``` ```ret``` 이 있습니다.

### Opcode : 스택
```push val``` : **val을 스택 최상단에 쌓습니다.**

> **연산**
> rsp -= 8,
> [rsp] = val

```assembly
[Register]
rsp = 0x7fffffffc400

[Stack]
0x7fffffffc400 | 0x0 <= rsp
0x7fffffffc408 | 0x0

[Code]
push 0x31337
```

```assembly
[Register]
rsp = 0x7fffffffc3f8

[Stack]
0x7fffffffc3f8 | 0x31337 <= rsp
0x7fffffffc400 | 0x0
0x7fffffffc408 | 0x0
```


```pop reg``` : **스택의 최상단의 값을 꺼내어 reg에 대입**합니다.

> **연산**
> reg = [rsp],
> rsp += 8

```assembly
[Register]
rax = 0
rsp = 0x7fffffffc3f8

[Stack]
0x7fffffffc3f8 | 0x31337 <= rsp
0x7fffffffc400 | 0x0
0x7fffffffc408 | 0x0

[Code]
pop rax
```

```assembly
[Register]
rax = 0x31337
rsp = 0x7fffffffc400

[Stack]
0x7fffffffc400 | 0x0 <= rsp
0x7fffffffc408 | 0x0
```

### Opcode : 프로시저
프로시저(Procedure)는 특정 기능을 수행하는 코드 조각입니다. 프로시저를 사용하면 반복되는 연산을 프로시저 호출로 대체할 수 있어서 전체 코드의 크기를 줄일 수 있으며, 기능별로 코드 조각에 이름을 붙일 수 있게 되어 코드의 가독성을 크게 높일 수 있습니다.

프로시저를 부르는 행위를 **호출(Call)** 이라고 하고, 프로시저에서 돌아오는 것을 **반환(Return)** 이라고 합니다. 
프로시저를 호출할 때는 프로시저를 실행하고 나서 원래의 실행 흐름으로 돌아와야 하기 때문에 Call 다음의 명령어 주소(return address, 반환 주소)를 스택에 저장한 후,
프로시저로 rip를 이동합니다.

프로시저의 명령어에는 ```call``` ```leave``` ```ret``` 이 있습니다.

```call addr``` : addr에 위치한 프로시저 호출

```leave``` : 스택프레임 정리

> **스택 프레임**이란 함수별로 서로 사용하는 스택의 영역을 구분하기 위해 사용되는 것입니다.

```ret``` : return address로 반환