# [Thunk](http://www.ruanyifeng.com/blog/2015/05/thunk.html) 阮一峰
# [co](https://github.com/tj/co/blob/master/index.js)
    co 函数库其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个库。使用 co 的前提条件是，Generator 函数的 yield 命令后面，只能是 Thunk 函数或 Promise 对象。
```js
var fetch = require('node-fetch');
var fs = require('fs');
let log = console.log.bind(console)

function thunkify(fn) {
    return function() {
        var args = new Array(arguments.length);
        var ctx = this;

        for (var i = 0; i < args.length; ++i) {
            args[i] = arguments[i];
        }

        return function(done) {
            var called;

            args.push(function() {
                if (called) return;
                called = true;
                done.apply(null, arguments);
                console.log('argument', arguments)
            });

            try {
                console.log('args', args[2])
                fn.apply(ctx, args);
            } catch (err) {
                done(err);
            }
        }
    }
};

var readFile = thunkify(fs.readFile);

var gen = function*() {
    var r1 = yield readFile('./f.ts', 'utf-8');
    console.log('content', r1);
    var r2 = yield readFile('./f.ts', 'utf-8');
    console.log('content', r2);
};

function run(fn) {
    var gen = fn();
    //实际上是这样调用的 fs.readFile('./f.ts', 'utf-8'，next)
    function next(err, data) {
        var result = gen.next(data);
        if (result.done) return;
        result.value(next);
    }
    next();
}

run(gen);
```
