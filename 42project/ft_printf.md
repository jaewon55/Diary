# FT_PRINTF

## 1.가변 인자(variable argument)
+ 가변 인자 : printf, scanf 처럼 매개변수의 개수가 정해지지 않은 함수의 인자
+ 선언 : 가변 인자를 정의하기 위해서는 하나의 고정 매개변수 뒤에 ...을 붙여 매개변수의 개수가 정해지지 않았다는 표시를 해준다.
```
반환값자료형	함수이름(자료형 고정매개변수, ...)
{	
}
```
+ 위에 정의된 함수에 인자를 넣어 실행하면 첫 매개변수가 고정 매개변수가 되고 나머지 값은 가변 인자가 된다.
+ 가변인자를 사용하기 위해서는 `stdarg.h`헤더 파일에 정의된 매크로를 사용해야 한다.
   
### 1-1.stdarg.h
+ 가변 인자를 포함한 함수에서 가변인자 list에 접근할 수 있는 매크로를 정의한 헤더
+ `va_list` : 가변 인수를 읽기 위한 포인터 변수
	+ 함수의 매개변수는 스택에 저장되고 이러한 변수를 각각 읽어오기 위해서는 포인터 연산이 필요하다. va_list는 가변 인자를 읽어오기 위한 자료형으로 `char *`형으로 정의되어 있다.
	+ 변수명은 관습적으로 ap(argument pointer)를 사용한다.
+ `va_start(va_list ap, argN)` : va_arg()를 실행하기전 고정 매개변수(argN)를 이용해 ap를 가변 인자의 시작주소로 설정한다.
+ `va_copy(va_list dest, va_list src)` : dest를 src의 복사본으로 초기화한다.
+ `va_arg(va_list ap, type)` : 가변 인수를 실제로 읽어 ap에 다음 인수의 주소를 넣고, 현재 인수의 값을 반환한다.
+ `va_end(ap)` : 가변 인수를 다 사용 후 처리(NULL)를 하는 매크로다.
   
## 참고
+ [가변 인자](https://dojang.io/mod/page/view.php?id=577)
+ [stdarg.h](https://jangsalt.tistory.com/entry/%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%88%98-vastart-vaend-vaarg-valist) 
