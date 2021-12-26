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
	+ 함수의 매개변수는 스택에 저장되고 이러한 변수를 각각 읽어오기 위해서는 포인터 연산이 필요하다. va_list는 가변 인자를 읽어오기 위한 자료형으로 char *형으로 정의되어 있다.
	+ 변수명은 관습적으로 ap(argument pointer)를 사용한다.
+ `void va_start(va_list ap, argN)` : va_arg()를 실행하기전 고정 매개변수(argN)를 이용해 ap를 가변 인자의 시작주소로 설정한다.
+ `void va_copy(va_list dest, va_list src)` : dest를 src의 복사본으로 초기화한다.
+ `type va_arg(va_list ap, type)` : 가변 인수를 실제로 읽어 ap에 다음 인수의 주소를 넣고, 현재 인수의 값을 반환한다.
+ `void va_end(ap)` : 가변 인수를 다 사용 후 처리(NULL)를 하는 매크로다.
   
## 2.printf
+ 정의 : printf함수는 format을 표준출력(stdout)으로 출력한다.
	```c
	int	printf(const char *restrict format, ...);
	```
	+ format : format은 일반적인 문자열과 서식 지정(conversion specifaction)을 포함하고 있다.
	+ 서식 지정(conversion specifaction) : 서식 지정은 %로 시작되며 서식 지정자(conversion specifier)로 끝나는 형식이다. %문자와 서식 지정자 사이에는 4개의 옵션을 지정할 수 있다.
+ 반환값 : printf를 통해 성공적으로 출력이 완료되면 출력한 문자(character)들의 개수를 반환한다.
   
### 2-1.서식 지정(conversion specifaction)
+ 옵션 : ```%[flags][width][.precision][length modifier][conversion specifier]```
	+ % : 서식 지정의 시작점
	+ flage : optional으로 여러 옵션을 지정할 수 있다.
		```
		- : 필드 내에서 좌측정렬(기본값 : 우측 정렬)
		+ : 표시되는 숫자가 항상 + 또는 - 부호를 달게 된다.(기본값 : - 부호만 붙는다.)
		space : 음이 아닌 숫자의 앞에 빈칸이 오게 된다.(+플래그는 space플래그를 덮어쓴다.)
		# : 8진수는 0으로, 0이 아닌 16진수는 0x(0X)로 시작한다. 소수는 항상 소수점을 표시한다.
		0(zero) : 필드 너비를 꽉 채울 때까지 0이 숫자 앞에 붙는다.
		g, i, o, u, x, X 변환이고 precision이 지정된 경우 무시된다.(-플래그는 0플래그를 덮어쓴다.)
		```
	+ minimum field width : optional으로 숫자 또는 *문자가 들어갈 수 있다. 출력될 최소 넓이를 지정하여 공백이 왼쪽에 추가되어 좌측 정렬된다. *문자의 경우 다음 인자에서 그 값을 얻는다.
	+ .precision : optional으로 뒤에 오는 서식 지정자에 따라 의미가 달라진다.
		```
		d, i, o, u, x, X : 숫자들의 최소 개수(만약 더 적은 숫자만으로 표시되는 경우 0이 앞쪽에 추가됨)
		a, A, e, E, f, F : 소수점 이후에 오는 숫자의 개수
		g, G : 유효숫자의 개수
		s : 바이트의 최대 숫자
		```
		+ precision은 .뒤에 정수가 오거나 문자 *이 온다. *문자의 경우 precision은 다음 인자로부터 얻어진다.
		+ 만약 인자가 음수인 경우 precision을 지정하지 않은 것과 같게 된다.
		+ .문자만 사용된 경우 precision은 0이 된다.
	+ length modifier : optional으로 length modifier가 존재하면 서식 지정자의 일반적인 형식 보다 길거나 짧다는 것을 의미한다.
		<img src = https://t1.daumcdn.net/cfile/tistory/2276194658544EE12B>
	+ conversion specifier : 서식 지정의 마지막. default argument promotion 덕분에 가변 인자를 갖는 함수에 전달된 float인자는 double로 자동적으로 변환된다. (printf로 전달된 char인자는 int로 변환된다.)
		<img src = https://t1.daumcdn.net/cfile/tistory/246AB546585460CB03>
   
### 2-2.진수별 표기법(2/8/16진수)
+ 각 진수별 22표기법
```c
int	binary = 0b10110 // 0B10110
int	octal = 026
int	hex	= 0x16 // 0X16
```
   
## 3 순환(recursive)Make
+ 정의 : 프로젝트의 규모가 큰 경우 각 모듈 디렉토리 별로 Makefile을 두어 독립적으로 관리하게 된다. 실행시 처음 실행되는 Makefile을 top Makefile이라고 하고 여기서 전체적인 설정과 각 모듈별 의존관계를 설정 후 하나의 Makefile이 실행되는것 처럼 일괄 빌드하는 것을 recursive make라고 한다.
+ sub-make 실행 : top Makefile에서 `$(MAKE) -C [경로] [옵션, 명령]`

## 참고
+ [가변 인자](https://dojang.io/mod/page/view.php?id=577)
+ [stdarg.h](https://jangsalt.tistory.com/entry/%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%88%98-vastart-vaend-vaarg-valist) 
+ [서식 지정자](https://ziegler.tistory.com/88)
+ [Recursive Make](https://mug896.github.io/make-script/recursive_make.html)
