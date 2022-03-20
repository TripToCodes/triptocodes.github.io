---
layout: single
title: "스프린트 short.ly (부제: Sequelize)"
---

### Sequelize

> Sequelize는 Postgres, MySQL, MariaDB 등을 위한 promise기반의 ORM이다. 현재 V7까지 배포되었는데 V5를 한국어로 번역해 놓으신 분이 있어서 블로그 작성에 많이 참고하였다. 
- [pjt3591oo님의 깃허브](https://pjt3591oo.github.io/sequelizejs_translate/build/html/index.html) 
- [Sequelize 공식문서](https://sequelize.org/v7/manual/getting-started.html)

<br><br>

#### 데이터베이스에 연결

- 데이터베이스를 생성하기 위해, Sequelize 인스턴스를 생성해야 합니다. 생성자에게 하나의 연결 URI를 전달하거나 **연결 파라미터 변수를 개별적으로 전달**하여 Sequelize 인스턴스 생성이 가능합니다.

>![Connecting to a database](https://images.velog.io/images/vas-y-somi/post/b63e8cd2-83b3-4813-8638-c168b08ac36d/Screen%20Shot%202022-03-18%20at%2010.10.32%20PM.png)


- models/index.js 에도 공식문서의 'database', 'username', 'password', { host, dialect }에 해당되는 값이 전달된 것을 볼 수 있다.
```js
let sequelize;
if (config.use_env_variable) {
  sequelize = new Sequelize(process.env[config.use_env_variable], config);
} else {
  sequelize = new Sequelize(
    config.database, // mysql> create database [db 이름]으로 생성한 것
    config.username, // root
    config.password, // mysql 비번
    config // host와 dialect가 config.json 파일에 있음
  );
}
```
<br><br>

#### 1. ORM 설정
>cli를 통해 ORM을 잘 사용할 수 있도록 bootstraping(프로젝트 초기 단계를 자동으로 설정할 수 있도록 도와주는 일)을 해줘야 합니다.

기본적으로 `npm i sequelize`로 Sequelize를 설치한 뒤 마이그레이션 파트를 봐야 한다. **Migrations-Installing the CLI/Project bootstrapping**를 보고 다음 명령어를 실행한다.
```
npm install --save-dev sequelize-cli
npx sequelize-cli init
```
`config`, `models`, `migrations`, `seeders` 폴더들이 생성될 것이다. 
```js
// config.js
{
  "development": {
    "username": "root",
    "password": null,
    "database": "database_development",
    "host":
    "127.0.0.1",
    "dialect": "mysql"
  },
 ...
}
```

위의 파일에서 `password`를 MySQL 비밀번호로 변경하고 `shortlyMvc`라는 데이터베이스도 생성한다.
```
mysql> create database shortlyMvc;
```


<br><br>

#### 2. 모델 생성하기
(1-2) 모델 생성을 통과하기 위해서는 공식문서 **Migrations-Creating the first Model (and Migration)**를 참고한다. 박스 부분을 url 과 url:string,title:string,visits:integer 로 바꾸면 된다.
>![](https://images.velog.io/images/vas-y-somi/post/6e41f9dd-6eaa-4f6c-a27b-dca8e8100c64/Screen%20Shot%202022-03-18%20at%2010.48.58%20PM.png)

이 명령문은
1. `models`폴더에 url.js이라는 모델 파일을 생성하고 
2. `migrations` 폴더에 `XXXXXXXXXXXXXX-create-url.js`라는 마이그레이션(스키마 변경에 따른 데이터 이주) 파일을 생성할 것이다.

>1번 파일을 보자.
```js
'use strict';
const {
  Model
} = require('sequelize');
module.exports = (sequelize, DataTypes) => {
  class url extends Model {
    /**
     * Helper method for defining associations.
     * This method is not a part of Sequelize lifecycle.
     * The `models/index` file will call this method automatically.
     */
    static associate(models) {
      // define association here
    }
  }
  url.init({
    url: DataTypes.STRING,
    title: DataTypes.STRING,
    visits: DataTypes.INTEGER // visits의 값으로 { type, defaultValue } 객체를 넣어 값에 DataTypes.INTEGER 와 0을 넣어야 한다.
  }, {
    sequelize,
    modelName: 'url',
  });
  // the defined model is the class itself
  console.log(url === sequelize.models.url); // true
  return url;
};
```

Sequelize에서 모델을 정의하는 방법은 두 가지로 하나는 `sequelize.define(modelName, attributes, options)`이고 다른 하나는 `extends`를 써서 `init(attributes, options)`을 호출하는 것인데 위에 생성된 파일을 보면 두번째 방법으로 정의된 것을 볼 수 있다.

![](https://images.velog.io/images/vas-y-somi/post/b47986fe-0666-47e4-bd14-4e8d8e6345a2/Screen%20Shot%202022-03-18%20at%2011.12.19%20PM.png)


**Note**
그러나 이 단계까지는 데이터베이스에 아무것도 삽입하지 않았다. 실제로 데이터베이스에 해당 테이블을 *생성*하려면 다음과 같은 명령을 실행해야 한다.

```
npx sequelize-cli db:migrate
```


이제 urls라는 테이블에 기본적으로 생성되는 id, createdAt, updatedAt과 지정했던 3개의 열이 모두 생성될 것이다.

<br><br>


#### 3. Controller 작성
    2) links controller 파일이 존재해야 합니다
    3) links controller에는 get, post 메소드가 각각 존재해야 합니다
    
controllers/links/index.js를 만들고 get, post, 그리고 처음보는 redirect 메서드를 작성해야 했다.

>**Pseudo code**
0. controllers/links/index.js를 생성한다.
1. /modules/utils에 url 유효성 검사를 하고 url title만 따오는 함수들을 불러온다.
2. /models 에서 url 인스턴스를 불러온다.
3. module.exports = { get: (callback), post: (callback), redirect: (callback) } 의 형태로 틀을 짠다.
4. 공식문서 [**Model Querying - Finders**](https://sequelize.org/v7/manual/model-querying-finders.html)의	`findAll`, `findByPk`,`findOne`, `findOrCreate`와 같은 다양한 메서드를 이용해 인스턴스 모델에서 특정 정보를 조회하고 request에 상응하는 response를 리턴한다.

```js
const { isValidUrl, getUrlTitle } = require("../../modules/utils");
const { url: URLModel } = require("../../models"); 
// 모델을 url로 그냥 불러오면 테이블 안의 url과 헷갈리기 때문에 
// 구조분해할당에서 URLModel로 이름을 바꾼다.

module.exports = {
  // 7) GET /links는 urls 테이블의 목록을 JSON의 형태로 반환합니다
  // findAll 메서드를 통해 SELECT 쿼리를 날리고 데이터 전송이 
  // 완료된 후에 res에 실어보내줘야 하므로 async/await을 잊지 말자.
  get: async (req, res) => {
    const data = await URLModel.findAll();
    res.status(200).json(data);
  },
  // 6) POST /links은 url을 받아 단축 url로 만듭니다
  post: (req, res) => {
    const { url } = req.body;
    // url 유효성 검사해서 유효하지 않은 경우 에러코드 전달
    if (!isValidUrl(url)) {
      return res.sendStatus(400);
    }
    
	// title만 추려내는 코드에는 create 보다는 findOrCreate을 
    // 써서 이미 있는 인스턴스가 테이블에 중복 생성되지 않도록 한다.
    getUrlTitle(url, async (err, title) => {
      if (err) {
        console.log(err);
        return res.sendStatus(400);
      }

      await URLModel.findOrCreate({
          where: { url: url },
          defaults: { title: title } 
        // defaults는 where로 찾은 것이 아무것도 없을 때 
        // 꼭 생성해야 하는 인스턴스를 정해주는 것
        })
        .then(([result, created]) => {
        // findOrCreate은 찾았거나 생성한 객체와 boolean을 담은
        // 배열을 리턴한다. 새로 생성된 것이 있으면 true, 없으면 false
          if (!created) { // 생성한 것이 없을때 ---> Found
            return res.status(201).json(result); 
          }
          res.status(201).json(result); // Created
        })
        .catch(error => {
          console.log(error);
          res.sendStatus(500); // Server error
        });
    });
  },
  // 8) GET /links/:id 을 요청하면 url 필드값으로 리디렉션합니다
  // 9) GET /links/:id 을 요청하면, 해당 레코드의 visit count가 1 증가해야 합니다
  redirect: async (req, res) => {
    await URLModel
      .findByPk(req.params.id)
      .then(result => {
        if (result) {
          return result.update({
            visits: result.visits + 1
          }); // result.increment("visits", { by: 1 })도 가능
        } else {
          res.sendStatus(204);
        }
      })
      .then(result => {
        res.redirect(result.url);
      // The res.redirect() function lets you redirect the user 
      //to a different URL by sending an HTTP response with status 302.
	  // app.get('/from', (req, res) => {
	  //   res.redirect('/to');
	  // });
      })
      .catch(error => {
        console.log(error);
        res.sendStatus(500);
      });
  }
}
```


<br><br>


#### 4. Router 연결

```js
const express = require("express");
const router = express.Router();
const linksController = require("../controllers/links");

router.get("/", linksController.get);
router.post("/", linksController.post);
router.get("/:id", linksController.redirect);

module.exports = router;

```

- res.redirect() 메서드는 302 상태메세지로 HTTP 응답을 보내고 유저가 다른 URL로 접속되도록 한다.
```js
app.get('/from', (req, res) => {
  res.redirect('/to'); // /from 으로 접속하면 /to 로 보냄
});
app.get('/to', (req, res) => res.send('Hello, World!'));
```


