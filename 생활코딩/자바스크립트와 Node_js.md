# 5. Node.js 로 웹 서버 만들기

> node main.js 

Terminal에 입력하면 웹 서버가 구동된다.

```js
response.end();
```

`response.end()` 에 어떤 코드를 넣느냐에 따라 사용자에게 보내는 데이터가 달라진다. 프로그래밍적으로 사용자에게 보낼 데이터를 생성한다.

```js
response.writeHead(200);
/*
200 : 정상
303 : 리렉션
404 : Not Found
*/
```
`response.writeHead()`에 약속된 상태 코드를 넣어 헤더 정보를 내보낼 수 있다.


# 9. URL의 이해

![URL 그림](https://blog.kakaocdn.net/dn/cqfXty/btqFKB7VkO4/SA8CZYv0nTgKq4L81WcqmK/img.png)

- query string 값을 변경하면 웹 서버에게 데이터를 전달할 수 있다.

# 10. URL을 통해서 입력된 값 사용하기

- query string parsing 하기

```js
    var queryData = url.parse(_url, true).query;
    console.log(queryData.id);
    
    //
    response.end(queryData.id);
```

- url 뒤에 `?id=xxx` 를 입력하면 query string을 받아오는 것을 확인할 수 있다.

# 11. App 제작 - 동적인 웹페이지 만들기

- query string과 제목을 동적으로 연결한다.
   - query string `?id=CSS` 입력시 제목이 변경된다.
   - 목차에서 CSS 클릭시 query string이 `?id=CSS`로 변경된다.
- 초기(home) 화면 (query string이 없을 때)의 제목을 `Welcome`으로 보여준다.

```js
    var queryData = url.parse(_url, true).query;
    var title = queryData.id;
    
    if(_url == '/'){
      title = 'Welcome';
    }
    if(_url == '/favicon.ico'){
        response.writeHead(404);
        response.end();
        return;
    }
    response.writeHead(200);
    var template = `
    <!doctype html>
    <html>
    <head>
    <title>WEB1 - ${title}</title>
    <meta charset="utf-8">
    </head>
    <body>
    <h1><a href="/">WEB</a></h1>
    <ul>
        <li><a href="/?id=HTML">HTML</a></li>
        <li><a href="/?id=CSS">CSS</a></li>
        <li><a href="/?id=JavaScript">JavaScript</a></li>
    </ul>
    <h2>${title}</h2>
    <p><a href="https://www.w3.org/TR/html5/" target="_blank" title="html5 speicification">Hypertext Markup Language (HTML)</a> is the standard markup language for <strong>creating <u>web</u> pages</strong> and web applications.Web browsers receive HTML documents from a web server or from local storage and render them into multimedia web pages. HTML describes the structure of a web page semantically and originally included cues for the appearance of the document.
    <img src="coding.jpg" width="100%">
    </p><p style="margin-top:45px;">HTML elements are the building blocks of HTML pages. With HTML constructs, images and other objects, such as interactive forms, may be embedded into the rendered page. It provides a means to create structured documents by denoting structural semantics for text such as headings, paragraphs, lists, links, quotes and other items. HTML elements are delineated by tags, written using angle brackets.
    </p>
    </body>
    </html>
    `;

    response.end(template);
```

# 12. Node.js의 파일 읽기 기능

- javaScript 파일 읽는 방법 

```js
var fs = require('fs');
fs.readFile('sample.txt', 'utf8', function(err, data){
    console.log(data);
});
```

# 13. App 제작 - 파일을 이용해 본문 구현

- file의 데이터를 읽어서 query string이 바뀔 때마다 데이터를 업데이트한다.

```js

    fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description){
        var template = `
        <!doctype html>
        <html>
        <head>
        <title>WEB1 - ${title}</title>
        <meta charset="utf-8">
        </head>
        <body>
        <h1><a href="/">WEB</a></h1>
        <ul>
            <li><a href="/?id=HTML">HTML</a></li>
            <li><a href="/?id=CSS">CSS</a></li>
            <li><a href="/?id=JavaScript">JavaScript</a></li>
        </ul>
        <h2>${title}</h2>
        <p>${description}</p>
        </body>
        </html>
        `;

        response.end(template);
    })
```

# 19. App 제작 - Not found 구현

-  `pathname` 을 이용하여 존재하지 않는 파일 호출 시 Not found를 보여준다.

```js
var pathname = url.parse(_url, true).pathname;
    var title = queryData.id;
    
    if (pathname == '/'){
        fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description){
        var template = `
            <!doctype html>
            <html>
            <head>
            <title>WEB1 - ${title}</title>
            <meta charset="utf-8">
            </head>
            <body>
            <h1><a href="/">WEB</a></h1>
            <ul>
                <li><a href="/?id=HTML">HTML</a></li>
                <li><a href="/?id=CSS">CSS</a></li>
                <li><a href="/?id=JavaScript">JavaScript</a></li>
            </ul>
            <h2>${title}</h2>
            <p>${description}</p>
            </body>
            </html>
            `;

            response.writeHead(200);
            response.end(template);
        });
    }
    else
    {
        response.writeHead(404);
        response.end('Not found');
    }
```
# 32. post 방식으로 전송된 데이터 받기

- form의 method에 "post"를 사용할 경우 url에 하위 속성 내용들이 숨겨진다.
- request.on('data')로 post로 전송한 데이터를 수신할 수 있다.

```js
/*
Post로 create_process에 하위 속성을 전달한다.
*/
var template = templateHTML(title, list, `
          <form action="http://localhost:3000/create_process" method="post">
            <p><input type="text" name="title" placeholder="title"></p>
            <p>
              <textarea name="description" placeholder="description"></textarea>
            </p>
            <p>
              <input type="submit">
            </p>
          </form>
        `);

/*
숨겨진 속성(배열)을 받아서 parse한다.
*/
else if(pathname === '/create_process'){
      var body = '';
      request.on('data', function(data){
          body = body + data;
      });
      request.on('end', function(){
          var post = qs.parse(body);
          var title = post.title;
          var description = post.description
      });
      response.writeHead(200);
      response.end('success');
    } 
```
# 29. Node.js의 패키지 매니저와 PM2

- pm2를 사용하면 서버 관리를 쉽게 할 수 있다.
  - 서버가 닫히면 자동으로 열린다.
  - js 업데이트시 자동으로 업데이트된다.
  - Log를 통해 서버 상태를 확인하기 쉽다.

```
//설치
npm install pm2 -g

//실행 (--watch 사용시 자동으로 update)
pm2 start main.js (--watch)

//모니터링
pm2 monit

//현재 관리중인 목록 확인
pm2 list

//로그 확인
pm2 log

//종료
pm2 stop main

```


# 33. 파일 생성과 리디렉션

- 파일 생성은 `fs.writeFile(파일path, 내용, 인코딩방식, function)`으로 가능하다.
- 페이지 이동이 필요할 경우 `response.writeHead(302, {Location: `이동할 url`});`을 사용하여 리다이렉션한다.

```js
else if(pathname === '/create_process'){
      var body = '';
      request.on('data', function(data){
          body = body + data;
      });
      request.on('end', function(){
          var post = qs.parse(body);
          var title = post.title;
          var description = post.description;
          fs.writeFile(`data/${title}`, description, 'utf8', function(err){
            response.writeHead(302, {Location: `/?id=${title}`});
            response.end();
          })
      });
    }
```

# 46. 입력정보에 대한 보안

- xss 공격과 같이 Input등의 입력 정보를 가지고 서버의 Path를 통해서 파일을 조회하는 경우 `../`등을 사용해 정보를 가져갈 수 있다.
- `require('path')`를 사용하여 해당 path의 base 정보만 가져와 상위, 하위폴더의 탐색을 막을 수 있다.
- 
```js
fs.readdir('./data', function(error, filelist){
        var filteredId = path.parse(queryData.id).base;
        ...
        });
```
