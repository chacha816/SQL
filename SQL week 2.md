## SQL 2주차 정리 (2021/02/08 ~ 0201/02/14)

### [1주차 간단히 복습]

* ###### select * from ~

* ###### where과 And

* ###### != '같지 않음'

* ###### between ~ and ~ (기간)

* ###### where ~ in(##, ##) = A or B

* ###### Like  / Limit / Distinct / Count

~~~
# 과목별 신청자 수 구하기
1) 웹개발 종합반 (= 133명)
show tables
SELECT count(*) FROM orders
where course_title = "웹개발 종합반";

2) 앱개발 종합반 (=153명)
SELECT count(*) FROM orders
where course_title = "앱개발 종합반";
~~~





## 1. Group by 란?

### : 동일한 범주를 갖는 데이터를 하나로 묶어서, 범주별 통계를 내주는 것 

~~~
# 성씨별로 범주화하고 가장 많은 성씨순으로 정렬
SELECT name, count(*) FROM users
group by name
order by COUNT(*) desc;
~~~

=> desc는 내림차순으로 정렬(큰 숫자부터)



### Group by 결과가 맞는지 확인해보기

~~~
SELECT * FROM users
where name = '신**';

SELECT COUNT(*) FROM users
where name = '신**';

SELECT name, COUNT(*) FROM users
group by name;

SELECT COUNT(*) FROM users
where name = '이**';
~~~



### [Group by 응용하기]

#### 1-1) 주차별 '오늘의 다짐' 갯수 구하기

~~~
select week, count(*) from checkins
group by week; 
SELECT * FROM checkins
where week = 3;
~~~

#### 1-2) 주차별 '오늘의 다짐' 좋아요 최소값 구하기 

~~~
SELECT week, MIN(likes) from checkins
group by week;
SELECT * FROM checkins
where week = 3;
~~~

#### 1-3) 주차별 '오늘의 다짐' 좋아요 최대값 구하기 

~~~
SELECT week, MAX(likes) from checkins
group by week;
SELECT * FROM checkins
where week = 2;
~~~

#### 1-4) 주차별 '오늘의 다짐' 좋아요 평균값 구하기 

~~~
SELECT week, AVG(likes) from checkins
group by week;

SELECT week, AVG(likes) FROM checkins
where week = 1;

SELECT WEEK, AVG(likes) FROM checkins
where week = 2;
~~~

#### 1-5) 주차별 '오늘의 다짐' 좋아요 합계 구하기

~~~
SELECT week, SUM(likes) FROM checkins
group by week;

SELECT WEEK, SUM(likes) FROM checkins
where week = 2;

SELECT * FROM checkins
where week = 2;

~~~




## 2. Order by
### : Order by는 정렬할 때 사용
~~~
select name, count(*) from users 
group by name;

select name, count(*) from users 
group by name
order by count(*);

select name, count(*) from users 
group by name
order by count(*) DESC;
# DESC = 디센딩 = 내림차순의 약자
~~~



### [Order by 응용하기]

#### 2-1) like를 많이 맏은 순서대로 '오늘의 다짐' 출력하기
~~~
select * FROM checkins
order by likes DESC;

SELECT comment FROM checkins
order by likes desc;

SELECT comment,likes FROM checkins
order by likes desc;

※ 여러개의 필드 중 2개만 선택해서 보고싶으면, select에 '필드값','필드값'을 콤마로 이어서 나열하면 됨
~~~



## 3.  where와 Group by, order by 같이 사용해보기

#### Q. 웹개발 종합반의 결제수단별 주문건수 세어보기
~~~
SELECT payment_method, count(*) FROM orders
where course_title = "웹개발 종합반"
group by payment_method
order by count(*) desc;
~~~



## 4. Order by와 Group by 같이 연습해보기

#### : 문자열을 순서대로 정렬(알파벳, 한글, 시간 등 가능)
~~~
SELECT * FROM users
order by email;

SELECT * FROM users
order by name desc;

SELECT * FROM users
order by created_at;

SELECT * FROM users
order by created_at desc;
~~~



## 5.  Group by 연습하기

* 1) 원하는 테이블과 2) 범주로 사용할 필드 3) 범주에 따라 통계를 계산하고 싶은 필드
* 단, 갯수의 경우 제외

#### [퀴즈 1] 앱개발 종합반의 결제수단별 주문건수 세어보기
~~~
SELECT payment_method, COUNT(*) FROM orders
where course_title = "앱개발 종합반"
group by payment_method;
~~~
#### [퀴즈 2] Gmail을 사용하는 성씨별 회원수 세어보기
~~~
SELECT name, count(*) FROM users
where email LIKE '%@gmail.com'
group by name;
~~~
#### [퀴즈 3] course_id별 '오늘의 다짐'에 달린 평균 like 갯수 구해보기
~~~
SELECT course_id, AVG(likes) FROM checkins
group by course_id;
~~~



### [꿀팁:blush:] 이렇게 퀴리를 작성하면 편해요!

1) show tables로 어떤 테이블이 있는지 살펴보기
2) 제일 원하는 정보가 있을 것 같은 데이블에 select * from 테이블명 limit 10 쿼리 실행해보기
3) 원하는 정보가 없으면 다른 테이블에도 2)를 해보기
4) 데이블을 찾았다! 범주를 나눠서 보고싶은 필드를 찾기
5) 범주별로 통계를 보고싶은 필드를 찾기
6) SQL 쿼리 작성하기!



### :+1:이외 유용한 문법 배워보기

#### Alias : 별칭 기능 (알리아스) = "as" 이름 지어주기, 원하는 이름으로 결과 출력
~~~
select * FROM orders as o
where o.course_title = "앱개발 종합반";

SELECT name, count(*) as freq123 FROM users
group by name;
~~~





# 2주차 과제

## : 네이버 이메일을 사용하여 앱개발 종합반을 신청한 주문의 결제수단별 주문건수 세어보기
~~~
SELECT payment_method, count(*) FROM orders
where email like '%naver.com'
AND course_title = "앱개발 종합반"
group by payment_method;
~~~
