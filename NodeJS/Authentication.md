# 🗝 Authentication

# 인증이란?

---

> You are who you say you are
> 

password, pin-code ,fingerprints, signature, mobile phone, hardware token etc...

보통 password, pin-code 사용

## How does Auth happen?

서버에 가입 → id / password 저장

로그인 성공 → 다음에 사용자가 다른 요청을 할 때 해당 사용자인지 아닌지 구분하는 방법 2가지

- **Session & Cookie**
    1. client가 server에 로그인하면 server는 User DB에 해당 id와 password가 있는지 확인하고, 존재하면 session을 만든다.
        1. session에는 사용자의 id, seesion의 id, session의 expiration time에 대해 간직한다. 
    2. session 정보는 Session DB에 저장한다. (파일 시스템이나 메모리에 저장하기도 한다.)
    3. client에게 session과 관련된 정보를 보내준다.
        1. 보통은 **cookie**에 정보를 넣어 보낸다.
        2. `http-only`라는 옵션을 주면 해당 쿠키는 **브라우저**에 인해서만 읽을 수 있다. (안전한 방식)
            1. javascript나 program내에서는 볼 수 없다.
    4. cookie를 이용해서 client가 server에 다른 요청을 할 때 이 cookie의 정보를 자동으로 브라우저에서 포함해주므로 client에서 보내준 session id를 통해서 session DB에 존재하는 session id인지, 만료된 건 아닌지 검토한다.
    5. 유효하면, session id를 통해 사용자가 누구인지 파악해서 관련된 데이터를 client에 보낼 수 있다.
    - 장점
        - session DB에 모든 session에 대한 정보를 보관하므로 신뢰할 수 있다.
        - cookie를 사용하기 때문에 server에서 보낼 때 쉽고, client에서 별도의 처리를 해주지 않아도 브라우저에서 해주므로 간단하게 구현할 수 있다.
        - `http-only` 옵션을 사용하면 보안성을 높일 수 있다.
        - 사용자에게 사용자에 대한 정보를 보내는 것이 아닌 session id를 보내므로 사용자가 누군지 다른 정보들을 주고 받지 않기 때문에 보안성이 높다.
    - 단점
        - **Stateful**
            - 서버에서 시시각각 변하는 로그인하는 사용자에 대해서 session의 정보를 보관하고 있으므로 서버에 상태가 있다. 
            이 때문에 서버를 확장해서 여러 개의 서버에서 동작하거나 마이크로서비스를 하는 경우에, 한 서버에 session을 보관하고 있으므로 다양한 서버들이 이 session의 정보를 확인하기 위해 하나의 서버에 접속해서 네트워크 요청을 해야 한다. 즉, 시간이 많이 걸려서 성능이 좋지 않은 경우가 발생한다.

## What is JWT?

---

### JSON Web Token

JSON 구성 

1. Header 
    1. 사용하는 알고리즘
    2. 타입
2. Payload 
    1. 전송하는 데이터 
3. signature
    1. encoding한 헤더와 페이로드 뿐만 아니라 encoding하기 위해 사용하는 secret key를 이용해서 함께 인코딩 해놓는다.

> **JWT는 JSON이라는 파일 안에 필요한 모든 데이터를 넣어서 이를 주고받을 수 있다.
Secret key를 통해 인코딩하므로 정보의 유효성을 확인할 수 있어서 안전하다.**
> 

1. 사용자가 로그인하고 유효한 사용자라면, 우리가 원하는 정보를 함께 묶어서 JWT를 만들게 된다. 
2. 만들어진 JWT를 사용자에게 보낸다
3.  추후에 요청하는 모든 API 요청에 대해서 서버에서 받은 JWT를 헤더에 포함해서 보내게 되면, 서버에서는 이 JWT를 통해 유효한지, 수정되었는지, 만료되었는지, 사용자 정보가 정확한지 유효성 검사 후 해당하는 데이터를 client에게 보낼 수 있다. 

- 장점
    - Stateless
        - 한 번 JSON으로 만들어서 client에게 보내주고 검증만 하면 되기 때문에 별도의 상태는 필요 없다.
        - 따라서, 서로 서버간의 네트워크 요청을 통해서 사용자 검증을 하지 않아도 JWT를 만들고 해독할 수 있는 동일한 secret key만 가지고 있으면 된다.
- 단점
    - 서버와 클라이언트 간에 끊임없이 JWT를 주고받을 수 있다.
        - 영원히 만료되지 않는 JWT를 주고받는다면 해커가 이를 악용할 수 있으므로 이 점을 유념하고 사용해야 한다.
- 패스워드 안전하게 보관하기
    - **bcrypt** : 패스워드를 안전하게 보관할 수 있도록 패스워드를 해싱하는 알고리즘
        - 사용자가 가입할 때 입력한 아이디와 패스워드를 그대로 데이터베이스에 저장하면 해커가 우리 서버에 있는 정보를 읽었을 때 사용자 정보를 훔쳐갈 수 있고 이를 악용할 수 있다.
        - 하지만, bcrypt를 통해서 패스워드를 그대로 보관하는 것이 아니라 암호화 알고리즘을 이용해서 해싱해서 잘 암호화된 패스워드를 데이터베이스에 저장하는 것이다.
        - bcrypt된 암호화 살펴보기
            - $2b$15$T/P1b.MpsULbf6czCs1qKOA7DiR.nKz9.y0IDpx1MXL5mFLWYtbHS
                - Alg : 알고리즘에 대한 정보
                - cost : 암호화를 위한 비용
                - salt : 암호화 복잡하게 만듦
                    - bcrypt는 암호화를 하는 데에만 이용이 가능하다.
                    - 알고리즘을 통해서 해시를 만들 수는 있지만 만들어진 해시를 가지고 비밀번호로 다시 변환할 수 없다.
                    - 즉, 암호화만 가능하다.
                    - 관련된 모든 텍스트, 자주 사용하는 비밀번호에 대해서 가능한 조합들을 테이블로 만들어놓고 암호를 유추하는 해커들.
                    - salt 사용시 해커가 가지고 있어야 하는 테이블의 개수가 기하급수적으로 늘어나게 된다.
                    - 즉, 해독 시간이 기하급수적으로 늘어난다. (해킹을 훨씬 어렵게 만들어버린다.)
                        
                        ```jsx
                        const bcrypt = require('bcrypt');
                        
                        const password = 'abcd1234';
                        const hashed = bcrypt.hashSync(password, 10);
                        console.log(`password: ${password}, hashed: ${hashed}`);
                        // password: abcd1234, hashed: $2b$10$551dHGQk5rA6XJ00hrDtZO/KhauD6Hgx1pKi.jcSth2yXv4v/5xZC
                        
                        const result = bcrypt.compareSync('abcd123', hashed); // 입력한 비밀번호와 hashed 비교하기 - 다르면 false
                        console.log(result);
                        
                        ---------- console -----------
                        password: abcd1234, hashed: $2b$10$gY5PwbbxehCMBs8hEA7OY.bmovngxO6uq3HaIQ64bk9KsozK1lF6K
                        false
                        ```
                        
            - hash : 최종적으로 암호화된 정보

## JWT 사용해보기

---

`npm i jsonwebtoken`

1. sign
    1. `sign([payload], [secret key token], [options])`
    2. options에서 expiresIn 옵션을 줘서 토큰이 특정 시간동안에만 유효할 수 있도록 만들어줘야 한다
2. verify : 토큰을 변경했는지 검증하기
    1. `verify(비교 토큰1, 비교 토큰2, callback(error, decoded) ⇒ {})`

```jsx
const jwt = require('jsonwebtoken');

const secret = 'UN1gCmEiijb56eC6VEy!0#G@dCY7mcco';
const token = jwt.sign(
    {
        // payload - token에 담고 싶은 필수적인 데이터만 넣기
        id: 'ellie',
        isAdmin: true, 
    },
    // secret key token 
    secret
);
const edited = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImVsbGllIiwiaXNBZG1pbiI6dHJ1ZSwiaWF0IjoxNjM1ODYzNjk4fQ.V2_V0pt6OcWQ30a5VxLgzvoMo7GaLFuQHnqBxaX_E9A';

// verify를 통해 코드를 악의적으로 변경했는지 확인할 수 있다. 
jwt.verify(edited, secret, (error, decoded) => {
    console.log(error, decoded); 
    // JsonWebTokenError: invalid signature
    // 한번 발행된 코드는 변경되면 안된다. 
});
console.log(token); 

------------- console ------------- 
null { id: 'ellie', isAdmin: true, iat: 1635863698 }
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImVsbGllIiwiaXNBZG1pbiI6dHJ1ZSwiaWF0IjoxNjM1ODYzOTY5fQ.FVlB-qjDOm4sARGgWequqEKDY7pyUxhuIE_2gQZRFQ8
```

sign의 옵션에 expiresIn을 통해 유효 시간 설정이 가능하다.

그 시간을 넘기는 코드가 있을 경우 에러가 발생한다.

```jsx
const jwt = require('jsonwebtoken');

const secret = 'UN1gCmEiijb56eC6VEy!0#G@dCY7mcco';
const token = jwt.sign(
    {
        // payload - token에 담고 싶은 필수적인 데이터만 넣기
        id: 'ellie',
        isAdmin: true, 
    },
    // secret key token 
    secret,
    { expiresIn: 2 }
);

setTimeout(() => {
    // verify를 통해 코드를 악의적으로 변경했는지 확인할 수 있다. 
    jwt.verify(token, secret, (error, decoded) => {
        console.log(error, decoded); 
        // JsonWebTokenError: invalid signature
        // 한번 발행된 코드는 변경되면 안된다. 
    });
}, 3000);

------------ console ------------

TokenExpiredError: jwt expired
    at /Users/syl/Desktop/dreamCoding_code/dream_nodejs/10-auth/node_modules/jsonwebtoken/verify.js:152:21
    at getSecret (/Users/syl/Desktop/dreamCoding_code/dream_nodejs/10-auth/node_modules/jsonwebtoken/verify.js:90:14)
    at Object.module.exports [as verify] (/Users/syl/Desktop/dreamCoding_code/dream_nodejs/10-auth/node_modules/jsonwebtoken/verify.js:94:10)
    at Timeout._onTimeout (/Users/syl/Desktop/dreamCoding_code/dream_nodejs/10-auth/2-jwt.js:17:9)
    at listOnTimeout (internal/timers.js:551:17)
    at processTimers (internal/timers.js:494:7) {
  expiredAt: 2021-11-02T14:46:22.000Z
}
```
