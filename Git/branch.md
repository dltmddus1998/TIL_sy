# 브랜치 관리와 고급기능

## Git Branch

여러 개발자가 동시에 서로 다른 작업을 할 수 있게 만들어 주는 기능이 바로 깃의 **브랜치(Branch)** 이다.

**협업**을 하는 상황 뿐만 아니라, **기존의 소스 코드를 해치지 않으면서 다른 작업을 시도해보고 싶을 때**에도 이 브랜치 기능을 활용할 수 있다. feature 브랜치를 만들어서 원하는 기능을 구현할 수 있다. 그리고 feature 브랜치에서 완성한 코드를 기존 소스 코드에 반영해도 문제가 없다면, 그 때 병합(merge)할 수 있다.

### 브랜치란?

독립적으로 어떤 작업을 진행하기 위한 개념.

개발을 하다 보면 한 페이지 안의 여러 기능을 따로 구현하기 위해, 코드를 여러 개로 복사해야 하는 일이 자주 생긴다.

브랜치 기능을 활용하면, 코드를 통째로 복사한 후 원래 코드가 변경될 우려 없이 독립적으로 개발할 수 있다.

즉, **각각의 브랜치는 다른 브랜치의 영향을 받지 않으므로 여러 작업을 동시에 진행할 수 있다**.

**브랜치 기능의 장점**

- 한 소스코드에서 **동시에** 다양한 작업 가능
- 소스코드의 한 시점과 동일한 상태를 만들고, 브랜치를 넘나들며 작업 수행 가능
- 각각의 브랜치에서 생긴 변화가 다른 브랜치에 영향을 주지 않고 **독립적**으로 코딩 진행 가능

ex) hotfix, release, develop, feature ...

![image](https://user-images.githubusercontent.com/73332608/133124871-f40770f4-85db-400d-a422-8e77a1e08493.png)

여러 브랜치를 만든 레파지토리의 Git Graph (출처: Git Beginner's Guide for Dummies)

**master** 또는 **main** 이라는 이름을 가진 통합 브랜치에 뿌리를 두고, 각각의 브랜치가 갈라져 나오고 있는 모습이다.

이렇게 나누어진 브랜치에서 각자 **독립적인** 작업 영역(repository: 저장소) 안에서 **마음대로 소스 코드를 변경**할 수 있다. 분리된 작업 영역(브랜치)에서 변경된 내용들은 다른 브랜치와 병합(merge)해서 다시 새로운 하나의 브랜치로 모을 수 있다.

브랜치를 사용하면 아래와 같이 동시에 여러 작업을 진행할 때의 작업 흐름을 한눈에 파악할 수 있다.

![image](https://user-images.githubusercontent.com/73332608/133124959-65523d32-5986-4984-be2c-7195663c152f.png)

동시에 여러 작업을 진행하는 Git Graph

여러 명이 동시에 작업을 할 때 다른 사람의 작업에 영향을 끼치지 않게 하기 위해 먼저 통합 브랜치(master || main)에서 **자신의 작업 전용 브랜치**를 만든다. 그리고 각자의 브랜치에서 맡은 영역에 대한 작업을 진행한 후, 작업이 끝난 브랜치는 통합 브랜치에 병합(merge)하여 변경 사항을 적용한다.

이를 통해 다른 브랜치의 작업에 영향을 받지 않고 **독립적으로** 특정 작업을 수행하고 그 결과를 하나로 모아 나가게 된다. 이렇게 작업을 진행하면 브랜치로 그 작업의 **내용들이 모두 기록**돼서 문제가 발생 시 원인이 되는 작업을 찾아 그에 대한 대책을 세우기 쉽다.

### 브랜치 종류

- **통합 브랜치 (Integration Branch)**
  - 배포될 소스 코드가 기록되는 브랜치
  - Github Repository를 생성하면 기본적으로 main 브랜치가 생성된다. (기존의 경우 master인 경우도 많음)
  - 해당 프로젝트의 모든 기능이 정상적으로 작동하는 상태의 소스코드가 담겨 있따.
- **피처 브랜치 (Feature Branch)**
  - 기능 추가, 버그 수정과 같이 단위 작업을 위한 브랜치
  - 통합 브랜치로부터 만들어내며, 피처 브랜치에서 하나의 작업이 완료되면 다시 통합 브랜치에 병합되는 방식으로 진행된다.
  - (= 토픽 브랜치)

### 브랜치 명령어 모음 & git work flow

**새로운 브랜치 생성**

- `$ git branch 새로운 브랜치 이름`

**새로운 브랜치 생성 후 해당 브랜치로 전환**

- `$ git switch -c 새로운 브랜치 이름`
- `$ git checkout -b 새로운 브랜치 이름`

기본적으로 개발을 진행할 때 main 브랜치가 아닌 dev 브랜치를 하나 만들어서 작업을 하는 경우가 많다. dev 브랜치를 하나 만들어서 해당 브랜치로 이동해보면 다음과 같다. (dev 브랜치 생성 후 dev 브랜치로 이동)

`$ git checkout -b dev`

`$ git switch -c dev`

![image](https://user-images.githubusercontent.com/73332608/133125034-2eb61fc2-bb63-4919-aa92-5f666d4131fb.png)

여기서 `HEAD` 는 현재 위치의 커밋을 가리킨다. (현재 작업중인 커밋)

이 때 Remote Repository에도 생성한 브랜치를 반영하려면 `git push origin dev` 명령어를 입력해야 한다.

( 앞서 있었을 Fork & Clone 과정 : Project remote repository -fork→ Origin remote repository -clone→ Origin local repository )

이후 팀 프로젝트에 들어가기 앞서 회의를 통해 하나의 기능을 구현할 때 'feature/기능이름' 이라는 브랜치를 만들어서 작업하기로 정했다. 로그인 기능 구현을 위해 feature/login 이라는 브랜치를 생성해 보면 다음과 같다.

`$ git checkout -b feature/login`

`$ git switch -c feature/login`

![image](https://user-images.githubusercontent.com/73332608/133125069-0b4a83e5-9d62-45d1-b216-87459f1db547.png)

여기에 소셜 로그인(oauth) 기능을 추가해보자. 위험부담을 줄이기 위해 만들어 놓은 코드를 손대지 않기 위해 새로운 브랜치를 하나 더 만들면 다음과 같다.

`$ git checkout -b feature/login-oauth`

`$ git switch -c feature/login-oauth`

![image](https://user-images.githubusercontent.com/73332608/133125107-9f387bc7-5079-4abd-896f-39c480973576.png)

소셜 로그인 기능까지 구현이 완료되었다. 이 feature/login-oauth에 있는 코드를 feature/login 브랜치로 병합해보자. 이 내용은 아래 **브랜치 병합** 에서 다룰 것이다.

**브랜치 목록 확인**

- `$ git branch`
- 알파벳 q를 통해 종료 가능

**브랜치 목록과 각 브랜치의 최근 커밋 확인**

- `$ git branch -v`

**브랜치 삭제**

- `$ git branch -d 삭제할 브랜치 이름`
- `$ git branch -D` : 병합하지 않은 브랜치를 강제 삭제하는 방법

**브랜치 전환**

- `$ git switch 브랜치 이름`
- `$ git checkout 브랜치 이름`

**브랜치 병합**

- master 브랜치로 dev브랜치를 병합할 때 ( master ← dev)

1. `$ git checkout master`
2. `$ git merge dev`

feature/login-oauth → feature/login 병합

1. `git checkout(switch) feature/login` 명령어를 통해 feature/login 브랜치로 이동
   1. 병합하기 위해서는 먼저 병합이 될 브랜치로 이동해야 한다.
2. `$ git merge feature/login-oauth` 명령어를 통해 병합 진행
   1. feature/login-oauth 브랜치가 병합되기 전 feature/login 브랜치에 추가적인 커밋이 없으므로, 브랜치가 분기될 필요가 없다. 따라서 자동적으로 **fast-forward** 방식으로 병합이 이뤄진다.
      1. **fast-forward : 별도의 커밋을 생성하지 않고 feature/login 브랜치가 바리키는 커밋을 feature/login-oauth가 생성한 커밋으로 바꾸는 작업**

![image](https://user-images.githubusercontent.com/73332608/133125176-ea0b43ce-2166-4663-8dff-bf5b55e4cf49.png)

만약 feature/login 브랜치에 별도의 커밋이 있었다면, fast-forward가 아닌 merge commit 방식으로 병합됐을 것이다. 이는 각 브랜치가 줄기처럼 분기한 후, 병합의 모양새를 가진다.

![image](https://user-images.githubusercontent.com/73332608/133125202-92dbf21e-5b44-4eb9-b0ff-28938674da15.png)

\*\*merge vs. rebase
rebase의 원리 = fast-forward

merge : 변경 내용의 이력이 모두 그대로 남기 때문에 이력이 복잡해진다.
rebase : 말 그대로 branch base를 이동시킨다는 뜻으로, 머지처럼 브랜치 통합을 목적으로 하지만 ,특정 시점으로 브랜치가 가리키는 곳을 변경하는 기능을 한다.

아래 그림과 같이 feature/login 브랜치에서 `$ git rebase main feature/login` 명령어를 입력하면 main의 가장 최신 커밋으로 브랜치가 가리키는 곳이 변경된다. (main의 다른 커밋에서 충돌이 없을 경우)\*\*

![image](https://user-images.githubusercontent.com/73332608/133125246-38f85eb1-f9bd-4e1f-b760-89b666fb061b.png)

로컬의 작업한 내용을 Remote Repository에 업로드하기 위해서는 `$ git push origin feature/login` 을 입력한다.

+) feature/login 브랜치의 변경 사항을 다른 팀원들과 함께 코드 리뷰를 하고 dev 브랜치에 적용하려면 Github의 Pull Request 기능을 활용해 dev 브랜치로의 반영을 요청할 수 있다. 리뷰가 끝난 코드는 브라우저에서도 dev 브랜치로 병합할 수 있다.

**로그에 모든 브랜치를 그래프로 표현**

- `$ git log --branches --graph --decorate`

아직 commit 하지 않은 작업을 스택에 임시로 저장

- `$ git stash`

+) git 명령어 심화

[Dangit, Git!?!](https://dangitgit.com/ko)

## 전체 흐름 Git Workflow

![image](https://user-images.githubusercontent.com/73332608/133125299-851027fb-8ef3-4895-bb73-afe86aeb0dc6.png)

1. Local에서 새로운 브랜치를 생성하고 작업이 끝나면 Remote Repository로 Push한다.
2. Project Upstream Repository에 반영(merge)될 수 있도록 Pull Request 한다.
   1. 만약 작업 중 Remote Upstream에 업데이트 발생시 Local로 Pull받아주어야 한다.
