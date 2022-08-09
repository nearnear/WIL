# SQL DML

*DML : Data Manipulation Language*

테이블의 행 및 칼럼을 추가/삭제/변형한다.

## INSERT

- `INSERT INTO {테이블 이름} ({칼럼1, 칼럼2, 칼럼3}) VALUES ({칼럼1 값, 칼럼2 값, 칼럼3 값})` : 행 추가

```sql
INSERT INTO table1 ({col1, col2, col3})
     VALUES ({val1, val2, val3})
```

## DELETE

특정 조건에 해당하는 데이터를 삭제하기 위해서는 
- `DELETE FROM {테이블 이름} WHERE {조건문}` : 조건에 해당하는 행 삭제
- `TRUNCATE {테이블 이름}` : 테이블 초기화

```sql
DELETE FROM table1
      WHERE ISNULL(col1)
```

## UPDATE

테이블 내의 데이터들을 특정 조건으로 필터링한 뒤에 필터링 된 데이터만 바꾸고 싶은 경우가 있다.
- `UPDATE {테이블 이름} SET {변경 사항들} WHERE {조건문}` : 조건에 해당하는 칼럼을 수정

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

단, `WHERE`로 시작하는 조건문 없이, 모든 행을 변경할 수는 없다. 



