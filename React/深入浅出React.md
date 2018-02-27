## 《深入浅出React和redux》细节汇总
- #### 一、[React简介](#React简介)
  - [create-react-app工具使用](#create-react-app工具使用)
  - [react思维](#react思维)
  - [VirtualDOM](#VirtualDOM)
  - [JSX](#JSX)
- #### 二、[React详解](#React详解)

  - [React组件数据](#React组件数据)
    - [prop](#prop)
    - [state](#state)
    - [context](#context)
  - [React的两个对象：ReactElement与组件实例](#ReactElement与组件实例)
  
  - [组件生命周期](#组件生命周期)
    1. [装载过程](#装载过程)
    2. [更新过程](#更新过程)
    3. [卸载过程](#卸载过程)
  - [React组件的性能优化](#React组件的性能优化)
  - [React高级组件](#React高级组件)
  ***************************************************
 <a id="React简介"></a>
  ### 一、React新的前端思维模式
  <a id="create-react-app工具使用"></a>
- **1、create-react-app工具使用**
1. 安装create-react-app
  ```
  npm install create-react-app -g
  ```
2. 创建项目
  ```
  creact-react-app demos
  cd demos
  npm start
  ```
3. 分解应用
package.json
```
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build",
  "test": "react-scripts test --env=jsdom",
  "eject": "react-scripts eject"
}
```
`npm start`启动开发环境，`npm run build`创建生产环境优化代码，`npm test`用于测试单元，`npm run eject`把潜藏在react-scripts中的一序列技术栈“弹射”到
应用的顶端，此命令不可逆且会改变和增加一些文件。
> 
 <a id="react思维"></a>
- **2、react思维**
  - **数据驱动渲染**

1. 开发者不需要像jQuery一样详细的操作DOM着重于‘如何去做’,只需要着重于“我要显示什么”，而不用操心“怎样去做”;
2. react理念**UI = reader（data）**
> 1. 用户看到的界面（UI），是一个 **纯函数（render）** 的执行结果，只接受数据（data）作为参数；
> 2. 纯函数：没有任何副作用，输出完全依赖于输入的函数；
> 3. 对于react开发者，重要的是区分哪些属于data，哪些属于render，要更新界面，要做的就是更新data；
> 4. react实践的也是"响应式编程"的思想。

 <a id="VirtualDOM"></a>
- **3、Virtual DOM**
1. 每次render函数被调用，都要把整个组件重新渲染一遍会浪费，而react对此利用Virtual DOM，让每次渲染都只从新渲染最少的DOM;
2. DOM树：HTML是结构化文本，而DOM是结构化文本的抽象表达形式，浏览器在渲染HTML格式网页时，会先将HTML文本解析以构建DOM树，然后根据DOM树渲渲染出用户看到
界面，当改变内容时，就去改变DOM树上的节点；
3. 虽然DOM树只是一些简单的JavaScript语句，但DOM操作会引起浏览器对网页的从新布局和绘制，所以Web前端开发优化原则之一： **尽量较少DOM操作** ；
4. react开发会中jsx语句，将被Babel解析为创建React组件或HTML元素的语句，但React并不会通过其直接构建或操作DOM树，而是先构建Virtual DOM；
5. DOM树是对HTML的抽象，而Virtual DOM是对DOM树的抽象；
6. Vritual DOM不触及浏览器，只存在于JavaScript空间的树形结构，每次自上而下的渲染React组件时，都会对比此次产生的Vritual DOM和上一次产生的，然后真正的
DOM树只需要操作有差别的部分。
> 
 <a id="JSX"></a>
- **4、JSX**
1. JSX: 是JavaScript的语法扩展，允许我们在JavaScript中编写HTML一样的代码，最终会编译成普通的JavaScript语句；
2. **属性**
  - 自定义属性`data-*`;
  - class和for为JavaScript保留关键字，所以class和for属性使用className和htmlFor;
3. **JavaScript表达式**
  - JSX允许在闭合标签中使用JavaScript表达式，但必须用{}包裹；
  - JavaScript表达式要求必须有 **返回值** ，所以不能直接使用 if else 语句，但可以使用三元操作表达式和&&，||这样的比较运算符来书写；
  - 如果确实需要使用 if else语句，可以写在函数中，然后在{}中调用。
4. **样式**
  - 通过style属性定义，单属性值不能是字符串只能是对象，且属性名需要使用驼峰命名法（font-size变为fontSize）。
5. **注释**
  - 标签内注意需要写在{}中。
6. **数组**
  - JSX中的数组会自动展开；
  - 注意如果数组或迭代器中的每一项都是HTML标签或组件，那么它们必须要拥有唯一的key属性，这样有助于React的DIFF算法，实现最高效的DOM更新。
7. 事件挂载
  - JSX中可以通过onClick（HTML原生为onclick）
  - HTML直接使用onclick缺点：
    1. onclick添加的事件处理函数是在全局环境下执行，污染全局环境，容易产生意想不到的后果；
    2. 给很多DOM元素添加onclick事件，可能会影响网页的性能；
    3. 对于使用onclick的DOM元素，如果要动态的从DOM树种删除，需要把对应的事件处理器注销，否则可能造成内存泄漏。
  - JSX中的onClick事件（不存在以上问题）
    1. onClick挂载的每个函数都可以控制在组件中，不会污染全局空间；
    2. JSX中onClick没有产生直接使用onclick的HTML，而是使用了 **事件委托** 方式处理，无论有多少个onClick出现，其实最后都只在DOM树上添加了一个事件处理
    函数，挂在最顶层的DOM节点上。
    3. 所有的点击事件都被这个事件处理函数捕获，然后根据具体组件分配给特定函数，所以性能较高；
    4. 因为React控制了组件的生命周期，在unmount的时候能够清除相关的所有事件处理函数，内存泄漏问题解决。
```
function Demo(){
  const name = 'jsx';
  const arr = [
    <h3 key = {0}>数组</h3>
    <p key = {1}>数组会自动展开，注意添加key属性</p>
  ];
  const func = () => {
    let result = 'hello';
    if (name){
      result += name;
    } else {
      result += 'world';
    }
    return result;
  };
  return (
    <div>
      <h2></h2>
      {/*注释*/}
      <p style = {{color: 'red',fontSize: '14px'}}>hello {name || 'world'}</p>
      <p className = {name ? 'classA' : 'classB'}>
        hello {name && 'world'}
      </p>
      <p>
        {func()}
      </p>
      {arr}
    </div>
  )
}
```
   ***************************************************
<a id="React详解"></a>
### 二、React详解
<a id="React组件数据"></a>
#### 1. React数据

<a id="prop"></a>
- **React的prop**
 1. prop(property的简写)是从外部传递给组件的数据，一个组件通过定义自己能够接受的prop就定义了自己的对外公共接口；
 2. 每个React组件都是独立存在的模块，组件之外的一切都是外部世界，外部世界就是通过prop来和组件对话的。
  - **给prop赋值**
  ```
  class Demo extends Component{
    render(){
      return(
        <div>
          <Child caption = "toProp" initValue = {0}/>//给子组件<Child />传入caption和initValue信息，子组件需定义相关prop接口
        </div>
      )
    }
  }
  ```
  - **读取prop值**
  > 1. 给`this.prop`赋值是`React.Component`构造函数的工作之一；
  > 2. 如果一个组件需要定义自己的构造函数，一定要在构造函数的第一行`super`调用父类也就是`React.Component`的构造函数；
  > 3. 如果没有在构造函数中调用`super(props)`，那么组件实例被构造之后，类实例的所有成员就无法通过`this.props`访问到父组件传递过来的`props`值。
  ```
  class Child extends Component{
    constructor(props){
      super(props);
      this.state = {
      //获取外部传入的prop，并用于state初始化
        count: props.initValue || 0,
        caption: props.caption
      }
    }
  }
  
  ```
  - **propTypes检查**
  1. prop是组件的对外接口，所以一个组件该声明自己的接口规范，规范组件支持哪些prop，每个prop该是什么样的格式；
  2. React通过propTypes来规范，因为propTypes已经从React包中分离出来，所以新版React中无法使用`React.PropTypes.*`,需导入`prop-types`
  `npm install prop-type --save`导入`import PropTypes from ('prop-types')`
  3. propTypes验证器
  
    1. JavaScript基本类型：

    PropTypes.array
    
    PropTypes.bool
    
    PropTypes.func
    
    PropTypes.number
    
    PropTypes.object
    
    PropTypes.string
    
    2. 可以被渲染为子节点的对象，包括数值、字符串ReactElement（指的是JSX中的闭合标签）或数组：
    
    PropTypes.node
    
    3. ReactElement
    
    PropTypes.element
    
    4. 指定类的实例
    
    PropTypes.instanceOf(Message)
    
    5. 只接受指定的值：
    
    PropTypes.oneOf(['News','Photos'])
    
    6. 多个对象类型中的一个：
    
    PropTypes.oneOfType([
      PropTypes.string,
      PropTypes.number,
      PropTypes.instanceOf(Message)
    ])
    
    7. 指定类型组成的数组：
    
    PropTypes.arrayOf(PropTypes.number)
    
    8. 指定类型的属性构成的对象：
    
    PropTypes.objectOf(PropTypes.number)
    
    9. 符合指定格式的对象：
    
    PropTypes.shape({
      color: PropTypes.string,
      fontSize: PropTypes.number
    })

    10. 在任意类型上加上isRequired使其不为空：
    
    PropTypes.func.isRequired 
    
    eg:

    Child.propTypes = {
      initValue: PropTypes.number,
      caption: PropTypes.string
      
    }
    
    
    
<a id="state"></a>
- **React的state**
1. state代表组件的内部状态，由于React组件不能修改传入的prop，所以需要使用state记录自身数据变化；
  - **state初始化**
   ```
   constructor(props){
    ...
    this.state = {
      count: props.initValue || 0
    }
   }
   ```
   注意：使用React.createClass方法创建出来的组件类，通过getInitialState方法获取初始值，但这种方法已被废弃。
  - **读取和更新state**
    1. 读取`this.state`
    2. 更新`this.setState({})`
  **注意：**不要直接修改this.state的值，虽然能够改变组件的内部状态，但只是野蛮的修改了state，却不会驱动组件从新渲染，所以变化不会反应到界面
  而，`this.setState()`所做的事是先改变`this.state`的值，然后驱动组件更新
  - **prop和state对比**
    1. prop用于定义外部接口，state用于记录内部状态；
    2. prop的赋值在外部世界使用组件时，state的赋值在组件内部；
    3. 组件不应该改变prop的值，而state的存在就是为了让组件来改变。
    
    
<a id="context"></a>
- **React的context**
1. 使用prop给内部子组件传递数据时需要一层一层的传递，即使中间有组件不需要使用，这样比较麻烦；
2. 使用context可以实现跨级传递。
  - **context使用步骤**
    1. 父组件通过getChildContext()方法将需要传入的信息放进context，并声明childContextTypes（如果不声明无法再组件中使用getChildContext()）;
    2. 要使用的子组件中通过声明contextTypes（需要和父组件一致）就可以通过组件实例的context属性访问接收到的数据；
    3. 无状态的组件可以在函数参数中获取context；而又状态的组件可以通过this.context和生命周期函数获取context。
    eg:父组件生命
    ```
    class Parent extends React.Component{
      getChildContext(){
        return {color: "red"}      
      }
      
      render(){
        return(
          <div>
            <Child />
          </div>
        )
      }
    }
    
    Parents.childContextTypes = {
      color: PropTypes.string.isRequired
    }
    ```
    (有状态)子组件：
    ```
    class Child extends React.Component{
      render(){
        return(
          <div>
            <p style = {{color:{this.context.color}}}>有状态的组件可以通过this.context获取</p>
            <Grandchild />
          </div>
        )
      }
    }
    
    Child.contextTypes = {
      color: PropTypes.string.isRequired
    }
    ```
    (无状态)孙子组件：
    ```
    function Grandchild(context){
      return(
        <p style = {{color: {context.color}}}>无状态组件可以直接在函数的参数中获取</p>
      )
    }
    
    Grandchild.contextTypes = {
      color:PropTypes.string.isRequired
    }
    ```
***********************************************************************************************
<a id="ReactElement与组件实例"></a>
#### 2. ReactElement、组件与组件实例
- **2.1、ReactElement**
1. JSX允许在JavaScript中写嵌套的闭合标签，那么JSX中的闭合标签是什么呢？打印出来：
```
console.log(<h1>hello world</h1>);
console.log(<App />);
```
![ReactElement运行截图](https://github.com/Hui0830/studyNotes/blob/master/Images/ReactElement1.JPG)
- 运行程序后，在浏览器控制台可以看到打印了两个$$typeof为Symbol（react.element）的对象，这个对象就叫做ReactElement。
> **注意：** 只有在React中使用JSX，闭合标签才会被编译为ReactElement，如果在其他框架或库中使用JSX，闭合标签会根据编译器不同而被编译为不同的对象。


- **2.2、ReactElement是什么？**
1. ReactElement是一个不可变的普通对象，他描述了一个组件实例或一个DOM节点；
2. ReactElement只包含组件的类型（比如h1、或者App）、属性以及子元素等信息；
3. ReactElement不是组件实例，所以不能在ReactElement中调用React组件的任何方法；
4. ReactElement只是告诉你想在屏幕上显示什么。
- ReactElement的两种类型：
  - 从打印出来的截图，可以发现，两个ReactElement对象的type属性有所不同，这正代表着ReactElement的两种类型
    1. 当ReactElement的type属性是一个字符串，它表示一个DOM节点，它的prop属性就对应了DOM的属性`console.log(<h1>hello world</h1>)`就是这种；
    2. 当ReactElement的type属性是一个表示数组的类或函数时，则它表示一个组件`console.log(<App /> )`打印出来的就是这种。
**两种ReactElement可以相互嵌套，彼此混合来描述DOM树；ReactElement在React中非常重要，在编写组件时，开发者大部分时间都花在设计和编写ReactElement所组成的元素树上**

<a id="React的渲染流程"></a>
- **2.3、React的渲染流程**
 1. 当React遇到组件的ReactElement时，它会给这个ReactElement表示的组件一些props（即数据，有时也包括context），然后问该组件渲染的ReactElement是      什么；
 2. 如果渲染的任然是表示组件的ReactElement，那么将会一直问下去，直到了解到所有组件要渲染的DOM元素为止；
 3. 解析出所有要渲染的DOM元素之后，React就使用react-dom或react-native这样的渲染模块来执行渲染。
> 1. **对一个组件来说，props就是输入，ReactElement就是输出；**
> 2. 此特性不仅清晰的表述了组件把数据渲染成视图的过程，而且方便了性能优化；
> 3. B比如，对一个庞大的DOM树，如果每次都要重新渲染，那么势必会极大的消耗性能；但如果在渲染之前，先判断一下输入的props是否改变，然后在决定是否渲         染，这将会有效的避免不必要的渲染，进而提升性能。
  
  
<a id="组件实例"></a>
- **2.4、组件实例**
**1. 组件实例简介：**
> - 组件实例是组件类的实例化对象，它通常用来管理内部状态、处理生命周期函数；
> - 大多数情况下，我们无需创建组件实例，React会负责创建它；
> - ReactDOM.render()返回的就是组件实例；
> - 某些组件方法中的this也指向组件实例，利用组件的Refs属性也可以获取组件实例（如果在DOM元素上使用Refs,将获取相应的真实DOM节点）。
**@注意：无状态的函数没有实例化对象，因此没有内部状态，也无法使用生命周期函数。**
  
<a id="区别"></a>
- **2.5、组件、ReactElement与组件实例的区别**
```
import React from 'react';
import {render} from 'react-dom';

import App from './App';

const componentInstance = render(<App />, document.getElementById('root'));

console.log('组件、ReactElement与组件实例');
console.log(App);
console.log(<App />);
console.log(componentInstance);
```
![组件、ReactElement与组件实例代码运行截图]()
1. **组件：**`console.log(App);`在控制台打印出一个名为App的类（JavaScript中类是基于Function原型的，因此这里打印了function App()）;
2. **ReactElement：**`console.log(<App />);`一个描述了组件实例的$$typeof为Symbol(react.element)的对象；
3. **组将实例：**`console.log(componentInstance);`一个名为App的组件类的实例化对象；
> 组件、ReactElement、组件实例是三个完全不同的概念：
> 1. 组件是一个函数或类，决定如何把数据变成视图；
> 2. ReactElement只是一个描述了组件实例或DOM节点的普通对象；
> 3. 组件实例则是组件类的实例化对象。

***********************************************************************************************
<a id="组件生命周期"></a>
#### 3. 组件生命周期
- **React严格定义了组件的生命周期，生命周期可能会经历如下三个过程：**
 - **装载过程（Mount）**：也就是把组件第一次在DOM树上渲染的过程；
 - **更新过程（Updata）**：当组件被从新渲染的过程；
 - **卸载过程（Unmount）**：组件从DOM树中删除的过程。
1. 三种不同的过程，React库会调用组件的一些成员函数，即生命周期函数。


<a id="装载过程"></a>
- **3.1、装载过程**
 - 当组件第一次被渲染时，依次调用的函数：
   - construction
   - getInitalState
   - getDefaultProps
   - componentWillMount
   - render
   - componentDidMount
 1. **constructor**
> ES6中每个类的构造函数，要创建一个组件类的实例，便会调用对应的构造函数
> **注意：**
> 1. 并不是每个组件都需要定义自己的构造函数，无状态的React组件往往就不需要定义构造函数；
> 2.一个React组件需要构造函数目的：
>  - 初始化state，因为组件的生命周期中任何函数都可能要访问state，那么整个周期中第一个被调用的构造函数便是初始化state最理想的地方；
>  - 绑定成员函数的this环境：
>    - 因为在ES6语法下，类的每个成员函数在执行时的this并不是和类实例自动绑定的；
>    - 而在构造函数中this就是当前组件实例，所以，为了方便将来调用，往往在构造函数中将这个实例的特定函数绑定this为当前类实例：
 ```
       ...
       constructor(props){
          super(props);
          
          this.onClickFunc = this.onClickFunc.bind(this);
       }
  ```
  2. **getInitialState和getDefaultProps**
   1. getInitialState函数的返回值用来初始化组件的this.state;
   2. getInitialState只出现在装载过程，也就是说一个组件的整个生命周期过程中，这个函数只被调用一次；
   3. getDefaultProps函数的返回值可以作为props的初始值；
   4. **两个函数都只有在使用React.createClass方法创建组件类时才会用到**：
  ```
    const Sample = React.createClass({
      getInitialState: function() {
        return {foo: '返回值将作为this.state的初始值'};
      },
      getDefaultProps: function() {
        return {sampleProp: '作为props的初始值'}
      }
    })
  ```
   5. 使用ES6时,在构造函数中通过this.state赋值完成状态初始化；通过给类属性（**注意是类属性，而不是类的实例对象的属性**）defaultProps赋值指定的props初始值：
  ```
    class Sample extends React.Component{
      constructor (props){
        super(props);
        this.state = {foo: '初始值'}
      }
    }
    Sample.defaultProps = {
      sampleProps: 0
    }
 ```
   6. **React.createClass创建方法已经逐渐被Facebook官方废弃**
 3. **render**
   - **render函数是React组件中最重要的函数，一个React组件可以忽略其他所有函数都不实现，但一定要实现render函数，因为所有React组件的父类React.Component类对除了render之外的生命周期函数都有默认实现。**
   - 通常一个组件要发挥作用，总是要渲染一些东西，render函数并不做实际的渲染动作，它只是返回一个JSX描述结构，最终由React来操作渲染过程；
   - 当某个特殊的组件作用不是渲染界面，或者没有东西可画时，可让render函数返回null或者false，即告诉React此组件不渲染任何DOM元素；
   - **注意：**render函数应该是一个纯函数，完全根据this.state和this.props来决定返回的结果，而且不要产生任何副作用，不要在render函数中调用this.setState去改变状态，因为一个纯函数不应该引起状态的改变。
 4. **componentWillMount和componentDidMount**
 
   - 在装载过程中，componentWillMount会在render函数之前调用，此时还没有任何东西渲染出来，即使调用this.setState修改状态也不会发生重新绘制；
    
   - componentDidMount在render函数之后调用，但render调用之后并不会立即调用，而是在render函数返回的东西已经引发了渲染，组件已经被‘装载’到了DOM树上后，componentDidMount才被调用，此时已绘制出真实的DOM树；
    
   - **注意：**
> 1. render函数本身并不往DOM树上渲染或者装载内容，它只是返回一个表示JSX表示的对象（及组件实例），然后由React库根据返回的对象决定如何渲染；
> 2. 而React库肯定是要把所有组件返回的结果综合起来，才能知道如何产生对应的DOM修改；
> 3. 所以只有React库调用所有组件的render函数之后，才有可能完成DOM装载，这时候才会依调用componentDidMount函数作为装载的收尾。
> 4. componentWillMount可以在服务器和浏览器端被调用，而componentDidMount只能在浏览器端被调用（因为componentDidMount是在‘装载’完成之后被调用，且‘装载’是一个创建组件并放到DOM树上的过程，而服务器端渲染通过React组件产生的只是一个纯粹的字符串，并不会产生DOM树，即在服务器端不可能完成‘装载过程’所以无法调用componentDidMount）


<a id="更新过程"></a>
- **3.2、更新过程**
 - 随着用户的操作改变展示的内容，当props或者state被修改时，就会引发组件的更新过程；
 - 更新过程会依次调用以下生命周期函数，其中render函数和“装载”过程一样：
   - componentWillReceiveProps
   - shouldComponentUpdate
   - componentWillUpdate
   - render
   - componentDidUpdate
 - 并不是所有的更新过程都会执行全部函数。
 1. **componentWillReceiveProps（nextProps）**
   - 并不是只有在组件的props发生改变的时候才会调用此函数；
   - 在更新过程，只要是父组件的render函数被调用，在render函数里被渲染的子组件就会经历更新过程，不管父组件传给子组件的props有没有改变，都会触发子组件的componentWillReceiveProps函数；
   - **注意：**通过this.setState方法触发的更新过程不会调用这个函数；
   - 因为，这个函数适合根据新的props值（也就是参数nextProps）来计算是不是要更新内部状态state；而更新内部状态的方法是this.setState,如果this.setState的调用导致componentWillReceiveProps再调用，那将是一个死循环。
 2. **shouldComponentUpdate(nextProps,nextState)**
   - 除了render函数，shouleComponentUpdate可能是生命周期函数中最重要的一个函数；
   - 因为render函数决定了该渲染什么，shouldComponentUpdate决定了一个组件什么时候不需要渲染；
   - render和shouldComponentUpdate也是React生命周期函数中唯二两个要求有返回结果的函数；
   - render函数的返回结果用于构建DOM对象，shouldComponentUpdate函数返回一个布尔值，告诉React库这个组件这次更新过程是否继续；
   - 在更新过程中，React库首先调用shouldComponentUpdate函数，如果这个函数返回true，那就继续更新过程，接下来调用render，反之则终止此次更新过程；
   - shouldComponentUpdate的参数就是接下来的props和state值；我们可以根据这两个参数，外加this.props和this.state来判断返回true或false，从而避免不必要的更新。
 3. **componentWillUpdate和componentDidUpdate**<br>
  - 如果组件的shouldComponentUpdate返回true，React接下来调用componentWillUpdate、render和componentDidUpdate；<br>
  - 和“装载”过程不同，这对函数都可以在服务器和浏览器更新阶段调用<br>
  - 不过，通常在使用React做服务端渲染时，基本不会经历更新过程，因为服务器端只需要产出HTML字符串，而一个装载过程就足够产出HTML字符串了，所以正常情况下，服务器端不会调用componentDidUpdate函数，如果调用了，说明程序有错，需要改进。
    
    
<a id="卸载过程"></a>
- **3.2、卸载过程**<br>
 - React组件的卸载过程只涉及一个函数componentWillUnmount,<br>
 - 当React组件要从DOM树上删除之前，对应的componentWillUnmount函数会被调用，所以这个函数适合做一些清理性的工作。<br>
***********************************************************************************************
<a id="React组件的性能优化"></a>
#### 4. React组件的性能优化
***********************************************************************************************
<a id="React高级组件"></a>
#### 5. React高级组件
***********************************************************************************************
    
    
  
