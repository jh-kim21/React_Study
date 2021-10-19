## Passport
- 보안을 위한 MiddleWare
- 기본적으로 Session을 사용한다.
- Session을 바탕으로 사용하기 때문에 Session 아래부분에 구현해야만 한다.

### 설치 Command
```
npm install passport
npm install passport-local
```

### Passport 설치
```JSX
app.use(session({
  secret: 'asadlfkj!@#!@#dfgasdg',
  resave: false,
  saveUninitialized: true,
  store:new FileStore()
}))

var passport = require('passport')
  , LocalStrategy = require('passport-local').Strategy;
```

- session 활성화하는 코드 다음 다음 부분에 넣어야 한다.

### 인증 구현
- 성공했을경우 successRedirect로 보낸다.
- 실패했을경우 failureRedirect로 보낸다.
```JSX
  app.post('/auth/login_process',
    passport.authenticate('local', {
       successRedirect: '/',
       failureRedirect: '/auth/login' 
    }));
```

### 자격 확인
- done을 이용하여 자격 확인할 수 있다.
- Default로 LocalStrategy는 `username`, `password` parameter에서 자격증명을 찾는다.
- 자격확인의 필드값을 usernameField, passwordField를 변경하여 지정해줄 수 있다.

```JSX
  passport.use(new LocalStrategy(
    {
    usernameField: 'email',
    passwordField: 'pwd'
    },
    function(username, password, done) {
      if(username === authData.email){
        if(password === authData.password)
        {
          return done(null, authData);
        }else{
          return done(null, false, { message: 'Incorrect password.' });
        }
      }else{
        return done(null, false, {
           message: 'Incorrect username.'
         });
      }
    }
  ));
```

### 세션 이용
- serializeUser는 로그인이 성공하였을 경우 한번 출력된다.
- deserializeUser는 재접속하였을 때 로그인한 사용자인지 확인하는 데 사용된다.
- done으로 user 정보를 보낼 경우 request.user에 로그인 사용자의 정보가 들어있다.
- logout시에는 request.logout()을 사용한다.
```JSX
app.use(passport.initialize());
app.use(passport.session());

passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  done(null, authData);
});
```

### 플래쉬 메시지
- 로그인이 틀렸을 경우 경고메시지와 같은 일회성 메시지를 출력하는 코드이다.
- Session 아래에 use 구현한다.
- request.flash()에 플래쉬 메시지가 들어간다.
- failureFlash가 true일 경우 실패할때 메시지가 들어간다.
- successFlash가 true일 경우 성공할때 메시지가 들어간다.
```
npm install -s connect-flash
```

```JSX
var flash = require('connect-flash')
app.use(flash())
  app.post('/auth/login_process',
    passport.authenticate('local', {
       successRedirect: '/',
       failureRedirect: '/auth/login',
       failureFlash: true,
       successFlash: true,
    }));
```
