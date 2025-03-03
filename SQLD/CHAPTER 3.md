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

- 특정 조건을 만족하는 행만을 대상으로 연산을 수행
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