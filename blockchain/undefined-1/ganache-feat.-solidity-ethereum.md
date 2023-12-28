# ganache 환경에서 컨트랙트 배포하기 (feat. solidity, ethereum)

## 1. 이더리움 개발에 필요한 개념

```bash

[ block ]  
	- block 기능 : 트랜잭션 및 데이터를 모아서, 하나의 블록에 저장한다. 

[ account ] 
	- 구분
		- account 에는 2 종류가 있음. EOA와 CA 가 있음. 

	  - EOA 특징 
			  - EOA 는 '개인키' 가 있는 계정.
				- 토이 비트코니에서 했던 '개인키' 가 있는 계정 (#부족📛📛)
				- '개인키' 가 존재하는 '지갑' 임 

		- CA 특징
				- '컨트랙트 코드' 에 의해 '제어'되는 '스마트 컨트랙트' address(=account)
				- '컨트랙트 코드'가 있고, '코드에 의해 제어' 되는 'address(account)' (#코드에 의해 제어된다는게 아직 부족❓❓)
				- 'privateKey(개인키)' 가 '없음'. 
				- 4가지 전부 다 가지고 있음. 

CF. account 는 이더리움이 생기고 나서 유명해지기 시작


[ Message 와 transaction ]
	- 구분
		- 메시지의 수신자가 ether를 보낼 때, '서명'  을 '있으면', -> 'transaction'
		- 메시지의 수신자가 ether를 보낼 때, '서명'  이 '없으면' ->  'message'
	
	- 특징
		- 무조건 'EOA 계정' 에서 생성된다. CA 에서는 보내지 못 한다. (#부족📛📛) 
		- 'EOA' 가 보내고 -> 다시 CA 가 보낸다❓❓ (#부족📛📛)


[ ETH ]
	- 의의 
		- 이더리움 네트워크에서 사용하는 암포화폐의 기본 단위 
		- 본래 '트랜잭션 발생' 했을 때 사용하는 자원임.  (#부족📛📛, 좀 더 맥락 파악 필요)
		- 현재는 '코인' 으로 사용되는 경향이 있음. 

```

\


## 2. 이더리움 개발에 사용되는 툴

### 1) geth

```bash
[특징]
	- 'go 언어로 작성' 되어 있는 이더리움 클라이어트 
	- 스마트 컨트랙트 배포와 실행까지 할 수 있음. 
	- chainID(블록체인 고유 식별자) 를 확인해서, 테스트넷 인지, 메인넷 인지 확인 가능
	- '테스트 넷 구축' 을 위해서 사용
	- 23년 9월 현재, 이더리움이 'POS'으로 전환 -> geth 로 테스트하는 것에는 오류가 생김
```

### 2) Ganache

```bash
[특징]
- '로컬 개발 환경 및 테스트 환경' 을 제공
- '이더리움 네트워크'
- 사용 예 : 테스트 네트워크 보다, 가나쉬에 올려서 테스트 할 수 있게 하는 라이브러리
```

### 3) 파운드리

```bash
[특징]
- '솔리디티로 TDD' 가능
```

### 4) 메타마스크

```bash
[특징]
- '브라우저에서 사용' 하는 확장 프로그램 
- '이더리움 스마트 컨트랙트' 와 '상호작용' 하기 위해 사용 (#📛📛부족)
- '개인키' 를 직접 전달하지 않아도, 안전하게 사용 가능 (#📛📛이해부족)
```

### 5) truffle, hardhat

```bash
[특징]
- '스마트 컨트랙트 개발'
- '테스트 배포' 를 쉽게 할 수 있도록 도와주는 프레임워크 

CF. '프레임 워크' = 그 안에, '폴더' 가 구성되어 있다. 
```

## 3. RPC (Remote Procedure Call)

### 프로시저(procedure) 란?

```bash
[ 의의 ]
	- (영어) '절차' 라는 의미
	- (코드) '특정 작업' 을 수행하기 위해 정의된, 코드블록 (by GPT)
		- 쉽게 말하면, '어떤 작업을 수행하는 코드'

[특징]
	- '함수와 비교' 하면, 프로시저 만의 특징이 나타남. 

[함수와 비교해서 알게되는 프로시저의 특징]
	- 함수의 특징 
		- 함수는 '입력' 에 따른 '출력' 이 '무조건' 있어야 한다. 왜냐면, 애초에 '출력 발생을 목표' 로 하기 ㄸ문에.  | ex) 잘못된 것을 입력해도, 'undefined' 가 나오는 것 처럼 
		- '클라이언트' 에서 처리 한다. 

	- 이에 비해 프로시저는 
		- '반환값이 꼭 있어야 하는 건 아님'
			- 즉, 어떤 코드를 실행해서, 기능을 하는 건 동일한데, 반환값이 필수는 아님. 
			- 함수가 'input - process - output' 의 과정을 거칠 때, process 에 집중함. 
		- 그래서, 출력보다 '절차' 가 중요함. 
		- '서버' 에서 주로 처리함


(🤯 그러면, 이러한 procedure 를 remote 에서 call 한다는 건 뭐지? )


[ RPC 란? ]
	- remote procedure call(원격 프로시저 호출) 의 약자 
	- '별도의 원격 제어' 를 위한 코딩을 '하지 않고' , '다른 주소 공간' 에 있는 '프로시저를 실행' 할 수 있게 하는 '프로세스 간 통신'


	- 일반적으로, '코드를 작성' 하고, -> '프로그램을 실행' 시키면, -> 함수는 '자신의 메모리 공간' 에서 기능한다. 
	
	- RPC 의 경우, 내 컴퓨터에 있는 함수(기능) 을 실행하지 않는다. 
	- RPC 의 경우, '다른 주소' 에 있는, '기능(함수, 프로시저)' 를 실행시키고 싶어서 사용한다. 
		- 그러면, '다른 곳에 있는 기능' 을 사용한다면, '원격 함수 호출' 같은 것도 있겠네 ❓❓ 
			👉 그렇다고 함. 
	- 그래서, '다른 주소에 있는 메모리' 를 사용해서, '다른 메모리 공간에서 동작하는 프로시저' 를 실행할 수 있게 해준다. 


[ RPC 사용의 장점 ]
	- 이미 만들어져 있는 함수를 사용 
	- 따라서, '기능' 은 '원격으로 실행' 하고 '비즈니스 로직 개발' 에 집중할 수 있음.


(👏 그러면, 이제 앞으로, RPC 를 통해, 이더리움 네트워크에 보내게 된다. 
혹은, 이더리움 자체가 갖고 있는 프로시저를 실행시켜서, 뭔가를 하게 된다. )

```

## 4. cli 환경에서 ganache 이용해서 잔액 확인 해보기

### 1) ganache 설치 및 열기

```bash
# ganache 설치 
npm i -g ganache-cli

# ganache 실행
npx ganache-cli
	# 127.0.0.1:8545 포트에서 열리고, 
	# gas price, gas limit, available accounts, private key 등이 나옴 
```

### 2) cli 환경에서, '현재 client 의 version 확인' 해보기

1. JSON 객체 준비

```json
{
    "jsonrpc" : "2.0",   // json-RPC 버전이 2버전임!
    "method" : "web3_clientVersion",    // 실행시킬 메서드 이름
    "params" : [],      // 메서드에 전달할 인자값(매개변수)
}
```

2. 현재 ganache 가 cli 에 켜져 있는 상태에서, 새로운 cli 를 열고 아래를 실행

```bash
curl -X POST -d '{ "jsonrpc" : "2.0" , "method" : "web3_clientVersion"  , "params" : []}' http://127.0.0.1:8545

	# 코드 해석 👇 
	curl : 커맨드 라인에서, url 로 데이터를 전송하거나 가져오는 도구
	-X POST : url 로 보낼 때, 'POST 요청' 을 한다
	"jsonrpc" : "2.0" : 현재 사용하고 있는 rpc 버전이 2.0 임.  
	"method" : "web3_clientVersion" : 호출하려고 하는 메서드의 이름 | web3 하위 요소인  clientVersion 에 접근할 때 '__' 언더바를 이용 ⭐⭐ 
					: "clientVersion" 이건, '클라이어트 버전 알려줘' 라는 의미 ⭐⭐ 
	"params" : [] : 해당 메서드에 어떤 파라미터를 전달할거야? 라고 했을 때, 전달할 것이 없으니까 빈 배열
	http://127.0.0.1:8545 : 로컬 컴퓨터 8545 포트 에서 실행되고 있는 Ethererum RPC 서버(로컬환경에서 열려있는 가나쉬) 에 보냄 

	# 반환되는 값 
	{"jsonrpc":"2.0","result":"EthereumJS TestRPC/v2.13.2/ethereum-js"}
```

### 3) cli 환경에서, '지갑에 있는 잔액' 확인해보기

1. npx ganache-cli 실행해서 -> account 정보 가져오기

```bash
# 터미널에서 가나쉬 실행
npx ganache-cli

# 실행결과 👇👇 가 터미널에 찍힘. 
	# 찍힌 것 중에서 계정 정보를 하나 가져온다. 
	Available Accounts
	==================
	(0) 0xDAe8BDe972Da49b763Ff4976CE510FbAa26Ace73 (100 ETH)
	(1) 0x89685A253fc6f6692f8181976474dBc1eFF09BAC (100 ETH)
	(2) 0xdbfec707FD3f09B12C428b6A553bA1c8e8503700 (100 ETH)
	(3) 0x93aF901470Fd587b514856fD07ce2D931c3e788E (100 ETH)
	(4) 0x7017aB3D4aA951b4d161d037Eff8dc64ABD753C3 (100 ETH)
	(5) 0x0e9906904B2642a02C5aC7B6d38A2173cA81561c (100 ETH)
	(6) 0x4224Fc79f21ab33ac3940c0EfC414191c822Ce9D (100 ETH)
	(7) 0x23d48Df82CF1e7f1EBEc0C0b28Eb45f7219b49dC (100 ETH)
	(8) 0xc7ad46135288C8ed2A007dDA388c02b412E625B5 (100 ETH)
```

2. 가져온 계정에 있는 잔액을 확인

```bash
# ⭐⭐ 반드시 가나쉬를 실행한 터미널과 다른 터미널을 연다 

# 계정 잔액 가져오는 명령어 실행
curl -X POST -d '{"jsonrpc" : "2.0", "method" : "eth_getBalance", "params" : ["여기에 accounts 중 하나를 기록!"]}' http://127.0.0.1:8545
	ex) curl -X POST -d '{"jsonrpc" : "2.0", "method" : "eth_getBalance", "params" : ["0xDAe8BDe972Da49b763Ff4976CE510FbAa26Ace73"]}' http://127.0.0.1:8545

	    # [파라미터 해석]
	        # params : 조회할 계정의 주소!
	        # 지값에 있는 잔액 확인
	
	    # [메소드]
	        # 사용하는 메소드 : eth.getBalance(매개변수)
	        # web3 라이브러리를 사용하면, eth.getBalance() 메소드에 . 으로 접근함.
	        # 다만, RPC 로 호출하면, _ 로 접근

# 터미널에 찍히는 결과 
	{"jsonrpc":"2.0","result":"0x56bc75e2d63100000"}
		# [결과 해석]
	        # {"jsonrpc":"2.0" : jsonrpc 2.0 버전으로 rpc 통신 했다.  
	        # 0x56bc75e2d63100000 : 0x 16진수 | 이게 잔액임 | 단위는 웨이(Wei) ⭐⭐


(그러면, wei 라는 단위는 뭘 말하는 걸까❓❓❓)

```

3. 단위 변환

```bash
- web3 라이브러리 에서는 getBalance 를 호출 -> 10진수로 변환해서 변환값을 준다. 

- 하지만, 현재의 환경에서 반환되는 지갑의 금액 단위는 'wei' 임. 
	- 현재의 환경은 RPC 인가?❓❓❓  👉 응 RPC 임 
	- RPC 를 통해 이더리운 네트워크와 통신하면서 테스트하는 환경? ❓❓❓ 👉 응 RPC 를 통해 이더리움 네트워크 안에 있는 메서드를 씀 
	- WEB3 라이브러리는? ❓❓❓ 👉 이 라이브러리를 써서 이더리움 안에 있는 프로시저를 갖다 쓰는 것. 

- 'wei' 를 'ether' 단위로 변경하려면 
	- '1eth === 100 x 10e18 wei' 
	- 또는, 'wei = 10**18' 으로 변환하면 됨 
```

## 5. 가스비 관련 용어 정리

```bash
[주유소]
	- 리터당 가격 : 2000원 
	- 총 넣고 싶은 가스량 : 20리터 
	- 총 발생한 주유비 : 20리터 x 2000원 = 40,000원

[이더리움]
	- 가스비당 가격('gasPrice') : 2000원 
	- 총 쓰고 싶은 가스량('gas') : 20gas
	- 총 발생한 가스비 : 20 x 2000원 = 40,000원 

	- 가스비 계산 : 보통, byte 당 5gas 
```

\


## 6. cli 에서 RPC 를 통해 트랜잭션 보내기

```bash
[이 과정의 의의]
- CLI 를 통해서 , RPC 호출을 통해, 이더리움 네트워크에 있는 프로시저를 호출해서, 사용하는 것 임. 
- WEB3 와의 차이점은, WEB3 라는 라이브러리를 쓰는지 여부만 다를 뿐, 이더리움 네트워크 안에 있는 걸 가져와 쓴다는 점은 동일.


[필요한 내용]
- 받는 사람 
- 보내는 사람 
- 잔액


[1st 터미널에서 npx ganache-cli 를 실행시켜서, '받는 사람' 과 '보내는 사람' 의 '계정 정보' 가져오기]
	Available Accounts
	==================
	(0) 0xDAe8BDe972Da49b763Ff4976CE510FbAa26Ace73 (100 ETH)
	(1) 0x89685A253fc6f6692f8181976474dBc1eFF09BAC (100 ETH)
	(2) 0xdbfec707FD3f09B12C428b6A553bA1c8e8503700 (100 ETH)
	(3) 0x93aF901470Fd587b514856fD07ce2D931c3e788E (100 ETH)


[두 번째 터미널 열고, 아래 sendTracsaction 코드 작성 및 실행]
curl -X POST -d '{"jsonrpc" : "2.0", "method" : "eth_sendTransaction","params": [{ "from" : "0xdFCA7c620659842598eC6b4BB0c790d67ffAdef4","to" : "0x38Ed646e8F7d5CFA9557eABDfB90f3041B747eC5","value" : "6677667"}]}' http://127.0.0.1:8545


# [터미널에 찍히는 결과물]
	#{"jsonrpc":"2.0","result":"0x452666962b1c3bbaa385fde2c31e0291f4b8d1deb183b06835cd45563246bc8b"}



[실제로, 해당 계정에 돈이 들어갔나 확인] #위에 적은 잔액확인 명령어로!
curl -X POST -d '{"jsonrpc" : "2.0", "method" : "eth_getBalance", "params" : ["0x38Ed646e8F7d5CFA9557eABDfB90f3041B747eC5"]}' http://127.0.0.1:8545

	# 결과물 : 그러면 아래와 같이 찍힘 👇👇👇 
	#{"jsonrpc":"2.0","result":"0x56bc75e2d6375e4a3"}
```

\
\


## 7. web3 라이브러리를 통해, 간단한 컨트랙트 배포 해보기

### web 3.0 라이브러리

```bash
[ web 3.0 라이브러리 특징 ]
- https://web3js.readthedocs.io/en/v1.10.0/

- javascript 라이브러리 임. 

- 'RPC 통신'을 편하게 사용할 수 있도록 이더리움 재단에서 지원

- 'node' 에서 요청을 보내면, '지정된 API' 에 따라, '이더리움, 블록체인'  과 상호작용할 수 있도록 만든 라이브러리 ('웹 어플리케이션' 개발시 사용)
	ex) getBalance 같은 메서드를 호출하면, 계정의 잔액을 받아볼 수 있음. 

- ether.js 도 web 3.0 에 포함되어 있음. 
	
❓ 이것도 RPC 통신인가? ❓❓❓ 👉 그러함. 이것도 RPC 임 
❓ RPC 통신은, CLI 로만 하는건가? 👉 CLI 로만 하지는 않음. 
```

### 컨트랙트 배포 해보기

* 진행되는 process 이해해보기

```BASH
1. [소스코드 작성] 소스 코드를 'solidity' 로 작성

2. [컴파일] '솔리디티 파일' 을 'EVM(이더리움 가상 머신)' 이 이해할 수 있도록 '바이트 코드' 로 '컴파일' 한다. ⭐⭐⭐ 
	👉 이러한 이유 때문에, 버전을 잘 맞춰야 하는 건가❓❓❓ 

3. [트랜잭션 생성 및 전송]'변환한 바이트 코드' 내용을 포함한 '트랜잭션을 생성' 하고, '이더리움 네트워크에 전송' 

4. [네트워크에서 벌어지는 일]
	1) 보내진 트랜잭션은 '트랜잭션 풀' 에 담긴다. 
	2) 'ganache' 를 사용하게 되면, '트랜잭션이 들어오는 순간', ⭐⭐곧 바로 '채굴'되고 👉 곧바로 '블록' 에 담긴다. 👉 그리고 데이터로 저장된다. ⭐⭐ 
		- 따라서, 별도로, 마이닝을 할 필요가 없다. 
	3) 그 다음 '스마트 컨트랙트를 이더리움 네트워크에 배포' 된다. 👉 그러면, '다른 계정들' 이 '해당 스마트 컨트랙트' 와  '상호작용' 이 가능해진다. 


❓ ganache 를 사용하지 않으면 어떤 일이 벌어지지? 
❓ ganache 를 사용하지 않으면, 무엇을 또 해줘야 하지? 
❓ ganache 는 어떤 역할을 하지? 

```

#### 솔리디티 기본 구조

* 구조 이해해보기

```bash
- 사전 셋팅 
	- solidity 익스텐션 설치 하고, 작성 시작
	- 반드시 ; 를 넣어줘야 한다. 

1. 라이센스 식별자 
    - 솔리디티를 사용할 수 있게 해주는 것에 대해서 라이센스 식별자를 추가
    - 주석 내용을 라이센스 식별자로 인식

2. 솔리디티 버전

3. 배포할 컨트랙트에 대한 내용
    - 컴파일러 버전이 업데이트 된다고 했을 때, 깨질 수 있는데, 여기에 버전을 명시해서, 컴파일 할 수 있게 
```

#### solidity 소스 코드 작성

* 예시 코드

```js
// SPDX-License-Identifier:MIT  
		// ☝ 솔리디티 식별자 | 이건 지우고 작성해야 함 

pragma solidity ^0.8.0; 
	// ⭐ '솔리디티 컴파일러' 버전이 0.8.0 이상! 을 의미 
    // ☝ 이 버전에 맞춰서 solc 를 설치해줘야 함 | $ npm i solc@0.8.0

	CF. ^ 의 의미 
	'해당 버전 이상' 부터 '그 다음 메이저 버전 미만!' 까지를 의미한다.
	따라서, pragma solidity ^0.8.0; 를 작성하면, 솔리디티 컴파일러 버전은 0.8.0 부터 0.9.0 미만! 까지를 커버하게 된다.
    // 컴파일러 버전이 업데이트 된다고 했을 때, 깨질 수 있는데, 여기에 버전을 명시해서, 컴파일 할 수 있게 한다. 


// 스마트 컨트랙트 내용 선언
contract Counter {
    uint256 value;

    constructor(){}

    // setter 
    function setValue(uint256 _value) public {
        // value 의 상태가 변경된다. -> 그러면, '가스비' 가 발생한다. 
        value = _value;
    }

    // getter
    function getValue() public view returns(uint256){
        // view : 조회하기 위한 코드를 작성 할 때 📛📛📛 
        // returns(uint256) : 함수의 반환 타입 

        return value;
    }
}

```

#### solidity를 EVM 이 볼 수 있는 바이트 코드로 '컴파일' 하기

```bash
# solc-js 라이브러리 설치 
	npm i solc@0.8.0
		# 컴파일 할 때, 버전 차이가 나기 때문에, 그냥 solc 로 설치하면 에러남 
		# 즉, 'test.sol'에서 pragma solidity ^0.8.0; 로 설치해 줬기 때문 때문에, solc 를 0.8.0 으로 설치 해야 함

# 실제로 '컴파일 된 파일' 만들기
npx solc --bin --abi ./컴파일 시키고 싶은 파일이름.sol
👉 ex) npx solc --bin --abi ./test.sol

	# --bin : 컴파일 완료된 바이트 코드 
	# abi : 컴파일된 코드가 어떻게 구성되어 있는지에 대한 설명 | 인터페이스가 작성된 파일


- 그러면, solc 랑 위에 있는 라이브러리랑 무슨 차이 인거야❓❓❓ 
	- solc 는 실제로 설치
	- pragma 는 어떤 버전을 설치해야 컴파일 할 수 있는지를 알려줌 
```

#### web3 라이브러리로, 솔리디티로 작성한 코드를 스마트 컨트랙트로 배포

❓❓❓ 배포한다는게 뭐지

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- web 3 라이브러리 -->
    <script src="https://cdn.jsdelivr.net/npm/web3@1.10.0/dist/web3.min.js"></script>
</head>
<body>
</body>
<script>

    // web3 라이브러리 8545 port 로 연결
    const web3 = new Web3("http://127.0.0.1:8545")

    // 계정에 있는 잔액 확인 
    web3.eth.getBalance("0xdFCA7c620659842598eC6b4BB0c790d67ffAdef4").then(( data ) => {
        // 0xdFCA7c620659842598eC6b4BB0c790d67ffAdef4 
            // 이건, ganache 실행을 통해 얻은 account
        
        // 잔액확인 | wei 단위
        console.log(data)

        // ether 단위로 변경해서 보기 
        const value = web3.utils.fromWei(data)
        console.log("from Wei to ether" , value)
    })

    // CA 생성 
        // 솔리디티로 작성한 코드를 스마트 컨트랙트에 배포 
    web3.eth.sendTransaction({
        from : "0xdFCA7c620659842598eC6b4BB0c790d67ffAdef4" , 
        gas : "700000" , 

        // 컴파일 완료된 bin 파일 넣기 👇👇👇
        data : "54986131684651651 막 이렇게 숫자로 되어 있음"
    }).then(console.log)

    /* 
        ✅ 여기에 있는 contractAdress 가 CA 임. 

        - 정상적으로 배포되면 이러한 값이 찍힘 👇👇 
        {
            blockHash : ""
            blockNumber : "" 
            contractAddress : "0xe15ABa86eB02054b909d95d5e65fC3647b0e7b3E" | 이게 CA 임 ⭐⭐ 
            cumulativeGasUsed : "" 등등
        }
    */
</script>
</html>
```

## 🤯 궁금증

### `pragma solidity ^0.8.0;` vs `npm i solc@0.8.0` 의 차이

```bash
- pragma 는 솔리디티 컴파일 할 때, 이 버전으로 해~ 라고 알려주는 역할 

- solc 는 실제로, 해당 버전을 찾아서, 설치하는 역할 

```
