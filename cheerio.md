# [cheerio](https://github.com/cheeriojs/cheerio)
    Fast, flexible & lean implementation of core jQuery designed specifically for the server.
#### exports 中的 this
b.js 作 exports 模块
```js
/**作为输出模块 */
exports.name = 'b.js';
exports.nameThis = function() {
    console.log('nameThis:', this.name);
    console.log('this:', this);
}
exports.nameThis();
```
c.js 作 require 模块
```js
var b = require('./b.js');
b.name = 'c.js';
b.nameThis();
```
![Result](png/exportsThis.png)
