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

## 라우터 

## 보안

## Express Generator
