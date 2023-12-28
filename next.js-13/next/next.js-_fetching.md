# next.js 공식문서\_fetching

## Data Fetching, Caching, and Revalidating

* 요약

```bash
- 'Data Fetching, Caching, and Revalidating' 페이지를 통해 
	1) fetch 
	2) cache
	3) revalidate 
	에 대해서 배울 수 있음. 
	(This page goes through how you can fetch, cache, and revalidate data in React and Next.js.)

- fetch, cache, revalidate 에 대해서는 4가지 방법이 있음 
	1. on the server, with 'fetch'
	2. on the server, with third-party libraries 
	3. on the client, via a 'Route Handler' 
	4. on the client, with third-party libraries

- 궁금한 점 
	- on the server 라는 말은 '서버 컴포넌트' 에서 진행된다는 말 인가? 
	- 그런데, 그러면, client 에서도, fetching, cache, revalidate 를 하게 되는 경우가 있으려나❓❓❓❓❓❓ 
	- 이 경우 까지 배워야 하려나? ❓❓❓❓❓❓❓❓ 

```

### 1st 방법 : \[Fetching Data on the Server with `fetch`]

* 출처 : (https://nextjs.org/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating#fetching-data-on-the-server-with-fetch)
* 요약

```bash
next.js 는 '원래 있던 Fetch API' 를 '발전' 시킴

어떻게 caching, revalidating 문제를 해결했을까❓❓❓ ⭐⭐⭐⭐⭐ 


```

#### fetch 예시 코드

```
async function getData() {
  const res = await fetch('https://api.example.com/...')
  // The return value is *not* serialized
  // You can return Date, Map, Set, etc.
 
  if (!res.ok) {
    // This will activate the closest `error.js` Error Boundary
    throw new Error('Failed to fetch data')
  }
 
  return res.json()
}
 
export default async function Page() {
  const data = await getData()
 
  return <main></main>
}
```
