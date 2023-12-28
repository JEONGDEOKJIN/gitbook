# 1.설치와 실행

## 1.설치와 실행

#### latest 가 14 버전인 상황에서, 13 버전으로 설치하기

```bash
# next 기본 설치 | 이때, 14 버전이 설치 되긴 함 
- npx create-next-app
- npx create-next-app <앱이름>


# 버전을 명시하면 -> 해당 버전으로 설치 됨
npm install next@13


# [참고] 리액트 버전을 17로 설치해야할 경우
npm install react@17
npm install react-dom@17

```

#### 최신버전으로 설치 (출처 : 생활코딩) | 현재는 next.js 14 버전이 나온 상황 자동으로 14버전으로 설치 됨 |

```bash
# node 설치가 되어 있어야 함 

# next.js 설치 @터미널 
npx create-next-app@latest .
	# . : 현재 폴더에 만들겠다 
	# latest : 최신 버전을 설치하겠다.

	# 주의 사항 
		📛 최신버전이 아닌, 13.4 버전을 설치 해야 함 


# 설치 환경 
	- next 13 부터 app router 가 적용됨 -> so, app router 적용이 필수!!! 

# 실행
	npm run dev

```

* 이번 튜토리얼 설치 환경

```
- 다음 튜토리얼로이제, tailwind 랑 typescript 를 디벨롭 시켜 나가자. 
```

![](https://i.imgur.com/YXAe2Cu.png)

*

![](https://i.imgur.com/OnW4pj9.png)

\
