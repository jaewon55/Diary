# Born2beroot_guide

## 파티션 나누기

## sudo설치 및 관리
+ sudo명령은 root권한을 빌려 사용하기 때문에 보안관리를 철저하게 해야한다. 이런 관리는 /etc/sudoers파일에서 관리한다.
+ `dpkg -l sudo` 명령으로 sudo설치 확인
+ `apt install sudo` 명령으로 sudo설치
+ `visudo` /etc/sudoers 파일을 편집
    + 일반적인 편집기(nano, vi)를 통해 sudo를 편집하면 문제가 발생할 수 있음 따라서 파일에서도 visudo를 통해 편집할 것을 권장한다.
    + visudo를 통해 편집을 하면 잘못된 편집에 대해 지적을 해준다.
+ sudoers파일 편집
    ```Defaults	authfail_message="custom message"```   
    + sudo접근 실패 시 출력하는 메시지
    ```Defaults	badpass_message="Wrong password custom"```   
    + 잘못된 비밀번호 입력 시 출력하는 메시지
    ```Defaults	log_input```   
    + 입력 로그를 기록함
    ```Defaults	log_output```   
    + 출력 로그를 기록함
    ```Defaults	requiretty```   
    + 현재 셀이 tty일 때만 sudo를 실행할 수 있도록 한다.
    ```Defaults	iolog_dir="/var/log/sudo/"```   
    + sudo 로그를 저장할 디렉터리의 경로를 설정한다.
    ```Defaults	passwd_tries=3```    
    + 암호 입력 횟수를 지정(기본도 3회)
    ```Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"```    
    + sudo명령을 실행할 때 사용하는 가상 셀의 PATH를 제한한다.
   
## 그룹 설정
+ 그룹 목록은 /etc/group 파일에 저장된다.
+ `grep jaewchoi /etc/group`
+ `groupadd user42` user42(그룹) 추가
+ `groupdel user42` user42(그룹) 삭제
+ `gpasswd -a jaewchoi user42` user42(그룹)에 jaewchoi(사용자) 추가
+ `gpasswd -d jaewchoi user42` user42(그룹)에서 jaewchoi(사용자) 삭제

## AppArmor
+ AppArmor는 시스템 관리자가 프로그램의 권한을 제한할 수 있도록 하는 리눅스 커널 보안 모듈이다.
    + 프로그램의 프로필을 통해 네트워크 접근, raw 소켓 접근, 파일의 읽기, 쓰기, 실행권한 등 해당 프로그램이 취할 수 있는 작업을 제한하고 관리한다.
    + 강제적 접근 통제(MAC)를 제공하며 취약한 서버 소프트웨어에 유용하다.
        + MAC(Mandatory Access Control) : 미리 정해진 정책과 보안 등급에 의거 허용된 접근 권한과 부여된 허용등급을 비교하여 접근을 통제하는 모델
            + ex) 보안등급이 낮은 사용자가 보안등급이 높은 파일에 접근할 수 없음
    + 프로필은 enforcing mode와 complain mode로 실행할 수 있다.
        + enforcing mode : 허용하지 않은 리소스 접근을 차단
        + complain mode : 제한하지 않고 위반 사항만 로그로 작성

## UFW
+ 방화벽 : 서로 다른 네트워크를 지나는 데이터를 허용하거나 거부하거나 검열, 수정하는 하드웨어나 소프트웨어 장치. 일반적으로 신뢰할 수 있는 내부 네트워크(인트라넷)와 신뢰할 수 없는 외부 네트워크(인터넷)간의 장벽을 구성한다.
+ UFW(Uncmplicated FireWall) : 데비안 계열 및 다양한 리눅스 환경에서 작동되는 사용하기 쉬운 방화벽 관리 프로그램으로 간단한 명령 및 명령수가 적은 CLI를 사용하고 iptables를 사용한다.
    + iptable : 시스템 관리자가 리눅스 커널 방화벽이 제공하는 테이블과 그것을 저장하는 체인, 규칙들을 구성할 수 있게 해주는 응용 프로그램(리눅스상에서 방화벽을 설정하는 도구)
+ 설정
    ```apt install ufw```
    + ufw설치
    ```ufw status verbose```
    + 기본상태 확인
    ```ufw enable```
    + 방화벽 실행
    ```ufw allow 4242```
    + 4242포트 허용

## SSH(Secure SHell)
+ 네트워크 상의 다른 컴퓨터에 로그인 하거나 원격 시스템에서 명령을 실행하고 다른 시스템으로 파일을 복사할 수 있도록 해주는 응용 프로그램 또는 프로토콜. 안전하지 못한 네트워크에서 안전하게 통신을 할 수 있는 기능을 제공하고 기본적으로 22번 포트를 사용한다.
+ ssh는 public key 와 private key가 한 쌍을 이루어 통신시 인증을 한다.
    + public key : 메세지를 암호화해서 전송이 가능하지만 복호화는 불가능 하다.
    + private key : 본인의 컴퓨터에 저장하고 쌍을 이루는 public key와 비교해 인증을 하고 암호화된 메세지를 복호화 가능하다.
+ 설정
    ```systemctl status ssh```
    + ssh실행과 포트확인
    ```vim /etc/ssh/sshd_config```
    + ssh 설정파일 열기
        + ssh설정은 sshd_config(서버) ssh_config(클라이언트) 두 개가 있다.
    + Port를 4242로 PermitRootLogin은 no로 설정한다.
    ```sudo systemctl restart ssh```
    + ssh재시작으로 설정 적용
+ ssh로 원격 접속(포트 포워딩)
    + 가상환경에서 `hostname -I`명령으로 IP주소를 확인
    + virtualbox의 설정->Network->PortForwarding에서 새로운 포트 포워딩 규칙 추가
        + 호스트 포트 : 로컬에서 사용중이 아닌 포트 아무거나
        + 게스트 ip : 가상환경의 ip
        + 게스트 포트 : 가상환경의 포트 (subject에서는 특정 포트만 열어두니깐 해당 포트로 설정)
    + 로컬에서 `ifconfig | grep inet`명령으로 IP주소 확인
    + 로컬에서 `ssh [계정]@[로컬 ip 주소] -p [호스트 포트]`


## 비밀번호 정책
+ passwd \[option\] \[계정\] : 기본적으로 비밀번호를 변경하는 명령이지만 계정을 사용하지 못하게 하거나, 패스워드 만기일 및 유효기간 등을 설정할 수 있다.
    + 옵션
    ```
        -a : all이라는 뜻으로 -S옵션과 같이 쓰여 모든 사용자에 대한 비밀번호 정보를 보여준다.
        -S : satus 사용자에 대한 비밀번호 정보를 알 수 있다.
        -l : lock 사용자의 비밀번호에 잠금을 걸어 로그인을 막는다.
        -u : unlock 사용자에게 설정되어 있는 잠금을 푼다.
        -n : 패스워드 변경까지의 최소 날짜를 설정한다. 패스워드 변경 후 최소로 사용해야 되는 날짜수
        -x : 현재 패스워드의 유효기간을 지정한다.
        -w : 패스워드 만료 전 경고 날짜를 지정한다.
        -i : 패스워드 만료된 뒤 사용자 계정 사용이 실제 로그인이 불가능하게 되기까지 유예기간을 설정
        -e : 다음 로그인 시에 반드시 패스워드를 변경하도록 할 때 사용한다.
    ```
    + -S옵션의 7개 필드
    ```
        1. 유저 로그인명
        2. L(비밀번호 잠김), NP(비밀번호 없음), P(사용가능한 비밀번호)
        3. 가장 최근에 변경된 일자
        4. 비밀번호 변경까지 최소 일자
        5. 비밀번호 변경까지 최대 일자
        6. 비밀번호 만료를 알리는 경고 기간
        7. 비밀번호가 만료되고 비밀번호가 잠기기까지의 유예기간
    ```

+ chage \[optioin\] \[계정\] : 비밀번호 관련 저장 파일(/etc/shadow)의 날짜 관련 필드 설정을 모두 할 수 있는 명령어
    + 옵션
    ```
        -l : 사용자의 비밀번호에 대한 정보를 보여준다.
        -d : 최근 비밀번호를 바꾼 날을 수정한다.
        -m : 비밀번호 변경의 최소 날짜를 지정한다.
        -M : 비밀번호 변경 없이 사용 가능한 최대 날짜를 지정한다.
        -I : 비밀번호 만료 후 잠금까지 유예기간을 지정한다.
        -E : 계정이 만기되는 날을 지정한다.
        -W : 비밀번호 만료 전 변경을 요구하는 경고 날짜를 지정한다.
    ```

+ 전체 정책 변경
    + 날짜관련 전체 설정
        ```vim /etc/login.defs```
        + 비밀번호 default 설정파일
        + `PASS_MAX_DAYS` : 비밀번호 유효기간 설정 30
        + `PASS_MIN_DAYS` : 비밀번호 최소 사용기간 2
        + `PASS_WARN_AGE` : 만료기간전 경고메세지 전송일 수 7
    + 강력한 암호정책 설정
        ```vim /etc/pam.d/common-password```
        + 암호정책을 설정하는 파일
        ```apt install libpam-cracklib```
        + 비밀번호의 강도를 설정하고 검사하는 모듈인 libpam-cracklib를 설치
        + 정책 옵션
        ```
            debug : 모듈이 동작을 보여주기 위해 syslog에 정보를 남긴다.
            type=LINUX : 모듈의 기본 동작은 패스워드를 물어 볼때 LINUX라고 바꿀
            retry=N : 암호 입력 재시도 가능 횟수
            difok=N : 이전 암호와 비교해서 최소 N개 이상의 문자가 달라야 함
            minlen=N : 암호의 최소 길이 설정
            dcredit=N : (N < 0) 암호가 포함해야하는 숫자의 최소 개수
                        (N >= 0) 숫자가 가질 수 있는 크레딧값의 최대값
            ucredit=N : (N < 0) 암호가 포함해야하는 대문자의 최소 개수
                        (N >= 0) 대문자가 가질 수 있는 크레딧값의 최대값
            lcredit=N : (N < 0) 암호가 포함해야하는 소문자의 최소 개수
                        (N >= 0) 소문자가 가질 수 있는 크레딧값의 최대값
            ocredit=N : (N < 0) 암호가 포함해야하는 숫자의 특수문자 개수
                        (N >= 0) 특수문자가 가질 수 있는 크레딧값의 최대값
            minclass=N : 암호에 있어야 하는 최소 문자 종류
            maxrepeat=N : 연속된 같은 문자를 N개까지만 허용
            maxsequence=N : 단순한 나열의 문자를 N개까지만 허용
            maxclassrepeat=N : 같은 종류의 문자를 N개까지만 허용
            reject_username : 암호가 계정의 이름을 포함하는지 검사
            enforce_for_root : root계정에도 상기의 옵션을 적용
        ```
        ```minlen=10 difok=7 dcredit=-1 lcredit=-1 ucredit=-1 maxrepeat=3 reject_username enforce_for_root```

## 새로운 유저 추가
```mkdir /home/newuser```
+ 새로운 유저의 홈디렉터리 생성
```useradd -d /home/newuser newuser```
+ 홈디렉터리를 지정한 새로운 계정 생성
```passwd newuser```
+ 새계정의 비밀번호 지정
```vim /etc/default/useradd```
+ 새계정의 기본셀을 bash셀로 지정

## hostname
```hostname```
+ 명령으로 host확인
```hostnamectl set-hostname [newname]```
+ 명령으로 hostname 변경


## cron
+ 특정한 시간 또는 특정 시간 마다 작업을 자동으로 수행하고 싶을 때 사용하는 명령어
+ crontab - cron작업을 설정하는 파일을 crontab파일이라고 한다. (/etc/crontab 파일에 설정된 내용을 읽어서 수행)
    + crontab의 7개 필드
    ```
    분 : 0~59로 설정
    시 : 0~23으로 설정
    일 : 1~31로 설정
    월 : 1~12로 설정
    요일 : 0~7또는 sun, mon등 글자로 설정 (0과 7일 일요일)
    사용자 : 사용자이름
    명령 : 실행할 명령어
    ```
+ /usr/sbin/anacron : cron과 함께 동작하는 프로그램으로 서버가 중지되었을 때도 작업을 실행하도록 보장한다.
+ /var/log/cron : cron실행내용이 기록되는 로그파일
```crontab -e```
+ crontab을 수정할 editor가 열린다.
```*/10 * * * * bash /root/monitoring.sh | wall```
+ wall명령을 통해 전체 사용자에게 monitoring.sh의 실행내용을 메세지로 전송한다.(Broadcast message)
```service cron start```
+ cron실행

## OS의 architecture 및 kernel version
+ `uname` : 시스템에 대한 정보를 출력한다.
    + 옵션
        ```
        -a : -p 와 -i 옵션을 제외하고 모든 정보를 출력
        -s : 커널의 이름을 출력한다.
        -n : 네트워크의 호스트 이름을 출력한다.
        -r : 커널의 릴리즈 정보를 출력한다.
        -v : 커널의 버전을 출력한다.
        -m : 시스템의 하드웨어 타입을 출력한다.(아키텍쳐)
        -p : 프로세서의 종류를 출력한다.
        -o : 운영체제의 이름을 출력한다.
        ```
    + `uname -snrvmo`

## physical/virtual processors의 개수
+ physical processors : cpu의 코어
+ virtual processors : cpu의 쓰래드
+ `lscpu` : cpu의 정보를 표시
    + `lscpu | grep Core\(s\) | awk '{ print $4 }'`
+ `/proc/cpuinfo` : cpu의 정보를 저장한 파일
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
    + 위는 1개의 코어에 2개의 쓰래드가 있는 경우 cpuinfo의 내용이다. 2개의 processor이지만 physical id는 동일하다. -> 1개의 physical processors(코어)에 2개의 virtual processors(쓰래드)
    + `grep processor /proc/cpuinfo | wc -l)`

## 서버에서 사용 가능한 RAM/디스크 및 사용률
+ `free` : 메모리의 현황을 출력
    + total : `free -m | grep Mem | awk '{ print $2 }'`
    + used : `free -m | grep Mem | awk '{ print $3 }'`
    + persentage : `free -m | grep Mem | awk '{ printf "%.2f\n", $3/$2 * 100}'`
+ `df` : 디스크의 현황을 출력
    + total : `df -ht ext4 --total | grep total | awk '{ print $2 }' | tr -d "A-Z"`
    + used : `df -ht ext4 --total | grep total | awk '{ print $3 }'`
    + persentage : `df -ht ext4 --total | grep total | awk '{ print $5 }'`

## CPU사용률
+ `top` : 현재 서버의 상태를 출력
    + `top -b -n 1 | grep Cpu | tr -d "nid," | awk '{print 100.0-$7}'`

## 마지막 재부팅 날짜 및 시간
+ `who` : 로그인한 사용자에대한 정보
    + `who -b` : 마지막 재부팅 날짜 및 시간
    + `who -b | awk '{print $3}'`
    + `who -b | awk '{print $4}'`

## LVM 활성화 여부
+ lsblk에서 lvm활성화 확인
    ```
    if [ $(lsblk | grep lvm | wc -l) -ne 0 ];then
        echo -e "\t#LVM use: yes"
    else
        echo -e "\t#LVM use: no"
    fi
    ```

## 작동중인 연결 개수
+ ss : 소켓의 상태를 출력
    + tcp : TCP this means  established  connections 설정된 연결
    + `ss -t | grep ESTAB | wc -l`

## 서버를 사용하고 있는 이용자 수
+ /etc/passwd 파일에서 /bin/bash로 설정된 사용자에서 root는 제외
    + `grep /bin/bash /etc/passwd | wc -l | awk '{ print $1-1 }'`

## 서버의 IPv4와 MAC주소
+ ip 주소 : 네트워크 주소, IPv4는 3자리 숫자(0~255) 4개의 조합으로 구성된 주소
    + `hostname -I`
+ MAC 주소 : 하트웨어 주소, 컴퓨터의 고유한 주소
    + `ip addr | grep ether | awk '{print $2}'`
+ lo : 로컬호스트, 루프백, 자기 자신한테 정보를 재전달 할 때 사용하는 IP주소 127.0.0.1
+ ip addr : 모든 네트워크에 할당된 주소를 표시한다.

## sudo 프로그램으로 실행된 명령의 수
+ journalctl : systemd의 로그를 확인한다. systemd-journald.service에 의해 systemd의 정보를 분석한다.
    + `journalctl _COMM=sudo | grep COMMAND | wc -l`

```
#!/bin/bash

RAM_TOTAL=$(free -m | grep Mem | awk '{ print $2 }')
RAM_USED=$(free -m | grep Mem | awk '{ print $3 }')
RAM_PERSENTAGE=$(free -m | grep Mem | awk '{ printf "%.2f\n", $3/$2 * 100}')
DISK_TOTAL=$(df -ht ext4 --total | grep total | awk '{ print $2 }' | tr -d "A-Z")
DISK_USED=$(df -ht ext4 --total | grep total | awk '{ print $3 }')
DISK_PERSENTAGE=$(df -ht ext4 --total | grep total | awk '{ print $5 }')
echo -e "\t#Architecture: $(uname -snrvmo)"
echo -e "\t#CPU physical : $(lscpu | grep Core\(s\) | awk '{ print $4 }')"
echo -e "\t#vCPU : $(grep processor /proc/cpuinfo | wc -l)"
echo -e "\t#Memory Usage: ${RAM_USED}/${RAM_TOTAL}MB (${RAM_PERSENTAGE}%)"
echo -e "\t#Disk Usage: ${DISK_USED}/${DISK_TOTAL}Gb (${DISK_PERSENTAGE})"
echo -e "\t#CPU load: $(top -b -n 1 | grep Cpu | tr -d "nid," | awk '{print 100.0-$7}')%"
echo -e "\t#Last boot: $(who -b | awk '{print $3}') $(who -b | awk '{print $4}')"
if [ $(lsblk | grep lvm | wc -l) -ne 0 ];then
	echo -e "\t#LVM use: yes"
else
	echo -e "\t#LVM use: no"
fi
echo -e "\t#Connexions TCP : $(ss -t | grep ESTAB | wc -l) ESTABLISHED"
echo -e "\t#User log: $(grep /bin/bash /etc/passwd | wc -l | awk '{ print $1-1 }')"
echo -e "\t#Network: IP $(hostname -I) ($(ip addr | grep ether | awk '{print $2}'))"
echo -e "\t#Sudo : $(journalctl _COMM=sudo | grep COMMAND | wc -l) cmd"
```
