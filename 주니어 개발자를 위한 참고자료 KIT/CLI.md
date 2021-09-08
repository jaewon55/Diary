# CLI(Command Line Interface)
+ 터미널에서 명령어를 통해서 컴퓨터와 상호작용 하는 방식
   
## CLI 기본 명령어
+ pwd(print workking directory)
	+ 현재 위치하고 있는 디렉터리를 출력한다.
   
+ ls(list)
	+ 현재 위치하고 있는 디렉터리의 목록을 출력한다.
   
+ cd(change directory)
	+ 다른 디렉터리로 이동한다.
	+ 상대경로 : 현재 위치하고 있는 디렉터리를 기준으로한 경로설정
	+ 절대경로 : root 디렉터리를 기준으로한 경로설장
	+ `cd ~` 또는 `cd` : home디렉터리로 이동한다.
	+ `cd /` : root 디렉터리로 이동한다.
	+ `cd .` : 현재 디렉터리로 이동한다.
	+ `cd ..` : 현재 디렉터리의 부모 디렉터리로 이동한다.
   
+ mkdir(make directory)
	+ 새로운 디렉터리를 생성한다.
   
+ touch(create a file)
	+ 새로운 파일을 생성한다.
   
+ cat(concatenae)
	+ `cat filename` : 파일의 내용을 출력한다.
	+ `cat >> filename` : 파일에 새로운 내용을 추가한다.
	+ `cat > filename` : 파일의 내용을 수정한다.(이전에 입력한 내용은 삭제)
   
+ mv(move)
	+ 파일(폴더)을 다른 위치로 옮기거나 파일의 이름을 수정한다.
	+ `mv filename dir` : file을 dir폴더로 옮긴다.
	+ `mv filename dir/newname` : file을 dir로 옮기고 새로운 이름으로 바꾼다.
	+ `mv filename newname` : file을 새로운 이름으로 수정한다.
   
+ cp(copy)
	+ 파일(폴더)을 복사한다.
	+ `cp file01 file02` : file01을 복사해 file02를 만든다.
	+ `cp file01 folder01/copyfile` : file01을 복사해 folder01에 copyfile 이름으로 생성한다.
   
+ rm(remove)
	+ 파일(폴더)을 삭제한다. 삭제의 경우 복구가 어렵기 때문에 주의해서 사용해야한다.
	+ `rm file01` : file01을 삭제한다.
	+ `rm -r dir01` : 디렉터리 dir01을 삭제한다. 디렉터리의 경우 `rm -r`을 사용해야 삭제가 가능하다
   
## Permission
<img src = "https://res.cloudinary.com/lwgatsby/f_auto/www/uploads/2019/11/fig_permissions_chmod-command.jpg">
이미지 출처 : https://www.liquidweb.com/kb/what-is-umask-and-how-to-use-it-effectively/
