---
title: SQL Cheat Sheet
date: 2022-07-06
tags:
    - Database
    - SQL
---

이 글은 MySQL의 SQL 쿼리 중 DML(Data Manipulation Language), 즉 테이블의 행을 검색 및 변경하는 기능을 정리하고 또 찾아보기 위한 목적으로 작성했다.

## 들어가며

> [여기](https://www.w3schools.com/mysql/trymysql.asp?filename=trysql_select_all)서 웹으로 SQL 쿼리를 실행해 볼 수 있다.

- SQL 쿼리는 대문자와 소문자를 구분하지 않는다. 즉, 쿼리를 대문자로 쓰던 소문자로 쓰던 섞어서 쓰던 같은 결과를 도출한다. 아래에서 다루겠지만, 함수뿐만이 아닌 문자열 데이터도 대문자와 소문자를 구분하지 않는다.

- SQL 쿼리의 한 줄 주석은 `-- {주석}` 형태로 쓸 수 있다.


## 테이블 내 탐색

### SELECT

데이터베이스에서 테이블의 행을 검색하기 위한 가장 기본적인 함수는 `SELECT {칼럼(column)의 이름, 또는 불러올 값}`이며 칼럼을 검색하는 경우 뒤에 `FROM {테이블}`로 칼럼이 속한 테이블 이름을 명시한다.

```SQL
SELECT 
    col1 AS First, 
    col2, 3, 'sql', NULL
FROM table1
```

- 전체 행을 선택하기 위해 `SELECT *` 문을 쓸 수 있다.
- `SELECT` 뒤에는 상수, 문자열, NULL 값을 쓸 수 있다.
- 검색 결과에 가져올 칼럼의 이름을 바꾸려면 `SELECT {본래 이름} AS {바꿀 이름}`을 활용한다.


나아가 `WHERE {행 조건}`으로 검색할 행의 조건을 지정할 수 있으며 `ORDER BY {정렬 기준}`으로 행을 정렬할 수 있다. 또한 `LIMIT {가져올 갯수}`를 통해 가져올 행의 개수를 설정할 수 있다.

```SQL
SELECT col1, col2, col3
FROM table1
WHERE condition1
ORDER BY sort_standard1
LIMIT integer1
```

- `ORDER BY`는 디폴트로 오름차순으로 정리하며, `ORDER BY {정렬 기준} ASC`으로 오름차순을 명시할 수 있다. 내림차순은 `ORDER BY {정렬 기준} DESC`를 활용한다.
- `LIMIT {건너뛸 갯수}, {가져올 갯수}`로 인자를 두개 입력할 수 있다. 이 경우, `건너뛸 갯수 : 건너뛸 갯수 + 가져올 갯수` 범위만큼 검색한다.


행을 그룹으로 묶어서 검색할 수도 있다. `GROUP BY {그룹들}`은 그룹별 값을 도출하며, `HAVING {그룹 내 조건}`을 통해 그룹으로 묶은 뒤의 조건을 설정할 수 있다.

```sql
SELECT 
    col1, 
    COUNT (DISTINCT col2)
FROM table1
WHERE condition1
GROUP BY col1
HAVING in_group_condition1
```

- `GROUP BY {그룹들}`은 복수의 기준으로 그룹을 묶을 수 있다.
- `WHERE`가 그룹하기 전에 조건을 기준으로 하는 필터라면, `HAVING`은 그룹한 이후의 필터이다. 
- `DISTINCT`는 중복 값을 제거하는 연산이며, 종합하여 `COUNT (DISTINCT {칼럼})`은 칼럼 내의 고유한 값들의 개수를 세어 반환한다.


### 연산자

SQL 쿼리의 연산자는 파이썬과 비슷하기도, 다르기도 하다.

사칙연산  `+, - , *, /`과 나머지 연산 `%, MOD`을 사용할 수 있다.

```sql
SELECT (col1 + col2) / 2, col1 MOD 3
FROM table1
```

논리 연산자로 `{값} IS /NOT/ {True 또는 False}`을 사용할 수 있다.
```sql
SELECT col1 IS TRUE, col2 IS NOT TRUE
FROM table1
```

논리 연산자로 `AND 또는 &&`와 `OR 또는 ||`를 사용할 수 있다.
```sql
SELECT col1, col2
FROM table1
WHERE col1 IS TRUE AND col2 IS NOT TRUE
```

등호 `=`와 부등호 `!= 또는 <>`를 사용할 수 있다. 등호는 파이썬과 다르게 `=`로 나타냄에 주의하자.
```sql
SELECT col1, col1 = col2 AS Equality, col3
FROM table1
WHERE col3 <> 1
```

`/NOT/ BETWEEN {최소값} AND {최대값}`로 데이터의 범위를 검증할 수 있다. 
```sql
SELECT col1, col2
FROM table1
WHERE col2 BETWEEN 0 AND 5
```

데이터가 어떤 값 사이에 있는지 `/NOT/ IN ({값들})`로 검증할 수 있다.
```sql
SELECT col1 IN ('good', 'fair', 'excellent'), col2
FROM table1
```

최소값 `MIN({값들})`, 최대값 `MAX({값들})`, 개수 `COUNT({값들})`, 합 `SUM({값들})`, 평균 `AVG({값들})`을 구할 수 있다.
```sql
SELECT
    MAX(col1),
    MIN(col1),
    COUNT(col1),
    SUM(col1),
    AVG(col1)
FROM table1
```
- `COUNT({값들})`는 NULL 값을 제외한 행의 개수를 센다.


### 문자열

파이썬에는 정규표현식 개념이 있어서 패턴에 맞는 문자열을 검색할 수 있다. SQL에도 비슷한 기능이 있으며, `LIKE {...%패턴...}`형식 또는 `LIKE {..._패턴...}`으로 구현한다. 전자는 `%`에 해당되는 문자가 없거나 여러개의 문자가 해당될 수 있는 반면, 후자인 `_`에는 한 개의 문자만 대응 될 수 있다. 또한, 글의 초반에 언급한 대로 SQL 문자열은 대문자와 소문자를 구분하지 않는다.

```sql
SELECT
    'SQL' LIKE '%sql%', -- True
    'SQL' LIKE 'sql%',  -- True
    'SQL' LIKE 'S%',    -- True
    'SQL' LIKE '%L',    -- True
    'SQL' LIKE 'S%L',   -- True
    'SQL' LIKE '_ql',   -- True
    'SQL' LIKE '_q_',   -- True
    'SQL' LIKE '__ql_', -- False
```

또한 `REPLACE({문자열}, {대체될 문자열}, {대체할 문자열})`을 활용해 부분 문자열을 문자열에서 찾아 다른 문자열로 대체할 수 있다.
```sql
SELECT
    REPLACE('It is 12 AM', 'AM', 'PM') -- It is 12 PM
```

입력한 문자열을 순서대로 이어주는 파이썬의 join과 비슷한 기능을 하는 함수도 있다. `CONCAT ({문자열들})`은 입력 문자열을 그대로 이어주고, `CONCAT_WS({연결 문자열}, {문자열들})`을 통해 첫번쨰 인자로 전달한 문자열을 이용해 다음 인자로 주어진 문자열들을 잇는다.

```sql
SELECT CONCAT('Hi', ' ', 'there ', '!'), -- Hi there !
SELECT CONCAT_WS('-', 2022, 7, 8, 'AM') -- 2022-7-8-AM
```
- 문자열이 아닌 정수값을 입력해도 문자열로 이은 결과를 반환한다.


문자열을 왼쪽 또는 오른쪽에서 자를 수도 있다. 왼쪽에서부터 자르기 위해서 `LEFT({대상 문자열}, {자를 길이})`를 활용하고, 오른쪽에서 부터 자르기 위해 `RIGHT({대상 문자열}, {자를 길이})`를 활용한다.

```sql
SELECT
    LEFT('yyyy-mm-dd hh:mm', 10) AS DATE, -- yyyy-mm-dd
    RIGHT('yyyy-mm-dd hh:mm', 5) AS TIME -- hh:mm
```

문자열의 왼쪽 또는 오른쪽에 패딩을 할 수 있다. 왼쪽 패딩을 위해서는 `LPAD ({대상 문자열}, {패딩 횟수}, {패딩 문자})`을, 오른쪽 패딩을 위해서는 `RPAD ({대상 문자열}, {패딩 횟수}, {패딩 문자})`을 활용한다.

```sql
SELECT
  LPAD('This is padding', 3, '.'), -- ...This is padding
  RPAD('This is padding', 3, '.')  -- This is padding...
```

공백을 줄여주는 함수도 있다. `TRIM {문자열}`을 통해 입력 문자열의 양쪽 공백을 제거할 수 있다.
```sql
SELECT
    TRIM(' trim me ') -- 'trim me'
```

### 시간/날짜

MySQL에는 날짜와 시간을 나타내는 변수 자료형 DATETIME이 있다. 

DATETIME 자료형을 생성하는 방법은 `DATE({날짜 문자열})` 또는 `TIME({시간 문자열})`을 이용하는 것이다.

```sql
SELECT
  '2022-7-1' = '2022-07-01', -- FALSE
  DATE('2022-7-1') = DATE('2022-07-01'), -- TRUE
  '1:18:4' = '01:18:04', -- FALSE
  TIME('1:18:4') = TIME('01:18:04'); -- TRUE
```

- `DATE`와 `TIME`에 전달하는 문자열은 0을 제외해도 무방하다.

DATETIME에서 연도, 월, 요일명, 날짜, 시간, 분, 초를 추출하는 함수는 각각 `YEAR({DATETIME})`, `MONTH({DATETIME})`, `DAYNAME({DATETIME})`, `DAY({DATETIME})`, `HOUR({DATETIME})`, `MINUTE({DATETIME})`, `SECOND({DATETIME})`이다.

```sql
SELECT
  YEAR(NOW()) AS YEAR,
  MONTH(NOW()) AS MONTH,
  DAYNAME(NOW()) AS DAYNAME,
  DAY(NOW()) AS DAY,
  HOUR(NOW()) AS HOUR,
  MINUTE(NOW()) AS MINUTE,
  SECOND(NOW()) AS SECOND
```
- `NOW()`는 현재 날짜와 시간을 DATETIME 자료형으로 반환한다.

### 조건문

파이썬의 if 조건문에 비할 바는 아니지만, SQL에서도 조건에 따라 다른 값을 반환하도록 지정할 수 있다. `IF ({조건}, {조건이 참일 때 반환값}, {조건이 거짓일 때 반환값})` 형식으로 사용한다.

```sql
SELECT 
    IF (col1 > 3, 'Bigger than 3', 'Equal or Smaller than 3')
FROM table1
```

반환하고자 하는 경우의 수를 더 세세하게 나눌 수도 있다. `CASE WHEN {경우1} THEN {결과값1} ... ELSE {이외의 경우 반환값} END` 형식으로 나타내며, `WHEN {경우} THEN {결과값}`은 추가할 수 있다. 만약 앞서 나온 조건에 해당되는 경우 이미 반환값이 결정되었을 것이므로, 뒤에 나온 조건은 앞서 나온 조건들에 해당되지 않는 경우만 포함한다.

```sql
SELECT
    CASE
        WHEN -1 > 0 THEN '-1은 양수다.'
        WHEN -1 = 0 THEN '-1은 0이다.'
        ELSE '-1은 음수다.'
    END
```

검색한 행에 NULL값이 있고 이 값만 변경하고자 하면 `IF ({비교 대상} = NULL, {바꿀 값}, {비교 대상})`으로 표현할 수 있다. 특별히 이와 같은 작업을 하는 함수가 있으며, `IFNULL({비교 대상}, {바꿀 값})`으로 간단하게 표현한다.

```sql
SELECT
    IFNULL(col1, '0')
FROM table1
```



## 테이블 합치기

### 서브쿼리

서브쿼리란 쿼리를 통해 검색하고자 하는 값을 임시로 불러와 쿼리 안에 쿼리를 사용하는 방법이다. 바로 다음에 나오는 `JOIN`과 같이 외부 테이블의 값을 가져오는 방법 중의 하나이지만, 데이터를 저장하지 않고 임시로 사용한다는 점에서 차이가 있다. 서브 쿼리는 괄호내에 쿼리문을 작성하는 `(SELECT ... FROM ...)` 형식으로 사용할 수 있다. 한가지 예는 다음과 같다. 

```sql
SELECT col1, col2
FROM table1
WHERE col2 IN (SELECT col3 FROM table2)
ORDER BY col1
```

여기서는 `WHERE`의 조건문에 서브쿼리를 사용하여 외부 테이블의 값을 가져왔다. 


### JOIN

원하는 정보가 여러 테이블에 흩어져 있는 경우, 몇 개의 외부 테이블을 합치는 명령어가 필요하다. Pandas 패키지에서 데이터프레임을 병합하는 경우를 생각해보자(사실 pandas 공식 문서에는 merge를 SQL의 JOIN을 들어 설명하고 있지만, 이미 pandas를 사용해본 경험이 있다면 이해가 빠를 것이다). `df1.merge(df2, how='inner', on='col1')`명령어는 df1에 df2를 양쪽 데이터프레임에 공통적으로 존재하는 col1의 값들이 일치되도록 병합한다. 반면 `df1.merge(df2, how='left', on='col1')`은 왼쪽, 즉 df1의 키만 보존하며 두 데이터프레임을 병합한다.

이처럼 테이블을 합치는 방법은 여러가지가 있으며, 여기서는 INNER, OUTER, CROSS JOIN을 소개한다. 


#### INNER JOIN

INNER JOIN을 위해서는 합치고자 하는 테이블에 공통된 키가 있어야 한다. 쿼리는 `JOIN {외부 테이블} ON {공통된 칼럼}` 형식이다. 공통된 키에 대해 병합되기 때문에 공통된 칼럼에 대한 두 테이블의 교집합을 떠올릴 수 있다(단, 엄밀히 테이블은 집합이 아니다!).

```sql
SELECT A.col1, A.col2, B.col2, C.col2
FROM table1 A
JOIN table2 B
  ON A.col1 = B.col1
JOIN table3 C
  ON A.col2 = C.col3
WHERE condition1
ORDER BY sort_standard1
```
- `FROM table1 A`는 table1을 A로 명명하고, `JOIN table2 B`는 table2를 B로 명명한다. 이후 어느 테이블에서 행을 가져오는지 명시할 때 새로 명시한 이름을 사용할 수 있다.
- 위의 예에서 처럼 여러개의 테이블을 병합할 수 있다. 쿼리 형식 `JOIN {외부 테이블} ON {공통된 칼럼}`만 지키면 된다.
- 또한, `WHERE`나 `ORDER BY`등 `SELECT`에서 익힌 조건문들도 그대로 사용할 수 있다는 걸 잊지 말자.


그렇다면 하나의 테이블을 두번 JOIN할 일은 없을까? 의외로 하나의 테이블을 다시 외부 테이블처럼 JOIN하는 활용처도 있으며, 이를 SELF JOIN이라고 한다.

```sql
SELECT
    A.col1, A.col2 AS val,
    B.col1, B.col2 AS next_val
FROM table1 A 
JOIN table2 B
  ON A.col2 + 1 = B.col2
```
이처럼 하나의 칼럼에 lag를 두는 경우, 서로 다른 칼럼을 가져오는 것처럼 JOIN으로 불러와 활용할 수 있다. 

#### OUTER JOIN

OUTER JOIN은 공통된 키가 아닌 하나의 테이블이 가지는 키를 기준으로 합치는 방법이며, 합치고자 하는 방향에 따라 LEFT JOIN과 RIGHT JOIN으로 나뉜다. 쿼리는 `LEFT JOIN {외부 테이블}`과 `RIGHT JOIN {외부 테이블}`이다.

```sql
SELECT A.col1, B.col1, A.col2, A.col3
FROM table1 A
LEFT JOIN table2 B
       ON A.col2 = B.col2 AND A.col3 = B.col3
```

- `LEFT JOIN`을 `RIGHT JOIN`으로 바꿔 쓸 수 있다. 
- LEFT JOIN은 table1(또는 A)의 키를 기준으로 합치며, RIGHT JOIN은 table2(또는 B)의 키를 기준으로 합친다. 기준이 되지 않는 테이블에 있는 칼럼 값이 NULL이더라도 기준이 되는 칼럼 값이 존재하면 반환하므로, 결과값으로 반환되는 테이블의 행의 갯수는 기준이 되는 칼럼의 합치기 전 행의 갯수와 같다.

#### CROSS JOIN
두 칼럼 값의 모든 조합 쌍을 나타내는 카르테지안 프로덕트(Cartesian Product)를 실행하는 방법도 있다. `CROSS JOIN {외부 테이블}` 명령으로 구현하며, 이를 CROSS JOIN이라고 한다.

```sql
SELECT A.col1, B.col2
FROM table1 A
CROSS JOIN table2 B
```
결과값은 `A.col1`와 `B.col2`의 모든 조합 썅을 담은 테이블이다.



## 테이블 및 데이터 생성/수정/삭제

### 테이블 생성/수정/삭제

테이블을 생성하기 위해 `CREATE TABLE {테이블 이름} ({칼럼1}{칼럼1 자료형}{칼럼1 옵션들}, ..., {칼럼N}{칼럼N 자료형}{칼럼N 옵션들})` 명령어를 활용할 수 있다.

```sql
CREATE TABLE table1 (
    col1 INT AUTO_INCREMENT PRIMARY KEY,
    col2 VARCHAR(10) NOT NULL,
    col3 VARCHAR(10) UNIQUE NOT NULL,
    col5 INT UNSIGNED,
    col6 INT DEFAULT 0
)
```
- 옵션은 생략할 수 있다.
- `AUTO_INCREMENT`는 행을 생성 할 때마다 자동으로 1씩 증가하는 칼럼 옵션이다.
- `UNIQUE`는 중복 입력만 불가한 칼럼 옵션이다.
- `NOT NULL`은 NULL 값만 입력 불가한 칼럼 옵션이다.
- `PRIMARY KEY`는 중복 입력과 NULL 값 입력이 불가한 칼럼 옵션이다. `UNIQUE`와 `NOT NULL`을 합한 기능을 제공한다.
- `UNSIGNED`는 숫자 자료형에만 적용 가능하며, 양수만 입력받는 칼럼 옵션이다.

당연하게도 테이블 이름이나 컬럼의 옵션, 또는 컬럼 자체를 수정할 일이 발생한다. 테이블의 이름을 변경하는 명령어는 `ALTER TABLE {테이블 이름} RENAME TO {바꿀 이름}`이다. 테이블을 삭제하기 위해서는 `DROP TABLE {테이블 이름}`을 활용한다.

```sql
ALTER TABLE table1 RENAME TO better_table,
DROP TABLE better_table
```

컬럼의 이름 또는 자료형을 변경하는 명령어는 `ALTER COLUMN {칼럼 이름} {바꿀 이름} {바꿀 자료형}`이다. 즉 칼럼 이름을 통해 칼럼에 접근해 자료형을 수정할 수 있다. 컬럼을 추가하는 함수는 `ADD COLUMN {추가할 칼럼 이름} {자료형} {옵션}`이며, 삭제는 테이블과 비슷하게 `DROP COLUMN {칼럼 이름}`을 활용한다.

```sql
ALTER COLUMN col1 col1 INT,
ALTER COLUMN col2 cool_col VARCHAR(10), 
DROP COLUMN col3,
ADD COLUMN col1_2 INT AFTER col1
```
- 함수들을 `,`로 구분해 여러 함수를 한번에 실행할 수 있다.

### 데이터 추가/삭제/변형

테이블과 칼럼을 수정하는 방법을 알았으니, 데이터를 수정하는 방법도 필요한 것을 알 수 있다. 데이터란 `SELECT`문으로 검색하던 일련의 값들이 담긴 행을 의미한다.

데이터를 추가하기 위해서는 `INSERT INTO {테이블 이름} ({칼럼1, 칼럼2, 칼럼3}) VALUES ({칼럼1 값, 칼럼2 값, 칼럼3 값})`을 활용한다. 

```sql
INSERT INTO table1 ({col1, col2, col3})
     VALUES ({val1, val2, val3})
```

특정 조건에 해당하는 데이터를 삭제하기 위해서는 `DELETE FROM {테이블 이름} WHERE {조건문}`을 활용한다. 이 때 삭제되는 것은 칼럼이 아닌 행임에 유의한다. 테이블을 초기화하기 위해서는 `TRUNCATE {테이블 이름}`을 활용한다.

```sql
DELETE FROM table1
      WHERE ISNULL(col1)
```

테이블 내의 데이터들을 특정 조건으로 필터링한 뒤에 필터링 된 데이터만 바꾸고 싶은 경우가 있다. 이때는 `UPDATE {테이블 이름} SET {변경 사항들} WHERE {조건문}`을 사용한다.

```sql
UPDATE table1
SET 
    col1 = 'changed',
    col2 = 2
WHERE col3 IN (
        SELECT col1
        FROM table2
    )
```

- `WHERE`로 시작하는 조건문 없이, 모든 행을 변경할 수는 없다. 



## 참고자료
1. 얄코의 MySQL, https://www.yalco.kr/lectures/sql/
3. 프로그래머스 SQL 고득점 Kit, https://school.programmers.co.kr/learn/challenges?tab=sql_practice_kit
1. 책 <데이터베이스 첫걸음>, 미크, 기무라 메이지
4. MySQL 8.0 Reference Manual, https://docs.oracle.com/cd/E17952_01/mysql-8.0-en/index.html