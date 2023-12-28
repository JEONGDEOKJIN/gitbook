# 트러플(truffle) 환경에서 컨트랙트 배포 하기

## TOC

1. 1️⃣ 설치 및 환경 설정
2. 2️⃣ 컴파일
   1. 1\) 솔리디티 코드 작성
   2. 2\) 컴파일 명령어 작성
3. 3️⃣ 배포 (migration, deploy)
   1. 1\) 어떤 네트워크에 배포? 👉 ganache 로 배포! 👉 so, ganach 설치 및 실행
   2. 2\) '컴파일된 파일' 을 가져와서, 배포(deploy) 준비
   3. 3\) 'npx truffle migrate' 로 실제 배포(migration, deploy) 하기
   4. 4\) 배포한 CA 주소 저장 해두기
4. 4️⃣ 배포된 컨트랙트에 접근
   1. 1\) truffle 콘솔 사용
5. 5️⃣ 테스트 코드 작성
   1. 1\) 테스트 코드 작성
   2. 2\) 테스트 코드 실행
6. 6️⃣ 테스트 코드 작성
   1. 1\) 배포한 컨트랙트 내용 조회
   2. 2\) getValue 값 가져오기
   3. 3\) setValue 로 값 변경하고, getValue 로 값 가져오기
7. 7️⃣ react hook 으로 증감 버튼 누르면, 상태변수 변경 및 조회 구현
   1. 1\) react 기본 설정
   2. 2\) hooks, abi 폴더 및 파일 만들기
   3. 3\) 컴파일 완료된 Counter.json 에서 abi 키의 값만 가져와서 붙여넣기
   4. 4\) hook 코드 작성
   5. 5\) react app.js 코드 작성
8. 8️⃣ 실행해보기 | #checksheet
9. 🤯 디버깅
   1. 문제 상황
10. 🔮 궁금증

\


## truffle 이해

* truffle 개념

```bash
- 배포를 편하게 해주는 '프레임워크'(폴더 구성이 포함되어 있음.)
- Dapps 개발을 쉽게 할 수 있도록, 도와주는, 프레임워크
- 배포를 편하게 할 수 있음! 
- 스마트 컨트랙트 컴파일, 배포 및 테스트 기능을 쉽게 할 수 있도록 도와준다. 
```

* truffle 구조

```bash
트러플 사용 초기 단계 
	트러플은 프레임워크 이기 때문에, 'init 하면 -> 폴더 구조 3개' 를 만들어준다. 
	
생성되는 폴더 
	1) constract 
		- 솔리디티 코드를 작성한 'sol 파일'을 담을 폴더 
		- '컴파일' 명령을 내리면, 'contract 폴더'에 있는 'sol 파일' 을 읽어서, '컴파일' 을 진행. 
		- 그러면, build 폴더가 생기고, 컴파일된 내용이, json 파일로 생성됨. 
	
	2) migrations 
		- '스마트 컨트랙트' 를 '이더리움 네트워크' 에 배포하는 과정 (❓❓❓)
		- 컨트랙트 코드를 진행할 js 코드 작성 
		- 이더리움 네트워크에 배포하는 내용을 작성할 js 를 이 폴더에 

	3) test 
		- 테스트 파일 
```

\


## sol 파일 예시

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Counter {
    // 상태 변수 
    uint256 private value;
        // [uint256]
            // 의미 : unsigned int, 부등호가 없는 정수, 마이너스로 떨어질 수 없는 정수
            // 필요성 : 바이트를 낭비하지 않기 위해 작성

    // 지갑의 주소
    address sender = 0x0123456789012345678901234567890123456789;
        // address 타입 
            // [특징] ca 를 담을 수 있음. -> so, 40자리가 와야 함. 
            // [특징] 지갑주소를 만들 때 봤음 
            // [특징] address 타입으로 변수를 만들 경우, 내부 인스턴스가 있어서, 내부 메소드를 사용할 수 있음.  
                // ex) sender.balance : 해당 주소의 이더 잔액 조회 가능 
                // ex) sender.transfer("보낼금액") : 이 지갑에, 얼마를 전송해~ 라는 의미 | 실패시 '예외' 를 발생시킴 
                // ex) sender.send("보낼금액") : 이 지갑에, 얼마를 전송해~ 라는 의미 | 성공시 true, 실패시 false

    constructor() {}

    function setValue( uint256 _value ) public {
        value = _value;
    }
        /*  [해석]
            - 내용 변경이니까 -> 가스비 발생 
            - '원격 프로시저 호출' 에서 send 로 보낸다. 
                - 스마트 컨트랙트 함수를 호출할 때, 이더리움 네트워크에 트랜잭션을 전송하려면, send 나 sendTransaction 메소드를 사용 
                - 여기에서 send 는 이더리움 네트워크에, 트랜잭션을 전송하는 메소드로 사용된건가? 
                - 그러면, 이 setValue 를 send 로 보내주는 과정이 있는 건가? ❓❓❓❓❓❓ 
            - public 접근자 : 어디서나 접근 가능
        */

    function getValue( ) public view returns (uint256) {
        return value;
    }
        // view : 상태 변수 변경하지 않고 읽기 전용! 

}
```

## dev

### 1️⃣ 설치 및 환경 설정

```bash
# 리액트 설치
npx create-react-app dj_test
	# 	트러플에 리액트는 필수가 아님 (다만, 지금 리액트를 쓰기 위해서임!)

# 방금 만든 폴더로 이동 
cd dj_test

# 리액트 설치한 폴더에 truffle 설치 (✅ 리액트 폴더에 트러플을 설치해야 하는 건 아니지만, 이번에는 이렇게 진행)
npm i truffle

# 트러플 초기 셋팅 
npx truffle init

# truffle-config.js 파일 수정
	# 환경 설정 | 이걸 복사해서 -> truffle-config.js 로 붙여넣기
	module.exports = {
	  networks: {
	    development: {
	      host: "127.0.0.1", // Localhost (default: none)
	      port: 8545, // Standard Ethereum port (default: none) | 가나쉬 포트 ✅
	      network_id: "*", // Any network (default: none)
	    },
	  },
	
	  compilers: {
	    solc: {
	      version: "0.8.13",     // 솔리디티 버전✅
	    },
	  },
	};
```

### 2️⃣ 컴파일

#### 1) 솔리디티 코드 작성

```bash
- contract 폴더에 sol 파일 만들기
```

* sol 파일 코드

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract Counter {
    // 상태 변수 
    uint256 private value;
        // [uint256]
            // 의미 : unsigned int, 부등호가 없는 정수, 마이너스로 떨어질 수 없는 정수
            // 필요성 : 바이트를 낭비하지 않기 위해 작성

    // 누르면 1 증가 
    function increment() public {
        value += 1;
    }

    // 누르면 1 감소 
    function decrement() public {
        value -= 1;
    } 

    // 값 가져오기
    function getValue( ) public view returns (uint256) {
        return value;
    }
        // view : 상태 변수 변경하지 않고 읽기 전용!
}

// 왜 여기에는 1) sender 와 2) consturctor 3) setValue 가 없지?
// address sender = 0x0123456789012345678901234567890123456789;
// constructor() {}
```

#### 2) 컴파일 명령어 작성

```bash
# truffle 이 설치된 dj_test 경로에서 실행했음 
npx truffle compile

# 실행 후, dj_test 내부에, build 폴더가 생김 
	# 해당 폴더에는 json 형식으로 저장되어 있고 
	# build 폴더 안에, ⭐⭐ abi 가 생김 
	# network 키 안에는 'CA 가 저장 될 예정⭐⭐'
```

*   abi : 이걸 통해서, 이제 solidity 를 컨트롤 해서, 이더리움 안에 있는 메서드를 실행하게 됨 (#📛 | 추가로 좀 더 학습 필요)&#x20;

    <figure><img src="https://i.imgur.com/JP0u1Zm.png" alt=""><figcaption></figcaption></figure>
*   ca 가 들어갈 networks&#x20;

    <figure><img src="https://i.imgur.com/Zrune0S.png" alt=""><figcaption></figcaption></figure>
*   ⭐⭐⭐ abi 의 '값' 만 가져와야 함&#x20;

    <figure><img src="https://i.imgur.com/K7jTDTF.png" alt=""><figcaption></figcaption></figure>

\


### 3️⃣ 배포 (migration, deploy)

#### 1) 어떤 네트워크에 배포? 👉 ganache 로 배포! 👉 so, ganach 설치 및 실행

```bash
# ganache 설치 
	# 설치 경로는 dj_test! 
npm i ganache-cli 

# ganache 실행
npx ganache-cli
```





#### 1.5) truffle 환경 설정 ⭐⭐

```js
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1", // Localhost (default: none)
      port: 8545, // Standard Ethereum port (default: none) | 가나쉬 포트 ✅
      network_id: "*", // Any network (default: none)
    },
  },

  compilers: {
    solc: {
      version: "0.8.13",     // 솔리디티 버전✅ | sol 파일에 적힌 버전과 맞아야 함
    },
  },
};
```

![](https://i.imgur.com/bzUlhTE.png)

#### 2) '컴파일된 파일' 을 가져와서, 배포(deploy) 준비

* a) '배포 파일 이름' 정하기

```js
- 파일명 : '번호(순서)'_'내용'_'컨트랙트 이름'.js 파일로 이름 작성
ex) 1_deploy_Counter.js 이렇게 이름을 작성
```

![](https://i.imgur.com/saCYden.png)

* b) '컴파일된 결과물' 가져오고 하고 👉 배포(deploy) 하기

```js
// '컴파일 된 결과물' 가져오기
const Counter = artifacts.require("Counter");
    // [필요성] '컴파일된 결과물' 을 '이더리움 네트워크' 에 올릴 수 있도록 하기 위함 
    // [해석] 
        // artifacts : 컴파일된 결과를 가져올 수 있는 truffle 의 라이브러리 
        // artifacts.require("Counter") : 컴파일 된 결과 중, 파일 이름이 Counter 인 것을 가져온다. 

module.exports = (deployer) => {

    // Counter 파일, 배포하기 
    deployer.deploy(Counter)
        // [해석]
            // deployer : module.exports 실행시, Truffle 에 의해 주입 된다. 
            // deployer.deploy : 이더리움 네트워크에 올라가게 한다. 
            // Counter : 1) '작성한 solidity' 가 2) '컴파일된 파일' 을 3) 'artifacts' 로 가져온 파일
}
			// deploy 매개변수 = 배포할 스마트 컨트랙트 = 트러플 콘솔에서 이 이름을 불러야 함 ⭐⭐⭐⭐⭐

    // [해석]
        // 익명함수의 매개변수인 deployer 는 Truffle 이 해당 함수를 실행할 때, 자동적으로, 채워준다. 
    
    // [배포 완료 되면]
        // 1) '스마트 컨트랙트의 바이트 코드' 가 '이더리움 네트워크' 에 배포 ⭐⭐⭐⭐⭐⭐
        // 2) 그 결과, 해당 컨트랙트 주소(CA, contract address) 가 생성된다.  ⭐⭐⭐⭐⭐⭐
```

\


#### 3) 'npx truffle migrate' 로 실제 배포(migration, deploy) 하기

```bash
# 실제, 배포 가능하게 하는 명령어
	# 경로는 dj_test 에서 진행 | 그 이유는 truffle 이 dj_test 에 설치 되어 있기 때문! 
npx truffle migrate
	# 현재, ganache 네트워크 안에 있으므로, ganache 에서 배포 진행
```

![](https://i.imgur.com/x2VXx74.png)

\


#### 4) 배포한 CA 주소 저장 해두기

```
0x3Dcbf3B1842038Df2C34afb9e2808592fC08801a
```

```BASH
그러면, 이제 
	- ⭐⭐ '해당 CA 주소'로, 이더리움 네트워크에 올라가 있는 Counter.sol 파일에 접근해서, 상태변수값을 가져오거나, 수정할 수 있음. ⭐⭐ 
	- 이 과정에서 'CA 로 요청을 보내서, call 이나, send 를 통해, 원격 프로시저 실행을 할 수 있음' 이라는 건 어떤 의미 일까❓❓❓ 
		- web3 라이브러리 중 하나 인 call, send 를 사용해서, 'await contract.methods.getValue().call();' 이러한 구문을 통해, solidity 에 작성 함수인 getValue( ) 에 접근할 수 있다는 말 임! 
```

\


### 4️⃣ 배포된 컨트랙트에 접근

* 이 과정에 대한 궁금증

```
- 지금 이 순간이 원격 프로시저, call, send ca 요청, 이라는 키워드를 사용해서, 어떻게 설명될 수 있나 ? ❓❓❓❓❓❓ 

[수업 중 이런 필기가 있는데, 완전히 이해를 못 한 듯 하다.]
- CA 로 요청을 보내서, call 이나, send 를 통해, 원격 프로시저 실행을 할 수 있음.
    - ⭐⭐지금은 그걸 하지 않고, 트러플에서 콘솔로 확인할 수 있음. ⭐⭐
    - 트러플에서 할 수 있게 편하게 도와줌
    - truffle 콘솔에서 확인해보고 싶은데
```

#### 1) truffle 콘솔 사용

```js
// 기존에는, js 에서 코드로 작성하고 -> 개발자 도구로 확인했던 것 같은데, 지금은 truffle 전용 콘솔창을 열어서 진행

// truffle 콘솔 실행
	//dj_test 폴더에서 진행
npx truffle console

// truffle 콘솔에서 아래와 같이 친다 👇👇👇 
Counter.deployed().then( (instance) => (counter = instance ) );
	// [해석]
		// Counter 
				// 1) 'sol파일 작성' 할 때, 그 이름이 'Counter.sol' 이었음. 
			// 2) migrations 에서 const Counter = artifacts.require("Counter") 이렇게 컴파일된 파일을 가져왔음. (⭐⭐ 이 순간, 배포될 이름이 결정됨! ⭐⭐⭐)
			// 3) 위에서 결정된 이름으로 'deployer.deploy(Counter)' 에 의해 deploy 됨 
			// 4) truffle 콘솔에서는, 위와 같이 deploy 된 이름을 사용함! 따라서, Counter 가 됨. 

		// Counter 실행의 의미 ⭐⭐⭐
			// 아직, '이더리움 네트워크에 올라가기 전!' 'Counter 컨트랙트의 정의 및 구조' 를 볼 수 있음. ⭐⭐⭐ 

		// Counter.deployed() 의 의미 ⭐⭐⭐ 
			// '실제 이더리움에 배포된 Counter 를 '인스턴스' 로 가져온다.		

		// Counter.deployed().then( (instance) => (counter = instance ) );
			// 실제 이더리움에 배포된 Counter 컨트랙트를 1) instance 매개변수로 받고 2) instance 에 있는 것을 counter 변수에 넣기
		
		// instance 를 받은 counter 로 할 수 있는 것 
			// 1) 배포된 스마트 컨트랙트(Counter) 의 하위 요소인
			// 2) 메서드(sol 파일 안에 있는 getValue, setValue 등) 에 접근
			// 3) 여기에 call 과 send 가 들어가 있는 건가❓❓❓❓❓ | '여기에는 call 과 send 가 메서드로 포함되어 있음' 이라는게 무슨 말 이지❓❓❓ 

// sol 파일에 기재한 함수인 getValue( ) 를 실행해서 값을 가져오기
counter.getValue()
	// 결과 : BN { negative: 0, words: [ 0, <1 empty item> ], length: 1, red: null }

// 아래 setValue 는 sol 파일에 없음 -> so, 아래와 같이 기재하면, 에러남 
counter.setValue(20)

// counter.increment(1)
counter.increment(1) 👉 counter.getValue() 하면, 👉 'BN { negative: 0, words: [ 1, <1 empty item> ], length: 1, red: null }' 이렇게 값이 나와 있음. 
```

*   truffle 이 실행되는 경로&#x20;

    <figure><img src="https://i.imgur.com/0sXXDTe.png" alt=""><figcaption></figcaption></figure>
*   counter.getValue() 실행 결과&#x20;

    <figure><img src="https://i.imgur.com/91bYEtu.png" alt=""><figcaption></figcaption></figure>
* 아래 세부 메소드 정의
  * setValue 없음
  *   increment() 있음.&#x20;

      <figure><img src="https://i.imgur.com/DyaWZEJ.png" alt=""><figcaption></figcaption></figure>

\


### 5️⃣ 테스트 코드 작성

#### 1) 테스트 코드 작성

*   테스트 코드 작성 위치&#x20;

    <figure><img src="https://i.imgur.com/RtyqrpZ.png" alt=""><figcaption></figcaption></figure>

#### 2) 테스트 코드 실행

```bash
# test 폴더 안에 있는게 아래 명령어를 쓰면 실행된다. 
	# 실행 경로는 dj_test 안에서 실행하면 된다.
	# truffle console 이 아니라, ✅일반 터미널 임. 
npx truffle test


```

\


### 6️⃣ 테스트 코드 작성

#### 1) 배포한 컨트랙트 내용 조회

```js
it( "counter 1" , async () => {
	// 배포한 컨트랙트 내용 조회
	counter = await Counter.deployed();
		// console.log("배포한 컨트랙트 내용 조회" , counter)

		// [해석] Counter 는 '배포 되기 전 구조 및 정의'
		// Counter.deployed() 는 배포되고 난 후 의 내용 
} )
```

\


#### 2) getValue 값 가져오기

```js
[call 의 기능] 
- call 은 ⭐⭐'읽기 전용 함수(함수에서 view 혹은 pure 접근자가 붙은 경우)'⭐⭐ 에서 값을 가져올 때 사용 된다. 

[call 의 필요성]
- 물론, truffle 에서는 call 을 붙이지 않아도, 값을 가져올 수 있으나 
	1) 다른 사용자들에게 명시적으로 '값을 가져오는 거야' 라고 알려줄 수 있고 
	2) 'truffle 프레임워크를 쓰지 않는, 다른 환경에서', 개발할 때, '호환' 이 잘 될 수 있게 하기 위해, call 을 쓰는 경우가 있다. 

[예문]
	it( "counter 2" , async() => {
		console.log( "counter 2 - call" , await counter.getValue.call() )  // 🔵 작동함
		// console.log( "counter 2 - getValue()" , await counter.getValue() )  // 🔵 작동함
	})

```

\


#### 3) setValue 로 값 변경하고, getValue 로 값 가져오기

```js
// 'setValue' 로 변경(가스비 발생) 하고 -> getValue 로 조회 
it("counter 3", async () => {
	await counter.setValue(20);
	console.log("setValue(20) 설정하고 -> getValue 로 가져오기" , await counter.getValue.call());
});
```

\


### 7️⃣ react hook 으로 증감 버튼 누르면, 상태변수 변경 및 조회 구현

* CA 주소 갖고 있기

```BASH
0x34910676293884d9ca816135cC3a7350c57A1308
```

#### 1) react 기본 설정

```bash
- 기본적인 디렉토리 구조 및 코드 제거

- 이번에는 App 화살표 함수로 우선 시작 
	const App = () => {   }
```

#### 2) hooks, abi 폴더 및 파일 만들기

```
src 폴더에 hooks, abi 폴더 만들기
```

![](https://i.imgur.com/Cp7vWCB.png)

#### 3) 컴파일 완료된 Counter.json 에서 abi 키의 값만 가져와서 붙여넣기

```bash
1) sol 파일로 작성 Counter 파일 작성 
2) 해당 sol 파일을 컴파일 함. 
3) 컴파일 결과가 build 폴더 하위의 Counter.json 으로 나옴 
4) 이 Counter.json 에서 abi 키에 해당하는, [ ] 이 부분만 빼서 가지고 온다. 
```

*   abi 가져오기&#x20;

    <figure><img src="https://i.imgur.com/FtZ9hpp.png" alt=""><figcaption></figcaption></figure>
*   위에서 만든 src > abi 폴더에 붙여넣기&#x20;

    <figure><img src="https://i.imgur.com/p5wuNcr.png" alt=""><figcaption></figcaption></figure>

\


#### 4) hook 코드 작성

```js
import { useState, useEffect } from "react"

import Web3 from 'web3';
    // npm i web3 | ⭐⭐⭐ 이렇게 깔끔하게 써놔도, 된다 

// Web3 custom hook | ✅ custom hook 작성시 use 붙여야 함 
const useWeb3 = () => {

    // 현재 접속한 메타 마스크 지갑 정보를 담을 변수 
    const [user , setUser] = useState({
        account : "", 
        balance : "",
    })
        // 지금 접속된 지갑 정보랑, web3 연결된 것! 을 갖고 있을 것 임. ❓❓❓ 
        // 유저가 접속하면 여기에 저장❓❓❓

    // 네트워크와 연결한 web3 인스턴스를 담을 변수
    const [web3 , setWeb3] = useState(null);

    useEffect( () => {
        // 메타마스크가 설치 되어 있는지 확인하기 | 'window.ethereum' 안에, 네트워크 정보, 지갑들이 들어가 있음 -> so, 메타마스크가 있다면, 빈 값이 아닐 것 -> so, window.ethereum 으로 판별 가능 
            if(window.ethereum){

                // 로그인 
                window.ethereum.request({
                    method : "eth_requestAccounts", 
                })
                    /* - window.ethereum.request 
                            : window.ethereum 객체 안에 있는, request 메소드를 쓰겠다는 말 
                            : 'Ethereum JSON-RPC 메서드' 이제 쓸게~ 라는 의미
                        - method : "eth_requestAccounts" 
                            : 구체적으로, eth_requestAccounts 이름의 메서드를 사용할게 라는 말
                            : '이더리움 네트워크' 안에서, '사용자가 승인'한, '지갑 주소들'이 나옴
                            : EX) 메타마스크에서 '승인' 을 눌러서, 디앱 또는 웹사이트에, 이더리움 지갑 주소 제공을, 허용한 지갑 주소들을 의미
                    */
                .then( async ([walletAddress]) => {
                    /* async ([data]) 중 [data] 해석 
                        '받아온 배열' 중 '첫 번째 요소' 를 data 변수 에 할당
                        data 에는 '지갑 주소' 가 담겨져 있어야 함
                    */

                    // web3 인스턴스 생성
                    const web3Provider = new Web3(window.ethereum);
                        /* [해석] 
                            web3 라이브러리를 통해, 이더리움 네트워크과 통신, 이더리움 네트워크 안에 있는 컨트랙트와 통신하고, 트랜잭션을 생성/전송 할 수 있음. 
                            web3 라이브러리를 연결하는 방법은 여러가지 임 (ganache 로 할 수도 있고, 브라우저 안에 있는 메타마스크로 할 수도 있고)
                            그러면, 지금은 'window.ethereum 안에 있는 메타마스크' 방식으로 web3 라이브러리를 연동 한다는 말 임. 
                            결국, '메타마스크로 연동시킨 web3 라이브러리' 를 통해 '이더리움 네트워크' 와 '통신' 하게 된다. ⭐⭐⭐ 
                        */
                    
                    // set 메소드에 넣어주기 
                    setWeb3(web3Provider);
                        /* 이 순간, 'const web3Provider' 에 머무르지 않고, useState 로 관리하는 이유 
                            1) 컴포넌트 재실행에 따라서 -> 로컬 변수가 초기화 되어서 -> web3Provider 에 할당되는 값이 변경되는 것을 막기 위해
                            2) 추가적으로, 특정 변수의 정보가 변했을 때만, 재렌더링 되게 할 수 있음.
                        */

                    setUser({
                        // 지갑 주소가 담겨 있어야 함    
                        account : walletAddress,    // 지갑 주소는, 'privatekey -> 공개키 -> 20바이트' 의 순서로 만들어지는 것
                        balance : web3Provider.utils.toWei(await web3Provider.eth.getBalance(walletAddress) , "ether")
                            // [해석] web3 라이브러리를 다녀올 때, await 로 기다리게 해야 한다는 것 ⭐⭐⭐
                    });
                })

            } else {
                alert("메타 마스크! 설치! 하! 셈! 😸")
            }
    } , [])

    return {
        user, 
        web3,
    }
}
```

\


#### 5) react app.js 코드 작성

**a) 자주 오류가 발생하는 부분**

```bash
- web3 라이브러리를 hook 을 통해 가져오기 때문에, user 정보 등이 다 도착 할 때 까지 기다리는 처리를 해줘야 함. ex) 조건부 렌더링, loading 상태 등 
```

**b) 코드**

```js
import {useEffect , useState} from 'react';
import useWeb3 from './hooks/web3.hook';
import abi from "./abi/Counter.json"

const App = () => {

  const { user, web3 } = useWeb3();
  const [count , setCount] = useState(0);
  
  // 이더리움 내에 있는 상태변수 값을 가져오기 
    // 1) 상태변수를 조회하는 함수를 인코딩 2) CA 컨트랙트 주소에, 인코딩된 함수 내용을 넣고, 3) 이더리움 내에서 계산해서, 4) 값을 받아옴
  const getCount = () => {

    if(web3 === null) return;
    
    // customHook 잘 들어왔나 확인
    console.log("web3.utils" , web3.utils)  // // hook 에서 만든 값 들어왔나 확인
    console.log("user" , user)    // hook 에서 만든 값 들어왔나 확인

    // '컴파일 된 결과물인 abi' 에서 'getValue' 메소드 가져오기 
    const getValueData = abi.find( (data) => data?.name === "getValue" );
      // 순회하는 요소 객체 name 의 key 가 'getValue' 인지 확인하고 -> 맞으면, return 
        // 그러면, 아래의 내용이 들어감 👇👇 
              /* 
                getValueData = {
                  "inputs": [],
                  "name": "getValue",
                  "outputs": [
                    {
                      "internalType": "uint256",
                      "name": "",
                      "type": "uint256"
                    }
                  ],
                  "stateMutability": "view",
                  "type": "function",
                  "constant": true
                }
              */
                
    // 쓰고 싶은 함수를 인코딩 하기
    const data = web3.eth.abi.encodeFunctionCall(getValueData, []);
      // web3.eth.abi.encodeFunctionCall : 메소드를 인코딩 해서, 이더리움 네트워크에 올린다. 
      // (getValueData, []) : 1) getValueData 이건, 이더리움 네트워크에 올릴 ABI 정보 2) [] 이건, 해당 함수에 필요한 매개변수인데, 이 함수는 매개변수가 필요 없음. 
  
    // 원격 프로시저 호출  😥😥😥 
    web3.eth.call({
      to : "0x3a1904a63b22D74BFaE32Bb982295Bff599BFEDa",  // ✅ npx truffle migrate 로 나오는 ca 값 입력
      data    // 쓰고 싶은 메소드 ✅ 여기로 getValue 함수가 들어감. 
    })  
    .then( (data) => {
      console.log(data)   // 16진수로 나오고 있음. 

      // 16진수 -> 10진수 로 변경
      const result = parseInt(data , 16);   
      console.log(result);

      // 카운트 등록
      setCount(Number(result));
    })
        /* 원격 프로시저를 호출한다는 게 무슨 의미지? | 😥😥😥😥😥😥 이 순간 이해가 100% 안 돼  
          이렇게 호출하면 -> 이더리움 네트워크에서, 이미 배포된 sol 파일 안에 있는 메소드에 접근해서, -> 해당 파일 안에 있는 메소드를 실행시키고 -> 결과값을 반환시킨다.
        */
  }
  // 상태변경 함수 : increment
  const increment = async() => {
    console.log("증가 찍힘")    
    if(user && user.account){
      // abi 에서 name 이 increment 인 메소드를 가져와서 incrementData 에 담기
      const incrementData = abi.find( (data) => data.name === 'increment' );
  
      // abi 객체를, 이더리움 네트워크에 올릴 수 있게, 인코딩 해서 data 변수에 담기 
      const encodedData = web3.eth.abi.encodeFunctionCall(incrementData, []);

      const fromUserAccount = user.account;
      console.log("user.account" , user.account)
      console.log("user.account" , fromUserAccount)
      
      const _data = await web3.eth.sendTransaction({
        from : fromUserAccount, 
        to : "0x3a1904a63b22D74BFaE32Bb982295Bff599BFEDa",  // ✅ npx truffle migrate 로 나오는 ca 값 입력
        data : encodedData    
      })

      console.log(_data)
      getCount()
    }
      /* sendTransaction 이거랑 send 랑 다른 점이 뭐였지❓❓❓  */
      /* 뭘 변경해줘! 라는 말이 안 적혀 있는데, 어떻게 된거지❓❓❓ 
        👉 아, 이건, 이미 solidity 에 적혀 있어서, 이미 반영 되었을 것. 
      */
  }

  // 상태변경 함수 : decrement
  const decrement = async() => {

    console.log("감소 찍힘")
    
    if(user && user.account){
      // abi 에서 name 이 increment 인 메소드를 가져와서 incrementData 에 담기
      const decrementData = abi.find( (data) => data.name === 'decrement' );
  
      // abi 객체를, 이더리움 네트워크에 올릴 수 있게, 인코딩 해서 data 변수에 담기 
      const encodedData = web3.eth.abi.encodeFunctionCall(decrementData, []);

      const fromUserAccount = user.account;
      console.log("user.account" , user.account)
      console.log("user.account" , fromUserAccount)
      
      const _data = await web3.eth.sendTransaction({
        from : fromUserAccount, 
        to : "0x3a1904a63b22D74BFaE32Bb982295Bff599BFEDa",  // ✅ npx truffle migrate 로 나오는 ca 값 입력
        data : encodedData    
      })

      console.log(_data)
      getCount()
    }
  }

  useEffect( () => {
    // 최초값 조회
    if( web3 !== null ) getCount(); 
  } , [web3]);

  return (
    <>
      {/* 현재 메타마스크에 로그인한 유저의 지갑 주소 */}
      <p> 지갑 주소 : {user.account} </p>   

      <p> 카운트 : {count} </p>
      <button onClick={increment} > 증가 </button>
      <button onClick={decrement} > 감소 </button>
    </>
  )
}
export default App;
```

### 8️⃣ 실행해보기 | #checksheet

```bash
# npx truffle migrate -> CA 값 기억하기
0x469FbC41736952356A81f6B9F9d65248204CE2E3

# 컴파일 
npx truffle compile 

# 컴파일 된 코드를 src>abi 에 옮기기 
- 옮기기

# 가나쉬 실행
npx ganache-cli

# 이더리움 네트워크 배포
npx truffle migrate


```

### 🤯 디버깅

#### 문제 상황

```
account 를 넣으면 작동하는데 
contract address 를 넣으면 작동을 안 해 😥😥😥 
```

![](https://i.imgur.com/Jnewql2.png)

**천천히 처리 과정 따라가 보기 : step by step 읽어보기**

* user.account 까지는 찍힘 ![](https://i.imgur.com/89LwjzY.png)
* 아... 이 부분을 data 에 넘겨주는 값을, encodedData 변수로 변경했는데, 그러려면, data key 를 유지하고 있었어야 했음 ⭐⭐⭐ ![](https://i.imgur.com/hLev6HQ.png)

### 🔮 궁금증

```
- 현재 네트워크에 있는 계정을 찍어보니까, 내가 하지 않은 계정들 까지 나옴. 
- 이건, 예전에 했던 계정을 이곳에 그냥, 놓아둔거야? 
```

![](https://i.imgur.com/a2FK45d.png)

\


```bash
npx truffle migrate 하면, 아래 정보들이 나오는데 
account 랑 
contract address 가 어떤 차이? 

- account : 지갑 주소 (개인키 -> 공개키 -> 일정 자리만 뽑아서 만든 주소)
- contract address : 해당 '컨트랙트'가, 이더리움 네트워크에 '배포'될 때, '생성되는 주소'  
```

![](https://i.imgur.com/ksW6cA3.png)
