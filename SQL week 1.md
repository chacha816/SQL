## SQL 1주차 정리 (2021/02/01 ~ 2021/02/07)

### [DBeaver로 SQL 살펴보기]

### 1. DBeaver 란?

*  무료 데이터베이스 관리툴
* JDBC 드라이버를 통해 데이터베이스와 통신
* 다양한 DB지원 (중 MySQL을 공부해보기로!)



### 2. DB에 있는Data 살펴보기

* 코드 마지막에  ;는 '여기까지 코드를 실행시킨다'는 의미 

~~~
#전체 테이블 살펴보기
show tables;

#각 시트별 data보기
<SELECT * FROM >을 사용
select * from orders
select * from checkins
select * from courses
select * from enrolleds
select * from enrolleds_detail
select * from orders
select * from point_users
select * from users

#orders에서 특정필드만 가져오기
select created_at, course_title, payment_method, email from orders;
~~~

 

### 3. orders에서 필요한 데이터 추출

* from ~~(orders 등)은 tables 중 엑셀에서 시트를 선택하는 것과 같음
* where은 추출하고 싶은 데이터의 조건 설정
* and는 where에 조건 추가
* select * 은 '*의 결과 값을 출력한다'고 이해하기 

~~~
1) orders에서 kakaopay로 결제한 데이터만 추출
SELECT * FROM orders
where payment_method = "kakaopay";

2) orders에서 '앱개발 종합반'을 신청했고, kakaopay로 결제한 데이터 추출
SELECT * FROM orders
where course_title = "앱개발 종합반"
and payment_method = "kakaopay";

3) point가 20,000점 이상인 사용자 데이터 추출
SELECT * FROM point_users
where point > 20000;

4) users에서 성씨가 황씨인 사람만 추출
SELECT * FROM users
where name = '황**';

5) orders에서 '앱개발 종합반'을 신청했고, CARD로 결제한 데이터 추출
SELECT * FROM orders
where course_title = "웹개발 종합반"
AND payment_method = "CARD";
~~~



### 4. '같지 않음' 조건은 !=로 사용

~~~
# orders에서 웹개발 종합반을 신청하지 않은 데이터는?
select * from orders
where course_title != "웹개발 종합반";
~~~



### 5.  다양한 예제로 SQL 사용해보기

#### 5-1) 7월 13일부터 14일까지 데이터 추출

* between "날짜" and "날짜" 는 원하는 기간동안의 데이터를 추출할 때 사용
* orders시트에 created_at이라는 필드에서 해당기간 동안의 데이터를 추출한 것

~~~
select * from orders 
where created_at between "2020-07-13" and "2020-07-15";
~~~

#### 5-2) 1주차 또는 3주차 강의 다짐 출력

* A or B처럼 특정한 값을 선택할 때는 in(##, ##)로 조건 설정
* checkins 시트에서 week 필드에 1 또는 3에 해당하는 값 출력

~~~
select * from checkins 
where week in (1, 3);
~~~

#### 5-3) daum을 포함하는 메일주소 출력

* like는 where에서 ~~한 조건에 해당하는 데이터를 추출

~~~
select * from users 
where email like '%daum.net';

<응용하기>
# '.com'을 포함하는 메일주소
SELECT * FROM users
where email LIKE '%.com';

# 메일주소에 's'가 들어가는 것
select * from users 
where email like 's%';

# 메일주소에 's'가 들어가고 'com'으로 끝나는 것
select * from users 
where email like 's%com';

# 메일주소에 'good'이 들어가는 것
select * from users 
where email like '%good%';
~~~

* 다중 like는 정규표현식을 사용하면 더 쉽다. 
  * REGEXP_LIKE를 사용하면 효율적
  * '김|이|박'은 성씨가 김 or 이 or 박에 해당하는 데이터를 추출하는 것을 뜻함.

~~~
1) 다중 like
SELECT * FROM users u 
where(name LIKE "%김%" or name LIKE "%이%" or name LIKE "%박%")

2) 정규표현식
SELECT * FROM users u 
where REGEXP_LIKE(name, '김|이|박')
~~~

#### 5-4) 결제수단이 CARD가 아닌 주문데이터만 출력하기

~~~
SELECT * FROM orders
where payment_method != "CARD";
~~~

#### 5-5) 20000 ~30000 포인트를 보유하고 있는 유저 출력해보기 

~~~
SELECT * FROM point_users
where point BETWEEN 20000 AND 30000;
~~~

#### 5-6) 이메일이 s로 시작하고 com로 끝나는 유저 출력해보기

~~~
SELECT * FROM users
where email like 's%com';

<응용하기> 
# sql이 들어가는 네이버 주소를 사용하는 유저
SELECT * FROM users
where email like 'sql%naver.com';

# 이메일이 s로 시작하고 com로 끝나면서 성이 이씨인 유저만 출력해보기
SELECT * FROM users
where email LIKE 's%com'
AND name = "이**";
~~~

#### 5-7) 기타

~~~
#성이 남씨인 유저의 이메일만 추출하기
SELECT email from users
where name = "남**";

#gmail 을 사용하는 2020/07/12 ~ 13에 가입한 유저를 출력하기
SELECT * from users
where email like '%gmail.com'
and created_at BETWEEN "2020-07-12" and "2020-07-14";

#gmail 을 사용하는 2020/07/12 ~ 13에 가입한 유저의 수를 출력하기
SELECT COUNT(*) FROM users
where email like '%gmail.com'
and created_at BETWEEN "2020-07-12" and "2020-07-14";
~~~



### 6. LIMIT : 상위 데이터 일부만 가져오기

* 전체 테이블 구조를 빠르고 간단하게 확인할 때 사용
  * Python에서 head(함수)와 같은 문법

~~~
SELECT * FROM orders
where payment_method = "kakaopay"
LIMIT 5

SELECT * FROM checkins 
limit 10
~~~



### 7. Distinct : 중복 데이터는 제외하고 가져오기 

~~~
SELECT payment_method FROM orders;
SELECT DISTINCT (payment_method) from orders;
~~~



### 8. count : 몇 개인지 숫자 세보기

~~~
SELECT COUNT(payment_method) from orders;
SELECT COUNT(*) from orders

<응용하기>
# 회원의 성씨가 몇개인지 궁금하다면?
select DISTINCT (name) from users;
select COUNT(DISTINCT (name)) from users;
~~~



---

## 1주차 과제

###  : naver 이메일을 사용하면서, 웹개발 종합반을 신청했고 결제는 kakopay로 이뤄진 주문데이터 출력하기

~~~
SELECT * from orders
where email like '%naver.com'
and course_title = "웹개발 종합반"
and payment_method = "kakaopay";
~~~
