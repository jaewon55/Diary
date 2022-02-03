# Sudo설치 및 설정</br>
+ **sudo명령은 root권한을 빌려 사용하기 때문에 보안관리를 철저하게 해야한다.**</br>
+ **/etc/sudoers파일에서 설정 및 관리한다.**</br>
### Sudo설치
```
apt install sudo
```
****
### Sudo설정
```
visudo
```
+ /etc/sudoers 파일을 편집하는 명령어
    + 일반적인 편집기보다 visudo명령을 통해 편집하는 것을 권장</br>
```
Defaults	authfail_message="custom message"
Defaults	badpass_message="Wrong password custom"
Defaults	log_input
Defaults	log_output
Defaults	requiretty
Defaults	iolog_dir="/var/log/sudo/"
Defaults	passwd_tries=3
Defaults	secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```
+ **sudoers파일 편집**
    + `Defaults    authfail_message="custom message"`</br>sudo접근 실패 시 출력하는 메시지
    + `Defaults	badpass_message="Wrong password custom"`</br>잘못된 비밀번호 입력 시 출력하는 메시지
    + `Defaults	log_input`</br>입력 로그를 기록함
    + `Defaults	log_output`</br>출력 로그를 기록함
    + `Defaults	requiretty`</br>tty일 때만 sudo를 실행할 수 있도록 한다.
    + `Defaults	iolog_dir="/var/log/sudo/"`</br>sudo 로그를 저장할 디렉터리의 경로를 설정한다.
    + `Defaults	passwd_tries=3`</br>암호 입력 횟수 지정
    + `Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/snap/bin"`</br>sudo명령을 실행할 때 사용하는 가상셀의 PATH를 제한