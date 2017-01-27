## 说明


访问地址 https://spring-fe.github.io/code-snippets/

## JavaScript

from 月影

### 如何写好JavaScript
#### 操作DOM
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