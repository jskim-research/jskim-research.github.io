---
layout: single
title: "Github 명령어 정리"

toc: true
toc_label: INDEX
toc_sticky: false

categories:
  - Github
---

# Introduction
실제와 같이 순차적으로 Repository 생성 => 환경 설정 => 파일 처리 명령어 순으로 정리함.

이외의 것들은 Etc에 정리.

# Github repository 생성
순서
* github.com에서 new repository를 통해 remote repository를 생성한다.
* remote repository를 복사하거나 기존 폴더에서 local repository를 생성한다.
  * `git clone [원격 저장소 주소]`
  * 기존 폴더에서 `git init`

# 환경 설정
## local repository와 remote repository 연결
```
git remote add origin [remote repository 주소] or git remote add origin [branch 이름]
git remote -v // remote repository와 연결 확인
```

## git user 설정
```
git config --global user.name "[사용자명]"
git config --global user.email "[사용자이메일명]"
git status // git 상태 확인
```

# 파일 처리 명령어
## Commit
```
git add [파일명.확장자명] // [파일명.확장자명]을 스테이지에 올림
git commit -m "[메시지명]" // -m 없이 하는 것을 추천
git commit -a -m "[메시지명]" // 스테이징과 커밋을 동시에 진행 (add + commit)
git log // 커밋 내역 확인
git log --pretty=oneline // 커밋 내역 한 줄로 표기
git show [커밋 id] // 특정 커밋 내역 확인
git diff // 최근 버전과 작업 파일간 차이를 출력
git diff [이전 커밋 id] [이후 커밋 id]
git checkout [커밋 해시] // 지정한 커밋 해시로 이동
git reset HEAD^ // 현재 HEAD의 이전 커밋으로 되돌리기
git reset HEAD~n // 현재로부터 n번째 이전 커밋으로 되돌리기
git reset --soft [커밋ID] // head만 바뀜
git reset --mixed [커밋ID] // staging도 그 때로 바뀜
git reset --hard [커밋ID] // working 디렉토리 / staging 모두 그 때로 바뀜
git revert [커밋 해시] // 지정한 커밋 해시의 변경 이력을 취소
```

## Push
```
git push -u [저장소명] [브랜치명] // 보통 저장소명을 origin으로 한다. => -u를 붙여서 다음번엔 git push 만으로 가능하게 함
```

만약 access deny가 발생한다면 [[2]]을 확인하자.

그리고 Push 하기 전에 .gitignore 파일을 잘 추가했는지 확인하자. [[3]]

# Etc
## Github message convention [[1]]

커밋 메세지 7가지 규칙
1. 제목과 본문을 빈 행으로 구분합니다.
2. 제목을 50글자 이내로 제한합니다.
3. 제목의 첫 글자는 대문자로 작성합니다.
4. 제목의 끝에는 마침표를 넣지 않습니다.
5. 제목은 명령문으로! 과거형을 사용하지 않습니다.
6. 본문의 각 행은 72글자 내로 제한합니다.
7. 어떻게 보다는 무엇과 왜를 설명합니다.

커밋 메세지 구조
```
<type>(<scope>): <subject> 
<BLANK LINE> 
<body>
<BLANK LINE>
<footer>

<type>:
feat : 새로운 기능에 대한 커밋 
fix : build 빌드 관련 파일 수정에 대한 커밋 
build : 빌드 관련 파일 수정에 대한 커밋 
chore : 그 외 자잘한 수정에 대한 커밋(기타 변경) 
ci : CI 관련 설정 수정에 대한 커밋 
docs : 문서 수정에 대한 커밋 
style : 코드 스타일 혹은 포맷 등에 관한 커밋 
refactor : 코드 리팩토링에 대한 커밋 
test : 테스트 코드 수정에 대한 커밋

예시
Feat: 관심지역 알림 ON/OFF 기능 추가(#123) 
시군구의 알림을 각각 ON/OFF 할 수 있도록 기능을 추가함 
- opnion0055: 구분 코드 
해결: close #123

```

[1]: https://xtring-dev.tistory.com/entry/Git-규칙적인-Commit-메세지로-개발팀-협업하기-👾
[2]: https://hyeo-noo.tistory.com/184
[3]: https://eunjin3786.tistory.com/302
