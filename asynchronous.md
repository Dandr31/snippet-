# asynchronous
    实验下javaScript的异步执行 
### setTimeOut
+ 实验1
```js
var log = console.log.bind(console);
setTimeout(function() { log(new Date()) }, 1000);//1000ms 
setTimeout(function() { log(new Date()) }, 2000);
setTimeout(function() { log(new Date()) }, 3000);
```
[结果](./asynchronous1.png)
三行 ` setTimeOut() ` 异步执行，它们的 **callback()** 调用时间相差1000ms。但是这个差值有细微区别，并非精确等于1000ms。
#### 说明
可以假设每一行代码都有一个 **执行完毕事件触发器**，哪一行执行完毕触发 **执行完毕事件** ，而js引擎 **轮询** 每一个事件触发器， **轮询** 就会有一定的顺序。比如有一个房间里面有A B C D四个人，A B C 都在写字，A写100个字需要1000ms，B要2000ms，D要3000ms。但是D并不知的他们的写字速度，所以如果D想要知道他们三人的写字速度。首先， *依次* 通知三人开始写字，并分别记录开始时间，这里每个人的开始时间不同，D还要以一定频率依次观察三人写了多少字，并在发现某个人写完字后，记录下结束时间。
+ 实验2
```js
var log = console.log.bind(console);
setTimeout(function() { log(new Date()) }, 1000);
setTimeout(function() { log(new Date()) }, 1000);
setTimeout(function() { log(new Date()) }, 1000);
```
[结果](./asynchronous2.png)
+ 实验3
```js
var log = console.log.bind(console);

function test(callback) {
    callback();
    setTimeout(function() { log('set1', new Date()) }, 1000);
    setTimeout(function() { log('set2', new Date()) }, 2000);
    setTimeout(function() { log('set3', new Date()) }, 3000);
}
test(function() {
    setTimeout(function() { log('call', new Date()) }, 3000)
})
```
[结果](./asynchronous3.png)
