아주 간단한 crackme 샘플을 분석하고 디버거와 디스어셈 코드에 익숙해지는 단계입니다. ```crackme``` 라는 프로그램은 말 그대로 크랙 연습 목적으로 작성되어 공개된 프로그램입니다.

## abex' crackme #1

<img width="214" alt="program" src="https://user-images.githubusercontent.com/66156026/150151667-2a5fa80c-25e2-4c9c-b867-d33199d9e142.png">

디버거를 시작하기 전에 파일을 실행시켜보면 "Make me think your HD is a CD-Rom." 이라는 메시지 박스가 출력됩니다.
CD-Rom 이라는 단어를 보면 HD가 HDD(Hard Disk Drive)를 의미한다는 것을 추측할 수 있습니다. 

<img width="195" alt="error" src="https://user-images.githubusercontent.com/66156026/150152028-793395c9-f9d6-4697-a9a5-b2c6c68741ca.png">

[확인] 을 누르면 Error 메시지 박스가 출력되면서 종료됩니다. 아직 어떻게 무엇을 크랙하라는 건지 이해할 수 없기 때문에 코드를 직접 보고 파악해야 합니다.

> crackme 샘플은 보통 serial key를 맞추는 것이 대부분인데 abex #1은 조금 특이합니다.

### Start debugging
OllDbg를 이용해 파일을 열어봅니다.

<img width="812" alt="crackme" src="https://user-images.githubusercontent.com/66156026/150154902-9f1aa76f-0d1f-47e3-b1ed-522a6d5e1873.png">

이전의 HelloWorld 파일과는 다르게 EP 코드가 매우 짧은 것을 확인할 수 있는데, 이는 abex' crackme 파일이 어셈블리 언어로 만들어진 실행 파일이기 때문입니다.


