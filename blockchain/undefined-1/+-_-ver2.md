# 업그레이드된 트퍼플 배포 + 야구 게임\_트러플ver2

* 그림 이해 👇👇 \[\[2023-10-06 16.55.55\_\_231005\_\_업그레이드된 트퍼플 배포 + 야구 게임.excalidraw]]

\


## 📌 TOC

1. 🚀 checkSheet
2. 💭 분석
   1. 전체 사용자 흐름
      1. (이것만 보고, DFD 를 짜보려 했는데 ) ㅠㅠ
      2. Baseball.sol 분석
3. 🌴 DEV

## 🚀 checkSheet

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

# 교수님 받은 파일 다시 실행시켜 보려면 
1) 가나쉬 실행 👉 여기에서 받은 privateKey 로 메타마스크 연결 
2) react 실행 
3) npx truffle migrate 하고 -> app2.js 에서 ca 넣어줘야 하는 부분에 넣어주기 
```

\


## 💭 분석

* 해야 하는 것

```
1. 게임 끝나면, 자동적으로, 다시 시작되는 거 
2. 어드민 계정으로 들어가야만 보이게 하기 
```

### 전체 사용자 흐름

![](https://i.imgur.com/a3GdXz0.png)

#### (이것만 보고, DFD 를 짜보려 했는데 ) ㅠㅠ

```
- 😥😥😥 어떤 그림을 보고, 이 코드의 로직을 잘 이해할 수 있었으면 좋겠음 
```

![](https://i.imgur.com/IjsGeFf.png)

\


#### Baseball.sol 분석

```bash
[포인트]
- 소유자의 balance에 전송

- 클래스 분석 하는 것 처럼 하면 되려나? 
	- 

```

## 🌴 DEV

* 개발 과정

```bash
# 기본 설치 
	# react 설치하고 뜨는거 봐야 함 
	npx create-react-app dj_test
	
	# 해당 폴더로 이동 ⭐⭐
	cd test

	# 기본 설치 : truffle, web3, ganache
	npm i truffle web3 ganache-cli

	# truffle 환경 설정 👉 3개 폴더(contracts, migrations, test) 만들기 
	npx truffle init


#  '@contracts' 폴더에, 'Baseball.sol' 파일에, 소스 코드 작성 하기 
	



# src 에 1) abi 랑, 2) 컴파일된 파일을 가져올 수 있는 이름 변경 작업 같은거 해줘야 함 

# 컴파일 해야 함 
npx truffle compile 

# 컴파일 한 내용을 abi 만 따서 옮겨야 해 

# 이더리움 네트워크에 migrate(deploy)
truffle 을 써서, npx truffle migrate 로  이더리움에 배포 

```
