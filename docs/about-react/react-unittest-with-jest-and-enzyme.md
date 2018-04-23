# 如何利用Jest与Enzyme进行React单元测试

## 前言
随着前端工程化的重要性越来越高，前端单元测试也备受重视。一套良好的代码自动化质量检测方案不仅可以保证代码的质量和健壮性，也可以降低人员变动与代码重构带来的维护成本。

本次我们专注在React前端项目的单元测试上，以及Jest与Enzyme等工具的介绍与实践。
## 前端测试工具
我们一般所熟知的前端测试工具主要分为 **测试框架**、**断言库**和**测试覆盖率工具**等几类。先简单介绍一下：

### 测试框架
测试框架是一组自动化测试的规范、测试思想和惯例的集合，它提供编写测试脚本的基础代码和运行测试脚本的环境。目前常见的前端测试框架有[Jasmine](https://jasmine.github.io/)，[Mocha](https://mochajs.org/)，[Jest](http://facebook.github.io/jest/)。

### 断言库
断言库主要提供语义化方法，用于判断源码的实际执行结果与预期结果是否一致，如果不一致就抛出一个错误。目前比较常见的前端断言库有[Should.js](https://shouldjs.github.io/)，[Chai.js](http://chaijs.com/)。

### 测试覆盖率工具
统计测试结果，生成测试报告与报表，如[istanbul](https://github.com/gotwarlost/istanbul)。


## 为什么选择Jest与Enzyme
### Jest
[Jest](http://facebook.github.io/jest/) 是 Facebook 发布的一个开源的、基于 `Jasmine` 框架的 JavaScript 单元测试工具。

它内置了常用的测试工具如断言库和测试覆盖率工具等，配置方便，开箱即用。

另外，Jest提供快照测试功能，通过比对 UI 代码生成的快照文件，实现对 React 组件的自动化测试。

目前Github上star数已破万，除了Facebook官方推荐使用之外，其他业界公司如Airbnb也在尝试转向Jest。

### Enzyme
[Enzyme](http://airbnb.io/enzyme/)是Airbnb开源的 React 测试类库，它提供了一套简洁强大的 API，并通过 `jQuery` 风格的方式进行DOM处理，开发体验十分友好。

目前Github上star数也已破万，同时也获得了React 官方的推荐。


## 测试环境配置与安装
- 首先，我们默认目前的React项目基于`Webpack`+`Babel`来进行打包构建，通过npm或yarn安装`jest` `enzyme` `babel-jest` `react-test-renderer`。以npm为例：

```javascript
npm install jest enzyme babel-jest react-test-renderer --save-dev
```
- npm依赖包下载完成后，在`package.json`新增配置项`jest`：

```javascript
  "jest": {
    "moduleFileExtensions": [
      "js",
      "jsx"
    ],
    "coveragePathIgnorePatterns": ["<rootDir>/lib/", "<rootDir>/node_modules/", "__test__"],
    "transform": {
      "^.+\\.js$": "babel-jest"
    }
  }
```
其中`moduleFileExtensions`代表支持加载的文件名，与 `Webpack` 中的 `resolve.extensions` 类似；`coveragePathIgnorePatterns`忽略该路径下的测试覆盖率；`transform`用于编译 ES6/ES7 语法，需配合 babel-jest 使用。更多配置可参考[官方文档](https://facebook.github.io/jest/docs/en/configuration.html)。


- 同时在`package.json`的`scripts`下新增test命令：

```javascript
"test": "jest --colors",
"coverage": "jest --colors --coverage"
```
`--colors`代表高亮输出，`--coverage`代表输出覆盖率报表。

### Tips:
如果你的项目中使用了`react-slick`，你需要添加`test-setup.js`文件并添加内容：
```javascript
// to fix react-slick issue
window.matchMedia = window.matchMedia || function() {
    return {
        matches : false,
        addListener : function() {},
        removeListener: function() {}
    };
};
```
并在`package.json`的`jest`配置中添加：
```javascript
"setupFiles": ["test-setup.js"]
```

## 测试脚本编写
demo

## 思考与总结
前端单元测试的好处显而易见：
- 保障代码质量和功能实现的完整度
- 提前定位问题和解决bug，减少后期开发成本
- 便于项目维护，后续任何代码更新也必须跑通测试用例，即使进行重构或开发人员发生变动也不受影响

但在业务开发过程中很难有很多的人力去进行测试用例的编写，一些变化较快的业务项目无需进行单元测试。目前比较适合引入单元测试的场景有：
- 底层代码框架和功能模块。
- 较为稳定的项目、或项目中较为稳定的部分。
- 被多次复用的部分，比如一些通用组件和库函数。

## 参考
https://facebook.github.io/jest/
https://github.com/airbnb/enzyme


> 作者简介

**范可** 滴滴上海前端团队架构师，全栈工程师，微信专家，曾就职于SAP，LIMO创始人，关注于React、Vue、Mini Program等跨端技术的打通。

![](../../images/fanke.jpeg)
