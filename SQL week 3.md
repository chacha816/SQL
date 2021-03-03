## SQL 3주차 정리 (2021/02/15 ~ 2021/02/21)

### [2주차 간단히 복습]

- ###### Group by

- ###### MIN / MAX / AVG / SUM 

- ###### Order by  ~ DESC

- ###### where, Group by, Order by 같이 사용

- ###### Alias : 별칭 기능

~~~
<예시코드>
SELECT WEEK, AVG(likes) FROM checkins
where week = 2;

select name, count(*) from users 
group by name
order by count(*) DESC;

select * FROM orders as o
where o.course_title = "앱개발 종합반";

SELECT payment_method, count(*) FROM orders
where email like '%naver.com'
AND course_title = "앱개발 종합반"
group by payment_method;
~~~





## 1. join과 subquery를 통해 테이블 합쳐서 정보 추출

#### Q) 2주 동안 공부한 방법으로 point가 3000점 이상인 user_id를 찾으려면?

~~~
SELECT * FROM checkins
SELECT * FROM users
where user_id = "4b8a10e6"
~~~

1) checkins 데이블의 user_id를 복사

2) users테이블에서 user_id를 갖는 데이터 가져오기 

3) 작성자를 조회하고 싶은 '오늘의 다짐' 갯수대로 ,2 과정 반복

#### But, 이대로 하면 100명을 얻으려면 100번을 해야함... 더 좋은 방법은 없을까?



### :bulb: Join이란?  

#### : 두 테이블의 공통된 정보(key값)를 기준으로 테이블을 연결해서 한 테이블처럼 보는 것을 의미

~~~
SELECT * FROM checkins
left join users
on checkins.user_id = users.user_id;
~~~

※ 필드명 뒤에 id가 붙으면 key값 일 확률이 높음



### [Join 응용하기]

#### 1-1) 포인트가 5000점이 넘고 성이 박씨인 user_id 추출하기 

~~~
select * from point_users
left join users 
on point_users.user_id = users.user_id
where point > 5000
AND name like "박**";
~~~

※ SQL의 Join은 엑셀의 vlookup과 동일하다고 생각하면 됨 (Join의 종류 : Left join, Inner join)

#### 1-2) Left Join 이해해보기(null값이 추출됨)

~~~
select* from users 
left join point_users 
on users.user_id = point_users.user_id
~~~
#### 1-3)  Inner Join 이해하기 (교집합만 추출, null값이 없음)
~~~
select * from users 
inner join point_users 
on users.user_id = point_users.user_id; 
~~~

▶ Primary_key(구분할수 있도록 만든 필드, 중복X)
▶ Fpreign key(테이블과의 연결을 위한 필드, 중복O. 그러나 각 테이블에서는 고유해야함)






### [실습1] orders 테이블에 users 테이블 연결해보기
~~~
SELECT * FROM orders o 
left join users u 
on o.user_id = u.user_id;
~~~
### [실습2] checkins 테이블에 users 테이블 연결해보기
~~~
SELECT * FROM checkins c 
left join users u 
on c.user_id = u.user_id 
~~~
### [실습3] enrolleds(수강등록)테이블에 courese 테이블 연결해보기
~~~
SELECT * FROM enrolleds e 
left join courses c2 
on e.course_id = c2.course_id 
~~~



###  [배웠던 문법을 Join과 함께 사용해보기]
#### 연습1) checkins 테이블에 courses 테이블 연결해서 통계치 내보기 : 과목별 '오늘의 다짐' 갯수 세어보기
~~~
SELECT c2.title , COUNT(c2.title) FROM checkins c 
left join courses c2 
on c.course_id = c2.course_id
group by c2.title;
~~~
~~~
(코드 스니펫 예시)
select co.title, count(co.title) as checkin_count from checkins ci 
left join courses co 
on ci.course_id = co.course_id 
group by co.title;
~~~


#### 연습2) point_users 테이블에 users 테이블 연결해서 순서대로 정렬해보기 : 많은 포인트를 얻은 순서대로 유저 데이터 정렬해보기

~~~
SELECT * FROM point_users p
left join users u
on p.user_id = u.user_id 
order by point desc;
~~~


#### 연습3) order 테이블에 users 테이블 연결해서 통계치 내보기 : 네이버 이메일 사용하는 유저의 성씨별 주문건 수 세어보기

~~~
SELECT u.name , COUNT(u.name) FROM orders o 
left join users u 
on o.user_id = u.user_id 
where u.email like "%naver.com"
group by u.name;
~~~
~~~
#(코드스니펫 예시) : as로 필드명 만들어주기
select u.name, count(u.name) as count_name from orders o 
left join users u 
on o.user_id = u.user_id 
where u.email like '%naver.com' 
group by u.name;
~~~





## 2. Subquery란?
### : 쿼리 안의 쿼리라는 의미로 더 편하고 간단하게 데이터를 얻을 수 있는 방법
~~~
SELECT * FROM users u 
where u.user_id IN (SELECT o.user_id
	from orders o
	where o.payment_method  = "kakaopay"
);
~~~

##### 위의 내용을 Join으로 작성하면?
~~~
SELECT u.user_id , u.name , u.email FROM orders o 
left join users u 
on o.user_id = u.user_id 
where o.payment_method = "kakaopay";
~~~



#### <응용하기 - '오늘의 다짐'에 남겨진 좋아요 수와 평균이 구해진 데이터 필드>

~~~
SELECT c.checkin_id , c.user_id ,c.likes,
	(SELECT AVG(c2.likes) from checkins c2
	where c2.user_id = c.user_id) as avg_like_user
FROM checkins c;
~~~



###  :bulb:  Subquery는 Where절에 들어가거나 Select절에 자주 사용됨
* Where절에 들어가는 Subquery : 결과를 조건에 활용하는 방식.
		 ###### ex : where 필드명 in (subquery) 이런 방식!



#### 2-1) 카카오페이로 결제한 주문건유저들만, 유저 테이블에서 출력해보기
~~~
SELECT * FROM users u 
where u.user_id in (SELECT o.user_id 
	from orders o
	where o.payment_method = 'kakaopay');
~~~


#### 2-2) 유저 포인트가 평균보다 많은 사람들의 데이터 추출해보기 (*참고:평균 포인트는 5380점)

~~~
(내가 작성한 코드)
SELECT * FROM users u 
where u.user_id in (SELECT pu.user_id 
	from point_users pu 
	where point > 5380);
~~~
~~~
(코드스니펫 예시)
select * from point_users pu 
where pu.point > (select avg(pu2.point) from point_users pu2);
~~~
#### <응용하기>
~~~
select pu.point_user_id , pu.user_id, pu.point, (select u.name from users u where u.user_id = pu.user_id) as user_name 
from point_users pu 
where pu.point > (select avg(pu2.point) from point_users pu2);
~~~


##### :point_right: 내가 작성한 코드와 예시코드의 차이점
: user_id 결과값은 동일하지만, 내 코드에서는 포인트 점수를 알 수 없음.



#### 2-3) 이씨 성을 가진 유저의 포인트의 평균보다 큰 유저들의 데이터 추출하기

~~~
SELECT * FROM point_users pu 
where pu.point > (SELECT AVG(pu2.point) from point_users pu2 
	left join users u 
	on pu2.user_id  = u.user_id 
	where u.name = "이**");
~~~
#### <응용하기>
~~~
SELECT pu.point_user_id , pu.user_id , pu.point, (select u2.name from users u2 where u2.user_id =pu.user_id) as user_name
FROM point_users pu 
where pu.point > (SELECT AVG(pu2.point) from point_users pu2 
	left join users u 
	on pu2.user_id  = u.user_id 
	where u.name = "이**");
~~~
: 이씨 성을 가진 유저의 포인트의 평균보다 큰 유저의 데이터를 추출했기 때문에 평균보다 높은 이씨 성을 가진 유저가 출력될 수 있음. 응용코드로 성씨까지 확인가능





## 3.  Select절에 들어가는 Subquery
### : 기존 테이블과 함께 보고싶은 데이터를 쉽게 붙이는 것.
###### select 필드명, 필드명,(subquery) from... 방식!



#### 3-1) checkins 테이블에 course_id별 평균 likes수 필드 우측에 붙여보기 
~~~
SELECT c.checkin_id , c.user_id ,c.likes,
	(SELECT AVG(c2.likes) from checkins c2
	where c2.user_id = c.user_id) as avg_like_user
FROM checkins c;
~~~


#### 3-2) checkins 테이블에 과목명별 평균 like수 필드 우측에 붙여보기 (난이도🔥 🔥 🔥 )

~~~
select checkin_id, c3.title, user_id, likes, (select avg(c2.likes) 
	from checkins c2 
	where c.course_id = c2.course_id) as like_avg    #as로 필드명 설정해서 정리해주기
from checkins c 
left join courses c3 
on c.course_id = c3.course_id;
~~~



#### 3-3) checkins 테이블에 week별 최대 likes수 필드 우측에 붙여보기

~~~
SELECT c.checkin_id, c.week, c.likes, (SELECT  MAX(c2.likes)
	from checkins c2
	where c.user_id = c2.user_id) as max_lilkes
FROM checkins c;
~~~





## 4. Quiz

### [Q1-Join 연습] 성이 김씨인 유저의 결제수단별 주문건 수 세어보기
~~~
SELECT o.payment_method, COUNT(o.payment_method) from orders o 
left join users u 
on o.user_id = u.user_id 
where u.name = "김**"
group by o.payment_method; 
~~~



### [Q2-Join 연습] is_registered = false인 경우, 수강완료한 강의 중 가장 나중 강의의 current_order 구해보기

~~~
(내가 작성한 코드)
SELECT * , MAX(ed.current_order) from enrolleds e 
left join enrolleds_detail ed 
on e.enrolled_id = ed.enrolled_id 
where e.is_registered = FALSE 
and ed.done = 1 ;
~~~
~~~
(코드스니펫 예시)
SELECT MAX(ed.current_order) FROM enrolleds_detail ed 
left join enrolleds e 
on ed.enrolled_id = e.enrolled_id 
where e.is_registered = FALSE
and ed.done = 1;
~~~

##### :point_right: 내가 작성한 코드와 예시코드의 차이점
: 내 코드는 select 부분에 *을 사용해서 enrolleds와 enrolleds_detail을 합친 테이블의 전체 필터 값이 나온 것.  select에 max(ed.current_order)를 작성해서 가장 큰 값인 21이 출력됨.



### [Q3-Where subquery + join] 이씨 성을 가진 유저들의 '평균포인트 + 1000점'보다 더 많은 포인트를 가지고 있는 데이터를 추출해보기

~~~
select * FROM point_users pu 
where pu.point > 
(select AVG(pu2.point) 
from point_users pu2
left join users u 
on pu2.user_id = u.user_id
where u.name = "이**") + 1000;
~~~



### [Q4-Select Subquery] enrolled_id별 수강완료한 강의 중, 가장 나중 강의의 current_order 구해보기

~~~
SELECT e.enrolled_id, e.user_id , 
	(SELECT MAX(ed.current_order) FROM enrolleds_detail ed
where ed.done =1
AND ed.enrolled_id=e.enrolled_id) as last_order 
FROM enrolleds e;
~~~







## 3주차 과제

### :  enrolled_id별 수강완료한 강의 갯수를 세어보고, 완료한 강의 수가 많은 순서대로 정렬해보기. user_id도 같이 출력되어야 한다.
~~~
SELECT e.enrolled_id, e.user_id, 
(SELECT COUNt(*) FROM enrolleds_detail ed 
where ed.done = TRUE 
AND ed.enrolled_id = e.enrolled_id) as max_count
FROM enrolleds e
order by max_count DESC;
~~~
