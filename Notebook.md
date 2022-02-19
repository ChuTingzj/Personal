前端工程化:

   初始化项目

​    ① 新建项目空白目录，并运行 npm init –y 命令，初始化包管理配置文件 package.json

  ② 新建 src 源代码目录 

③ 新建 src -> index.html 首页和 src -> index.js 脚本文件 

④ 初始化首页基本的结构 

⑤ 运行 npm install jquery –S 命令，安装 jQuery

  ⑥ 通过 ES6 模块化的方式导入 jQuery 

   安包

​    在项目中安装webpack：npm install webpack@5.42.1 webpack-cli@4.7.2 -D

​    -S【--save】指令（dependencies节点之下）： 开发上线阶段都要用到

​    -D【--save-dev】指令（devDependencies节点之下）：只在开发阶段会用到

   配置

​    1、根目录下创建webpack.config.js的webpack配置文件

  module.exports = {

  mode:'development'  //设置模式（开发模式：development 生产模式：production）

}

  2、在package.json的scripts节点下，新增dev脚本

  "scripts": {

  "dev" : "webpack"

 },

  3、在终端中运行npm run dev

自定义打包的入口和出口

在webpack.config,js配置文件中通过entry节点指定打包的入口、output节点指定打包的出口

  const path = require(‘path’)     //导入node.js中操作路径的模块

  module.exports = {

​    entry:path.join(__dirname,’入口文件路径’)

​    output:{

​      path:path.join(__dirname,’出口文件存放路径’)

​      filename:’输出文件的名称’

}

}

Webpack插件（修改了 webpack.config.js 配置文件或 package.json 配置文件，必须重启实时打包的服务器）

​    1、webpack-dev-server （npm install webpack-dev-server@3.11.2 -D）

​      类似于 node.js 阶段用到的 nodemon 工具 

每当修改了源代码，webpack 会自动进行项目的打包和构建

配置

  ① 修改 package.json -> scripts 中的 dev 命令

​        "scripts": {

​         "dev" : "webpack serve"

​         },

  

② 再次运行 npm run dev 命令，重新进行项目的打包（失败则先执行npm install webpack-cli --save-dev【原因：没有加载到webpack-cli】，停止服务ctrl+c*2） 

③ 在浏览器中访问 http://localhost:8080 地址，查看自动打包效果

2、html-webpack-plugin（npm install html-webpack-plugin@5.3.2 -D）

  webpack 中的 HTML 插件（类似于一个模板引擎插件） 

​      可以通过此插件自定制 index.html 页面的内容

​    配置

  const HtmlPlugin = require('html-webpack-plugin')

const htmlPlugin = new HtmlPlugin({

  template:'./src/index.html',

  filename:'./index.html',

})

module.exports = {

  mode:'development',

  plugins:[htmlPlugin],

}

3、devServer节点

  配置

​    devServer:{

​      open:true,    //初次打包完成后，自动打开浏览器

​      host:’127.0.0.1’,   //实时打包所使用的主机地址

​      port:80,      //实时打包所使用的端口号

}

Loader加载器

1、   css-loader 可以打包处理 .css 相关的文件

① 运行 npm i style-loader@3.0.0 css-loader@5.2.6 -D 命令，安装处理 css 文件的 loader

② 在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

​    module：{

​      rules：[

​        {test:/\.css$/,use:[‘style-loader’,’css-loader’]}

]

}

【use 数组中指定的 loader 顺序是固定的】

【多个 loader 的调用顺序是：从后往前调用】

2、  less-loader 可以打包处理 .less 相关的文件

​                   ①     运行 npm i less-loader@10.0.1 less@4.1.1 -D 命令

​                   ②     在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下

​    module：{

​      rules：[

​        {test:/\.less$/,use:[‘style-loader’,’css-loader’,’less-loader’]}

]

}

 

3、打包处理样式表中与 url 路径相关的文件

​        ① 运行 npm i url-loader@4.1.1 file-loader@6.2.0 -D 命令

② 在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

​    module：{

​      rules：[

​        {test:/\.jpg|png|gif$/,use:’url-loader?limit=22229’}

]

}

【其中 ? 之后的是 loader 的参数项：

limit 用来指定图片的大小，单位是字节（byte）

只有 ≤ limit 大小的图片，才会被转为 base64 格式的图片】

4、  babel-loader 可以打包处理 webpack 无法处理的高级 JS 语法

①运行如下的命令安装对应的依赖包：

npm I babel-loader@8.2.2 @babel/core@7.14.6 @babel/plugin-proposal-decorators@7.14.5 -D

②在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下：

module：{

​      rules：[

​        {test:/\.js$/,use:’babel-loader’,exclude:/node_modules/}

]

}

③配置

在项目根目录下，创建名为 babel.config.js 的配置文件，定义 Babel 的配置项如下：

​            Module.exports = {

​              plugins:[[‘@baebl/plugin-proposal-decorators’,{legacy:true}]]

}

打包发布

① 配置webpack打包发布

​    在 package.json 文件的 scripts 节点下，新增 build 命令如下

​    "scripts": {

  "dev": "webpack serve",

  "build":“webpack –mode production”

 },

​    【--model 用来指定 webpack 的运行模式，production 代表生产环境，对打包的文件

进行代码压缩和性能优化，通过 --model 指定的参数项，会覆盖 webpack.config.js 中的 model 选项】

② 把 JavaScript 文件统一生成到 js 目录中

在 webpack.config.js 配置文件的 output 节点中，进行如下的配置

​    Output:{

​      Path:path.join(__dirname,’dist’),

Filename:’js/main.js’,

}

③ 把图片文件统一生成到 image 目录中

修改 webpack.config.js 中的 url-loader 配置项，新增 outputPath 选项即可指定图片文件的输出路径：

{

test:/\.jpg|png|gif$/,

use:{

​    loader: 'url-loader',

options:{

​    limit:22228,

​    outputPath:’image’,

},

}, 

}

④ 自动清理 dist 目录下的旧文件

为了在每次打包发布时自动清理掉 dist 目录中的旧文件，可以安装并配置 clean-webpack-plugin 插件：

//安装清理dsit目录的webpack插件

​    npm install [clean-webpack-plugin@3.0.0](mailto:clean-webpack-plugin@3.0.0) -D

//按需导入插件、得到插件的构造函数后，创建插件的实例对象

​    const {CleanWebpackPlugin} = require(‘clean-webpack-plugin’)

​    const cleanPlugin = new CleanWebpackPlugin()

//把创建的cleanPlugin插件的实例对象，挂载到plugins节点上

​    plugins:[htmlPlugin,cleanPlugin], //挂载插件

SourceMap

1、   Source Map 就是一个信息文件，里面储存着位置信息。

2、  也就是说，Source Map 文件中存储着压缩混淆后的代码，所对应的转换前的位置。

3、  有了它，出错的时候，Console将直接显示原始代码，而不是转换后的代码

开发环境下，推荐在 webpack.config.js 中添加配置，即可解决sourcemap的默认行为

​      devtool:’eval-source-map’

生产环境下，建议关闭sourcemap或者在 webpack.config.js 中添加配置

​      ⭐devtool:’nosources-source-map’(只显示行号不暴露源码)

  Ps：@用来指定一段文件的路径

原理：resolve:{

​    alias:{

​      '@':path.join(__dirname,'./src/') 

​    }

},

ES6

1、let与const

let

① let可以批量的声明与赋值

② let不能重复声明同一变量

③ let的块级作用域

④ let没有变量提升

⑤ let不影响作用域链（函数中没有找到变量会向外层作用域查找）

const

① const声明常量（不能修改）

② const必须赋初始值

③ 一般常量使用大写

④ const的块级作用域

⑤ 对数组和对象的修改（指针不变）不算做常量的修改

2、变量的解构赋值

1、 数组的解构赋值

const f4 = [1,2,3,4]

let [a,b,c,d] = f4

乱序解构

   let {3:a,2:b,1:c,0:d} = f4

2、    对象的解构赋值

const people = {

​     name:’张三’,

​     age:’21’,

​     sayHello:function(){

​       console.log(‘Hello!’)

},

}

let {name,age,sayHello} = people;

let{sayHello} = people;

3、   模板字符串``[反引号]

let str = `字符串`;

①内容中可以出现换行符

Let str = `<ul>

<li>a</li>

<li>b</li>

<li>c</li>

</ul>`

②变量的拼接

​          let a = ‘name’;

​          let b = `我的名字是${a}`

4、   简化对象的写法

let name = ‘张三’

let behavior = function(){

​    console.log(‘Wassup!’)

}

const people = {

​      name,

​      behavior,

improve(){

​      console.log(‘I can do this all day!’)

}

}

5、   箭头函数

声明无参函数

   Past：let fn = function(){}

   Latest：let fn = ()=>{}

声明带参函数

   Past：let fn = function(a,b){return a+b}

   Latest: let fn = (a,b)=>{return a+b} 

特性

\1.  this是静态的，始终指向函数声明时所在作用域下的this的值

function getName1(){

​    console.log(this.name)

}

let getName2() = () =>{

​    console.log(this.name)

}

window.name = ‘张三

const people = {

​      Name:’zhangsan’

}

//直接调用函数this都指向window

​                getName1();           //张三

​                getName2();          //张三

​                 //call方法调用(改变this指向)

​                getName1.call(people);   //zhangsan

​                getName2.call(people);  //张三

\2.   不能作为构造函数实例化对象

Let Person = (name,age) => {

​      This.name = name;

This.age = age;

}

Let people = new Person(‘张三’,30);

//输出结果报错

\3.  不能使用arguments变量

\4.  简写

①省略小括号（形参有且只有一个）

let add = n => {

​    return n+n

}

console.log(add(9))

②省略花括号（代码体有且只有一条语句,此时return必须省略，而且语句的执行结果就是函数的反回值）

Let pow = n => n * n；

6、   函数参数的默认值(具有默认值的参数通常放后)

Function add(a,b,c=10){

   Return a+b+c

}

Let result = add(1,2)

Console.log(result)    //结果为13

①与解构赋值结合

Function connect（{host=’127.0.0.1’,username,password,port}）{

​    Console.log(host)

​    Console.log(username)

Console.log(password)

Console.log(port)

}

Connect({

​      Host:’localhost’,

​      Username:’root’,

​      Password:’root’,

​      Port:80

})

7、   rest参数(代替arguments获取实参)

function data(…args){

   console.log(args);

}

data(1,2,3,4);       //返回一个数组

ps：rest参数必须放到最后，放在形参的位置

8、   扩展运算符(将数组转换为逗号分割的参数序列)

Const data = [‘张三’,’里斯’,’王五’]

Function chuancan(){

​     Console.log(arguments)

}

Chuancan(data);  //只有一个元素

Chuancan(…data); //三个元素

运用

①合并数组(array.concat)

Const a = [‘zhangsan’]

Const b = [‘lisi’]

Const res = […a,…b]  //[‘zhangsan’,’lisi’]

②克隆数组

Const a = [‘A’,’B’,’C’]

Const a1 = […a]

Console.log(a1)   //[‘A’,’B’,’C’]

③将伪数组转为数组

Const div = document.querySelectorAll(‘div’); //一个类数组

Const divArr = […div];

Console.log(divArr);     //结果为数组

④代替object.assign

Const obj1 = {name:’zhangsan’}

Const obj2 = {age:20}

Past:const obj3 = Object.assign(obj1,obj2);

Latest:const obj3 = {…obj1,..obj2}

9、  Symbol数据类型

特点

①Symbol的值是唯一的，用来解决命名冲突问题

②Symbol值不能与其他数据类型进行运算

③Symbol定义的对象属性不能用for…in遍历，但是可以使用Reflect.ownKeys来获取对象的所有键名

创建Symbol

​      let s = Symbol();

​      let s2 = Symbol(‘一些描述’);

​      let s3 = Symbol.for(‘一些描述’)

使用

​      //为对象添加属性和方法,不会破坏原有属性和方法

​      let game = {}

​      let methods = {

​          up:Symbol(),

​          down:Symbol()

};

game[methods.up] = function(){

​      console.log(‘Up方法’)

}

game[methods.down] = function(){

​      console.log(‘Down方法’)

}

 

Let youxi = {

[Symbol(‘say’)]: function(){

​    Console.log(‘Say方法’)

}

[Symbol(‘act’)]: function(){

​    Console.log(‘Act方法’)

}

}

​          Symbol的内置值

​            **hasinstance**

​              Class Person{

​      static [Symbol.hasinstance](param){

​      console.log(param)

​      console.log(‘用来检测类型’)

​      return true        //可以决定结果

}

}

let o = {}   //即param

console.log(o instanceof Person)

​            **isConcatSpreadable**

​             const arr = [1,2,3]

​              const arr1 = [4,5,6]

​              arr1[Symbol. isConcatSpreadable] = false //不可展开

​              console.log(arr.concat(arr1));

10、 迭代器(iterator接口)

原理

①创建一个指针对象，指向当前数据结构的起始位置

②第一次调用对象的next方法，指针自动指向数据结构的第一个成员

③接下来不断调用next方法，指针一直往后移动，直到最后一个成员

④每调用next方法返回一个包含value和done属性的对象

Ps:任何数据结构只要部署了iterator接口(属性)都可以完成for…of操作

​     Examples:Array,Arguments,Set,Map,String,TypedArray,NodeList

应用(自定义遍历数据)

const class = {

​     Name:’A班级’，

​     Stus:[

​       ‘zhangsan’,

​       ‘lisi’,

​       ‘wangwu’

],

[Symbol.iterator](){

   let index = 0;

   return {

   next() => {

​     if(index < this.stus.length){

​           const result = {value:this.stus[index],done:false}

​           index++

return resuult

}else{

​     return {value:undefine,done:true}

}

}

};

}

}

11、生成器

​      介绍：是一种异步编程的解决方案，一种特殊的函数

​        function * gen(){

​      console.log(‘aaaa’)

​      yield ‘bbb’;        //yield:函数代码的分隔符

​      console.log(‘bbbb’)

​      yield ‘ccc’;

}

let iterator = gen();    //输出结果为一个迭代器对象

iterator.next();        //直接调用’aaaa’    console.log(iterator.next())结果为一个带value和done属性的对象

for(let v of gen()){

​    console.log(v)     //结果为‘aaaa’,’bbb’,’bbbb’,’ccc’

}

​      函数参数

​        function * gen(arg){

​          console.log(arg)        //1、结果为AAA

​          let one =yield ‘aaa’;

​          console.log(one)       //3、结果为BBB

​          let two = yield ‘bbb’;

​          console.log(two)       //5、结果为CCC

​          yield ‘ccc’;

}

let iterator = gen(‘AAA’);

console.log(iterator.next());  //2、结果为{value:’aaa’,done:false}

console.log(iterator.next(‘BBB’));  4、//结果为{value:’bbb,done:false}【第二次调用传入的实参将作为第一个yield语句的返回结果】

console.log(iterator.next(‘CCC’));    //6、结果为{value:’ccc’,done:false}【第三次调用传入的实参将作为第二个yield语句的返回结果】

​    函数实例

​       场景1：1s后输出111，2s后输出222，3s后输出333

​        Past:[回调地狱]

setTimeout(()=>{

​      console.log(111);

​      setTimeout(()=>{

​      console.log(222)

​      setTimeout(()=>{

​      console.log(333)

},3000)

},2000)

},1000)

​        Latest:

​            function one(){

​    setTimeout(()=>{

​    console.log(111)

​    iterator.next();

},1000)

}

function two(){

​    setTimeout(()=>{

​      console.log(222)

iterator.next();

},2000)

}

function three(){

​    setTimeout(()=>{

​    console.log(333)

},3000)

}

 

function * gen(){

​    yield one(),

​    yield two(),

yield three()

}

 

let iterator = gen();

iterator.next();

​        场景2：模拟获取 用户数据 订单数据 商品数据

​          function getUsers(){

​    setTimeout(()=>{

​    let data = ‘用户数据’

​    iterator.next(data)   //第二次调用所传的实参将作为第一个yield语句的输出结果

},1000)

}

function getMenus(){

​    setTimeout(()=>{

​      let data = ‘订单数据’

​      iterator.next(data)   //第三次调用所传的实参将作为第二个yield语句的输出结果

},1000)

}

function getGoods(){

​    setTimeout(()=>{

​      let data = ‘商品数据’

iterator.next(data)   //第四次调用所传的实参将作为第三个yield语句的输出结果

},1000)

}

 

function * gen(){

​    let users = yield getUsers();

​    console.log(users)

​    let menus = yield getMenus();

console.log(menus)

​    let goods = yield getGoods();

​    console.log(goods)

}

let iterator = gen();

iterator.next()

12、Promise

​      介绍：是一种异步编程的解决方案，promise实际上是一个构造函数，用来封装异步操作并且可以获取其成功或失败的结果

​      状态：pending等待、fulfilled成功、rejected失败

​      **实例化promise对象**

​        const p = new Promise(function(resolve,reject){

​            setTimeout(function(){

​      let data = ‘成功读取数据’;

​      resolve(data);      //此时promise对象状态为成功

 

​      let err = ‘数据读取失败’

​      resolve(err);       //此时promise对象状态为失败

},1000)

});

 

//调用.then()方法

p.then(function(value){

​    console.log(value)    //状态成功则调用第一个函数

},function(reason){

console.log(reason)   //状态失败则调用第二个函数

})

​        **用promise读取文件**

​          const fs = require(‘fs’);

​          Past:

fs.readFile(‘./aaa.md’,(err,data)=>{

​      if(err) throw err;

​      console.log(data.toString())

});

​      Latest:

​           const p = new Promise(function(resolve,reject){

​                fs.readFile(‘./aaa.md’,(err,data)=>{

​                if(err){

​    reject(err)

}else{

​    resolve(data)

}

});

})

p.then(function(value){

​    console.log(value.toString())     //状态成功则调用第一个函数

},function(reason){

console.log(‘读取失败’)   //状态失败则调用第二个函数

})

​       **用promise发送Ajax请求**

​        Cosnt p = new Promise((resolve,reject)=>{

​       Const xhr = new XMLHttpRequest();

​       xhr.open(‘GET’,’https://api.apiopen.top/getJoke’);

​       xhr.send();

​       xhr.onreadystatechange = function(){

​           if(xhr.readyState === 4){

​     if(xhr.status >= 200 && xhr.status < 300 ){

​     //表示成功

​     resolve(xhr.response)

}else{

​     //表示失败

​     reject(xhr.status)

}

}

}

​      })

​     p.then(function(value){

​     console.log(value)

},function(reason){

​     console.error(reason)

})

**Promise.prototype.then****方法**

​    Const p = new Promise((resolve,reject)=>{

​        setTimeout(()=>{

​      resolve(‘用户数据’)

},1000)

})

p.then(function(value){

​    console.log(value)

},function(reason){

​    Console.error(reaon)

})

Ps:当抛出错误、返回结果是promise类型的且其状态为失败时，返回的结果都是失败状态的promise

​    当返回结果不是promise类型、返回结果是promise类型且其状态为成功时，返回的结果都是成功状态的promise

链式调用：一个then方法后可以接着多个then方法，即可以在then方法中继续嵌套异步任务

​      

Vue

   特性

①数据驱动视图

数据的变化会驱动视图自动更新

好处：只需维护数据，vue会自动渲染页面结构

②双向数据绑定

Js数据的变化，会被自动渲染到页面

页面上表单采集的数据发生变化，vue会自动获取，并更新到js数据中

​      Ps：底层原理M(数据源)V(视图)VM(vue的实例对象)

Vue-cli脚手架项目目录

​           Project name            //项目名

​        -build               //用来使用webpack打包使用的依赖

​        -config              //用来做整个项目的配置目录

​        -node_modules        //用来管理项目中使用依赖

​        -src                 //放置书写vue的源代码

​          assets             //存放静态资源

​          components        //书写vue的组件

​          router             //配置项目路由

​         app.vue            //项目的根组件

​         main.js             //项目的主入口

​        -static

​       -.babelrc             //将es6转为es5

​       -.editorconfig          //项目编辑配置

​       -.gitignore            //git忽略文件

​       -.postcssrc.js          //源码相关js

​       -index.html           //项目的主页

​       -package.json         //依赖管理

​       -package-lock.json      //加锁文件

​       -readme.md          //项目阅读文件

Vue2

​     内置变量$event(原生的DOM对象e)

            <div id=’app’>

​        <button @click=’add(1,$event)’>加</button>    //不传参默认参数是e

​      </div>

            <script>

​        const app = new vue({

​            el:’#app’,

​            data:{

​                count:0

},

​            methods:{

​                add(number,event){

​              this.count += n

​              if(this.count % 2 === 0){

​                e.target.style.backgroundColor = ‘red’

}else{

​                e.target.style.backgroundColor = ‘’

}

}

}

})

​      </script>

​     事件修饰符

1、   阻止事件的默认行为

@事件类型.prevent

2、  阻止事件冒泡（触发内部的事件的同时不触发外部的事件）

@事件类型.stop

3、  以捕捉模式触发当前的事件处理函数

@事件类型.capture

4、  绑定的事件只触发一次

@事件类型.once

5、  只有在event.target是当前元素自身时触发事件的处理函数

@事件类型.self

​     按键修饰符(结合键盘事件使用)

​                         @keyup.enter、@keydown.enter…………

​     指令

​        1、V-model

​            修饰符

①.number  自动将用户输入的值转换成数字

②.trim   自动过滤首尾的空白字符

③.lazy   在’change’时(失去焦点)监听事件，取代’input’

2、V-for

​      Ps:在v-for后要加上:key = ‘item.id’(key的值只能是字符串或数字)

​     过滤器(只限在vue2)

​              应用场景：插值表达式、v-bind属性绑定

​        Ps：① |    管道符   表示调用过滤器   

② 过滤器函数必须定义到filter节点下，且必须有返回值

③  过滤器函数的形参始终是管道符前的参数

④ 全局过滤器与私有过滤器重名时，优先调用私有过滤器

1、   插值表达式

<p>{{ message | 过滤器名}}</p>   //对message进行文本的格式化

​        2、v-bind属性绑定

                       <div v-bind:id=’rawid | formatId’></div>     //对rawid值进行格式化

​        3、定义全局过滤器(Vue.filter())

​                    Vue.filter()接收两个参数,第一个参数是过滤器的名字,第二个参数是全局过滤器的处理函数,无须定义在filter节点下

​            Vue.filter(‘过滤器名’,(形参) => {

​                return 返回结果

})

​     侦听器(watch)

​        定义到watch节点下

​      **语法格式**

​        const app = new Vue({

​            el:’#app’,

​            data:{username:’’},

​            watch:{

​          username【要监听的数据名】(newValue,oldValue){

​          console.log(newValue,oldValue)

​          //判断newValue是否为空

​          If(newValue == ‘’)return

​          //调用jquery的Ajax向服务器发送请求

​          $.get(‘…………’+newValue,function(res){

​    console.log(res)

})

}

}

}) 

​      **侦听器的格式**

1、   方法格式的侦听器

缺点：①无法在刚进入页面时自动触发

②如果侦听的是对象不会触发侦听器

2、  对象格式的侦听器

好处：  ①可以通过immediate选项，使其自动触

②可以通过deep选项深度侦听对象的每一个属性

Watch{

   Username:{

​      Handler(newValue,oldValue){

   Console.log(newValue,oldValue)

​    ………

},

​    Immdeicate:true   //控制是否自动触发

}

}

​              Ps：如果要侦听某一对象的子元素可以直接侦听其子元素

用法：’对象名.元素名’

​     计算属性

​        介绍:经过一系列运算后的得到的一个属性值,并且可以被模板结构或methods方法使用

​        Ps：定义到computed节点之下,计算属性在定义时要定义成方法格式

                   <div>{{rgb}}</div>

computed:{

​            rgb:function(){

​      return `rgb(${this.r},${this.g},${this.b})`

}

}

Git

   步骤

1、   git config –global user.name “ChuTingzj”

2、  git config –global user.email “[1752275697@qq.com](mailto:1752275697@qq.com)”

3、  git init        //初始化git

指令

​    1、git status       //查看状态

​    2、git add 文件名  //添加到缓存区

​    3、git commit -m ‘说明’  //提交至本地仓库

​    4、git log/git log --pretty=oneline  //查看版本，确定需要回到的时刻

​    5、git reset –hard  版本号          //版本回退

​    6、git reflog            //查看历史操作

​    7、git clone 地址      //克隆线上仓库到本地

​    8、git push           //提交到线上仓库

​    9、git pull            //拉取线上仓库代码

   分支

​    1、git branch        //查看分支

​    2、git branch 分支名    //创建分支

​    3、git checkout 分支名  //切换分支

​    4、git branch -d 分支名   //删除分支

​    5、git merge 被合并的分支名 //合并分支

   忽略文件

​    场景：在很多不变的文件目录或者还有一些即使有改动也不想提交到远程仓库的文件，可以使用忽略文件

​    Ps：忽略文件对当前目录及其子目录生效

​    创建：touch .gitignore

​    书写规则

​      /mtk/      //过滤整个文件夹

​      *zip       //过滤所有.zip文件

​      /mtk/do.txt   //过滤某个具体文件

​      !index.php      //不过滤具体的某个文件