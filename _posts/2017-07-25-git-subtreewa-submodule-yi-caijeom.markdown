---
layout: post
title: Git, Subtree와 Submodule 의 차이.
date: 2017-07-25 10:09:35.000000000 +09:00
tags: [git, subtree, submodule]
---
Rails를 RESTful 프로젝트와 Admin 프로젝트로 나누면서 공통으로 쓰는 Library, Model들이 생겨나기 시작하였다. 여기서 문제점은 두 곳 모두에서 사용하다 보니 필요한 경우 수정이 일어났는데, 그 때마다 두 프로젝트 간 공통으로 있어야 할 코드들이 서로 달라지는 것이였다. 그래서 이 방법을 어떻게 해결할 수 있을 까 고민을 하다가 찾아보니 Git의 기능 중 Submodule과 Subtree가 있었다. 둘 다 프로젝트 안의 프로젝트를 생성, 관리하는 것인데 그 관리 방법에서 차이를 보인다. 먼저 가장 간단하게 설명하자면 Submodule은 link, Subtree는 copy의 개념이다.

## Submodule
> 프로젝트를 수행하다 보면 다른 프로젝트를 함께 사용해야 하는 경우가 종종 있다. 함께 사용할 다른 프로젝트는 외부에서 개발한 라이브러리라던가 내부 여러 프로젝트에서 공통으로 사용할 라이브러리일 수 있다. 이런 상황에서 자주 생기는 이슈는 두 프로젝트를 서로 별개로 다루면서도 그 중 하나를 다른 하나 안에서 사용할 수 있어야 한다는 것이다.

[git-scm](https://git-scm.com/book/en/v2/Git-Tools-Submodules)에서는 위처럼 설명한다. 여기서 가장 중요한 부분은 두 프로젝트를 서로 별개로 다룬다는 것이다. Submodule의 로컬 저장소는 "Detached HEAD"상태로 남는다. 즉 이 곳에서 수정 작업을 해봤자 후에 수정한 내용을 잃어버릴 수 있다는 것이다. 그래서 사실 정석적으로 사용하는 것은 어떤 변경사항이 필요할 때는 Submodule repository에 들어가서 작업을 하고 푸시하고, 이 Submodule을 쓰는 Repository 들에서 최신버전으로 업데이트를 한 뒤 사용하도록 하는 것이 맞다. 마이크로서비스를 추구하는 프로젝트에 굉장히 잘 어울리는 방법이라 생각한다.

## Subtree

> With subtrees, there are no nested repos: there’s only one repo, the container, just like a regular codebase. That means just one lifecycle, and no special tricks to keep in mind for commands and workflows, it’s business as usual. Ain’t life sweet?

[@porteneuve](https://medium.com/@porteneuve/mastering-git-subtrees-943d29a798ec)가 Medium에 올린 글을 발췌하였다. 여기서 가장 중요한 부분은 **"Only one repo, the container, just like a regular codebase"** 이다. 즉 Subtree를 이용하면 다른 Repository를 마치 나의 Repository처럼 이용할 수 있는 것이다. 그래서 Subtree를 통하여 Pull을 받고나면 평소 하나의 Repository처럼 사용 하여도 아무런 문제가 없다. 허나, 이렇게 공통 Repository를 사용하는 경우 역시나 어느 한 곳이 outdated될 문제가 있는데 이를 막기 위하여 각 Repository 마다 공통의 Repository를 관리하기 위해 subtree push, pull등의 명령어를 계속 사용해주는 관리자가 있어야한다. 이는 소규모의 프로젝트, 그러나 공통되는 부분이 있어 따로 관리를 해야만 하는 것이 있는 경우에 잘어울린다고 생각된다. 아마 대부분의 경우 Submodule보다는 Subtree가 더 어울릴 것이라고 생각된다.
