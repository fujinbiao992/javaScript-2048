#判断胜利和失败

## 判断游戏是否获胜

​	2048 游戏的胜利条件为，玩家合并出了数字为 2048 的单元格。因此，下面在 Game2048 函数中编写代码，判断合并后单元格的数值是否达到了 2048。如果达到了，说明游戏已经结束，玩家获得了胜利，此时可以弹出一个“您获胜了”的提示信息。具体代码如下。

```javascript
var winNum = 2048;	// 胜利条件
var isGameOver = false;	// 游戏是否已经结束
board.onMove = function(e) {
	if (e.to.num >= winNum) {
		isGameOver = true;
		setTimeout(function() { alert('您获胜了'); }, 300);
	}
	……（原有代码）
};
```

​	在上述代码中，第 6 行用于延迟 300 毫秒后再弹出提示信息，这样可以等待移动单元格的动画结束后再出现提示。具体延迟的时间长短根据实际体验而定即可。

​	游戏结束后，为了避免键盘还能进行单元格移动，在处理键盘事件前，判断 isGameOver 变量，如果游戏结束，则不再执行移动操作。修改 keydown()事件方法，具体代码如下。

```javascript
$(document).keydown(function(e) {
	if (isGameOver) {
		return false;
	}
	……（原有代码）
};
```

​	由于合并出 2048 数字并不简单，为了方便测试，可以先将胜利条件修改为比较小的数字，如 128，观察程序是否能够判断玩家已经获得胜利。

## 判断游戏是否失败

​	游戏失败的条件为，数字填满了所有的单元格，并且相邻单元格也无法合并。为了判断这种情况，下面在 board 的原型对象中增加 canMove()方法，表示当前是否还可以继续移动，具体代码如下。

```javascript
canMove: function() {
  for (var x = 0, arr = this.arr, len = arr.length; x < len; ++x) {
    for (var y = 0; y < len; ++y) {
      if (arr[x][y] === 0) {
        return true;
      }
      var curr = arr[x][y], right = arr[x][y + 1];
      var down = arr[x + 1] ? arr[x + 1][y] : null;
      if (right === curr || down === curr) {
        return true;
      }
    }
  }
  return false;
}
```

​	上述代码用于以左上角的单元格为基点，开始遍历。如果当前单元格为 0，表示可以移动；如果相邻的右、下单元格与当前单元格数字相等，表示可以合并；如果遍历完成后仍然没有符合条件的单元格，则说明当前已经无法移动了。

​	接下来在 Game2048 函数中调用 board.canMove()方法，判断是否已经失败，具体代码如下。

```javascript
board.onMoveComplete = function(e) {
	// 判断是否失败
	if (!board.canMove()) {
		isGameOver = true;
		setTimeout(function() { alert('本次得分：' + score); }, 300);
	}
	……（原有代码）
};
```



​	通过浏览器进行测试，观察程序是否能够判断游戏失败的情况。

## 完善游戏结束页面

​	在游戏胜利或失败时，直接弹出警告框的交互体验并不友好，接下来将优化此功能。在 index.html 中，找到game_container 容器，在容器内部新增如下代码，显示游戏结束画面。

```html
<div id="game_over" class="game-over game-hide">
  <div class="game-over-info">
    <div id="game_over_info"></div>
    <span id="game_restart">重新开始</span>
  </div>
</div>
```

​	在上述代码中，第 1 行 class 为 game-hide 的元素表示隐藏元素，其样式代码如下。

```css
<style>
	.game-hide { display: none; }
</style>
```

​	在页面中，id 为 game_over_info 的元素用于显示游戏结束时的提示信息，考虑到游戏获胜和失败时显示的信息不同，该元素的内容留空，通过 JavaScript 来更改内容。

​	在 View 原型对象中增加 win()和 over()方法，分别用于显示获胜和失败的信息，具体代码如下。

```javascript
win: function() {
  $('#' + this.prefix + '_over_info').html('<p>您获胜了</p>');
  $('#' + this.prefix + '_over').removeClass(this.prefix + '-hide');
},
over: function(score) {
  $('#' + this.prefix + '_over_info').html('<p>本次得分</p><p>' + score + '</p>');
  $('#' + this.prefix + '_over').removeClass(this.prefix + '-hide');
},
```

​	在上述代码中，第 2 行和第 6 行用于在 id 为 game_over_info 元素中添加提示信息，第 3 行和第 7 行用于移除 id 为 game_over 元素的 class 样式 game-hide，移除后提示信息就会显示出来。

​	在增加了 win()和 over()方法后，在 Game2048 函数中按照如下步骤进行修改。

```javascript
// ① 在 board.onMove 中找到如下代码：
setTimeout(function() { alert('您获胜了'); }, 300);
// 修改为：
setTimeout(function() { view.win(); }, 300);
// ② 在 board.onMoveComplete 中找到如下代码：
setTimeout(function() { alert('本次得分：' + score); }, 300);
// 修改为：
setTimeout(function() { view.over(score); }, 300);
```

通过浏览器访问测试，当游戏结束后，就会看到效果。