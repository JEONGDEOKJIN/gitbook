# \[문제상황] 주소는 있는데, 해당 페이지로 갔을 때, 아무것도 안 그려주고 있음.

#### \[문제상황] 주소는 있는데, 해당 페이지로 갔을 때, 아무것도 안 그려주고 있음.

![](https://i.imgur.com/l6Qd2mX.png)

* 해결

```bash
1. 폴더 잡아주고 > page.js 에 원하는 것 넣어주기 

2. 업데이트는 read 와 create 의 합성이기 때문에, 해당 기능들 섞어서 넣어주기 
	- create 는 클라이언트 컴포넌트 이기 때문에 그대로 가져왔음 
	- read 는 서버 컴포넌트 이기 때문에, 그대로 못 가져옴 

3. read 는 
	1) data fetching 
	2) 가져온 데이터 useState 에 저장하기 
	3) html 로 꽂아주기 로 진행 
```

* 반드시 만나게 되는 문제 1 : input 창은 read 함과 동시에 create 을 하기에, read 와 crate 데이터가 충돌이 나게 됨 ⭐⭐⭐⭐⭐⭐⭐⭐

```
이때, read 할 때, 한번만! 가져오게 해야 함 
그러면, 이번에는 data fetching 할 때, id 를 넣어서 해결. 
그러면, id 가 바뀔 때 마다 가져오게 되는 거니까 -> 실질적으로는 1번만! 가져오게 된다. ⭐⭐⭐⭐⭐ 

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
```

* 반드시 만나게 되는 문제 2 : 업데이트 버튼을 누르고, 글이 바로 업데이트가 안 되는 경우 ⭐⭐⭐⭐⭐⭐⭐⭐⭐⭐

```js
이건, 그려주는 데이터를 cache 를 사용하기 때문이다. 

- 우선은, cache 를 사용하지 않는 방안으로 작성.
  const resp = await fetch(`http:localhost:9999/topics/${props.params.id}`, {
    cache: "no-store",
  });

- 그 다음 revalidation 을 사용하는 방안으로 작성. ⭐⭐⭐⭐⭐⭐ 

```

#### NEXT.JS 에서는 PATCH 또는 PUT 을 써야 함

* 수정할 때는 POST 말고, PATCH 또는 PUT 을 사용 해야 함 ![](https://i.imgur.com/QsmnUSe.png)
