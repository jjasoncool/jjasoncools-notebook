----------------------------------------
## CSS 選擇器
----------------------------------------
- DOM 階層選取
- `element1 element2` **在 element1 之內的所有 element2 都會被選取，包含子或孫階層**
- `element1, element2` **element1 和 element2 都會被選取**
- `element1 + element2` **在 element1 之後的第一個 element2 會被選取，必須要接在 element1 之後的元素，中間有其他元素失效**
- `element1 > element2` **在 element1 之內的所有 element2 都會被選取，但須為直接 child，若為孫階層則不生效**
- `element1 ~ element2` **在 element1 之後的所有 element2 都會被選取，但必須在同一個 parent 之下**
- 屬性字串選取
- `[attribute^=value]` **a[href^="https"] Selects every a element whose href attribute value begins with "https"**
- `[attribute$=value]` **a[href$=".pdf"] Selects every a element whose href attribute value ends with ".pdf"**
- `[attribute*=value]` **a[href*="w3schools"] Selects every a element whose href attribute value contains the substring "w3schools"**

----------------------------------------
## 基本語法與技巧
----------------------------------------
- 強制禁止換行
```css
span {
    white-space: nowrap;
}
```

- 表格內文字太長，強迫換行
```css
table {
    table-layout:fixed;
}
table td {
    word-wrap: break-word;
}
```

- 可以指定某 div 內的頁籤顯現或消失
```css
#wrapper > div {
    display: none;
}
#wrapper > div:target {
    display: block;
}
```

- 將物件置中
```css
img {
    margin: auto 0;
}
```

----------------------------------------
## 字型字體
----------------------------------------
- 引用 otf 檔案範例
```css
@font-face {
    font-family: "Noto Sans Black";
    font-style: normal;
    font-weight: 200;
    src: url(../fonts/NotoSansCJKtc-Black.otf) format("opentype");
}

/* 這樣即可使用該字型的otf檔案 */
.someclass {
    font-family: "Noto Sans Black", sans-serif;
}
```

- 不可選取
```css
label {
    user-select: none;
}
```

- 假裝成連結的Button
```css
.btnLink{
    border : 0px;
    padding : 0px;
    background-color : transparent;
    color: #0000EE;
    text-decoration: underline;
}
```

----------------------------------------
## 輸出版面配置
----------------------------------------
- 換行區塊
>   有時候需要一行字不要被截斷換行，可以利用 display: inline-block; 再將一段字句用元素包起來
>   則該元素將會呈現為方塊，且集中一行，無空間才會換行
```html
<span class="ship-info">CR No. <u> foo </u></span>
<span class="ship-info">IMO No. <u> bar </u></span>
```
```css
.ship-info {
    display: inline-block;
}
```

- 浮游
>   float 可以讓區塊像是漂浮效果堆疊，多用來排版
>   外層父元素高度需要使用 `clear: both;`，才會套用到子元素高度
```css
div {
    float: left;
}

.parent:after {
    clear: both;
}
```

- 單行間隔
>   可以配合 float 使用，讓文字可以置中對齊
```css
span {
    line-height: 20px;
}
```

- 列印調整
>   因為 page-break-before 的關係，因此在列印時插入以下程式碼分頁
```html
<div class="page-break"></div>
```
```css
/* 分頁手段 */
@media print {
    .page-break {
        display: block;
        page-break-before: always;
    }
}
/* 調整列印版面配置 */
@media print
{
    @page {
      size: A4; /* DIN A4 standard, Europe */
      margin:3mm;
    }
}
```

- footer 置底
>   absolute 會跟著上一層的高度，因此需要讓body**footer的parent**使用min-height: 100%;
>   然後HTML設定100%，這樣才會讓body再參照parent**html**的高度，避免footer遮蓋內容或是沒有置底
```css
html {
    height: 100%;
}

body {
    /* position的設定也很重要，否則footer不會參照body的實際位置 */
    position: relative;
    /* height: 100%; */
    /* 若是這個屬性也設定了，footer就不會再內容超出的時候置底了 */
    min-height: 100%;
    margin: 0px;
    padding: 0px;
    background: #ffffff;
    font-family: "Noto Sans", sans-serif;
    font-size: 12pt;
    font-weight: 300;
    color: #000000;
}

#content {
    padding-bottom: 6em;
    /* 設定這個的用處為讓內容離footer保持一定距離，避免覆蓋到footer的空間 */
}

#footer {
    background-color: #2c3e50;
    position: absolute;
    bottom: 0;
    width: 100%;
    overflow: hidden;
    padding: 2em 0em;
}
```

- 某區塊背景圖片透明化
```css
.header {
    position: relative;
    width: 100%;
    padding: 28px 0 10px;
    /* background: url('../images/') repeat-x; */
    box-shadow: 0 0 10px 1px #e3e3e3;
}

/* 使用before元素來製作套用某區塊內的子元素，便可以合適該元素的大小，製作透明效果不影響其他的元素 */
.header::before {
    background: url(../images/bg.jpeg) no-repeat center 61%;
    background-size: cover;
    content : "";
    display: block;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity : 0.6;
    z-index: -1;
}
```

----------------------------------------
## 頁面動畫、變化
----------------------------------------
