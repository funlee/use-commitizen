use-commitizen
-----------------
本文讲述如何使用工具来规范化编写 Git 的 Commit message 。

### commit message 的作用
- 提供更多的历史信息，方便快速浏览。
- 可以过滤某些 commit（比如文档改动），便于快速查找信息。
- 可以直接从 commit 生成 Change log。

### commit message 的格式
我们采用 [Angular](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.greljkmo14y0 "Git Commit Message Conventions") 规范，这是目前使用最广的写法，比较合理和系统化，并且有配套的工具，下面介绍这套规范的格式。

每次提交，Commit message 都包括三个部分：Header，Body 和 Footer。
```jsx
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>

```

其中，Header 是必需的，Body 和 Footer 可以省略。

不管是哪一个部分，任何一行都不得超过72个字符（或100个字符），这是为了避免自动换行影响美观。

#### Header
Header 包括三个字段：type（必填）、scope（选填）和 subject（必填）。

type，用于说明 commit 的类别，只允许使用下面 7 个标识：

- feat：新功能（feature）
- fix：修补bug
- docs：文档（documentation）
- style： 格式（不影响代码运行的变动）
- refactor：重构（即不是新增功能，也不是修改bug的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动

type 为 feat 和 fix，则该 commit 将肯定出现在 Change log 之中。

scope：用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

subject：是 commit 目的的简短描述，字符不应该太长，否则影响美观。
- 以动词开头，使用第一人称现在时，比如 change，而不是 changed 或 changes
- 第一个字母小写
- 结尾不加句号（.）

#### body
Body 部分是对本次 commit 的详细描述，可以分成多行。下面是一个范例。
```
More detailed explanatory text, if necessary.  Wrap it to 
about 72 characters or so. 

Further paragraphs come after blank lines.

- Bullet points are okay, too
- Use a hanging indent
```

#### Footer
Footer 部分只用于两种情况
1. 不兼容变动
如果当前代码与上一个版本不兼容，则 Footer 部分以 BREAKING CHANGE 开头，后面是对变动的描述、以及变动理由和迁移方法。

2. 关闭 Issue
如果当前 commit 针对某个 issue，那么可以在 Footer 部分关闭这个 issue

### 使用 Commitizen
下面介绍利用 Commitizen 如何自动生成符合上述规范的 commit message。

先全局安装 commitizen ：

```bash
$ npm install -g commitizen

```

在项目目录里，运行下面的命令，使其支持 Angular 的 Commit message 格式。

```bash
$ commitizen init cz-conventional-changelog --save --save-exact

```

接下来在提交的时候就可以使用 `git cz` 就可以根据提示，生成自动化的 commit message 。
```bash
$ git add .

$ git cz

```

### 添加 Commitlint 校验
Commitizen 可以帮助我们规范自己的 commit-message，但是在团队合作中，如何规范其他成员的 commit 规范呢？

以前可以使用 validate-commit-msg 来检查你的项目的 commit-message 是否符合格式，由于官方已经把 validate-commit-msg 项目给废弃掉啦，推荐使用 [commitlint](https://github.com/conventional-changelog/commitlint)。

commitlint 和 validate-commit-msg 功能类似，可以帮助我们 lint commit messages, 如果我们提交的不符合指向的规范, 直接拒绝提交, 下面看看怎么使用 commitlint 吧。

#### 安装

安装 commitlint 和 推荐的校验配置项 config-conventional：

```bash
$ npm install --save-dev @commitlint/config-conventional @commitlint/cli

```
在 commitlint 中，我们可以指定一份校验配置项，比如以下的配置项你都可以使用：

- [@commitlint/config-angular](https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-angular)
- [@commitlint/config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-conventional)
- [@commitlint/config-lerna-scopes](https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-lerna-scopes)
- [@commitlint/config-patternplate](https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-patternplate)
- [conventional-changelog-lint-config-atom](https://github.com/erikmueller/conventional-changelog-lint-config-atom)
- [conventional-changelog-lint-config-canonical](https://github.com/gajus/conventional-changelog-lint-config-canonical)

我们推荐使用 [@commitlint/config-angular](https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-angular)，在我们的安装命令中，也已经安装了此规范。

#### 配置校验规范

在项目目录下创建配置文件 .commitlintrc.js, 写入:
```js
module.exports = {
  extends: [
    "@commitlint/config-conventional"
  ],
  rules: {
  }
};

```

#### 结合 Husky
校验 commit message 的最佳方式是结合 git hook, 所以需要配合 [Husky](https://github.com/typicode/husky)。

安装 husky :

```bash
npm install husky --save-dev

```

在 package.json 中添加：

```json
{
    "husky": {
        "hooks": {
            "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
        }
    }
}

```

至此就已经完成了利用 config-conventional 规范对 commit message 的校验。你可以试试不通过 `git cz` 命令按照规范提交代码，就会报错拒绝提交。

```bash
$ git add .

$ git commit -m "test commitlint"

# 报错，提交失败

```

### 生成 Change log

如果你的所有 commit 都符合 Angular 格式，那么发布新版本时，Change log 就可以用脚本自动生成。

生成的文档包括以下三个部分。
- New features
- Bug fixes
- Breaking changes

每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容。

我们使用 conventional-changelog 自动根据 commit 生成 change log 。

#### 安装：

```bash
$ npm install -g conventional-changelog-cli
$ cd my-project
$ conventional-changelog -p angular -i CHANGELOG.md -s

```

#### 配置 npm 命令
在 package.json 中添加：
```json
{
    "scripts": {
        "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s -r 0"
    },
}

```

通过执行 `npm run changelog` 就可以生成 change log 文档了。
