# SQL 테스트



#### 문제 2

![](https://i.imgur.com/faUEbyk.png)

<details>

<summary>답안 </summary>

\[!note]+ ![](https://i.imgur.com/tDzr4Ou.png)



</details>





#### 문제 4

![](https://i.imgur.com/WB3Ex3n.png)

> \[!note]+ 해설 ![](https://i.imgur.com/H5C6pxu.png) ![](https://i.imgur.com/VKQDLd0.png)

```

<button onclick="toggleContent()">토글 버튼</button>

<div id="toggleContent" style="display:none;">
    여기에 숨겨진 내용을 작성하세요.
</div>

<script>
    function toggleContent() {
        var content = document.getElementById("toggleContent");
        if (content.style.display === "none") {
            content.style.display = "block";
        } else {
            content.style.display = "none";
        }
    }
</script>
```





