[공식 문서](https://www.notion.so/6fa316a6e3ec4682a2c25991ca2857a8)

[Stream & Buffer](https://www.notion.so/Stream-Buffer-a1a3f8437da046a790d5c6e8f18dd04b)

## **Achievement Goals**

- HTTP
  - HTTP 요청/응답을 브라우저를 통해 확인할 수 있고, 해당 내용을 읽을 수 있다.
  - HTTP 다양한 요청 방식과, 응답 코드에 대해 이해할 수 있다.
- node.js modules의 사용

  - node.js의 내장 http 모듈을 사용할 수 있다.
  - http 모듈 사용시에 서버에 CORS 설정을 할 수 있다.

    : 서버에서 Allow 하는 조건들을 다 맞추가 있는지 (사전에 서버에 확인하는 요청)

    ```jsx
    const defaultCorsHeader = {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
      "Access-Control-Allow-Headers": "Content-Type, Accept",
      "Access-Control-Max-Age": 10,
    };
    ```

    - 모든 도메인(`*`)을 허용한다.
    - 메소드는 `GET POST PUT DELETE OPTIONS` 만 허용한다.
    - 헤더에는 `content-type`과 `accept`만 쓸 수 있다.
    - `preflight request` 는 10초까지 허용 된다.

  - CommonJS를 이용한 모듈 내보내기/불러오기를 할 수 있다.

- 라우팅과 API

  - 라우팅(조건에 따른 분기)을 이해하고, 이를 서버 코드에서 구현할 수 있다.
  - 클라이언트가 사용할 수 있도록, 서버 API 문서를 직접 작성할 수 있다.

    ```jsx
    const http = require("http");

    const PORT = 5000;

    const ip = "localhost";

    const server = http.createServer((request, response) => {
      // const { method, url } = request;

      if (request.method === "OPTIONS") {
        // CORS 설정을 돌려줘야 한다.
        response.writeHead(200, defaultCorsHeader);
        response.end();
      }

      if (request.method === "POST" && request.url === "/upper") {
        let body = [];
        // 대문자로 바꾸기
        request
          .on("data", (chunk) => {
            body.push(chunk);
            console.log("SHOW ME CHUNK!!!!!", chunk);
          })
          .on("end", () => {
            console.log(body); // [ <Buffer 22 22....> ]

            body = Buffer.concat(body).toString().toUpperCase();
            response.writeHead(200, defaultCorsHeader);
            response.end(body);
            console.log(body);
          });
      } else if (request.method === "POST" && request.url === "/lower") {
        // 소문자로 바꾸기
        let body = [];
        request
          .on("data", (chunk) => {
            body.push(chunk);
          })
          .on("end", () => {
            body = Buffer.concat(body).toString().toLowerCase();
            response.writeHead(200, defaultCorsHeader);
            response.end(body);
          });
      } else {
        // 에러 처리. bad request
        response.statusCode = 404;
        response.end();
      }

      console.log(
        `http request method is ${request.method}, url is ${request.url}`
      );
      // response.writeHead(200, defaultCorsHeader);
      // response.end('hello mini-server sprints');
    });

    server.listen(PORT, ip, () => {
      console.log(`http server listen on ${ip}:${PORT}`);
    });

    const defaultCorsHeader = {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
      "Access-Control-Allow-Headers": "Content-Type, Accept",
      "Access-Control-Max-Age": 10,
    };
    ```

- Express 라이브러리

  - express 라이브러리가 어떤 작업을 단순하게 만드는지 이해할 수 있다.

    ```jsx
    const express = require("express");
    const cors = require("cors");

    const app = express();
    app.use(cors());
    app.use(express.json({ strict: false }));
    const express = require("express");
    const cors = require("cors");

    const app = express();
    app.use(cors());
    app.use(express.json({ strict: false }));

    const port = 5000;
    const ip = "localhost";
    app.use(express.static("client"));

    app.get("/", (res, req) => {
      res.send("hello world");
    });

    app.post("/upper", (req, res) => {
      let result = req.body;
      result = result.toUpperCase();
      res.json(result);
      console.log(result);
    });

    app.post("/lower", (req, res) => {
      let result = req.body;
      result = result.toLowerCase();
      res.json(result);
      console.log(result);
    });

    app.listen(port, ip, () => {
      console.log(`http server listen on ${ip}:${port}`);
    });
    ```

  - 미들웨어의 개념을 이해할 수 있다.

    - 자동차 공장에서는 컨베이어 벨트 위에 올려진 자동차의 뼈대에, 각 공정마다 부품을 추가한다.
    - 모든 부품이 추가되면 완성된 자동차가, 어딘가 문제가 있다면 불량품이 결과물로 나오게 된다.
    - 미들웨어는 자동차 공장의 공정과 비슷하다.
    - 컨베이어 벨트 위에 올라가 있는 request에 필요한 기능을 더하거나, 문제가 발견된 불량품을 밖으로 걷어내는 역할을 한다.
    - 미들웨어는 express의 가장 큰 장점이다.

  - 자주 사용하는 미들웨어

    - 미들웨어를 사용하는 상황

      - 모든 요청에 대해 url이나 메소드를 확인할 때
      - POST 요청 등에 포함된 body(payload)를 구조화할 때 (쉽게 얻어내고자 할 때)
      - 모든 요청/응답에 CORS 헤더를 붙여야 할 때

        ```jsx
        const defaultCorsHeader = {
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
          "Access-Control-Allow-Headers": "Content-Type, Accept",
          "Access-Control-Max-Age": 10,
        };

        // 생략
        if (req.method === "OPTIONS") {
          res.writeHead(201, defaultCorsHeader);
          res.end();
        }
        ```

        [cors 미들웨어](https://github.com/expressjs/cors)를 사용하면 이 과정을 간단하게 처리할 수 있다.

        ```jsx
        const cors = require("cors");

        // 생략
        app.use(cors()); // 모든 요청에 대해 CORS 허용
        ```

        ```jsx
        const cors = require('cors')

        // 생략
        // 특정 요청에 대해 CORS 허용
        app.get('/products/:id', cors(), (req, res, next) => {
        	res.json({msg: 'This is CORS-enabled for a Single Route'})
        }
        ```

      - 요청 헤더에 사용자 인증 정보가 담겨있는지 확인할 때

    - 미들웨어를 사용하면 Node.js만으로 구현한 서버에서는 다소 번거로울 수 있는 작업을 보다 쉽게 적용할 수 있다.

  - **미들웨어 작동 원리**

  [Express 앱에서 사용하기 위한 미들웨어 작성](https://expressjs.com/ko/guide/writing-middleware.html)

- 서버 개발과 디버깅
  - CRUD 를 수행하는 웹 서버 개발 방법을 익힐 수 있다.
  - 서버 개발을 돕는 다양한 툴들을 익힐 수 있다.
