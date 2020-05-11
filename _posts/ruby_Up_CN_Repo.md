---
layout: post
title: ruby 切换国内源
date: 2020-05-11 11:25
category: 
author: 
tags: []
summary: 
---

# ruby 切换国内源

 1.查看现有源：
```bash
gem sources -l
*** CURRENT SOURCES ***

https://rubygems.org/
```
2.删除现有源：
```bash
# gem sources --remove https://rubygems.org/
https://rubygems.org/ removed from sources
```

3.添加新源：
```bash
# gem sources -a http://gems.ruby-china.com/
http://gems.ruby-china.com/ added to sources

# gem sources --add https://ruby.taobao.org/
ERROR:  SSL verification error at depth 0: ok (0)
Error fetching https://ruby.taobao.org/:
	SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (certificate rejected) (https://ruby.taobao.org/specs.4.8.gz)
```
