# SQL vs. NoSQL

---

## SQL → RDBMS

---

테이블 형식으로 관리

테이블은 행, 열로 구성

엑셀과 비교했을 때 가장 큰 차이점 : SQL은 각 행마다 특정한 데이터 타입을 지정해야 한다.

table, column, row → Schema

Data integrity ( 데이터의 진실성 ) 보장 → 스키마에서 정의한 다른 타입의 데치터를 넣을 수 없으므로 

✔︎ key

1. Primary key 
    1. primary key로 검색의 최적화
2. foreign key
    1. 두가지 다른 테이블의 관계 설정 

oracle, mysql, postgreSQL (강력하고 좋은 툴 제공, mysql에 비해 복잡, query도 다름), SQLite ... 

## NoSQL

---

### 왜? NoSQL이 나왔지?

1990년대 중반에 Object를 기반으로 한 프로그램에서 ‘object 자체를 데이터베이스에 어떻게 저장하지?’ 에 대한 많은 고민 → object형 데이터베이스

2000대에 인터넷과 빅데이터의 성장으로 NoSQL DB 성장

### NoSQL 특징

- schema-less
- non-relational
    - 자체적으로 고립된 형태로 관리 가능
    - 저장하고자 하는 데이터를 여러개로 분산해서 다른 서버에서  관리하기 용이함
- 특수한 문제를 해결하기 위해 나옴

### 종류

- key-value
- document
- wide-column
- graph

1. **mongoDB**
    1. document로 관리하는 방식
    2. 관련있는 document를 묶어주는 collection 존재 
    3. 각각의 collection은 non-relational, 즉 관련이 없으므로 개별적으로 관리하기 용이함
    4. 보통 NoSQL 사용시 A collection에서 B collection이 필요하다면 데이터를 중복해서 관리하는 것을 선호함

### code

```jsx
const userJane = {
	username: 'janedoe',
	birthday: new Date(1980, 6, 20)
}

const userBob = {
	first_name: 'bob',
	last_name: 'doe'
}

const product = {
	title: 'Toothbrush',
	description: 'An ordinary toothbrush',
	price: 3.99
}
```

### Database

**Users Collection**

{id: 123, username: ‘janedoe’, birthday:’1980-06-20’}

{id: 812, first_name: ‘bob’, last_name: ‘doe’}

**Products collection**

{id: 123, title: ‘Toothbrush’, price: 3.99, description: ‘An ordinary toothbrush’}

NoSQL은 스키마라는 개념이 없기때문에 어떤 정보가 정확한지 알 수 없으므로 잘못 저장해도 오류나지 않으므로 개발자가 스스로 잘 관리해야 한다.

정보가 일관성을 가지지 않으면 제품에 문제가 생길 수 있다. 

ex) 

1. Wide Column
    1. cassandra
    2. Cloud Bigtable
2. Graph
    1. neo4j
3. Document
    1. mongoDB
4. Key-Value
    1. Redis
    2. DynamoDB

## SQL vs. NoSQL

|  | SQL | NoSQL |
| --- | --- | --- |
| Data Structure | rigid/fixed | undefined/flexible |
| Data lookup | easy | easy |
| Aggregate queries | easy (관계형 특화) | difficult (성능도 안좋음) |
| Slicing & Dicing Data | easy  | difficult  |
| Scaling for high input | difficult | easy |
| running cost | costly | cheap |

<aside>
💡 Scaling (수직 확장 || 수평 확장)
SQL의 경우 Relational (관련성)이 있기 때문에 각각의 테이블을 다른 서버에 배포할 수 없다 → 수직적인 확장 (램 증가, CPU 교체, DISK 추가)만 가능
NoSQL은 Non-relational 하므로 각각 다른 데이터의 collection을 다른 서버에 배포할 수 있다 → 수평적인 확장 가능

</aside>

### 🖇 무엇을 써야할까??

1. 어떤 데이터 타입을 저장할건지.
2. 얼마나 많은 사용자를 예상하고 있고 각각의 사용자마다 얼마나 많은 데이터를 저장할 수 있는지
3. ⭐️ 각각의 데이터가 서로 관계가 있는지 

### ✍️ Example

***SQL***

- accounting software
- e-commerce platforms
- customer relationship software (CRM)

***NoSQL***

- social networks (graph)
- distributed cache (key-value)
- content management systems (document)
- real-time analytics (wide-column)
    - 빅데이터, 실시간 ...

### ✏️ Hybrid approach

**하나의 제품에서 딱 하나의 종류의 데이터베이스를 사용하는 게 아니라, 필요에 따라 부분적으로 다른 데이터베이스를 사용할 수 있다.**

예를 들어, user service에서는 mongoDB를, billing service에서는 MySQL을, transcoding service에서는 DynamoDB를 사용할 수 있다. 

# ORM & ODM

---

## ORM (Object Relational Mapping)

<aside>
💡 우리의 코드에 여러가지 object가 있다면 이것을 데이터베이스에 저장하기 위해서 어떻게 이 object를 테이블로 변환할건지, 스키마를 만들고 분할할건지, 데이터베이스에서 어떻게 데이터를 읽어서 object로 변환할건지 고민하게 되는데 이런 번거로움을 해결해주는게 바로 ORM이다.

</aside>

<aside>
💡 ORM은 우리가 코드상에서 작성한 데이터베이스를 위에서 고민한 것들을 자동으로 해준다.

</aside>

> Sequelize라는 ORM을 이용하면 코드상으로 스키마가 어떻게 보여지는지 정의하고 프로그램을 실행하면 자동으로 데이터베이스에 테이블을 만들어주고 필요한 데이터를 데이터베이스에 넣어준다.
> 

**즉, ORM(+ ODM)은 데이터베이스를 한단계 추상화된, 우리에게 좋은 API를 제공하면서 그 API를 통해 데이터베이스를 읽고 쓸 수 있다.** 

## 장점

1. 데이터베이스 쿼리가 어떻게 생겼는지 걱정하지 않고 Business Logic에 좀 더 초점을 두고 개발하게 도와줌
2. 반복되는 쿼리문 감소
3. 데이터베이스를 추상화하는 단계이므로 다른 SQL DB를 사용하더라도 쿼리가 달라지는 것을 몰라도 ORM만 안다면 편하게 사용활 수 있음
4. 스키마 변경시 (코드 변경시) 자동으로 처리해주는 migration을 제공함 

## 단점

1. 복잡한 쿼리 불가능
2. 데이터베이스만을 이용했을 때의 빠른  성능, 메모리의 최적화에 대해 아쉬움
    1. 정말 많은 데이터가 있고 성능이 중요하다면 ORM 없이 DabaBase와 SQL을 사용하는게 낫다.

## 종류

1. TypeORM
2. Sequelize
3. Prisma

## ODM (Object Document Mapper)

mongoDB의 경우 Object를 Document로 매핑해주므로 ODM을 사용한다.
