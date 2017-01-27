## 说明


访问地址 https://spring-fe.github.io/code-snippets/

## JavaScript

from 月影

### 如何写好JavaScript
#### 操作DOM
张三，李四，王五，赵六列表，点击其中一个列表项，背景颜色变为黑色，其他列表项为默认颜色。
##### 版本1
```html
<ul id="user-list">
	<li>张三</li>
	<li>李四</li>
	<li>王五</li>
	<li>赵六</li>
</ul>
```
```css
body{
	background-color: white;
	font-size: 24px;
}
#user-list{
	line-height: 1.5em;
}
#user-list > li:hover{
	background-color: rgba(0,0,0,0.3);
}
```
```js
let list = document.querySelector('#user-list');
let items = document.querySelectorAll('#user-list > li');

list.addEventListener('click', function(e){
	if(e.target.tagName === 'LI'){
		let item = e.target;
		
		items.forEach(function(item){
			item.style.background = 'inherit';
			item.style.color = 'inherit';
		});
		
		item.style.background = 'black';
		item.style.color = 'white';
	}
});

```
##### 版本2
```html
<ul id="user-list">
	<li>张三</li>
	<li>李四</li>
	<li>王五</li>
	<li>赵六</li>
</ul>
```
```css
body{
	background-color: white;
	font-size: 24px;
}

#user-list{
	line-height: 1.5em;
}

#user-list > li:hover{
	background-color: rgba(0,0,0,0.3);
}


#user-list > li.active{
	background-color: black;
	color: white;
}
```
```js
let list = document.querySelector('#user-list');
let items = list.querySelectorAll('li');

list.addEventListener('click', function(e){
	items.forEach(function(item){
		item.className = '';
	});
	if(e.target.tagName === 'LI'){
		let item = e.target;
		item.className = 'active';
	}
});
```
相对于版本1，版本2使javascript与HTML、CSS的职责分离。
##### 版本3
```html
<ul id="user-list">
	<li><input name="items" id="item0" type="radio" value="张三"/><label for="item0">张三</label></li>
	<li><input name="items" id="item1" type="radio" value="李四"/><label for="item1">李四</label></li>
	<li><input name="items" id="item2" type="radio" value="王五"/><label for="item2">王五</label></li>
	<li><input name="items" id="item3" type="radio" value="赵六"/><label for="item3">赵六</label></li>
</ul>
```
```css
body{
	background-color: white;
	font-size: 24px;
}

#user-list{
	line-height: 1.5em;
}

#user-list input{
	display: none;
}

#user-list label{
	display: block;
}

.#user-list > li:hover{
	background-color: rgba(0,0,0,0.3);
}

#user-list input:checked+label{
	background-color: black;
	color: white;
}
```
#### API的设计
红绿灯，三个状态用红(stop)、绿(pass)、黄(wait)表示，要求用JavaScript让三个状态轮流切换，每个状态的停留时间是2秒。
##### 版本1
```html
<ul id="traffic" class="wait">
  <li><span></span></li>
  <li><span></span></li>
  <li><span></span></li>
</ul>
```
```css
#traffic > li{
  display: block;
}

#traffic span{
  display: inline-block;
  width: 50px;
  height: 50px;
  background-color: gray;
  margin: 5px;
  border-radius: 50%;
}

#traffic.stop li:nth-child(1) span{
  background-color: #a00;
}

#traffic.wait li:nth-child(2) span{
  background-color: #aa0;
}

#traffic.pass li:nth-child(3) span{
  background-color: #0a0;
}
```
```js
const traffic = document.getElementById('traffic');

(function reset(){
  traffic.className = 'wait';
  
  setTimeout(function(){
      traffic.className = 'stop';
      setTimeout(function(){
        traffic.className = 'pass';
        setTimeout(reset, 2000)
      }, 2000)
  }, 2000);
})();
```
问题：

如果需求增加到 5 盏、10 盏灯？

```js
const traffic = document.getElementById('traffic');

(function reset(){
  traffic.className = 'wait';
  
  setTimeout(function(){
      traffic.className = 'stop';
      setTimeout(function(){
        traffic.className = 'pass';
        setTimeout(function(){
          ...
            ...
              ...
                ...
                  setTimeout(reset, 2000)
```
过程耦合 + Callback Hell…… 差评！！！
##### 版本2
