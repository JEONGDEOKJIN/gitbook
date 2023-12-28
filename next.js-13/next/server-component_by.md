# server component\_by 공식문서 ⭐⭐⭐

* 출처 : https://nextjs.org/docs/app/building-your-application/rendering/server-components

#### Server Components

```BASH

Server Component 는 
	'UI' 를 그려줌 
	'streaming and partial rendering' 를 가능하게 함 


3가지 종류의 렌더링 방식이 있음. 

```

#### [Benefits of Server Rendering](https://nextjs.org/docs/app/building-your-application/rendering/server-components#benefits-of-server-rendering)

* 궁금한 점

```
서버에서 렌더링을 할 때 좋은 점
	- 그러면, 반대로, 클라이언트에서 렌더링을? ❓❓❓❓❓❓❓❓❓❓❓ 

```

```bash
1. data fetching 
	- DB 에 물리적으로 가까움 (closure to youre data source) -> 데이터 fetching 할 때, 드는 시간이 줄어든다. (reduce time)

2. security 
	- tokens and API keys 저장 
	- 그러면, 서버 컴포넌트에 tokens 을 저장해야 하나 ❓❓❓❓❓❓❓❓❓❓❓❓❓❓❓❓❓❓❓ 

3. caching 
	- 한번 받은 건, 저장해서, reuse 가능 

4. bundle sizes 
	- 서버 컴포넌트가 pre-render 해서, 클라이언트에게는 HTML 만 전달한다. 
	- SO, client 가 처리해야 하는 JavaScript bundle 의 줄어들고 -> 로딩 시간이 단축된다. 
	- as the client does not have to download, parse and execute any JavaScript for Server Components.

5. Initial Page Load and 
	- 즉각 다운 된다는 말 

6. SEO 에 좋음 


7. streaming 
	- 필요한 정보를 불러온다. 

	-  Server Components allow you to split the rendering work into chunks and stream them to the client as they become ready.

	- 렌더링 작업을 -> chunk 로 나눈다. ❓❓❓❓❓ 

```

#### [Using Server Components in Next.js](https://nextjs.org/docs/app/building-your-application/rendering/server-components#using-server-components-in-nextjs)
