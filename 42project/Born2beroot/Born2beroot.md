# Born2beroot

+ 최신 Debian 또는 CentOS를 선택
    + CentOS - SELinux
    + Debian - AppArmor
+ LVM을 사용해 암호화된 파티션을 2 개 이상 생성해야 한다.
+ aptitude 와 apt의 차이?
+ SELinux 와 AppArmor는 무엇인가?
+ SSH서버는 4242 포트에서만 동작해야한다. 보안상 이유로 SSH를 root로 사용해 접속해선 안된다.
    + SSH는 defense에서 새로운 계정을 성정함으로 테스트 된다. 이것이 어떻게 작동하는지 이해해야 함
+ UFW 방화벽으로 운영 체제를 구성하여 포트 4242만 열어 두어야 한다.
    + virtual machine을 시작할 때 방화벽이 활성화 상태여야 한다. CentOS의 경우 기본 방화벽 대신 UFW를 사용해야 한다.
+ virtual machine의 호스트 이름은 로그인에서 42로 끝나야 한다. 평가중 호스트 이름을 수정해야 한다.
+ 강력한 암호정책을 적용해야 한다.
    + 암호는 30일 마다 만료되어야 한다.
    + 암호 수정 전까지 허용되는 최소 일수는 2일로 한다.
    + 암호 만료되기 7일 전에 경고 메세지를 받아야 한다.
    + 암호는 10자 이상이고 대문자 및 숫자를 포함해야한다. 또한 3개 이상 동일한 문자가 연속 되어서는 안된다.
    + 비밀번호에는 사용자 이름이 포함되어선 안된다.
    + 이전 비밀번호와 최소 7자 이상 달라야한다.(root의 경우 제외)
    + 구성 파일을 설정한 후 root를 포함해 virtual machine에 있는 계정의 암호를 모두 변경해야 한다.
+ 규칙에 따라 sudo를 설치하고 설정해야 한다.
    + sudo 인증시 비밀번호는 3회로 제한한다.
    + 잘못된 비밀번호로 인한 에러 발생시 사용자 정의 메세지가 표시되어야 한다.
    + sudo를 사용하는 동작은 입력과 출력 모두 로그 파일에 저장되어야 한다.(로그 파일은 /var/log/sudo/폴더에 저장해야 함)
    + 보안상의 이유로 TTY모드를 활성화해야 한다.
    + 보안상의 이유로 sudo가 사용할 수 있는 경로는 제한되어야 한다.
        + ex)/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
+ root사용자 외에도 인트라ID를 사용하는 사용자가 있어야 한다.
    + 이 사용자는 user42 및 sudo 그룹에 속해 있어야 한다.
    + defense에 그룹을 할당한 새로운 사용자를 만들어야 한다.
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

    https://nostressdev.tistory.com/12
    + 데비안?
    + LVM?(logical volume manager) 디스크나 대용량 스토리지 장치를 유연하고 확장 가능하게 다룰 수 있는 기술이며 이를 커널에 구현한 기능
        + 파티션?
        + volume group? https://tech.cloud.nongshim.co.kr/2018/11/23/lvmlogical-volume-manager-1-%EA%B0%9C%EB%85%90/
    + GRUB boot loader
    + aptitude 와 apt의 차이?
    + SELinux 와 AppArmor는 무엇인가?

    파일 시스템 : https://mamu2830.blogspot.com/2019/10/chs-lba.html
    하드 디스크 : https://mamu2830.blogspot.com/2019/10/blog-post_14.html
    리눅스 마운트 : https://mamu2830.blogspot.com/2019/11/fdisk-df-etcfstab-blkid.html
    lvm : https://mamu2830.blogspot.com/2019/12/lvmpv-vg-lv-pe-lvm.html
    VDI, VHD, VMDK : https://apophis0.tistory.com/48
    + sudo 설정 (https://velog.io/@appti/born2beroot-sudo-%EC%84%A4%EC%A0%95)
    ```
    Defaults	authfail_message="Authentication attempt failed custom."
    Defaults	badpass_message="Wrong password custom."
    Defaults	log_input
    Defaults	log_output
    Defaults	requiretty
    Defaults	iolog_dir="/var/log/sudo/"
    Defaults	passwd_tries=3
    ```
    + lvm만들기 (https://www.manualfactory.net/14379)