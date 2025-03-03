## CHAPTER 4.1 : 서브쿼리 ⭐️⭐️⭐️⭐️

### 서브쿼리란? 


```
SELECT E.EMPNO,
		E.ENAME,
		D.DEPTNO,
		D.DNAME,
		D.LOC
FROM EMP E,
		(SELECT DEPTNO,
				DNAME,
				LOC
				FROM EDPT) D
WHERE E.DEPTNO = D.DEPTNO;

```

#### 서브쿼리 위치에 따른 구분

- 스칼라 서브쿼리 - SELECT문의 칼럼 입력 위치에 들어감
- 인라인 뷰 - FROM절의 테이블 입력 위치
- 중첩 서브쿼리 - WHERE절, HAVING절의 칼럼 또는 테이블 입력 위치

#### 메인쿼리의 칼럼 사용 여부에 따른 구분

- 연관 서브쿼리 - 메인쿼리의 칼럼을 서브쿼리에서 사용
- 비연관 서브쿼리 - 메인쿼리의 칼럼을 서브쿼리에서 사용하지 않음

1.<span style="background:#d4b106"> 서브쿼리에서는 메인쿼리의 칼럼을 사용가능하다!</span>
	1. 사용을 했으면 연관 서브커리
	2. 사용을 안했다면 비-연관 서브커리

---

### 스칼라 서브쿼리

- 주로 SELECT 절에 위치하지만 컬럼이 올 수 있는 대부분 위치에 사용할 수 있다. 컬럼 대신 사용되므로 반드시 하나의 값만을 반환해야 하며 그렇지 않은 경우 에러를 발생시킨다.

```
SELECT M.PRODUCT_CODE,
		(SELECT S.PRODUCT_NAME
			FROM PRODUCT S
			WHERE S.PRODUCT_CODE = M.PRODUCT_CODE) AS PRODUCT_NAME,
		M.MEMBER_ID,
		M.CONTENT
FROM PRODUCT_PREVUEW M;
```
- 스칼라 서브쿼리를 이용하여 PRODUCT_REVIEW 테이블에 존재하지 않는 PRODUCT_NAME 데이터를 출력

### 인라인 뷰

- FROM 절 등 테이블명이 올 수 있는 위치에 사용 가능하다.

```
SELECT M.PRODUCT_CODE,
		S.PRODUCT_NAME
		S.PRICE,
		M.MEMBER_ID,
		M.CONTENT
FROM PRODUCT_PREVUEW M;
		(SELECT PRODUCT_CODE,
				PRODUCT_NAME,
				PRICE
			FROM PRODUCT) S
WHERE M.PRODUCT_CODE = S.PRODUCT_CODE;
```

#### 중첩 서브쿼리 ⭐️⭐️⭐️

- 쿼리 안 특히 WHERE 절과 HAVING 절에 다른 쿼리가 중첩되어 들어간 경우를 지칭
- 반환하는 값의 형태가 하나가 아니며, 다양한 반환값을 지님

 
| <center>반환값 유형</center> | 설명                                                                                        |
| ----------------------- | ----------------------------------------------------------------------------------------- |
| 단일행                     | 반환되는 값이 단일행, 즉 1건 이하의 데이터인 경우이다. <br>단일행 비교연산자 ( >,>=, <, <=, =, <> 등)의 연산 대상으로 사용할 수 있다. |
| 다중행                     | 반환되는 값이 다중행인 경우로 다중행 비교연산자 (IN, ALL, ANY, SOME, EXISTS등)의 <br>연산 대상으로 사용할 수 있다.           |
| 다중칼럼                    | 반환되는 값이 여러 칼럼, 즉 벡터인 경우이다. 반환 값이 단일 칼럼인 스칼라 서브쿼리와 달리 반환값이 여러 칼럼을 가진 테이블 형태가 된다.           |


---

#### 중첩 서브쿼리 예제 1 - 단일행
```
SELECT EMPNO,
		ENAME,
		SAL
FROM EMP
WHERE SAL >= (SELECT AVG(E.SAL) FROM EMP E);

-----------------------------------------------------------
- 급여(SAL)가 평균 이상인 사원 정보를 조회한다.

[해설]
- 서브쿼리 : [EMP] 테이블에서 급여의 평균을 구한다
- 메인쿼리 : 서브쿼리를 WHERE절에서 조건식의 일부로 사용하고 있다. 급여가 서브 쿼리로 구한 평균값 이상인 건을 출력한다.
```

#### 중첩 서브쿼리 예제 2 - 다중행
```
SELECT EMPNO,
		ENAME,
		SAL,
		DEPTNO
FROM EMP
WHERE DEPTNO IN (SELECT AVG(E.SAL) FROM EMP E);

-----------------------------------------------------------
- 급여(SAL)가 1000 이하인 부서원이 있는 부서에 속한 사원 정보를 조회한다.

[해설]
- 서브쿼리 : [EMP] 테이블에서 급여가 1000이하인 행의 부서번호를 DISTINCT로 조회하여 중복되지 않은 부서 번호 리스트를 구한다.
- 메인쿼리 : IN 연산자를 사용하여 EMP 테이블 내 부서번호가 서브쿼리의 결과에 속한 건만 조회한다.
```

> 💡 IN 연산자 
-  OR 연산자와 = 연산자가 합쳐져 있다고 생각하면 쉽다.
> 💡 EXISTS 연산자 
- 존재 하는지 안하는지 반환

#### 중첩 서브쿼리 예제 3 - 다중칼럼
```
SELECT EMPNO,
		ENAME,
		JOB,
		SAL
FROM EMP
WHERE (JOB, SAL) IN (SELECT JOB, MAX(SAL) FROM EMP GROUP BY JOB);

-----------------------------------------------------------
- 직업(JOB)별로 가장 급여가 높은 사원 정보를 조회한다.

[해설]
- 서브쿼리 : [EMP] 테이블에서 직업에 대해 GROUP BY를 수행하여 직업별 급여의 최댓값을 MAX(SAL)로 구하여 출력한다
- 메인쿼리 : 직업과 급여가 서브쿼리의 결과와 매칭되는 사원 정보를 출력한다.
```

---

## CHAPTER 4.2 : VIEW

- 특정 SELECT 문에 이름을 붙여서 재사용이 가능하도록 저장해놓은 오브젝트이다. SQL에서 테이블처럼 사용할 수 있으며 앞서 배운 인라링 뷰를 뷰로 정의한다고 가정해보면 쿼리 작성 시 인라인 뷰가 들어가는 위치에 뷰 이름만 기술하게 될 것이다.

```
CREATE OR REPLACE VIEW DEPT_MEMBER AS
	SELECT A.DEPARTMENT_ID,
			A.DEPARTMENT_NAME,
			B.FIRST_NAME,
			B.LAST_NAME
	FROM DEPARTMENTS A
	LEFT OUTER JOIN EMPLOYEES B
		ON A.DEPARTMENT_ID = B.DEPARTMENT_ID;
```

- 혼돈하지 말아야 할 점은 뷰는 가상 테이블 이라는 것이다. 따라서 실제 데이터를 저장하지는 않고 해당 데이터를 조회해오는 SELECT 문만 가지고 있다.

---

## 집합연산자

- UNION ALL - 각 쿼리의 결과 집합의 합집합이다. 중복된 행도 그대로 출력된다.
- UNION - 각 쿼리의 결과 집합의 합집합이다. 중복된 행은 한 줄로 출력된다.
- INTERSECT - 각 쿼리의 집합의 교집합이다. 중복된 행은 한 줄로 출력된다. (헤더 값은 첫번째 쿼리를 따라간다)
- MINUS / EXCEPT - 앞에 있는 쿼리의 결과 집합에서 뒤에 있는 쿼리의 결과 집합을 뺸 차집합이다. 중복된 행은 한 줄로 출력 된다.

---

## CHAPTER 4.2 : 그룹함수

- GROUP BY절의 연산 결과에 대해 그룹 별로 연산을 수행하는 함수
- 집계함수, ROLLUP, CUBE, GROUPING SETS 등

### ROLLUP
- GROUP BY절에 들어가는 칼럼을 대상으로 하위 그룹핑을 수행
- 그룹별 소계 & 총계
- ex) GROUP BY ROLLUP(날짜, 이름) ------ (날짜, 이름) -> (날짜) -> (전체) 순서로 하위 그룹핑

```
SELECT CYL, COINT(*)
FROM MTCARS
GROUP BY ROLLUP(CYL)
ORDER BY CYL;

-------------------------
실린더 수 별로 그룹핑하여 그룹 별 개수를 구하고, 총계를 구한다.
```

### CUBE
- 조합 가능한 모든 경우로 그룹핑을 수행
- ex) GROUP BY CUBE(날짜, 이름) ------ (날짜, 이름) -> (날짜) -> (이름) -> (전체) 순서로 하위 그룹핑

```
SELECT CYL, GEAR, COINT(*)
FROM MTCARS
GROUP BY CUBE(CYL, GEAR)
ORDER BY CYL, GEAR;

-------------------------
실린더 수, 기어 수 별로 그룹핑하여 그룹 별 개수를 구하고, 실린더 수 별 소계, 기어 수 별 소개, 총계를 구한다.
```

### GROUPING SETS
- 그룹핑 대상을 지정하는 함수
- GROUPING SETS의 인자에 ROLLUP이나 CUBE 함수를 넣을 수 있으며,
	이런 경우에는 ROLLUP이나 CUBE의 그룹핑 결과인 소계, 총계들이 각각 별개의 인자로 지정된 것과 같은 결과를 반환한다.

```
SELECT CYL, GEAR, COINT(*)
FROM MTCARS
GROUP BY GROUPING SETS(CYL, GEAR)
ORDER BY CYL, GEAR;

-------------------------
실린더 수, 기어 수 별로 그룹핑하여 그룹 별 개수를 구한다.
```

### GROUPING
- ROLLIP, CUBE, GROUPING SETS와 함께 쓰이며 소계에 해당하는 결과 행과 그렇지 않은 행을 구분
- 소계에 해당하는 결과 행의 결우에는 1을 반환하고 그렇지 않은 경우에는 0을 반환
- GROUPING함수와 CASE문을 사용하여 소계나 총계를 표시하는 행에 대해서 그 의미에 맞는 텍스트를 값으로 지정가능

```
SELECT CASE GROUPING(CYL)
		WHEN 1 THEN '총계' ELSE TO_CHAR(CYL)
		END AS CYL,
		COUNT(*)
FROM MTCARS
GROUP BY ROLLUP(CYL)
ORDER BY CYL;
```

