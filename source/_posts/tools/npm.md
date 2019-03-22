---
title: npm script
tags: 前端工程化
categories: 大前端
abbrlink: 29376
date: 2019-03-22 10:15:24
---

### npm init初始化

npm是node的包管理工具，创建package.json进行配置符合node开发规范。使用 `npm init` 生成package.json文件，执行命令之后会问几个基本问题。名称、描述、版本号、作者信息、入口文件、仓库地址、许可协议、暴露的文件，多数问题已经提供了默认值。

*tips:* 嫌上面的初始化方式太啰嗦？你可以使用 npm init -f / npm init -y 告诉 npm 直接跳过参数问答环节，快速生成 package.json。结合默认配置快速生成package.json。目录名称不能是中文会报错。

```
npm config set init.author.email "xxx"
npm config set init.author.name "xxx"
npm config set init.author.url "xxx"
npm config set init.license "MIT"
npm config set init.version "0.1.0"
npm config set init.private true
```



串行 报错终止

并行 太长 多容易出问题 使用node任务管理器 npm-run-all **支持通配符** 提供了串行（报错终止） 并行 --parallel

参数 npm run xxx -- --command(前边的-- 意思为给xxx运行提供额外的参数)

日志输出 npm run xxx --loglevel silent / --silent / -s 尽可能少的显示信息 符合规范将看不到任何输出

显示尽可能多的执行状态 npm run xxx --loglevel verbose / --verbose / -d 这个日志级别的输出实例如下（详细打印出了每个步骤的参数、返回值)



### npm 钩子执行

举例来说，运行 npm run test 的时候，分 3 个阶段：

1. 检查 scripts 对象中是否存在 pretest 命令，如果有，先执行该命令；
2. 检查是否有 test 命令，有的话运行 test 命令，没有的话报错；
3. 检查是否存在 posttest 命令，如果有，执行 posttest 命令；

```json
“scripts”: {
    "lint:js": "eslint *.js --fix",
    "lint:css": "stylelint *.css --fix",
    "lint:markdown": "markdownlint --config .markdownlint.json *.md",
 	"lint": "npm-run-all --parallel lint:js lint:css",
    "pretest": "npm run lint",
    "posttest": "npm run lint:markdown",
    "test": "mocha test/"
}
```

执行 *npm run test* 会优先执行lint指令中 其次执行test执行最后执行markdown。中途出现了错误会终止执行指令

增加测试覆盖率 依赖收集 npm i nyc opn-cli -D

1. 覆盖率收集工具 [nyc](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fistanbuljs%2Fnyc)，是覆盖率收集工具 [istanbul](https://link.juejin.im/?target=https%3A%2F%2Fistanbul.js.org) 的命令行版本，istanbul 支持生成各种格式的覆盖率报告，我已经使用多年；
2. 打开 html 文件的工具 [opn-cli](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fsindresorhus%2Fopn-cli)，是能够打开任意程序的工具 [opn](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fsindresorhus%2Fopn) 的命令行版本，作者是前端社区非常高产的 [Sindre Sorhus](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fsindresorhus)，它在 npm 上发布了超过 1000 个包，并且质量都很不错。



然后在 package.json 增加 nyc 的配置，告诉 nyc 该忽略哪些文件。最后是在 scripts 中新增 3 条命令：

1. precover，收集覆盖率之前把之前的覆盖率报告目录清理掉；
2. cover，直接调用 nyc，让其生成 html 格式的覆盖率报告；
3. postcover，清理掉临时文件，并且在浏览器中预览覆盖率报告；

```json
"scripts": {
    "lint:js": "eslint *.js --fix",
    "lint:css": "stylelint *.css --fix",
    "lint:markdown": "markdownlint --config .markdownlint.json *.md",
 	"lint": "npm-run-all --parallel lint:js lint:css",
    "pretest": "npm run lint",
    "posttest": "npm run lint:markdown",
    "test": "mocha test/",
    "precover": "rimraf coverage",
    "cover": "nyc --reporter=html npm test",
    "postcover": "rimraf .nyc_output && opn coverage/index.html"
 }
```