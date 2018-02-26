## 《深入浅出React和redux》细节汇总
- #### 一、[React简介](#React简介)
  - [create-react-app工具使用](#create-react-app工具使用)
  - [react思维](#react思维)
  - [VirtualDOM](#VirtualDOM)
  - [JSX](#JSX)
- #### 二、[React详解](#React详解)
  - [React组件数据](#React组件数据)
  - [React的两个对象：ReactElement与组件实例](#ReactElement与组件实例)
  - [组件生命周期](#组件生命周期)
  - [React组件的性能优化](#React组件的性能优化)
  - [React高级组件](#React高级组件)

  - [](#)
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
> 
1. 开发者不需要像jQuery一样详细的操作DOM着重于‘如何去做’,只需要着重于“我要显示什么”，而不用操心“怎样去做”;
2. react理念**UI = reader（data）**
> 1. 用户看到的界面（UI），是一个 **纯函数（render）** 的执行结果，只接受数据（data）作为参数；
> 2. 纯函数：没有任何副作用，输出完全依赖于输入的函数；
> 3. 对于react开发者，重要的是区分哪些属于data，哪些属于render，要更新界面，要做的就是更新data；
> 4. react实践的也是"响应式编程"的思想。
> 
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
    
    ```
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
    
    ```
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
- ReactElement
1. JSX允许在JavaScript中写嵌套的闭合标签，那么JSX中的闭合标签是什么呢？打印出来：
```
console.log(<h1>hello world</h1>);
console.log(<App />);
```
[aaaaa](!http://img.blog.csdn.net/20161010160433614?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
***********************************************************************************************
<a id="组件生命周期"></a>
#### 3. 组件生命周期
***********************************************************************************************
<a id="React组件的性能优化"></a>
#### 4. React组件的性能优化
***********************************************************************************************
<a id="React高级组件"></a>
#### 5. React高级组件
***********************************************************************************************
    
    
  
