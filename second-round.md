## 二面

### 数组去重合并

#### 题目

```js
var a = [[1, 3, 5, 2, 7], [0, 4, 5, 2, 1]];
[1, 3, 5, 2, 7, 0, 4] === mergeArray.apply(null, a);
```

#### 实现

```js
function mergeArray(){
	const result = [];
	[].forEach.call(arguments, arr => result.push(...arr));
	return [...new Set([...result])];
}
```

### 检测ip格式

#### 题目

```js
checkIpByRegExp("192.0.0.1") === true;
checkIpByRegExp("23.10.259.0") === false;
```

#### 实现

```js
function checkIpByRegExp(ip){
	return /^(?:1\d{2}|2(?:[0-4]\d|5[0-5])|[1-9]\d|\d)(?:\.(?:1\d{2}|2(?:[0-4]\d|5[0-5])|[1-9]\d|\d)){3}$/.test(ip);
}
```

### bind实现事件监听兼容各浏览器

由于时间有限，笔试时只编写了部分。结束一个小时后才补完了全部，简直难受，这么简单个需求竟然写了这么久，都是完美主义害的。笔试时想把EventEmitter直接放到bind方法闭包里面去，但那样不可能取到元素的this，导致时间的浪费，肠子都悔青了

#### 实现

```js
function EventEmitter(){
	this.events = {};
}
EventEmitter.prototype.addEventListener = function(event, callback){
	if(this.events[event]){
		this.events[event].push(callback);
	}else{
		this.events[event] = [callback];
	}
	return this;
};
EventEmitter.prototype.emit = function(event, e){
	try{
		var a = 0,
			events = this.events[event];
		while(events[a]){
			events[a++](e);
		}
		return this;
	}catch(e){
		throw "no such event";
	}
};
EventEmitter.prototype.removeEventListener = function(event, callback){
	try{
		var a = -1,
			events = this.events[event];
		while(events[++a]){
			events[a] === callback && events.splice(a, 1);
		}
		return this;
	}catch(e){
		throw "no such event";
	}
};
Element.prototype.bind = function bind(event, callback, isCapture){
	if(this.addEventListener){
		this.addEventListener(event, callback, isCapture);
		return this;
	}
	if(this.attachEvent){
		this.attachEvent("on" + event, callback);
		return this;
	}
	if(!this.event){
		this.event = new EventEmitter;
	}
	this["on" + event] = function(e){
		this.event.emit("click", e);
	};
	this.event.addEventListener(event, callback);
	return this;
};
Element.prototype.unbind = function(event, callback, isCapture){
	if(this.removeEventListener){
		this.removeEventListener(event, callback, isCapture);
		return this;
	}
	if(this.detachEvent){
		this.detachEvent("on" + event, callback);
		return this;
	}
	try{
		this.event.removeEventListener(event, callback);
		return this;
	}catch(e){}
};
```

#### 测试结果

```js
var html = document.documentElement;
function a(e){
	console.log(e, 12);
}
html
	.bind("click", function(e){
		console.log(e, 1);
	})
	.bind("click", a)
	.bind("click", function(e){
		console.log(e, 123);
	})
	.bind("click", function(e){
		console.log(e, 1234);
	})
	.unbind("click", a)
	.bind("click", function(e){
		console.log(e, 12345);
	});
```
