# snippet-
snippet  方便在手机上面看代码

## [spaceShip](spaceShip.md)

## [marginDemo](marginDemo.md)

## [colorUI](colorUI.md)

## [spinner](SpinnerExamples.md) 
    一个用css做的加载动画
  
## [flex layout](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
    阮一峰的布局教程 他的教程脉络清晰 叙述有条理 可见真正理解 便是再难的东西也能解释得浅显易懂 
#  Details
1. 箭头函数表达式的语法比函数表达式更简洁，并且没有自己的this，arguments，super或 new.target。这些函数表达式更适用于那些本来需要匿名函数的地方，并且它们不能用作构造函数。
> An arrow function expression is a syntactically compact alternative to a regular function expression, although without its own bindings to the this, arguments, super, or new.target keywords. Arrow function expressions are ill suited as methods, and they cannot be used as constructors.
2. 引入箭头函数有两个方面的作用：更简短的函数并且不绑定this。
> Two factors influenced the introduction of arrow functions: shorter functions and no existence of this keyword.
3. 函数做构造函数时
```js
function CommentRecord() {
    this.record = { name: 'CommentRecord', id: 987654 };
    this.update = function() {
        log('CommentRecord this.update')
    }
}
//外部定义的属性和方法 在函数作为构造函数时调用时无效
CommentRecord.update = function() {
    log('CommentRecord update')
}
CommentRecord.out = true;
let c = new CommentRecord();
c.update();                   // CommenRecord this.update
console.log(c.out)            //undefined
console.log(CommentRecord.out)//true
```
4. 
