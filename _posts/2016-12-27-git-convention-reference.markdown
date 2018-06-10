---
layout: post
title: Git convention Reference
date: 2016-12-27 05:32:47.000000000 +09:00
tags: [git]
---

GIT 커밋, 브랜치를 어떻게 관리하는 것이 좋을지 찾아보고 정리하여 보았다. 실제로 회사에서 적용하여 사용하고 있는데 커밋 히스토리를 보는데, 브랜치 관리 하는데 많은 도움이 되었다.

### 1. [Angularjs Git Commit Message Conventions](http://dogfeet.github.io/articles/2013/angularjs-git-commit-message-conventions.html)

#### 커밋 메세지 포맷

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
---

**메세지 제목**

`<subject>`줄에는 무엇이 달라졌는지 간략하게 기술한다.

`<type>`에 작성 하는 것

- feat (feature)
- fix (bug fix)
- docs (documentation)
- style (formatting, missing semi colons, …)
- refactor
- test (when adding missing tests)
- chore (maintain)

`<scope>`에 넣는 것

여기에는 무엇을 수정했는지 적는다. 예를 들어 $location, $browser, $compile, $rootScope, ngHref, ngClick, ngView라고 적으면 되겠다.

`<subject>`에 허용되는 것.

- 군더더기 없이 간략하게 현재형으로 적는다: 'changed'나 'changes'가 아니라 'change'로 적는다.
- 굳이 첫 문자를 대문자로 적지 않는다.
- 문장 끝에 마침표(.)로 끝내지 않는다.

**메시지 바디**

- <subject>와 마찬가지로 군더더기 없이 간략하게 현재형으로 적는다: 'changed'나 'changes'가 아니라 'change'로 적는다.
- 기존과 무엇이 달라졌고 왜 수정했는지에 대한 내용이 들어가야 한다.
- http://365git.tumblr.com/post/3308646748/writing-git-commit-messages
- http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

**메시지 푸터**

1. 주요 변경 내용(Breaking changes) - 주요 변경 내용은 푸터에 언급한다. 무엇을 고쳤는지, 왜 고쳤는지, 마이그레이션은 어떻게 해야 하는지 설명한다.
2. 이슈 번호 넣기 - 이슈 번호는 푸터에 별도 라인으로 넣는다. 이 이슈 라인은 "Closes"로 시작한다: Closes #123, #245, #992 [^1]

---

### 2. [Git style guide](https://github.com/agis-/git-style-guide)

#### 브랜치
- 간결하고 잘 설명될 수 있는 이름을 선택하세요.
- 외부 서비스(예를 들어 깃헙)의 티켓(할일)에 해당하는 이름도 브랜치 이름으로 쓰는데 좋은 예시 중 하나입니다.
- 단어를 분리하기 위해 이음표(-) 를 사용하세요.
- 몇명의 사람들이 같은 기능들을 구현한다면, 개인적 기능 브랜치와 팀 기능 브랜치를 가지고 작업하는데 편리 할 수 있습니다. 아래와 같은 명명 규칙을 사용해 보세요.

        $ git checkout -b feature-a/master # team-wide branch
        $ git checkout -b feature-a/maria # Maria's personal branch
        $ git checkout -b feature-a/nick # Nick's personal branch

  개인이 작업한 브랜치는 계속해서 팀이 구현하는 브랜치로 합쳐지고 나중 "master"와 합쳐질 것입니다. (see "Merging").

- 브랜치가 머지된 이후에는 (남겨둬야 할 특별한 이유가 없다면) 리파지토리에서 삭제합니다.

#### 커밋
- 각각의 커밋은 하나의 논리적 변화를 나타내야 합니다. 여러개의 논리적 변화를 한번에 커밋하지 마세요. 예를 들어 하나의 문제점을 수정하고 기능의 성능을 향상 시켰다면, 두 개의 커밋으로 분리해야 합니다.

- 한개의 논리적 변화를 여러개의 커밋으로 나누지 마세요. 예를 들어 하나의 구현된 기능과 그것의 테스트 코드는 한 커밋이어야 합니다.

- 커밋은 빨리 그리고 자주 하세요. 독립적인 커밋은 무언가 잘못됬을때, 이해하고 되돌리기 쉽습니다.

- 커밋은 논리적으로 순서를 가져야 합니다. 예를 들어 커밋 X는 커밋 Y의 변화에 의존한다면 커밋 Y는 커밋 X 보다 먼저 일어나야 합니다.

#### 메시지

- 커밋 메시지를 작성할때, 터미널 보다는 에디터를 사용하세요.

        # good
        $ git commit

        # bad
        $ git commit -m "Quick fix"

- 요약하는 문장(예를 들어, 메시지의 첫출)은 간단명료한 설명이 되어야 합니다. 50글자를 넘지 않는 것이 바람직합니다. 대문자로 시작하는 명령형 현재시제를 사용해야 합니다. 커밋의 실질적인 제목이 되기 때문에 마침표(온점)로 끝나는 것은 바람직하지 않습니다.

        # good - 50글자를 넘지않고 대문자로 시작하는 명령형 현재시제 문장
        Mark huge records as obsolete when clearing hinting faults

        # bad
        fixed ActiveModel::Errors deprecation messages failing when AR was used outside of Rails.

- 이후에 자세한 설명을 위해 이어지는 문장 앞에 빈 줄이 와야 합니다. 72글자 정도에서 줄바꿈이 돼야하며 왜 변화가 필요했는지, 어떻게 변경했는지에 대한 설명이 필요하고 혹시나 있을지 모르는 side-effects에 대해서 기술해야 합니다.

        Short (50 chars or fewer) summary of changes

        More detailed explanatory text, if necessary. Wrap it to
        72 characters. In some contexts, the first
        line is treated as the subject of an email and the rest of
        the text as the body. The blank line separating the
        summary from the body is critical (unless you omit the body
        entirely); tools like rebase can get confused if you run
        the two together.

        Further paragraphs come after blank lines.

        - Bullet points are okay, too

        - Use a hyphen or an asterisk for the bullet,
        followed by a single space, with blank lines in
        between

        Source http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
  궁극적으로, 커밋 메시지를 작성할때, 지금부터 1년 후에 커밋을 거슬러 갈때 무엇이 필요한지를 생각해야 합니다.

- 만약 커밋 A가 다른 커밋 B에 의존한다면, 커밋 A 메시지의 시작은 의존성을 언급해야 합니다. 커밋을 언급할때는 커밋 해쉬를 사용하면 됩니다.
  비슷하게, 커밋 B에서 발생된 버그를 커밋 A에서 풀었다면 커밋 A에서 그것을 언급해야 합니다.

- 만약 다른 커밋에 밀어넣으려면 --squash를 사용하고, 의도를 확실하게 표시하고자 한다면 --fixup을 사용하세요. [^2]

        $ git commit --squash f387cab2

#### 머지

- 이력을 "깔끔"하고 "단순"하게 유지하세요. 브랜치를 "머지하기 바로 직전에"
1. 이력이 관습을 따르고 있는지, 필요하지 않은 행위는 없는지 확인하세요. (squash/reorder commits, reword messages etc.)
2. 머지될 브랜치를 리베이스 하세요.

        [my-branch] $ git fetch
        [my-branch] $ git rebase origin/master
        # then merge

이 결과는 "master"브랜치의 맨뒤에 이력을 붙여서 매우 간단한 이력을 보여주게 됩니다.

- 만약 브랜치에 하나 이상의 커밋이 있다면, fast-forward 머지는 하지 마세요.

        # good - 머지 커밋이 반드시 일어납니다.
        $ git merge --no-ff my-branch

        # bad
        $ git merge my-branch

#### 기타

- 푸시하기 전에 테스트 하세요. 완료되지 않은 일을 푸시하지 마세요.
- 종종 성능 유지 작업을 통해 리파지토리가 좋은 모습을 가지도록 유지하세요. 그것이 로컬 리파지토리이건, 원격 리파지토리건 간에

---

### 3. [A Successful Git Branching model](http://nvie.com/posts/a-successful-git-branching-model/)

- Git Branch 관련하여 그림과 함께 세세한 설명이 잘 되 있어서 Reference용.

---

[^1]: Close(s)와 이슈 번호를 커밋 메시지에 넣어 GitHub에 Push하면 해당 이슈가 자동으로 닫힌다.
[^2]: 리베이스 할때 --autosquash를 사용하세요. 표시된 커밋은 자동으로 밀어 넣어집니다.
