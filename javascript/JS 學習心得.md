## 基本概念 ##

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
    // 現在 player 保持不變，而 newPlayer 則是 {score: 2, name: 'Jeff'}

    // 如果你想使用 object spread 語法的話，你可以用以下的寫法：
    // var newPlayer = {...player, score: 2};
    ```

## react ##

- 在 React 中，我們遵循的傳統通常是用 on\[Event\] 來命名那些代表 event 的 prop，用 handle\[Event\] 來命名那些 handle event 的方法
