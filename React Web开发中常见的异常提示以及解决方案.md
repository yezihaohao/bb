> 使用React 开发web app的过程中，总会有踩坑的时候，针对常见的各种异常提示，作以下汇总，以便帮助大家避免少踩坑

> 汇总中会有相关对应的错误异常栗子用法

#### 1. Uncaught TypeError: Cannot read property xxx of undefined

这个异常基本上是开发过程中最常见的异常了，引发异常的使用场景也有很多，举几个栗子：

- **未初始化state的属性值便直接使用**
```js
class App extends Component {
	state = {}  
	componentDidMount() {
		axios.post('https://extension-ms.juejin.im/resources/gold', {category: 'frontend'})
			.then(res => {
				this.setState({data: res.data.data});
			});
	}
	render() {
		return (
			<div className="App">
				{
					this.state.data.map(v => <span key={v.id}>{v.title}</span>)
				}
			</div>
		);
	}
}
// 处理方案有几种
// 1: 初始化state --> state = {data: []}
// 2: 采用短路写法 this.state.data && this.state.data.map()
```
- **未初始化组件的props参数值**
```js
// 使用组件时，数据来自异步获取
// 1: 初始化props (推荐)
class Greeting extends React.Component {
  static defaultProps = {   
    person: { name: 'react' }
  }

  render() {
    return (
      <div>Hello, {this.props.person.name}</div>
    )
  }
}
// 也可以
Greeting.defaultProps = {
  name: 'Stranger'
};
// 2: render时初始化
render() {
    const { person = {name: 'react'} } = this.props;
    return (
      <div>Hello, {person.name}</div>
    )
}
// 其他
```

#### 2. this的绑定问题

![image](https://note.youdao.com/yws/public/resource/9922af5dc1c71d8541777f2cf3088460/xmlnote/7F228B53118B4203AC61A95AB7429767/515)

```js
testClick() {
	console.log(this.state.data)
}
render() {
	return (
		<button onClick={this.testClick}>测试</button>
	);
}
```

处理绑定的有三种方式：

(1) 调用函数时绑定：onClick={this.testClick.bind(this)}

(2) 构造函数中绑定：
```js
constructor (props) {
    super(props)
    this.testClick = this.testClick.bind(this)
}
```

(3) 箭头函数：(个人推荐箭头函数，更便捷)
```
testClick = () => {
	console.log(this.state.data)
}
```


#### 3. 循环key的问题

![image](https://note.youdao.com/yws/public/resource/9922af5dc1c71d8541777f2cf3088460/xmlnote/029CADDAC21D4A0EAEF3D0A17155B6F3/547)

设置唯一的key值防止对已存在的dom重新渲染

```js
this.state.data.map(v => <span key={v.id}>{v.title}</span>)
// 设置唯一性的id,不要设置循环的index
// bad
this.state.data.map((v, i) => <span key={i}>{v.title}</span>)
```
官方给了一个用index作为key导致的问题demo: [戳我查看codepen](https://reactjs.org/redirect-to-codepen/reconciliation/index-used-as-key)

#### 4. componentDidUpdate或componentWillUpdate 中更新state问题

![image](https://note.youdao.com/yws/public/resource/9922af5dc1c71d8541777f2cf3088460/xmlnote/5EBE49A18FBE4FD2AF36A41A13954A59/573)
```js
// 一般问题是需要通过props来更新state导致的
// 老版本的在componentWillReceiveProps中修改state
componentWillReceiveProps(nextProps) {
	this.setState({data: nextProps.data});
}
// 16.3以后的新版本在getDerivedStateFromProps中更新state
static getDerivedStateFromProps(nextProps, prevState) {
    return {
        data: nextProps.data
    }
}
```

#### 5. Objects are not valid as a React child  组件参数设置异常 或者直接渲染对象

![image](https://note.youdao.com/yws/public/resource/9922af5dc1c71d8541777f2cf3088460/xmlnote/11B6E30E907C4E21A4C087AE8C299DFE/592)

```js
render(){
    const obj = {a: 1};
    return (
        <span>{obj}</span>  
        // --> <span>{obj.a}</span>
    )
}

const CompA = (data) => <span>{data}<span> 
// --> ({data}) => <span>{data}</span>

```

#### 6. 忘记export 组件

![image](https://note.youdao.com/yws/public/resource/9922af5dc1c71d8541777f2cf3088460/xmlnote/9B2D9A7271ED44C98004025339DE3147/609)

找到对应的组件，export 即可

#### 7. .map （xxx） is not a function 

和上面很类似，这个问题常发生在数据来自异步加载，没有进行相应的初始化或者初始化不规范，数据类型不一致导致的问题

#### 8. 组件没有return DOM

![image](https://note.youdao.com/yws/public/resource/9922af5dc1c71d8541777f2cf3088460/xmlnote/DFCC1EB37835443687F759F1881E40C9/653)
```js
// error
render() {
	<div className="App">
	</div>
}
// yes
render() {
	return(
	    <div className="App">
	    </div>
	)
}
```

#### 9. 使用未知的标签或者组件名称使用小写

The tag <test> is unrecognized in this browser. If you meant to render a React component, start its name with an uppercase letter. 

其实就是字面意思，找到对应的标签，改成首字母大写

#### 10. 路由外使用link组件

![image](https://note.youdao.com/yws/public/resource/9922af5dc1c71d8541777f2cf3088460/xmlnote/E8A2AA5515FD4C46AA1E21EE5D46C4F5/672)

使用Link的组件必须在用Router包裹的组件下使用

如果不是局部刷新的页面，可在最外层写一个空白的路由页面组件将内容页面包裹

```js
const BlankPage = () => (
    <HashRouter>
        <Route path="/" component={App} />
    </HashRouter>
)
export default BlankPage;
```

#### 11. 跳转相同路由报错

Warning: You cannot PUSH the same path using hash history

```js
<Link
    to={somePath}
    replace={somePath === this.props.location.pathname}
>
跳转
</Link>

// history操作，判断跳转路由相同的话，用replace
this.props.history.push  --> this.props.history.replace
```


#### 总结

总结来看这些bug都比较简单，有时候细节和不规范往往会导致一些常见或者莫名bug，制定开发规范或文档可以轻松避免一些没必要的bug，年中了，祝大家bug越来越少，加班越来越少~

若有常见问题遗漏，欢迎大佬随时补充。

前端攻城狮3群：743490497。欢迎各位同学进群分享和交流。
