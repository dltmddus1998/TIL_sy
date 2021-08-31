# MySQL

## 데이터베이스 관련 명령어

**데이터베이스 생성**

```sql
CREATE DATABASE 데이터베이스_이름;
```

**데이터베이스 사용**

데이터베이스를 이용해 테이블을 만들거나 수정하거나 삭제하는 등의 작업을 하려면, 먼저 데이터베이스를 사용하겠다는 명령을 전달해야 한다.

```sql
USE 데이터베이스_이름;
```

**테이블 생성**

`USE` 를 이용해 데이터베이스를 선택했다면, 이제 테이블을 만들 수 있다. 다음과 같은 필드 조건이 있는 user 테이블을 만들어보자.

필드 - id(number type, PRIMARY KEY & AUTO_INCREMENT), name(varchar type), email(varchar type)

```sql
CREATE TABLE user (
	id int PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(255),
	email VARCHAR(255)
);
```

**테이블 정보 확인**

```sql
DESCRIBE user;
-->
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int          | NO   | PRI | NULL    | auto_increment |
| name  | varchar(255) | YES  |     | NULL    |                |
| email | varchar(255) | YES  |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)
```

**특정 테이블 정보 조회**

```sql
# 특정 값과 동일한 데이터 찾기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 = "특정 값"

# 특정 값을 제외한 값 찾기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 <> "특정 값"

# 특정 값보다 크거나 작은 데이터 필터하기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 > "특정 값"

SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 <= "특정 값"

# 문자열에서 특정 값과 비슷한 값들을 필터링 하기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 LIKE "%특정 문자열%"

# 리스트의 값들과 일치하는 데이터를 필터링 하기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 IN ("특정값_1", "특정값_2")

# 값이 없는 경우 NULL 사용
SELECT *
FROM 테이블_이름
WHERE 특성_1 IS NULL

# 값이 없는 경우 제외하면 NOT NULL
SELECT *
FROM 테이블_이름
WHERE 특성_1 IS NOT NULL
```

**ORDER BY**

돌려받는 데이터 결과를 어떤 기준으로 정렬하여 출력할지 결정한다.

order by는 선택적으로 사용이 가능하다.

```sql
# 기본 정렬은 오름차순
SELECT *
FROM 테이블_이름
ORDER BY 특성_1

# 내림차순 - DESC 사용
SELECT *
FROM 테이블_이름
ORDER BY 특성_1 DESC
```

**LIMIT**

결과로 출력할 데이터의 개수를 정할 수 있다.

limit는 선택적으로 사용이 가능하다.

쿼리문에서 사용할 때에는 가장 마지막에 추가한다.

```sql
SELECT *
FROM 테이블_이름
LIMIT 200
```

**DISTINCT**

유니크한 값을 받고 싶을 때에는 `SELECT DISTINT` 를 사용할 수 있다.

```sql
# 특성_1을 기준으로 유니크한 값들만 선택
SELECT DISTINCT 특성_1
FROM 테이블_이름

# 특성_1, 특성_2, 특성_3의 유니크한 '조합' 값들을 선택
SELECT
  DISTINCT
    특성_1
    ,특성_2
    ,특성_3
FROM 테이블_이름
```

**INNER JOIN**

`INNER JOIN` 이나 `JOIN` 으로 실행할 수 있다.

```sql
# 둘 이상의 테이블을 서로 공통된 부분을 기준으로 연결
SELECT *
FROM 테이블_1
JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B
```

**OUTER JOIN**

`OUTER JOIN` 은 다양한 선택지가 있다.

```sql
# LEFT OUTER JOIN으로 LEFT INCLUSIVE 실행
SELECT *
FROM 테이블_1
LEFT OUTER JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B

# RIGHT OUTER JOIN으로 RIGHT INCLUSIVE을 실행
SELECT *
FROM 테이블_1
RIGHT OUTER JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B
```

**여러 쿼리문 한 번에 써보기**

문제 ) Brazil에서 온 고객을 도시별로 묶은 뒤, 각 도시 수에 따라 내림차순 정렬하기. 그리고 CustomerId에 따라 오름차순으로 정렬한 3개의 결과만 요청하기.

> _같은 결과를 출력하는 서로 다른 쿼리문이 있을 수 있다. 같은 결과를 다른 방법으로 표현할 수 있다._

```sql
SELECT c.CustomerId, c.FirstName, count(c.City) as 'City Count'
FROM customers AS c
JOIN employees AS e ON c.SupportRepId = e.EmployeeId
WHERE c.Country = 'Brazil'
GROUP BY c.City
ORDER BY 3 DESC, c.CustomerId ASC
LIMIT 3;
```

#

## 데이터베이스 설계

### 관계형 데이터베이스

---

구조화된 데이터는 하나의 테이블로 표현할 수 있다. 사전에 정의된 테이블을 relation이라고도 부르기 때문에, 테이블을 사용하는 데이터베이스를 관계형 데이터베이스(Relational database)라고 한다.

관계형 데이터베이스를 학습하면서, 반드시 알아야 하는 키워드

- 데이터 : 각 항목에 저장되는 값
- 테이블 (table | relation) : 사전에 정의된 열의 데이터 타입대로 작성된 데이터가 행으로 축적됨
- 칼럼 (column | filed) : 테이블의 한 열을 가리킴
- 레코드 (record | tuple) : 테이블의 한 행에 저장된 데이터
- 키 (key) : 테이블의 각 레코드를 구분할 수 있는 값이다.
  - 각 레코드마다 고유한 값을 가진다.
  - 기본키 (primary key)와 외래키(foreign key) 등이 있다.

### 관계 종류

---

- 1 : 1 - 하나의 레코드가 다른 테이블의 레코드 한 개와 연결된 경우

  예시 : User - Phonebook

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fc63271f-ccf6-4c33-bf8e-013563c5abf0/Untitled.png)

  User table과 Phonebook table의 1 : 1 관계

  각 전화번호가 단 한 명의 유저와 연결되어 있으므로 1 : 1

  자주 사용되는 관계는 아니다.

  1:1로 나타낼 수 있는 관계라면 User테이블에 phone_id를 대신해 phone_number를 직접 저장하는게 낫다.

- 1 : N - 하나의 레코드가 서로 다른 여러 개의 레코드와 연결된 경우

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c323bc75-36c2-4e17-9dca-67cf17ae22e1/Untitled.png)

  User table과 Phonebook table의 1 : N 관계

  한 명의 유저가 여러 전화번호를 가질 수 있다. 그러나 여러명의 유저가 하나의 전화번호를 가질 수는 없다.

  이러한 **1 : N 관계는 관계형 데이터베이스에서 가장 많이 사용된다.**

- N : M - 여러 개의 레코드가 다른 테이블의 여러 개의 레코드와 관계가 있는 경우

  - 다대다 관계를 위해 스키마를 디자인할 때, 조인 테이블을 만들어 관리한다.
  - 1 : N 관계와 비슷하지만, 양방향에서 다수의 레코드를 가질 수 있다.

  예시 - Customer, Package

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e8f5466c-1f5f-484e-9b4c-931810c32057/Untitled.png)

  두 개의 테이블과 일대다 관계를 형성하는 새로운 테이블로 다대다 관계를 표현할 수 있다.

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f11cdcb9-b0e2-47b9-ae35-16855a9e67ae/Untitled.png)

- self referencing : 테이블 스스로 관계를 가짐

  - 예를 들어 추천인이 누구인지 파악하기 위해 사용될 수 있다.

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/25e3cddb-ca72-40fe-bec7-4ef770a5f05c/Untitled.png)

  User 테이블의 recommend_id는 User 테이블의 user_id와 연결되어 있다. 한 명의 유저(user_id)는 한 명의 추천인(recommend_id)를 가질 수 있다. 그러나 여러 명이 한명의 유저를 추천인으로 등록할 수 있다. 이 관계는 일대다 관계와 유사하다고 생각할 수 있으나 일반적으로 일대다 관계는 서로 다른 테이블의 관계를 나타낼 때 표현하는 방법이다.
