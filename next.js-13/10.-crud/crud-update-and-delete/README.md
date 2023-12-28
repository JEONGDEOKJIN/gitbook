# \[CRUD] Update & Delete

### \[task] update, delete 버튼이 1) 게시글 들어가면 보이고 2) 3번 게시글에서 누르면, 3번 게시글이 update 될 수 있게 하기\_서버 컴포넌트에서, 클라이언트 환경에서 제공하는 데이터가 필요한 경우, 서버 컴포넌트 내에 클라이언트 컴포넌트를 운영하게 되는 경우 ⭐⭐⭐⭐⭐

* 이건 어떤 문제냐면

```bash
- 서버 컴포넌트인 layout.js 에서, id 에 값이 있는 경우, 보이고, 안 보이고를 처리. 
- 그러려면, id 값을 알아야 함. 
- id 값은, useParams 훅을 사용해서 알 수 있음. 
- 그러면, 서버컴포넌트에서, 브라우저(클라이언트) api 에서 제공하는 데이터를 필요로 하는 상황임. 
```

* 해결방안 ⭐⭐⭐⭐⭐

```bash
'layout.js' 는 '서버 컴포넌트'를 유지하면서, 
(클라이언트 환경인 브라우저 js 환경에서 제공받는) useParams 에서 제공받게 되는(의존하는) id 값에 따라서 보였다, 안 보였다 하는 컴포넌트를 '클라이언트 컴포넌트로 변환' 하면 됨

즉, 특정 서버 컴포넌트가, 클라이언트(브라우저) 환경 자원, api, 데이터에 의존하게 되는 경우, 
해당 부분을 별도의 클라이언트 컴포넌트로 만들어서 빼면, 
서버 컴포넌트는 그대로 유지되면서, 
클라이언트로 부터 필요한 자원을 쓰는 클라이언트 컴포넌트가 만들어진다.
```

* 서버 컴포넌트 안에서, 클라이언트(브라우저) 환경의 리소스가 필요하여, 클라이언트 컴포넌트로 변환해서 사용하는 코드 ⭐⭐⭐ ![](https://i.imgur.com/O1SCd7I.png)
* 컴포넌트 클라이언트에서 게시글 들어갔을 때만 버튼이 보이게 하는 코드

```bash
핵심은 
'게시글에 들어갔을 때, 를 어떻게 포착할 것 인가!'
여기에서는 게시글 id 값, 을 사용했다 는 것 ⭐⭐⭐⭐⭐ 
```

```js
"use client";
import Link from "next/link";
import { useParams } from "next/navigation";

export function Control() {
  const params = useParams();
  const id = params.id;
  console.log("params.id", id);

  return (
    <ul>
      <li>
        <Link href="/create"> Create </Link>{" "}
      </li>

      {id ? (
        <>
          <li>
            <Link href="/update"> Update </Link>{" "}
          </li>
          <li>
            <input type="button" value="delete" />{" "}
          </li>
        </>
      ) : null}
      
    </ul>
  );
}
```

#### 팁

```
코드 스니펫 블록 처리 하고 -> 전구 클릭하면 -> move to new file 나옴 
그러면, import 하는 것 까지 자동으로 생김 
```

![](https://i.imgur.com/L2spwbo.png) ![](https://i.imgur.com/2gAQSlV.png)
