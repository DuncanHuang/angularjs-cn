#第一章 AngularJS简介

我们创造惊人的基于Web的应用程序的能力是不可思议的, 但是创建这些应用程序时所涉及的复杂性也是让人难以置信的. 我们的Angular团队希望减轻我们在参与开发AJAX应用程序时的痛苦. 在Google, 我们曾经在构建像Gmail, Maps, Calendar以及其他几个大型Web应用程序时经历了最痛苦的教训. 我想我们也许能够利用这些经验让更多的人受益.

我们希望在编写Web应用程序时感觉更像是第一次我们编写了几行代码并站在后面惊讶于它发生了什么. 我们希望编码的过程感觉更像是创造而不是视图满足Web浏览器其奇怪的内部运作工作.

与此同时, 我们还希望环境能够帮助我们作出设计选择, 使应用程序很容易创建并从一开始就很容易理解, 并且随着它们的不断成长, 正确的选择会让我们的应用程序易于测试, 扩展和维护.

我们视图在Angular中做到这些. 已经取得的结果让我们非常兴奋. 这很大程度上归功于Angular开源社区中的成员的出色工作和相互帮助, 同时也让我们学习到更多的东西. 我们希望你也加入到我们的社区中来, 并帮助我们努力让Angular变得更好.

你可以在我们的[Github主页](https://github.com/shyamseshadri/angularjs-book)的仓库中查看那些较大的或者较复杂的例子和代码片段, 你也可以拉取分支以及研究这些代码.

##概念

在你将使用的Angular应用中有几个核心的概念. 事实上, 任何这些概念并不是我们发明的. 相反, 我们从其他开发环境借鉴了大量成功的做法, 并使用包含HTML, 浏览器和许多其他Web标准的方式实现了它.

###客户端模板

多页面的Web应用程序都是通过组装和连接服务器上数据来创建HTML, 然后将完成的页面发送到浏览器中. 大多数的单页应用程序 - 也称为AJAX应用程序 - 这一点做的很好, 在某种程度上. Angular以不同的方式组装模板和数据并推送到浏览器中运行.  然后服务器将变成给模板提供静态资源和给这些模板提供所需的正确数据的角色.

让我们来看一个例子, 在Angular中如何在浏览器中组装模板和数据. 我们照惯例使用一个Hello, World的例子, 但是并不是编写一个"Hello, World"的单一字符串, 而是构造这个问候"Hello"作为稍后我们可能会改变的数据.

针对它, 我们创建了一个`hello.html`模板:

    <html ng-app>
    <head>
        <script src="angular.js"></script>
        <script src="controller.js"></script>
    </head>
    
    <body>
        <div ng-controller="HelloController">
            <p>{{greeting.text}}, World</p>
        </div>
    </body>
    </html>    
    
然后我们在`controller.js`中编写逻辑:

    function HelloController($scope){
        $scope.greeting = {text: 'Hello'};
    }

将`hello.html`载入任意浏览器中, 我们将看到如图1-1所示:

![Hello](figure/hello.png)

图1-1 Hello World

于现在使用广泛的大多数方法相比, 这里有一些有趣的事情需要注意:

+ HTML并没有类或者ID以确定在哪里添加事件监听器.
+ 当`HelloController`设置`greeting.text`为`Hello`时, 我们并没有注册任何事件监听器或者编写任何回调函数.
+ `HelloController`只是一个很普通的JavaScript类, 它并没有继承任何Angular提供的东西.
+ `HelloController`接收所需的`$scope`对象, 而无需创建它.
+ 我们并没有调用HelloController自身的构造器, 或者在调用它时来计算它.

接下来我们会看到更多的差异, 但是我们应该清楚, Angular应用程序的结构与过去类似的应用程序完全不同.

为什么我们作出了这些设计选择以及Angular是如何工作的? 让我们来看看Angular从其他地方借鉴的一个好的概念.

###模型/视图/控制器(MVC)

MVC应用程序结构是20世纪70年代被作为Smalltalk的一部分引进的. 从Smalltalk开始, MVC在几乎每一个涉及用户界面的桌面开发环境中都变得受欢迎. 无论你是使用C++, Java还是Object-C, 都有可以利用MVC的气息. 然而, 直到最近, MVC才开始应用于Web开发中. *[MVC was all but foreign to web development. 大意: 几乎与Web开发都不相关]*

MVC背后的核心思想是你可以在你的代码管理中清晰的分离数据(模型), 应用程序逻辑(控制器)以及给用户呈现数据(视图).

视图从模型中获取数据并显示给用户. 当用户通过点击或者输入与应用程序交互时, 控制器通过改变模型中的数据来响应. 最后, 该模型会通知视图它已经发生改变. 以便它可以更新它显示的信息. 

在Angular应用程序中, 视图就是文档对象模型(DOM), 控制器是JavaScript类, 模型数据存储在对象的属性中.

我们认为MVC的灵活主要是以下几个原因. 首先, 它给你在哪里摆放什么提供了一个智能模型, 因此你不需要每次都创造它. 其他人参与到你的项目中合作时, 将能够即时理解你已经编写好的部分, 因为他们会知道你使用了MVC结构来组织你的代码. 也许最重要的是, 它给你的应用程序更易于扩展, 维护和测试提供了极大的好处.

**译注, 参考阅读:** 

1. MVC是软件工程中的一种软件架构模式 - [MVC](http://zh.wikipedia.org/wiki/MVC)
2. Smalltalk是一门面向对象的程序设计语言 - [Smalltalk](http://zh.wikipedia.org/wiki/Smalltalk)

###数据绑定

之前常见的AJAX单页应用程序, 像Rails, PHP或者JSP平台都是在通过在发送给用户显示之前将数据合并到HTML字符串中来帮助我们创建用户界面(UI).

像jQuery这样的库也是将模型扩展到客户端并让我们使用类似的风格, 但是它只有单独更新部分DOM的能力, 而不能更新整个页面. 这里, 我们将数据合并到字符串形式的HTML模板中, 然后通过在一个占位元素中设置`innerHTML`将返回的结果插入到我们所希望的DOM元素中.

这一切都工作得很好, 但是当你希望插入新的数据到用户界面(UI)中时, 或者基于用户的输入改变数据, 你需要做一些相当不平凡的工作以确保你的数据状态的准确性, 无论是在用户界面中(UI)还是JavaScript属性中.

但是如果我们可以不用编写代码就能处理好这一些的工作会怎样? 如果我们可以只需声明用户界面的哪些部分对应JavaScript的哪些属性并让它们自动同步又会怎样? 这种编程风格被称为数据绑定. 由于它是MVC的一项伟大的工程, 便于你编写视图和模型时减少代码, 我们把它引入到了Angular中. 大部分将数据从一处迁移到另一处的工作都会自动完成.

来看看这一行为, 让我们使用第一个例子并让它动起来. 原来是, 一旦HelloController设置了其模型`greeting.text`, 它便不再改变. 让我们通过添加一个根据用户输入改变`greeting.text`值的文本输入框来改变这个例子, 让它能够活动.

这里是新的模板:

    <html ng-app>
    <head>
        <script src="angular.js"></script>
        <script src="controllers.js"></script>
    </head>
    
    <body>
        <div ng-controller="HelloController">
            <input ng-model="greeting.text" />
            <p>{{greeting.text}}, World</p>
        </div>
    </body>
    </html>

`HelloController`控制器可以保持不变.

将它载入到浏览器中, 我们将看到如图1-2所示屏幕截图:

![Hello with data binding](figure/hello2.png)

图1-2 应用程序的默认状态

如果我们使用'Hi'文本替换输入框中的'Hello', 我们将看到如图1-3所示截图:

![Hi](figure/hello3.png)

图1-3 改变文本框值之后的应用程序

我们并没有在输入字段上注册改变值的监听器, 我们有一个将会自动更新的UI.
同样的情况也适用于来自服务器端的改变. 在我们的控制器中, 我们可以对服务器发出一个请求, 获得响应, 然后设置`$scope.greeting.text`等于它返回的值. Angular会自动更文本输入框和双大括号中的text为该值.

###依赖注入

之前我们提到过, 在`HelloController`中有很多东西都可以重复, 这里我们并没有编写. 例如, `$scope`对象将自动绑定数据并传递给我们; 我们不需要通过调用任何函数来创建它. 我们只是通过将它放置在HelloController的构造器中来访问它.

正如我们将在后面的章节中会看到, `$scope`并不是我们唯一可以访问的事物. 如果我们希望将数据绑定到用户浏览器的URL地址中, 我们可以通过将用于管理它的对象`$location`放在我们的构造器中来访问它, 就像这样:

    function HelloController($scope, $location){
        $scope.greeting = {text: 'Hello'};
        //use $location for something good here...
    }
    
我们通过Angular的依赖注入系统达到这个神奇的效果. 依赖注入让我们得以延续这种开发的风格, 而不需要创建依赖, 我们的类只需要知道它需要什么.

在此之前, 它是一个被称为得墨忒耳定律的设计模式, 通常也被称作最少知识原则. 由于我们的HelloController的工作只是设置greeting模型的初试状态, 这种模式会告诉你无需担心其他的事情, 例如`$scope`是如何创建的, 或者在哪里可以找到它.

这个特性并不只是通过Angular框架创建的对象才有. 你也可以更好的按照这个风格编写其他的代码.

###指令

Angular最好的部分之一就是你可以如同编写HTML一样编写你的模板. 之所以可以这样做, 是由于这个框架的核心部分我们已经包含了一个的DOM解析引擎, 它允许你扩展HTML的语法.

我们已经在我们的模板中看到了一些不属于HTML规范的属性. 例如包含在双花括号中的数据绑定, 用于指定哪个控制器对应哪部分视图的`ng-controller`, 以及给输入框绑定一个模型的`ng-model`. 这些我们都成为HTML扩展指令.

Angular带有许多指定, 以帮助你定义应用程序的视图. 很快我们就会看到更多的指令. 这些指令可以定义来我们通常用作视图的模板中. 它们可以用于声明设置你的应用程序如何工作或者创建可复用的组件.

你并不仅限于使用Angular自带的指令. 你也可以编写你自己的HTML模板扩展, 做你想做的事情. 

##示例:购物车

让我们来看一个较大的例子, 它展示了更多的Angular的能力. 想象一下, 我们要创建一个购物应用程序. 在应用程序的某个地方, 我们需要展示用户的购物车并允许他编辑. 接下来我们直接跳到那部分.

    <html ng-app="myApp">
    <head>
    <title>Your Shopping Cart</title>
    </head>
    <body ng-controller="CartController">
        <h1>Your Order</h1>
        <div ng-repeat="item in items">
            <span{{item.title}}</span>
            <input ng-model="item.quantity" />
            <span>{{item.price | currency}}</span>
            <span>{{item.price * item.quantity | currency}}</span>
            <button onclick="remove($index)">Remove</button>
        </div>
        <script src="angular.js"></script>
        <script>
        function CartController($scope){
            $scope.items = [
                {title: 'Paint pots', quantity: 8, price: 3.95},
                {title: 'Polka dots', quantity: 17, price: 12.95},
                {title: 'Pebbles', quantity: 5, price: 6.95}
            ];
            
            $scope.remove = function(index){
                $scope.item.splice(index, 1);
            }
        }
        </script>
    </body>
    </html>

返回的用户界面截屏如图1-4所示:

![shopping-cart](figure/shopping-cart.png)

图1-4 购物车用户界面

下面是关于这个示例的简短参考. 本书其余的部分提供了更深入的讲解.

让我们从顶部开始:

    <html ng-app>
    
`ng-app`属性告诉Angular应该管理页面的哪一部分. 由于我们把它放在`<html>`元素中, 这将告诉Angular我们希望它管理整个页面. 这往往也是你所希望的, 但是如果你是结合现有的Angular应用程序来使用其他方式管理页面, 你可能希望把它放在应用程序中的某个`<div>`中.

    <body ng-controller="CartController">

在Angular中, 你用于管理页面区域的JavaScript类被称为控制器. 通过在body标签中包含一个控制器, 我们声明这个`CartController`将管理`<body>`和`</body>`之间的所有事物.

    <div ng-repeat="item in items">

`ng-repeat`的意思就是对于被称为`$items`的数组的每一个元素都复制一次`<div>`中的DOM. 在每一个复制的div副本中, 我们都会给当前元素设置一个名为`item`的属性, 因此我们可以在模板中使用它. 正如你所看到的, 这将导致这三个`<div>`的每一个都包含产品的标题, 数量, 单价, 总价以及一个用于移除整个项的按钮.

    <span>{{item.title}}</span>
    
正如我们在"Hello, World"例子中所示,  数据绑定通过`{{ }}`让我们将变量的值插入到页面部分并保持同步. 完整的表达式`{{item.title}}`会迭代检索当前item, 然后将由item的标题属性组成内容插入到DOM中.

    <input ng-model="item.quantity">
   
文本输入字段和`item.quantity`的值之间的`ng-model`定义并创建数据绑定.

`<span>`中的`{{ }}`用于设置一个单向的联系, 意思就是"在这里插入一个值". 这是我们希望的效果, 但是当用户改变单价时应用程序也需要知道, 这样它就能够改变总价.

我们通过使用`ng-model`来同步模型中的变化. `ng-model`声明将`item.quantity`的值插入到文本域中, 每当用户输入一个新的值时它也能够自动更新`item.quantity`的值.

    <span>{{item.price | currency}}</span>
    <span>{{item.price * item.quantity || currency}}</span>
    
我们希望单价和总价被格式化为美元形式. Angular自带了一个称为过滤器的特性让我们可以转换文本, 这里绑定了一个称为`currency`的过滤器用于给我们处理这里的美元格式. 在下一章我们将会看到更多的过滤器.

    <button ng-click="remove($index)">Remove</button>
    
这允许用户通过点击产品旁边的Remove按钮来从他们的购物车中移除项目. 我们设置它点击这个按钮时调用`remove()`函数. 我们还给它传递了一个`$index`, 它包含了`ng-repeat`索引值, 因此我们可以知道哪一项将会被移除.

    function CartController($scope)
    
这个`CartContoller`用于管理购物车的逻辑. 这会告诉Angular, 控制器需要在这里给他设置一个称为`$scope`的东西. 这个$scope用于让我们在用户界面中将数据绑定到元素中.

    $scope.items = [
        {title: 'Paint pots', quantity: 8, price: 3.95},
        {title: 'Polka dots', quantity: 17, price: 12.95},
        {title: 'Pebbles', quantity: 5, price: 6.95}
    ];
    
通过定义`$scope.items`, 我们创建了一个虚拟的数据哈希表来描述用户购物车中的物品集. 我们希望它可以用于UI中的数据绑定, 因此将他添加到$scope中.

当然, 一个真实版本的购物车不可能只在内存中工作, 它需要访问服务器中正确存储的数据. 我们将在后面的章节中讨论这些.

    $scope.remove = function(index){
        $scope.items.splice(index, 1);
    }
    
我们还希望`remove()`函数能够用于用户界面中的数据绑定, 因此我们同样将它添加到$scope中. 对于这个工作在内存中的版本的购物车, `remove()`函数可以即时从数组中删除项目. 由于`<div>`列表是通过`ng-repeat`创建的数据绑定, 所以当项目消失时列表将自动收缩. 记住, 每当用户在UI上点击一个Remove按钮时, 这个`remove()`函数就会被调用.

##小结

我们已经看到了Angular最基本的用法以及一些非常简单的例子. 本书后面的部分将专注于介绍这个框架提供的功能.