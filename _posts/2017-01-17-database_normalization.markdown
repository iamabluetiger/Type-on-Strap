---
layout: post
title: 데이터베이스 정규화
date: 2017-01-17 14:41:14.000000000 +09:00
---
관계형 데이터베이스의 설계에서 중복을 최소화하게 데이터를 구조화하는 프로세스를 정규화라고 한다. 데이터베이스 정규화의 목표는 이상이 있는 관계를 재구성하여 작고 잘 조직된 관계를 생성하는 것에 있다. 일반적으로 정규화란 크고, 제대로 조직되지 않은 테이블들과 관계들을 작고 잘 조직된 테이블과 관계들로 나누는 것을 포함한다. 정규화의 목적은 하나의 테이블에서의 데이터의 삽입, 삭제, 변경이 정의된 관계들로 인하여 데이터베이스의 나머지 부분들로 전파되게 하는 것이다.

정규화는 하나의 테이블을 여러개의 테이블로 분리시키는 과정이므로 일반적으로 정규화가 진행될 수록 테이블 수가 늘어나게 된다. 이 과정은 중복된 데이터를 줄이고 이상증상들을 없앨 수 었지만 테이블 수가 늘어나 SELECT 시에 여러 테이블을 참조해야만 하므로 성능이 저하되는 단점이 있다.

    여러 테이블에 분산되어 있는 정보를 SELECT 시에 JOIN을 하게 되는데 이는 데이터베이스에서 가장 부하가 많이 걸리는 작업 중에 하나이다. 참고로 이를 줄이기 위해 Subquery를 사용하기도 한다. 이런 단점 때문에 대규모 데이터 처리를 하는 경우 하나의 테이블에 몰아 넣기도 한다.

#### __정규화 종류__

- __1st Normal Form(1NF)__
  1. 모든 항목에 값이 있어야 한다.
  - No multi-valued or composite attributes
  - NULL을 허용하지 않는다.

- __2st Normal Form(2NF)__
  1. Key 외의 다른 속성들은 Key에만 종속적이어야 한다 (완전 함수 종속)
  - Key의 일부 속성에 의해 결정되는 속성 들이 있지 않아야 한다.

- __3st Normal Form(3NF)__
  1. 2NF 만족 (2NF과정을 거치지 않고 3NF으로 변환 가능하다)
  2. No transitive dependency
  3. `**보통 3NF까지 만족하면 정규화되었다라고 표현한다`

            Transitive dependency (이행 종속)
            Z = not a subset of any candidate key라고 할 때 X->Z & Z->Y인 함수 종속이 없는 relation.

- __Boyce-Codd Normal Form(BCNF)__
  1. For every X->A in FD's in relation schema R, X is a superkey of R.
  2. BCNF는 3NF의 부분집합이다.

```ruby
# 업로드 예정
# - __4st Normal Form(4NF)__
# - __5st Normal Form(5NF)__
# - __6st Normal Form(6NF)__
```

#### __참조__

1. [위키백과: 데이터베이스 정규화](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4_%EC%A0%95%EA%B7%9C%ED%99%94)
- [ezphp](http://brown.ezphp.net/125)
- [Lael's World](http://brown.ezphp.net/125)
