# [mume](https://github.com/shd101wyy/mume)
    用TypeScript写的MarkDown 转换器
### 可以看出我的逻辑能力要比他们低很多 但实际上除了一遍遍反复练习 没有其他办法了
### 当我写这篇文档时 明显思路很混乱

## TypeScript 调试 in VS Code
### 编译为javaScript直接调试 
#### 1.写tsconfig.json
```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es6",
    "sourceMap": true,
    "skipLibCheck": true,
    "outDir": "out",
    "lib": ["es6", "dom"],
    "rootDir": ".",
    "declaration": true,
    "noUnusedLocals": true
  },
  "include": ["src/**/*", "test/**/*"],
  "exclude": ["node_modules"]
}
```
+ **sourceMap**属性和调试相关
#### 2.编译cmd
```
tsc
```
#### 3.写lanuch.json
```json
{
     "type": "node",
     "request": "launch",
     "name": "mpjs",
     "program":"C:\\Users\\Thinkpad\\Desktop\\Book\\mume-master\\test\\usage\\mp.js"
 }
```
1. type: node 
2. request: launch Or attach 
3. name 该配置名字
4. program : 启动调试时运行的js文件地址 
#### 4.启动调试

#### 注意
1. 如果一次设置太多断点 项目太大时就很难运行到断点位置 所以可以设置少量断点 多次调试
2. 运行时有一个路径问题 在 mp.js中的 *filePath: Path*  **Path** 分为调试时路径 和node运行时路径
3. **在调试状态把鼠标放在断点前 已经运行后的代码上可以查看变量值**十分智能 
4. 使用ts-node 调试总会有莫名奇妙的错误
5. ***可以直接在typeScript上设置断点 2019-04-22***
   + ！[ts debuger](debuger/typeScript-VSC.png)
   + 尝试不安装 *ts-node* 也可以实现此功能 直接在 ts 文件上设置断点 2019-04-23
6. 若调试时 未运行到的代码 鼠标停在上面的值是不准确的 
## 代码笔记
#### htmlExport
1. readFile:node 的 fs 模块
2. parseMD:将 md 文件convert为html 文件
    + (method) MarkdownEngine.parseMD(inputString: string, options: MarkdownEngineRenderOption): Promise<MarkdownEngineOutput>
    + transformMarkdown
        + (alias) transformMarkdown(inputString: string, { fileDirectoryPath, projectDirectoryPath, filesCache, useRelativeFilePath, forPreview, forMarkdownExport, protocolsWhiteListRegExp, notSourceFile, imageDirectoryPath, usePandocParser, tocTable, }: TransformMarkdownOptions): Promise<TransformMarkdownOutput>import transformMarkdown
#### parseMd
1. 
#### htmlParse----- **[cherrio.js](https://github.com/cheeriojs/cheerio/tree/master)**
1. *$().html()* 注意本软件版本号为  **** *"version": "1.0.0-rc.3"* 会自动把中间的错误部分删掉
```html
  <html>
      <body>
          <html>
            <body>
            </body>
          </html>
      </body>
  </html>
```
2. 我在 github 仓库得到的是 **master** 分支 *"version": "0.22.0"*  不会删掉中间部分 两版本依赖差别为
```JSON
    "dependencies": {
    "css-select": "~1.2.0",
    "dom-serializer": "~0.1.1",
    "entities": "~1.1.1",
    "htmlparser2": "^3.9.1",
    "lodash": "^4.17.11",
    "parse5": "^3.0.1"  //version": "0.22.0 没有应用此库
    }
```
