# 설치, 환경 설정 및 보일러 플레이트

## 1. 설치, 환경 설정 및 보일러 플레이트

#### latest 가 14 버전인 상황에서, 13 버전으로 설치하기 #📛📛 아직 css, tailwind 는 안 함

```bash
# next 기본 설치 | 이때, 14 버전이 설치 되긴 함 
npx create-next-app
npx create-next-app <앱이름>

# 버전을 명시하면 -> 해당 버전으로 설치 됨 ⭐⭐⭐⭐⭐ 
npm install next@13


# [참고] 리액트 버전을 17로 설치해야할 경우
npm install react@17
npm install react-dom@17

```

#### node : 버전 16 으로 설치하기 #📛📛 | 아직 설치 안 함 | wsl 을 사용해야?

* 출처 : https://chat.openai.com/share/b3df5963-b13b-4a6c-9b00-1fb1febd25f4

```bash
# 노드 버전 확인 
node -v

# nvm 설치 
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# 터미널에서 16 버전 설치
nvm install 16.0.0
nvm use 16.0.0
```

![](https://i.imgur.com/5xWNyec.png)

#### 디렉토리 구조 #⭐⭐⭐

* 참고 자료
  * 조교님 깃헙 : 이건 버전 1 리소스에 있음
  * 교수님 깃헙 : 이건 팀 노션에서 문서에 있는거 같음 or 문서 or 회의 한 페이지
  * 그때 받은 사진 자료 : 이건, 노션에, 스프린트에 있음0
* 서버 컴포넌트를 빼면, 옆에 자리가 남아있게 된다.
* 현재 내가 하고 있는 것 ![](https://i.imgur.com/qQklhE4.png)
* 디렉토리 구조
  * 참고할 것 : https://github.com/vercel/nextjs-postgres-nextauth-tailwindcss-template/blob/main/app/api/auth/%5B...nextauth%5D/route.ts
  * 프론트 디렉토리 : https://www.notion.so/95b8e6c2d33d47f9bb80928e477b5cc6?v=16b5166a14af43cda095c2c8cd865822\&p=c45646b7961b4695a4f7a203efa6792e\&pm=s

```bash

📦my-app  

┣ 📂docs   // 프로젝트 진행할 때 참고할 것들 우선, 넣기

┣ 📂.next

┣ 📂public   // 사진, 아이콘 같은 정적 자원 가져오기 

┣ 📂assets   // 폰트 파일 넣기
┃ ┃ ┣ 📂fonts   // 폰트 

┣ 📂server  
┃ ┃ ┣ 📂app  
	
┃ ┃ ┃ ┣ 📂_component   // 공통사용 컴포넌트
┃ ┃ ┃ ┃ ┃ ┃ ┣ 📜Create_any_Btn.js  // create 할 때, 등록페이지로 넘어가게 하는 버튼

┃ ┃ ┃ ┣ 📂_utils   // 공통 사용 함수
┃ ┃ ┃ ┃ ┃ ┃ ┣ 📜getNoticesList.js  // get 데이터 가져오는 것들. 비슷한 유형들. 복사해서사용 가능. 요청 URL 만 다름 
┃ ┃ ┃ ┃ ┃ ┃ ┣ 📜getSubscriptionsList.js

┃ ┃ ┃ ┣ 📂admin    // 어드민 페이지 
┃ ┃ ┃ ┃ ┣ 📂create  
┃ ┃ ┃ ┃ ┃ ┣ 📂blacklist  
┃ ┃ ┃ ┃ ┃ ┃ ┣ 📜page.js  
┃ ┃ ┃ ┃ ┃ ┗ 📂notices  
┃ ┃ ┃ ┃ ┃ ┃ ┣ 📜page.js  
┃ ┃ ┃ ┃ ┗ 📂main  
┃ ┃ ┃ ┃ ┃ ┣ 📜page.js  

┃ ┃ ┃ ┣ 📜page.js    // 기본 page.js
┃ ┃ ┃ ┣ 📜layout.js  // 기본 layout.js

```

* 고민 되는 부분

```
혹시, 서버 컴포넌트에서, 클라이언트 컴포넌트로 빼면, 어디에 놔야 할까❓❓❓ 
```

#### 뼈대 코드 (base code)

* layout 부터 만들게 되려나? 이건 음... 아직 잘 모르겠네

#### 환경변수 env 설정

* 참고 : \[\[231104\_env\_환경변수\_생활코딩]]
