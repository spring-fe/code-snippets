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
```js
const traffic = document.getElementById('traffic');

var stateList = ['wait', 'stop', 'pass'];

var currentStateIndex = 0;

setInterval(function(){
  var state = stateList[currentStateIndex];
  traffic.className = state;
  currentStateIndex = (currentStateIndex + 1) % stateList.length;
}, 2000);
```
问题：
依赖于外部变量 stateList、currentStateIndex
封装性不好……差评！！
##### 版本3
```js
const traffic = document.getElementById('traffic');

function start(traffic, stateList){
  var currentStateIndex = 0;

  setInterval(function(){
    var state = stateList[currentStateIndex];
    traffic.className = state;
    currentStateIndex = (currentStateIndex + 1) % stateList.length;
  }, 2000);
}

start(traffic, ['wait', 'stop', 'pass']);
```
问题：
版本 3 虽然中规中矩……
但其实可复用性差！
##### 版本4
```js
const traffic = document.getElementById('traffic');

function poll(...fnList){
  let stateIndex = 0;
  
  return function(...args){
    let fn = fnList[stateIndex++ % fnList.length];
    return fn.apply(this, args);
  }
}

function setState(state){
  traffic.className = state;
}

let trafficStatePoll = poll(setState.bind(null, 'wait'),
                            setState.bind(null, 'stop'),
                            setState.bind(null, 'pass'));

setInterval(trafficStatePoll, 2000);
```
版本 4 函数式编程，抽象出 poll 方法，有点意思

然而需求来了……

PM 做了需求变更如下：

wait、stop、pass 状态的时长不相等，分别改成 1秒、2秒、3秒

那么是否要回归到版本 1 呢？
##### 版本5
```js
const traffic = document.getElementById('traffic');

function wait(time){
  return new Promise(resolve => setTimeout(resolve, time));
}

function setState(state){
  traffic.className = state;
}

function reset(){
  Promise.resolve()
    .then(setState.bind(null, 'wait'))
    .then(wait.bind(null, 1000))
    .then(setState.bind(null, 'stop'))
    .then(wait.bind(null, 2000))
    .then(setState.bind(null, 'pass'))
    .then(wait.bind(null, 3000))
    .then(reset);
}

reset();
```
版本 5 用 Promise 解决问题， 抽象出 wait 方法，还不错
##### 版本6
```js
const trafficEl = document.getElementById('traffic');

function TrafficProtocol(el, reset){
  this.subject = el;
  this.autoReset = reset;
  this.stateList = [];
}

TrafficProtocol.prototype.putState = function(fn){
  this.stateList.push(fn);
}

TrafficProtocol.prototype.reset = function(){
  let subject = this.subject;
  
  this.statePromise = Promise.resolve();
  this.stateList.forEach((stateFn) => {
    this.statePromise = this.statePromise.then(()=>{
      return new Promise(resolve => {
        stateFn(subject, resolve);
      });
    });
  });
  if(this.autoReset){
    this.statePromise.then(this.reset.bind(this));
  }
}

TrafficProtocol.prototype.start = function(){
  this.reset();
}

var traffic = new TrafficProtocol(trafficEl, true);

traffic.putState(function(subject, next){
  subject.className = 'wait';
  setTimeout(next, 1000);
});

traffic.putState(function(subject, next){
  subject.className = 'stop';
  setTimeout(next, 2000);
});

traffic.putState(function(subject, next){
  subject.className = 'pass';
  setTimeout(next, 3000);
});

traffic.start();
```
优点：面向对象、函数式、Promise、灵活可扩展

缺点：复杂度、实现难度，是否过度设计了？

设计是双刃剑

写代码简单

程序设计不易

且行且珍惜……
#### Javascript的效率
给定一个很大的数组，数组里面有许多整数，用 JavaScript 实现一个函数，要求：

将数组中之和为 10 的每一对数配对并找出，返回这些数配对后的数组。

例如：[11, 3, 8, 9, 7, -1, 1, 2, 4...]

得到：[[11,-1],[3,7],[8,2],[9,1]...]

##### 版本1
```js
let list = [11, 4, 9, 3, -1, -3, 6, 7, 9, 13, 8];

function map(list){
	let ret = [], len = list.length;
	
	for(let i = 0; i < len; i++){
		for(let j = i+1; j < len; j++){
			if(list[i] + list[j] === 10){
				ret.push([list[i], list[j]]);
			}
		}
	}
	return ret;
}

console.log(JSON.stringify(map(list)));
```
##### 版本2
```js
let list = [11, 4, 9, 3, -1, -3, 6, 7, 9, 13, 8];

function map(list){
	let ret = [];
	list = list.sort((a,b)=>a-b);
	
	for(let i = 0, j = list.length - 1; i < j;){
		let a = list[i], b = list[j];
		if(a + b === 10){
			ret.push([a,b]);
			i++;
			j--;
		}else if(a + b < 10){
			i++;
		}else{
			j--;
		}
	}
	return ret;
}

console.log(JSON.stringify(map(list)));
```
为什么版本2比版本1快很多？

还有没有别的解法？
#### 高阶函数－过程抽象
数据抽象是把数据抽象出来由函数处理，高阶函数是函数处理函数。

修改函数

```js
function setColor(color, el){
  el.style.color = color;
}

setColor('red', content);
```
```js
function setColor2(el, color){
  el.style.color = color;
}
setColor2(content, 'red');
```
```js
function setColor2(el, color){
  setColor(color, el);
}
setColor2(content, 'red');
```
```js
function reverseArgs(fn){
  return function(...args){
    args = args.reverse();
    return fn.apply(this,args);
  }
}
setColor = reverseArgs(setColor);
setColor(content, 'blue');
```
```js
function delay(fn, time){
  return function(...args){
    setTimeout(fn.bind(this, ...args), time);
  }
}
setColor = delay(reverseArgs(setColor), 100);
setColor(content, 'blue');
```
```js
var wait500 = reverseArgs(setTimeout).bind(null, 500);
wait500(()=>console.log('message'));
```



