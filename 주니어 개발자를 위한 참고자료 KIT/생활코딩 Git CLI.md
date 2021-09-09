# Git CLI
## 버전관리
+ 버전관리의 시작
	+ 저장소(repository)생성
		+ `git init .` : git을 초기화 한다. 현재 디렉터리에 git저장소를 생성한다.
		+ `.git` : git을 초기화 하면 생성되는 저장소(repository)
   
+ 버전의 생성
	+ git의 버전은 파일의 위치에 따라 `Working tree` -> `Staging Area` -> `Repository` 3단계에 걸쳐 생성된다.
	+ `Workingg tree` : git을 초기화한 디렉터리에서 파일을 생성 또는 수정하면 해당 파일은 Working tree에 위치하게 된다.
	+ `Staging Area` : `Working tree`에 위치한 파일중 버전으로 생성하고 싶은 파일들을 골라 명령어를 통해 `Staging Area`로 위치시켜 버전생성 준비를 한다.
	+ `Repository` : `Staging Area`에 위치한 파일을 명령어를 통해 버전을 생성하면 `Repository`에 버전으로 저장된다.
	+ 버전 생성 명령어
		+ `git status` : git의 상태 - `Working tree` 또는 `Staging Area`에 위치하고 있는 파일을 보여준다.
		+ `git add` : `Working tree`에 있는 파일을 `Staging Area`로 옮겨 버전 생성을 준비한다.
		+ `git commit -m "massage"` : `Staging Area`에 위치한 파일을 버전으로 생성하고 "massage"를 남긴다.
		+ `git log` : git의 역사 `Repository`에 생성된 버전의 생성자, 생성시간, massage 등을 보여준다.
		+ `git log --stat` : `Repository`에 생성된 버전에 어떤 파일이 수정 또는 생성되었는지 보여준다.