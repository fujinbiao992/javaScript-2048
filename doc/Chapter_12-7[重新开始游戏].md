#重新开始游戏

​	当游戏结束后，在页面中提供“重新开始”按钮，单击此按钮可以重新开始游戏。为了实现这个功能，首先在 View 原型对象中增加 cleanNum()方法，用于清空页面中所有的数字单元格，具体代码如下。

```javascript
cleanNum: function() {
	this.nums = {};
	$('#' + this.prefix + '_over').addClass(this.prefix + '-hide');
	$('.' + this.prefix + '-num').remove();
},
```

​	上述第 2 行代码用于清空 this.nums 中保存的所有数字单元格对象，第 3 行代码用于隐藏游戏结束时的提示信息，第 4 行代码用于移除页面中所有的数字单元格。

​	然后修改 Game2048 函数中的代码，将开始游戏相关的功能整理到 start()函数中。具体代码如下。

```javascript
function Game2048(opt) {
    var score = 0; // 初始分数
    var winNum = 2048; // 获胜条件
    var isGameOver = true; // 在调用 start()前游戏处于停止状态
    ……
    function start() { // 开始游戏 
        score = 0; // 将保存的分数重置为 0
        view.updateScore(0); // 将页面中的分数重置为 0
        view.cleanNum(); // 清空页面中多余的数字单元格
        board.init(); // 初始化单元格数组
        board.generate(); // 生成第 1 个数字  
        board.generate(); // 生成第 2 个数字  
        isGameOver = false; // 将游戏状态设为开始
    }
    $('#' + prefix + '_restart').click(start); // 为“重新开始”按钮添加单击事件
    start(); // 开始游戏
}
```

​	在上述代码中，第 6~14 行将初始化游戏相关的代码整理到 start()函数中， 第 15 行代码用于获取 id 为 game_restart 的元素（即“重新开始”按钮），为其添加单击事件，单击时执行 start()函数开始游戏。第 16 行代码调用了 start()函数，用于在页面打开后自动开始游戏。

​	至此，一个简单的网页版 2048 游戏已经开发完成。