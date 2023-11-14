## 基本概念 ##
- 運算子
  - (一元運算子)加號 (+) 一元運算子。嘗試將運算元轉換成數字，假如它還不是數字的話。
    - +"3" 回傳 3
    - +true 回傳 1

- 宣告變數

    **以下兩種變數宣告若在全域範圍內表現則雷同**

  - var
    - 用於一般宣告變數，需要在**區域**之外被讀取 **※區域指的是 function 或者迴圈包住的 {} 之中**
    - 重複宣告時將會用新值覆蓋舊值

  - let
    - 用於單一性宣告變數，可在**區域**之內被讀取其值 **※區域指的是 function 或者迴圈包住的 {} 之中**
    - block-scoped
    - 重複宣告時將會出錯

  - const
    - 宣告之後，不能再被改變 (在**區域**函數內)
    - block-scoped
    - 須注意的是，若宣告陣列，陣列被視為 pass by reference 所以對陣列做元素變更，如:push,pop不會有問題

- JSON (JavaScript Object Notation.)

  - javascript物件轉換為JSON字串

    `JSON.stringify()`

  - JSON字串轉換為javascript物件

    `JSON.parse()`

- array
  - 清除陣列
    `arr.length = 0`

- arr.reduce(callback[accumulator, currentValue, currentIndex, array], initialValue)
  - 參數

        callback
            用於處理陣列中每個元素的函式，可傳入四個參數：
            accumulator
                用來累積回呼函式回傳值的累加器（accumulator）或 initialValue（若有提供的話，詳如下敘）。累加器是**上一次呼叫後，所回傳的累加數值**。
            currentValue
                原陣列目前所迭代處理中的元素。
            currentIndex(選擇性)
                原陣列目前所迭代處理中的元素之索引。若**有傳入 initialValue，則由索引 0 之元素開始，若無則自索引 1 之元素開始**。
            array(選擇性)
                呼叫 reduce() 方法的陣列。

        initialValue(選擇性)
            於第一次呼叫 callback 時要傳入的累加器初始值。若沒有提供初始值，則原陣列的第一個元素將會被當作初始的累加器。假如**於一個空陣列呼叫 reduce() 方法且沒有提供累加器初始值，將會發生錯誤**。
    ```js
    const result = {
        success: ["max-length", "no-amd", "prefer-arrow-functions"],
        failure: ["no-var", "var-on-top", "linebreak"],
        skipped: ["no-extra-semi", "no-dup-keys"]
    };
    function makeList(arr) {
        // Only change code below this line
        const failureItems = [];
        let result = arr.reduce((initValue, currentValue) => {
            failureItems.push(`<li class="text-warning">${currentValue}</li>`);
            // return 會變成下一次的 initValue
            return [...initValue, currentValue];
        }, []);
        console.log(result);

        return failureItems;
    }

    const failuresList = makeList(result.failure);
    ```

- recursion
  ```js
    function rangeOfNumbers(startNum, endNum) {
        // rangeOfNumbers(1,5) = [1,2,3,4,5]
        if (startNum == endNum) {
            return [startNum];
        }
        return [startNum, ...rangeOfNumbers(startNum + 1, endNum)];
    };
  ```

## javascript ES6 ##
### arrow function ###
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

### Destructuring Assignment ###
- 直接展開
    ```js
    const a = ['a', 'b', 'c'];
    const b = [1, 2, ...a, 3];
    // result: b = [1, 2, 'a', 'b', 'c', 3]
    ```

- 截斷陣列
    ```js
    const [a, b, ...arr] = [1, 2, 3, 4, 5, 7];
    console.log(a, b);
    console.log(arr);

    // The console would display the values 1, 2 and [3, 4, 5, 7].
    ```

- 當作function參數過濾
    ```js
    const user = { name: 'John Doe', age: 34 };

    const name = user.name;
    const age = user.age;

    // 以上等同於下句
    const { name, age } = user;

    // 若是要指定給不同名稱變數
    const newName = user.name;
    const newAge = user.age;

    // 以上同下句
    const { name: newName, age: newAge } = user;
    ```

### Object Property Shorthand ###
```js
let a = 'foo',
    b = 42,
    c = {};

// Shorthand property names (ES2015)
// 等同 let o = {a :a, b: b, c: c}
let o = {a, b, c}
```

### Class (Function with constructor) ###
```js
// constructor
class Vegetable {
    constructor(vege) {
    this.name = vege;
    }
}

const carrot = new Vegetable('carrot');
console.log(carrot.name); // Should display 'carrot'

// setter & getter
class Thermostat {
    constructor(degree) {
    this.Fahrenheit = degree;
    this.Celsius = (this.Fahrenheit - 32) * 5 / 9;
    }

    get temperature() {
    return this.Celsius;
    }

    set temperature(update) {
    this.Celsius = update;
    }
}

const thermos = new Thermostat(76); // Setting in Fahrenheit scale
let temp = thermos.temperature; // 24.44 in Celsius
thermos.temperature = 26;
temp = thermos.temperature; // 26 in Celsius
```

### Module Export Import ###
```js
// add.js
// 將 function,object,value...等 變為模組
const add = (x, y) => {
  return x + y;
}

export { add, a, b };
// or
export const add = (x, y) => {
  return x + y;
}
export a = 'some'
export b = 'thing'

// or old nodejs syntax
module.exports = {
    add,
    a,
    b,
}

// index.js
// 引用
import { add, a, b } from './math_functions.js';
// 可直接呼叫 add
// 或者
import * as myMathModule from "./math_functions.js";
// 呼叫時必須包含 alias name
myMathModule.add(2,3);


// export default
// Usually you will use this syntax if only one value is being exported from a file. It is also used to create a fallback value for a file or module.
// new syntax
export default function add(x, y) {
  return x + y;
}
// old nodejs syntax
module.exports = {
    default: function add(x, y) {
        return x + y;
    }
}
// 不需要中括弧
import add from "./math_functions.js";
import { default as add } from "./math_functions.js";
```

### promise ###
```js
// 常用在 call db, filesystem, 所有 io 和 webapi
// https://pjchender.dev/javascript/note-event-loop-microtask/
// https://www.jsv9000.app/?code=c2V0VGltZW91dChmdW5jdGlvbiBhKCkgeyBjb25zb2xlLmxvZygnTWFjcm8gVGFzaycpIH0sIDApOwoKUHJvbWlzZS5yZXNvbHZlKCkKLnRoZW4oZnVuY3Rpb24gYigpIHsgY29uc29sZS5sb2coJ01pY3JvIHRhc2snKSB9KTsKCmNvbnNvbGUubG9nKCdZbycpOw%3D%3D
const touchGirlsBreast = new Promise((success, reject) => {
  // need girls react true/false
  let result = touch();

  if(result) {
    success("We got the life goal!");
  } else {
    reject("Hentai");
  }
}).then(message => {
  // 定義 then
  console.log(message);
}).catch(error => {
  // 定義 catch
  console.log(error);
});
```

### Object Mutation ###
- 透過 Mutation（修改）來改變資料
    ```js
    var player = {score: 1, name: 'Jeff'};
    player.score = 2;
    // 現在 player 是 {score: 2, name: 'Jeff'}
    ```
- 不透過 Mutation（修改）來改變資料
    ```js
    var player = {score: 1, name: 'Jeff'};
    var newPlayer = Object.assign({}, player, {score: 2});
    // 如果你想使用 object spread 語法的話，你可以用以下的寫法：
    var newPlayer = {...player, score: 2};
    // 現在 player 保持不變，而 newPlayer 則是 {score: 2, name: 'Jeff'}
    ```

### async ###
### await ###

## react ##
- 在 React 中，我們遵循的傳統通常是用 on\[Event\] 來命名那些代表 event 的 prop，用 handle\[Event\] 來命名那些 handle event 的方法
