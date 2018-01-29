# 手把手教你写创建React webapp项目的脚手架
[返回首页](../../README.md)


[作者github地址](https://github.com/sunshinewlz01/generator-react-webapp)

本文主要针对脚手架初学者，老鸟可略过~

## 一、安装环境
首先保证已经安装以下软件：
1. Node.js（6.0以上版本）
2. npm (3.0以上版本)
3. 安装yeoman

```bash
npm install -g yo
```

## 二、关于脚手架
在开始开发任何一个项目时，总需要初始化项目工程，最开始的时候往往是手动创建工程目录和文件，并逐渐形成一套架构规范或者最佳实践和风格指南。<br />
当需要把这套最佳实践应用到其他项目中时，为了快速创建标准化项目，往往就需要一个脚手架来自动创建标准化的工程项目了。<br />
鉴于Yeoman的强大功能，基于Yeoman写一个脚手架是一个比较好的选择：<br />
1. Yeoman是一个通用的脚手架系统允许创建任何的app。它可以迅速的搭建一个新项目，并且能够简化现有项目的维护。
2. Yeoman构建的项目与语言无关。它可以构建任何语言的项目 (Javascript, Java, Python, C#,等。)
3. Yeoman它自己不能做任何操作。每个操作都是由generators基本插件在Yeoman环境所完成的。Yeoman总是可以为你需要的脚手架工具作出正确的选择。
4. Generators是Yeoman生态系统的基础.它是一个重要组件，运行yo为终端用户生成项目文件。
本文即主要是介绍如何基于Yeoman写一个创建React webapp项目的脚手架，下面将具体逐步介绍。

## 三、组织generator
通过如下命令创建package.json文件：
```bash
npm init
```
最后生成的package.json文件如下：
```json
{
     "name": "generator-react-webapp",
     "version": "0.0.1",
     "description": "Generator react webapp scaffold.",
     "main": "generators/app/index.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1"
     },
     "repository": {
       "type": "git",
       "url": "https://github.com/sunshinewlz01/generator-react-webapp.git"
     },
     "keywords": [
       "yeoman-generator",
       "react",
       "webapp"
     ],
     "author": "weileizhe",
     "license": "MIT",
     "bugs": {
       "url": "https://github.com/sunshinewlz01/generator-react-webapp/issues"
     },
     "homepage": "https://github.com/sunshinewlz01/generator-react-webapp#readme",
     "files": [
       "generators"
     ],
     "dependencies": {
       "yeoman-generator": "^1.1.1"
     }
   }
```
注意：<br />
1. 这个generator的name属性必须以“generator-”为前缀;
2. keywords属性必须包含"yeoman-generator";
3. “file” 属性必须是由你的generator使用的文件排列和目录。
   
另外，为了保证yeoman-generator已经安装到最新版本，你还需要运行一下如下命令：
```bash
npm install yeoman-generator --save
```

## 四、generator文件树结构

Yeoman会深度链接到文件系统和你如何组织你的目录树。<br />
如上面的generator,将暴露“yo name”命令。
如果想暴露“yo name:subgenerator”命令，需要在package.json里面做如下配置：
```json
{
  "files": [
    "generators/app",
    "generators/subgenerator"
  ]
}
```
一般情况下，一个generator工程项目的树形结构如下：
```
── LICENSE
├── README.md
├── generators
│   └── app
│       ├── index.js
│       └── templates
│           ├── README.md
│           ├── compile.sh
│           ├── package.json
│           ├── public
│           │   ├── favicon.ico
│           │   ├── iconfont
│           │   │   ├── demo.css
│           │   │   ├── demo_fontclass.html
│           │   │   ├── demo_symbol.html
│           │   │   ├── demo_unicode.html
│           │   │   ├── iconfont.css
│           │   │   ├── iconfont.eot
│           │   │   ├── iconfont.js
│           │   │   ├── iconfont.svg
│           │   │   ├── iconfont.ttf
│           │   │   └── iconfont.woff
│           │   ├── index.html
│           │   └── manifest.json
│           └── src
│               ├── index.js
│               ├── index.less
│               └── static
│                   ├── common
│                   ├── components
│                   ├── core
│                   ├── images
│                   └── mock
└── package.json
```

以上文件中templates文件夹下面的是项目模板文件，generator/app/index.js则是这个generator的主程序文件。

## 五、如何写generator

### 1.基础generator
Yeoman提供了一个基础的generator，你可以扩展到实现你自己的行为。这个基本的 generator将添加大部分的功能。ES6写法如下：
```js
// 创建 yeoman generator 的核心功能模块.
const Generator = require('yeoman-generator');
module.exports = class extends Generator {
// some code here
}
```

### 2.generator生命周期钩子
- initializing()
初始化方法
- constructor()
构造函数，获取输入参数以及配置信息等。
- prompting()
提供给用户的选项菜单，并获取用户输入
- configuring()
保存配置信息
- writing()
写入文件
- install()
安装packages(npm, yarn, bower等)
- end()
安装结束
- conflicts()
处理异常和冲突

### 3.主体代码
按照前面所说的生命周期钩子函数，重写方法，得到generator主体代码。

```js
/**
 * Created by weileizhe on 17/7/19.
 */


// 创建 yeoman generator 的核心功能模块.
const Generator = require('yeoman-generator');

// 文件读写模块.
const fs = require('fs');
// 路径模块
const path = require('path');

// PS: fs 和 path 是 NodeJS 的核心模块，无需安装.

/**
 * Base generator.
 */
module.exports = class extends Generator {

    /** 构造函数 */
    constructor(args, opts) {
        // 继承必须.
        super(args, opts);

        // 获取 AppName，使用路径尾.
        this.projectName = args[0];
        console.log(this.projectName);
    }

    /**
     * 初始化方法.
     */
    initializing() {
        this.log("开始构建项目...");
    }

    prompting() {
        const done = this.async();
        if(!this.projectName) {
            const prompts = [{
                type    : 'input',
                name    : 'projectName',
                message : 'Your project name',
                default : this.appname // Default to current folder name
            }];
            return this.prompt(prompts).then((props) => {
                this.props = props;
                done();
            });
        } else {
            done();
        }

    }

    /**
     * 写入配置
     */
    configuring() {
        const projectName = this.projectName || this.props.projectName;
        const projectPath = process.cwd() + '/' + projectName;

        // 获取 package 配置模板.
        let defaultSettings = this.fs.readJSON( this.templatePath('package.json') );
        // 做新 package 配置文件.
        let packageSettings = {
            name: this.projectName || this.props.projectName,
            private: true,
            version: defaultSettings.version,
            description: this.projectName || this.props.projectName,
            scripts: defaultSettings.scripts,
            repository: defaultSettings.repository,
            author: defaultSettings.author,
            devDependencies: defaultSettings.devDependencies,
            dependencies: defaultSettings.dependencies,
            homepage:defaultSettings.homepage
        };

        // 写入 package.json.
        this.fs.writeJSON(this.destinationPath(projectPath+'/package.json'), packageSettings);
    }

    /**
     * 写入文件
     */
    writing() {
        const projectName = this.projectName || this.props.projectName;
        const projectPath = process.cwd() + '/' + projectName;

        if(projectName) {
            fs.mkdirSync(`${projectName}`);

            console.log(projectPath);
            process.chdir(projectPath);
        }
        console.log(this.destinationPath(projectPath,"/src"));

        /* 拷贝所需的文件. */

        this.fs.copy(
            this.templatePath("src"),
            this.destinationPath(projectPath,"/src")
        );
        this.fs.copy(
            this.templatePath("public"),
            this.destinationPath(projectPath,"/public")
        );
        this.fs.copy(
            this.templatePath(".eslintrc"),
            this.destinationPath(projectPath,"/.eslintrc")
        );
        this.fs.copy(
            this.templatePath(".npmignore"),
            this.destinationPath(projectPath,"/.gitignore")
        );
        this.fs.copy(
            this.templatePath(".stylelintrc"),
            this.destinationPath(projectPath,"/.stylelintrc")
        );
        this.fs.copy(
            this.templatePath("build.sh"),
            this.destinationPath(projectPath,"/build.sh")
        );
        this.fs.copy(
            this.templatePath("compile.sh"),
            this.destinationPath(projectPath,"/compile.sh")
        );
        this.fs.copy(
            this.templatePath("nginx.conf"),
            this.destinationPath(projectPath,"/nginx.conf")
        );
        this.fs.copy(
            this.templatePath("README.md"),
            this.destinationPath(projectPath,"/README.md")
        );

    }

    /**
     * 安装方法
     */
    install() {
        // 安装 package 安装.
        this.installDependencies({ npm: true,bower:false});
    }

    end() {

        this.log("构建项目成功！");

    }

};
```

### 4.添加自己的方法
当然，除了重写方法外，你也可以添加自己的方法。
下面是一个例子：
```js
module.exports = class extends Generator {
  method1() {
    console.log('method 1 just ran');
  },
  method2() {
    console.log('method 2 just ran');
  }
};
```

### 5.运行generator
因为你是在本地开发的 generator，它还不能够作为一个全局的 npm 模块去获取。所以还需要在generator项目的根目录（在 generator-name/ 文件夹），执行如下命令：
```bash
npm link
```
这将安装项目依赖项和链接一个全局模块到本地文件。npm下载完后，将能够使用yo name命令运行调试generator了。具体项目github地址见[generator-react-webapp](https://github.com/sunshinewlz01/generator-react-webapp)。

## 六、如何部署和使用
### 1. 部署
开发完成好generator自后，可以部署到npm官方服务器上，当然也可以部署到私服上。命令如下：
```bash
npm publish
```

### 2. 使用
- 安装Yeoman
```bash
npm install -g yo
``` 
- 安装generator
```bash
npm install generator-xxx -g
```
- 使用generator
```bash
yo react-xxx
```


### 七、结束语
Yeoman的强大不止这些，本文实际上只是使用了其中的一个小小点。更多请访问[Yeoman官方网站](http://yeoman.io/)

> 译者简介
**魏磊哲** 魏磊哲，负责滴滴海浪事业部前端架构工作，曾先后供职于百度、陆金所，喜欢研究技术和工具，不限于前端，业余时间喜欢看看书踢踢球。 
![](../images/wlz.jpeg)



