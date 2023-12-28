# 230909 ts-node 를 실행할 때, npx 를 사용하는 이유

***

createDate : 2023-09-07

publish : 벨로그 blogTitle : reduce 메소드 publishURL : postDate :

tags : #javascript, #1차작성,

***

***

createDate : 2023-09-07

isPublished : publish : blogTitle : publishURL : postDate :

tags : #옵시디언, #obsidian

***

https://chat.openai.com/share/d1a3b3a8-06f1-4c3e-815e-18cf3aa6470a

* ts-node 는 typescript 를 js 로 컴파일 하지 않아도, 실행할 수 있게 해주는 npm 패키지 임.

\


* 현재 프로젝트에서는 보통, '로컬(특정한 디렉토리)' 에 ts-node 를 설치하고 있음

```bash
예를 들어, 04_p2p 디렉토리에 접근해서, 거기에서 npm i @types/ts-node ts-node 이렇게 설치하고 있음.
```

\


* 설치된 ts-node 는 'node\_modules/.bin' 경로에 바이너리가 위치하게 됨 -> 따라서, 정식으로 실행하려면 아래와 같이 입력해야 함

```
./node_modules/.bin/ts-node index.ts
```

\


* 다만, 오류 발생 가능성이 높음 -> 이에, npx 접두어를 사용해서, 굳이 node\_modules/.bin 경로 까지 가지 않고, node\_modules/.bin 하위에 설치된 binary 를 사용할 수 있게 함 -> 따라서, 아래와 같이 입력하면, ts-node 를 사용해서, ts 파일을 실행할 수 있음.

```
npx ts-node index.ts
```
