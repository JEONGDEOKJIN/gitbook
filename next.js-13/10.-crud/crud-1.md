# \[CRUD] 글 생성

#### 문제 상황 create 을 누르면 -> 서버로 데이터를 전송해야 함 -> 페이지가 리로드 된다. #📛약함

![](https://i.imgur.com/7pbPgvw.png)

* 해결 : preventDefault

#### \[task] 글 작성한 걸 -> 서버에 fetching 하게 하기

```js
"use client"

export default function Create() {
    return (
        <>
            {/* submit 은 사용자 참여가 필요. | 이게 작동하려면, 클라이언트 쪽 자원이 필요 */}
            <form onSubmit={(e) => {
                e.preventDefault();
                const title = e.target.title.value
                    // target = form 태그
                const body = e.target.body.value; 

                // 보낼 때, 데이터를 여기에 넣기
                const options = {
                    method : 'POST', 
                    headers : {
                        'Content-Type' : 'application/json'
                    },
                    body : JSON.stringify({title, body})    // json 으로 변환해서 전송
                }

                fetch(`http://localhost:9999/topics` , options)
                .then(res => res.json())
                .then(result => {
                    console.log(result)
                    
                    const lastId = result.id
                })

            }}>  
                <p>
                    <input type="text" name="title" placeholder="title"  />  
                </p>
                <p>
                    <input type="text" name="body" placeholder="body"  />  
                    {/* <textarea name="body" placeholder="body" />  */}
                </p>
                <p>
                    {/* 아, 헐, 이게 보내는 거 였어? 😥😥😥😥😥😥 */}
                    <input type="submit" value="create" />
                </p>

            </form>
        </>
    )
}
```

#### \[task] fetching 되면 -> 사용자가, 작성한 글로 'redirection' 되게 하기

* 핵심

```bash
useRouter 사용 

[핵심 함수] 
	- useRouter hook 

	- input 
		: 'domain:port 이후의 주소' 
			- 왜냐면, 라우팅 한다는 것은, 파일 시스템 기반의 라우팅의 경우, 현재 파일 네트워크 안에서 찾아가는 것. 여기에 'input' 하는 정보는 '어디로 가게 할 것 인가!' 에 대한 정보   
			- 그러면, 도메인 + port 번호를 기재하지 않는 이유는, 당연함. 어디론가 이동하게 하려면, 그 이후의 것을 변경해야 
		: ex) 'http://localhost:3000/read/8' 에서, 'read/{parameter}'

	- output
		: '기입한 주소' 로 페이지가 리디렉션됨 

	- 특징 
		- 브라우저의 URL을 변경하지만, 페이지를 새로 고침하지 않음. -> 이 말은, layout 정보는 그대로 유지된다는 말 일까? 변경된 정보만 가져온다는 말 일까? ❓❓❓❓❓❓ (#❓❓❓)


```

* 전체 코드

```js
"use client"

import { useRouter } from "next/navigation";

export default function Create() {

    const router = useRouter();

    return (
        <>
            {/* submit 은 사용자 참여가 필요. | 이게 작동하려면, 클라이언트 쪽 자원이 필요 */}
            <form onSubmit={(e) => {
                e.preventDefault();
                const title = e.target.title.value
                    // target = form 태그
                const body = e.target.body.value; 

                // 보낼 때, 데이터를 여기에 넣기
                const options = {
                    method : 'POST', 
                    headers : {
                        'Content-Type' : 'application/json'
                    },
                    body : JSON.stringify({title, body})    // json 으로 변환해서 전송
                }

                fetch(`http://localhost:9999/topics` , options)
                .then(res => res.json())
                .then(result => {
                    console.log(result)
                    const lastId = result.id
                    console.log(lastId)
                    
                    // 리디렉션 시키기 
                    router.push(`/read/${lastId}`)
                })

            }}>  
                <p>
                    <input type="text" name="title" placeholder="title"  />  
                </p>
                <p>
                    <input type="text" name="body" placeholder="body"  />  
                    {/* <textarea name="body" placeholder="body" />  */}
                </p>
                <p>
                    {/* 아, 헐, 이게 보내는 거 였어? 😥😥😥😥😥😥 */}
                    <input type="submit" value="create" />
                </p>

            </form>
        </>
    )
}
```



#### \[문제상황] 글을 작성하고, 리디렉션 까지 되었을 때, 'fresh data' 를 사용해서, '현재 리스트가 자동으로 업데이트' 되게 하기

* 이어지는 문제 상황 참고 : \[\[231103\_생활코딩\_글생성#^64a28a]]
* useRouter 를 통한 리디렉션은 cache 를 사용함 -> 따라서, 서버에서 fresh data 를 가져오지 않음

```bash
- 해당 페이지에서 처음 가져오면 -> cache 를 사용하지 않음 -> so, cache 는 miss
- '동일한 페이지에서 새로고침(리디렉션)' 하면 -> cache 를 사용함, 통신을 하지 않음 -> so, cache는 HIT 임 
- useRouter 를 사용하면 -> '동일한 페이지에서 새로고침(리디렉션)' 하게 됨 -> so, cache 를 사용함
```

* 이를 해결하려면, revalidating 을 해야 함

```js
[cache 자체를 저장 안 하는 방법] 
	export default async function RootLayout({ children }) {
	  // 서버 경로를 넣어서, 서버에서 데이터를 가져오기(fetching)
	  const res = await fetch("http://localhost:9999/topics", {
	    // next: { revalidate: 10 },  // 10초 간만 cache 유지, 10초 이후 fresh data 가져오고 다시 cache 저장
	    // next: { revalidate: 0 },   // 캐쉬를 쓰지 않겠다.
	    cache: "no-store", // 캐쉬를 쓰지 않겠다.
	  });


[on demand] ✅✅✅✅✅✅ 추가 기록 필요 


```

**우선, 여기서는 cache 를 만들지 않고, 리디렉션 까지 되었을 때, 'fresh data' 를 사용 하기**

1. data fetching 받아올 때, cache 없음으로 설정

```js
export default async function RootLayout({ children }) {
  // 서버 경로를 넣어서, 서버에서 데이터를 가져오기(fetching)
  const res = await fetch("http://localhost:9999/topics", {
    // next: { revalidate: 10 },  // 10초 간만 cache 유지, 10초 이후 fresh data 가져오고 다시 cache 저장
    // next: { revalidate: 0 },   // 캐쉬를 쓰지 않겠다.
    cache: "no-store", // 캐쉬를 쓰지 않겠다.
  });
```

2. 리디렉션 시킬 때, 'router.refresh();' 하여, fresh data 받아오고 -> useRouter 훅 사용하기 -> 그러면, useRouter 는 캐시를 쓰지 않고, router.refresh(); 에서 받은 데이터를 사용함

```js
fetch(`http://localhost:9999/topics` , options)
.then(res => res.json())
.then(result => {
	console.log(result)
	const lastId = result.id
	console.log(lastId)

	// router 가 fresh data 를 fetching 받기 ⭐⭐ 
	router.refresh();

	// useRouter 이용해서 리디렉션 시키기 
	router.push(`/read/${lastId}`)
})
```



