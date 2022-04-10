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
