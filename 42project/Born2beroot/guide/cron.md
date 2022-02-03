# Cron
### subject
+ monitoring.sh라는 간단한 스크립트를 bash에서 만들어야 한다. 서버 시작 시 스크립트는 10분마다 모든 터미널에 대한 아래 정보들을 표시한다.
    + OS의 architecture 및 kernel version
    + 물리적 프로세서 개수
    + 가상 프로세서 개수
    + 서버에서 사용 가능한 RAM 및 사용률(%)
    + 서버에서 사용 가능한 메모리 및 사용률(%)
    + 프로세서 사용률(%)
    + 마지막 재부팅 날짜 및 시간
    + LVM 활성화 여부
    + 작동중인 연결 개수
    + 서버를 사용하고 있는 이용자 수
    + 서버의 IPv4와 MAC주소
    + sudo 프로그램으로 실행된 명령의 수
    + defense시 스크립트의 내용이 어떻게 작동하는지 설명할 수 있어야 한다. 또한 수정하지 않고 일시정지 할 수 있어야 한다.
****
### Cron
#### Cron?
+ 특정한 시간 또는 특정 시간 마다 작업을 자동으로 수행하고 싶을 때 사용하는 명령어
+ crontab - cron작업을 설정하는 파일을 crontab파일이라고 한다. (/etc/crontab 파일에 설정된 내용을 읽어서 수행)
+ crontab의 7개 필드
    + 분 : 0~59로 설정
    + 시 : 0~23으로 설정
    + 일 : 1~31로 설정
    + 월 : 1~12로 설정
    + 요일 : 0~7또는 sun, mon등 글자로 설정 (0과 7일 일요일)
    + 사용자 : 사용자이름
    + 명령 : 실행할 명령어

#### Cron 설정
```
crontab -e
```
+ **cron을 설정할 editor가 열린다.**
```
*/10 * * * * bash /root/monitoring.sh | wall
```
+ **10분마다 monitoring.sh을 wall명령어와 실행**
+ **wall : 전체 사용자에게 메세지를 전송한다.(Broadcast message)**
```
service cron start
// service cron stop
```
+ **cron실행**
****
### monitoring.sh작성
#### OS의 architecture 및 kernel version
+ **`uname` : 시스템에 대한 정보를 출력한다.**
+ **`uname`옵션**
    + -a : -p 와 -i 옵션을 제외하고 모든 정보를 출력
    + -s : 커널의 이름을 출력한다.
    + -n : 네트워크의 호스트 이름을 출력한다.
    + -r : 커널의 릴리즈 정보를 출력한다.
    + -v : 커널의 버전을 출력한다.
    + -m : 시스템의 하드웨어 타입을 출력한다.(아키텍쳐)
    + -p : 프로세서의 종류를 출력한다.
    + -o : 운영체제의 이름을 출력한다.

#### physical/virtual processors의 개수
+ **physical processors : cpu의 코어**
+ **virtual processors : cpu의 쓰래드**
+ **`lscpu` : cpu의 정보를 표시**
+ **`/proc/cpuinfo` : cpu의 정보를 저장한 파일**
    ```
    processor   :   0
    ...
    physical id :   0
    ...
    processor   :   1
    ...
    physical id :   0
    ...
    ```
    + **위는 1개의 코어에 2개의 쓰래드가 있는 경우 cpuinfo의 내용이다. 2개의 processor이지만 physical id는 동일하다. -> 1개의 physical processors(코어)에 2개의 virtual processors(쓰래드)**

#### 서버에서 사용 가능한 RAM/디스크 및 사용률
+ **`free` : 메모리의 현황을 출력**
+ **`df` : 디스크의 현황을 출력**

#### CPU사용률
+ **`top` : 현재 서버의 상태를 실시간으로 나타냄**

#### 마지막 재부팅 날짜 및 시간
+ **`who` : 로그인한 사용자에대한 정보**
    + `who -b` : 마지막 재부팅 날짜 및 시간

#### LVM 활성화 여부
+ **lsblk에서 lvm활성화 확인**

#### 작동중인 연결 개수
+ **ss : 소켓의 상태를 출력**
    + tcp : TCP this means  established  connections, 설정된 연결

#### 서버를 사용하고 있는 이용자 수
+ **/etc/passwd 파일에서 /bin/bash로 설정된 사용자에서 root는 제외**

#### 서버의 IPv4와 MAC주소
+ **ip 주소 : 네트워크 주소, IPv4는 3자리 숫자(0~255) 4개의 조합으로 구성된 주소**
+ **MAC 주소 : 하트웨어 주소, 컴퓨터의 고유한 주소**
+ **lo : 로컬호스트, 루프백, 자기 자신한테 정보를 재전달 할 때 사용하는 IP주소 127.0.0.1**
+ **ip addr : 모든 네트워크에 할당된 주소를 표시한다.**

#### sudo 프로그램으로 실행된 명령의 수
+ **journalctl : systemd의 로그를 확인한다. systemd-journald.service에 의해 systemd의 정보를 분석한다.**
    + ``journalctl _COMM=sudo`` _COMM 옵션으로 특정 프로그램지정이 가능하다.
****