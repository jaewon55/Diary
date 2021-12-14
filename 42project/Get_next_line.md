# GET_NEXT_LINE
   
## 1.파일 입출력
+ 파일 : 관련있는 데이터의 집합으로 데이터를 저장하는데는 물론 데이터를 전송하거나 장치를 접근하는데도 사용
+ 종류 : 유닉스에서 파일은 용도에 따라 일반파일(regular file)과 특수파일(special file)로 구분
	+ 일반파일 : 텍스트, 바이너리 형태의 데이터를 저장하는 파일
	+ 특수파일 : 데이터 전송, 장치 접근에 사용되는 파일
+ 유닉스에서 파일을 읽고 쓰는 방법은 저수준(low-level)과 고수준(high-level)파일 입출력으로 구분
	+ 저수준 파일 입출력 : 유닉스 커널의 시스템 호출을 사용하여 파일 입출력을 수행 
	+ 고수준 파일 입출력 : C언어 표준 함수로 제공(standard input/output library)
   
### 1-1.저수준 파일 입출력
+ 저수준 파일 입출력은 바이트 단위로 입출력을 수행한다. 특수 파일도 읽고 쓸 수 있으므로 파일 입출력의 기본이 된다.
+ 파일 기술자(file descriptor)
	+ 파일 기술자는 현재 열려 있는 파일을 구분할 목적으로 유닉스가 붙여놓은 정수값
	+ 프로세스가 파일을 열때 파일 기술자는 0번부터 시작해서 가장 작은 번호가 자동으로 할당된다.
	+ 프로세스가 처음 동작할 때 0, 1, 2는 기본으로 할당된다.
	```
	<파일 기술자 0~2의 용도>
	0 : 표준 입력
	1 : 표준 출력
	2 : 표준 오류 츨력
	```
+ 파일 열기 : open(2)
	```c
	#include <sys/types.h>
    #include <sys/stat.h>
    #include <fcntl.h>
    int open(const char *path, int oflag [, mode_t mode]);

    [parmeter 설명]

    path : 열려는 파일이 있는 경로

    oflag : 파일 상태 플래그. <sys/fcntl.h>(/usr/include/sys/fcntl.h)파일에 #define O_RDWR 2와 같이 정의되어있다.
                플래그를 OR(|)연산자로 연결해 지정할 수 있다. 

    mode : 접근 권한(O_CREAT플래그를 지정해 파일을 생성할 때만 사용한다. 파일 권한을 설정하듯이 지정할 수 있으나 
               <sys/stat.h>에 정의된 플래그를 사용할 수 있다. 

               0644 권한 = S_IRUSR|S_IWUSR|S_IRGRP|S_IROTH;

    [return 값]

    success : 파일 기술자 

        fail : -1 및 외부변수 errno에 실패사유 코드 저장한다. perror()로 출력하면 메세지 확인 가능.
	```
+ 파일 읽기 : read(2)
	+ read함수는 파일 기술자가 가리키는 파일에서 지정한 크기만큼 바이트를 읽어서 buf로 지정한 메모리 영역에 저장한다.
	+ read함수를 실행할 때마다 읽어온 크기만큼 오프셋(offset)이 이동해 다음 읽어올 위치를 가리킨다.
	```c
	#include <unistd.h>
    ssize_t read(int fildes, void *buf, size_t nbytes);

    [parmeter 설명]
    fildes : 파일기술자
    buf : 바이트를 저장할 메모리 영역의 시작 주소
    nbytes : 읽어올 바이트수



      <파일 읽기 : ex2_4.c>

    #include <fcntl.h>
    #include <unistd.h>
    #include <stdlib.h>
    #include <stdio.h>

      int main(void){
        int fd, n;
        char buf[10];
        fd = open("unix.txt", O_RDONLY);
        if(fd == -1) {

            perror("Open");
            exit(1);
        }

        n = read(fd, buf, 6);
        if(n == -1){
            perror("Read");
            exit(1);
        }

        buf[n] = '\0';

        //read함수는 읽어온 데이터 끝에 자동으로 널을 추가하지 않으므로 널 문자를 추가한다. buf를 문자열로 출력하려면 널을 추가해야한다. 

        printf("n=%d, buf=%s\n", n, buf);
        close(fd);
        return 0;
    }

    <실행 결과>

    #ex2_4.out
    n=6, buf= Unix S
	```
   
## 정적 변수
+ 정적 변수는 프로그램이 시작될 때 생성 및 초기화되고 프로그램이 끝날 때 사라진다.
+ 변수에 static을 붙여서 선언한다.
	```c
	static int	num;
	```
+ 정적 변수는 선언할 때 초기값을 지정하지 않으면 0이 들어간다.

## 파일 기술자(file descriptor)의 갯수
+ OPEN_MAX : limit.h에 정의되어 있는 매크로
    + 클러스터기준 경로 vim /Library/Developer/CommandLineTools/SDKs/MacOSX10.15.sdk/usr/include/sys/syslimits.h
    + max open files per process - todo, make a config option?
    + 10240으로 정의되어 있다.
+ 256 : 맥의 기본 설정(ulimit -Sn)
    + 기본 설정이고 변경이 가능하다(ulimit -Hn)
+ 1024 : Linux 기본설정
    + [Sun](https://docs.oracle.com/cd/E19476-01/821-0505/file-descriptor-requirements.html)의 문서에 따르면
    Linux의 기본설정이고 프로세스의 성능향상을 위해 제한을 걸어둠
+ `open()`함수의 반환형은 int형이기 때문에 file descriptor의 최댓값은 2,147,483,647 ?
+ 결론 : 시스템마다 파일 기술자의 최댓값은 다르기 때문에 OPEN_MAX 매크로를 사용하는 것이 이상적이지만
        norminette의 형식을 지키기 위해서는 연결리스트로 구현하는 것이 가장 완벽한 작성인 것 같다.
   
## 참고
+ [파일 입출력](https://jihooyim1.gitbooks.io/unixbasic/content/contents/02.html)
+ [정적 변수](https://dojang.io/mod/page/view.php?id=690)
+ [파일 기술자](https://techblog.woowahan.com/2569/)