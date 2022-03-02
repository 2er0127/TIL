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

