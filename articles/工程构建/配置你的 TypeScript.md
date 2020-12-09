# 配置你的 TypeScript

## 背景
最近在忙于搭建工程项目，由于整个项目 70% 属于业务代码， 30% 属于组件、utils工具函数。整个项目全部用 TypeScript 来写不好适合，工作量大且部分同学可能未掌握 TypeScript。最终决定 核心重要的文件（components,utils）使用 TypeScript 来书写,其他的业务逻辑代码用 js 来书写


## 配置 TypeScript

1. 依赖安装

> npm install --save-dev typescript ts-loader

2. webpack 配置解析 loader
```js
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
```
3. 根目录新建 tsconfig.json

```json
{
  "compilerOptions": {
    // null类型检测,const teacher: string = null;会报错
    "strictNullChecks": true,
    // 不允许变量或函数参数具有隐式any类型
    "noImplicitAny": false,
    "module": "CommonJS",
    "target": "ES5",
    // 允许编译JS
    "allowJs": true,
    "experimentalDecorators": true,
    "noImplicitThis": true,
    "noImplicitReturns": true,
    "alwaysStrict": true,
    "skipLibCheck": true,
    "inlineSourceMap": true,
    "inlineSources": true,
    "noFallthroughCasesInSwitch": true,
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "strict": true,
    "removeComments": true,
    "moduleResolution": "node",
    "baseUrl": ".",
    "pretty": true,
    "strictPropertyInitialization": true,
    "importHelpers": true,
    "lib": ["es5", "es2016"]
  },
  "include": ["./src/**/*.ts"],
  "exclude": ["node_modules", "**/*.test.ts"]
}

```

到此 你的项目已经可以愉快的使用 TypeScript 编写代码了

## 配置 Eslint
我们的项目比较特别，混用了 TypeScript 和 javascript. 但是 eslintrc 中的 parser 只能指定一个解析器 例如：
```js
module.exports = {
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "project": "./tsconfig.json"
    },
    "extends": ["plugin:@typescript-eslint/recommended"],
};
```
上诉指定 **@typescript-eslint/parser** 解析器后 ts 和 js 的文件都会使用这个解析器来解析，@typescript-eslint 的规则并不能全部覆盖到 javascript, 因此我们的策略是 针对不同的文件后缀使用不同的 rules

## eslint overrides 特性

overrides 使得我们可以针对不同的文件类型 设置不同的规则，但是在 overrides 中不允许再使用 extends，这意味着我们不能方便的使用 eslint 推荐规则。但是这里有一个潜规则可以绕过 原理上是将 extends 转换成 rules

```js
const typescriptEslintRecommended = require("@typescript-eslint/eslint-plugin")
  .configs.recommended;

module.exports = {
  extends: [
    "@tencent/eslint-config-tencent",
    "plugin:react/recommended",
    "prettier"
  ],
  rules: {
    "react/prop-types": 0
  },
  overrides: [
    {
      files: ["./src/**/*.ts", "./src/**/*.tsx"],
      parser: "@typescript-eslint/parser",
      parserOptions: {
        sourceType: "module",
        project: "./tsconfig.json"
      },
      plugins: ["@typescript-eslint"],
      rules: typescriptEslintRecommended.rules
    }
  ]
};

```

至此我们便可以在项目中，愉快的混写 TypeScript 和 Javascript 啦！