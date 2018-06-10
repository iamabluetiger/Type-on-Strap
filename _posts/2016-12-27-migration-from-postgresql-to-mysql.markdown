---
layout: post
title: Migration from PostgreSQL to MySQL
date: 2016-12-27 06:23:01.000000000 +09:00
tags: [postgresql, migration, mysql, pg2mysql]
---
Rails에서 사용하던 PostgreSQL Database를 MySQL로 Migration 하기.

[pg2mysql](https://github.com/ChrisLundquist/pg2mysql)를 설치하기만 하면 순조롭게 MySQL로 Migration을 하여 새로운 데이터베이스를 사용할 수 있을지 알았다... 는 말도 안되고 당연히 문제가 벌어지겠거니 했는데 생각보다는 꽤 빠른 시간 안에 테스트를 완료할 수 있었다. pg2mysql은 링크를 타고 들어가보면 아시겠지만 거의 2년이 지난 migration 프로그램이기 때문에.. 프로그램 실행 전후로 몇가지 해야할 것이 있다. (2번부터는 프로그램 사용 이후 Dump파일해서 해줘야 함)

1. ENGINE의 Default값이 MyISAM인 것 유의하기. _(필자는 InnoDB 사용)_
- Rails를 사용한다면 테이블 마다의 id에 AUTO INCREMENT 추가하기.
`ALTER TABLE table_name MODIFY id int(11) AUTO_INCREMENT;`
- Migration 과정에서 생긴 쓸데 없는 값 "\'" 지우기.
- ar\_internal\_metadata 테이블, 관련한 것 지우기. ( Rails5 부터 생겼다고 합니다. )
- MySQL의 예약어인 (key) column name을 ''로 감싸기
- time을 따옴표로 감싸고 있는거 지우기 (이건 왜 생긴건지는 모르겠지만..)

---

```bash
# postgresql dump command
pg_dump database_name -f file_name.sql

# scp download command
scp host@ip:/file/path/file_name.sql /file/path/

# pg2mysql command 
php pg2mysql_cli.php /file/path/postgres.sql /file/path/mysql.sql engine_name
```
