<aside>
๐ก ORM์ด ์ฐ๋ฆฌ๊ฐ ์์ฑํ ์ฝ๋๋ฅผ ์ด๋ป๊ฒ DB๋ก ๋ณํํด์ฃผ๋์ง ์ฐ์ตํด๋ณด์. (Ellie ์ ์๋ ๊ฐ์ dwitter Project์์ Sequelize ์ค์ตํด๋ณด๊ธฐ)

</aside>

## dwitter Project ERD
 <img src="https://user-images.githubusercontent.com/73332608/152633074-bbf30a3b-754f-498a-8cd6-72abd24c6576.png" width="300" height="300"> 

ORM์ ์ฌ์ฉํ๋ฉด ์ฝ๋์์์ ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ํ์ํ ํ์ด๋ธ์ ์์ฑํด์ค ์ ์์ผ๋ฏ๋ก ๋ ๊ฐ๋จํ๋ค. (SQL์ ๋ํด ๋ชฐ๋ผ๋ ํ  ์ ์๋ค.)

`npm start sequelize`๋ก ์ค์นํด์ฃผ๊ณ  ์ด๋ฅผ ํ์ฉํ๊ธฐ ์ํด `app.js`์์ ๋ค์๊ณผ ๊ฐ์ด ์์ฑํด์ค๋ค.

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
    // ๊ธฐ๋ณธ๊ฐ์ mysql์ด์ง๋ง ๋ช์์ ์ผ๋ก ๋ํ๋ด์ฃผ์.
    dialect: 'mysql',
});
```



# Users ํ์ด๋ธ

๋จผ์ , User๋ผ๋ ๋ชจ๋ธ์ ๋ง๋ค์ด์ค๋ค. 

```jsx
// ๋ชจ๋ธ ๋ง๋ค๊ธฐ (User)
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

`createdAt`, `updatedAt` ์ปฌ๋ผ์ ์ถ๊ฐํ๊ณ  ์ถ์ง ์์ผ๋ฉด option์ `timestamps`๋ฅผ `false`๋ก ์ค์ ํด์ค๋ค.

![image](https://user-images.githubusercontent.com/73332608/152632534-7d590591-07a7-415d-a310-aad255c20e8b.png)

npm start

ํด๋น ์ฝ๋๋ฅผ ์คํํ๋ฉด **์ง์ ํ์ด๋ฆ + s**๋ก ํ์ด๋ธ์ด ๋ง๋ค์ด์ง๋ค.

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

sequelize์ ๋ฉ์๋๋ค์ ์ด์ฉํ๋ค. 

`findByUsername`์ ๊ฒฝ์ฐ `findOne`์ ์ด์ฉํ์ฌ where๋ผ๋ ์ต์์ ํตํด ํํํ๋ค.

`findById`๋ id๊ฐ users ํ์ด๋ธ์์ **Primary Key**์ด๋ฏ๋ก `findByPk`๋ฅผ ํตํด ํํํ๋ค.

๋ง์ง๋ง์ผ๋ก, `createUser`์ `create`์ ์ด์ฉํ์ฌ ๋ง๋ค์ด์ง ๋ฐ์ดํฐ ์์ ์๋ dataValues์ ์ ๋ณด๊ฐ ๋ด๊ฒจ์์ผ๋ฏ๋ก data.dataValues.id๋ฅผ ๋ฆฌํดํ๋ค.



# Tweets ํ์ด๋ธ

์์์ ํ๋ ๊ฒ์ฒ๋ผ ๋จผ์  Tweet ๋ชจ๋ธ์ ๋ง๋ค์ด์ค๋ค.

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

// userId FK ์ค์ 
// Tweet์ User์ ์ข์๋๋ค๊ณ  ๊ด๊ณ๋ฅผ ์ ์ํด์ฃผ๋ฉด ๋ฐ์ดํฐ๋ฒ ์ด์ค์์ ์์์ foreign key๋ก ์ฐ๊ฒฐํด์ค๋ค. 
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


์์ ๊ฐ์ด user ํ์ด๋ธ ๋ด์ฉ์ ๊ทธ๋๋ก ์ํ๊ฒ ์ ์กํ ์๋ ์์ง๋ง ์๋์ ๊ฐ์ด `attributes`๋ผ๋ ์ต์์ `Sequelize.col`์ด๋ผ๋ ๋ฉ์๋๋ฅผ ์ด์ฉํ์ฌ ์ข๋ ๊ฐํธํ๊ฒ ์ ์กํ  ์ ์๋ค.

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
        order: [['createdAt', 'DESC']], // order by (createdAt์ ๊ธฐ์ค์ผ๋ก ๋ด๋ฆผ์ฐจ์)
    })
}
```

![image](https://user-images.githubusercontent.com/73332608/152632560-46999156-304f-4486-85e1-780f4ce678c1.png)

๊ทธ๋ฐ๋ฐ ์ฐ๋ฆฌ๋ ์ฝ๋ฉํ  ๋ ๊ฐ์ฅ ์ค์ํ๊ฒ ์๊ฐํด์ผํ  ๊ฒ ์ค ํ๋๊ฐ ์๋ค. ๋ฐ๋ก ์ฝ๋๊ฐ ์ค๋ณต๋๋ ๊ฒ์ ์ต๋ํ ์ง์ํด์ผํ๋ค๋ ์ ์ด๋ค. 

์์ ์ผ๋ ์ฝ๋๋ ๊ณ์ ์ค๋ณต๋๊ธฐ ๋๋ฌธ์ ๋ฐ๋ก ๋ณ์๋ก ๋นผ์ฃผ๊ฒ ๋ค.

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
    order: [['createdAt', 'DESC']], // order by (createdAt์ ๊ธฐ์ค์ผ๋ก ๋ด๋ฆผ์ฐจ์)
}
```

์์ ๊ฐ์ด `INCLUDE_USER`, `ORDER_DESC`์ sequelize ๋ฉ์๋์ ์์ฑ์ ํ ๋นํด์ค๋ค.

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

`getAllByUsername`์ `findAll`์ ์ด์ฉํ๋๋ฐ, username์ ํตํด tweet์ ์ฐพ๋ ๋ฉ์๋์ด๋ฏ๋ก, include๋ฅผ ํ๋ ์ถ๊ฐํด์ ํํํด์คฌ๋ค.

```jsx
export async function getById(id) {
    return Tweet.findOne({
        where: {id},
        ...INCLUDE_USER
    })
}

// create์ ๊ฒฝ์ฐ์๋ username๊ณผ name์ ๋ฐ์์ค๋ ๊ฒ ์๋๋ผ userId๋ฅผ ๋ฐ์์จ๋ค. 
export async function create(text, userId) {
    return Tweet.create({ text, userId })
        .then(data => getById(data.dataValues.id));
}
```

`create`๋ `create`๋ฅผ ์ด์ฉํ๋๋ฐ ์์ `getById`๋ฅผ ์ด์ฉํ์ฌ ํด๋น tweet์ id๋ฅผ ํตํด ํด๋น tweet์ ๋ณด๋ฅผ ๊ฐ์ ธ์จ๋ค.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fb5af743-4284-4791-981c-1671bcb18fea/Untitled.png)

```jsx
export async function update(id, text) {
    return Tweet.findByPk(id, INCLUDE_USER)
        .then(tweet => {
						// text (updateํ tweet๋ด์ฉ)์ tweet.text์ ๋ฐ๋ก ํ ๋นํ๋ค.
            tweet.text = text;
            // save๋ฅผ ํตํด ์ ์ฅํ ์๊ธฐ์์ ์ promise๋ก ๋ฆฌํดํ๋ค. 
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

`update`๋ `findByPk`๋ก ์์ ํ๋ ค๋ tweet์ id๋ฅผ ์ฐพ์๋ธ ํ tweet์ ์์ ํ text๋ฅผ tweet.text์ ํ ๋นํด์ค๋ค.

์ดํ, `save` ๋ฉ์๋๋ฅผ ํตํด ํด๋น ๋ด์ฉ์ ์ ์ฅํ  ์ ์๋ค.

`remove`, ์ญ์  ๊ฐ์ ๊ฒฝ์ฐ์๋ `destroy`๋ก ํด๋น ๋ฐ์ดํฐ๋ฅผ ์ญ์ ํ  ์ ์๋ค.
