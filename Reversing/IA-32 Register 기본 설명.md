애플리케이션의 디버깅을 위해 기본적으로 알아야 할 ```IA-32(Intel Architecture 32bits)```의 레지스터에 대한 내용입니다.
리버싱 초급 단계에서 리버싱을 잘 하려면 디버거가 해석해주는 어셈블리 명령어를 알아야 하는데 이런 어셈블리 명령어의 대부분은 레지스터를 조작하고 그 내용을 검사하는 것이기 때문에 레지스터를 모르면 명령어 자체도 이해하기 힘듭니다.

## CPU 레지스터란?
레지스터(Register)란 CPU 내부에 존재하는 다목적 저장 공간입니다. 일반적으로 메모리라고 하는 RAM(Random Access Memory)과는 조금 성격이 다릅니다.
CPU가 RAM에 있는 데이터를 엑세스(Access)하기 위해서는 물리적으로 먼 길을 돌아가야 하기 때문에 시간이 오래 걸립니다.
하지만 레지스터는 CPU 내부에 존재하며 CPU와 한 몸이기 때문에 고속으로 데이터 처리가 가능합니다.

### IA-32의 레지스터
IA-32는 지원하는 기능도 매우 많고 그만큼 레지스터의 수도 많습니다. 애플리케이션 디버깅의 초급 단계에서는 디버깅할 때 가장 많이 보게 될 ```Basic program execution register```에 대해서 알아야 합니다.

### Basic program execution registers
![Basic Program Execution Registers](https://user-images.githubusercontent.com/66156026/149463796-6819eabe-0b3e-4158-a340-8232ba3396b6.png)

Basic program execution registers는 다시 4개의 그룹으로 나눌 수 있습니다.
- General Purpose Registers(32bits - 8개)
- Segment Registers(16bits - 6개)
- Program Status and Control Register(32bits - 1개)
- Instruction Pointer(32bits - 1개)

> 레지스터 이름에 E(Extended)가 붙은 경우는 예전 16비트 CPU인 IA-16 시절부터 존재하던 16비트 크기의 레지스터들을 32비트 크기로 확장시켰다는 것을 의미합니다.

```1) 범용 레지스터```

범용 레지스터(General Purpose Registers)는 이름처럼 **범용적으로 사용되는 레지스터**입니다.
IA-32에서 각각의 범용 레지스터들의 크기는 **32비트(4바이트씩)** 입니다. 보통은 **상수/주소 등을 저장할 때** 주로 사용되며, 특정 어셈블리 명령어에서는 특정 레지스터를 조작하기도 합니다.
또한 어떤 레지스터들은 특수한 용도로 사용되기도 합니다.

![범용 레지스터](https://user-images.githubusercontent.com/66156026/149464790-e84c5487-a256-4ee8-a5a4-eb59a54734bb.png)

EAX를 기준으로
- EAX : (0~31) 32bits
- AX : (0~15) EAX의 하위 16bits
- AH : (8~15) AX의 상위 8bits
- AL : (0~7) AX의 하위 8bits

즉 4바이트(32비트)를 다 사용하고 싶을 때는 EAX를 사용하고, 2바이트(16비트)만 사용하고 싶을 때는 EAX의 하위 16비트 부분인 AX를 사용하면 됩니다.
AX는 다시 상위 1바이트(8비트)인 AH와 하위 1바이트(8비트) AL로 나뉘어집니다. 이런 식으로 하나의 32비트 레지스터를 상황에 맞게 8비트, 16비트, 32비트로 알뜰하게 사용 가능합니다.

- EAX : Accumulator for operands and results data
- EBX : Pointer to data in the DS segment
- ECX : Counter for string and loop operations
- EDX : I/O pointer

위 4개의 레지스터들은 주로 **산술 연산(ADD, SUB, XOR, OR 등)** 명령어에서 **상수/변수 값의 저장** 용도로 많이 사용됩니다.
어떤 어셈블리 명령어(MUL, DIV, LODS 등)들은 특정 레지스터를 직접 조작하기도 합니다.
그리고 ECX와 EAX는 특수한 용도로도 사용됩니다. **ECX는 반복문 명령어(LOOP)에서 반복 카운트(loop count)로 사용**됩니다.(루프를 돌 때마다 ECX를 1씩 감소시킴.)
**EAX는 일반적으로 함수 리턴 값에 사용**됩니다. **모든 Win32 API 함수들은 리턴 값을 EAX에 저장한 후 리턴**합니다.

> Win32 API 함수들은 내부에서 ECX와 EDX를 사용합니다. 따라서 이런 API가 호출되면 ECX와 EDX의 값은 변경됩니다. ECX와 EDX에 중요한 값이 저장되어 있다면, API 호출 전에 다른 레지스터나 스택에 백업해야 합니다.

- EBP : Pointer to data on the stack (in the SS segment)
- ESI : source pointer for string operations
- EDI : destination pointer for string operations
- ESP : Stack pointer (in the SS segment)

위 4개의 레지스터들은 주로 **메모리 주소를 저장하는 포인터**로 사용됩니다. **ESP는 스택 메모리 주소**를 가리킵니다. 어떤 명령어들(PUSH, POP, CALL, RET)은 ESP를 직접 조작하기도 합니다. (스택 메모리 관리는 프로그램에서 매우 중요하기 때문에 ESP를 다른 용도로 사용하지 말아야 합니다.)

**EBP는 함수가 호출되었을 때 그 순간의 ESP를 저장하고 있다가, 함수가 리턴 하기 직전 다시 ESP에게 값을 되돌려줘 스택이 깨지지 않도록 합니다.** 이것을 **Stack Frame 기법**이라고 합니다.
**ESI와 EDI는** 특정 명령어들(LODS, STOS, REP, MOVS 등)과 함께 주로 **메모리 복사**에 사용됩니다.

```2) 세그먼트 레지스터```

세그먼트(Segment)란 IA-32의 메모리 관리 모델에서 나오는 용어입니다.
IA-32 보호 모드에서 세그먼트란 메모리를 조각내어 각 조각마다 시작 주소, 범위, 접근 권한 등을 부여해서 메모리를 보호하는 기법을 말합니다. 또한 세그먼트는 페이징(Paging) 기법과 함께 가상 메모리를 실제 물리 메모리로 변경할 때 사용됩니다.
**세그먼트 메모리는 Segment Descriptor Table(SDT)이라고 하는 곳에 기술**되어 있는데, **세그먼트 레지스터는 이 SDT의 index를 가지고 있습니다.**

![세그먼트 레지스터](https://user-images.githubusercontent.com/66156026/149467069-fc828be2-2f2e-4169-a6f4-987155419822.png)

보호 모드에서의 세그먼트 메모리 모델입니다. 세그먼트 레지스터는 총 6개(CS, SS, DS, ES, FS, GS)이며 각각의 크기는 16비트(2바이트)입니다.
각 세그먼트 레지스터가 가리키는 세그먼트 디스크립터(Segment Descriptor)와 가상 메모리가 조합되어 **선형주소(Linear Address)** 가 되며, **페이징 기법**에 의해서 선형주소가 최종적으로 **물리주소(Physical Address)** 로 변환됩니다.

> 만약 OS에서 페이징을 사용하지 않는다면 선형주소가 그대로 물리주소가 됩니다.

- CS : Code Segment
- SS : Stack Segment
- DS : Data Segment
- ES : Extra(Data) Segment
- FS : Data Segment
- GS : Data Segment

**CS는 프로그램의 코드 세그먼트**를 나타내며 **SS는 스택 세그먼트**, **DS는 데이터 세그먼트**를 나타냅니다. **ES, FS, GS 세그먼트는 추가적인 데이터 세그먼트**입니다.
FS 레지스터는 애플리케이션 디버깅에도 자주 등장하는데 SEH(Structured Exception Handling), TEB(Thread Environment Block), PEB(Process Environment Block) 등의 주소를 계산할 때 사용됩니다.

```3) 프로그램 상태와 컨트롤 레지스터```

- EFLAGS : Flag Register

플래그(Flag) 레지스터의 이름은 EFLAGS이며 32비트(4바이트) 크기입니다. (16비트의 FLAGS 레지스터의 32비트 확장 형태입니다.)
EFLAGS 레지스터는 각각의 비트마다 의미를 가지고 있습니다.

![eflags](https://user-images.githubusercontent.com/66156026/149780121-78994144-508f-4bad-ad0d-b29493f94b47.jpeg)

**각 비트의 1 or 0의 값은 On/Off 또는 True/False 를 의미**합니다. 일부 비트는 시스템에서 직접 세팅하고, 일부 비트는 프로그램에서 사용된 명령의 수행 결과에 따라 세팅됩니다.

> Flag는 깃발이 올라가면 1(On/True), 깃발이 내려가면 0(Off/False)으로 이해할 수 있습니다.

EFLAGS 레지스터의 32개의 각 비트 의미를 전부 이해하는 것은 매우 어렵기 때문에 리버싱 입문 단계에서는 애플리케이션 디버깅에 필요한 **3가지 Flag(ZF, OF, CF)** 에 대해서 우선 이해합니다.
특히 이 3개의 플래그가 중요한 이유는 조건 분기 명령어(Jcc)에서 이들 Flag의 값을 확인하고 그에 따라 동작 수행 여부를 결정하기 때문입니다.

- Zero Flag(ZF) : 연산 명령 후에 결과 값이 0이 되면 ZP가 1(True)로 세팅됩니다.
- Overflow Flag(OF) : 부호 있는 수(signed interger)의 오버플로가 발생했을 때 1로 세팅됩니다. 그리고 MSB(Most Significant Bit)가 변경되었을 때 1로 세팅됩니다.
- Carry Flag(CF) : 부호 없는 수 (unsigned integer)의 오버플로가 발생했을 때 1로 세팅됩니다.

```4) Instruction Pointer```

- EIP : Instruction Pointer

Instruction Pointer는 **CPU가 처리할 명령어의 주소를 나타내는 레지스터**이며, 크기는 32비트(4바이트)입니다.(16비트의 IP 레지스터의 확장 형태입니다.)
CPU는 EIP에 저장된 메모리 주소의 명령어(instruction)를 하나 처리하고 난 후 자동으로 그 명령어 길이만큼 EIP를 증가시킵니다.

범용 레지스터들과는 다르게 EIP는 그 값을 직접 변경할 수 없기 때문에 다른 명령어를 통해 간접적으로 변경해야 합니다.
EIP를 변경하고 싶다면 특정 명령어(JMP, Jcc, CALL, RET)를 사용하거나 인터럽트(interrupt), 예외(exception)를 발생시켜야 합니다.
