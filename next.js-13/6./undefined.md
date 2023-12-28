# 라우팅 보충 스터디

## 2. 라우팅

#### @어드민 페이지

```bash
# main 페이지 (첫 화면)
NEXT_PUBLIC_API_URL + admin/main

# 매물 등록 페이지
NEXT_PUBLIC_API_URL + admin/create/real_estates/[id]

# 공시자료 등록 페이지
NEXT_PUBLIC_API_URL + admin/create/notices/[category]/[id]
	# DB 저장할 때, 게시글 유형이 있으면 좋을거 같은데 

```

* 중요 개념 요약

```
- 입력하게 되는 것이 
http://localhost:3000/read/1
http://localhost:3000/read/2 등등 이렇게 뭐가 될지 모르면 -> parameter 로 만들어서 관리 
```

#### 방법론 요약

```
아마도, a 링크 방식 또는 리디렉션 방식 중 하나를 사용하게 될 것. 
```

**next.js 공식 홈페이지 : replace 사용 | 클라이언트 에서 되는 것 같음**

* 출처 : https://nextjs.org/docs/app/building-your-application/data-fetching/forms-and-mutations
* useRouter 관련해서 사용할 수 있는 것 들 : https://nextjs.org/docs/app/api-reference/functions/use-router

```js
    // fetching -> DB 에서 받아온 fresh data 가공
    fetch(process.env.NEXT_PUBLIC_API_URL + `posts`, options)
      .then((res) => res.json()) // 방금 저장한 데이터를 return 받아서 -> json 으로 변환
      .then((result) => {
        console.log("처리한 데이터가 잘 들어왔는지 확인", result);

        const latestID = result.id;

        // router 가 fresh data 를 fetching 받기
        router.refresh();

        // 방금 쓴 글을 확인하기 위한 리디렉션
        // 만약, '게시물 등록' 이 완료되면 -> 'admin/main 중 청약 관리' 에서 볼 수 있게 한다면 -> 바로, admin/main 으로 돌려도 될 듯
        router.replace(`http://localhost:3000/admin/main`);
        // redirect('admin/main') // Navigate to new route
      });
  };
```

**next.js 공식 홈페이지 : redirect 사용 | 서버 에서 되는 것 같음**

* 출처 : https://nextjs.org/docs/app/building-your-application/data-fetching/forms-and-mutations
* useRouter 관련해서 사용할 수 있는 것 들 : https://nextjs.org/docs/app/api-reference/functions/use-router

```ts
'use server'
 
import { redirect } from 'next/navigation'
import { revalidateTag } from 'next/cache'
 
export default async function submit() {
  const id = await addPost()
  revalidateTag('posts') // Update cached posts
  redirect(`/post/${id}`) // Navigate to new route
}
```

**'a 링크' 방식 -> 'Link' 태그 사용**

* '사용자가 클릭' 했을 때 -> 해당 주소로 이동
* next.js 에서는 a 태그가 아니라, Link 태그를 사용

```js
// 예시 
  <li>
	<Link href="/create"> Create </Link>
  </li>
```

**리디렉션 방식 | useRouter 사용**

* 사용자가 클릭하지 않았는데, 페이지가 이동
* 개발자가 의도한 대로 페이지를 전환 시키고자 할 때
* 예를 들어,
  * 사용자가 글을 작성하고 -> DB 에서 해당 정보를 가져와서 -> 방금 작성한 글을 보여주고자 할 때
* 이 부분 흐름도 : https://www.figma.com/file/1MKhuVFyKgkxbo7SzZ4cNy/next.js-%EA%B3%B5%EB%B6%80?type=whiteboard\&node-id=9-2103\&t=hr4e0bZDfnz7hZZ8-4

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

                fetch(process.env.NEXT_PUBLIC_API_URL + `topics` , options)
                .then(res => res.json())
                .then(result => {
                    console.log(result)
                    const lastId = result.id
                    console.log(lastId)

                    // router 가 fresh data 를 fetching 받기 ⭐⭐ 
                    router.refresh();

                    // useRouter 이용해서 리디렉션 시키기  ⭐⭐⭐⭐⭐ 여기에서 리디렉션
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

#### \[TASK]

* [ ] '매물 등록' 누르면 -> 등록 페이지로 가게 하기
  * [ ] 이어서 layout READ 하고 create 할 수 있게 하기

#### 포인트

1. `a` 대신 `Link` 를 사용 | 그래야, 페이지 전환에서 실제로 변하는 데이터는 page.js 뿐 인데, layout.js 까지 렌더링 하는 비효율을 막을 수 있음.

* 참고 : \[\[231103\_생활코딩\_single page application]]
