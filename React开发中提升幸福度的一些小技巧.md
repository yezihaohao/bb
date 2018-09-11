> 又一篇来自日常开发的汇总：各位客官请对号入席，店小二逐一上菜。

##### 第一道菜：回锅肉

react数组循环，基本都会设置一个唯一的key，表格的对象数组循环一般没什么问题，数据基本都会有一个id。那有种情况就比较坑了，出现在表单形式的页面结构中，对某个数组进行增删改操作，一般对于非对象数组而言，没有id，可能很多人会偷懒，循环的时候，直接设置数组的下标index作为key，当出现增删改时候，就会出现数据对不上或者重新渲染组件的问题等。解决方案有很多种，例如把字符串数组等重组对象数组，每个元素设置一个唯一id等。另外有个方式：推荐使用[shortid](https://github.com/dylang/shortid)生成唯一key的数组，和数据数组一起使用，省去提交数据时再重组数组。
```js
import React from 'react';
import shortid from 'shortid';

class Demo extends React.Component {
    constructor(props) {
		super(props);
		this.state = {
			data: ['a', 'b', 'c']
		}
		this.dataKeys = this.state.data.map(v => shortid.generate());
	}
	
    deleteOne = index => { // 删除操作
        const { data } = this.state;
        this.setState({ data: data.filter((v, i) => i !== index) });
        this.dataKyes.splice(index, 1);
    }
    
    render() {
	    return (
    	    <ul>
               {
                   data.map((v, i) => 
                    <li 
                        onClick={i => this.deleteOne(i)}  
                        key={this.dataKeys[i]}
                    >
                        {v}
                    </li>
                    )
               } 
            </ul>
	    )
	}
}
// 稍微抽取，可以封装一个通用的组件

```
##### 第二道菜：番茄炒蛋

通过判断值是否存在来控制元素是否显示，一般三目运算可以达到此效果，最简单的还是用短路的写法：
```js
// 不错
const flag = 'something';
flag && <div></div>

// 很好
// 注意一般可能上面写法多一些，但当flag为0 的时页面上会显示0，用!!将其转为boolean避免坑，
// 代码也更规范
const flag = 'something';
!!flag && <div></div>
```
##### 第三道菜：酸辣土豆丝
使用组件，传递props：
```js
const { data, type, something } = this.state;
<Demo 
    data={data}
    type={type}
    something={something}
/>
```
也许另外一种传递方式更简洁：
```
const { data, type, something } = this.state;
<Demo 
    {...{ data, id, something }}
/>
```
##### 第四道菜：清炒时蔬
组件的props有时候会定义很多，但是调用组件传递props的时候又想一个个传，不想一次性传递一个option对象，通过扩展运算符和解构赋值可以简化此操作：
```
const Demo = ({ prop1, prop2, prop3, ...restProps }) => (
    <div>
        xxxx
        { restProps.something }
    </div>
)
// 父组件使用Demo
<Demo
    prop1={xxx}
    prop2={xxx}
    something={xxx}
/>
```
##### 第五道菜：小吃-糍粑
一般改变state值的一种方式：
```js
const { data } = this.state;
this.setState({ data: {...data, key: 1 } });
```
另外一种可以通过callback的方式改变state的值
```js
this.setState(({ data }) => ({ data: {...data, key: 1 } }));
```
还可以：
```js
this.setState((state, props) => {
    return { counter: state.counter + props.step };
});
```
##### 第六道菜：水煮肉片
React 性能优化有很多种方式，那常见的一种就是在生命周期函数shouldComponentUpdate里面判断某些值或属性来控制组件是否重新再次渲染。

判断一般的字符串，数字或者基础的对象，数组都还是比较好处理，那嵌套的对象或者数组就比较麻烦了，对于这种，可以转成字符串处理，但属性值的位置不同时，那就无效了。

推荐使用lodash(或者其他的类似库)的[isEqual](https://lodash.com/docs/4.17.10#isEqual)对嵌套数组或对象进行判断(相比其他方式更简单些)
```js
shouldComponentUpdate(nextProps, nextState) {
    if (_.isEqual(nextState.columns, this.state.columns)) return false;
    return true;
}
```

##### 第七道菜：干锅兔
创建弹层的三种方式：
1. 普通组件通过state和样式控制，在当前组件中显示弹层-每次引入组件并且render里面控制显示，挂载节点在某组件里面
```js
// 弹层 
const Dialog = () => <div>弹层</div>
// 某组件
render() {
    return (
        this.state.showDialog && <Dialog />
    )
}
```
2.通过Portals创建通道，在根节点外部挂载组件-但还是需要每次引入并且在render里面调用
```js
// 弹层 
class Dialog extends React.Component {
  constructor(props) {
    super(props);
    this.el = document.createElement('div');
  }
  componentDidMount() {
    modalRoot.appendChild(this.el);
  }
  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }

  render() {
    return ReactDOM.createPortal(
      this.props.children || <div>xxxx</div>,
      this.el,
    );
  }
}
// 某组件
render() {
    return (
        this.state.showDialog && <Dialog />
    )
}

```
3.推荐使用ReactDom.render创建弹层-挂载根节点外层，使用也更方便
```js
// demo
let dialog;
class Dialog {
    show(children) {    // 显示
        this.div = document.createElement('div');
        document.body.appendChild(this.div);

        ReactDom.render(children || <div>xxxx</div>, this.div);
    }
    destroy() {     // 销毁
        ReactDom.unmountComponentAtNode(this.div);
        this.div.parentNode.removeChild(this.div);
    }
}
export default {
    show: function(children) {
        dialog = new Dialog();
        dialog.show(children);
    },
    hide: xxxxx
};
// 某组件
import Dialog from 'xxx';
alert = () => {
    Dialog.show(xxxx);
}
render() {
    return (
        <button onClick={this.alert}>点击弹层</button>
    )
}
```

##### 第八道菜：火烧肉

render props是现在很流行的一种渲染方式，通过回调函数，渲染子组件，参数可为父组件的任意属性值（[官网也有相应的介绍](https://reactjs.org/docs/render-props.html)）新版的contextApi也采用了这个模式。

很多种场景使用此方式的做法：
```js
// 权限控制组件，只需要封装一次connect，
// 通过render props向子组件传递权限
class AuthWidget extends Component {
    render() {
        return this.props.children(this.props.auth);
    }
}

const mapStateToProps = state => {
    const { auth } = state;
    return { auth: state.auth };
};
export default connect(mapStateToProps)(AuthWidget);

// 其他组件使用
<AuthWidget
    children={auth => auth.edit && <a>编辑</a>}
/>

// 使用antd的form时
const Test = ({ form, children }) => {
    return children(form);
};
const FormTest = Form.create()(Test);

class Demo extends Component {
    render() {
        return (
            <div>
                xxxxx
                <FormTest>
                    { form => {
                        this.form = form;
                        return (
                            <Form>
                                <Form.Item>
                                    {getFieldDecorator('field', xxx)(
                                        <Input placeholder="请输入链接地址" />
                                    )}
                                </Form.Item>
                            </Form>
                        )
                    }}
                </FormTest>
            </div>
        )
    }
}
```

##### 第九道菜：粉丝白菜虾仁汤 
子组件改变父组件的state方式有很多种，可以在父组件设置一个通用函数，类似：setParentState，通过子组件回调处理时，就可以更方便的统一处理：
```js
// 父组件
state = {
    data: {}
}
setParentState = obj => {
    this.setState(obj);
}
// 子组件
onClick = () => {
    this.props.setParentState({ data: xxx });
}
```

##### 第十道菜：麻婆豆腐
永远不要直接设置state的值：this.state.data = { a: 1 }。这个会导致几个问题：
1：组件不会重新渲染  2：shouldComponentUpdate(nextProps, nextState) 函数里面 this.state的值是已经改变了,和nextState的值相同。

举个栗子：
```
// wrong
const { data } = this.state;
data.a = 1;     // 等价于this.state.data.a = 1;
this.setState({ data }); // shouldComponentUpdate里面观察到 this.state 和nextState的值是相同的
// 此时函数里面性能相关的优化是无效的

// correct  需要用到当前state值的写法
this.setState(state => ({ data: {...state.data, a: 1} }))
```

> 各位客官，菜已上齐，请慢用

> react相关讨论，请加Q群：743490497
