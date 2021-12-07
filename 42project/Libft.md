# LIBFT
   
## Part1 - Libc Functions
+ isalpha
	```c
	int	ft_isalpha(int c);
	```
	+ return : `c`가 알파벳이면 0이 아닌 값
+ isdigit
	```c
	int	ft_isdigit(int c);
	```
	+ return : `c`가 숫자면 0이 아닌 값
+ isalnum
	```c
	int	ft_isalnum(int c);
	```
	+ return : `c`가 알파벳 또는 숫자면 0이 아닌 값
+ isprint
	```c
	int	ft_isprint(int c);
	```
	+ return : `c`가 공백(sp)을 포함한 출력문자(printing character)면 0이 아닌 값
+  strlen
	```c
	size_t	ft_strlen(const char *str);
	```
	+ return : `str`의 길이
	+ size_t : 해당 시스템에서 최대 크기의 데이터를 표현하는 unsigned형
	+ `str`이 0일 경우 undifined-behavior으로 segmentation fault에러 발생
+ memset
	```c
	void	*ft_memset(void *s, int c, size_t n);
	```
	+ return : `n`바이트만큼 `c`(unsigned char)로 변경한 `s`
	+ unsigned char로 변경해서 할당하는 이유
		+ 1byte 단위로 읽기 위함
		+ ungined char만이 메모리상 바이트 복사를 표준 명세서 상에서 보증한다.
+ bzero
	```c
	void	ft_bzero(void *s, size_t n);
	```
	+ `s`를 n바이트 만큼 NULL로 변환?
+ memcpy
	```c
	void	*ft_memcpy(void *dst, const void *src, size_t n);
	```
	+ return : `src` 부터 `n`바이트 만큼 복사한 `dst`
	+ 만약 dst와 src가 메모리상 겹치게 된다면(overlap) undefined-behavior으로 예상치 못한 결과가 나올 수 있다.
+ memmove
	```c
	void	*ft_memmove(void *dst, const void *src, size_t n);
	```
	+ return : `src` 부터 `n`바이트 만큼 복사한 `dst`
	+ memcpy에서 overlab이 발생했을 때 생길 수 있는 문제를 보완
+ strlcpy
	```c
	size_t	ft_strlcpy(char *dst, const char *src, size_t n);
	```
	+ return : 만들려고 시도한 문자열의 길이(`src`의 길이)
	+ `src`를 `n - 1`만큼 `dst`에 복사, 마지막엔 `'\0'`로 문자열의 끝을 표시
+ strlcat
	```c
	size_t	ft_strlcat(char *dst, const char *src, size_t n);
	```
	+ return : 만들려고 시도한 문자열의 길이(`dst`의 초기값 + `src`의 길이)
	+ `n`은 `dst`의 길이 + `src`의 길이 + null자리 이다.
	+ 만약 `dst`의 길이가 `n`보다 크거나 같다면 동작을 수행하지 않고 `src`의 길이 + `n`값을 반환한다.
+ toupper
	```c
	int	ft_toupper(int c);
	```
	+ (소문자라면 대문자로 변경한)`c`
+ tolower
	```c
	int	ft_tolower(int c);
	```
	+ (대문자라면 소문자로 변경한)`c`
+ strchr
	```c
	char	*ft_strchr(const char *s, int c);
	```
	+ return : 문자열 `s`의 처음부터 `c`가 위치한 주소, 없다면 null
	+ `s`의 끝(`'\0'`)도 `s`의 일부로 보고 `c`가 0이라면 `s`의 끝 주소를 반환한다.
+ strrchr
	```c
	char	*ft_strrchr(const char *s, int c);
	```
	+ return : 문자열 `s`의 끝에서 `c`가 위치한 주소, 없다면 null
	+ `s`의 끝에서 부터 `c`의 위치를 찾음
+ strncmp
	```c
	int	ft_strncmp(const char *s1, const char *s2, size_t n);
	```
	+ return : `s1`과 `s2`를 비교한 값(같다면 0, `s1`이 크다면 0보다 큰 값, `s2`가 크다면 0보다 작은 값)
	+ 값을 비교할 때는 unsigned char형으로 변환해서 비교해야 한다.
+ atoi
	```c
	int	ft_atoi(const char *str);
	```
	+ return : `str`에 포함된 정수값, 순서가 맞지 않거나 정수가 없으면 0을 반환한다.
	+ 결과가 `long long max` 이상인 경우 -1 고정
	+ 결과가 `long long min` 이하인 경우 0 고정
+ calloc
	```c
	void	*ft_calloc(size_t count, size_t size);
	```
	+ return : `count` * `size` 만큼 메모리를 할당한 후 값을 NULL로 채운 주소
+ strdup
	```c
	char	*ft_strdup(const char *s1);
	```
	+ return : 메모리를 할당하고 문자열 `s1`을 복사한 주소를 반환