---
title: Vue|VSCode|ESLint+autofix最简配置
date: 2021-10-04 09:56:35
categories: coding
tags: [ide]
---

> 定个小目标 ⛳️：实现用 VS Code 编辑默认 vue cli 创建的 vue2 项目，对.vue, .js 等文件有错误检查与代码风格检查(lint)、保存时自动修复(autofix)、按照 Prettier 风格进行格式化。

上网搜索 vscode 的 vue 项目 lint 和格式化，大多文章介绍的是其团队自己一揽子规范，很多规则不通用，还有很多过时、无效的配置也一并贴出来（当前为 2021 年），看得我头都大了 🤯

我只想要一个 vue、eslint、prettier 三者结合的官方默认配置，怎么这么麻烦？？
于是我决定痛下决心，分别查询各个官网以寻求最简配置 ⚙️

> ✅Tips: 只关心配置的朋友，直接拉到最后。

## 问题的解决思路

经过一轮探索与思考，要想实现上述目标，我们其实要解决三个问题：

1. 代码检查(Lint)，包括错误检查与风格检查
2. 在 Lint 之后做代码自动修复(autofix)
3. 使用代码格式化工具(formatter)去做 autofix

具体到 VS Code 这款编辑器的配置，我们需要搞定以下概念以及解决方案：

- 概念
  - Linter 与 Formatter 的区别？
- VS Code 相关
  - VS Code 如何识别 Vue SFC(.vue 单文件) 文件？
  - VS Code 如何识别项目的 eslint 配置，并在编辑器中提示错误？
  - VS Code 如何在保存时自动修复 eslint 错误？
  - VS Code 如何通过 Prettier 这款格式化工具，来自动修复 eslint 错误？
  - 如何解决 ESLint、Prettier 之间的规则冲突？
- 项目配置相关
  - vue cli 创建的默认 eslint 配置到底包含了哪些规则？
  - 如何设置 Eslint 的代码错误规则？
  - 如何设置 Eslint 的代码风格 Prettier 的规则？

## Linter 与 Formatter 的区别？

我们首先讲下什么是 Linter：
Linter 是语法检查/代码质量检查工具，不同语言都有自己的版本，比如 JavaScript 的 Linter 有 ESLint，JSLint，Python 有 pylint...而 JS 中目前最常用的就是 ESLint，通过配置规则+ESLint 的命令行工具(CLI)，可以实现代码检错、风格检错、自动修复错误等能力。

然后是 Formatter：
Formatter 是指代码格式化工具，这类工具会帮你把代码按照规则进行统一的格式化，保证项目代码的美观、统一。前端有代表性的 Formatter 有 Prettier、Beautify 等，他们都可以规范 HTML、CSS、JS 等前端语言。

那么 Linter 与 Formatter 有什么区别呢？参考 Prettier 官网的解释：[Prettier vs. Linters · Prettier](https://prettier.io/docs/en/comparison.html)，我们可以得知 Linter 主要有两类配置：

- 代码格式化相关(Formatting rules) :
  - eg: max-len, no-mixed-spaces-and-tabs, keyword-spacing, comma-style…
- 代码质量相关(Code-quality rules):
  - eg no-unused-vars, no-extra-bind, no-implicit-globals, prefer-promise-reject-errors…
    而 Formatter 如 Prettier，只关心代码格式化的规则，完全不关心代码质量的规则。

所以我们应该发挥各自所长，在项目中：

- 使用 Linter 来做代码质量检查
- 在 Linter 中配置 Formatter 的规则，使用 Linter 来做代码风格检查
- 使用 Formatter 来做代码的格式化

## 💪 实战：从零开始配置

> 环境说明：
>
> - Mac OS 10.15.7
> - vs code 1.50.1
> - @vue/cli 4.5.12
> - 有 eslint 配置的 vue2 项目

### 初始化 vue2 项目

场景是这样的，通过 @vue/cli 创建一个默认的 vue2 项目，包括 eslint、babel 都是最基础的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144533342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)创建之后，用没装什么插件的 vs code 打开项目：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144557609.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

### 安装必要 vs code 插件

看到.vue 后缀的文件默认是没有语法高亮的，我们需要安装 `vetur` 插件来支持
![](https://img-blog.csdnimg.cn/20210403144624365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
安装完之后，vscode 即支持 vue SFC 文件的语法高亮：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144651977.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
什么？？居然没有错误提示，明明 vue/cli 默认帮我们创建了 eslint 配置的！

### 代码质量错误检测

其实这里要安装 vs code 的插件 `ESLint`，他能读取项目中的 eslint 配置，并告诉 vs code，vs code 才可以在编辑器中找到错误并提示出来！
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021040314471268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
安装完之后，回到`App.vue`，可以看到错误的代码有红色波浪线提示，鼠标悬浮还有具体错误原因，eslint(no-undef)，这里说明 vscode 已经可读取项目的 eslint 配置，并按照 no-undef(不允许存在未定义的变量)这条规则定位错误！注意，**这是属于代码质量类错误**。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144731133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
对于 vue 的 template，代码质量类错误也是可以检测出来：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144746895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
可以看到这里错误规则来源于`eslint-plugin-vue`，这是因为 vue cli 创建的 eslint 规则中，包含了两条继承的规则：

- `eslint:recommended`: 负责检查 JS 错误(eslint)，来自 `eslint` 包

- `plugin:vue/essential`：负责检查 vue 的错误(eslint-plugin-vue)，来自 `eslint-plugin-vue` 包
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144809209.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

### 代码风格类错误检测

接下来我们测试下 **代码风格类错误** ，在 template 和 script 中都打上多余的空格：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144834909.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
好家伙，这么丑的代码都不报错，还有没有王法了？！为什么呢，我们不是有配置 eslint 规则吗？怀疑到了我们的两条规则：`eslint:recommended`，`plugin:vue/essential`，查阅资料发现：

[List of available rules - ESLint - Pluggable JavaScript linter](https://eslint.org/docs/rules/) 告诉我们：`eslint:recommended` 只包含了必要的代码质量规则，对于风格规则，完全不在意。

[User Guide | eslint-plugin-vue](https://eslint.vuejs.org/user-guide/#usage)告诉我们：`plugin:vue/essential`只包含了 vue 在 ESLint 的解析、以及 vue 错误的检查，对于风格规则也是完全不 care。如果我们想加上 vue 的代码风格规范，应该使用 "plugin:vue/recommended" 规则集。
我们只需改一下 eslint 配置：

```js
"eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
+     "plugin:vue/recommended"
-     "plugin:vue/essential",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {}
  },
```

再回到编辑器，可以发现 template 的代码风格校验告警了，而 script 中仍然没有提示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021040314485646.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
其实也很容易想到，当前 eslint 的规则集里缺少 JS 的风格校验规则，是 Prettier 登场的时候了！ 我们希望 eslint 里能写入 prettier 的规则，这里需要在项目里安装 [eslint-plugin-prettier-vue](https://www.npmjs.com/package/eslint-plugin-prettier-vue) NPM 依赖，根据其 NPM 文档指引，我们在项目里安装：

```
npm install --save-dev \
  eslint-plugin-prettier-vue \
  eslint-plugin-vue \
  eslint-config-prettier \
  eslint \
  prettier
```

- prettier: 是 prettier 的核心包
- eslint: 是 eslint 的核心包
- eslint-plugin-vue：是 vue 的 eslint 插件，包含多个规则集
- eslint-plugin-prettier-vue：是 prettier fo vue 的 eslint 插件，包含 prettier 风格检测规则集
- [eslint-config-prettier](https://www.npmjs.com/package/eslint-config-prettier)：禁用 linter 与 prettier 之间会产生冲突的部分规则，保证 Prettier 与 ESLint 不冲突

安装完后我们再为 ESLint 添加风格校验规则集：

```js
"eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
	    "plugin:vue/recommended",
+     "plugin:prettier-vue/recommended",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {}
  },
```

再回到编辑器，已经可以检测 template, script, style 所有的风格错误了！
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144915196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

### 开启 ESLint Autofix

检测出错误，我们该修复错误了，我们可以手动修复（太笨），也可以命令行执行 `eslint --fix`（太麻烦），有没有更舒服地方式，有，在保存文件时自动修复风格错误！

要开启此特性，我们在 vscode 中，打开 `偏好设置preference`，点击切换到 JSON 配置文件模式：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403144930610.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
在配置中添加：

```json
// 开启代码保存时，eslint执行fix动作
"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true
},
```

保存后回到编辑器，按`ctrl+s`保存，可以发现代码根据`prettier`的规则，一口气格式化好了！
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021040314494851.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
至此，ESLint 将怀揣 vue 与 js 的质量规则+prettier for vue 的风格规则，帮我们检测代码异常，并自动修复风格问题 🤩

正式宣布，我们完成了在 vscode 下，编辑**有 eslint 配置 的 vue2 项目**，拥有代码质量检测+代码风格检测以及自动根据 prettier 规则修复的目标了！

### 其他说明

#### autofix 的范围

能自动修复的只有代码风格类错误（缩进，换行），代码质量类是没法自动修复的（你当他 AI 吗？自动帮你修 bug）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403145007876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)
如上图，输入 aaa 检测出一条质量问题(no-undef)，一条风格问题(缺少;号)，点击保存，只会帮你把分号加上 😥
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210403145058802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JydXNraQ==,size_16,color_FFFFFF,t_70)

#### 关于 prettier 风格覆盖

在 eslint 配置中的 `rules` 字段配即可(更多字段查询https://eslint.org/docs/rules/)，例如想覆盖 prettier 的双引号规则为单引号:

```js
rules: {
    'prettier-vue/prettier': [
      'error',  // 不符合规则的设为错误，好让eslint修复
      {
        // Override all options of `prettier` here
        // @see https://prettier.io/docs/en/options.html
        singleQuote: true,
      },
    ],
  },
```

## 疑问总结

- VS Code 相关

  - VS Code 如何识别 Vue SFC(.vue 单文件) 文件？
    - 通过 `vetur` 插件
  - VS Code 如何识别项目的 eslint 配置，并在编辑器中提示错误？
    - 通过 `Eslint` 插件
  - VS Code 如何在保存时自动修复 eslint 错误？
    - 偏好配置中开启 ：`editor.codeActionsOnSave` -> `"source.fixAll.eslint": true`
  - VS Code 如何通过 Prettier 这款格式化工具，来自动修复 eslint 错误？
    - 项目安装 `prettier`, `eslint`, `eslint-plugin-prettier-vue`, `eslint-config-prettier `
    - eslint 配置中继承 `plugin:prettier-vue/recommended` 规则
  - 如何解决 ESLint、Prettier 之间的规则冲突？
    - 安装 eslint-config-prettier 依赖，禁用冲突的规则，需要搭配其他包使用，如 `eslint-plugin-prettier-vue`

- 项目配置相关

  - vue cli 创建的默认 eslint 配置到底包含了哪些规则？

    - `eslint:recommended`: 负责检查 JS 错误(eslint)，不含风格检测，来自 `eslint` 包

    - `plugin:vue/essential`：负责检查 vue 的必要错误(eslint-plugin-vue)，不含风格检测，来自 `eslint-plugin-vue` 包

  - 如何设置 Eslint 的代码错误规则？

    - 继承已有的规则集，或通过 rules 手动配置

  - 如何设置 Eslint 的代码风格 Prettier 的规则？

    - 通过`plugin:prettier-vue/recommended`继承 Prettier 的规则集

## 配置总结

VS Code 插件：

- `Vetur`: 让 vscode 识别 vue SFC 文件，语法高亮、代码检测等
- `ESLint` 让 vscode 读取项目 eslint 配置，并在编辑器中提示语法错误、应用 autofix 等

NPM 开发依赖：

- `prettier:` 是 prettier 的核心包
- `eslint`: 是 eslint 的核心包
- `eslint-plugin-vu`e：是 vue 的 eslint 插件，包含多个规则集
- `eslint-plugin-prettier-vue`：是 prettier fo vue 的 eslint 插件，包含 prettier 风格检测规则集
- [eslint-config-prettier](https://www.npmjs.com/package/eslint-config-prettier)：禁用 linter 与 prettier 之间会产生冲突的部分规则，保证 Prettier 与 ESLint 不冲突

ESLint 配置：

```json
"eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
      "plugin:vue/recommended",
      "plugin:prettier-vue/recommended",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {
      "prettier-vue/prettier": [
        "error",
        {
          "singleQuote": true
        }
      ]
    }
  }
```

VS Code 配置

```json
// 开启代码保存时，eslint执行fix动作
"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true
}
```

以上就是 Vue 项目在 vscode 中 lint/autofix/format 的最简配置 🤓 装备好武器，开始撸代码吧 👨🏻‍💻

> 参考网站：
> Prettier vs. Linters · Prettier: https://prettier.io/docs/en/comparison.html
> ESLint rules: https://eslint.org/docs/rules/
> eslint-plugin-vue: https://eslint.vuejs.org/user-guide/#usage
> eslint-plugin-prettier-vue: https://www.npmjs.com/package/eslint-plugin-prettier-vue
