---
layout: post
title: CentOS에 nginx, rails 환경 구축하기
date: 2016-12-02 08:55:45.000000000 +09:00
tags: [centos, nginx, puma, rails, deploy, ruby]
---

CENTOS 환경에서 Rails 서버 구축을 해야하는 상황을 위하여 Manual로 작성해두었다.

### Install nginx

```bash
sudo yum install -y epel-release
sudo yum install -y nginx

# CentOS 7
sudo systemctl start nginx 
sudo systemctl enable nginx (Enable nginx to start when your system boots.)

# CentOS 6
sudo service nginx start
chkconfig nginx on
```
---

#### Create custom SELinux policy module

```bash
# Install utilities to be able to change selinux policies
sudo yum install -y policycoreutils policycoreutils-python
sudo yum install -y setroubleshoot

grep nginx /var/log/audit/audit.log | audit2allow -M nginx
semodule -i nginx.pp
```
---  

### Install rbenv

```bash
sudo yum install -y git-core zlib zlib-devel gcc-c++ patch readline readline-devel libyaml-devel libffi-devel openssl-devel make bzip2 autoconf automake libtool bison curl sqlite-devel libcurl libcurl-devel postgresql-libs postgresql-devel 

cd
git clone git://github.com/sstephenson/rbenv.git .rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
exec $SHELL

git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bash_profile
exec $SHELL
```
---

### Install ruby

```bash
rbenv install -v 2.3.3
rbenv global 2.3.3
gem install bundler
```
---

### Install rails

```bash
gem install rails
rbenv rehash
```
---

### Install database ( If you use local database )

```bash
# mariadb
sudo yum install mariadb-server mariadb-devel

# postgresql
sudo yum install postgresql-server postgresql-contrib
```
--- 

