<aside>
💡 ORM이 우리가 작성한 코드를 어떻게 DB로 변환해주는지 연습해보자. (Ellie 선생님 강의 dwitter Project에서 Sequelize 실습해보기)

</aside>

## dwitter Project ERD
 <img src="https://user-images.githubusercontent.com/73332608/152633074-bbf30a3b-754f-498a-8cd6-72abd24c6576.png" width="300" height="300"> 

ORM을 사용하면 코드상에서 데이터베이스에 필요한 테이블을 생성해줄 수 있으므로 더 간단하다. (SQL에 대해 몰라도 할 수 있다.)

`npm start sequelize`로 설치해주고 이를 활용하기 위해 `app.js`에서 다음과 같이 작성해준다.

```jsx
sequelize.sync().then(client => {
    // console.log(client);
    const server = app.listen(config.host.port);
    initSocket(server);
})
```

```jsx
const { host, user, database, password } = config.db;
export const sequelize = new SQ.Sequelize(database, user, password, {
    host,
    // 기본값은 mysql이지만 명시적으로 나타내주자.
    dialect: 'mysql',
});
```



# Users 테이블

먼저, User라는 모델을 만들어준다. 

```jsx
// 모델 만들기 (User)
const User = sequelize.define('user', {
    id: {
        type: DataTypes.INTEGER,
        autoIncrement: true,
        allowNull: false, 
        primaryKey: true,
    },
    username: {
        type: DataTypes.STRING(45),
        allowNull: false,
    },
    password: {
        type: DataTypes.STRING(128),
        allowNull: false,
    },
    name: {
        type: DataTypes.STRING(128),
        allowNull: false,
    },
    email: {
        type: DataTypes.STRING(128),
        allowNull: false,
    },
    url: DataTypes.TEXT,
    }, { timestamps: false }
);
```

`createdAt`, `updatedAt` 컬럼을 추가하고 싶지 않으면 option에 `timestamps`를 `false`로 설정해준다.

![image](https://user-images.githubusercontent.com/73332608/152632534-7d590591-07a7-415d-a310-aad255c20e8b.png)

npm start

해당 코드를 실행하면 **지정한이름 + s**로 테이블이 만들어진다.

```jsx
export async function findByUsername(username) {
    return User.findOne({ where: { username } });
}

export async function findById(id) {
    return User.findByPk(id);
}

export async function createUser(user) {
    return User.create(user)
        .then(data => data.dataValues.id);
}
```

sequelize의 메서드들을 이용했다. 

`findByUsername`의 경우 `findOne`을 이용하여 where라는 옵션을 통해 표현한다.

`findById`는 id가 users 테이블에서 **Primary Key**이므로 `findByPk`를 통해 표현한다.

마지막으로, `createUser`은 `create`을 이용하여 만들어진 데이터 안에 있는 dataValues에 정보가 담겨있으므로 data.dataValues.id를 리턴한다.



# Tweets 테이블

위에서 했던 것처럼 먼저 Tweet 모델을 만들어준다.

```jsx
const Tweet = sequelize.define('tweet', {
    id: {
        type: DataTypes.INTEGER,
        autoIncrement: true,
        allowNull: false, 
        primaryKey: true,
    },
    text: {
        type: DataTypes.TEXT,
        allowNull: false,
    },
});

// userId FK 설정
// Tweet은 User에 종속된다고 관계를 정의해주면 데이터베이스에서 알아서 foreign key로 연결해준다. 
Tweet.belongsTo(User);
```

```jsx
export async function getAll() {
    return Tweet.findAll({
        include: {
            model: User,
        }
    })
}
```

![image](https://user-images.githubusercontent.com/73332608/152632576-7f865895-3409-484e-9eab-1b248d0c25a5.png)


위와 같이 user 테이블 내용을 그대로 속하게 전송할수도 있지만 아래와 같이 `attributes`라는 옵션에 `Sequelize.col`이라는 메서드를 이용하여 좀더 간편하게 전송할 수 있다.

```jsx
const Sequelize = SQ.Sequelize;
.
.
.

export async function getAll() {
    return Tweet.findAll({
        attributes: [
            'id', 
            'text', 
            'createdAt', 
            'userId', 
            [Sequelize.col('user.name'), 'name'],
            [Sequelize.col('user.username'), 'username'],
            [Sequelize.col('user.url'), 'url']
        ],
        include: {
            model: User,
            attributes: []
        },
        order: [['createdAt', 'DESC']], // order by (createdAt을 기준으로 내림차순)
    })
}
```

![image](https://user-images.githubusercontent.com/73332608/152632560-46999156-304f-4486-85e1-780f4ce678c1.png)

그런데 우리는 코딩할 때 가장 중요하게 생각해야할 것 중 하나가 있다. 바로 코드가 중복되는 것을 최대한 지양해야한다는 점이다. 

위에 썼던 코드는 계속 중복되기 때문에 따로 변수로 빼주겠다.

```jsx
const Sequelize = SQ.Sequelize;

const INCLUDE_USER = {
    attributes: [
        'id', 
        'text', 
        'createdAt', 
        'userId', 
        [Sequelize.col('user.name'), 'name'],
        [Sequelize.col('user.username'), 'username'],
        [Sequelize.col('user.url'), 'url']
    ],
    include: {
        model: User,
        attributes: []
    },
}

const ORDER_DESC = {
    order: [['createdAt', 'DESC']], // order by (createdAt을 기준으로 내림차순)
}
```

위와 같이 `INCLUDE_USER`, `ORDER_DESC`에 sequelize 메서드의 속성을 할당해준다.

```jsx
export async function getAll() {
    return Tweet.findAll({...INCLUDE_USER, ...ORDER_DESC});
}

export async function getAllByUsername(username) {
    return Tweet.findAll({...INCLUDE_USER, ...ORDER_DESC, 
        include: {...INCLUDE_USER.include, where: { username }
    }});
}
```

`getAllByUsername`은 `findAll`을 이용했는데, username을 통해 tweet을 찾는 메서드이므로, include를 하나 추가해서 표현해줬다.

```jsx
export async function getById(id) {
    return Tweet.findOne({
        where: {id},
        ...INCLUDE_USER
    })
}

// create의 경우에도 username과 name을 받아오는 게 아니라 userId를 받아온다. 
export async function create(text, userId) {
    return Tweet.create({ text, userId })
        .then(data => getById(data.dataValues.id));
}
```

`create`는 `create`를 이용했는데 위의 `getById`를 이용하여 해당 tweet의 id를 통해 해당 tweet정보를 가져온다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fb5af743-4284-4791-981c-1671bcb18fea/Untitled.png)

```jsx
export async function update(id, text) {
    return Tweet.findByPk(id, INCLUDE_USER)
        .then(tweet => {
						// text (update한 tweet내용)을 tweet.text에 바로 할당한다.
            tweet.text = text;
            // save를 통해 저장한 자기자신을 promise로 리턴한다. 
            return tweet.save();
        });
}

export async function remove(id) {
    return Tweet.findByPk(id)
        .then(tweet => {
            tweet.destroy();
        });
}
```

`update`는 `findByPk`로 수정하려는 tweet의 id를 찾아낸 후 tweet을 수정한 text를 tweet.text에 할당해준다.

이후, `save` 메서드를 통해 해당 내용을 저장할 수 있다.

`remove`, 삭제 같은 경우에는 `destroy`로 해당 데이터를 삭제할 수 있다.
