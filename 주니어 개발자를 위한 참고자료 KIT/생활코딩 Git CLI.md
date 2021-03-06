# Git CLI
## 버전관리
+ 버전관리의 시작
	+ 저장소(repository)생성
		+ `git init .` : git을 초기화 한다. 현재 디렉터리에 git저장소를 생성한다.
		+ `.git` : git을 초기화 하면 생성되는 저장소(repository)
   
+ 버전의 생성
	+ git의 버전은 파일의 위치에 따라 `Working tree` -> `Staging Area` -> `Repository` 3단계에 걸쳐 생성된다.
	+ `Working tree` : git을 초기화한 디렉터리에서 파일을 생성 또는 수정하면 해당 파일은 `Working tree`에 위치하게 된다.
	+ `Staging Area` : `Working tree`에 위치한 파일중 버전으로 생성하고 싶은 파일들을 골라 명령어를 통해 `Staging Area`로 위치시켜 버전생성 준비를 한다.
	+ `Repository` : `Staging Area`에 위치한 파일을 명령어를 통해 버전을 생성하면 `Repository`에 버전으로 저장된다.
	+ 버전 생성 명령어
		+ `git status` : git의 상태 - `Working tree` 또는 `Staging Area`에 위치하고 있는 파일을 보여준다.
		+ `git add` : `Working tree`에 있는 파일을 `Staging Area`로 옮겨 버전 생성을 준비한다.
		+ `git commit -m "massage"` : `Staging Area`에 위치한 파일을 버전으로 생성하고 "massage"를 남긴다.
		+ `git commit --amend` : 이전에 commit한 massage를 수정할 수 있다.
		+ `git log` : git의 역사 `Repository`에 생성된 버전의 생성자, 생성시간, massage 등을 보여준다.
		+ `git log --stat` : `Repository`에 생성된 버전에 어떤 파일이 수정 또는 생성되었는지 보여준다.
   
+ 버전간 차이점 비교
	+ `git diff` : `Working tree`에 있는 파일을 `Repository`에 생성된 마지막 버전과 비교해서 차이점(수정부분)을 보여준다.
	+ `git log -p` : `Repository`에 저장된 버전들의 수정(생성)사항을 보여준다.
   
+ checkout과 시간여행
	+ `git checkout 버전아이디` : 해당 버전 아이디로 `Working tree`를 변경시킨다.
	+ `git checkout master` : 가장 최신버전(master)으로 변경된다.

+ 버전 삭제
	+ `git reset --hard 버전아이디` : 해당 버전 아이디로 reset하면서 현재 작업하던 파일도 지운다.
	+ `git reset --soft 버전아이디` : 해당 버전 아이디로 reset하면서 현재 작업하는 파일은 살려둔다.

+ 버전 되돌리기
	+ `git revert 버전아이디` : 해당 버전을 그대로 두면서 해당 버전 아이디로 수정했던 내용을 이전 버전으로 되돌린다. 이때 충돌이 일어날 수 있기 때문에 역순으로 진행해야 한다.

## Branch & Conflict
+ branch : 나무에서 가지가 뻗어나가듯 버전이 여러 갈래로 나눠진다.
	+ `git branch` : 현재 branch의 목록을 보여주고 현재 위치하고 있는 branch는 앞에 *이 붙는다.
	+ `git branch branch명` : 새로운 branch를 만든다.
	+ `git checkout branch명` : 현재 위치를 해당 branch로 변경한다.
	+ `git log --all --graph --oneline` : 모든 버전 목록을 축약해서 한눈에 보기 쉽게 그래프로 나타낸다.
+ merge : 여러갈래로 나누어진 branch를 병합하는 기능
	+ base : 합쳐진 두 branch의 공통된 조상
	+ merge commit : 두 branch가 합쳐진 commit
	+ 만약 현재 master 브랜치와 o2브랜치가 있고 o2브랜치를 master브랜치에 병합하고 싶다면
		1. master 브랜치로 위치를 이동
		2. `git merge o2` 명령어를 통해 병합
+ conflict : 두 브랜치를 병합하는데 같은 파일의 같은 부분이 수정되었을 때 충돌이 일어난다. 이때 직접 이 부분을 수정하고 add, commit 해야 한다.
	+ 충돌이 일어나면 git이 수정해야하는 부분을 표시해주고 해당 파일을 `working tree`에 위치시킨다.
	+ 해당 파일에 들어가 충돌이 일어나는 부분을 수정하고 add, commit 하면 branch가 정상적으로 병합된다.