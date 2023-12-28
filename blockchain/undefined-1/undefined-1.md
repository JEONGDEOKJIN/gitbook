# 대체 비동기가 왜 문제가 되고, 어떻게 대처해야 하는지를 고민해봄

* 그림 👉👉 \[\[2023-10-06 09.46.36\_비동기처리과정이해]] 여기에서 그림 ⭐⭐⭐

## TOC

1. 1️⃣ 문제 상황 : 비동기 처리를 하면, 결과값이 불확실하게 도착한다. 그로 인해, 많은 오류가 발생한다.
2. 2️⃣ 이 상황을 이해하기 위해서 해야 할 것
3. 3️⃣ App.js 실행 순서를 따라가보자.
4. 4️⃣ custom hook 실행 순서 이해하기
5. 5️⃣ 비동기 처리 다시 이해
6. 6️⃣ 비동기 처리된 데이터를 안전하게 받기

### 1️⃣ 문제 상황 : 비동기 처리를 하면, 결과값이 불확실하게 도착한다. 그로 인해, 많은 오류가 발생한다.

* 문제 상황 : 비동기 함수가 호출되는 부분에서, 어떤 함수를 위로 놓으면 정상작동하고, 어떤 함수를 아래로 놓으면 오류가 발생한다.\
  ![](https://i.imgur.com/0HgkDQz.png)
* 비동기 처리된 데이터를 받아오는 부분 ![](https://i.imgur.com/DPAnH2r.png)

### 2️⃣ 이 상황을 이해하기 위해서 해야 할 것

```BASH
이 상황을 이해하기 위해 우선 

1. 비동기 함수가 REACT 의 APP.JS 에서 호출되고 있었으므로, 우선, 'REACT 의 실행 순서'를 익히고 
2. 그 다음, 문제가 되는 변수인 user 의 값을 가져오는 'web3.hook 파일의 순서'를 이해하자. 
	- 이 부분에서 자연스럽게 비동기 처리를 공부하게 될 것 

3. 그리고, 어떻게 비동기 상황을 코드로 처리하면 효율적일지를 꼭 정리해보자. 
```

\


### 3️⃣ App.js 실행 순서를 따라가보자.

* App.js 실행 순서 일반론

```bash
1. 초기화 및 state(변수 및 set 함수) 설정 

2. 컴포넌트 렌더링 : App.js 함수 본문 실행 -> jsx 반환 
	- 아직 렌더링이 되지는 않음 
	- 이 jsx 는 리액트에게 '지시사항' 이 된다. 
	- 예시 👇👇👇 : 아래와 같이 jsx 코드를 뱉어낸다. 
			<div>
			  <p>현재 카운트: {count}</p>
			  <button onClick={() => setCount(count + 1)}>증가</button>
			</div>

3. useEffect 실행 
	- dependency 중 [ ] 설정된 것이 실행 
	- 컴포넌트가 마운트 될 때만 실행

4. DOM 업데이트 
	- 반환된 JSX에 따라 실제 DOM이 업데이트 된다. 
	- 그래서, useState 에 set 함수 설정을 해도, 바로 업데이트 되지 않을 수 있음. (#❓❓)

5. 나머지 useEffect 실행 
	- dependency 배열에 명시된 상태 혹은 props 가 변할 경우 

6. 상태 또는 props 변경
	- 컴포넌트의 '상태'나 'props'가 변경되면 컴포넌트는 '재렌더링' 된다.
	- 이 때, 변경된 상태나 props에 '의존'하는 `useEffect`가 다시 실행될 수 있습니다

7. 컴포넌트 언마운트 
	- 컴포넌트가 DOM에서 제거될 때, `useEffect`에서 반환된 'cleanup 함수'가 실행됩니다. (#❓❓)
```

* 예시 코드

```js
import React, { useState, useEffect } from 'react';

function App() {
  // 1. 초기화 및 State 설정 
	  // 1) 기본값을 변수에 넣고 2) 상태 변수와 set함수 를 설정.  
  const [count, setCount] = useState(0);

  // 2. 컴포넌트 렌더링
  console.log('컴포넌트 렌더링');

  // 3. useEffect: 컴포넌트가 마운트될 때만 실행 
	  // dependency 가 [] 이므로, 해당 페이지가 로딩될 때만 실행된다. 
  useEffect(() => {
    console.log('컴포넌트가 마운트될 때 실행');
    
    return () => {
      console.log('컴포넌트가 언마운트될 때 실행');
    };
  }, []);

  // 5. 나머지 useEffect: count 상태가 변경될 때마다 실행
  useEffect(() => {
    console.log('count 상태가 변경될 때마다 실행');
  }, [count]);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        클릭 횟수: {count}
      </button>
    </div>
  );
}

export default App;

```

\


### 4️⃣ custom hook 실행 순서 이해하기

* 이러한 흐름 속에서 대체 왜 hook 파일의, 이 setweb3를 올리고 내리느냐에 따라서, -> app.js 에서 빈값이 생기고, 안 생기는 걸까. ⭐⭐⭐
* 그러면, hook.js 파일은 어떤 순서로 실행되는 걸까

```bash
1. import 읽기 

2. useWeb3 함수 

	2.1 초기화 및 state 설정 
	2.2 컴포넌트 렌더링 : 함수 본문 실행 -> jsx 반환
	2.3 useEffect 실행 : [ ] deps 실행 
		- 메타마스크 로그인 되어 있는 상태이므로, window.ethereum 거쳐서, -> wallet address 받음 
		- wallet address input 되고 -> web3 라이브러리 거쳐서 -> balance 받음 
	2.4 DOM 업데이트 : user 와 web3 가 반환 
		- 이때, 1) window.ethereum 2) web3 라이브러리 통신 과정에서 시간이 걸리면, 반환되는 user 및 web3 는 비어있을 수 있음. 
	2.5 나머지 useEffect 실행 
	2.6 상태 및 props 변경에 따른 재렌더링 
	2.7 컴포넌트 언마운트
```

* 그러면, app.js 파일은 어떤 순서로 실행되나

```bash
1. import 읽기 

2. App.js 실행 
	1) 초기화 및 state 설정 
		- 커스텀 hook : useWeb3() 실행 -> "import useWeb3 from './hooks/web3.hook'" 경로를 따라서, web3.hook 으로 진입 -> import 읽고 -> useWeb3 함수로 진입 -> 위의 실행순서를 따름 
		- 이때, '비동기 처리' 에 따라서, user 와 web3 가 반환되는데,  '1) window.ethereum 2) web3 라이브러리 통신 시간' 이  'app.js 로 다시 돌아온 순간'  에 맞춰서 끝나면, 값이 들어있는거고, 'return 될 때 까지 안 끝나면, 값이 없는것임'
		- 만약, '비동기 처리' 가 끝나면, then 에 의해 return 문이 채워지게 되고 -> app.js 에서는 custom hook 의 상태가 변화 했으므로 -> 상태변수를 채워넣게 됨 (그리고 재렌더링 까지 가게 됨.)

	  - useState 실행

	2) 컴포넌트 렌더링 : 함수 본문 실행 -> jsx 반환 
		a) getCount() 실행 

		b) increment() 실행 
			- const fromUserAccount = user.account; 로 user 를 호출 
				- 지금 이 순간 까지, '⭐⭐⭐ hook.js 의 비동기 처리가 안 끝나 있으면⭐⭐⭐' , user 는 비어있는 것임. 


3. useEffect 실행 : []
4. DOM 업데이트 
5. 나머지 useEffect 실행 
	- web3 deps 를 갖고 있는 useEffect 실행 
6. 상태 및 props 재렌더링 
7. 컴포넌트 언마운트
```

* 빈 값이 들어오면 발생되는 문제

```js
예를 들어서, 'fromUserAccount' 가 없으면 -> web3 라이브러리로 트랜잭션을 보낼 때, 빈값을 보내게 되고 -> RPC 에러가 나게 된다. 
```

![](https://i.imgur.com/nIXK35p.png)

\


### 5️⃣ 비동기 처리 다시 이해

1. 일반론 | 비동기가 처리되는 과정

```bash
1. [@주 스레드 | V8 ] 주 스레드 인 javascript v8 엔진에서, 비동기 함수(setTimeout, fetch 등 ) 호출 👉 주 스레드 안에 있는 'call stack' 에 push 됨 👉 '비동기 함수에 대한 제어권' 이, WEB API 에게 '위임' 👉 비동기 함수는 call stack 을 빠져나옴('pop') 👉 V8 은 다른 일을 처리할 수 있음. 

2. [@ Web API] V8 에게 위임받은 비동기 함수 작업을 처리 👉 처리 완료되면, '콜백 함수' 가 'callback queue' 에 추가 

3. [@ callback queue] 
	- 이벤트 루프(event loop) 는 계속, callback queue 와 call stack 을 확인하고 있음. 
	- '콜백 함수' 가 callback queue 에 쌓인 경우, 이벤트 루프가 하는 일
		1) call stack 에 처리할 일이 없으면 (비어있으면) 
		2) queue 에 대기중인 작업을 call stack 에 넣는다. 
		3) '콜백 함수' 가 call stack 에 push 되고 -> v8 엔진에 의해 처리되고 -> 스택에서 pop 된다. 
	- 따라서, 
		1) 'callback queue' 에 들어온 '콜백 함수' 는 
		2) callback queue 에 대기하고 있다가 
		3) call stack 이 비어있게 되면, 'event loop' 가 'callback queue' 에서 대기하고 있는 '콜백함수' 를 'call stack' 으로 가져온다.  

```

\


2. 브라우저의 프로세스, 스레드, 구성 설명 이해하기
   * 그림 👉👉 \[\[2023-10-06 09.46.36\_비동기처리과정이해]] 여기에서 그림

```
[참고]
- https://velog.io/@code-bebop/JS-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EC%BD%9C%EB%B0%B1-%ED%81%90

- https://sculove.github.io/post/javascriptflow/
```

![](https://i.imgur.com/0Ndnfnj.png)

```bash
자바스크립트는 싱글스레드 하고 하는데, 구체적으로 그게 무슨 의미인지 알고 싶었음 

자바스크립트가 돌아가는 환경은 '브라우저' 또는 'node.js' 가 있음. 그런데, '자바 스크립트는 싱글 스레드 이다.' 라는 말은, '브라우저가 싱글 스레드 이다.' 라고 오해하기 쉬운데, 싱글 스레드의 주인은 브라우저가 아님. 그러면 뭐지? 

자바스크립트는 '자바 스크립트 엔진(ex v8)' 에 의해 실행된다. 그러면, 자바 스크립트를 실행시키는 '엔진' 이 '싱글 스레드' 일 것 이다.

'싱글 스레드' 이므로 '동기적(synchronous)' 으로 코드를 실행하는게 '기본' 이다. 

'동기(synchronous) 처리' 란 a 작업이 '끝날 때 까지 기다렸다가', 다음 b 작업을 진행하는 것 이다. 

그런데, 문제는 'I/O 작업(EX 네트워크 입출력, 파일 입출력)' 같이, '시간이 오래 걸리는 작업' 의 경우, A 작업이 완료 될 때 까지 그 이후의 작업들이 처리 되지 않는다. EX) 무한 대기........ -> 사용자 경험 최악 

따라서, '싱글 스레드' 를 사용하는 '자바 스크립트 엔진' 에서, '비동기 처리' 를 해줄 필요가 생긴다. 
EX) 로딩 되는 페이지가 있어도, 다른 버튼들 작동할 수 있는 것들

그러면 어떻게 비동기 처리가 되는가? 

1. [@ js 소스 코드] 비동기 함수가 호출된다. 

2. [@V8 스레드 내의 stack] 우선, js 에서 모든 함수가 호출되면(동기 함수건, 비동기 함수건) 👉 스레드가 각자 개별적으로 갖고 있는 저장 공간인 'stack' 에 'push' 쌓인다.

3. [@V8 스레드 내의 stack] '비동기 함수' 라면, '별도의 스레드(혹은 시스템)' 인 'WEB API' 에게 '비동기 함수의 제어권' 을 '위임' 한다.  👉 '위임 후 CALL STACK 에서 제거' 된다. 

4. [@WEB API] '위임 받은 비동기 함수' 를 '처리' 한다. 👉 처리 완료되면, 'callback queue' 에 '콜백 함수' 를 내보낸다. 

5. [@이벤트 루프] 
	- 1) 이벤트 루프는, call stack 과 call back queue 를 모니터링 한다. 
	- 2) call stack 이 비어있고(쌓인 작업이 다 끝났고⭐⭐⭐) + callback queue 에 콜백함수가 있으면 👉 콜백함수를 call stack 에 추가 한다. 
	- 3) v8 엔진은 '전달 받은 콜백 함수' 를 실행 한다. 
```

\


### 6️⃣ 비동기 처리된 데이터를 안전하게 받기

```bash
'의존' 성이 있는 데이터, 함수, 등을 만났을 때,  ⭐⭐⭐⭐⭐⭐ 
```

* 그러면, 비동기 처리가 다 됐겠지? 라고 운에 맞기지 않고, 올 때 까지, 안전하게 기다릴 수 있을까? 어떻게 해결?

1. 조건부 렌더링

```js
    // 유저의 지갑 정보
    if(user && user.account){
      const fromUserAccount = user.account;
      console.log("user.account" , user.account)
    }
```

2. loading 상태로 처리

```js
import React, { useState, useEffect } from 'react';

function DataLoader() {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // 가상의 API 호출 예시
    fetch('https://api.example.com/data')
      .then(response => response.json())
      .then(data => {
        setData(data);        // 데이터 설정
        setLoading(false);    // 로딩 상태 업데이트
      })
      .catch(error => {
        console.error("API 호출 중 에러 발생:", error);
        setLoading(false);    // 에러 발생시에도 로딩 상태 업데이트
      });
  }, []);

  if (loading) {
    return <div>Loading...</div>;  // 데이터를 불러오는 동안 표시될 UI
  }

  return (
    <div>
      <h1>Data:</h1>
      <ul>
        {data.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default DataLoader;

```

3. setWeb3 의 순서를 바꾼다고? ???????????????? ![](https://i.imgur.com/8TvTOro.png)
