# Web Server
## subject
+ lighttpd, MariaDB, PHP와 함께 작동하는 WordPress 웹 사이트를 설정한다.
## Web Server
#### Web Server?
+ **web server는 소프트웨어와 하트웨어로 구분된다.**
    + 하드웨어 : Web서버가 설치되어 있는 컴퓨터
    + 소프트웨어 : 웹 브라우저 클라이언트로부터 HTTP요청을 받아 정적인 컨텐츠(.html/.jpg)를 제공하는 컴퓨터 프로그램

#### CGI
+ **web server는 동적인 컨텐츠를 바로 제공할 수 없기 때문에 외부 프로그램(php,python)으로 부터 도움을 받는데 이 때 web server와 외부 프로그램과의 연계법이 CGI이다.**
****
## lighttpd
+ **이름에서도 알 수 있듯 경령화 된 웹서버 프로그램 이다.**
+ **적은 자원을 사용하여 높은 성능을 내는 웹서버로 고안되어 메모리를 적게 사용한다.**
+ **기본적으로 80번 포트를 통해 연결한다.**
****
## WordPress
+ **웹사이트/홈페이지/블로그 등을 제작할 수 있는 CMS(Content Management System)으로 오픝소스 프로그램으로 누구나 개발 및 수정에 참여 가능하다.**
****
## web server설정
#### 포트 허용 및 포트 포워딩
```
ufw allow 80
```
+ **lighttpd와 통신하기 위해 80번 포트를 열고 포트 포워딩 룰을 추가한다.**

#### lighttpd 와 php-fpm 설치 및 설정
```
apt install lighttpd
apt install php7.4-fpm
```
+ **lightpd와 php-fpm을 설치한다.**
    + fpm : fastCGI process manager
```
vim /etc/php/7.4/fpm/php.ini
```
+ **php.ini파일의 cgi.fix_pathinfo = 1의 주석을 해제한다.**
```
server.modules += (
	"mod_dirlisting",
	"mod_staticfile",
	"mod_fastcgi",
)

fastcgi.server = ( ".php" => (( 
					 "socket" => "/var/run/php/php7.4-fpm.sock" 
				 )))
```
+ **`vim /etc/lighttpd/lighttpd.conf`lighttpd의 기본 설정파일을 열어 마지막 부분을 수정한다.**
```
#!php
<?php phpinfo(); ?>
```
+ **`vim /var/www/html/info.php` php파일 작성**
+ **\[로컬ip\]:\[로컬포트\]/info.php 로 접속해 연결 확인**

#### mariadb 설치 및 데이터베이스 생성
```
apt install mariadb-server
apt install php7.4-mysql
```
+ **mariadb-server와 php-mysql설치**
```
mysql -u root -p
```
+ **root계정으로 mariadb접속**
```
CREATE DATABASE wordpassdb;
// 데이터베이스 생성
CREATE USER 'jaewchoi'@'localhost' IDENTIFIED BY '123';
// 사용자 생성
GRANT ALL ON wordpassdb.* TO 'jaewchoi'@'localhost' IDENTIFIED BY '123' WITH GRANT OPTION;
// 사용자에게 데이터베이스 권한 부여
FLUSH PRIVILEGES;
// 설정 저장
EXIT;
// 나가기
```
+ **데이터 베이스 생성과 계정 생성**
```
wget -O /tmp/wordpress.tar.gz "http://wordpress.org/latest.tar.gz"
```
+ **웹에서 검색하는 wget명령어로 wordpress압축파일 설치**