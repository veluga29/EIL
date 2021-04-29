# Git 기초

## Git 기본 이용 사이클

![a cycle of git](C:\Users\sungyoon\git\EIL\image\git_cycle.JPG)

* Git 저장소 선언하기 (Initialization & Repository)
  * Git 저장소를 흔히 Repo(레포)라고 부르며, Repo는 git으로 관리하는 하나의 메인 저장소를 뜻함
  * 사용자가 변경한 모든 내용 추적 가능
    * 현재 상태, 변경 시점, 변경한 사용자, 설명 텍스트 등
  * 관리할 폴더에서 `git init` 을 통해 선언
  * 주목할 특징
    * Git은 이제 Local에서 모든 것을 저장 및 버전 관리 가능하고 나중에 원격 서버에 올려도  상관 없음
    * Git은 데이터를 추가만 할 수 있음
      * 파일 삭제 == 삭제 기록 추가 (물론 온전한 삭제도 가능하지만, 버전 관리에서는 삭제 기록도 매우 중요)
    * Git은 파일을 추적하지 않음
      *  파일의 내용 단위로 **각 문자와 줄을 추적**
      * 빈 디렉토리는 추적하지 않음

​    

* 파일 추가/수정/삭제

​    

* 변경 사항 선택

  ![File Status](C:\Users\sungyoon\git\EIL\image\change_status.JPG)

  * 파일 상태
    * 파일은 추적 여부로 구분해 tracked, untracked 파일로 나눌 수 있음
    * tracked 파일은 Unmodified(수정 없음), Modified(수정 있음), Staged(저장(커밋)을 위해 준비됨) 상태로 구분됨

  * 스테이징(Staging)을 통해 커밋하고 싶은 파일 선택
    * 스테이징이 필요한 이유?
      * 여러 작업 중 일부분만 커밋해야 할 때
      * 커밋 전 상태를 수정 또는 체크할 때 (안전하게 커밋하기 위함)

​    

* 상태 업데이트
  * 커밋(Commit)을 통해 새로운 버전으로 업로드
  * 커밋을 하면, 각 버전마다 40자리의 숫자 + 알파벳 조합으로 이루어진 해시값이 존재
    * 해시 값은 버전의 주소(Key)
    * 해시값은 내용(파일 구조)을 사용해 생성됨 (파일이 어떤 폴더 밑에 있고, 어떤 내용이다 등의 상태를 40자리로 표현)
    * Commit Hash 값으로 Checkout하면 버전 이동 가능



## Git Branch

* 소프트웨어 버전 넘버링 상식

  ![version numbering](C:\Users\sungyoon\git\EIL\image\ver_numbering.JPG)

  * 보통 세가지 숫자로 표현되며, 위 그림과 같이 각각 의미가 다름
  * 마지막에 알파벳이 들어가는 경우도 있지만, 보통은 모두 숫자로 사용
  * 1.1, 2.1 같이 두 개로 표현된 경우, 1.0.1, 2.0.1을 뜻함

​    

* 브랜치 (Branch)

  ![Branch](C:\Users\sungyoon\git\EIL\image\branch.JPG)

  * 버전 관리시 수많은 오류를 개발자들이 각각 따로 처리하는 상황이 발생하는데, 이를 위해 브랜치 개념이 등장
  * 브랜치는 시간의 흐름의 축

​    

## Git 명령어

* 프로젝트 총 관리자 및 시작자 관점

  * 프로젝트 시작 선언

    * `git init`
      * .git 파일이 생성됨 (처음엔 숨김 상태라 안보임)
      * 모든 버전 관리 정보가 담겨있으므로 조심해야 함 (버전을 초기화하고 싶을 땐, 이 폴더를 통째로 삭제하면 됨)
    * 로컬에서 Git 초기화 진행
    * 시작 버전은 master branch에 기록

  * .gitignore 파일을 생성해 양식(정규표현식)에 맞춰 작성하면, 저장하고 싶지 않은 파일들을 무시할 수 있음

  * README.md 파일 작성

    ![Readme_tip](C:\Users\sungyoon\git\EIL\image\readme_tip.JPG)

    * Repo의 메인 페이지 역할
    * 프로젝트 설명 및 사용방법, 라이센스 등을 기술

  * 유저 이름과 이메일 등록하기 (log에 남기는 용도)

    * `git config --global user.name="[이름]"` : 깃 설정 파일을 내 모든 컴퓨터에 적용, 그 중 이름 정보 입력하겠다.
    * `git config --global user.email="[이메일]"` : 깃 설정 파일을 내 모든 컴퓨터에 적용, 그 중 이메일 정보 입력하겠다.

  * 파일 스테이지로 올리기

    * `git add [file]`
    * [file]을 스테이지로 올림 (폴더나 전체도 가능)
    * 스테이지에서 내리기
      * `git restore --staged [file]`

  * 파일 상태 체크하기 (습관적으로)

    * `git status`
    * `git diff`

  * 스테이지에 있는 내용 커밋

    * `git commit -m "add README.md"`
      * 간단한 설명과 함께 커밋

  * 커밋 기록 살펴보기

    * `git log`

  * 원격 저장소와 연결
    * `git remote add origin [url]`
    * origin이라는 이름으로 [url]과 연결 (origin은 원격 저장소에 관용 표현이므로 변경 가능)
  * 원격 저장소로 올리기
    * `git push origin master`
    * 원격 저장소 master branch에 업데이트
    * 로컬과 원격 저장소가 동기화됨!
  * 버전 이동하기
    * `git log` 로 원하는 버전의 해시값 확인
      * 만일 과거로 돌아와 미래 로그가 안보인다면,  `git log --all`
    * `git checkout [40자리 해시값의 앞 7자리]`
      * 예시
      * `git checkout a703380`
      * `git checkout master`



