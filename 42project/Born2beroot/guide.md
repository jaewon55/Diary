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
    ```Defaults	badpass_message="Wrong password custom."```
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
+ `groupadd user42` 그룹 추가
+ `groupdel user42` 그룹 삭제
+ `gpasswd -a jaewchoi user42` user42그룹에 jaewchoi(사용자) 추가
+ `gpasswd -d jaewchoi user42` user42그룹에서 jaewchoi(사용자) 삭제

## AppArmor
+ AppArmor는 시스템 관리자가 프로그램의 권한을 제한할 수 있도록 하는 리눅스 커널 보안 모듈이다.
    + 프로그램의 프로필을 통해 네트워크 접근, raw 소켓 접근, 파일의 읽기, 쓰기, 실행권한 등 해당 프로그램이 취할 수 있는 작업을 제한하고 관리한다.
    + 강제적 접근 통제(MAC)를 제공하며 취약한 서버 소프트웨어에 유용하다.
        + MAC(Mandatory Access Control) : 미리 정해진 정책과 보안 등급에 의거 허용된 접근 권한과 부여된 허용등급을 비교하여 접근을 통제하는 모델
            + ex) 보안등급이 낮은 사용자가 보안등급이 높은 파일에 접근할 수 없음
    + 프로필은 enforcing mode와 complain mode로 실행할 수 있다.
        + enforcing mode : 허용하지 않은 리소스 접근을 차단
        + complain mode : 제한하지 않고 위반 사항만 로그로 작성