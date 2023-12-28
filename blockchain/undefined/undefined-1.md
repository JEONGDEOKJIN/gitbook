# 지갑 생성 및 지갑 목록 보기

## TOC

1. '지갑 생성' 클릭한 경우
2. 지갑 목록 보기 클릭
3. 목록으로 생성된 계좌 클릭한 경우
4. '목록 보기' 같은 '데이터 보기' 를 하는데, post 요청을 하는 이유 ⭐⭐⭐







## block.test.ts 에서 어떤 흐름으로 테스트하는지 이해 하기

```bash
[목표]
- ABC 가 DJ로 부터 받은 서명을 검증

[상황]
DJ 는 'Message' 에 "hello" 를 넣었음. 

이 Message 를 보는 사람이 '이게, 최초에 바로 그때 생성된 바로 그 메시지가 맞아?' 라는 의구심을 가질 수 있어서, 그 의구심을 푸는데 도움이 될 수 있게 (쉽게 말하면, Message 검증을 도와주기 위해)
1) puclicKey = G(기준점) x privateKey 식을 이용해서 얻은 'puclicKey' 와 
2) 자신만이 알고 있는 'privateKey' 를 'k^-1(z+r*private key) mod n' 연산(서명을 만들어내는 연산)에 넣어서 '서명(signature)' 을 '함께 전달' 함. 
3)  결국, 'Message, publicKey, signature(서명)' 을 함께 전달함

그런데, 전달되는 과정에서, 슈퍼 미세먼지 모래 폭풍이 일어나서, 통신망에 많은 에러가 발생했음. 그래서, DJ 가 전달하는 값들이 손상됨. (Message, publicKey, signature 일부가 변조됨)

ABC 라는 사람이 DJ 의 Message 를 "hello" 라고 받음. (다만, publicKey, signature 일부가 달라짐.)
ABC 는 이 Message 가 올바른건지 알고 싶어졌음. 
그래서 'Message' , 'signature' , 그리고 'publicKey' 를 'U1 * G + U2 * public key' 연산(공개키를 넣었을 때 서명값의 일부와, 개인키를 넣었을 때 서명값의 일부가 같은지를 확인하는 연산) 에 넣었음. 

ABC 는 이 연산에서 true 라고 알려주면, 'DJ 가 hello 라고 했구나' 라고 믿기로 함. 
그런데, false를 뱉음 
message 는 올바른데, publicKey 전달이 잘못되어서, 오류가 생김. 
ABC 는 왜 안 되는지 모르겠어서 분통이 터짐. 끝. 

이 과정을 전지적으로 지켜보고 있던 관리자는, publicKey 도 검증되어야 할거 같다는 필요성을 느낌? 

[관련 질문들]
- public key 를 공개하면, private key 가 털리는거 아니야? 
	publicKey = G*privateKey 의 공식 안에서, G 는 기준점으로써, 이미 사람들이 알고 있는 상태이기 때문에, publicKey 를 알려줄 경우, privateKey 가 털릴 수도 있지 않겠냐! 라는 걱정이 생겼음. 다만, '타원곡선 로그 문제 (ECDLP)(Q=kP 의 타원곡선 위의 두 점 P와 Q가 주어졌을 때, 다음의 관계를 만족하는 스칼라 k를 찾는 것이 어렵다는 것.' 로 인해,  걱정이 없다는 걸 알게 됨

- 지금 코드를 보면, 공개키에 대한 아무런 검증을 하지 않고, 공개키를 사용해서 서명을 검증하고 있어. 이렇게 '공개키 무검증' 에 따른 문제점이 분명히 존재할텐데, 공개키를 검증하지 않아도 되는거야? 아니면, 검증 기법이 있는데 내가 아직 모르는 건가?
👉 '공개키 검증' 을 위해 사용되는 방법에는 'PKI(Public Key Infrastructure)' 이 대표적임. 다만, 블록체인을 기반으로 통신하는 경우, 다른 방식을 사용 (우선, 여기까지만 생각하자.)

- 그러면, 대체 이 서명이 일치하는지를 어떻게 파악하는거야?  
'U1 * G + U2 * public key' 연산에 넣을 경우, 
'privateKey 자체를 알 수는 없지'만, 
privateKey 와 동일한지 여부를 판단할 수 있을 정도는 나온다. 
(그리고, 수학을 이해해야, 이 안에서 어떻게 돌아가는지를 이해할 수 있다. 그러니까, 우선, 여기에서 스톱..😥)
```

\


## 위 흐름을 block.test.ts 코드 적용해서 이해해보기

* 깃에 주석으로 해석 👇👇 참고
  * https://github.com/JEONGDEOKJIN/studyNote/blob/master/typescript/kga/230912\_class/05\_wallet/src/core/\_\_test/block.test.ts

\


## 위의 흐름을 코드로 구현한 wallet 클래스 및 지갑 생성, 목록 조회 과정 이해하기

* git 주소 https://github.com/JEONGDEOKJIN/studyNote/tree/master/typescript/kga/230912\_class/05\_wallet

### '지갑 생성' 클릭한 경우

* 깃 주소
  * https://github.com/JEONGDEOKJIN/studyNote/tree/master/typescript/kga/230912\_class/05\_wallet/src/core/wallet
*   흐름&#x20;

    <figure><img src="https://i.imgur.com/nk3rKN8.png" alt=""><figcaption><p>![[blockchain_wallet_지갑생성클릭 1.excalidraw]</p></figcaption></figure>



*   클릭 예시&#x20;

    <figure><img src="https://i.imgur.com/Ag3DNNN.png" alt=""><figcaption></figcaption></figure>

### 지갑 목록 보기 클릭

* 깃 주소
  * https://github.com/JEONGDEOKJIN/studyNote/tree/master/typescript/kga/230912\_class/05\_wallet/src/core/wallet



*   흐름도 &#x20;

    <figure><img src="https://i.imgur.com/ejStG2H.png" alt=""><figcaption><p>blockchain_wallet_지갑목록보기.excalidraw</p></figcaption></figure>



*   클릭 예시&#x20;

    <figure><img src="https://i.imgur.com/Rz6DK6z.png" alt=""><figcaption></figcaption></figure>

\


### 목록으로 생성된 계좌 클릭한 경우

* 깃 주소
  * https://github.com/JEONGDEOKJIN/studyNote/tree/master/typescript/kga/230912\_class/05\_wallet/src/core/wallet
*   흐름도&#x20;

    <figure><img src="https://i.imgur.com/NAzl9LK.png" alt=""><figcaption><p>![[blockchain_wallet_목록으로생성된계좌클릭한경우.excalidraw]]</p></figcaption></figure>



*   클릭 예시&#x20;

    <figure><img src="https://i.imgur.com/r6bGJwN.png" alt=""><figcaption></figcaption></figure>



## ⭐⭐⭐그래서 정리하면,

```bash
- '지갑' 이란? 
	- 나만의, 고유한 어떤 정보들을 담는 곳 ex) 돈, 신분증 등

- 지금 이 블록체인 튜토리얼의 경우 '지갑' 이란? 
	- 'name' 이라는 공간에는, '계좌 정보(account)' 를 담고, 
	- 'content' 라는 공간에는, 'privateKey' 를 담아둠. 
	- 하고 많은 데이터 중에서, 왜 이 2가지 데이터를, 그렇게 담았는지는, 아직 잘 모르겠음. 

- 이 '지갑' 은 어떻게 만들어지는데? 
	- 지갑은 '개인키' 에 의해 '공개키' 가 생성되고 -> 공개키에서 '26번째 인덱스' 부터의 값이 '지갑 주소'가 된다. -> 지갑 주소가 있으면 지갑이라는게 껍데기는 만들어진거다. 

- 그럼, 지갑에 어떤 '내용' 을 채우나? 
	- 이건 앞으로 좀 더 지켜봐야 할 것 같다.

- 그럼, 이 '지갑' 이라는게 왜 필요한데?
	- 이것도 지켜봐야 할 것 같다. 
```











## 알게 된 점

### '목록 보기' 같은 '데이터 보기' 를 하는데, post 요청을 하는 이유 ⭐⭐⭐

```
GET 요청으로 백엔드에 정보를 요청하면, 
해당 요청은 URL 에 남을 수 밖에 없다.

따라서, 계좌 정보 같은 민감한 내용을 백엔드에 전달하려 할 때, GET 요청을 하면, URL 에 남아서, 중간에 탈취될 위험성이 존재하게 된다. 

그러면, 어떻게 방지할 수 있을까? 

'이 계좌정보에 해당하는 data 가져다줘' 라고 조회하려면, POST 요청을 사용할 수 있다. 
그러면, 프론트가 백엔드에 요청을 전달할 때, body 에 숨어들어가서, 어떤 정보를 요청했는지, url 에 남지 않게 된다. 

body 에 들어간다는게, 아직은, 잘 이해가 안 된다.
그러면, 구체적으로 어떤 body 에 들어가게 되는지 이해하면 좋을 것 같다. 
```

![](https://i.imgur.com/24IndEI.png)









