# record

> 创建记录

## 配置

> 详情可以查看commit提交记录

- Init
- Git Commit Message
- TypeScript
- Eslint
- Prettier
- Lint Staged
- Jest
- Npm Script Hook
- Githun Action

### Init

采用 NPM 可以对任何普通的项目进行初始化操作，执行 [`npm init`](https://docs.npmjs.com/cli/init) 会在项目根目录下生成 `package.json` 包描述文件。

### Git Commit Message

[Commitizen](https://github.com/commitizen/cz-cli) 是一个规范 Git 提交说明（Commit Message）的 CLI 工具，具体如何配置可查看 [Cz 工具集使用介绍](https://juejin.im/post/5cc4694a6fb9a03238106eb9)。本项目中主要使用了以下功能：

- [cz-customizable](https://github.com/leonardoanalista/cz-customizable)
- [commitlint](https://commitlint.js.org/#/)
- [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog)

配置以后会产生以下能力：

- 使用 `git cz` 代替 `git commit` 进行符合 Angular 规范的 Commit Message 信息提交
- 代码提交之前会通过 [husky](https://github.com/typicode/husky) 配合 git hook 进行提交信息校验，一旦提交信息不符合 Angular 规范，则提交会失败
- 执行 `npm run changelog` 会在根目录下生成 `CHANGELOG.md` 版本日志

> 温馨提示：如果不知道什么是 CLI （命令行接口），可查看 [使用 NPM 发布和使用 CLI 工具](https://juejin.im/post/5eb89053e51d454de54db501)。

### TypeScript

#### TypeScript 背景

工具函数库的实现采用 TypeScript，除了可以自动生成 ts 声明文件供外部更好的提示使用之外，也可以避免 JavaScript 动态性所带来的一些无法预料的错误信息（具体可查看 [Top 10 JavaScript errors from 1000+ projects (and how to avoid them)](https://rollbar.com/blog/top-10-javascript-errors/)），从而使算法的设计更加严谨。 TypeScript 的构建方式有很多种，除了原生编译器 tsc 以外，还包括 Webpack、Rollup、 Babel 以及 Gulp 等（更多构建工具的集成可查看 [Integrating with Build Tools](https://www.typescriptlang.org/docs/handbook/integrating-with-build-tools.html)）:

- Webpack 主要用于页面应用的模块化构建，使用 Webpack 构建会增加构建库的体积，因此简单工具库的制作使用 Webpack 完全是 "杀鸡用牛刀"。
- Rollup 是一个构建工具库非常不错的轻量选择，它持有的 [Tree Shaking](https://github.com/rollup/rollup) 以及可构建输出 [ES Module](https://github.com/rollup/rollup/wiki/ES6-modules) 的特性使得它被 tsdx、microbundle 甚至 Vue 等广泛使用。
- Babel 对于 TypeScript 可使用 [@babel/preset-typescript](https://babeljs.io/docs/en/babel-preset-typescript) 去除 TypeScript 类型标记，但是不做类型编译检查，更多关于 Babel 对于 TypeScript 支持的限制可查看 [@babel/plugin-transform-typescript - Caveats](https://www.babeljs.cn/docs/babel-plugin-transform-typescript#caveats)或 [Babel 7 or TypeScript](https://kulshekhar.github.io/ts-jest/user/babel7-or-ts)。
- Gulp 是一个非常轻量的构建工具，并且也是 TypeScript 官方推荐的构建工具，具体可查看 [TypeScript - Building](https://github.com/microsoft/TypeScript#building)，简单的 Gulp 配置可查看 [TypeScript 中文网 - Gulp](https://www.tslang.cn/docs/handbook/gulp.html)。

由于算法的函数工具库功能非常单一简单，因此采用 TypeScript 官方推荐的 Gulp 工具进行构建即可满足需求。

> 温馨提示：构建工具，更多可以了解 [esbuild](https://github.com/evanw/esbuild)、[parcel](https://github.com/parcel-bundler/parcel)以及 [backpack](https://github.com/jaredpalmer/backpack) 等构建工具。当然如果你想要更多了解这些构建工具的差异以及在什么项目环境下应该做如何选型，可以自行搜索前端构建工具的对比或差异，这里推荐一篇个人觉得总结不错的文章 [前端构建：3 类 13 种热门工具的选型参考](https://segmentfault.com/a/1190000017183743)。

#### TypeScript 配置

本项目会构建输出 CommonJS 工具包（npm 包）供外部使用，采用 TypeScript 设计并输出声明文件有助于外部更好的使用该资源包。TypeScript 编译采用官方文档推荐的 Gulp 工具，配合 [gulp-typescript](https://github.com/ivogabe/gulp-typescript) 和 [tsconfig.json](https://www.tslang.cn/docs/handbook/tsconfig-json.html) 配置文件，可快速进行项目构建。在根目录下新建 `tsconfig.json` 文件并新增以下配置：

```js
{
  "compilerOptions": {
    // 指定 ECMAScript 目标版本 "ES3"（默认）， "ES5"， "ES6" / "ES2015"， "ES2016"， "ES2017" 或 "ESNext"。
    "target": "ES5",
    // 构建的目标代码删除所有注释，除了以 /!* 开头的版权信息
    "removeComments": true,
    // 可配合 gulp-typescript 生成相应的 .d.ts 文件
    "declaration": true,
    // 启用所有严格类型检查选项。启用 --strict 相当于启用 --noImplicitAny, --noImplicitThis, --alwaysStrict， --strictNullChecks, --strictFunctionTypes 和 --strictPropertyInitialization
    "strict": true,
    // 禁止对同一个文件的不一致的引用
    "forceConsistentCasingInFileNames": true,
    // 报错时不生成输出文件
    "noEmitOnError": true
  }
}
```

> 温馨提示：这里没有新增 `module` 配置信息，因为默认输出 CommonJS 规范，更多关于 TypeScript 配置信息可查看[TypeScript 官方文档 / 编译选项](https://www.tslang.cn/docs/handbook/compiler-options.html)。如果对于 CommonJS 和 ES6 规范的区别不是很清晰，这里有一篇非常好的文档可以供大家阅读：[ES modules: A cartoon deep-dive](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)、[ES6 modules](https://github.com/rollup/rollup/wiki/ES6-modules)、以及 [pkg.module](https://github.com/rollup/rollup/wiki/pkg.module)。

同时在根目录下新建 `gulpfile.js` 文件：

```javascript
const gulp = require("gulp");
const ts = require("gulp-typescript");
const tsProject = ts.createProject("tsconfig.json");
// 输出 CommonJS 规范到 dist 目录下
gulp.task("default", function () {
  const tsResult = tsProject.src().pipe(tsProject());
  return tsResult.js.pipe(gulp.dest("dist"));
});
```

在 `package.json` 中新增 script 脚本：

```javascript
"scripts": {
  "build": "rimraf dist && gulp"
},
```

其中 [rimfaf](https://github.com/isaacs/rimraf) 用于构建之前清除 dist 目录文件内容。此时在 `src` 目录下新增 TypeScript 源码并使用 `npm run build` 命令可以进行项目构建并输出 CommonJS 规范的目标代码到 `dist` 目录下。

除此之外，此项目希望可以快速生成声明文件供外部进行代码提示，此时仍然可以借助 `gulp-typescript` 工具自动生成声明文件。在 `gulpfile.js` 中新增以下配置

```javascript
const gulp = require("gulp");
const ts = require("gulp-typescript");
const tsProject = ts.createProject("tsconfig.json");
const merge = require("merge2");
// 输出 CommonJS 规范到 dist 目录下
gulp.task("default", function () {
  const tsResult = tsProject.src().pipe(tsProject());
  return merge([
    tsResult.dts.pipe(gulp.dest("types")),
    tsResult.js.pipe(gulp.dest("dist")),
  ]);
});
```

修改 `build` 命令使其在构建之前同时可以删除 `types` 目录：

```javascript
"scripts": {
  "build": "rimraf dist types && gulp",
},
```

再次执行 `npm run build` 会在项目根目录下生成 `types` 文件夹，该文件夹主要存放自动生成的 TypeScript 声明文件。

需要注意发布 npm 包时默认会将当前项目的所有文件进行发布处理，但是这里希望发布的包只包含使用者需要的编译文件 `dist` 和 `types`，因此可以通过`package.json` 中的 [`files`](https://docs.npmjs.com/files/package.json#files)（用于指定发布的 NPM 包包含哪些文件） 字段信息进行控制：

```javascript
"files": [
  "dist",
  "types"
],
```

> 温馨提示：发布的 npm 包中某些文件将忽视 `files` 字段信息，包括 `package.json`、`LICENSE`、`README.md` 等。
除此之外，如果希望发布的 npm 包通过 `require('algorithms-utils')` 或 `import` 形式引入时指向 `dist/index.js` 文件，需要配置 `package.json` 中的 [`main`](https://docs.npmjs.com/files/package.json#main) 字段信息：

```javascript
"main": "dist/index.js"
```

> 温馨提示： 对于工具包使用全量引入的方式并不是一个好的选择，可以通过具体的工具方法进行按需引入。

### Rollup

- @rollup/plugin-node-resolve: Rollup插件，使用节点解析算法定位模块，用于在node_modules中使用第三方模块
- @rollup/plugin-babel: 使用babel
- @rollup/plugin-commonjs: Rollup插件将CommonJS模块转换为ES6，这样它们就可以包含在Rollup包中
- @rollup/plugin-typescript: 用于在Rollup和Typescript之间无缝集成

```bash
# 安装 babel相关

yarn add -D @babel/preset-env
```

- @microsoft/api-extractor: 是为了把所有的 .d.ts 合成一个，并且，还是可以根据写的注释自动生成文档。

### Eslint

》 TypeScript 的代码检查工具主要有 TSLint 和 ESLint 两种。早期的 TypeScript 项目一般采用 TSLint 进行检查，TSLint 和 TypeScript 采用同样的 AST 格式进行编译，但主要问题是对于 JavaScript 生态的项目支持不够友好，因此 TypeScript 团队在 2019 年宣布全面转向 ESLint，（具体可查看 TypeScript 官方仓库的 [`.eslintrc.json`](https://github.com/microsoft/TypeScript/blob/master/.eslintrc.jso) 配置）, 更多关于转向 ESLint 的原因可查看：

- <https://medium.com/palantir/tslint-in-2019-1a144c2317a9>
- <https://github.com/microsoft/TypeScript/issues/30553>

TypeScript 和 ESLint 使用不同的 AST 进行解析，因此为了在 ESLint 中支持 TypeScript 代码检查需要制作额外的[自定义解析器](https://cn.eslint.org/docs/developer-guide/working-with-custom-parsers)（Custom Parsers，ESLint 的自定义解析器功能需要基于 [ESTree](https://github.com/estree/estree)），目的是为了能够解析 TypeScript 语法并转成与 ESLint 兼容的 AST。[@typescript-eslint/parser](https://github.com/typescript-eslint/typescript-eslint#getting-started--installation) 在这样的背景下诞生，它会处理所有 ESLint 特定的配置并调用 [@typescript-eslint/typescript-estree](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/typescript-estree) 生成 ESTree-compatible AST（需要注意的是不仅仅兼容 ESLint，也能够兼容 Prettier）。

`@typescript-eslint` 是一个 Monorepo 体系结构的仓库，采用 [Learn](https://github.com/lerna/lerna) 进行设计，除了上述提到的 NPM 包之外，还包含以下两个重要的 NPM 包：

- [@typescript-eslint/eslint-plugin](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin): 配合 `@typescript-eslint/parser` 一起使用的 ESLint 插件，可以设置 TypeScript 的校验规则。
- [@typescript-eslint/eslint-plugin-tslint](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin-tslint): TSLint 向 ESLint 迁移的插件。

> 温馨提示：如果你正在使用 TSLint，并且你希望兼容 ESLint 或者向 ESLint 进行过渡（TSLint 和 ESLint 并存）， 可查看 [Migrating from TSLint to ESLint](https://github.com/typescript-eslint/typescript-eslint#migrating-from-tslint-to-eslint)。除此之外，以上所介绍的这些包发布时版本一致（为了联合使用的适配性），如果还有什么需要注意的话你可能需要关心一下 `@typescript-eslint` 对于 TypeScript 和 ESLint 的版本支持性，更多可查看该库包的 @typescript-eslint/parser 的仓库信息。

#### 配置过程

从背景的介绍中可以理解，对于全新的 TypeScript 项目（直接抛弃 TSLint）需要包含解析 AST 的解析器 @typescript-eslint/parser 和使用校验规则的插件 @typescript-eslint/eslint-plugin，这里需要在项目中进行安装

```javascript
npm i --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

在根目录新建 `.eslintrc.js` 配置文件，并设置以下配置：

```javascript
module.exports = {
  root: true,
  parser: "@typescript-eslint/parser",
  plugins: ["@typescript-eslint"],
  extends: ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
};
```

其中：

- `parser: '@typescript-eslint/parser'`：使用 ESLint 解析 TypeScript 语法
- `plugins: ['@typescript-eslint']`：在 ESLint 中加载插件 `@typescript-eslint/eslint-plugin`，该插件可用于配置 TypeScript 校验规则。
- `extends: [ ... ]`：在 ESLint 中使用[共享规则配置](https://cn.eslint.org/docs/developer-guide/shareable-configs)，其中 `eslint:recommended` 是 ESLint 内置的推荐校验规则配置（也被称作最佳规则实践），`plugin:@typescript-eslint/recommended` 是类似于 `eslint:recommended` 的 TypeScript 推荐校验规则配置。

> 温馨提示：如果你稍微阅读一下 recommanded 源码你会发现，其实内部可以理解为推荐校验规则的集合。因此如果想基于 `@typescript-eslint/eslint-plugin` 进行自定义规则，则可参考 [TypeScript Supported Rules](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin#supported-rules)。
配置完成后在 `package.json` 中设置校验命令

```javascript
"scripts": {
  "lint": "eslint src",
}
```

此时如果在 `src` 目录下书写错误的语法，执行 `npm run lint` 就会输出错误信息：

```javascript
> eslint src
C:\Code\Git\algorithms\src\greet.ts
  2:16  warning  Missing return type on function  @typescript-eslint/explicit-module-boundary-types
✖ 1 problem (0 errors, 1 warning)
```

> 温馨提示：输出的错误信息是通过 [ESLint Formatters](https://cn.eslint.org/docs/user-guide/formatters/) 生成，查看 ESLint 源代码并调试可发现默认采用的是 [stylish](https://cn.eslint.org/docs/user-guide/formatters/#stylish) formatter。

#### ESLint 插件

如果不使用插件，很难发现写的代码可能存在 TypeScript 格式错误（除非手动 `npm run lint` 或监听代码的变更并实时运行 `npm run lint`），此时可以通过 VS Code 插件进行处理。安装 ESLint 插件后可进行代码的实时提示，具体如下图所示：

当然为了防止不需要被校验的文件出现校验信息（例如配置文件），可以通过 `.eslintignore` 文件进行配置（例如以下是目前为止产生的一些配置文件）：

```bash
# gulp
gulpfile.js
# eslint
.eslintrc.js
# commitizen
commitlint.config.js
# jest
jest.config.js
# build
dist
```

此时可以发现之前执行 `lint` 命令的错误通过插件的形式可实时在 VS Code 编辑器中进行显示。除此之外，一些简单的 ESLint 格式错误（例如 多余的`;` 等）可通过配置 Save Auto Fix 进行保存自动格式化处理。具体 VS Code 的配置可参考 [ESLint 插件](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)的文档说明，这边应该需要进行如下配置：

``` javascript
"editor.codeActionsOnSave": {
  "source.fixAll": true,
  "source.fixAll.eslint": true
}
```

> 温馨提示：VS Code 的配置分为两种类型（用户和工作区），针对上述通用的配置主要放在用户里，针对不同项目的不同配置则需要放入工作区进行处理。

除此之外，需要在构建前进行 ESLint 校验，一旦 ESLint 校验不通过则不允许进行源码的构建操作：

```javascript
"scripts": {
  "lint": "eslint src --max-warnings 0",
  "build": "npm run lint && rimraf dist types && gulp",
}
```

需要注意在构建时需要进行校验的严格控制，一旦 lint 抛出 warning 或者 error 则立马终止构建（详情可查看 [ESLint 退出代码](https://cn.eslint.org/docs/user-guide/command-line-interface#exit-codes)）。

> 温馨提示：需要注意 Shell 中的 `&&` 和 `&` 是有差异的，`&&` 主要用于顺序执行，如果其中一个脚本失败退出那么整个组合脚本执行失败，`&` 主要用于并发执行，表示两个脚本同时执行。这里构建的命令需要等待 `lint` 命令执行通过才能进行，一旦 `lint` 失败那么构建命令将不再执行。

#### ESLint Pre-Commit Hook

尽管可能配置了 ESLint 的校验脚本 以及 VS Code 插件，但是有些 ESLint 的规则校验是无法通过 Save Auto Fix 的（例如质量规则），因此还需要一层保障能够确保代码提交之前所有的代码能够 ESLint 校验，这个配置将在 Lint Staged 中讲解。

### Prettier

#### Prettier 背景

Prettier 是一个统一代码格式风格的工具，如果你不清楚为什么需要使用 Prettier，可以查看 [Why Prettier?](https://prettier.io/docs/en/why-prettier.html)。很多人可能疑惑，ESLint 已经能够规范我们的代码风格，为什么还需要 Prettier？在 [Prettier vs Linters](https://prettier.io/docs/en/comparison.html) 中详细说明了两者的区别，Linters 有两种类型的规则：

- 格式规则（Formatting rules）：例如 [max-len](https://eslint.org/docs/rules/max-len)、[keyword-spacing](https://eslint.org/docs/rules/keyword-spacing)以及 [no-mixed-spaces-and-tabs](https://eslint.org/docs/rules/no-mixed-spaces-and-tabs) 等
- 质量规则（Code-quality rules）：例如 [no-unused-vars](https://eslint.org/docs/rules/no-unused-vars)、[no-implicit-globals](https://eslint.org/docs/rules/no-implicit-globals) 以及 [prefer-promise-reject-errors](https://eslint.org/docs/rules/prefer-promise-reject-errors) 等

ESLint 的规则校验同时包含了 **格式规则** 和 **质量规则**，但是需要注意的是大部分情况下只有 **格式规则** 可以通过 `--fix` 或 VS Code 插件的 Sava Auto Fix 功能一键修复，而 **质量规则** 更多的是发现代码的 Bug 防止代码出错，往往需要手动修复。因此 **格式规则** 并不是必须的，而 **质量规则** 则是必须的。Prettier 与 ESLint 的区别在于 Prettier 专注于统一的**格式规则**，从而减轻 ESLint 在**格式规则上**的校验，而对于**质量规则** 则交给专业的 ESLint 进行处理。总结一句话就是：Prettier for formatting and linters for catching bugs!（ESLint 是必须的，Prettier 是可选的！）

需要注意如果 ESLint（TSLint） 和 Prettier 配合使用时**格式规则**有重复且产生了冲突，那么在编辑器中使用 Sava Auto Fix 时会让你的一键格式化哭笑不得。此时应该让两者把各自注重的规则功能区分开，使用 ESLint 校验**质量规则**，而**格式规则**则交给 Prettier 进行处理，更多信息可查看[Integrating with Linters](https://prettier.io/docs/en/integrating-with-linters.html)。

> 温馨提示：在 VS Code 中使用 ESLint 匹配到相应的规则时会产生黄色波浪线提醒以及红色文件名提醒。Prettier 更希望你对格式规则无感知，从而不会让你觉得有任何使用的负担。如果想要了解更多 Prettier，还可以查看 Prettier 的背后思想 [Option Philosophy](https://prettier.io/docs/en/option-philosophy.html)，个人认为了解一个产品设计的**哲学**能更好的指导你使用该产品。

#### Prettier 配置

首先安装 Prettier 所需要的依赖：

```javascript
npm i  prettier eslint-config-prettier --save-dev
```

其中：

- `[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)`: 用于解决 ESLint 和 Prettier 配合使用时容易产生的**格式规则**冲突问题，其作用就是关闭 ESLint 中配置的一些格式规则，除此之外还包括关闭 `@typescript-eslint/eslint-plugin`、`eslint-plugin-babel`、`eslint-plugin-react`、`eslint-plugin-vue`、`eslint-plugin-standard` 等格式规则。

理论上而言，在项目中开启了 ESLint 的 `extends` 中设置了带有格式规则校验的规则集，那么就需要通过 `eslint-config-prettier` 插件关闭可能产生冲突的相对应的格式规则：

```javascript
{
  "extends": [
    "plugin:@typescript-eslint/recommended",
    // 用于关闭 ESLint 相关的格式规则集，具体可查看 https://github.com/prettier/eslint-config-prettier/blob/master/index.js
    "prettier",
    // 用于关闭 @typescript-eslint/eslint-plugin 插件相关的格式规则集，具体可查看 https://github.com/prettier/eslint-config-prettier/blob/master/%40typescript-eslint.js
    "prettier/@typescript-eslint",
  ]
}
```

配置完成后，可以通过[命令行接口](https://prettier.io/docs/en/cli.html)运行 Prettier:

```javascript
"scripts": {
  "prettier": "prettier src test --write",
},
```

`--write` 参数类似于 ESLint 中的 `--fix`（在 ESLint 中使用该参数还是需要谨慎哈，建议还是使用 VS Code 的 Save Auto Fix 功能），主要用于自动修复格式错误。此时书写格式的错误代码：

```javascript
import great from "@/greet";
// 中间这么多空行
export default {
  great,
};
```

执行 `npm run prettier` 进行格式修复：

```javascript
PS C:\Code\Git\algorithms> npm run prettier
> algorithms-utils@1.0.0 prettier C:\Code\Git\algorithms
> prettier src test --write
src\greet.ts 149ms
src\index.ts 5ms
test\greet.spec.ts 11ms
```

修复之后的的文件格式如下：

```javascript
import great from "@/greet";
export default {
  great,
};
```

需要注意如果某些规则集没有对应的 `eslint-config-prettier` 关闭配置，那么可以先通过 [CLI helper tool](https://github.com/prettier/eslint-config-prettier#cli-helper-tool) 检测是否有重复的格式规则集在生效，然后可以通过手动配置 `eslintrc.js` 的形式进行关闭（例如本项目中配置的 `plugin:jest/recommended` 可能存在规则冲突）：

```javascript
PS C:\Code\Git\algorithms> npx eslint --print-config src/index.ts | npx eslint-config-prettier-check
No rules that are unnecessary or conflict with Prettier were found.
```

例如把 `eslint-config-prettier` 的配置去除，此时进行检查重复规则：

```javascript
PS C:\Code\Git\algorithms> npx eslint --print-config src/index.ts | npx eslint-config-prettier-check
The following rules are unnecessary or might conflict with Prettier:
- @typescript-eslint/no-extra-semi
- no-mixed-spaces-and-tabs
The following rules are enabled but cannot be automatically checked. See:
https://github.com/prettier/eslint-config-prettier#special-rules
- no-unexpected-multiline
```

此时假设 `eslint-config-prettier` 没有类似的关闭格式规则集，那么可以通过配置 `.eslintrc.js` 的形式自己关闭相应冲突的格式规则。

> 温馨提示：ESLint 可以对不同的文件支持不同的规则校验， 因此 `--print-config` 只能对应单个文件的冲突格式规则检查。 由于通常的项目是一套规则对应一整个项目，因此对于整个项目所有的规则只需要校验一个文件是否有格式规则冲突即可。

#### Prettier 插件

通过命令行接口 `--write` 的形式可以进行格式自动修复，但是类似 ESLint，我们更希望项目在实时编辑的时候可以通过保存就自动格式化代码（鬼知道 `--fix` 以及 `--write` 格式了什么文件，当然更希望通过肉眼的形式立即感知格式化），此时可以通过配置 VS Code 的 [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) 插件进行 Save Auto Fix，具体的配置查看插件文档。

#### Prettier Pre-Commit Hook

和 ESLint 一样，尽管可能配置了 Prettier 的自动修复格式脚本 以及 VS Code 插件，但是无法确保格式遗漏的情况，因此还需要一层保障能够确保代码提交之前所有的代码能够进行 Prettier 格式化，这个配置将在 Lint Staged 中讲解，更多配置方案也可以查看 [Prettier - Pre-commit Hook](https://prettier.io/docs/en/precommit.html)。

### Lint Staged

#### Lint Staged 背景

在 Git Commit Message 中使用了 [commitlint](https://commitlint.js.org/#/) 工具配合 husky 可以防止生成不规范的 Git Commit Message，从而阻止用户进行不规范的 Git 代码提交，其原理就是监听了 Git Hook 的执行脚本（会在特定的 Git 执行命令诸如 `commit`、`push`、`merge` 等触发之前或之后执行相应的脚本钩子）。Git Hook 其实是进行项目约束非常好用的工具，它的作用包括但不限于：

- Git Commit Message 规范强制统一
- ESLint 规则统一，防止不符合规范的代码提交（当然也可以包括 Prettier 格式规范以及 Style 样式规范等）
- 代码稳定性提交，提交之前确保测试用例全部通过
- 发送通知
- CI 集成（服务端钩子）

Git Hook 的钩子非常多，但是在客户端钩子中个人最常用的钩子主要是以下两个：

- `pre-commit`：Git 中 `pre` 系列的钩子允许终止即将发生的 Git 操作（`post` 系列往往用作通知行为），`pre-commit` 钩子在键入提交信息（运行 `git commit` 或 `git cz`）前运行，主要用于检查当前即将被提交的代码快照，例如提交遗漏、测试用例以及代码等。该钩子如果以非零值退出则 Git 将放弃本次提交。当然你也可以通过配置命令行参数 `git commit --no-verify` 绕过钩子的运行。
- `commit-msg`：该钩子在用户输入 Commit Message 后被调用，接收存有当前 **Commit Message** 信息的临时文件路径作为唯一参数，因此可以利用该钩子来核对 Commit Meesage 信息（在 Git Commit Message 中使用了该钩子对提交信息进行了是否符合 Angular 规范的校验）。该钩子和 `pre-commit` 类似，一旦以非零值退出则 Git 将放弃本次提交。

除了上述常用的客户端钩子，还有两个常用的服务端钩子（写这个文档的时候顺手学习了一波）：

- `pre-receive`：该钩子会在远程仓库接收 `git push` 推送的代码时执行（注意不是本地仓库），该钩子会比 `pre-commit` 更加有约束力（总会有这样或那样的开发人员不喜欢提交代码时所做的一堆检测，他们可能会选择绕过这些钩子），`pre-receive` 钩子可用于接收代码时的强制规范校验，如果某个开发人员采用了绕过 `pre-commit` 钩子的方式提交了一堆 💩 一样的代码，那么通过设置该钩子可以拒绝代码提交。当然该钩子最常用的操作还是用于检查用于是否有权限推送代码、非快速向前合并等。
- `post-receive`：该钩子在推送代码成功后执行，适合用于发送邮件通知或者触发 CI 。

> 温馨提示：想了解更多 Git Hook 信息可以查看 [Git Hook 官方文档](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90) 或 [Git 钩子：自定义你的工作流](https://github.com/geeeeeeeeek/git-recipes/wiki/5.4-Git-%E9%92%A9%E5%AD%90%EF%BC%9A%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BD%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%B5%81)。

需要注意初始化 Git 之后默认会在 `.git/hooks` 目录下生成所有 Git 钩子的 Shell 示例脚本，这些脚本是可以被定制化的。但是对于前端开发而言去更改这些示例脚本适配前端项目非常不友好（大多数前端开发同学压根不会设计 Shell 脚本，尽管这个对于制作工具是一件非常高效的事情），因此社区就出现了类似的增强工具，它们对外抛出的是简单的钩子配置（例如 [ghooks](https://github.com/ghooks-org/ghooks) 在 `package.json` 中只需要进行简单的[钩子属性配置](https://github.com/ghooks-org/ghooks#setup)），而在内部则通过替换 Git 钩子示例脚本的形式使得外部配置的钩子可以被执行，例如 `[husky](https://github.com/typicode/husky)`、`ghooks` 以及 `[pre-commit](https://github.com/pre-commit/pre-commit)`等。

> 温馨提示： Git Hook 还可以定制脚本执行的语言环境，例如对于前端而言当然希望使用熟悉的 Node 进行脚本设计，此时可以通过在脚本文件的头部设置 `#! /usr/bin/env node` 将 Node 作为可执行文件的环境解释器，如果你之前看过 [使用 NPM 发布和使用 CLI 工具](https://juejin.im/post/5eb89053e51d454de54db501) 可能会对这个环境解析器相对熟悉，这里也给出一个使用 Node 解释器的示例：[ghooks - hook.template.raw](https://github.com/ghooks-org/ghooks/blob/master/lib/hook.template.raw)，ghooks 的实现非常简单，感兴趣的同学可以仔细阅读一些源码的实现。
介绍 Git Hook 是为了让大家清晰的认知到使用 Hook 可以在前端的工程化项目中做很多事情（本来应该放在 Git Commit Message 中介绍相对合适，但是鉴于那个小节引用了另外一篇文章，因此将这个信息放在本小节进行科普）。

之前提到使用 Git Hook 可以进行 ESLint 规范约束，因此大家其实应该能够猜到使用 `pre-commit` 钩子（当然需要借助 Git Hook 增强工具，本项目中一律选择 `husky`）配合 ESLint 可以进行提交说明前的项目代码规则校验，但是如果项目越来越大，ESLint 校验的时间可能越来越长，这对于频繁的代码提交者而言可能是一件相对痛苦的事情，因此可以借助 `lint-staged` 工具（听这个工具的名字就能够猜测 lint 的是已经放入 Git Stage 暂存区中的代码，`ed` 在英文中表明已经做过）减少代码的检测量。

#### Lint Staged 配置

使用 [commitlint](https://commitlint.js.org/#/) 工具可以防止生成不规范的 Git Commit Message，从而阻止用户进行 Git 代码提交。但是如果想要防止团队协作时开发者提交不符合 ESLint 规则的代码则可以通过 [lint-staged](https://github.com/okonet/lint-staged) 工具来实现。`lint-staged` 可以在用户提交代码之前（生成 Git Commit Message 信息之前）使用 ESLint 检查 Git 暂存区中的代码信息（`git add` 之后的修改代码），一旦存在 💩 一样不符合校验规则的代码，则可以终止提交行为。需要注意的是 `lint-staged` 不会检查项目的全量代码（全量使用 ESLint 校验对于较大的项目可能会是一个相对耗时的过程），而只会检查添加到 Git 暂存区中的代码。根据官方文档执行以下命令自动生成配置项信息：

```bash
npx mrm lint-staged
```

需要注意默认生成的配置文件是针对 JavaScript 环境的，手动修改 `package.json` 中的配置信息：

```javascript
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
},
"lint-staged": {
  // 这里需要注意脚本的 --max-warnings 0
  // 否则就算存在 warning 也不会终止提交行为
  "*.ts": "npm run lint"
}
```

此时如果代码有 💩 , 则提交时会提示错误信息且提交会强制失败

### Jest

#### 测试背景

如果对于测试的概念和框架不是特别清楚，这里推荐一些可查看的文章：

- [JavaScript 程序测试](https://javascript.ruanyifeng.com/tool/testing.html) - 全面的测试基础知识
- [New to front-end testing? Start from the top of the pyramid!](https://dev.to/noriste/new-to-front-end-testing-start-from-the-top-of-the-pyramid-36kj) - 重点可以了解一下测试金字塔和测试置信度
- [[译] JavaScript 单元测试框架：Jasmine, Mocha, AVA, Tape 和 Jest 的比较](https://juejin.im/post/5acc721a6fb9a028b77b23c9) - 单元测试框架对比中文版（2018）
- [JavaScript unit testing frameworks in 2020: A comparison](https://raygun.com/blog/javascript-unit-testing-frameworks/) - 单元测试框架对比英文版（2020）
除此之外，如果想了解一些额外的测试技巧，这里推荐一些社区的最佳实践：
- [javascript-testing-best-practices](https://github.com/goldbergyoni/javascript-testing-best-practices/blob/master/readme-zh-CN.md)
- [ui-testing-best-practices](https://github.com/NoriSte/ui-testing-best-practices)
由于这里只是一个简单的 Node 环境的工具库包的单元测试，在对比了各个测试框架之后决定采用 Jest 进行单元测试：
- 内置断言库可实现开箱即用（从 it 到 expect， Jest 将整个工具包放在一个地方）
- Jest 可以可靠地并行运行测试，并且为了让加速测试进程，Jest 会先运行先前失败的测试
- 内置覆盖率报告，无需额外进行配置
- 优秀的报错信息

> 温馨提示：前端测试框架很多，相比简单的 Node 环境测试，e2e 测试会更复杂一些（不管是测试框架的支持以及测试用例的设计）， Karma 测试管理工具配合 Mocha 进行浏览器环境测试， PhantomJS 以及 Nightwatch（使用的都是皮毛），[testcafe](https://github.com/DevExpress/testcafe) 测试框架（复杂的 API 官方文档），除此之外如果还感兴趣，也可以了解一下 [cypress](https://github.com/cypress-io/cypress) 测试框架。

#### Jest 配置

本项目的单元测试主要采用了 [Jest](https://jestjs.io/en/) 测试框架，需要注意 Jest 如果需要对 TypeScript 进行支持，可以通过配合 Babel 的形式，具体可查看 [Jest - Using TypeScript](https://jestjs.io/docs/en/getting-started#using-typescript)，当然这会产生一些限制（具体可查看 [Babel 7 or TypeScript](https://kulshekhar.github.io/ts-jest/user/babel7-or-ts)）。由于本项目没有采用 Babel 进行转译，并且希望能够完美支持类型检查，因此采用 [ts-jest](https://kulshekhar.github.io/ts-jest/user/install#customizing) 进行单元测试。按照官方教程进行依赖安装和项目初始化：

```bash
npm install --save-dev jest typescript ts-jest @types/jest
npx ts-jest config:init
```

对根目录的 `ject.config.js` 进行配置修改（注释部分是新增的配置，后续配置可能还会增加）：

```javascript
module.exports = {
  preset: "ts-jest",
  testEnvironment: "node",
  // 输出覆盖信息文件的目录
  coverageDirectory: "./coverage/",
  // 覆盖信息的忽略文件模式
  testPathIgnorePatterns: ["<rootDir>/node_modules/"],
  // 如果测试覆盖率未达到 100%，则测试失败
  // 这里可用于预防代码构建和提交
  coverageThreshold: {
    global: {
      branches: 100,
      functions: 100,
      lines: 100,
      statements: 100,
    },
  },
  // 路径映射配置，具体可查看 https://kulshekhar.github.io/ts-jest/user/config/#paths-mapping
  // 需要配合 TypeScript 路径映射，具体可查看：https://www.tslang.cn/docs/handbook/module-resolution.html
  moduleNameMapper: {
    "^@/(.*)$": "<rootDir>/src/$1",
  },
};
```

需要注意路径映射也需要配置 `tsconfig.json` 中的 `paths` 信息，同时注意将测试代码包含到 TypeScript 的编译目录中。配置完成后在 `package.json` 中配置测试命令：

```JSON
"scripts": {
  "lint": "eslint src --max-warnings 0",
  "test": "jest --bail --coverage",
  "build": "npm run lint && npm run jest && rimraf dist types && gulp",
}
```

需要注意 Jest 中的这些配置信息（更多配置信息可查看 [Jest CLI Options](https://jestjs.io/docs/zh-Hans/cli)）：

- `bail` 的配置作用相对类似于 ESLint 中的 `max-warnings`，设置为 `true` 则表明一旦发现单元测试用例错误则停止运行其余测试用例，从而可以防止运行用例过多时需要一直等待用例全部运行完毕。
- `coverage` 主要用于在当前根目录下生成 `coverage` 测试目录报告。

> 温馨提示：Jest CLI Options 中的 `findRelatedTests` 可用于配合 `pre-commit` 钩子去运行最少量的单元测试用例，可配合 `lint-staged` 实现类似于 ESLint 的作用，更多细节可查看 [`lint-staged - Use environment variables with linting commands`](https://github.com/okonet/lint-staged#use-environment-variables-with-linting-commands)。

在当前根目录的 `test` 目录下新建 `greet.spec.ts` 文件，并设计以下测试代码：

```javascript
import greet from "@/greet";
describe("src/greet.ts", () => {
  it("name param test", () => {
    expect(greet("world")).toBe("Hello from world 1");
  });
});
```

> 温馨提示：测试文件有两种放置风格，一种是新建 `test` 文件夹，然后将所有的测试代码集中在 `test` 目录下进行管理，另外一种是在各个源码文件的同级目录下新建 `__test__` 目录，进行就近测试，大部分的项目可能都会倾向采用第一种目录结构（可以随便找一些 github 上的开源项目进行查看，这里 `ts-test` 则是采用了第二种测试结构）。除此之外，需要注意的一点是 Jest 通过配置 [`testMatch`](https://jestjs.io/docs/zh-Hans/configuration#testmatch-arraystring) 或 [`testRegex`](https://jestjs.io/docs/zh-Hans/configuration#testregex-string--arraystring) 可以使得项目识别特定格式文件作为测试文件进行运行（本项目采用默认配置可识别后缀为 `.spec` 的文件进行单元测试）。
此时可单独通过执行 `npm run test` 命令进行单元测试，这里演示执行构建命令时的单元测试（需要保证构建之前所有的单元测试用例都能通过）。如果测试失败，那么应该防止继续构建.
温馨提示：想要了解更多关于 Jest 的生态可以查看 [awesome-jest](https://github.com/jest-community/awesome-jest)。

#### ESLint 支持

`src` 目录下的源码通过配置 `@typescript-eslint/eslint-plugin` 可进行推荐规则的 ESLint 校验，为了使得 `test` 目录下的测试代码能够进行符合 Jest 推荐规则的 ESLint 校验，可以通过配置 [eslint-plugin-jest](https://github.com/jest-community/eslint-plugin-jest) 进行支持（`ts-jest` 项目就是采用了该插件进行 ESLint 校验，具体可查看配置文件 [`ts-jest/.eslintrc.js`](https://github.com/kulshekhar/ts-jest/blob/master/.eslintrc.js#L12)），这里仍然采用推荐的规则配置：

```javascript
module.exports = {
  root: true,
  parser: "@typescript-eslint/parser",
  plugins: ["@typescript-eslint"],
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    // 新增推荐的 ESLint 校验规则
    // 所有规则集查看：https://github.com/jest-community/eslint-plugin-jest#rules（recommended 标识表明是推荐规则）
    "plugin:jest/recommended",
  ],
};
```

需要注意修改 `package.json` 中的 ESLint 校验范围：

```javascript
"scripts": {
  // 这里对 src 和 test 目录进行 ESLint 校验
  "lint": "eslint src test --max-warnings 0",
},
```

执行 `npm run lint` 进行单元测试的格式校验

> 温馨提示：如果你希望 Jest 测试的代码需要一些格式规范，那么可以查看 [eslint-plugin-jest-formatting](https://github.com/dangreenisrael/eslint-plugin-jest-formatting) 插件。

### Npm Script Hook

不知道大家有没有发现，当你查看一个前端开源项目的时候第一时间找的是项目的描述文件 `package.json` 中的 `main`、`bin` 以及 `files` 等字段信息，除此之外如果还想深入了解项目的结构，应该还会查看 `scripts` 脚本字段信息用于了解项目的开发、构建、测试以及安装等流程。npm 的脚本功能非常强大，你可以利用脚本制作项目需要的任何流程工具。本文不会过多介绍 npm 脚本的功能，只是讲解一下其中用到的一个 [钩子](https://www.npmjs.cn/misc/scripts/#description) 功能.

npm 除了指定一些特殊的脚本钩子以外（例如 `prepublish`、`postpublish`、`preinstall`、`postinstall`等），还可以对任意脚本增加 `pre` 和 `post` 钩子，通过自定义钩子将并发执行的脚本进行简化.

```javascript
"lint": "eslint src test --max-warnings 0",
"test": "jest --bail --coverage",
"prebuild": "npm run lint && npm run test",
"build": "rimraf dist types && gulp",
"changelog": "rimraf CHANGELOG.md && conventional-changelog -p angular -i CHANGELOG.md -s"
```

此时如果执行 `npm run build` 命令时事实上类似于执行了以下命令：

```bash
npm run prebuild && npm run build
```

> 温馨提示：大家可能会奇怪什么地方需要类似于 `preinstall` 或 `preuninstall` 这样的钩子，举个例子。例如查看 [husky - package.json](https://github.com/typicode/husky/blob/master/package.json)，husky 在安装的时候因为要植入 Git Hook 脚本从而带来了一些副作用（此时当然可以通过 `preinstall` 触发 Git Hook 脚本植入的逻辑）。如果不想使用 husky，那么卸载后需要清除植入的脚本从而不妨碍原有的 Git Hook 功能。 当然如果想要了解更多关于 npm 脚本的信息，可以查看 [npm-scripts](https://www.npmjs.cn/misc/scripts/) 或 [npm scripts 使用指南](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html?utm_source=tuicool&utm_medium=referral)。

### Github Actions

#### CI / CD 背景

个人对于 CI / CD 可能相对不够熟悉（只是简单的玩过 Travis、Gitlab CI / CD 以及 Jenkins ），已经有很多讲解 CI / CD 的好文章，这里就不再过多介绍，有兴趣的同学可以看看以下一些好文：

- [Introduction to CI/CD with GitLab（中文版）](https://s0docs0gitlab0com.icopy.site/ee/ci/introduction/index.html)
- [GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
- [Github Actions 官方文档](https://docs.github.com/en/actions)
- [当我有服务器时我做了什么 · 个人服务器运维指南](https://shanyue.tech/op/#%E9%A2%84%E8%A7%88)（这个系列有点佩服啊）

在 [Introduction to CI/CD with GitLab（中文版）](https://s0docs0gitlab0com.icopy.site/ee/ci/introduction/index.html) 中你可以清晰的了解到 CI 和 CD 的职责功能：

![CI&CD.png](https://raw.githubusercontent.com/ziyi2/algorithms/feat/framework/images/CI%26CD.png)

每一个流程的细节，可发现 Gitlab 在每个阶段可用的功能：

![Deep CI&CD.png](https://raw.githubusercontent.com/ziyi2/algorithms/feat/framework/images/Deep%20CI%26CD.png)

由于本项目依赖 Github，因此没法使用 Gitlab 默认集成的能力。之前的 Github 项目采用了 Travis （之前一直在抱怨 Github 没有 CI / CD 能力）进行项目的 CI / CD 集成，现在因为有了更方便的 Github Actions，因此决定采用 Github 自带的 Actions 进行 CI / CD 能力集成（大家如果想更多了解这些 CI / CD 的差异请自行 Google 哈）。Github Actions 所带来的好处在于：

- 可复用的 Actions（以前你需要写复杂的脚本，现在可以复用别人写好的脚本，可以简单理解为 CI 脚本插件化）
- 支持更多的 [webhook](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

当然也会产生一些[限制](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#usage-limits)，这些限制主要都是和执行时间以及次数相关。需要注意类似于 Jenkins 等支持本地连接运行，Github Actions 也支持连接到本地机器运行 workflow，因此部分限制可能不受本地运行的限制。

本项目中使用到的 CI / CD 功能相对简单，如果想了解通用的 Actions，可查看 [官方 Actions](https://github.com/marketplace?type=actions) 和 [awesome-actions](https://github.com/sdras/awesome-actions)。

> 温馨提示：最近在使用 Jenkins 做前端的自动化构建优化，后续可能会出一篇简单的教程文章（当然会跟普通讲解的用法会有所不同喽）。

#### Github Actions 配置

本项目的 CI 配置可能会包含三个方面：

- 开发并更新静态资源站点流程
- 发布库包流程
- 提交 Pull Request 流程

这里主要讲解开发并更新静态资源站点，大致需要分为以下几个步骤（以下都是在 Github 服务器上进行操作，你可以理解为新的容器环境）：

- 拉取当前 Github 仓库并切换到相应的分支（默认 Git 就不需要安装啦）
- 安装 Node 和 Npm 环境
- 安装项目的依赖
- 构建库包和静态资源站点
- 发布静态资源

通过查看 [官方 Actions](https://github.com/marketplace?type=actions) 和 [awesome-actions](https://github.com/sdras/awesome-actions)，找到所需的 Actions:

- [Checkout](https://github.com/actions/checkout): 从 Github 拉取仓库代码到 Github 服务器的 `$GITHUB_WORKSPACE` 目录下
- [cache](https://github.com/actions/cache): 缓存 npm
- [setup-node](https://github.com/actions/setup-node): 安装 Node 和 Npm 环境
- [actions-gh-pages](https://github.com/peaceiris/actions-gh-pages): 在 Github 上发布静态资源

> 温馨提示：可用的 Action 很多，这里只是设置了一个简单的流程。
在 `.github/workflows` 下新增 `mian.yml` 配置文件
温馨提示：这里不再叙述具体的配置过程，更多可查看配置文件中贴出的链接信息。

上传 CI 的配置文件后，Github 就会进行自动构建，具体如下：

![workflows.png](https://raw.githubusercontent.com/ziyi2/algorithms/feat/framework/images/Workflows.png)

正在构建或者构建完成后可查看每个构建的信息，如果初次构建失败则可以通过构建信息找出失败原因，并重新修改构建配置进行再次构建，每次构建失败 Github 都会通过邮件的形式进行通知：

![fail.png](https://raw.githubusercontent.com/ziyi2/algorithms/feat/framework/images/Fail.jpg)

如果构建成功，则每次你推送新的代码后，Github 服务会进行一系列流程并自动更新静态资源站点。

## 总结

希望大家看完这篇文档之后如果想使用其中某些工具能够养成以下一些习惯：

- 通篇阅读工具对应的官方 Github README 文档以及官方站点文档（如果有站点文档），了解该工具设计的核心哲学、核心功能、解决什么核心问题。前端的工具百花齐放，同样的功能可能可以采用多种不同的工具实现，如果想要在项目中使用得当，就得知道这些工具的差异，就需要完整的阅读相应的官方文档，并考虑当前工具是否匹配你的项目环境。切忌大家在用什么你就用什么，需要有自己对于该工具使用的一些思考。比如之前在设计 UI 组件库的时候采用了 [Lerna](https://github.com/lerna/lerna)，但是当时并不明白它带来的一些弊端，一定需要考虑清楚在什么环境下使用它绝对合适！
- 在完整调研了各个工具的差异之后，就可以开始选择认为合适的工具（不一定是最新的工具）开始实践，在实践的过程中你会对该工具的使用越来越熟悉。此时如果遇到一些问题或者想要实现某些功能，在通篇阅读文档的基础上会变得相对容易。当然如果遇到了一些报错信息无法解决，此时第一个动作应该是去搜索当前工具所对应的 Github Issues（切忌养成一来就搜索 Baidu Or Google 的习惯），如果没有搜索到相应的 Issues 你可以立马提一个新的 Issue，如果工具库的维护者发现重复的 Issue 他会指引你到相应的 Issue，如果这个问题确实是新发现的，那么很好，你给该工具做了一个很好的贡献。当然除此之外，你也可以根据错误的堆栈信息追踪工具的源码，了解源码之后可能你就清晰了错误信息产生的原因以及如何规避它。
- 在完成以上两步之后，就可以开始开始总结工具的使用技巧了，此时如果你重新阅读工具的官方文档，或许会产生一些新的认知或思考。

## 文档

- [Npm 官方文档](https://docs.npmjs.com/)
- [使用 NPM 发布和使用 CLI 工具](https://juejin.im/post/5eb89053e51d454de54db501)
- [前端构建：3 类 13 种热门工具的选型参考](https://segmentfault.com/a/1190000017183743)
- [Cz 工具集使用介绍](https://juejin.im/post/5cc4694a6fb9a03238106eb9)
- [ES modules: A cartoon deep-dive](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)（强烈推荐阅读）
- [JavaScript 程序测试](https://javascript.ruanyifeng.com/tool/testing.html)
- [New to front-end testing? Start from the top of the pyramid!](https://dev.to/noriste/new-to-front-end-testing-start-from-the-top-of-the-pyramid-36kj)
- [JavaScript & Node.js Testing Best Practices](https://github.com/goldbergyoni/javascript-testing-best-practices/blob/master/readme-zh-CN.md)
- [[译] JavaScript 单元测试框架：Jasmine, Mocha, AVA, Tape 和 Jest 的比较](https://juejin.im/post/5acc721a6fb9a028b77b23c9)
- [JavaScript unit testing frameworks in 2020: A comparison](https://raygun.com/blog/javascript-unit-testing-frameworks/)
- [javascript-testing-best-practices](https://github.com/goldbergyoni/javascript-testing-best-practices/blob/master/readme-zh-CN.md)
- [ui-testing-best-practices](https://github.com/NoriSte/ui-testing-best-practices)
- [npm scripts 使用指南](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html?utm_source=tuicool&utm_medium=referral)
- [技术文章的写作技巧分享](https://juejin.im/post/5ecbdff6e51d45783e17a7a1)
- [Introduction to CI/CD with GitLab（中文版）](https://s0docs0gitlab0com.icopy.site/ee/ci/introduction/index.html)
- [GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
- [当我有服务器时我做了什么 · 个人服务器运维指南](https://shanyue.tech/op/#%E9%A2%84%E8%A7%88)
