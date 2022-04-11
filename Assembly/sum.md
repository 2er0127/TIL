```assembly
MAIN SEGMENT
	ASSUME CS : MAIN, DS : MAIN

	MOV AX, MAIN
	MOV DS, AX

	MOV AL, 100 ; 16진수로 64H
	SUM BB, AL

	MOV AH, 4CH
	INT 21H

	BB DB 200 ; 16진수로 C8H

MAIN ENDS
END
```
결과 : 메모리(BB) **실행 전** : ```C8H``` => 메모리(BB) **실행 후** : ```64H```

<br/>

```assembly
MAIN SEGMENT
	ASSUME CS : MAIN, DS : MAIN

	MOV AX, MAIN
	MOV DS, AX

	MOV AX, 0
	MOV DX, 0
	MOV DL, VAR1 ; 명령어(1BYTE)
	ADD AX, DX ; 명령어(2BYTE)
	MOV DL, VAR2
	ADD AX, DX
	MOV DL, VAR3
	ADD AX, DX
	MOV DL, VAR4
	ADD AX, DX
	MOV ANS, AX

	MOV AH, 4CH
	INT 21H

	VAR1 DB 50H ; 데이터 정의 지시어
	VAR2 DB 60H ; 데이터 정의 지시어
    VAR3 DB 80H ; 데이터 정의 지시어
	VAR4 DB 0F0H ; 데이터 정의 지시어
	ANS DW ? ; 데이터 정의 지시어

MAIN ENDS
END
```
결과 : **AX** - ```02 20``` = > **메모리(ANS)** - ```20 02```
