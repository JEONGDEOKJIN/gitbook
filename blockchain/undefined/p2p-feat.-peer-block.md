---
coverY: 52
layout:
  cover:
    visible: true
    size: full
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# P2P 이해 하기 (feat. peer 접속 및 갱신, block 갱신 및 생성)

***

{% embed url="https://github.com/JEONGDEOKJIN/studyNote/tree/master/typescript/kga/230908_class/04_p2p" %}
p2p 방식 코드 주석
{% endembed %}

***

## TOC

1. 'peer 접속' 분석
   1. 요약
   2. 'peer 접속 눌렀을 때' 흐름도
   3. index.ts 분석 (로직 처리)
2. 'peer 갱신' 분석
   1. 요약
   2. 흐름도
   3. 코드 분석
3. 'block갱신' 분석
   1. 요약
   2. 흐름도
   3. 코드 라인 해석
4. 'block 생성' 분석
   1. 요약
   2. 흐름도
   3. 코드 라인 해석
5. 회고

## peer 접속 및 block 갱신 화면 분석하기

![](https://i.imgur.com/ev6XqNN.png)

### 'peer 접속' 분석

#### 요약

```
peer 접속을 클릭하면, 백엔드에서 
1) socket 연결하고 
2) message type 을 전달해서
3) message type 에 따라 다른 방식으로 socket 에 send 한다. 
```

\


#### 'peer 접속 눌렀을 때' 흐름도

!\[\[blockchain\_p2p\_로직.excalidraw]]

![](https://i.imgur.com/anP6jFT.png)

#### index.ts 분석 (로직 처리)

* https://github.com/JEONGDEOKJIN/studyNote/tree/master/typescript/kga/230908\_class/04\_p2p/src/core

```ts

// 원래는 post 로 작성했는데, get 으로 바꿀 것 (왜냐면, 오타 이슈 , 내 v4 확인도 귀찮)
    // 찾으려면, ipconfig 해서, ipv4 가져옴. 
    app.get("/peer/add" , (req : Request , res : Response) => {

        // IPv4 네트워크 주소를 찾아서 -> v4 변수에 할당
            const networkinterface = os.networkInterfaces();
                // os.networkInterfaces(); 의 결과 '네트워크 인터페이스 정보' 를 알 수 있음. 
                    // '네트워크 인터페이스' 는 1) MAC 주소, 2) IP 주소 같이, 네트워크 연결에 필요한 요소들이 들어가 있음.  
                    // array of network interfaces which again consists of the following attributes(https://www.geeksforgeeks.org/node-js-os-networkinterfaces-method)
            console.log("🚀🚀networkinterface" ,networkinterface)
                /* 
                    🚀🚀networkinterface {
                        "로컬 영역 연결* 10" : [{
                            address : ""
                            netmask : "" 등등
                        }, {
                            address : ""
                            netmask : ""등등
                        }], 
                        "WI-FI" : [{
                            adress : ""
                            netmask : "" 등등
                        }, {
                            adress : ""
                            netmask : "" 등등
                        }]
                    }
                */

            let v4 : string;

            for (const key in networkinterface) {
                    //[for in 문법] networkinterface 객체에서, key 하나를 가져오고 -> 순회 하고 -> 그 다음, 다음 key 로 순회한다. 
                    // key = "로컬 영역 연결* 10" , "WI-FI" 등등
                    
                const Array = networkinterface[key];
                    // Array = [ {address : "" netmask : "" 등등} , {address : "" netmask : "" 등등} ] 
                
                for (const value of Array){
                    // [for of 문법] '반복가능한 객체' 중, '하나의 요소' 를 가져와서, 뭔가를 하고 -> 그 다음 요소로 넘어감.
                    
                    if(!value.internal && value.family === 'IPv4')
                        // internal 
                            // 'internal 값이 false' = 외부 네트워크와 통신 가능
                            // 'internal 값이 true' = 외부 네트워크와 통신 불가 | 내부만 가능
                        // family : 네트워크 주소 타입
                    
                    v4 = value.address;     // IPv4 네트워크의 주소를 v4 에 할당
                }}

        // 해당 주소에 '연결'
        ws.addToPeer(`ws://${v4}:7545`)
            // ws : websocket 프로토콜로 통신할거야 
            // 주소(address) : v4 
            // 포트번호(port) : 7545  
                // 추후에 Ganache(가나싱) 사용하기 위해서, 포트 번호를 7545 로 함. 
                // Ganache 란? : 이더리움 메인넷, 테스트넷, 사용하지 않고도, 스마트 컨트랙트 개발이 가능한 '개인(로컬) 블록체인 환경'

        res.end()
    })
```

### 'peer 갱신' 분석

#### 요약

```
1. peer 갱신 클릭을 하면 -> 소켓 목록을 가져와서 -> html 에 그대로 그려준다. 
```

#### 흐름도

![](https://i.imgur.com/BQcmNdW.png)&#x20;

!\[\[blockchain\_p2p\_로직\_peer갱신.excalidraw]]

#### 코드 분석

* @index.html

```js
    // peer 갱신
    const render = async() => {
        const {data : peer} = await axios.get('http://localhost:8080/peer')
            // 구조 분해 할당하고 이름을 peer 로 바꿈 
        peerView.innerHTML = peer.join(" | ")
    }

    peerViewBtn.onclick = render;
```

* index.ts

```ts
// 현재 socket 에 참여중인 사람들
    app.get('/peer' , (req : Request , res : Response) => {
        const sockets = ws.getSockets();
            // [해석] '웹소켓 서버' 에 연결된 '소켓들' 의 '목록' 가져오기 

            console.log("웹소켓 연결" , sockets)    
                // 웹소켓 연결 [ '::ffff:192.168.0.92 : 53360', '192.168.0.92 : 7545' ]
                // 여기에 나오는 데이터는 socket._socket.remoteAddress와 socket._socket.remotePort 정보를 문자열 형태로 저장한게, sockets 로 나옴
                // remoteAddress와 remotePort 가 담기는 곳 = @p2p.ts 65line 
                // 아마도, socket.send 할 때, 보내지는게 아닐까, 추정.

        res.json(sockets);
    });

```

### 'block갱신' 분석

#### 요약

```
block 갱신을 누르면 -> 현재 chain 에서 모든 정보를 가져와서 -> html 에 그려준다. 
```

#### 흐름도

![](https://i.imgur.com/9ojoH83.png)&#x20;

!\[\[blockchain\_p2p\_로직\_block갱신.excalidraw]]

#### 코드 라인 해석

* index.html

```js
    // block 갱신
    const blockRender = async () => {
        const {data : block} = await axios.get('http://localhost:8080/chains') 
         // 현재 들어오는거 chains 로 확인
        blockView.innerHTML = JSON.stringify(block);
    }
```

* index.ts

```ts
// 현재 전체 chain 반환을 요청
    app.get("/chains" , (req : Request , res : Response) => {
        res.json(ws.get())
    })
        /* [해석] ws 는 P2P 클래스의 인스턴스 
            P2P 클래스는 Chain 클래스를 상속받음. 
            Chain 클래스의 get 메소드는 this.chain 을 return 함 
            따라서, ws 객체 안에는 현재 chain 정보를 가져올 수 있는 get 메소드가 있음.
            따라서, 해당 결과물을 반환함. 
        */
```

\


### 'block 생성' 분석

#### 요약

```bash
input 에 원하는 data 를 입력하고 -> '생성' 을 클릭하면 -> generateBlock 메소드로 들어가고 -> return 된 newBlock 이 addToChain 으로 들어가서 -> 다른 블록에게 send 된다. 
```

#### 흐름도

![](https://i.imgur.com/4RwIY3p.png)

!\[\[blockchain\_p2p\_로직\_block생성.excalidraw]]

#### 코드 라인 해석

* index.html

```js
    // block 생성
    const _blockCreate = async() => {
        const _blockData = [blockData.value]

        const {data : block} = await axios.post("http://localhost:8080/block/mine" , {data : _blockData})   // _blockData 는 '배열' 형태로 들어감
    }

    blockCreate.onclick = _blockCreate;
```

* index.ts

```ts
app.post("/block/mine" , (req : Request, res : Response) => {
    // 블록에는 BODY 의 내용을 쓰게 됨 
        // 블록에 기록할 내용을 받고, data 의 배열은 '배열' 
        // 블록에 기록된건 문자열 배열

    // req.body 에서 구조분해할당으로 data 속성 뽑아오기 
    const {data} : {data : Array<string>} = req.body;
        /* [해석]
            1) req.body 에서 data 속성을 가져와서 객체 구조 분해 할당을 함 
            2) 해당 값에 Array<string> 타입 지정을 함
        */

    // 받아온 data 로 새로운 block 만들기
    const newBlock : Block | null = Block.generateBlock(ws.latestBlock() , [...data],ws.getAdjustmentBlock())
        // null 이면 에러
        // 블록을 만들면 정상 가져옴 

    if(newBlock === null) res.send("error")

    ws.addToChain(newBlock)     // newBlock을 chain 에 추가

    res.json(newBlock)      // 추가되었어~ 라고 보여줌
})
```

### 회고

```bash
- excalidraw 활용 흐름도 작성 팁 
	1. 흐름도 작성시, 손으로 먼저 하는게 중요. 왜냐면, 개념학습에 있어서, pen is stronger than keyboard
	2. 그 다음, 대략 흐름을 알것 같은 느낌이 들 때, 키보드로 한번 더 작성 
	3. '해당 흐름도에 관련된 코드' 를 '옆에 붙이는 느낌' 으로 모두 '밑에 작성' 해야 함  
		- 각 라인별 주석은 코드 라인 해석에 달자 

	4. 뒷 배경 & 텍스트를 분리해서 작성하면 좀 더 편함 

	5. 코드 공부에 있어 필수적인 목차 
		- 흐름도 
		- 코드 line or 기능해석 
		- 그 다음 '요약' (요약을 계속 시도 해야, 내가 말로 설명할 수 있게 된다.) ⭐⭐⭐⭐⭐ 
```
