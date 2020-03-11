# 如何把所有ts編譯成一份.d.ts？
###### tags: `typescript`,`webpack`
> 接續上文：[用webpack把CocosCreator專案內的所有ts編譯成一支js](https://hackmd.io/@1WURRvNQSLaq0ZutDixXNg/ryaUhfRVI)
## 什麼是.d.ts?
模組定義檔 ( Declaration Files )，是為了讓 TypeScript 能與目前市面上各種 JavaScript 模組/函式庫一起運作。

> The "d. ts" file is used to provide typescript type information about an API that's written in JavaScript. The idea is that you're using something like jQuery or underscore, an existing javascript library

## 個別生成對應的.d.ts
在tsconfig.json中加上`"declaration":true`，這樣每個ts被編譯時，也都會產生自己的.d.ts。
```json=
{
  "compilerOptions": {
    "module": "commonjs",
    "lib": [ "dom", "es5", "es2015.promise"],
    "target": "es5",
    "experimentalDecorators": true,
    "skipLibCheck": true,
    "declaration":true
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

## 編成一支.d.ts
(還沒成功)

## 參考資料
https://www.zhihu.com/question/52068257
https://blog.miniasp.com/post/2016/08/22/TypeScript-Future-Declaration-Files
https://stackoverflow.com/questions/21247278/about-d-ts-in-typescript