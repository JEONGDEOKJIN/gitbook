# 6. 라우팅

## 6. 라우팅

* 요약

```bash
1. 주소를 치면 -> 해당 segment 의 page.js 를 타겟해서 내려간다. 
2. page.js 가 return 하는 HTML 은 layout.js 의 children 에 안기게 된다. 
3. 'layout 들은 중첩' 해서 '상위 폴더' 로 이동하면서, 'RootLayout.js' 까지 이동하게 된다.
```

![](https://i.imgur.com/UFt9WvW.png)

### 공식 문서 공부

* 라우팅 이란

```bash
라우팅은 '어떤 네트워크' 안에서 통신 '데이터를 보낼 때' 최적의 '경로를 선택'하는 과정 
최적의 경로는 주어진 데이터를 가장 짧은 거리로 또는 가장 적은 시간 안에 전송할 수 있는 경로다. 
라우팅은 전화 통신망, 전자 정보 통신망, 그리고 교통망 등 여러 종류의 네트워크에서 사용된다. 
(출처 : 위키백과)


'주소(경로)' 에 따라서, '어떤 콘텐츠' 를 '어떤 방식' 으로 보여줄 것 인가. 
(출처 : 생활코딩 강의)


A file-system based router built on top of Server Components that supports layouts, nested routing, loading states, error handling, and more. (출처 : nex.js 공식문서)
즉, 
	server component 는 layouts, nested rounting, loading states 등을 지원함 
	이러한 server component 위에, 'file-system based' 의 router 가 있음. 

```

* 지금 내가 이해하고 있는 것 (231104)

```bash
'특정 주소' 를 입력하면, 
해당 주소를 바탕으로 '디렉토리 경로를 따라 간다.'
그 경로에 있는 page.js 파일을 찾는다. 
page.js 을 return 한 HTML 을 layout.js 의 children 혹은 props.children 안에 넣으려고 시도한다. 
만약, 동일 레벨에서 layout.js 가 없으면, 상위 레벨에 있는지 여부를 보고, 있으면, 거기에 넣으려고 시도 한다. 

```

* [App Router vs Pages Router](https://nextjs.org/docs#app-router-vs-pages-router)

```
[App Router] 
- Server Components and Streaming 를 사용할 수 있음. 
- 그러면, 나는 'Server Components and Streaming' 을 사용하는가 ⭐⭐⭐⭐⭐⭐ 
```

###

### 생활코딩 강의&#x20;

### '주소' 의 구성 요소

```bash
'주소' 는 
	1) http 프로토콜 
	2) domain
	3) path = segment + segment ... 
	로 이루어져 있음. 
```

* 그러면, '이 주소를 받아든, 컴퓨터는 어떤 입장?'

```bash
'이 주소에 가져, 해당 데이터를 가져오라는 의미구나?' 라고 생각하게 되는 걸까. 
```

![](https://i.imgur.com/GD6h9DF.png)

### \[방법론] next.js 에서 어떻게 '동적 라우팅' 시키지?

#### 구현해야 하는 것 (문제 상황)

```
http://localhost:3000/read/1 을 입력하면, 어떤 해당 parameter 가 반영된 글이 나와야 
```

\


#### 입력하게 될 url 주소

```bash
http://localhost:3000/read/1
http://localhost:3000/read/2
http://localhost:3000/read/3
http://localhost:3000/read/4
http://localhost:3000/read/5

등등 
뭐가 될지는 모름 
```

\


#### 디렉토리 구조

![](https://i.imgur.com/43TWG1R.png)

#### 사용된 매개변수 뽑기 : props.params.id

```js
export default function Read(props) {
    return (
        <>
            <h2> Read 임!!!  </h2>
            
            <p> 이곳은 몇 편의 글이 생겨날지 모르는 곳. <br></br>
                그래서, 미리 만들어 둘 수 없음. <br></br>
                그래서, 만드는 순간! id 값이 정해지는 '동적 라우팅' 을 하려는 곳 <br></br>
                {/* 사용된 매개변수 뽑기 */}
                이 글의 parameter : {props.params.id}
            </p>
        </>
    )
}

```

#### \[해석] 주소창에 'http://localhost:3000' 입력하면 벌어지는 일

```BASH
1. 주소창에 '주소' 를 입력한다. 
2. next.js 가, app 폴더 안에서, pape.js 가 있는지를 찾는다. 
3. page.js 가 있으면, page.js 의 return 값을 layout.js 의 children 에 넣는다. 
4. 최종적으로, 'RootLayout 함수에서 return 한 HTML' 을 응답 받는다.

- 느낀점 
	- 결국, 어떤 HTML 을 응답 받는가! ⭐⭐⭐ 
	- layout 과 그 안의 content 가 어떻게 하나의 HTML 로 합쳐지는가! 
```

![](https://i.imgur.com/t1837fa.png)

\


#### \[해석] 주소창에 'http://localhost:3000/create' 를 입력하면 벌어지는 일

* CREATE 폴더 안에 layout.js 가 없는 경우

```bash
localhost:3000/create 로 주소를 입력하면, 
app 폴더 밑에 create 폴더를 찾는다. 
해당 폴더가 page.js 를 갖고 있는지를 찾는다. 
만약 갖고 있다면, page.js 의 return 값을 갖고 layout.js 과 결합한다. 

1) 가장 가까운 폴더인 create 에게 layout.js 이 있으면, 해당 파일과 결합하고 
2) 가장 가까운 폴더인 create 에게 없으면, app 폴더 안에 있는, '최초의 layout.js' 과 결합한다. 

그리하여, page.js 는 layout.js 의 {children} 자리에 들어오고 난 다음, 
RootLayout 이 HTML 을 RETURN 한 결과를 응답 받는다.
```

![](https://i.imgur.com/yrttgPu.png)

* CREATE 폴더 안에 layout.js 가 있는 경우 ⭐⭐⭐

````bash
1. localhost:3000/create 로 주소를 입력하면, 

2. create 폴더에서 page.js 가져오기
- app 폴더 밑에 create 폴더를 찾는다. 
- 해당 폴더가 page.js 를 갖고 있는지를 찾는다. 
- 만약 갖고 있다면, page.js 의 return 값을 갖고 layout.js 과 결합하기 위해 준비한다. 

3. 부모 폴더 (create 폴더) 에 layout.js 가 있다면, 'props.children' 의 자리에 'page.js 의 return 값' 이 들어가게 된다. 그리고 'Layout 함수의 return 값을 반환한다.'
export default function Layout (props) {
	return (
		<form>
			<p> create 폴더 안에, layout.js 가 있으면, layout.js 안의 children 에 꽂힘! </p>
			{props.children} 
		</form>
	)
}

4. 그 다음 조부모 폴더(app) 안에 layout.js 에서 RootLayout 함수 안에 있는 '{children}' 자리에, 'Layout 함수의 return 값' 을 넣는다. -> 그렇게 해서, RootLayout 의 return 값을 HTML 로 응답해준다. 

- 시사점 
	- 결국, 'RootLayout 의 return 값을 HTML 로 응답해준다.' 라는 것 ⭐⭐⭐ 
	- 결국, 이렇게 조합해서 HTML 로 만든다는 것 

	```




### 주소창에 'http://localhost:3000/read/1' 를 입력하면 벌어지는 일 

``` bash
[전제]
- 게시글 1, 게시글 2, 3, 등등 많은 글들을 만들어야 함. 
- 문제는 '미리 만들어 둘 수 없음!' ⭐⭐⭐
- 되어야 하는 건, '글이 생성되는 순간, read/1 ' 이렇게 라우팅 까지 들어가야 함 ⭐⭐⭐⭐⭐ 
- 이렇게 ⭐동적으로(다이나믹하게) 라우팅 ⭐ 이 되어야 함 

[이 기술이 해결하는 문제는?]
- 게시글을 100 개 이상 만들어야 함 
- 그런데, '모든 것을 미리 만들 수 없음.'
- 따라서, '게시글을 만드는 순간, http://localhost:3000/read/1 여기에 parameter 를 할당해서, 주소를 확정해서 -> 라우팅이 되게 할 수 는 없을가?'

[next.js 가 해결하는 방식]
- next.js 는 최종적으로, 'src > app > layout.js 의 RootLayout 함수의 return 값을 HTML 로 반환한다.'
- 최종반환하기 전에, 필요한 재료들은, childeren 을 통해 가져온다. 
- 그러면, childeren 은 어디에서 오는가?  다양한 곳들에서 pages.js 가 꽂힘

[브라우저가 요청하면, 서버에서 어떤 일이 벌어지는가]
1. 사용자가 3000/read/1 로 요청을 보냄 
2. 서버에서 app > read > [id] 폴더 안에 있는 page.js 내용을 확인 
- [id] 는 어떠한 값이건 들어올 수 있는 매개변수 자리 
- page.js 는 '내용물' 이라는 걸 전제로 함 

3. page.js 의 return 값을 layout.js 의 children 으로 건네주려 함 
3.1 read 폴더 안에 없으므로, 상위 폴더로 이동 
3.2 그렇게 조립에 조립을 거쳐서, RootLayout 의 return 되는 HTML 이 나옴

````

![](https://i.imgur.com/UFt9WvW.png)

![](https://i.imgur.com/hQYnGRf.png) ![](https://i.imgur.com/YuY8uXm.png)

![](https://i.imgur.com/VldypCU.png)
