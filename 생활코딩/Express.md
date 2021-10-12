## Express란 무엇인가?
- Node.js를 위한 빠르고 개방적인 간결한 웹 프레임워크 라고 한다.
- 웹 애플리케이션, API 개발을 위해 설계되었다.
- 웹 애필리게이션은 또 무엇인가?
  - 웹 애플리케이션(web application) 또는 웹 앱은 소프트웨어 공학적 관점에서 인터넷이나 인트라넷을 통해 웹 브라우저에서 이용할 수 있는 응용 소프트웨어를 말한다.

## 실습 환경
- 기본적으로 Node.js 가 설치 되어 있어야 함
- process manager를 설치
```
npm install pm2@latest -g // process manager 설치
```
- Express 설치
```
npm install express
```

```JSX
pm2 start main.js --watch //process manager로 실행.
```
- 제대로 실행 되었는지 확인해 보려면 아래와 같은 Command를 입력 한다.
```
pm2 log //에러 체크
```

## Hello World 예제
- root Url에 대한 요청
- Hellow World로 응답
- 3000port에서 수신 대기
```JSX
const express = require('express')
const app = express()
 
app.get('/', (req, res) => res.send('Hello World!'))
app.listen(3000, () => console.log('Example app listening on port 3000!'))
```

## 간단한 예제
```JSX
var express = require('express')
var app = express()
var fs = require('fs');
var template = require('./lib/template.js');
 
//route, routing
//app.get('/', (req, res) => res.send('Hello World!'))
app.get('/', function(request, response) { 
  fs.readdir('./data', function(error, filelist){
    var title = 'Welcome';
    var description = 'Hello, Node.js';
    var list = template.list(filelist);
    var html = template.HTML(title, list,
      `<h2>${title}</h2>${description}`,
      `<a href="/create">create</a>`
    ); 
    response.send(html);
  });
});
 
app.get('/page', function(req, res) { 
  return res.send('/page');
});
 
app.listen(3000, function() {
  console.log('Example app listening on port 3000!')
});
```

## QueryString대신 Params
- Params Syntex

```
Route path: /users/:userId/books/:bookId
Request URL: http://localhost:3000/users/34/books/8989
req.params: { "userId": "34", "bookId": "8989" }
```

```JSX
app.get('/page/:pageId', function(request, response) { 
  fs.readdir('./data', function(error, filelist){
    var filteredId = path.parse(request.params.pageId).base;
    fs.readFile(`data/${filteredId}`, 'utf8', function(err, description){
      var title = request.params.pageId;
      var sanitizedTitle = sanitizeHtml(title);
      var sanitizedDescription = sanitizeHtml(description, {
        allowedTags:['h1']
      });
      var list = template.list(filelist);
      var html = template.HTML(sanitizedTitle, list,
        `<h2>${sanitizedTitle}</h2>${sanitizedDescription}`,
        ` <a href="/create">create</a>
          <a href="/update?id=${sanitizedTitle}">update</a>
          <form action="delete_process" method="post">
            <input type="hidden" name="id" value="${sanitizedTitle}">
            <input type="submit" value="delete">
          </form>`
      );
      response.send(html);
    });
  });
});
```

## Express MiddleWare 사용
- Middle ware 중 Body parser를 사용
- Body Parser를 사용을 위한 설치
```
 npm install body-parser
```
```JSX
var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: false }));
app.use(compression());
```
```JSX
app.post('/create_process', function(request, response){
  var post = request.body;
  var title = post.title;
  var description = post.description;
  fs.writeFile(`data/${title}`, description, 'utf8', function(err){
    response.writeHead(302, {Location: `/?id=${title}`});
    response.end();
  });
});
```


## Middle Ware 만들기
```JSX
app.get('*', function(request, response, next){
  fs.readdir('./data', function(error, filelist){
    request.list = filelist;
    next();
  });
});
```
## 에러 처리
- 기본적인 404 에러(없는 주소)의 경우 가장 아래에 해당 코드로 에러를 처리한다.
```JSX
app.use(function(req,res,next){
  res.status(404).send(`Sorry cant find that!`)
});
```
- 다른 Middle Ware에서 오류가 나올 경우 해당 코드로 에러를 처리할 수 있다.
```JSX
app.use(function(err,req,res,next){
  console.error(err.stack);
  res.status(505).send(`Something broke!`)
});
```


## 라우터 
- 라우터를 분리할 경우 앞의 경로가 기본 경로가 된다.
```JSX
app.use('/topic', topicRouter);
```
- 라우터를 사용할 때 기존과 달리 app이 아닌 express.Router()를 사용한다.
```JSX
var express = require('express');
var router = express.Router();

router.get(`/create`,function(request, response){
    var title = 'WEB - create';
    var description = 'Hello, Node.js';
    var list = template.list(request.list);
    var html = template.html(title, list, `
      <form action="/topic/create_process"
        method = "post">
        <p><input type="test" name="title" placeholder="title"></p>
        <p>
          <textarea name="description" placeholder="description"></textarea>
        </P>
        <p>
          <input type="submit">
        </p>
      </form>
      `,'');
    response.send(html);
});
```

## 보안
- http://expressjs.com/en/advanced/best-practice-security.html#dont-use-deprecated-or-vulnerable-versions-of-express
- Helmet을 사용하면 기본적인 보안은 체크 가능하다.
```
npm install --save helmet

var helmet = require('helmet');
app.use(helmet());
```
- dependencie 관련 에러는 nsp를 설치하여 체크한다.

## Express Generator
- http://expressjs.com/en/starter/generator.html
- 자동으로 웹을 만들기 전 기본 Node.js + Express의 세팅을 해주는 프로그램이다.
```
npm install express-generator -g
```
- express (folder명)으로 자동생성한다.
- 생성이후 npm install을 통해 node_modules를 받는다.
- jade라고 html을 더 축약한 포맷이 있으나, 자세히 다루지 않았다.
