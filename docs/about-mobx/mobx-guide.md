# Mobx入门指南

### Mobx简介
Mobx是一种轻量级的状态管理, 可以根据应用的 UI、数据或业务逻辑来组织store

### Mobx与Redux的对比
| Mobx        | Redux    |
| --------   | --------  |
| 多个store | store为单一数据源 |
| state可读可写，更新简单 | state只读，需要写 action，在 View 层调用 action，根据相应 action type，写对应 reducer返回新state |
| 只有用到该数据的地方才会更新  | 任何action dispatch都会广播，需要自己用需要我们在组件中用 shouldComponentUpdate 控制更新粒度 |

### 为何用Mobx取代setState
- setState是异步操作，使用不当会导致bug
- setState会造成一些不必要的重新渲染
- setState是不能满足所有的状态存储的，比如如果将一些权限管理状态放在state里面就会造成不必要的渲染

具体可以参考[3 Reasons why I stopped using React.setState](https://blog.cloudboost.io/3-reasons-why-i-stopped-using-react-setstate-ab73fc67a42e)

### observable
把基本数据类型，引用类型，普通对象，类实例，数组和映射变成可观察属性。
observable有两种用法：
- observable(value)
- @observable classProperty = value

实例：
```javascript
//将数组传给observable,对list做的操作都是视为可观察的，如push，shift等
const list = observable([1,2,4]);
//将object设为可观察的,只有初始化时存在的属性才会转化成可观察属性
const obj = observable({ 'price': 5 });
//创建key是动态的可观察对象
const obj = observable.map({ 'price': 5 });
obj.set('name': 'apple');

//类属性中声明可观察属性
import { observable, computed } from "mobx";
class OrderLine {
    @observable price = 0;
    @observable amount = 1;
    
    @computed get total() {
        return this.price * this.amount;
    }
}
```

### computed
计算属性可以根据现有的状态计算出新值，任何state的变化影响了最后计算的结果，都会触发相应的observer。
计算属性是被优化过的，如果使用的数据没被更改，它不会重新运行。

### action
action是用来修改State的，只执行查找，过滤器等函数不应该被标记为action。
推荐使用严格模式```mobx.useStrict(true)```，这样对于任何不适用动作的状态修改，mobx都会抛出异常。

### 核心函数autorun
使用 ```autorun``` 时，所提供的函数总是立即被触发一次，然后每次它的依赖关系改变时会再次被触发
先看个最简单的例子
```javascript
const obj = observable({
    a: 1,
    b: 2
})

autorun(() => {
    console.log(obj.a)
})
// 立即触发一次，输出 ：1

obj.b = 3 // 什么都没有发生
obj.a = 2 // observe 函数的回调触发了，控制台输出：2
```
因为只有obj的a属性在autorun里被用到了，所有只有对它的改动才会触发回调。

### Mobx基本数据流
![Mobx数据流](http://cn.mobx.js.org/flow.png)
可以看到Actions触发State的改变，接着会更新相应的计算属性，最后执行用到该State或者计算属性的方法，这就形成了完整的单向数据流。

### 与React的结合使用（mobx-react）

##### observer 函数可以用来将 React 组件转变成响应式组件
它用 ```mobx.autorun``` 包装了组件的 ```render``` 函数以确保任何组件渲染中使用的数据变化时都可以强制刷新组件。
observer 是由单独的 ```mobx-react``` 包提供的。

以下实例通过在React class引入```@observable```, 使得本地状态secondesPassed会每隔1秒触发对应的render, 这样就不需要用setState来管理状态了。
codepen地址：[React-mobx sample](https://codepen.io/mewwind/pen/vpaKLz)
```javascript
import {observer} from "mobx-react"
import {observable} from "mobx"

@observer class Timer extends React.Component {
    @observable secondsPassed = 0

    componentWillMount() {
        setInterval(() => {
            this.secondsPassed++
        }, 1000)
    }

    render() {
        return (<span>Seconds passed: { this.secondsPassed } </span> )
    }
})

React.render(<Timer />, document.body)
```
*注意*：当 observer 需要组合其它装饰器或高阶组件时，请确保 observer 是最深处(第一个应用)的装饰器。

##### 使用 inject 将组件连接到提供的 stores
```mobx-react``` 包还提供了 ```Provider``` 组件， 类似于Redux的Provider，它可以透传sotres
具体可参考视频 [Provider注入stores](https://egghead.io/lessons/react-connect-mobx-observer-components-to-the-store-with-the-react-provider)

##### componentWillReact
```mobx-react``` 定义了一个新的生命周期钩子函数```componentWillReact```
当组件因为它观察的数据发生了改变，它会安排重新渲染，这个时候```componentWillReact```会被触发

### 总结与进阶
- Mobx能够追踪观察属性，并对其作出反应，但我们需要更全面的了解它对什么会反应，为什么我们换一种写法它就没有反应了。具体可以参考[Mobx对什么作出反应](http://cn.mobx.js.org/best/react.html)
- Mobx与React的结合可以说是天衣无缝，如何来优化React的组件渲染，可以参考[优化组件渲染](http://cn.mobx.js.org/best/react-performance.html)
- 我们会遇到很多异步操作，比如请求数据，那对于编写异步的action，我们需要注意回调需要变成action，具体详见[异步action](http://cn.mobx.js.org/best/actions.html)
- TodoList实例会帮助我们更好地理解Mobx-React，具体参考[Todolist-mobx-react](https://codepen.io/avioli/pen/JXRMxO?editors=0010)