# 用webpack把CocosCreator專案內的所有ts編譯成一支js
###### tags: `cocos creator`,`typescript`,`webpack`,`.d.ts`
## 需要Node.js、npm
先載Node.js，裝好後cmd上確認是否有無安裝好。
![](https://i.imgur.com/ivZwtV3.png)
## 專案資料夾內產生package.json
![](https://i.imgur.com/Jc3jGPd.png)
用cmd，先cd到你的專案資料夾內，在cmd輸入:
```
npm init --save--dev
```
這個步驟是為了產生`package.json`跟`package-lock.json`(可以先不管他)，並且會產生node_modules資料夾。`--save--dev`是指針對此專案的開發環境產生，不是全域的。
* node_modules
node_modules裡面是各種模組。基本上版控時會忽略此檔案，因為真的蠻笨重的。
* package.json
`package.json`內定義了專案的依賴，可以看出此專案載了什麼模組來用。 
`package.json`是蠻重要的檔案，如果你的node_modules不見或刪除，只要資料夾內有`
package.json`，透過cmd，cd到資料夾輸入`npm init`，npm會去找package.json檔案內的所有依賴，把模組都載回來。

## npm安裝webpack
有了package.json後，那就來安裝webpack，安裝好後可以去package.json內看看依賴是不是有多了webpack。
* 安裝webpack指令:
```
npm install webpack webpack-cli --save-dev
```
## npm安裝ts-loader
ts-loader會幫我們把ts檔案都撈來編譯。
* 安裝ts-loader指令:
`npm i -D ts-loader`

## npm安裝typescript
如果是cocos專案已經用ts開發，就會有tsconfig.json檔案了可以不用這一步。
一般專案要ts編譯成js，cmd輸入:
```
npm install -D typescript
```
會產生 tsconfig.json。
我的cocos專案的tsconfig.json為:
```json=
{
  "compilerOptions": {
    "module": "commonjs",
    "lib": [ "dom", "es5", "es2015.promise"],
    "target": "es5",
    "experimentalDecorators": true,
    "skipLibCheck": true
  },
  "exclude": [
    "node_modules",
    "library",
    "local",
    "temp",
    "build",
    "settings"
  ]
}
```
## 在package.json中的script的部分加入某些東西
主要是這三個:
```
"build": "webpack --mode production",
"dev": "webpack --mode development",
"start-dev": "webpack --mode development --watch"
```
完整的package.json可參考(下方依賴有webpack跟ts-loader):
```json=
{
  "name": "slotbase",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --mode production",
    "dev": "webpack --mode development",
    "start-dev": "webpack --mode development --watch"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "ts-loader": "^6.2.1",
    "webpack": "^4.42.0", 
    "webpack-cli": "^3.3.11"
  },
  "dependencies": {
    "typescript": "^3.8.3"
  }
}
```
## webpack.config.js
安裝好上述模組後，手動在專案內新增檔案叫做`webpack.config.js`。這是webpack橅組的設定檔。
* 初始內容為:
```javascript=
module.exports = {
    plugins: [],
    module: {
        rules: []
    }
};
```
手動加上這些(如下圖)，加入TypeScript的載入器規則。
![](https://i.imgur.com/67rmDYI.png)
完整可參考:
```javascript=
module.exports = {
    entry: './allTs/index.ts',
    plugins: [],
    module: {
        rules: [{
            test: /\.ts$/,
            loader: 'ts-loader'
        }]
    },
    resolve: {
        extensions: ['.ts','.js']
    }
};
```
* entry:
entry是webpack執行時的進入點，可以看成是一般程式碼中的main()。可以自己設定路徑。我要編譯ts，所以把ts當作進入點，一般來說都是編譯js(ES6語法編譯成ES5語法)，所以其他專案可能會看到別人寫index.js當作進入點。

* 小撇步:
加上resolve，這樣我index.ts檔案內所import的檔案都不用加上附檔名，優先找ts，然後才是js。(看到這邊，代表也是可以同時編譯ts+js混用的專案。)
```
resolve: {
        extensions: ['.ts','.js']
} 
```

## 引入.d.ts檔案
其實只需要加上`///<reference path="./[檔案名].d.ts"/>`
```typescript=
///<reference path="./creator.d.ts"/>
import "./test" //測試用的.ts
import "./test2"//測試用的.ts
import "./testDist/test3"//測試用的資料夾內的.ts
const {ccclass, property} = cc._decorator;//引入.d.ts後就可以吃到定義了
@ccclass //引入.d.ts後就可以吃到定義了
export default class test2 extends cc.Component {
    start(){
        cc.log("YOOOOOOOOOOOOOO")
    }
}
```
* creator.d.ts怎麼來?
cocos引擎，把這些按一按，專案就會產生`creator.d.ts`。
![](https://i.imgur.com/CIbhriS.png)

## 執行，編譯成一支js
cmd中，cd到專案資料夾，輸入:
```
npm run build
```
成功!可以看到編譯成功幾支檔案
![](https://i.imgur.com/1DFwifp.png)

編譯出的檔案會放在預設的同層dist資料夾內，名叫main.js
可以更改輸出的所在資料夾跟檔名。請參考參考資料的連結。
* 最後的資料夾結構:
![](https://i.imgur.com/2SElOqn.png)
編譯成一支js後。大功告成。其實也可以利用混淆套件，編出來的js就沒人看得懂。

## 混淆與壓縮
TypeScript的編譯器並無法幫我們最小化並混淆編譯出來的JavaScript程式，但Webpack可以做到這件事。
也就是說:TypeScript模組將ts編譯成js，但沒有壓縮混淆的功能。得透過webpack才行。
但npm模組中好像有自帶壓縮混淆JS，透過webpack(還沒另外安裝任何壓縮混淆模組)，所以編譯出來的檔案除了很難看懂以外，全部都塞成一行，看起來是有達成混淆壓縮效果的。

* 手動安裝壓縮混淆模組，cmd輸入:
```
npm i -D uglifyjs-webpack-plugin
```
完整webpack如下
```javascript=
var UglifyJSPlugin = require('uglifyjs-webpack-plugin')
module.exports = {
    entry: './allTs/index.ts',
    plugins: [
        new UglifyJSPlugin({
            uglifyOptions: {
                compress: true,
            }
        })
    ],
    module: {
        rules: [{
            test: /\.ts$/,
            loader: 'ts-loader'
        }]
    },
    resolve: {
        extensions: ['.ts','.js']
    }
    
};


```


## 參考資料
https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/152551/
https://github.com/TypeStrong/ts-loader/issues/639
https://medium.com/i-am-mike/%E4%BB%80%E9%BA%BC%E6%98%AFwebpack-%E4%BD%A0%E9%9C%80%E8%A6%81webpack%E5%97%8E-2d8f9658241d
https://magiclen.org/webpack-typescript/




