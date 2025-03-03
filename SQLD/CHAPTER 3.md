## CHAPTER 3.1 : 관계형 데이터베이스 개요

- 관계형 데이터 베이스란?
	- E.F.Codd 박사의 정규화 이론에 따라 데이터의 일관성 문제를 근복적으로 해결한 데이터베이스 시스템
	- <span style="background:#fff88f">2차원 구조의 행(Row)과 열(Column)로 구성된 테이블(Table)</span>
	- SQL(Structured Query Language)이라는 공통의 질의언어를 정의해 데이터 조회, 가공, 추출이 가능
- 관련 용어
	- 칼럼 헤더 / 스키마
	- <span style="background:#fff88f">열 (Column) / 필드(속성)</span>
	- <span style="background:#fff88f">행 (Row) / 레코드(튜플)</span>
	- 하나의 데이터 행 = 인스턴스
	- TABLE , Relation (릴레이션)
		- 관계형 데이터 베이스는 모든 데이터를 2차원 테이블 형태로 표현한다.
		  2차원 테이블 형태는 우리가 엑셀을 작성할 때 흔히 사용하는 표 형식을 떠올리면 이해하기 쉬울 것이다.
	- SQL (Structured Query Language)
		- SQL은 관계형 데이터베이스에서 데이터를 다루기 위해 사용하는 언어이다.

---

### SELECT 문
-  저장되어 있는 데이터를 <span style="background:#fff88f">조회</span>하고자 할 때 사용하는 명령어이다.
-  WHERE절을 통해 특정 조건의 열만 조회할 수 있다.
-  테이블에 대한 별명(Alias) 부여 가능 

```
SELECT 컬럼1 , 컬럼2, ... FROM 테이블 WHERE 컬럼1 = '아무개';
SELECT * FROM 테이블;
SELECT 회원번호 AS ID, 테이블;

```

#### SQL문 구성과 실행 순서
```
[SELECT문 구성]

<절>
SELECT              5. 지정된 칼럼을 조회하며,
FROM      1.테이블로부터,
WHERE        2.조건에 맞는 행을 필터링하고
GROUP BY       3. 그룹핑을 한 뒤
HAVING           4. 조건에 맞는 그룹만 필터링하고, (이때, 그룹별 집계함수 사용가능)
ORDER BY              6. 정렬을 수행한다.

```

> F -> W -> G -> H -> S -> O  ( Fxx.. 왜 구했소)

---
## CHAPTER 3.2 : TRIM

### TRIM
- 문자열의 양 끝단에서 공백 또는 지정된 문자열을 제거하고 반환한다.

```
TRIM([[arg1] [arg2] FROM] arg3)

- arg1 : LEADING / TRAILING / BOTH, 생략될 경우 기본값은 BOTH이다.
- arg2 : 제거할 특정 문자 또는 문자열. arg2가 생략되면 공백을 제거한다.
- arg3 : 문자열값 또는 문자열형의 칼럼
```

```
SELECT TRIM('  GOOD. ') FROM DUAL;

[해설]
1. 문장의 앞, 뒤에서 공백 제거
```

#### LEADING
```
SELECT TRIM(LEADING 'G' FROM  'GOOD') FROM DUAL;

[해설]
1. 'GOOD'의 앞에서 'G' 제거
```

#### TRAILING
```
SELECT TRIM(TRAILING '라' FROM '가나다라') FROM DUAL;

[해설]
1. 문장의 뒤에서 '라' 제거
```

#### BOTH
```
SELECT TRIM(BOTH '가' FROM  '가나다라가') FROM DUAL;

[해설]
1. '가나다라가'의 앞, 뒤에서 '가' 제거
```

#### LTRIM  = LEADING
#### RTRIM = TRAILING

#### 문자열
- TRIM
- SUBSTR
- UPPER
- LOWER
- LENGTH

---

## CHAPTER 3.2 : NULL 관련 함수

#### NVL
- 첫 번째 인자가 Null이 아니면 첫 번째 인자를 그대로 반환하고 Null 이면 두 번째 인자를 반환
	(SQL Server에서는 NVL 대신 ISNULL을 사용)
- NULL이 아닌경우에는 관심 없다 그대로쓴다 근데 NULL이면 우리가 정해놓은 어떤 값으로 바꾼다.

```
SELECT EMPNO, ENAME, NVL(COMM, 0) AS COMM FROM EMP;

[해설]
1. COMM 칼럼의 값이 NULL 인 것을 0으로 치환하여 반환
```

#### NULLIF
- 입력된 두 인자가 같으면 Null을 반환하고 다르면 첫 번째 인자를 반환

```
NULLIF(arg1, arg2) 

[해설]
1. arg1: 칼럼 또는 표현식.
2. arg2: 칼럼 또는 표현식. 단, arg1과 같은 데이터 타입을 가져야 한다.
```

#### COALESCE - 결합하다, 합체하다
- 입력된 인자를 순서대로 평가해 NULL이 아닌 첫 번째 인자를 반환

```
SELECT EMPNO, COALESCE(HOURLY_WAGE, SALARY, COMMISSION) AS TOTAL_SALARY FROM WAGES;

[해설]
1. HOURLY_WAGE, SALARY, COMMISSION 칼럼 순으로 읽어서 NULL이 아닌 첫 번째 값을 반환
```

---

## CHAPTER 3.3 : WHERE 절

- 특정 조건을 만족하는 행만을 대상으로 연산을 수행 (조건절이다)
- SELECT, UPDATE, DELETE에 사용가능하나 INSERT에는 사용불가
- FROM 절을 먼저 수행하므로 FROM절에서 정의한 테이블에 대한 별명(Alias)은 사용할 수 있으나,
	SELECT절에서 정의한 칼럼에 대한 별명은 사용할 수 없다.

```
SELECT * FROM EMP WHERE DEPTNO = 30;

[해설]
1. EMP 테이블에서
2. DEPTNO가 30인
3. 모든 행을 조회
```

```
UPDATE EMP SET JOB = 'SALESMAN' WHERE DEPTNO = 30;

[해설]
1. DEPTNO가 30인 행에 대해서 JOB 칼럼의 값을 'SALESMAN'으로 변경한다.
```

> 주의! WHERE절에서 NULL과의 동등/부정 비교
- WHERE절에서의 NULL과의 동등/부정 비교는 IS NULL, IS NOT NULL만 사용 가능하다.
- NULL, 칼럼!=NULL 등 비교연산자를 이용한 조건식은 제대로 동작하지 않는다. 
	결과는 FALSE가 나온다

> FROM 절에서의 별명은 WHERE절에서 사용가능하다 
> <span style="background:#fff88f">하지만 SELECT 절에서의 별명은 WHERE절에서 사용 불가 하다.</span>

---
## CHAPTER 3.5 : GROUP BY 절

- 특정 값을 기준으로 데이터를 그룹핑할 때 사용

```
SELECT DEPTNO, SUM(SAL) AS SALS FROM EMP GROUP BY DEPTNO;

[해설]
1. EMP 테이블에서
2. DEPTNO로 그룹핑 한 후,
3. DEPTNO별 SAL의 합계를 조회
```


#### 집계함수
- GROUP BY 절로 그룹핑한 후에는 해당 그룹에 대해 집계함수를 사용하여 개수, 합 , 평균 등을 계산
- 집계함수는 그룹을 대상으로 연산을 수행하므로, GROUP BY 절 없이 집계함수를 사용하면 전체 행을 하나의 그룹으로 간주하고 연산 수행

><span style="background:#fff88f"> 칼럼명 앞에 DISTINCT를 붙이면 칼럼값의 중복을 제거하므로, 집계함수의 인자로 입력되는 칼럼명에 DISTINCT를 사용하면 해당 그룹에서 첫 번쨰 행에 대해서만 연산을 수행</span>


### HAVING 절
- GROUP BY 연산이 끝난 결과에 대해서 HAVING절의 조건을 만족하는 그룹만 추출
- WHERE 절하고 다른점?
	- 집계함수를 쓸 수 있다.

```
SELECT ID FROM TBL
GROUP BY ID
HAVING COUNT(*) = 2;

[해설]
1. TBL 테이블에서
2. ID로 그룹핑 한 후,
3. ID별로 그룹핑한 개수가 2인
4. ID를 조회
```

### ORDER BY 절 ⭐️
- ORDER BY 절은 SELECT 문에서 논리적으로 맨 마지막에 수행된다.
```
SELECT 문의 논리적 수행 순서

1. SELECT
2. FROM
3. WHERE
4. GROUP BY
5. HAVING
6. ORDER BY

```

- ORDER BY 절을 이용해서 SELECT한 데이터를 정렬할 수 있으며 ORDER BY 절을 따로 명시하지 않으면 데이터는 임의의 순서대로 출력된다. ORDER BY 절 뒤에는 정렬의 기준이 되는 컬럼이 오게 되는데 컬럼은 하나가 될 수도 있고 그 이상이 될 수도 있다. 또한 ORDER BY 절 뒤에 오는 컬럼에는 옵션이 붙을 수 있으며 종류는 다음과 같다.

```
- ASC(Ascending) : 오름차순
- DESC(Descending) : 내림차순
- 옵션 생략 시 ASC가 기본값이 된다.
- 정렬의 기준이 되는 컬럼에 NULL 데이터가 포함되어 있을 경우 데이터 베이스 종류에 따라 정렬의 위치가 달라진다
Oracle의 경우에는 NULL을 최댓값 취급 -> 오름차순의 경우에 맨 마지막에 위치
SQL Server는 반대

순서를 변경하고 싶다면 NULLS FIRST< NULLS LAST 옵션을 써서 정렬 순서를 변경 가능하다
```

> <span style="background:#fff88f">ORDER BY 절은 논리적으로 SELECT 절 다음에 수행되기 때문에 SELECT 절에서 장의한 ALIAS를 사용할 수 있다. ORDER BY 절의, 옵션은 각 칼럼 다음에 하나씩 붙여서 작성해야한다.</span>


## CHAPTER 3.5 : JOIN ⭐️⭐️⭐️

- 두 개의 테이블을 하나로 병합하는 연산

>UNION = 합집합

### EQUI JOIN
- Equal(=), 즉 등식을 조건으로 사용할 때 발생하는 조인
- 칼럼 값이 정확하게 일치할 때에 성립하는 것으로 WHERE절의 조건이 등식인 경우 
- 예를 들어 한 쇼핑몰에서 sqlchild라는 아이디를 가진 사람이 온라인으로 마우스를 구매하고 리뷰를 작성했다고 하자, 마우스는 상품 테이블의 데이터일 것이고 리뷰는 리뷰 테이블에 저장될 것이다.
### Non EQUI JOIN
- WHERE절의 조건이 등식이 아닌 부등식 (BETWEEN, >, >=, <=)을 사용해 범위를 나타낸 조건일 때 발생하는 조인
- 예를 들어 이벤트 기간 동안 리뷰를 작성한 고객에게 사은품을 주는 행사를 하고 있다고 가정해보자, 이 경우 리뷰 테이블과 이벤트 테이블이 JOIN되어야 할 것 이다.


### INNER JOIN
- 교집합의 개념으로 기준이 되는 키에 따른 칼럼값이 존재하는 것만 병합

```
SELECT A1.ACTOR_NO ACT_N01, A2.ACTOR_NO ACT_N02, A2.MOVIE_NO, A1.NAME, A1.GENDER
FROM ACTOR A1 INNER JOIN APPR A2 ON A1.ACTOR_NO = A2.ACTOR_NO;
```
### OUTER JOIN
- 합집합의 개념으로, 기준이 되는 키에 따른 칼럼값이 존재하지 않더라도 모든 튜플을 병합
-  LEFT OUTER JOIN : 왼쪽 테이블의 모든 행을 포함시키면서 조인
-  RIGHT OUTER JOIN : 왼쪽 테이블의 모든 행을 포함시키면서 조인
-  FULL OUTER JOIN : 모든 테이블의 모든 행을 포함시키면서 조인

> 💡 오라클 문접중에 OUTER JOIN을 나타내는 문법이 있다
- FROM T1 T2 <span style="background:#d4b106">(+)</span> <- 시험 가능성

### NATURAL JOIN ⭐️⭐️
- 조인의 대상이 되는 두 테이블에서 같은 이름의 칼럼에 대해서는 동일한 칼럼값을 가지는 행만 병합

```
SELECT * FROM ACTOR NATURAL JOIN APPR;
```

### CROSS JOIN ⭐️⭐️
- 왼쪽 테이블의 각 행에 대한 오른쪽 테이블 모든 행의 대응을 조합하여 결과를 출력
- 왼쪽 테이블이 M행이고 오른쪽 테이블이 N행이면 CROSS JOIN의 결과는 MxN 행

```
SELECT S1.NAME, C1.NAME FROM STUDENT S1, CLUB C1;
=
SELECT S1.NAME, C1.NAME FROM STUDENT S1 CROSS JOIN CLUB C1;

둘 다 같은 의미
```

---
