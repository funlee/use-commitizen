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

#### 安装
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