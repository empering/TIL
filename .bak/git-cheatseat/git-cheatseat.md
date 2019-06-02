# git-cheatseat

자주 사용하는 git 커맨드 모음.



## git version upgrade

```shell
$ git --version
$ brew upgrade git
$ which git
# 경로가 /usr/bin/git인 경우,  Xcode와 함께 설치 된 Git을 사용. 따라서 참조 경로 변경
# 홈디렉토리에서 .bashrc 또는 .bash_profile

$ sudo vim .bash_profile
# 아래 path를 추가
# export PATH="/usr/local/bin:/usr/bin/git:/usr/bin:/usr/local/sbin:$PATH"

$ source .bash_profile
```



## push- default 브랜치 지정

- 현재 설정을 확인
  - nothing : Do not push anything
  - matching : Push all matching branches (default)
  - tracking : Push the current branch to whatever it is tracking
  - current : Push the current branch

```shell
$ git config --global push.default
```

- 현재 브랜치를 기본 브랜치로 지정

```shell
$ git config --global push.default current
```



## branch

```shell
$ git branch --list | -a | --remotes

#원격브랜치목록 최신화
$ git fetch
$ git checkout --track -b video-lessons origin/video-lessons 

#새로운 로컬브랜치 생성
$ git checkout -b newbranch

#현재 저장소에 연결된 원격 저장소
$ git remote -v

#로컬브랜치삭제
$ git branch --delete localbrnachname

#원격브랜치삭제
$ git push --delete origin remotebranchname

#원격저장소의 업스트림 브랜치 지정
$ git push --set-upstream origin master
```



## stage

```shell
#특정 디렉토리의 파일 모두 스테이징
$ git add <directory_name>/*

#특정 확장자 파일 모두 스테이징
$ git add *.java

#git에 기록된 적 있는 파일 중 업데이트 된 파일을 스테이징
$ git add --update

#스테이지에서 특정 파일 제외
$ git reset HEAD filename.java

#기존 커밋 업데이트
$ git add -all | git commit --amend
```



## reset

```shell
#특정 파일 수정사항 폐기
$ git checkout --filename.java

#저장되지 않은 모든 수정사항 폐기
$ git reset --hard

#특정 커밋 제외
$ git reset commitid

#저장되지 않은 모든 파일 삭제
$ git clean -fd

# 특정 커밋 되돌리는 커밋 생성
$ git revert commitid 
```



## stash

```shell
$ git stash save "Message"
$ git stash pop
$ git stash apply stash@{0}
$ git stash drop stash@{0}
$ git pull --rebase --autostash // 작업중인던 내용을 stash 하고 pull --rebase 진행 후 자동 되돌림
```



## log

```shell
# 커밋 히스토리 포맷팅
$ git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit

# 두 브랜치 사이의 다른 커밋 조회
$ git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset' --abbrev-commit --date=relative upstream/develop..origin/develop
```

