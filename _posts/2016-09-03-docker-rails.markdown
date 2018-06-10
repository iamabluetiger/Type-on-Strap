---
layout: post
title: Docker로 Rails 환경 구축하기
date: 2016-09-03 10:59:50.000000000 +09:00
---
앞서 Docker의 입문으로 Ghost를 배포해보았다. 이제 실제 개발 환경을 구축하여 서비스에 적용할 준비를 해볼 차례이다. 도커의 장점을 극대로 느껴보기 위하여 개발환경에서 거의 모든걸 구축하고 실서버에 빠른 배포를 해 볼 계획이기에 이 글의 환경은 현재 쓰고 있는 Mac Book의 **OSX** 이라는 것을 밝혀둔다. ~~*docker for mac 이 나온 이후엔 Host OS는 의미가 없는 것 같지만..*~~ 또한 Ruby on Rails, NGINX, PostgreSQL을 사용하여 서비스를 개발하고 있기 때문에 이를 기반으로 도커 환경을 구축해보려고 한다.

---

##Ruby on Rails
```docker
# Dockerfile

FROM ruby:latest
MAINTAINER Ten <iamabluetiger@sentbe.com>

# for update and build-essentail
RUN apt-get update -qq && apt-get install -y build-essential

# for postgres
RUN apt-get install -y libpq-dev

# for nokogiri
RUN apt-get install -y libxml2-dev libxslt1-dev

# for curb
RUN apt-get install -y libcurl3 libcurl3-gnutls libcurl4-openssl-dev

# for a JS runtime
RUN apt-get install -y nodejs

ENV RAILS_PATH /var/www/myapp-rest

RUN mkdir /var/www/
RUN mkdir $RAILS_PATH
WORKDIR $RAILS_PATH
ADD Gemfile* $RAILS_PATH/
RUN bundle install
ADD . $RAILS_PATH/

CMD ["rails", "server", "-b", "0.0.0.0"]
```
[Rails Image](https://hub.docker.com/_/rails/)가 있긴 하지만 [Ruby Image](https://hub.docker.com/_/ruby/)를 기반으로 Rails를 설치하여 요리조리 만지는 편이 더 더 쉽고 좋을 것 같아서 **ruby:latest**를 선택하게 되었다.

`apt-get install -y build-essential`는 조금 꺼림칙한 부분인데 그 이유는 Ruby Image가 [buildpack-deps:jessie](https://github.com/docker-library/buildpack-deps/blob/f1d33d5c92e1bd2aee9f2333ceb316251e6388d4/jessie/Dockerfile)를 기반으로 하고 있기 때문에.. 어쨋든 추가한다고 해서 나쁠 건 없으니까 추가하였다. 😏

그 외의 부분은 Rails를 설치하고 서버를 실행하는 부분이다. 0.0.0.0에 바인딩하는 이유는 확실하게는 잘 모르겠지만 Host에 연결하기 위함이라고 예상하고 있다. (혹시 정확한 이유를 아신다면 연락좀 부탁드립니다 😘)

---

##NGINX

```nginx
# Dockerfile

FROM nginx:latest
MAINTAINER Ten <iamabluetiger@sentbe.com>

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/nginx.conf
COPY myapp.conf /etc/nginx/conf.d/myapp.conf
```

```nginxconf
# nginx.conf
    
...

include /etc/nginx/conf.d/*.conf;

...
```

```nginx
# myapp.conf

upstream myapp {
  server 172.18.0.3:3000;
}

server {
  listen 80;
    
  location / {
    proxy_pass                          http://myapp;
    proxy_set_header  Host              $http_host;   # required for docker client's sake
    proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
    proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Proto $scheme;
    proxy_read_timeout                  900;
  }
}
```
**Dockerfile** - [nginx Image](https://hub.docker.com/_/nginx/)를 사용하고 있으며 nginx와 custom 설정을 위해 Host의 파일들을 COPY 하였다.

**nginx.conf** - 이 파일은 nginx 웹 설정을 위한 것이며 다른것들은 다 각자의 웹 설정에 맞추어 하시면 될 듯 하고 저는 custom 설정을 손쉽게 추가 하기 위해 이 코드를 추가하였습니다.

**myapp.conf** - [reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy)를 이용하기 위하여 작성하였다. location / 이 부분은 아직 공부가 더 필요한 부분이다. ~~공부는 하면할수록 양이 많아진다~~

---

##Docker Compose

> Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a Compose file to configure your application’s services. Then, using a single command, you create and start all the services from your configuration.

```docker
# docker-compose.yml

version: "2"

services:
  nginx:
    build: ./nginx/
    container_name: myapp-nginx
    depends_on:
      - web
    volumes_from:
      - web
    ports:
      - "80:80"
  web:
    build: ./myapp-rest/
    container_name: myapp-web
    links:
      - db
    ports:
      - "3000:3000"
    environment:
      - RAILS_ENV=${RAILS_ENV}
    env_file:
      - '.env/db'
      - '.env/web'
  db:
    image: postgres
    container_name: myapp-db
    volumes: 
      - "myapp-db:/var/lib/postgresql/data"
    ports:
      - "5432:5432"
    env_file:
      - '.env/db'

volumes:
  myapp-db:
    external: true
```

Docker Compose를 이용하면 여러개의 Container를 사용해야만 하는 번거로울 수 있는 상황을 효과적으로 해결할 수 있다. 앞서 [PostgreSQL](https://hub.docker.com/_/postgres/)의 Dockerfile은 왜 작성하지 않으셨는지 궁금하셨을텐데 [docker-compose](https://docs.docker.com/compose/)에 기본적인 설정만 넣으면 손쉽게 컨테이너 생성이 가능하다.

**docker-compose.yml** - Docker에 대한 기본적인 지식만 있다면 그리 어려운 부분은 없을 것 같아 자세한 설명을 적는 것은 생략하는 것으로.. 자세하게 알고싶다면 [Compose file reference](https://docs.docker.com/compose/compose-file/) 를 참조하자.

이제 `docker-compose up`를 입력하면 자동적으로 container들을 설정값에 따라 만들고 실행할 수 있다. 그리고나서 localhost로 접속하면 Rails의 기본 화면을 만나볼 수 있을 것이다!  

<br><br>

![rails default page](/content/images/2016/09/Screen-Shot-2016-09-03-at-7-53-18-PM.png)
