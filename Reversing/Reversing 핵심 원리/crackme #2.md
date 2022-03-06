Visual Basic으로 작성된 파일을 분석합니다. Visual C++이나 Assembly로 작성된 파일과는 또 다른 파일 형태입니다.

> 메모리(스택) 주소들은 사용자의 PC 환경에 따라 변경됩니다!

## abex' crackme #2 실행

<img width="247" alt="fileOpen" src="https://user-images.githubusercontent.com/66156026/150725595-a66e0ebe-38ab-44ae-a117-b02bf0f5892e.png">

전형적인 crackme 형태인 시리얼 키(Serial Key)를 알아내는 프로그램입니다.
Name을 따로 입력받기 때문에 Serial 값을 생성할 때 Name 문자열이 사용된다는 것을 추측해볼 수 있습니다.
Name과 Serial을 적절히 입력하고 [Check] 버튼을 누릅니다.

<img width="247" alt="errorM" src="https://user-images.githubusercontent.com/66156026/150725800-ec7d3847-9632-4861-9d2c-03d399e1332f.png">

"Wrong serial!" 메시지 박스가 출력됩니다. 다른 값으로 여러 번 시도해봐도 같은 메시지 박스가 출력됩니다.

## Visual Basic 파일 특징
이번에 분석할 abex's 2nd crackme 파일은 Visual Basic으로 제작되었습니다. Visual Basic 파일의 특징을 먼저 공부했습니다.

### VB 전용 엔진
VB 파일은 **MSVBVM60.dll(Microsoft Visual Basic Virtual Machine 6.0)** 이라는 VB 전용 엔진을 사용합니다.(The Thunder Runtime Engine이라는 이름으로 불리기도 합니다.)
VB 엔진은 메시지 박스를 출력하고 싶을 때 VB 소스코드에서 **MsgBox()** 함수를 사용할 수 있습니다.
VB 컴파일러는 실제로 **MSVBVM60.dll!rtc MsgBox()** 함수가 호출되도록 만들고, 이 함수 내부에서 Win32 API인 **user32.dll!MessageBoxW()** 함수를 호출해주는 방식으로 동작합니다.(VB 소스코드에서 user32.dll!MessageBoxW() 함수를 직접 호출하는 것도 가능합니다.)

### N(Native) code, P(Pseudo) code
VB 파일은 컴파일 옵션에 따라 **N code**와 **P code**로 컴파일이 가능합니다. **N code는 일반적인 디버거에서 해석 가능한 IA-32 Instruction을 사용**하는 반면에
**P code는 인터프리터(Interpreter) 언어 개념으로서 VB 엔진으로 가상 머신을 구현하여 자체적으로 해석 가능한 명령어(바이트 코드)를 사용**합니다.
그렇기 때문에 VB의 P code를 정확히 해석하려면 VB 엔진을 분석하여 에뮬레이터를 구현해야 합니다.

> P code와 유사한 형태로는 Java, Python 등이 있습니다. **P code의 장점은 이식성이 좋다**는 것입니다.(플랫폼별로 엔진을 제작하고 배포할 때 다른 플랫폼에서 기존의 사용자 코드를 거의 수정 없이 그대로 사용 가능합니다.)

### Event Handler
VB는 **주로 GUI 프로그래밍을 할 때 사용**되며, IDE 인터페이스 자체도 GUI 프로그래밍에 최적화되어 있습니다.
**VB 프로그램은 Windows 운영체제의 Event Driven 방식으로 동작**하기 때문에 main() 혹은 WinMain()에 사용자 코드(디버깅을 원하는 코드)가 존재하는 것이 아닌, **각 event handler에 사용자 코드가 존재합니다.**

위의 abex' crackme #2에서는 [Check] 버튼 handler에 사용자 코드가 존재할 것입니다.

### undocumented 구조체
VB에서 사용되는 **각종 정보들(Dialog, Control, Form, Module, Function 등)은 내부적으로 구조체 형식으로 파일에 저장**됩니다.
Microsoft에서는 이러한 구조체 정보를 정식으로 공개하지 않았기 때문에 VB 파일의 디버깅에 어려움이 있습니다.

## Start debugging

<img width="657" alt="code" src="https://user-images.githubusercontent.com/66156026/150728986-d4d99916-e120-4d46-bca4-7f7e6045ded2.png">

프로그램이 시작되고 **EP(Entry Point) 코드에서 처음 하는 일은 VB 엔진의 메인 함수(ThunRTMain)를 호출하는 것**입니다.

<img width="657" alt="83" src="https://user-images.githubusercontent.com/66156026/150729240-b7bebfa4-2aaf-457a-b76f-df371d868866.png">

EP 주소는 401238입니다. PUSH 401E14 명령에 의해 RT_MainStruct 구조체 주소(401E14)를 스택에 입력합니다.
그 다음 40123D 주소의 CALL 00401232 명령에 의해서 401232 주소의 JMP DWORD PTR DS:[4010A0] 명령어를 실행합니다.
VB 엔진의 메인 함수인 ThunRTMain() 함수로 갑니다.(스택에 입력했던 401E14 값은 ThunRTMain() 함수의 파라미터입니다.)

위 3줄이 VB 파일의 startup 코드 전부입니다.

### 간접호출
40123D 주소의 CALL 401232 명령은 특이한 기법을 사용하는 **ThunRTMain() 함수 호출**입니다.
**MSVBVM60.dll!ThunRTMain()으로 직접 가는 것이 아니라 중간의 401232 주소의 JMP 명령을 통해서 가기 때문입니다.**

<img width="657" alt="1" src="https://user-images.githubusercontent.com/66156026/150735329-82e87f0d-be2e-44f5-9e8a-90107d60a48b.png">

이런 기법은 VC++, VB 컴파일러에서 많이 사용하는 ```간접 호출(Indirect Call)``` 기법입니다.

> 4010A0 주소는 IAT(Import Address Table) 영역이며, MSVBVM60.ThunRTMain() 함수의 실제 주소가 담겨있습니다.

### RT_MainStruct 구조체
**ThunRTMain() 함수의 파라미터인** ```RT_MainStruct``` 구조체를 중요하게 봐야합니다. 401E14 주소에 RT_MainStruct가 존재합니다.

<img width="605" alt="rt" src="https://user-images.githubusercontent.com/66156026/150738440-e11e6af3-b08c-49e8-9dc6-5e6cdd6b206c.png">

MS에서 RT_MainStruct를 공개한 것은 아니지만, 실력있는 해외 리버서들이 분석하여 인터넷에 공개하였습니다.
**RT_MainStruct 구조체의 멤버는 또 다른 구조체의 주소들**입니다. **VB 엔진은 파라미터로 넘어온 RT_MainStruct 구조체를 가지고 프로그램의 실행에 필요한 모든 정보를 얻는다는 것**을 알 수 있습니다.

### ThunRTMain() 함수

<img width="659" alt="rtmain" src="https://user-images.githubusercontent.com/66156026/150739096-87a3f6ad-6f13-4e92-a663-6d0762e46740.png">

ThunRTMain() 코드의 시작 부분입니다. 이 주소는 MSVBVM60.dll 모듈의 주소 영역입니다. 메모리 주소가 완전히 달라지는 것을 볼 수 있습니다.
이 부분은 우리가 분석하는 프로그램의 코드 부분이 아니라 VB 엔진의 코드입니다.

## crackme 분석
아직 RT_MainStruct 구조체를 분석하는 것은 어렵기 때문에 에러 메시지 박스와 그 문자열을 이용해 패치해야 할 코드를 찾아봅니다.

### 문자열 검색
[Ctrl+F2] - OllDbg의 ```문자열 검색 기능(All referenced text strings)``` 을 사용하여 에러 메시지 박스에서 보았던 **"Wrong serial!"** 문자열을 더블 클릭하여 해당 주소로 이동합니다.

<img width="559" alt="text string" src="https://user-images.githubusercontent.com/66156026/150740415-070b073e-7d9c-456c-a341-d816bdea33d4.png">

<img width="656" alt="1" src="https://user-images.githubusercontent.com/66156026/150740600-683dbafa-b958-4a80-954b-e13301f44a8f.png">

메시지 박스의 타이틀("Wrong serial!"), 내용("Nope, this serial is wrong!")과 실제 메시지 박스 함수 호출 코드(4034A6)까지 볼 수 있습니다.

프로그래밍 관점에서 생각해보면, 알고리즘으로 시리얼 키를 생성하고 사용자가 입력한 키와 문자열 비교를 통해 각각 **TRUE**와 **FALSE**로 코드가 갈라질 것입니다.
그렇다면 위 코드 전후에 문자열 비교 코드와 키가 맞았을 때의 성공 메시지 박스 호출 코드가 존재하게 될거라 유추할 수 있습니다.

403458 주소에서 스크롤을 조금 올리면 추측한 대로 분기문을 포함한 코드가 나타납니다.

<img width="656" alt="2" src="https://user-images.githubusercontent.com/66156026/150744915-2db74c68-d12b-43c2-998c-6d86419c6f4d.png">

403329 주소의 __vbaVarTstEq() 함수를 호출해서 리턴 값(AX)을 비교(TEST)한 후 403332 주소의 조건 분기(JE 명령)에 의해서 참인지 거짓인 코드인지 분기하게 됩니다.

> **어셈블리 명령어**
> - **TEST** : 논리 비교(Logical Compare)
> 'AND' 연산과 동일. 두 오퍼랜드 중 하나가 0이면 AND 연산 결과는 0 -> ZF = 1로 세팅된다.
> - **JE** : 조건 분기(Jump if equal) : ZF = 1이면 점프한다.



