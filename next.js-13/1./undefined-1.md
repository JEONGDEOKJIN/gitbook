# 환경변수 설정

* next.js 공식 문서
  * https://nextjs.org/docs/app/building-your-application/configuring/environment-variables
* 요약

```bash
1. 홈 디렉토리에 '.env.local' 파일 만들기

2. @env.local 파일에서 env 에 담을 정보 넣기
API_URL = http://localhost:9999/

3. 서버 컴포넌트에서 가져오는 법 
export default async function RootLayout({ children }) {
  // 서버 경로를 넣어서, 서버에서 데이터를 가져오기(fetching)
  const res = await fetch(process.env.API_URL + 'topics', {
    // next: { revalidate: 10 },  // 10초 간만 cache 유지, 10초 이후 fresh data 가져오고 다시 cache 저장
    // next: { revalidate: 0 },   // 캐쉬를 쓰지 않겠다.
    cache: "no-store", // 캐쉬를 쓰지 않겠다.
  });

4. 클라이언트 컴포넌트에서 가져오는 법 
	- env 는 password 같은 중요한 정보가 들어가게 됨 
	- so, env 는 서버컴포너트에서만 접근 가능함
	- so, 브라우저 환경을 위한 env 를 쓰고 싶으면, 'NEXT_PUBLIC' 으로 사용해야 함 ⭐⭐⭐⭐⭐ | 출처 : https://nextjs.org/docs/app/building-your-application/configuring/environment-variables ⭐⭐⭐⭐⭐  

                fetch(process.env.NEXT_PUBLIC_API_URL + `topics` , options)
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

5. 이렇게 env 파일 만들면 -> git ignore 에 올라가서, 버전관리 하지 마세요\~ 가 된다. ![](https://i.imgur.com/V4ZmNT7.png)

#### 중요한 팁 : 이렇게 파일 작성하면 되요\~ 라고 나에게 힌트를 줄 때, ' ⭐⭐⭐⭐⭐

```
예를 들어서, .env.local 파일 형식을 작성하려 할 때, 
나중에도 이걸 써야 한다면, 
.env.local.example 이라고 써도 된다는 거지. 
이렇게 해서, ⭐샘플 정보⭐를 담으면 된다는 거지. 
```

![](https://i.imgur.com/qmG9iNa.png)
