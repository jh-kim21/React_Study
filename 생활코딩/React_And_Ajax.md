

## React & Ajax
### Ajax란 무엇인가?
- Asynchronous Javascript And Xml(비동기식 자바스크립트와 xml)의 약자
- 자바스크립트를 이용해 서버와 브라우저가 비동기 방식으로 데이터를 교환할 수 있는 통신 기능
- 브라우저가 가지고있는 XMLHttpRequest 객체를 이용해서 전체 페이지를 새로 고치지 않고도 페이지의 일부만을 위한 데이터를 로드하는 기법
- 즉, 쉽게 말하자면 자바스크립트를 통해서 서버에 데이터를 비동기 방식으로 요청하는 것이다.

### 실습 준비
> CMD 창에서 다음 Command를 실행한다.
```
npx create-react-app 
npm start 
```

### Ajax Call 을 통한 컴포넌트 초기화

- Component를 초기화 하기 위해서는 다음과 같이 한다.
  1. componentDidMount() 함수를 구현 한다. 처음 Componet가 Mount될 때 호출 되기 때문에 초기화 할 때 사용
  2. fetch함수를 통해서 Json 파일을 Load 
  3. Json string -> Json Object로 변환 
  4. Component의 State값을 변경 한 후, 값을 Return한다.

```JSX
import React, { Component } from 'react';

class Nav extends Component {
  state = {
    list:[]
  }
  componentDidMount(){  //Mount함수를 통한 초기화
    fetch('list.json')  // Json파일 로딩
      .then(function(result){  //.then 함수에 function의 첫번째 인자로 로딩 텍스트
        return result.json();
      })
      .then(function(json){   // .ther 함수의 첫번째 인자로 Json Object
        console.log(json);
        this.setState({list:json}); //State에 List를 초기화 한다.
      }.bind(this));
  }
  render(){     //초기화 된 List를 통해서 Component결과를 Return 한다.
    var listTag = [];
    for(var i=0; i<this.state.list.length; i++){
      var li = this.state.list[i];
      listTag.push(<li key={li.id}><a href={li.id}>{li.title}</a></li>)
    }
    return (
      <nav>
        <ul>
          {listTag}
        </ul>
      </nav>
    );
  }
}
... 생략
export default App;
```

### Ajax로 컴포넌트 상태 변경
- Component에서 Ajax로 데이터를 교환하면 Component를 재사용하기 어렵다
- 따라서 App으로 종속성을 이동시키는 작업이 필요하다.
- Component의 값을 변경 하기 위해 할 일
  1. Nav Component의 Props로 Callback function? ^^; 을 넘겨준다.
  2. Callback Function은 파라미터로 Id 값을 받는다.
  3. Id 값을 통해서 Id.Json 파일을 Loading 한 후 Json Oject로 변환
  4. State 값을 변경 한 후, Article의 Props에 값을 넘긴다.
  5. `<a href>` 에 Click Props로 Callback function을 넘긴다.
  6. Nav Props로 전달된 Function을 호출 한다.
```JSX

class App extends Component {
  state = {
    article:{title:'Welcome', desc:'Hello, React & Ajax'}
  }
  render(){
    return (
      <div className="App">
        <h1>WEB</h1>
        <Nav onClick={function(id){  //1. Nav Component의 Props로 Callback Function? ^^; 을 넘겨준다.
          fetch(id+'.json')          //2. Callback Function은 파라미터로 Id 값을 받는다.
            .then(function(result){  //3. Id 값을 통해서 Id.Json 파일을 Loading 한 후 Json Oject로 변환
              return result.json();
            })
            .then(function(json){
              this.setState({         //4. State 값을 변경 한 후, Article의 Props에 값을 넘긴다.
                article:{
                  title:json.title,
                  desc:json.desc
                }
              })
            }.bind(this));
        }.bind(this)}></Nav>
        <Article title={this.state.article.title} desc={this.state.article.desc}></Article>
      </div>
    )
  }
}

class Nav extends Component {
  state = {
    list:[]
  }
  componentDidMount(){
    fetch('list.json')
      .then(function(result){
        return result.json();
      })
      .then(function(json){
        console.log(json);
        this.setState({list:json});
      }.bind(this));
  }
  render(){
    var listTag = [];
    for(var i=0; i<this.state.list.length; i++){
      var li = this.state.list[i];
      listTag.push(
        <li key={li.id}>    
          <a href={li.id} data-id={li.id} onClick={function(e){  //5. <a href> 에 Click Props로 Callback function을 넘긴다.
            e.preventDefault();                                  //6. Nav Props로 전달된 function을 호출 한다.
            console.log('trigger');
            this.props.onClick(e.target.dataset.id);
          }.bind(this)}>
            {li.title}
          </a>
        </li>)
    }
    return (
      <nav>
        <ul>
          {listTag}
        </ul>
      </nav>
    );
  }
}

```

### 프리젠테이션 컴포넌트와 컨테이너 컴퍼넌트로 분업화

- 프리젠테이션 컴포넌트
  - 시각적인 표현만을 담당하고, 다른 앱에 부품으로 사용될 수 있는 컴포넌트
  - 데이터로부터 종속되지 않은 컴포넌트
  - `Nav` 컴포넌트 : 정보를 시각화하는 컴포넌트
- 컨테이너 컴포넌트
  - 프리젠테이션 콤포넌트를 사용해서 앱에 맞게 제어하는 컴포넌트
  - 데이터처리, 사용자 상호작용 처리 
  -  `App` 컴포넌트 : list.json 정보 가져오는 컴포넌트

```jsx
class Nav extends Component {
  render(){
    var listTag = [];
    for(var i=0; i<this.props.list.length; i++){
      var li = this.props.list[i];
      listTag.push(
        <li key={li.id}>
          <a href={li.id} data-id={li.id} onClick={function(e){
            e.preventDefault();
            console.log('trigger');
            this.props.onClick(e.target.dataset.id);
          }.bind(this)}>
            {li.title}
          </a>
        </li>)
    }
    return (
      <nav>
        <ul>
          {listTag}
        </ul>
      </nav>
    );
  }
}

class Article extends Component{
  render(){
    return(
      <article>
        <h2>{this.props.title}</h2>
        {this.props.desc}
      </article>
    );
  }
}

class App extends Component {
  state = {
    article:{title:'Welcome', desc:'Hello, React & Ajax'},
    list:[]
  }
  componentDidMount(){
    fetch('list.json')
      .then(function(result){
        return result.json();
      })
      .then(function(json){
        console.log(json);
        this.setState({list:json});
      }.bind(this));
  }
  render(){
    return (
      <div className="App">
        <h1>WEB</h1>
        <Nav list={this.state.list} onClick={function(id){
          fetch(id+'.json')
            .then(function(result){
              return result.json();
            })
            .then(function(json){
              this.setState({
                article:{
                  title:json.title,
                  desc:json.desc
                }
              })
            }.bind(this));
        }.bind(this)}></Nav>
        <Article title={this.state.article.title} desc={this.state.article.desc}></Article>
      </div>
    )
  }
}
```

