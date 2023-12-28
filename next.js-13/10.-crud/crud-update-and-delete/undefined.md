# \[적용예시]

#### \[포인트]

1. HTTP method 에서 "DELETE" 를 보내면 된다는, 간단한, 부분을, 간과했어

```
// 아, 삭제 하는 것도, 이렇게 method 로 호출하면 되는 구나 ⭐⭐⭐⭐⭐
const options = { 
  method: "DELETE", 
  headers: {
	"Content-Type": "application/json",
  }};
```

2. 리디렉션

```js
fetch(`http://localhost:9999/topics/${id}`, options)
  .then((res) => res.json())
  .then((result) => {
	console.log("result", result);
	
	// 데이터를 다시 받고 리디렉션 | 이게 없으면, cache 받아져 있는 걸로 가게 됨 ⭐⭐⭐⭐⭐ 
	router.refresh();
	
	// 삭제하고 리디렉션 ⭐⭐⭐⭐⭐⭐ 
	router.push("/");
	
  });
```
