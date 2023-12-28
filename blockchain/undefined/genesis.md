# 최초의 제네시스 블록 (GENESIS)

Document

```html
<h1> 나는 최초 블록임 해해해해해해 </h1>

<h1> 초기의 제네시스 블록은, 하드 코딩, 으로 직접 만들어준다. </h1>


<div>

    <div id="block"> 
        <label for=""> 블록의 버전(블록의 버전 초기값) </label>
        <h3 id="version">  </h3>

        <label for=""> 블록 높이 (제네시스,  최초의 블록으로, 아직 높이가 첫 단계, so, 높이는 0번) </label>
        <h3 id="height" >  </h3>

        <label for=""> 블록의 생성 시간(제네시스 블록 생성 시간) </label>
        <h3 id="timestamp" >  </h3>

        <label for=""> 이전 블록의 해시. (최초의 블록이므로, 이전 블록 해시를 0으로 대체)</label>
        <h3 id="previousHash" >   </h3>

        
        <label for=""> 블록 해시 (최초 블록 이므로 해시 0 으로 대체 )</label>
        <h3 id="hash" >  </h3>
        
        <label for=""> 머클 루트 (최초의 블록 이므로, 해시 0 으로 대체) </label>
        <h3 id="merkleRoot" >   </h3>


        <label for=""> 논스(블록을 생성하는 과정에서, 연산을 몇번 진행했는지) </label>
        <h3 id="nonce" >    </h3>

        <label for=""> 난이도(블록의 생성 주기를 조절하기 위한 값) </label>
        <h3 id="difficulty" ></h3>

        <label for="">블록의 내용(실제 비트코인 최초 블록의 내용)</label>
        <h3 id="data">  </h3>
        

    </div>

</div>

<script>
    // 초기 제네시스 블록

    // 하드 코딩 

    const blockData = {
        version : "1.0.0", 
        height : 0,
        timestamp : Date.now(), 
        previousHash : "0".repeat(64),   // 64 자리 반복.|  0을 64번 반복!
        hash : "0".repeat(64),
        merkleRoot : "0".repeat(64),
        nonce : 0, 
        difficulty : 0, 
        data : ["The times 03/Jan/2009 Chancellor on brick of second bailout for banks"]
    }

    for(const key in blockData){
        block.children[key].innerHTML = blockData[key];
    }

</script>
```
