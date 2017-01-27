## 说明


访问地址 https://spring-fe.github.io/code-snippets/

## JavaScript

from 月影

### 如何写好JavaScript
#### 操作DOM
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
相对于版本1，版本2使javascript与HTML、CSS的职责分离
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