# 230908\_socket

1. 나는 send 이벤트를 밑에 써야 한다고 생각했다. 그런데, send 를 한번 켜 놓으면, 신호가 오면, (어디에서건 신호가 오면) 반응한다고 생각 한다.

```
            // socket.send() 실행 해야만 -> message 이벤트가 발생  

                // 현재, 코드에서는, 'peer접속' 클릭 이후의 로직에서 socket.send() 이 없으므로 -> message 이벤트 발생 안 함 -> 🔵 수정완료

            socket.on("message" , (_data : string) => {
```

그래서 이 밑에도 가능

![](https://i.imgur.com/j5kIdBX.png)

2. send 안에 매개변수를 넣을 때,

위에 있는 것들 참고해서, 만드는데, IMessage 같은 것들 stringfy 같은 것들
