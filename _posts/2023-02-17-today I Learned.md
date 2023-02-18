---
layout: post
title: "0217 TIL"
date: 2023-02-18 19:25:00 +0900
categories: TIL
---

#### SUM

지정한 컬럼들의 값의 총합을 구하는 내장함수.

```sql
SELECT SUM(합계를 구할 컬럼) FROM 테이블;
```

#### AVG

지정한 컬럼들의 평균값을 구하는 내장함수.

```sql
SELECT AVG(평균을 구할 컬럼), AVG(평균을 구할 컬럼2) FROM 테이블;
```

> 선생님 TMI
>
> _실무에서는 쿼리가 굉장히 길기 때문에 `SELECT`, `FROM`, `WHERE`를 개행해서 씁니다.
> 회사 내에서 정한 규칙입니다만 알아보기 편하기 때문에 습관화하면 좋습니다!_

#### MAX

지정한 컬럼 내의 최댓값을 반환하는 내장함수. 숫자형 뿐만아니라 문자열도 가능.

```sql
SELECT MAX(검색할 컬럼) FROM 테이블;
```

#### MIN

지정한 컬럼 내의 최솟값을 반환하는 내장함수. 마찬가지로 문자열까지도 가능.

```sql
SELECT MIN(검색할 컬럼) FROM 테이블;
```

> 선생님 TMI2
>
> 오라클에서는 `DESC` 말고 `DESCRIBE`를 쓴다.
> 그 밖의 mySQL과 다른 명령어는 검색해서 찾아보기.

---

#### 데이터 그룹짓기

```sql
SELECT 검색할 컬럼(COUNT(*)포함하기) FROM 테이블 GROUP BY 그룹의 기준 컬럼;
```

위에서 배운 내장함수들(SUM, AVG, MAX, MIN)을 함께 활용하면 매우 유용하다.

#### 데이터 그룹에 조건 적용하기

→ GROUP BY 절에 HAVING 조건을 부여한다.

```sql
SELECT 검색할 컬럼 FROM 테이블 GROUP BY 기준 컬럼 HAVING 조건;

-- 예
SELECT user_id, COUNT(*) FROM rental GROUP BY user_id HAVING COUNT(user_id) > 1;
```

#### 두 개의 테이블 조회하기 - INNER JOIN

두 테이블의 정보를 한 번에 조회하는 INNER JOIN

→ JOIN : 여러 개의 테이블을 서로 연결하는 것.

→ 하나의 테이블로 합쳐버리면 용량이 커져서 다루기 어렵고, 테이블은 기능과 용도에 맞게 최대한 분리하는 것이 좋다.

→ 데이터 관련 법상 한 사람의 정보를 한 가지 테이블 전체에 들어있으면 안된다(!!!).

```sql
SELECT 검색할 컬럼
FROM 테이블(베이스, 중심이 되는 테이블)
INNER JOIN 연결할 테이블
ON 연결할 조건의 컬럼

--예
SELECT *
FROM rental
INNER JOIN user
ON user.id = rental.user_id;
```

테이블이름.컬럼명으로 구분.

베이스가 되는 테이블은 조회결과 왼쪽에, 연결되는 테이블은 오른쪽에 위치한다.

### LEFT JOIN

데이터가 없는 값은 null로 입력된다.

```sql
SELECT 검색할 컬럼
FROM 테이블
LEFT JOIN 연결할 테이블
ON 조건
```

INNER JOIN 은 두 데이터 중 겹치는 부분만 출력하는 반면
LEFT JOIN 은 왼쪽 데이터와 겹치는 부분을 출력한다.

ON 조건을 붙여주지 않으면 실행시 오류가 난다.(You have an error in your …)

어떤 값이 null 인지 확인하기가 쉽다.

### RIGHT JOIN

연결할 테이블(오른쪽)의 모든 값을 포함하여 연결 하기

```sql
SELECT 검색할 컬럼
FROM 테이블
RIGHT JOIN 연결할 테이블
ON 조건
```

좌우 방향잡기가 헷갈릴 때, 그냥 LEFT JOIN만 쓴다고 생각하자.

---

#### 서브쿼리

하나의 쿼리 안에 포함된 또 하나의 쿼리.

메인 쿼리가 서브쿼리를 포함하는 종속 관계.

- 특징

1. 알려지지 않은 기준을 이용한 검색에 유용
2. 메인 쿼리가 실행되기 이전에 한 번만 실행
3. 한 문장에서 여러 번 사용 가능

- 주의사항

1. 괄호와 함께 사용
2. 서브쿼리 안에서는 ORDER BY 사용 불가
3. 연산자의 오른쪽에 사용되어야한다.
4. 오로지 SELECT문으로만 작성할 수 있다.

```sql
-- 예
SELECT * FROM employee
WHERE 급여 >
(SELECT 급여 FROM employee WHERE 이름='elice');
```

#### 단일 행 서브쿼리

결과가 한 행만 나오는 서브쿼리.

```sql
--예
SELECT * FROM employee
WHERE 급여 >(단일행연산자)
(SELECT 급여 FROM employee WHERE 사원번호 = 1);
```

#### 다중 행 서브쿼리

결과를 2개 이상 반환하는 서브쿼리.

```sql
-- 예
SELECT * FROM employee
WHERE 급여 IN(다중행연산자)
(SELECT max(급여) FROM employee GROUP BY 부서번호);
```

- 다중 행 연산자

  1.  IN : 하나라도 만족하면 반환 → `1 in (1, 2, 3, 4)` 는 참
  2.  ANY : 하나라도 만족하면 반환(비교 연산 가능) → `10 < any(1, 2, 3, 4)` 는 거짓
  3.  ALL : 모두 만족하면 반환(비교 연산 가능) → `99 >= all(99, 100, 101)`는 거짓

- 예시

```sql
-- salaries 테이블에서 from_date가 2000-12-31 이전인 사람들의 급여 중 하나의 급여 보다 더 적은 급여를 받은 직원의 급여 정보를 모두 출력해보세요.

select * from salaries
where salary < any
(select salary from salaries where from_date < '2000-12-31');


-- salaries 테이블에서 from_date가 2000-12-31 이전인 사람들의 급여 중 모든 급여보다 적은 급여를 받은 직원의 급여 정보를 모두 출력해보세요.

select * from salaries
where salary < all
(select salary from salaries where from_date < '2000-12-31');
```

- 즉 <any 는 최댓값을, <all 은 최솟값을 찾아서 비교할 수 있다.

#### 스칼라 서브쿼리

`SELECT` 절에서 사용하는 서브쿼리

오로니 한 행만 반환하며, JOIN을 사용한 것과 같은 결과를 나타낸다.

데이터가 많은 경우, JOIN을 쓸 때보다 계산 속도가 빨라진다.

```sql
-- 예
SELECT students.name, (
	SELECT math
	FROM middle_test as m
	WHERE m.student_id = students.student_id
) AS middle_avg
FROM students;
```
