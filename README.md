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
		
		console.log(item.innerHTML);
	}
});

```