---
layout: single
title: from Data Model to Relational Schema
date: 2009-06-07 23:52:01.000000000 +09:00
categories:
tags: []
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
1. 각 클래스에 대해 table을 만든다

2. 각 속성에 대해 필드를 만들고, 적절한 타입을 할당한다.
 필요시, 필드를 하위 필드의 조합으로 구성한다.


3. 한 개 또는 여러 필드의 조합으로 primary key를 선정한다. 
 선정 과정에서, 해당 키의 고유성이 보장되는지 충분히 검토한다.


4. Many-Many Relationship은, 한 개의 새로운 중간 클래스를 연계하여,
 두 개의 1-Many relationship으로 분할하여 처리한다.


5. 1-Many relationship의 경우, 1쪽의 primary key를 Many 쪽에 foreign key로 등록하여 처리한다.

6. 1-1 relationship은 서로 비교하여, 해당 정보를 보다 필요로 하는 쪽으로 나머지 클래스의 
 primary key를 자신의 foreign key로 등록한다.


7. 필수 항목에 대해, foreign key가 null이 아니어야 한다는 제약 조건을 부여한다.

8. 상속에 대해, 1-1 relationship의 규칙을 적용한다. parent의 key를 child에 foreign key로 등록한다.


from Beginnig Database Design, pp 136, Clare Churcher

