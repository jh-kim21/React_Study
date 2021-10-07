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
- 
```JSX
const express = require('express')
const app = express()
 
app.get('/', (req, res) => res.send('Hello World!'))
app.listen(3000, () => console.log('Example app listening on port 3000!'))
```

## 간단한 예제

## QueryString을 대체한 Params

## Express MiddleWare 사용

## Middle Ware 만들기

## 에러 처리

## 라우터 

## 보안

## Express Generator
