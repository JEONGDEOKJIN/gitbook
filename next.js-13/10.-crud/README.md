# 10. CRUD 기본 흐름

## 4. CRUD 기본 흐름

### 1) CREATE

#### 'main 페이지' 에서' '게시글 등록' 눌렀을 때의 흐름

* 참고한 로직 : 생활코딩
* 샘플 코드 : create.sample.page.js
* 피그마 흐름도 참고 : https://www.figma.com/file/1MKhuVFyKgkxbo7SzZ4cNy/next.js-%EA%B3%B5%EB%B6%80?type=whiteboard\&node-id=9-2103\&t=hr4e0bZDfnz7hZZ8-4

```js
"use client";

import { useRouter } from "next/navigation";

export default function Create() {
  const router = useRouter();

  return (
    <>
      {/* submit 은 사용자 참여가 필요. | 이게 작동하려면, 클라이언트 쪽 자원이 필요 */}
      <form
        onSubmit={(e) => {
          e.preventDefault();
          const title = e.target.title.value;
          // target = form 태그
          const body = e.target.body.value;

          // 보낼 때, 데이터를 여기에 넣기
          const options = {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({ title, body }), // json 으로 변환해서 전송
          };

          fetch(process.env.NEXT_PUBLIC_API_URL + `topics`, options)
            .then((res) => res.json())
            .then((result) => {
              console.log(result);
              const lastId = result.id;
              console.log(lastId);

              // router 가 fresh data 를 fetching 받기 ⭐⭐
              router.refresh();

              // useRouter 이용해서 리디렉션 시키기 | 방금 쓴 글을 확인하기 위한 리디렉션
              router.push(`/read/${lastId}`);
            });
        }}
      >
        <p>
          <input type="text" name="title" placeholder="title" />
        </p>
        <p>
          <input type="text" name="body" placeholder="body" />
          {/* <textarea name="body" placeholder="body" />  */}
        </p>
        <p>
          {/* 아, 헐, 이게 보내는 거 였어? 😥😥😥😥😥😥 */}
          <input type="submit" value="create" />
        </p>
      </form>
    </>
  );
}

```

### 2) READ

* 피그마 흐름도 및 샘플 코드 : https://www.figma.com/file/1MKhuVFyKgkxbo7SzZ4cNy/next.js-%EA%B3%B5%EB%B6%80?type=whiteboard\&node-id=21-2291\&t=hr4e0bZDfnz7hZZ8-4
* \[공식 문서] fetch & async await 활용해서 getData | https://nextjs.org/docs/app/building-your-application/data-fetching/fetching-caching-and-revalidating#fetching-data-on-the-server-with-fetch
* 포인트

```bash
- client component 버전이 있고, server component 버전이 있음 

```

* 샘플 코드

```js
export default async function Read(props) {
  // 사용자와 상호작용이 없으므로, 서버 컴포넌트로 만들면 됨.
  const resp = await fetch(`http:localhost:9999/topics/${props.params.id}`, {
    cache: "no-store",
  });

  const topic = await resp.json();

  return (
    <>
      <h2> Read 임!!! </h2>

      <p> topic의 title 가져오기 : {topic.title} </p>
      <p> topic의 body 가져오기 : {topic.body} </p>

      <p>
        이곳은 몇 편의 글이 생겨날지 모르는 곳. <br></br>
        그래서, 미리 만들어 둘 수 없음. <br></br>
        그래서, 만드는 순간! id 값이 정해지는 '동적 라우팅' 을 하려는 곳{" "}
        <br></br>
        {/* 사용된 매개변수 뽑기 */}이 글의 parameter : {props.params.id}
      </p>
    </>
  );
}

```

### 3) Update

```js
"use client";

import { useParams, useRouter } from "next/navigation";
import { useEffect, useState } from "react";

export default function Update() {
  const router = useRouter();

  const params = useParams();
  const id = params.id;
  console.log("id" , id)

  const [title, setTitle] = useState("");
  const [body, setBody] = useState("");

  // data fetching 해오기
  useEffect(() => {
    fetch(`http://localhost:9999/topics/${id}`)
      .then((res) => res.json())
      .then((result) => {
        console.log("result🙌🙌", result);
        setTitle(result.title);
        setBody(result.body);
      });
  }, [id]);

  return (
    <>  
      {/* submit 은 사용자 참여가 필요. | 이게 작동하려면, 클라이언트 쪽 자원이 필요 */}
      <form
        onSubmit={(e) => {
          e.preventDefault();
          const title = e.target.title.value;
          // target = form 태그
          const body = e.target.body.value;

          // 보낼 때, 데이터를 여기에 넣기
          const options = {
            method: "PATCH", // ⭐⭐⭐ 수정할 때는 PUT 또는 PATCH 를 쓰기
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({ title, body }), // json 으로 변환해서 전송
          };

          fetch(`http://localhost:9999/topics/${id}`, options)
            .then((res) => res.json())
            .then((result) => {
              console.log(result);
              const lastId = result.id;
              console.log(lastId);

              // router 가 fresh data 를 fetching 받기 ⭐⭐
              router.refresh();

              // useRouter 이용해서 리디렉션 시키기
              router.push(`/read/${lastId}`);
            });
        }}
      >
        <p>
          <input
            type="text"
            name="title"
            value={title}
            // 이것만 있으면, title 이 적혀 있기만 함. 단순히 read 만 박아놓음
            // 즉, title 의 값이 바뀌어야, 여기 보이는게 바뀜
            placeholder="title"
            onChange={(e) => setTitle(e.target.value)}
                // 이게 있어야, input 에 쓴게 바뀔 때 마다, setTitle 에 저장되고 -> title 이 변하니까 -> 그게 value 에서 렌더링 됨
                    // 지금 약한것 1 : onChange
                        /* onChange={(e) => setTitle(e.target.value)} 계속 막 이렇게 쓰고 있었다는 것😥😥😥😥😥
                        */
                    // 지금 약한것 2 : setTitle 이 fetching 에서도 작동하고, onChange 에서도 작동하는 상황에서, id fetching 의 영향력을 끊어줬어야 함! ⭐⭐⭐⭐⭐ 
                
                // 글쓰고, 또 바로 안 바뀌네 ㅠㅠ 

          />
        </p>
        <p>
          <input
            type="text"
            name="body"
            value={body}
            placeholder="body"
            onChange={(e) => setBody(e.target.value)}
          />
          {/* <textarea name="body" placeholder="body" />  */}
        </p>
        <p>
          {/* 아, 헐, 이게 보내는 거 였어? 😥😥😥😥😥😥 */}
          <input type="submit" value="update" />
        </p>
      </form>
    </>
  );
}

```
