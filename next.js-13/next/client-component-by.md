# client component by 공식문서 ⭐⭐⭐

* 출처 : https://nextjs.org/docs/app/building-your-application/rendering/client-components

#### Client Components 란?

```bash

- 'interactive UI' 를 가능하게 함 
	- 'interactive' 가 된다는 것은, '요청했을 때(request time⭐⭐)',  '렌더링 될 수 있다.'  (can be rendered on the client at request time.) 
		- ex) 클릭 하거나, ui 업데이트 되는 것들 
	
	- 그러려면, 어떤 요청들이 있나? 
		- 사용자가 클릭하고, 하는 것들? 

- 'opt-in'
	- client component 를 사용하려면, '명시적으로 선언' 해줘야 한다. 
	- 즉, 'use client' 를 써줘야 함 

```

#### [Benefits of Client Rendering](https://nextjs.org/docs/app/building-your-application/rendering/client-components#benefits-of-client-rendering)

* 요약

```bash
1. Interactivity
	- client rendering 을 사용하면 -> 'state, effects, and event listeners' 를 사용할 수 있음. 
	- 'state, effects, and event listeners' 를 사용하면 ->  '사용자 상호작용' 또는 '업데이트 해야 하는 UI' 에 대해서, '즉각적인 반응(immediate feedback)' 을 해줄 수 있음. (they can provide immediate feedback to the user and update the UI.)


2. Browser APIs
	- 브라우저가 제공하는 api 를 사용할 수 있음. 

```

* 추가 생각해 봐야 하는 점

```bash
1. immediate feedback 을 해야 하는 task 가, 정말 그것인지를 알고 하는가. 
2. 내가 쓰는 useState, effect 가 'state, effects' 임을 아는가. 
```

#### [Using Client Components in Next.js](https://nextjs.org/docs/app/building-your-application/rendering/client-components#using-client-components-in-nextjs)

* 요약

```bash
1. 'use client' 을 사용 

2. 'use client' 의 아래 부분은 'client bundle' 로 간주함 

3. 'use client' 를 사용해야, 인식하는 이유 
	- This is because, by default, the components are rendered on the server where these APIs are not available.
	- 즉, '컴포넌트' 는 기본적으로 '서버 컴포넌트' 임. 
	- '서버 컴포넌트' 에서는 'these APIs' 에 접근할 수 있는 권한이 없음 
	- 따라서, '서버 컴포넌트' 로 선언하면, 'these APIs' 를 사용해야 하는 상황에서는 오류가 발생하게 됨. 
	- 그러니까, use client 로 선언해서 -> 1) React 에게 render 할 수 있게 하고 2) APIs 를 사용할 수 있게(available) 해야 함

4. 'client component' 를 통해 접근할 수 있는 'Browser APIs' 와 'react 훅 및 onclick 같은 이벤트 리스너의 관계'
	- 'onclick 같은 이벤트 리스너' 는 브라우저가 제공하는 'Browser API' 의 일부 임 -> 따라서, 브라우저(클라이언트) 에게 도달해야 실행되는 것 임. -> 따라서, 리액트에게 렌더링을 맡겨서, 클라이언트 렌더링을 하면 됨 
	- 'react 훅' 은 'Browser API' 와 직접 관련은 없음. 다만, '브라우저에서 제공하는!' 'js 환경 내에서 동작함'
		- 즉, 
			- 브라우저는 DOM, window, document 등의 객체를 제공함. -> react 훅 들중 일부는, 해당 객체에 의존해서 작동함 -> 따라서, react 훅이 포함된 컴포넌트는 '클라이언트 렌더링' 이 되어야 제대로 동작함 ⭐⭐⭐⭐⭐ 
			- EX) useEffect 는 'Dom 업데이트 이후 실행되는 사이드 이펙트를 처리' 하므로, 'DOM 요소 데이터에 접근' 할 수 있는 '브라우저 환경' 이어야 제대로 작동한다. 
			- EX) useRef 는 DOM 요소를 직접 조작 하는데 사용된다. -> 따라서,  '브라우저 환경' 이어야 제대로 동작. 


5. 즉, 'use client' 를 사용하면 벌어지는일? 
	1) React 가, '해당 컴포넌트' 를, render 할 수 있게 한다. 
	2) browse API 같은 'these APIs' 를 사용할 수 있게 한다. -> 따라서, browse API 에 포함되는 onclick 이벤트 리스너가 포함된 컴포넌트가 정상작동 한다. 
	3) 또한, brower 가 제공하는, window, DOM, document 객체에 접근할 수 있게 된다. -> 따라서, DOM 처리 이후를 업데이트 하는 useEffect 등 다양한 훅들을 포함한 컴포넌트가 정상작동한다. 


```

* use client 선언 예시 ![900](https://i.imgur.com/W8ObcWw.png)

#### [How are Client Components Rendered?](https://nextjs.org/docs/app/building-your-application/rendering/client-components#how-are-client-components-rendered)

* 요약

```bash

1. 'full page load' 인지 'subsequent navigation' 에 따라, 다르게 load 된다. | ⭐⭐⭐⭐⭐ 몰랐었음 
	CF. full page load : 처음 방문하거나, 새로고침한 경우 
	CF. subsequent navigation : 어느 정도 받아놓은 상태에서, 후속 탐색을 하는 경우

```

**\[Full page load] | #📛📛📛 잘 모르겠음**

* 출처 : https://chat.openai.com/share/15ad07cc-73c9-4064-9a50-2ccca8e4202d
* 출처 : (https://nextjs.org/docs/app/building-your-application/rendering/client-components#full-page-load)

```bash
[브라우저에서 요청하기 전 서버에서 하는 일]
1. '정적 html 파일'을 만들어 놓음 (브라우저 요청이 오면, 바로 보여줄 준비가 되어 있음)
2. '서버 컴포넌트의 상태와 로직' , '클라이언트가 사용할 정보' 가 담긴 'RSC Payload 파일' 을 미리 준비해둠

[브라우저가 서버에게 '해당 주소' 를 보여달라고 요청 하면 벌어지는 일]
1. 서버는 미리 준비해둔 '정적 html 파일' 과 'RSC Payload 파일' 을 건네준다. 
2. 브라우저는 
	1) '정적 html 파일' 로 정적 ui 를 렌더링을 하고 
	2) 'RSC Payload 파일' 기반으로 'DOM 업데이트'를 하고 
	3) 업데이트 된 DOM 을 기반으로 'event listener 와 dom 을 연결하는, hydration 작업'을 수행한다. 
	4) 그렇게 해서 '요청 했을 때(request time)' 에 반응하는, 동적인 ui (interactive ui) 가 렌더링 된다. ex) '버튼 클릭, ui 즉시 업데이트'  
```

![](https://i.imgur.com/WwgTX2T.png)

* 관련 개념
  * \[\[231103\_DOM]]
  * \[\[231103\_SSG 정적렌더링]]
  * \[\[231103\_hydration\_event listener 가 dom 에 붙게 하는 과정 ⭐⭐⭐⭐⭐⭐]]

**\[Subsequent Navigations]**

* 출처 : (https://nextjs.org/docs/app/building-your-application/rendering/client-components#subsequent-navigations)
* 요약

```bash
Subsequent Navigations 의 경우, server-rendered HTML 없이 클라이언트에서 (rendered entirely on the client) 렌더 된다. 

즉, 한번 js bundle 을 받으면, 리액트는 '이미 받아놓은 RSC Payload' 를 사용한다. 
(Once the bundle is ready, React will use the RSC Payload to reconcile the Client and Server Component trees, and update the DOM.)
```

* 궁금증

```
이로 인해, 재검증 방식이 필요하게 되는 것 같은데 😥😥😥😥😥😥 
```

#### \[Going back to the Server Environment]

* 출처 : (https://nextjs.org/docs/app/building-your-application/rendering/client-components#going-back-to-the-server-environment)
* \[문제상황] 클라이언트 컴포넌트에서 서버 컴포넌트로 변환하고 싶은 경우 ^2d03d4

```bash
- you may want to 'reduce the client bundle size', 'fetch data on the server', or 'use an API that is only available on the server'.
```

* \[솔루션]
  * interleaving Client and Server Components and [Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/forms-and-mutations).
  * 즉, 'Client and Server Components and Server Actions' 을 '⭐interleaving⭐' 하기
  * 더 정확한 것은 'Server and Client Composition Patterns'(https://nextjs.org/docs/app/building-your-application/rendering/composition-patterns) 참고
* 관련 개념
  * \[\[231103\_Server and Client Composition Patterns]]

#### 알게 된 점

```bash
- 'full page load' 인지 'subsequent navigation' 에 따라, 다르게 load 된다. | ⭐⭐⭐⭐⭐ 몰랐었음 
	- 여기에서 연속 탐색이라는 키워드 


```
