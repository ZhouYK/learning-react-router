#react-router原理
<pre>
    深入理解react-router pre v4 和 after v4实现原理
</pre>
## pre v4
### 注册
<pre>
    react-router静态路由，需要在固定的地方（嵌套在Router内），按规定的格式(要有固定的属性)进行注册。
    路由需要显示的声明在Router的children中，并且按照固定的格式
    <Router history={history}>
        <Route path='/' component={App}>
            <Route path='/list' component={List}></Route>
            <自定义组件 path='/customer' component={() => {return '自定义组件'}}> </自定义组件>
        </Route>
    </Router>
</pre>

### 地址变化监听
<pre>
    history提供了地址变化的hook，listen(() => {
        // 设置Router的state，引起re-render
    })
</pre>
### 匹配
<pre>
    Router每次render会遍历this.props.children去匹配path，获取到对应的业务组件component，最后生成一个队列。
    再通过React.createElement对组件队列进行反向的递归，最后得到所有业务组件的element树。
    Router里render函数对业务组件element树进行渲染，从而得到正确的视图。

    这里需要注意：
    1，匹配路由是最开始的pathname来源于传入的history
    2，Router没有对this.props.children的类型进行区分，不管是Route还是自定义一个Component，只要使用时传入props
    包含有效的path和component都会能形成有效的路由注册


    关于render在react中的功能：(ReactDom.render以及Component中的render)

    render会返回一个和组件结构一致的plain object：

    jsx: <div className="app" >
           <Modal>我不知道</Modal>
           <Modal>我知道</Modal>
        </div>
    plain object: {
        type: "div",
        props: {
            className: "app",
            children: [{
                type: f Modal(options),
                props: {
                    children: "我不知道"
                }
            }, {
                type: f Modal(options),
                props: {
                    children: "我知道"
                }
            }]
        }
    }
    每一个render函数渲染什么（注意不是返回），由return的最外层Component的返回决定，以此形成一个递归，直到遇到dom element这些。（类别redux中间件的组织方式）

    总结：render返回的element(plain object)描述的是组件的结构（类比数据库里的表）；render中最外层Component相当于一个函数，它的返回决定了渲染的内容

    一句话：结构和展示是分离的

</pre>


## after v4

### 注册
<pre>
    不再需要在固定的地方书写路由，也不再支持Route嵌套。Route承担了渲染业务组件的功能
</pre>

### 地址变化监听

<pre>
    history提供了地址变化的hook，listen(() => {
        // 设置Router的state，引起re-render
    })
</pre>

### 匹配

<pre>
    由之前pre v4的集中式匹配和渲染，到现在分散的让单个Route自己匹配和渲染；
    由此可以方便的对Route路由进行动态的渲染，比如操作的依赖控制

    注意：
    这里Route是用传入的path和history的pathname进行全匹配，所以没有了Route的嵌套
</pre>