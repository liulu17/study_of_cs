## dom操作
### 创建了一个段落

para = ducument.createElement('p')

### dom中添加元素
document.body.appendChild(para)

### 获取元素

para = document.querySelector('p')

### 添加监听器
para.addEventListener('click', functinon_name)

### dom加载和js执行顺序问题
js代码如果在执行时操作未加载的dom元素则会报错。有几种应对方式：
1. 将\<script\>代码放到body底部
2. 在\<script\>中为document添加文档加载的监听器。回调中写操作dom的逻辑
```javascript
document.addEventListener("DomContentLoaded", logic_fuction)
```
3. 使用async,这样加载dom和执行js代码是同时进行的
```javascript
<script src='mian.js' async>
```