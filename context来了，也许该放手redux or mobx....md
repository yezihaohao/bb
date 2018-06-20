> 老铁，学不动了？不要慌，耽误不了你几分钟...(说谎脸，汗)

#### long long ago

使用react的同胞们，也许都苦恼过其状态管理以及组件之间的数据传递和共享（笨重的方式通过props依次往子组件传递）。

这时候，redux（mobx类似）出现了，我们累死累活的从水深火热中解放了（第三方的库相对较复杂和麻烦）。。。

那还没有接触过redux或者还在使用redux路上的同学怎么办？不要着急，更简单的东西来了（前方高能，请各老铁注意！）： **Context API！！！**（React v16.3.0+） [戳我查看更新日志](https://github.com/facebook/react/blob/master/CHANGELOG.md#1630-march-29-2018)

先来安利一个todolist的栗子：

![栗子](https://uploads.codesandbox.io/uploads/user/bb338ac8-c84f-4a71-897b-aa24763fb664/nX_F-t.gif)

###### [戳我查看源码和demo](https://codesandbox.io/s/01ow48mwn0)

#### Context是什么？
Context直译就是上下文，是 React 16.3.0开始提供的一个官方API，它无需通过props的方式就可以完成项目中由上而下及组件之间的数据传递和共享，即你也不用依赖任何第三方的状态数据插件库就可以完成这项工作任务。

官方推荐使用的情况是：当需要用到全局数据的时候，比如：主题，多语言制或者用户登录授权等等。 **==当然：==** 你无需这么死板，当需要用到多层级的组件数据传递或者复杂的数据共享场景也可以使用context api，也可以用来做缓存使用。

#### Context简单使用

```js
// 1.使用React.createContext创建context提供者Provider 和 context订阅者cunsomer

const {Provider, Consumer} = React.createContext(defaultValue); // defaultValue根据使用场景设置

// 2.设置Provider组件
// 一般包裹需要订阅的子组件的顶层父组件
// value设置需要传递和共享的数据以及改变数据的函数等
// 为了避免没必要的重绘和渲染，value的数据属性值通过组件state设置

<Provider value={/* some value */}>
    {/* some component with comsumer */}
</Provider>

// 3.设置Consumer组件
// 通过函数作为子元素的方式，订阅context的变换

<Consumer>
  {value => /* render something based on the context value */}
</Consumer>

// 组合Provider 和 Consumer即可大功告成

<ProviderComponent>
    <ConsumerComponent>{somechildren}</ConsumerComponent>
</ProviderComponent>

// 其他更多用法，比如生命周期函数调用（可点击上面demo查看），高阶组件等浏览一下文档即会，非常简单

```

#### 简单总结

创建卖家和买家，通过大家都非常熟悉的React组件方式来进行买卖交易，如此简单的Context用法，大家还在等什么，赶紧丢掉其他的第三方库！！

> 结语： 小小皮一下，大家不要打我，新功能可以尝试，也需要大家的讨论和研究，类似redux的第三方库还是非常强大~~(前端攻城狮③群:743490497，欢迎大家进群讨论)

> 更多资料请查看以下链接:

> [https://reactjs.org/docs/context.html](https://reactjs.org/docs/context.html)

> [https://css-tricks.com/digging-into-react-context/](https://css-tricks.com/digging-into-react-context/)

