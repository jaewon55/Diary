# Web Server
## subject
+ lighttpd, MariaDB, PHP와 함께 작동하는 WordPress 웹 사이트를 설정한다.
## Web Server
### Web Server?
+ **web server는 소프트웨어와 하트웨어로 구분된다.**
    + 하드웨어 : Web서버가 설치되어 있는 컴퓨터
    + 소프트웨어 : 웹 브라우저 클라이언트로부터 HTTP요청을 받아 정적인 컨텐츠(.html/.jpg)를 제공하는 컴퓨터 프로그램

### CGI
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
### 포트 허용 및 포트 포워딩
```
ufw allow 80
```
+ **lighttpd와 통신하기 위해 80번 포트를 열고 포트 포워딩 룰을 추가한다.**

### lighttpd 와 php-fpm 설치 및 설정
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
```
systemctl restart lighttpd
```
+ **lighttpd 재시작 후 `\[로컬ip\]:\[로컬포트\]/info.php` 로 접속해 연결 확인**

### mariadb 설치 및 데이터베이스 생성
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

### WordPress설치 및 데이터베이스 지정
```
wget -O /tmp/wordpress.tar.gz "http://wordpress.org/latest.tar.gz"
```
+ **웹에서 검색하는 wget명령어로 wordpress압축파일 설치**
```
tar -xzf /tmp/wordpress.tar.gz -C /var/www/html
```
+ **설치한 압축파일을 lighttpd서버가 데이터를 읽는 디렉터리에 푼다.**
+ **`\[로컬ip\]:\[로컬포트\]/wordpress` 로 접속해 연결 확인**
```
// ** Database settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpassdb' );

/** Database username */
define( 'DB_USER', 'jaewchoi' );

/** Database password */
define( 'DB_PASSWORD', '123' );

/** Database hostname */
define( 'DB_HOST', 'localhost' );

/** Database charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The database collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );
```
+ **`vim /var/www/html/wordpress/wp-config-sample.php` Wordpress설정파일을 열어 데이터베이스 설정**
```
/**#@+
 * Authentication unique keys and salts.
 *
 * Change these to different unique phrases! You can generate these using
 * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
 *
 * You can change these at any point in time to invalidate all existing cookies.
 * This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );
```
+ **[secret-key](https://api.wordpress.org/secret-key/1.1/salt/) <-사이트에 접속해서 secret-key수정**
	+ secret-key는 접속할 때마다 변경된다.
```
mv /var/www/html/wordpress/wp-config-sample.php /var/www/html/wordpress/wp-config.php
```
+ **`wp-config-sample.php`파일을 `wp-config.php`로 파일명을 바꾼다.**
+ **\[로컬ip\]:\[로컬포트\]/wordpress 로 접속해 연결 확인**
****

## caddy로 WordPress구성하기

### caddy설치
+ **[공식사이트](https://caddyserver.com/docs/install#debian-ubuntu-raspbian)**
```
apt install curl
```
+ **설치에 필요한 curl명령어를 설치**
	+ curl : http 메시지를 쉘상에서 요청하여 결과를 확인하는 명령어, http를 이용하여 경로의 데이터를 가져온다.
```
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/testing/gpg.key' | sudo tee /etc/apt/trusted.gpg.d/caddy-testing.asc
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/testing/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-testing.list
sudo apt update
sudo apt install caddy
```
+ **caddy(testing releases)설치**
```
:90 {
	# Set this path to your site's directory.
	root * /usr/share/caddy

	# Enable the static file server.
	file_server

	# Another common task is to set up a reverse proxy:
	# reverse_proxy localhost:8080

	# Or serve a PHP site through php-fpm:
	# php_fastcgi localhost:9000
	php_fastcgi unix//run/php/php7.4-fpm.sock
	file_server
}
```
+ **`vim /etc/caddy/Caddyfile` caddy 설정파일 수정**
```
#!php
<?php phpinfo(); ?>
```
+ **`vim /usr/share/caddy/info.php` info.php파일 생성**
```
ufw allow 90
```
+ **ufw로 90번 포트를 허용하고 포트 포워딩으로 연결**
```
systemctl restart caddy
```
+ **caddy 재시작 후 `\[로컬ip\]:\[로컬포트\]/info.php`로 접속해 php-fpm연결 확인**
+ **이후 lighttpd처럼 WordPress연결**