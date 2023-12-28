# 230909 구조분해할당 문법 & 배열 메소드 (join) 에 대한 예시

```js
    const render = async() => {
        const {data : peer} = await axios.get('http://localhost:8080/peer')
            // 구조 분해 할당하고 이름을 peer 로 바꿈 

        peerView.innerHTML = peer.join(" | ")
    }
```

***

* 이렇게 보내고

```
    const _blockCreate = async() => {
        const _blockData = [blockData.value]

        const {data : block} = await axios.post("http://localhost:8080/block/mine" , {data : _blockData})   // _blockData 는 '배열' 형태로 들어감
    }
```

* 이렇게 받는게 , 아직 내가 이렇게 막 쓸 수 있지는 않은 것 같음.

```
app.post("/block/mine" , (req : Request, res : Response) => {
    // 블록에는 BODY 의 내용을 쓰게 됨 
        // 블록에 기록할 내용을 받고, data 의 배열은 '배열' 
        // 블록에 기록된건 문자열 배열

    const {data} : {data : Array<string>} = req.body;
        /* [해석]
            1) req.body 에서 data 속성을 가져와서 객체 구조 분해 할당을 함 
            2) 해당 값에 Array<string> 타입 지정을 함
        */

    const newBlock : Block | null = Block.generateBlock(ws.latestBlock() , [...data],ws.getAdjustmentBlock())
        // null 이면 에러
        // 블록을 만들면 정상 가져옴 

    if(newBlock === null) res.send("error")

    ws.addToChain(newBlock)
    res.json(newBlock)      // 추가되었어~ 라고 보여줌

})
```
