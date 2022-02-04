---
layout: single
title: "협업을 위한 github issue 작성방법"

toc: true
toc_label: INDEX
toc_sticky: false

categories:
  - Github
---

# Introduction

[[1]]을 참고하여 협업을 위한 github issue 작성 방법을 정리하고자 한다.

# 순서

* issue 생성
* 처리하고자 하는 이슈의 번호 확인
* `git branch feature/8 // (처리기능/이슈번호)`
* `git checkout feature/8`
* `git add .`
* `git commit -m "Fix: ~~~로 변경 close #8"`
* `git push -u origin feature/8`
* `git checkout master`
* `git merge feature/8`

# 예시

[[2]]와 같이 깃허브에서 issue를 생성할 수 있다.

Issue에는 누가 해결할 것인지 (Assignees) 또는 issue의 특성은 무엇인지 (Labels) 등을 설정하여 추가적인 정보를 줄 수도 있다.

또한 [[3]] 처럼 commit message에 #이슈번호를 붙이면 자동적으로 이 issue와 관련된 commit로 [[2]]에 나타나게 된다.

# 결론

필자는 가끔 수정해야할 버그를 까먹고 나중에서야 발견하는 경향이 있다.

일이 바쁘다보면 나중에 수정해야겠다고 생각했다가 잊어먹고 안하는 경우에 그렇다.

그러나 본 글에서처럼 issue를 통해 기록해두면 까먹을 일도 없고 어떤 식으로 진행되어왔는지 한 눈에 볼 수 있어 좋은 것 같다.


[1]: https://velog.io/@junh0328/%ED%98%91%EC%97%85%EC%9D%84-%EC%9C%84%ED%95%9C-%EA%B9%83%ED%97%88%EB%B8%8C-%EC%9D%B4%EC%8A%88-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0
[2]: https://github.com/jskim-research/CodingTest/issues/1
[3]: https://github.com/jskim-research/CodingTest/commit/08046f46e71c22d13b5e7e09ddc57973b3f4420a