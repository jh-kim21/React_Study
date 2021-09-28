# 5. Node.js 로 웹 서버 만들기

> node main.js 

Terminal에 입력하면 웹 서버가 구동된다.

```js
response.end();
```

`response.end()` 에 어떤 코드를 넣느냐에 따라 사용자에게 보내는 데이터가 달라진다. 프로그래밍적으로 사용자에게 보낼 데이터를 생성한다.

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


