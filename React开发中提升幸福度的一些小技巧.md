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

##### 第八道菜：火烧肉

render props是现在很流行的一种渲染方式，通过回调函数，渲染子组件，参数可为父组件的任意属性值（[官网也有相应的介绍](https://reactjs.org/docs/render-props.html)）

很多种使用此方式的做法：


##### 第九道菜：粉丝白菜虾仁汤 

##### 第十道菜：麻婆豆腐

创建弹层的三种方式, ReactDom的其他用法

function props render children方式  props => <div>{props.something}</div>

setParentState 

尽量新生成对象设置state，不然shouldcomponentupdate prevstate获取到的是已经改变的对象