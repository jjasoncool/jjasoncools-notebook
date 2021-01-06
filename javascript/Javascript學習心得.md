# javascript 參考物件 #
- 運算子
  - (一元運算子)加號 (+) 一元運算子。嘗試將運算元轉換成數字，假如它還不是數字的話。
    >   +"3" 回傳 3
    >   +true 回傳 1

- 宣告變數
    **以下兩種變數宣告若在全域範圍內表現則雷同**
  - var
    >   用於一般宣告變數，需要在**區域**之外被讀取 **※區域指的是 function 或者迴圈包住的 {} 之中**
    >   重複宣告時將會用新值覆蓋舊值

  - let
    >   用於單一性宣告變數，可在**區域**之內被讀取其值 **※區域指的是 function 或者迴圈包住的 {} 之中**
    >   block-scoped
    >   重複宣告時將會出錯

  - const
    >   宣告之後，不能再被改變 (在**區域**函數內)
    >   block-scoped
    >   須注意的是，若宣告陣列，陣列被視為 pass by reference 所以對陣列做push,pop不會有問題

- JSON (JavaScript Object Notation.)
    >   JSON.stringify


- location
    - hash:回傳#後面的值
    - host:回傳主機名稱，含port號
    - hostname:回傳主機名稱，不含port號
    - href:回傳整個URL
    - pathname:回傳主機名稱後面的路徑與querystring
    - protocol:回傳通訊協定
    - search:回傳querystring

- 物件{} 與陣列[] 有著不同
    >   陣列不可以用associative array的方式，只有物件可以
    >   但若物件為 undefined 必須要事先宣告，否則該元素不可使用
    >   物件與json是緊密相關的

# javascript 參考function #
- 取得物件
    getElementsByClassName()
    >   取得物件，使用classname

- 字串處理
  - .search(searchvalue)
    >   搜尋字串內是否有符合正規表示式(需使用//)或字串
    >   返還值為該字串第一個字母位置，若查無字串返回 -1

  - .indexOf(searchvalue)
      1. str.indexOf(searchValue[, fromIndex])
        >   若找不到目標值回傳-1
        >   回傳值為，符合字串第一個字的index
      2. arr.indexOf(searchElement[, fromIndex])
        >   若找不到目標值回傳-1
        >   使用全等===判斷，因此陣列內的值須注意型別

  - .slice(beginIndex[, endIndex])
    >   將字串變為陣列index處理，負數代表從字尾反算

  - .length
    >   抓取字串長度或者陣列長度

  - .substring(start, end);
    >   start 從0開始
    >   這個function主要截斷頭與尾之間的字串
    >   This method extracts the characters in a string between "start" and "end", not including "end" itself.
    >   end 可以配合 字串.length 達到想要切斷地方的效果

  - .substr(start, length)
    >   start 從0開始
    >   截斷依據需求長度

  - .join( '黏接字串' )
    >   將所有陣列值使用字串黏接，若黏接字串為空，則使用逗號','黏接

  - .split( '拆解字串' )
    >   依據某連接符號切割字串

  - .trim()
    >   字串切頭尾

  - JSON.parse()
    >   json字串轉換為javascript物件

- 型別判斷
  - typeof
    >   判斷變數型別，可以判斷是否 undefined

- 陣列處理
  - 陣列元素處理
    >   unshift(塞前) -> array <- push(塞後)
    >   shift(刪前)   <- array -> pop(刪後)

  - arr.forEach
    ```js
    arr.forEach(function callback(currentValue[, index[, array]]) {
        //your iterator
    }[, thisArg]);
    ```
  - arr.unshift( element1[, ...[, elementN]] )
    >   移除陣列中的元素

- 歷史紀錄 (**用在ajax請求時，可以多做一筆紀錄**)
  - history.pushState(state, title, url)
    >   附加功能:替換網址
    >   state，新建一個歷史紀錄狀態關聯，用以記錄歷史紀錄的事項
    >   修改的title標題，被firefox忽略
    >   url 目標網址必須與先前的 URL 在同一個主機名稱下

- 事件
    call

    addEventListener()
    >   事件監聽

    setTimeout(function, milliseconds, param1, param2, ...)
    >   設定延遲，之後執行函數
    >   param1, param2 為傳入前面function的參數
    >   milliseconds 單位為千分秒

- DOM
    - nextSibling
    - nextElementSibling
    >   兩者的差別在於 nextElementSibling 只回傳元素
    >   nextSibling 回傳任意物件包含文字
    >   所以若是針對DOM元素，nextElementSibling會比較合乎預期結果(下一個DOM元素)

- URLSearchParams
    >   此API用來處理網址列的querystring
    ```javascript
    var paramsString = "q=URLUtils.searchParams&topic=api";
    var searchParams = new URLSearchParams(paramsString);

    //Iterate the search parameters.
    for (let p of searchParams) {
        console.log(p);
    }

    searchParams.has("topic") === true; // true
    searchParams.get("topic") === "api"; // true
    searchParams.getAll("topic"); // ["api"]
    searchParams.get("foo") === null; // true
    searchParams.append("topic", "webdev");
    searchParams.toString(); // "q=URLUtils.searchParams&topic=api&topic=webdev"
    searchParams.set("topic", "More webdev");
    searchParams.toString(); // "q=URLUtils.searchParams&topic=More+webdev"
    searchParams.delete("topic");
    searchParams.toString(); // "q=URLUtils.searchParams"
    ```

# javascript ES6 #
## arrow function ##
```js
ele => ele.jobno === 3
// 就是
function(ele) {
  return ele.jobno === 3
}
// 等同
(ele) => {
    return ele.jobno === 3
}

```
## promise ##
## async ##
## await ##

# JQuery 參考 function #
## 選擇器 ##

- :last-child
    >   選擇最後一個元素值
- :gt(index)
    >   選擇所有超過 index 元素的元素值
- :eq(index)
    >   選擇該 index 元素的元素值
- :not(selector)
    >   排除 selector 條件
- $("selector1, selector2, selectorN")
    >   多重選擇器，可使用逗號做分隔
- $("[attr=value]")
    >   多重選擇器，使用DOM屬性元素
    >   可以使用[require]來抓取html5上面需要輸入的元素
- $("[attr=value]")
    >   多重選擇器，使用DOM屬性元素

- DOM操作 (確認那些元素是陣列)
    $(this).parents(".pjrow").children().find("a").contents()[0]

## JQuery方法 ##
- $ (jquery)
    >   **jquery 物件需使用jquery 方法時，需多加$()號**
    >   若是已經使用var設定值，若要利用var 的變數進行jquery方法時，則需要再重新再變數前面多加[ $ ]號

- $.noConflict()
    >   解決多版本JQuery衝突的官方提供正確方法
    >   可利用此方法將其中一個版本的Jquery指到另一個變數

- $.parseHTML(HTMLData)
    >   將HTML字串轉換為DOM元素

- $.fn.jquery
    >   查詢當前使用的JQUERY版本

- $.getScript( '../../js/alertify.js' );
    >   引用其他的js檔案
    >   注意：載入完成後才會引用，因此要使用完成頁面後觸發的方式才可以執行函式
    >   注意：若網頁為https引用http內容，則會被Mixed Content條款擋下，無法載入

- $.each( 物件或陣列, function( 屬性名稱或陣列index, 物件值或者陣列值 ){...} )
    >   與.each 雷同，唯一不同為此方法為針對物件或陣列使用，非用在DOM之中(像是JSON轉成陣列物件))

- $.inArray( value, array [, fromIndex ] )
    >   與javascript的.indexOf()方法類似，若回傳 -1 為找不到match項目
    >   因為javascript 會判斷 0 為 false ， 所以應判斷回傳值是否大於-1才可確認找不到目標值
    >   此函數為嚴格比較，因此若尋找類似 5+5 的值，在[ "8", "9", "10", 10 + "" ]陣列中仍會返回-1，須注意

**ajax**
- ajax 包含在jquery方法之中
    >   基本樣式如下
    ```js
    $.ajax({
        type: "POST",
        url: "./catchdata.php",
        data: {s_date: s_date.value, e_date: e_date.value},
        xhrFields: {
            onprogress: function(e) {
                console.log(e.target.responseText);
                // 這邊可以取得 server 回傳的東西 (頁面尚未完成)
            }
        }
    }).done(function(data) {
        if (data != 0) {
            progress(data);
        }
    });

    function progress(data) {
        var val = progressbar.progressbar("value") || 0,
        phpthrowValue = $(data).filter("input.progressValue").toArray();

        phpthrowValue.forEach(function(arrEle) {
            // array
            if (val < arrEle.value) {
                val = parseInt(arrEle.value);
            };
        });

        progressbar.progressbar("value", val);

        if (val <= 99) {
            // 超過5分鐘死掉
            progressTimer = setTimeout(outTime, 300000);
        }
    }

    // 傳送資料
    function submitByAjax(fntype,fnrefno,fndataString = "") {
        $.ajax({
            type: "POST",
            url: "tpqmail_query.php",
            data: {type: fntype, refno: fnrefno, dataString: fndataString}
        }).done( function(data) {
            if (data != 0) {
                alertify.notify("<span style='font-size: 20px;'>"+data+"</span>", 'custom', 3);
            }
            setTimeout(function(){location.reload()}, 2000);
        });
    }

    如果要 ajax 先執行，必須要加上 option
    async: false，否則會在 callback 才執行
    ```

## JQuery物件操作 ##

- .length
    >   取得物件長度，可以是字串或是物件數
    >   **($(JqObj).length == 0)** 可以用來檢查物件是否存在

- .val(key, value)
    >   取得該物件的值
    >   若是塞值，則在括弧內填入要塞入該指定元素的值
    >   通常為針對 value 屬性

- .attr(key, value)
    >   對某物件的某屬性指定值 (純文字html樣板需使用此方式給value值)
    >   沒有value時，取值
    >   **注意** jquery 升級版本後，attr不可使用在 checked, selected, disabled 等屬性上，須改用.prop

- .prop()
    >   針對checked以及selected還有disabled 可以做指定以及返還值的作用
    >   可利用來確認是否有checked以及selected還有disabled
    >   返還boolean

- .data()
    >   通常為對某個物件設定資料值，其實是存取在tag內的 data-xxx 屬性

- .on()
    >   事件監聽，當發生該事件的時候可以執行function 內動作

- .trigger("change")
    >   觸發事件
    >   使得某事件觸發，讓物件若原本有綁定特定觸發條件可以直接觸發

- .clone()
    >   複製出指定的物件，並可已對該已複製的物件做編輯而不影響原有物件
    >   注意：複製出來的物件假如有ID屬性，必須確保ID的唯一性，或是確保被複製的物件使用class屬性

- .wrap()
    >   新增元素，然後用該新增的元素包住指定的元素內內容
    >   wrap內新增的元素字串必須成對

- .before( content )
- .insertBefore( target )
    >   插入HTML字串或是物件在**指定元素前面**
    >   The .before() and .insertBefore() methods perform the same task.
    >   $(target).before(contentToBeInserted)
    >   $(contentToBeInserted).insertBefore(target)

- .after( content )
- .insertAfter( target )
    >   插入HTML字串或是物件在**指定元素後面**
    >   The .after() and .insertAfter() methods perform the same task.
    >   $(target).after(contentToBeInserted)
    >   $(contentToBeInserted).insertAfter(target)

- .serialize()
    >   將form內的參數轉為querystring
    >   必須是successful controls才會被傳遞
        Controls that are disabled cannot be successful.
        If a form contains more than one submit button, only the activated submit button is successful.
        All "on" checkboxes may be successful.
        For radio buttons that share the same value of the name attribute, only the "on" radio button may be successful.
        For menus, the control name is provided by a SELECT element and values are provided by OPTION elements. Only selected options may be successful. When no options are selected, the control is not successful and neither the name nor any values are submitted to the server when the form is submitted.
        The current value of a file select is a list of one or more file names. Upon submission of the form, the contents of each file are submitted with the rest of the form data. The file contents are packaged according to the form's content type.
        The current value of an object control is determined by the object's implementation.
        If a control doesn't have a current value when the form is submitted, user agents are not required to treat it as a successful control.

        Furthermore, user agents should not consider the following controls successful:

        Reset buttons.
        OBJECT elements whose declare attribute has been set.
        Hidden controls and controls that are not rendered because of style sheet settings may still be successful.
    >   不包含檔案


- .each( Function( Integer index, Element element ) )
    >   此方法針對JQuery物件中的DOM元素做迴圈處理，且在迴圈中，this 會指向當下迴圈物件而非選擇器選出來的JQuery物件
    >   function 內指定參數將會隨著迴圈進行而變動，可拿來利用
    >   跳出迴圈或中斷迴圈 使用 return (等同一般迴圈的continue) 或 return false (等同一般迴圈的break)

- .prev( 選擇器 )
    >   此方法可以在同一層內往**前**選擇一個

- .next( 選擇器 )
    >   此方法可以在同一層內往**後**選擇一個

- .closest( 選擇器 )
    >   使用這個方法的元素向外找尋DOM所有父元素，且**包含自己**，搜尋的父元素持續向外至document's root為止
    >   回傳值僅包含**單一**符合元素，若找不到元素則回傳0

- .parents( 選擇器 )
    >   使用這個方法的元素向外找尋DOM所有父元素，但**不包含自己**，搜尋的父元素持續向外至document's root為止
    >   回傳值包含**所有**符合元素，若找不到元素則回傳0

- .parent( 選擇器 )
    >   這個方法類似於.parents()，不同的是此方法只向上找尋一層
    >   並且$( "html" ).parent()回傳document，$( "html" ).parents() returns an empty set.

- .siblings( 選擇器 )
    >   選取同一層DOM的其他非指定物件，也就是與選擇器同一層的其他物件都會被選取，有點像是反向選取的概念
    >   但如果同一層有兩個以上物件被選擇器選到，那相當於全部都會被選到
    >   舉例：
        (A) B C D  程式碼:$(A).siblings(); 選到BCD
        A (B) C D  程式碼:$(B).siblings(); 選到ACD

- .find( 選擇器 )
    >   此方法與filter類似，為篩選出符合選擇器條件的DOM項目作為JQuery元件
    >   但須注意此法為篩選子項目適用，若要選擇同層項目無法篩選出來
    >   此方法與.children()效果相同，但.children()僅可找尋下一層項目，無法多層深入尋找

- .filter( 選擇器, function )
    >   同上說明，此方法為篩選出同層項目符合條件的選項
    >   在一群項目之中找出符合條件項目
    >   可用來選取 ajax 返回的 html 字串
    array.filter(function(v){return v!==''})

# Library 參考語法 #
## moment.js ##


## Jquery UI ##
```js
// textarea 在IE 無法拉取大小
$("textarea").resizable({
    handles: "se"
}).closest(".ui-wrapper").css("padding-bottom", "0px");
```

# 片段語法節錄 #
```js
// javascript 內建用法
// 取陣列值
array.forEach((value,index) => {
    console.log(index +" " + value);
});
// object key value
Object.keys(obj).forEach((key, index) => {
    console.log(`${key} - ${obj[key]}`);
});

var obj = { foo: 'bar'};

obj.hasOwnProperty('foo'); // true
obj.hasOwnProperty('toString'); // false
'toString' in obj; // true

// 函式簡寫(箭頭函式)
// 主要差異是沒有 this
var materials = [
    'Hydrogen',
    'Helium',
    'Lithium',
    'Beryllium'
];

materials.map(function(material) {
return material.length;
}); // [8, 6, 7, 9]

materials.map((material) => {
return material.length;
}); // [8, 6, 7, 9]

materials.map(({length}) => length); // [8, 6, 7, 9]

// 驗證 require 或者其他條件
// 使用 bootstrap 驗證 form 若驗證通過回傳 true
function valid(e) {
    var chkOK = false;
    var forms = $(".needs-validation");
    // 避免只輸入空白問題
    forms.find("input").not("input[type='file']").each(function () {
        $(this).val($(this).val().trim());
    });
    //刪除不需要驗證的input條件
    forms.find(":input[type=hidden], :input[type=button], :input:not(:visible)").each(function () {
        $(this).removeAttr("min max maxLength minLength pattern required");
    });
    // console.log (forms[0]);
    // function 內的 form 等同於 forms[0]
    var validation = Array.prototype.filter.call(forms, function (form) {
        // console.log (form);
        if (form.checkValidity() === false) {
            form.focus();
            e.preventDefault();
            e.stopPropagation();
        } else {
            chkOK = true;
        }
        form.classList.add("was-validated");
    });
    //console.log沒驗證通過的input
    if (!chkOK) {
        var notValidItems = "";
        $("input.form-control:invalid").each(function () {
            notValidItems += (this.id ? this.id : this.name) + ", ";
        });
        console.log("not valid: " + notValidItems);
    }
    return chkOK;
}

//欄位 numberic
$(".numberic").on("keydown", function (e) {
    // Allow: backspace, delete, tab, escape, enter and .
    if ($.inArray(e.keyCode, [46, 8, 9, 27, 13, 110]) !== -1
        // Allow: Ctrl+A, Command+A
        || (e.keyCode === 65 && (e.ctrlKey === true || e.metaKey === true))
        // Allow: home, end, left, right, down, up
        || (e.keyCode >= 35 && e.keyCode <= 40)
    ) {
        // let it happen, don't do anything
        return;
    }
    // Ensure that it is a number and stop the keypress
    if ((e.shiftKey || (e.keyCode < 48 || e.keyCode > 57)) && (e.keyCode < 96 || e.keyCode > 105)) {
        e.preventDefault();
    }
});

// 日期驗證之正規表示法
(?:19|20)[0-9]{2}-(?:(?:0[1-9]|1[0-2])-(?:0[1-9]|1[0-9]|2[0-9])|(?:(?!02)(?:0[1-9]|1[0-2])-(?:30))|(?:(?:0[13578]|1[02])-31))

//location.search 取得 querystring
// 需要判斷是否已有重複的變數，此處僅有判斷放在URI最後面的情況
$("#jobnoSearch").on("click",function () {
    if (location.search.search("&search=") == -1 ){
        location.search += "&search="+$("#inputjobno").val();
    } else {
        location.search = location.search.substring(0,location.search.search("&search="))+"&search="+$("#inputjobno").val();
    }
});

// 全選去對每個 checkbox 觸發 change 事件
$("#selAll").on("click", function () {
    $(".checkStatus").prop("checked", $(this).prop("checked")).trigger("change");
});
$(".checkStatus").on("change", function(){
    let allfields = $(this).closest("td").siblings().find(":input");
    let checkStatus = !($(this).prop("checked"));

    $(allfields).each(function () {
        $(this).prop("disabled", checkStatus);
    });
});

function getQueryString() {
    var queryString = location.search.substring(1);
    var result = {};
    if (queryString != "") {
        array = queryString.split("&");
        array.forEach(function(value) {
            // array
            getarray = value.split(":");
            getarray.forEach(function(getvalue) {
                returnvalue = getvalue.split("=");
                result[returnvalue[0]] = returnvalue[1];
            });
        });
    }
    return result;
}

function toQueryString(obj) {
    var string = "";
    console.log(obj);
    $.each(obj, function (index, value) {
        string += index + "=" + value + "&";
    });
    return string.substring(0, string.length - 1);
}

function changeTitle(type){
    var rrid='<?php print $rid;?>';
    var query = getQueryString();
    query["rrid"] = rrid;
    query["subject"] = type;
    var qs = toQueryString(query);
    window.location = window.location.pathname + "?" + qs;
}


// 虛擬的 form submit 資料(因為ajax 不可以用來下載東西)
var virtualForm = $("<form action=\"printSticker.php\" target=\"_blank\" method=\"POST\"></form>");
virtualForm.append("<input name=\"selectItem\" value=\"" + JSON.stringify(selectItem) + "\" />");
virtualForm.append("<input name=\"start\" value=\"" + $("#start").val() + "\" />");
virtualForm.appendTo($('body')).trigger('submit');

// 新增 options
$('<option/>', { value : row.bank_sale }).text(`${row.curr_name} (${row.currency})`).appendTo('#currency');
$.each(cancer, function (i, item) {
    $('#diagnosis').append($('<option>', {
        value: item,
        text : item
    }));
});

//button 綁定enter送出
$("#inputjobno").on("keypress",  function(event) {
    event.preventDefault();
    // Number 13 is the "Enter" key on the keyboard
    if (event.keyCode === 13) {
        $("#jobnoSearch").click();
    }
});

```
<!-- 很多的被選框 -->
<td class="options"><input name="CODE1[]" type="checkbox" value="1"></td>
<td class="options"><input name="CODE2[]" type="checkbox" value="2"></td>
<td class="options"><input name="CODE3[]" type="checkbox" value="3"></td>
<td class="options"><input name="CODE4[]" type="checkbox" value="4"></td>
<!-- 全選框 -->
<td class="options"><input name="all1" id="all1" type="checkbox" value="CODE1[]"></td>
<td class="options"><input name="all2" id="all2" type="checkbox" value="CODE2[]"></td>
<td class="options"><input name="all3" id="all3" type="checkbox" value="CODE3[]"></td>
<td class="options"><input name="all4" id="all4" type="checkbox" value="CODE4[]"></td>

```js
// 多重勾選選單
function checkOne(){
    let thisName = this.querySelector("input").name;
    let silbings = this.parentNode.getElementsByClassName("options");
    [].forEach.call(silbings, function (el) {
        let rowEachBox = el.querySelector("input");
        if (thisName != rowEachBox.name) {
            rowEachBox.checked = false;
        }
        // 全選
        if (rowEachBox.value != "") {
            // 如果勾選一般的找不到一樣的name元素，故不作用
            let checkboxes = document.getElementsByName(rowEachBox.value);
            [].forEach.call(checkboxes, function (columnBoxes) {
                columnBoxes.checked = rowEachBox.checked;
            });
        }
    });
}
function init() {
    'use strict';
    var allCheck = document.getElementsByClassName("options");
    for (let index = 0; index < allCheck.length; index++) {
        allCheck[index].addEventListener("click", checkOne, false);
    }
}
window.onload = init;


//即時更新頁面，同時使用抓取querystring傳遞參數
function show() {
    var queryString = location.search.substring(1);
    var result = {};
    if (queryString != "") {
        array = queryString.split("&");
        array.forEach(function(value,index) {
            // array
            getarray = value.split(":");
            getarray.forEach(function(getvalue,getindex) {
                returnvalue = getvalue.split("=");
                result[returnvalue[0]] = returnvalue[1];
            });
        });
    }
    // console.log(result);
    $.get("show.php", result, function(data) {
        $("#pro").html(data);
    });
}

// 動態POST變數(可以改post到後端的name，依據該元素的attr name)
function submitData() {
    var dataValues = {};
    dataValues[$("#imono").attr("name")] = $("#imono").val();
    dataValues[$("#certno").attr("name")] = $("#certno").val();
    dataValues["g-recaptcha-response"] = $("#g-recaptcha-response").val();
    $.ajax({
        type: "post",
        url: "./result.php",
        data: dataValues,
        success: function(data) {
            if (data != 0) {
                $("#updateArea").html(data);
                // 重新載入時需要重新對按鈕綁定事件
                reload("reload");
            }
        }
    });
}

// 這個函式是給上面更新頁面之後呼叫使用
function reload(btnId) {
    // 顯示結果表格會使用到
    $("#"+btnId).on("click", function () {
        location.href = location.protocol + "//" + location.host + location.pathname;
    });
}

// 下載問題
    function init() {
        $("#downAllCert").click(function() {
            var data = $("#downAllCert").val().split("|");
            console.log(data);
            var url = "<?=$AP_SERVER?>doc/sur/rdocAllCert.php"
            console.log(url);
            $.ajax({
                method: "POST",
                url: url,
                data: {crno: data[0], a: data[1], actype: data[2], p: data[3]}
            })
            .done(function( file ) {
                // ???????如何下載檔案
            });
        });
    }

    window.onload = init;

//頁籤顯示&隱藏 (配合DIV)
function showPage(obj) {
    $(obj).show();
    $(obj).siblings().hide();
}

function init() {
    var tags = document.querySelectorAll("#menu a");
    for (var j = 0; j < tags.length; j++) {
        tags[j].addEventListener("click", function(event){
            location.hash = this.getAttribute("href");
            showPage(location.hash);
            event.preventDefault();
        });
    }
    showPage("#content");
}

window.onload = init;

// 如果物件存在做判斷，否則不判斷
function findElement(){
    var form = document.getElementById('thisForm');
    var certCount = document.getElementById('tcert_number');
    if (typeof(certCount) != 'undefined' && certCount != null) {
        form.addEventListener('submit', function(e) {
            if (certCount.value <= 0) {
                e.preventDefault();
                alert("6204 證書張數最少需1張，否則應取消勾選 \"6204 國輪補換發證書\" 項目");
            }
        }, false);
    }
}

//事件監聽 (javascript 方法，若使用jquery則不需使用這堆函數)
var lis = {
    // Function for creating event listeners:
    addEvent: function(obj, type, fn) {
        'use strict';
        if (obj && obj.addEventListener) { // W3C
            obj.addEventListener(type, fn, false);
        } else if (obj && obj.attachEvent) { // Older IE
            obj.attachEvent('on' + type, fn);
        }
    }, // End of addEvent() function.
    // Function for removing event listeners:
    removeEvent: function(obj, type, fn) {
        'use strict';
        if (obj && obj.removeEventListener) { // W3C
            obj.removeEventListener(type, fn, false);
        } else if (obj && obj.detachEvent) { // Older IE
            obj.detachEvent('on' + type, fn);
        }
    }
};

function keyPressToUpper(e) {
    var targetElement = e.target || e.srcElement;
    // console.log(targetElement);
    var str = targetElement.value;
    targetElement.value = str.toUpperCase();
}

function init() {
    var btn = document.querySelectorAll('input[name="draw_no"]');
    for (var i=0 ; i < btn.length ; i++) {
        lis.addEvent(btn[i],'keyup',keyPressToUpper);
    }
}

window.onload = init;

// decodeEntities
function decodeEntities(encodedString) {
    let textArea = document.createElement('textarea');
    textArea.innerHTML = encodedString;
    return textArea.value;
}

// 輸入Code 找尋清單
$(".typeahead").typeahead({
    hint: true,
    highlight: true,
    minLength: 2,
    source: function (query, process) {
        return $.ajax({
            url: "index.php",
            data: {query : query.toUpperCase()},
            method: "POST"
        }).done(function (data) {
            // process 是回傳的json轉成陣列物件後，提供產生自動完成清單使用的函數
            process(JSON.parse($(data).filter("#searchCode").text()));
            ID = JSON.parse($(data).filter("#findID").text());
            nameData = JSON.parse($(data).filter("#findName").text());
        });
    },
    updater: function (item) {
        // 此plugin的物件需要使用this.$element才抓的到
        $(this.$element).parents(".form-row").find(".portName").val(nameData[item]);
        $(this.$element).parents(".form-row").find(".portID").val(ID[item]);
        return item;
    }
});
```

# 引用、打包、整合 #

```html
如果 script 檔案與原本的 html 檔案編碼不同，可以在 script 內宣告編碼解決亂碼問題
<script type="text/javascript" charset="UTF-8" src="xyz.js"></script>
```
