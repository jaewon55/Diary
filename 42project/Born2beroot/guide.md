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
+ `cat /etc/group | grep jaewchoi`
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
+ passwd \[option\] \[계정\]
+ chage명령어 :