```crackme``` 라는 프로그램은 말 그대로 크랙 연습 목적으로 작성되어 공개된 프로그램입니다.

## abex' crackme #1

<img width="214" alt="program" src="https://user-images.githubusercontent.com/66156026/150151667-2a5fa80c-25e2-4c9c-b867-d33199d9e142.png">

디버거를 시작하기 전에 파일을 실행시켜보면 "Make me think your HD is a CD-Rom." 이라는 메시지 박스가 출력됩니다.
CD-Rom 이라는 단어를 보면 HD가 HDD(Hard Disk Drive)를 의미한다는 것을 추측할 수 있습니다. 

<img width="195" alt="error" src="https://user-images.githubusercontent.com/66156026/150152028-793395c9-f9d6-4697-a9a5-b2c6c68741ca.png">

[확인] 을 누르면 Error 메시지 박스가 출력되면서 종료됩니다. 아직 어떻게 무엇을 크랙하라는 건지 이해할 수 없기 때문에 코드를 직접 보고 파악해야 합니다.

### Start debugging
OllDbg를 이용해 파일을 열어봅니다.

<img width="812" alt="crackme" src="https://user-images.githubusercontent.com/66156026/150154902-9f1aa76f-0d1f-47e3-b1ed-522a6d5e1873.png">

이전의 HelloWorld 파일과는 다르게 EP 코드가 매우 짧은 것을 확인할 수 있는데, 이는 **abex' crackme 파일이 어셈블리 언어로 만들어진 실행 파일**이기 때문입니다.
다른 개발툴을 사용하면 작성한 소스 코드 외에 컴파일러가 Stub Code를 추가시키기 때문에 디스어셈을 하면 복잡하게 보입니다.
하지만 어셈블리 언어로 작성을 하면 어셈 코드가 곧 디스어셈 코드가 되기 때문에 **EP에 main 함수가 바로 나타나는 직관적인 코드는 어셈블리 언어로 개발했다는 증거가 될 수 있습니다.**

### 코드 분석
Win32 API 함수 호출 위주로 살펴봅니다.

<img width="263" alt="1" src="https://user-images.githubusercontent.com/66156026/150320111-522176c1-b3a6-4688-b720-6c6911c941d7.png">

<br/>

<img width="263" alt="2" src="https://user-images.githubusercontent.com/66156026/150320117-cbaec4bd-7983-4678-ad4f-cc1205e065ba.png">

```assembly
MessageBox("Make me think your HD is a CD-Rom.")
GetDriveType("C:\")
...
MessageBox("Nah... This is not a CD-Rom Drive!")
MessageBox("OK, I really think that your HD is a CD-Rom! :p")
ExitProcess()
```

Windows 프로그래밍을 해봤다면 위 함수의 의미를 알고 있을 수 있습니다.
GetDriveType() API로 C 드라이브의 타입을 얻어오는데(대부분 HDD 타입이 리턴), 이걸 조작하여 CD-ROM 타입으로 인식하도록 만들어 "OK, I really think that your HD is a CD-ROM! :p" 메시지 박스가 출력되도록 하는 것이었습니다.

**라인별 상세 분석**

```; MessageBoxA() 호출```

<img width="612" alt="1" src="https://user-images.githubusercontent.com/66156026/150345015-fcc794f7-e6a3-44d7-bd68-222dfc0cd167.png">

```; 함수 내부에서 ESI = FFFFFFFF로 세팅됩니다.```

```; GetDriveTypeA() 호출```

<img width="612" alt="2" src="https://user-images.githubusercontent.com/66156026/150345227-81c80cd8-71cc-4255-8a72-6c5f96ea84e3.png">

```; 리턴 값(EAX)은 3(DRIVE_FIXED) 입니다.```

<img width="612" alt="3" src="https://user-images.githubusercontent.com/66156026/150345452-c2d2804e-3c1f-46df-b274-66b11f4498ef.png">

40101D ```; ESI = 0```

40101E ```; EAX = 2```

40101F ```; 의미 없는 JMP 명령(garbage code)```

401021 ```; ESI = 1```

401022 ```; ESI = 2```

401023 ```; EAX = 1```

```; 조건 분기(401028 또는 40103D)```

<img width="612" alt="4" src="https://user-images.githubusercontent.com/66156026/150345859-6f6b4b1f-febe-4c65-8bcb-1f04204e6d32.png">

401024 ```; EAX(1)과 ESI(2) 비교```

401026 ```; JE(Jump if Equal) 조건 분기 명령, 두 값이 같으면 40103D로 점프하고, 다르면 밑(401028)으로 진행. 40103D 주소는 제작자가 원하는 메시지 박스 출력 코드```

```; 실패 MessageBoxA() 호출```

<img width="612" alt="5" src="https://user-images.githubusercontent.com/66156026/150346278-aaac3cc1-53f7-4186-85c4-7a366eda5b93.png">

```; 성공 MessageBoxA() 호출```

<img width="612" alt="6" src="https://user-images.githubusercontent.com/66156026/150346452-a996717d-301c-4942-8d9d-460a60a55d46.png">

```; 프로세스 종료```

<img width="612" alt="7" src="https://user-images.githubusercontent.com/66156026/150346566-83834eb4-0877-4afa-bfa3-7dd6fa20d8ad.png">

### 위 코드에서 사용된 어셈블리 명령어 설명
<table>
<tr><th>명령어</th><th>설명</th></tr>
<tr><td>PUSH</td><td>스택에 값을 입력</td></tr>
<tr><td>CALL</td><td>지정된 주소의 함수를 호출</td></tr>
<tr><td>INC</td><td>값을 1 증가</td></tr>
<tr><td>DEC</td><td>값을 1 감소</td></tr>
<tr><td>JMP</td><td>지정된 주소로 점프</td></tr>
<tr><td>CMP</td><td>주어진 두 개의 operand 비교
                    <br/>* SUB 명령어와 동일하나 operand 값이 변경되지 않고 EFLAGS 레지스터만 변경됨
                    <br/> (두 operand의 값이 동일하다면 SUB 결과는 0이고 ZF=1로 세팅)</td></tr>
<tr><td>JE</td><td>조건 분기(Jump if Equal)
                    <br/>* ZF=1이면 점프</td></tr>
</table>


## 크랙
코드를 패치해 프로그램을 크랙합니다.

> 리버싱에서 기존의 코드(혹은 데이터)를 의도적으로 다른 코드로 덮어 쓰는 행위를 **"패치시킨다"** 라고 합니다.

단순 패치가 목적이라면 401026 주소 명령어(JE SHORT 0040103D)를 'Assemble' 기능[Space]을 이용하여 JMP 0040103D 명령어로 변경할 수 있습니다.

<img width="291" alt="patch" src="https://user-images.githubusercontent.com/66156026/150349172-616516f6-c6b3-4535-8858-31dc6f53c171.png">

조건 분기(JE) 명령어를 점프(JMP) 명령어로 바꾸는 것입니다.(CMP 명령어의 결과에 상관없이 무조건 점프해버립니다.)

## 스택에 파라미터를 전달하는 방법
위의 코드에서 중요하게 봐야 할 점은 함수 호출 시 스택에 파라미터를 전달하는 방법입니다.

401000~40100E 주소 사이의 명령어를 보면 MessageBoxA() 함수를 호출하기 전 4번의 PUSH 명령어를 사용하여 필요한 파라미터를 역순으로 입력합니다.

<img width="631" alt="4PUSH" src="https://user-images.githubusercontent.com/66156026/150349933-e25ead87-aa82-41ca-be18-edd0dc8ffba0.png">

위 어셈블리 코드를 C 언어로 번역하면,
```cpp
MessageBox(NULL, "Make me think your HD is a CD-Rom.", "abex' 1st crackme", MB_OK|MB_APPLMODAL);
```

실제 **C 언어 소스코드에서 함수에 넘기는 파라미터의 순서가 어셈블리 언어에서는 ```역순```**으로 넘어갑니다.

**스택은 FILO(First In Last Out) 구조이기 때문에 역순으로 입력**합니다.
FILO 구조이기 때문에 파라미터를 역순으로 넣어주면 받는 쪽(MessageBoxA 함수 내부)에서 올바른 순서로 꺼낼 수 있습니다.

<img width="379" alt="stack" src="https://user-images.githubusercontent.com/66156026/150351139-c9e058db-50b6-4829-9c27-d575e8280eac.png">

디버거를 이용해 EIP = 0040100E 시점까지 진행한 다음 스택을 보면 x86 환경에서 스택은 아래로 자라기(주소가 작아지는 방향으로 진행 - 스택에 값을 입력하면 ESP 값은 작아짐) 때문에 **MessageBoxA() 함수의 첫 번째 파라미터가 스택의 제일 위에 보이고, 마지막 파라미터가 아래에 쌓입니다.**

따라서 MessageBoxA() 함수 내부에서는 스택에서 파라미터를 하나씩 꺼내게 되는데, 스택의 FILO 구조에 따라서 첫 번째 파라미터부터 꺼낼 수 있게 되는 것입니다.(C 언어 소스코드에서 작성했던 순서대로)
파라미터를 꺼내서 사용하는 MessageBoxA() 함수 입장에서는 스택에 파라미터의 순서대로 들어 있는 셈이 됩니다.

> Garbage Code는 디버깅을 방해하고 리버서를 혼란시키기 위해 고의적으로 추가된 것입니다.

