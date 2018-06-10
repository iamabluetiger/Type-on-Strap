---
layout: post
title: Docker로 Ghost 배포하기
date: 2016-08-31 03:20:04.000000000 +09:00
tags: [docker, ghost, deploy]
---
[Docker](https://www.docker.com)를 공부하다보니 [Ghost](https://ghost.org)같은 설치형 블로그가 굉장히 접근하기 쉬울 것 같단 생각이 들어서 바로 정지되어 있던 AWS 계정을 복구 시켜 EC2를 생성하였다.

[Ghost Official Repository](https://hub.docker.com/_/ghost/)는 이미 Docker Hub에서 만나볼 수 있다. ~~`docker pull ghost` 커맨드 한번이면 블로그를 실행할 준비가 끝난다는 의미이다.~~ 이제보니 `docker run ghost` 한번이면 실행까지 가능하다.

> [Installation of Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/)는 이 곳에서 참고.

## Deploy Ghost

1. Docker Hub에서 Ghost Image 다운로드

        docker pull ghost

2. Run Container

        docker run -d -p 80:2368 ghost 

Docker로 Ghost를 배포하는 것이 끝났다. Host의 IP 주소 혹은 Domain 주소로 접속하면 블로그 기본 화면이 나타나는 것을 볼 수 있다. 

Docker의 장점을 부각시키기 위하여 일부러 다른 세팅들은 전혀 하지 않았다고는 하지만 두번의 명령에 블로그가 만들어 지는 것은 정말 대단한 것이라는 생각이 든다.

### ***도커 짱짱맨***

---

### Used Command

    sudo docker run --name iamabluetiger-blog -p 80:2368 -v /iamabluetiger-blog/ghost/:/var/lib/ghost/ -e NODE_ENV=production ghost

이 명령어가 위의 `docker run` 에 설정을 추가하여 사용했던 명령어이다. 간단히 설명하자면

* `--name iamabluetiger-blog` : Container 이름[^1]
* `-e NODE_ENV=production` : 노드 환경 설정[^2]
* `-v /iamabluetiger-blog/ghost/:/var/lib/ghost/`[^3] : Volume 설정[^4]

---

[^1]: Container 이름은 사실 굳이 설정하지 않아도 괜찮다.
[^2]: Ghost가 node.js환경이기 때문에 노드 환경 설정을 해주는 것이다.
[^3]: Ghost의 config, theme, data 등등의 permanent한 파일들을 저장, 사용하기 위함이다
[^4]: Docker에서 Volume 설정은 데이터를 Container가 Host에 저장하고 사용하겠다는 이야기이다.
