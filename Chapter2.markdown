#Angular应用程序剖析

不像典型的库, 你需要挑选你喜欢的功能, 在Angular中所有的东西都被设计成一个用于协作的套件. 在本章中我们将涵盖Angular中所有的基本构建块, 这样你就可以理解如何将它们组合在一起. 这些块都将在后面的章节中有更详细的讨论.

##启用Angular

任何应用程序都必须做两件事来启用Angular:

1. 加载`angular.js`库
2. 使用`ng-app`指令来告诉Angular它应该管理哪部分DOM

###加载脚本

加载库很简单, 与加载其他任何JavaScript库遵循同样的规则. 你可以从Google的内容分发网络(CDN)中载入脚本, 就像这样:

    <script src="http://ajax.google.com/ajax/libs/angularjs/1.0.4/angular.min.js"></script>
    
推荐使用Google的CDN. Google的服务器很快, 并且这个脚本是跨应用程序缓存的. 这意味着, 如果你的用户有多个应用程序使用Angular, 那么他将只需要下载脚本一次. 此外, 如果用户访问过其他使用Google CDN连接Angular的站点, 那么他在访问你的站点时就不需要再次下载该脚本.

如果你更喜欢本地主机(或者其他的方式), 你也可以这样做. 只需要在`src`中指定正确的地址.

###使用ng-app声明Angular的界限

> 原文是Boundaries, 意思是声明应用程序的作用域, 即Angular应用程序的作用范围.

`ng-app`指令用于让你告诉Angular你期望它管理页面的哪部分. 如果你在创建一个完全的Angular应用程序, 那么你应该在`<html>`标签中包含`ng-app`部分, 就像这样:

    <html ng-app>
    …
    </html>
    
这会告知Angular要管理页面中的所有DOM元素. 

如果你有一个现有的应用程序, 要求使用其他的技术来管理DOM, 例如Java或者Rails, 你可以通过将它放置在页面的一些元素例如`<div>`中来告诉Angular只需要管理页面的一部分即可.

    <html>
    …
        <div ng-app>
        …
        </div>
    …
    </html>
    
###模型/视图/控制器

在第一章中, 我们提到Angular支持模型/视图/控制器的应用程序设计风格. 虽然在设计你的Angular应用程序时有很大的灵活性, 但是总是别有一番风味的:

+ 模型包含代表你的应用程序当前状态的数据
+ 视图显示数据
+ 控制器管理你的模型和视图之间的关系

你需要使用对象属性的方式创建模型, 或者只包含原始类型的数据. 这里并没有特定的模型变量. 如果你希望给用户显示一些文本, 你可以使用一个字符串, 就像这样:

    var someText = 'You have started your journey';
    
你可以通过编写一个模板作为HTML页面, 并从模型中合并数据的方式来创建视图. 正如我们已经看过的, 你可以在DOM中插入一个占位符, 然后再像这样设置它的文本:

    <p>{{someText}}</p>
    
我们调用这个双大括号语法来插入值, 它将插入新的内容到一个现有的模版中.

控制器就是你编写用于告诉Angular哪些对象和原始值构成你的模型的类, 通过将这些对象或者原始值分配给`$scope`对象传递到控制器中.

    function TextController($scope){
        $scope.someText = someText;
    }

把他们放在一起, 我们得到如下代码:

    <html ng-app>
    <body ng-controller="TextController">
        <p>{{someText}}</p>
        
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.0.1/angular.min.js"></script>
        
        <script>
            function TextController($scope){
                $scope.someText = 'You have started your journey';
            }
        </script>
    </body>
    </html>
    
将它载入到浏览器中, 你就会看到

> 'You have started you journey'

虽然这个原始风格的模型工作在简单的情况下, 然而大多数的应用程序你都希望创建一个模型对象来包裹你的数据. 我们将创建一个信息模型对象, 并用它来存储我们的`someText`. 因此不是这样的:

    var someText = 'You have started your journey';
    
你应该这样编写:

    var messages = {};
    messages.someText = 'You have started your journey';
    function TextController($scope){
        $scope.messages = messages;
    }
    
然后在你的模板中这样使用:

    <p>{{messages.someText}}</p>
    
正如我们后面会看到, 当我们讨论`$scope`对象时, 像这样创建一个模型对象将有利于防止从`$scope`对象的原型中继承的意外行为.

我们正在讨论的这些方法从长远看来能够帮助你, 在上面的例子中, 我们在全局作用域中创建了`TextController`. 虽然这是一个很好的例子, 但是正确定义一个控制器的做法应该是将它作为模块的一部分, 它给你的应用程序部分提供了一个命名空间. 更新之后的代码看起来应该是下面这样.

    <html = ng-app="myApp">
    <body ng-controller="TextController">
        <p>{{someText.message}}</p>
        
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.0.1/angular.min.js"></script>
        
        <script>
            var myAppModule = angular.module('myApp',[]);
            
            myAppModule.controller('TextController', function($scope){
                var someText = {};
                someText.message = 'You have started your journey';
                $scope.someText = someText;
            });
        </script>
    </body>
    </html>
    
在这个版本中, 我们声明模块中`ng-app`元素的名称为`myApp`. 然后我们调用Angular对象创建了一个名为myApp的模块, 然后调用模块的`controller`方法并将我们的控制器函数传递给它.

一会儿我们就会知道为什么, 以及如何获取所有的模块. 但是现在, 只需要记住将所有的信息都保存在全局的命名空间中是一件好事, 并且这也是我们使用模块的机制.

##模板和数据绑定

在Angular应用程序中模板只是HTML文档, 就像我们从从服务端载入或者定义在`<script>`标签中的任何其他静态资源一样. 在你的模板中定义用户界面, 可以使用标准的HTML加Angular指令来定义你所需要的UI组件.

一旦进入浏览器中, Angular就会进入到你的整个应用程序中通过合并模板和数据的方式来解析这些模板. 在第一章中我们已经在购物车应用中看过了显示一个项目列表的例子.

    <div ng-repeat="item in items">
        <span>{{item.title}}</span>
        ...
    </div>
    
这里, 它只是外层`<div>`的一个副本, 里面所有的一切, 都一一对应`items`数组中的每个元素.

那么这些数据从哪里来? 在我们的购物车例子中, 在我们的代码中我们只将它定义为一个数组. 对于你开始创建一个UI并希望测试它是如何工作的, 这是非常合适的. 然而大多数的应用程序, 将使用一些服务器上的持久性数据. 在浏览器中你的应用程序连接你的服务器, 用户在页面上请求他们所需要的一切, 然后Angular将它[请求的数据]与你的模板合并.

基本的运作流程看起来像这样:

1. 用户请求你的应用程序的第一个页面
2. 用户浏览器发出一个HTTP请求连接到你的服务器, 然后加载包含模板的*index.html*页面
3. Angular载入到页面中, 等到页面完全加载, 然后查询定义在模板范围内的`ng-app`
4. Angular遍历模板并查询指令和绑定. 这将导致注册事件监听器和DOM操作, 以及从服务器上获取初始数据. 这项工作的最终结果是展示应用程序并将模板作为DOM转换为视图.
5. 连接到你的服务器加载你需要展示给用户所需的附加数据.

第1步至第3步是每个Angular应用程序的标准. 第4步和第5步对你来说是可选的. 这些步骤可以同步或者异步发生. 出于性能的考虑, 你应用程序所需的数据在第一个视图中[首屏]显示给用户, 可以减少并避免重复的请求HTML模板.

通过使用Angular组织你的应用程序, 你可以在你的应用程序中分离模板和数据. 这样做的结果是这些模板是可以缓存的. 在第一次载入之后, 实质上浏览器中就只需要请求新的数据了. 正如JavaScript, 图片, CSS以及其他资源, 缓存这些模板可以给你的应用程序提供更好的性能.

###显示文本

你可以使用`ng-bind`指令在你UI的任何地方显示和更新文本. 它有两种等价的形式. 一种是我们见过的双花括号形式:

    <p>{{greeting}}</p>
    
然后就是一个被称为`ng-bind`的基于属性的指令:

    <p ng-bind="greeting"><p>
    
这两者的输出是等价的. 如果模型中的变量`greeting`设置为"Hi, there", Angular将生成这样的HTML:

    <p>Hi, there</p>
    
浏览器将显示"Hi, There".

那么为什么你会使用上面的另外一种形式? 我们创建的双括号插入值的语法读起来更加自然并且只需要更少的输入. 虽然两种形式产生相同的输出, 但使用双花括号语法, 加载你应用程序的第一个页面`index.html`时, 在Angular替换花括号中的数据之前, 用户可能会看到一个未渲染的模板. 随后的视图将不会经历这一点.

原因是浏览器加载HTML页面, 渲染它, 直到那时Angular才可能准备解析它们.

好消息是你仍然可以在大多数模板中使用`{{ }}`. 然而, 在你的`index.html`页面中绑定数据, 应该使用`ng-bind`. 这样, 直到数据加载完你的用户将什么也看不到.

###表单输入

在Angular中处理表单元素是很简单的. 正如我们见过的几个例子, 你可以使用`ng-model`属性绑定到你的模型属性元素上. 这适用于所有标准的表单元素, 例如文本输入框, 单选按你, 复选框等等.  我们可以像这样绑定一个复选框到一个属性:

    <form controller="SomeController">
        <input type="checkbox" ng-model="youCheckedIt">
    </form>

这意味着:

1. 当用户选择复选框, `SomeController`的`$scope`中一个名为`youCheckedIt`的属性将变成true. 取消选择时使`youCheckedIt`变成false.
2. 如果你在`SomeController`中设置`$scope.youCheckedIt`为true, 这个复选框在UI中会被自动选择. 设置它为false则取消选择.

现在我想说的是我们真正想要的是, 当用户做了一些什么事情时作出响应. 对于文本输入框元素, 你使用`ng-change`属性来指定一个控制器方法, 那么无论什么时候用户改变输入框的值时, 这个控制器方法都应该被调用. 让我们做一个简单的计算器来帮助用户自己理解他们需要多少钱才能得到某些东西:

    <form ng-controller="StartUpController">
        Starting: <input ng-change="computeNeeded()" ng-model="funding.startingEstimate">
        Recommendation: {{funding.needed}}
    </form>
    
对于我们这个简单的例子, 让我们只设置输出用户预算十倍的值. 我们还将设置一个默认为0的值来开始:

    function StartUpController($scope){
    
        $scope.funding = { startingEstimate: 0 };
        
        $scope.computeNeeded = function(){
            $scope.needed = $scope.startingEstimate * 10;
        };
        
    }
    
然而, 前面的代码中有一个潜在的策略问题. 问题是当用于在文本输入框中输入时我们只是重新计算了所需的金额. 如果这个输入框只在用户在这个特定的输入框中输入时更新, 这工作得很好. 但是如果其他的输入框也在模型中绑定了这个属性会怎样呢? 如果它从服务器获取数据来更新又会怎样?

无论这个字段如何更新, 我们要使用一个名为`$watch()`的`$scope`函数[$scope对象的方法]. 我们将在本章的后面详细讨论`watch`方法. 基本的用法是, 可以调用`$watch()`并给他传递一个监控表达式和一个用于响应表达式变化的回调函数.

在这种情况下, 我们希望监控`funding.startEstimate`以及每当它改变时调用`computeNeeded()`. 然后我们使用这个方法重写了`StartUpController`.

    function StartUpController($scope){
    
        $scope.funding = { startingEstimate: 0 };
        
        $scope.computeNeeded = function(){
            $scope.needed = $scope.startingEstimate * 10;
        };
        
        $scope.$watch('funding.startingEstimate', computeNeeded);
        
    }
    
注意引号中的监控表达式. 是的, 它是一个字符串. 这个字符串是评估某些东西价格的Angular表达式. 表达式可以进行简单的运算和访问`$scope`对象的属性. 在本章的后面我们会涵盖更多关于表达式的信息.

你也可以监控一个函数返回值, 但是它并不会监控`funding.startingEstimate`, 因为它赋值为0, 并且0[初始值]不再会改变.

然后, 由于每当我们的`funding.statingEstimates`改变时`funding.needed`都会自动更新, 我们可以像这样编写一个更简单的模板.

    <form cn-controller="StartUpController">
        Starting: <input ng-model="funding.startEstimate">
        Recommendation: {{funding.needed}}
    </form>
    
在某些情况下, 你并不希望每一个改变都发生响应, 相反, 你希望等到用户来告诉你它准备好了. 例如可能完成购买或者发送一个聊天记录.

如果你的表单中有一组输入框, 那么你可以在这个表单上使用`ng-submit`指令给它指定一个提交表单时的回调函数. 我们可以让用户通过点击一个按钮请求帮助他们启动应用的方式来扩展上面的例子:

    <form ng-submit="requestFunding()" ng-controller="StartUpController">
        Starting: <input ng-change="computeNeeded()" ng-model="startingEstimate">
        Recommendation: {{needed}}
        <button>Fun my startup</button>
    </form>
    
    function StartUpController($scope){
        $scope.conputedNeeded = function(){
            $scope.needed = $scope.startingEstimate * 10;  
        };
        
        $scope.requestFunding = function(){
            window.alert("Sorry, please get more customers first.");
        };
    }

当尝试提交这个表单时, `ng-submit`指令也会自动阻止浏览器处理其默认的`POST`行为.

> 原文此处有错误, 表单提交的默认行为是`GET`.

在需要处理其他事件的情况下, 就像当你想要提供交互而不是提交表单一样, Angular提供了类似于浏览器原生事件属性的事件处理指令. 对于`onclick`, 你应该使用`ng-click`. 对于`ondblclick`你应该使用`ng-dblclick`等等.

我们可以尝试最后一次扩展我们的计算器启动应用, 使用一个重置按钮用于将输入框的值重置为0.

    <form ng-submit="requestFunding()" ng-controller="StartUpController">
        Starting: <input ng-change="computeNeeded()" ng-model="StartingEstimate">
        Recommendation: {{need}}
        <button>Fund my startup!</button>
        <button ng-click="reset()">Reset</button>
    </form>
    
    function StartUpController($scope){
    
        $scope.computeNeeded = function(){
            $scope.needed = $scope.startEstimate * 10;
        };
        
        $scope.requestFunding = function(){
            window.alert("Sorry, please get more customers first");
        };
        
        $scope.reset = function(){
            $scope.startEstimate = 0;
        }
    
    }
    
###不唐突JavaScript的一些话

在你JavaScript开发生涯的某些时刻, 有人可能会告诉你, 你应该编写"不唐突的JavaScript", 在你的HTML中使用`click`, `mousedown`以及其他类似的内联事件处理程序是不好的. 那么他是正确.

不唐突的JavaScript思想已经有很多解释, 但是其编码风格的原理大致如下:

1. 不是每个人的浏览器都支持JavaScript. 让每个人都能够看到你所有的内容和使用你的应用程序, 而不需要在浏览器中执行代码.

2. 有些人使用的浏览器工作方式不同. 视障人员使用的屏幕阅读器和一些手机用户并不能使用网站的JavaScript.

3. JavaScript在不同的平台工作机制不一样. IE浏览器通常是罪魁祸首. 你需要根据浏览器的不同而使用不同的事件处理代码.

4. 这些事件处理程序引用全局命名空间中的函数. 当你尝试整合其他库中的同名函数时, 它会让你头疼.

5. 这些事件处理程序合并了结构和行为. 这使你的代码更加难以维护, 扩展和理解.

总体来看, 当你按照这种风格编写JavaScript代码, 一切都很好. 然而有一件事并不是好的, 那就是代码的复杂度和可读性. 并不是给元素声明事件处理程序不起作用, 你通常给这些元素分配了ID, 获得这些元素的引用, 并给它设置了事件处理的回调函数. 你可以发明一个结构只用于清晰的创造它们之间的关联, 但大多数应用程序结束于设置在各处的事件处理函数.

在Angular中, 我们决定重新审视这个问题.

在这些概念诞生以来世界就已经改变了. 第1点, 这类有趣的群体已经不再有了. 如果你运行的浏览器不支持JavaScript, 那么你应该去使用20世纪90年代创建的网站. 至于第2点, 现代的屏幕阅读器已经跟上来了. 随着RAIA语义标签的正确使用,  你可以创造易访问的富UI应用. 现在手机上运行JavaScript与也能台式机能相提并论了.

因此现在的问题是: 重新恢复内联技术来解决我们第3点和第4点的可读性和简洁性的问题吗? 

正如前面所提到的, 对于大多数的内联事件处理程序, Angular都有一个等价形式的`ng-eventhandler="expression"`来替代`click`, `mousedown`, `change`等事件处理程序. 当用户点击一个元素时, 如果你希望得到一个响应, 你只需要简单的使用`ng-click`这样的指令:

    <div ng-click="doSomething()">…</div>
    
你的大脑里可能会说"不, 这样并不好"? 好消息是你可以放松下来. 这些指令不同于它们事件处理程序的前身(标准事件处理程序的原始形式):

+ 在每个浏览器中的行为一致. Angular会给你处理好差异.

+ 不会在全局命名空间操作. 你所指定的表达式仅仅能够访问元素控制器作用域内的函数和数据.

最后一点听起来可能有点神秘, 因此让我们来看一个例子. 在一个典型的应用程序中, 你会创建一个导航栏和一个随着你从导航栏选择不同菜单而变化的内容区. 我们可以这样编写它的框架:

    <div class="navbar" ng-controller="NavController">
    …
        <li class="menu-item" ng-click="doSomething()">Something</li>
    …
    </div>
    
    <div class="contentArea" ng-controller="ContentAreaController">
    …
        <div ng-click="doSomething()">…</div>
    …
    <div>

这里当用户点击navbar中的`<li>`和conent区中的`<div>`时都会调用一个称为`doSomething()`的函数. 作为开发人员, 你设置该函数调用你的控制器中的代码引用. 它们可能是相同或者不同的函数:

    function NavController($scope){
        $scope.doSomething = doA;
    }
    
    function ContentAreaController($scope){
        $scope.doSomething = doB;
    }    

这里, `doA()`和`doB()`函数可能时相同或者不同的, 取决于你给它们的定义.

现在我们还剩下第5点, 合并结构和行为. 这是一个有争议的话题, 因为你不能指出任何负面的结果, 但它与我们大脑里所想的合并表现职责和应用程序逻辑的行为非常类似. 当人们谈及关于标记结构和行为分离的时候, 这当然会有负面的影响.

如果我们的系统面临这种耦合问题时, 这里有一个简单的测试可以帮助我们找出来: 我们可以给我们的应用程序逻辑创建一个单元测试, 而不需要DOM的存在.

在Angular中, 是的, 我们可以在控制器中只编写包含业务逻辑的代码而不必引用DOM. 在我们之前编写的JavaScript中, 这个问题在事件处理程序中是不存在的. 注意, 在这里以及在这本书的其他地方, 目前我们所编写的控制器中, 都没有引用DOM和任何DOM事件处理程序. 你可以很轻松创建出这些不带DOM的控制器. 所有的元素定位和事件处理程序都发生在Angular中.

对于这个问题在编写单元测试时. 如果你需要DOM, 你在测试中创建它, 只会增加测试程序的复杂度. 当你的页面发生变化时, 你需要在你的测试中改变DOM, 这样只会带来更多的维护工作. 最后, 访问DOM是很慢的, 测试缓慢意味着反馈不会及时以及最终解析都是缓慢的. Angular的控制器测试并没有这些问题. 

因此你可以很轻松的声明事件处理程序的简单性和可读性, 毫无罪恶感的违反最佳实践.

###列表, 表格和其他重复的元素

最有用可能就是Angular指令, `ng-repeat`对于集合中的每一项都创建一次一组元素的一份副本. 你应该在你想创建列表问题的任何地方使用它.

比如说我们给老师编写一个学生花名册的应用程序. 我们可能从服务器获得学生的数据, 但是在这个例子中, 我们只在JavaScript将它定义为一个模型:

    var students = [{name: 'Mary Contrary', id:'1'},
                    {name: 'Jack Sprat', id: '2'},
                    {name: 'Jill Hill', id: '3'}];
                    
    function StudentListController($scope){
        $scope.students = students;
    }

我们可以像下面这样来显示学生列表:

    <ul ng-controller="">
        <li ng-repeat="student in students">
            <a href="/student/view/{{student.id}}">{{student.name}}</a>
        </li>
    </ul>
    
`ng-repeat`将会制作标签内所有HTML的副本, 包括标签内的东西. 这样, 我们将看到:

+ Mary Contrary
+ Jack Sprat
+ Jill Hill

分别链接到*/student/view/1, /student/view/2, /student/view/3*.

正如我们之前所见, 改变学生数组将会自动改变渲染列表. 如果我们做一些例如插入一个新的学生到列表的事情:

    var students = [{name: 'Mary Contrary', id:'1'},
                    {name: 'Jack Sprat', id: '2'},
                    {name: 'Jill Hill', id: '3'}];
                    
    function StudentListController($scope){
        $scope.students = students;
        
        $scope.insertTom = function(){
            $scope.students.splice(1, 0, {name: 'Tom Thumb', id: '4'});
        };
    }
    
然后在模板中添加一个按钮来调用:

    <ul ng-controller="">
        <li ng-repeat="student in students">
            <a href="/student/view/{{student.id}}">{{student.name}}</a>
        </li>
    </ul>
    <button ng-click="insertTom()">Insert</button>
    
现在我们可以看到:

+ Mary Contrary
+ Tom Thumb
+ Jack Sprat
+ Jill Hill

`ng-repeat`指令还通过`$index`给你提供了当前元素的索引, 如果是集合中第一个元素, 中间的某个元素, 或者是最后一个元素使用`$first`, `$middle`和`$last`会给你提供一个布尔值.

你可以想象使用`$index`来标记表格中的行. 给定一个这样的模板:

    <table ng-controller="AlbumController">
        <tr ng-repeat="track in album">
            <td>{{$index + 1}}</td>
            <td>{{track.name}}</td>
            <td>{{track.duration}}</td>
        </tr>
    </table>
    
这是控制器:

    var album = [{name: 'Southwest Serenade', duration: '2:34'},
                 {name: 'Northern Light Waltz', duration: '3:21'},
                 {name: 'Eastern Tango', duration: '17:45'}];
                 
    function AlbumController($scope){
        $scope.album = album;
    };
    
我们得到如下结果:

1. Southwest Serenade     2:34
2. Northern Light Waltz   3:21
3. Eastern Tango         17:45

###隐藏与显示

对于菜单, 上下文敏感的工具[*原文:context-sensitive tools*]以及其他许多情况, 显示和隐藏元素是一个关键的特性. 正如在Angular中, 我们基于模型的变化触发UI的改变, 以及通过指令将改变反映到UI中. 

这里, `ng-show`和`ng-hide`用于处理这些工作. 它们基于传递给它们的表达式提供显示和隐藏的功能. 即, 当你传递的表达式为true时`ng-show`将显示元素, 当为false时则隐藏元素. 当表达式为true时`ng-hide`隐藏元素, 为false时显示元素. 这取决于你使用哪个更能表达的你意图.

这些指令通过适当的设置元素的样式为`display: block`来显示元素, 设置样式为`display: none`来隐藏元素. 让我们看以个正在构建的Death Ray控制板的虚拟的例子:

    <div ng-controller="DeathrayMenuController">
        <p><button ng-click="toggleMenu()">Toggle Menu</button></p>
        <ul ng-show="menuState.show">
            <li ng-click="stun()">Stun</li>
            <li ng-click="disintegrate()">Disintegrate</li>
            <li ng-click="erase()">Erase from history</li>
        </ul>
    </div>
    
    function DeathrayMenuController($scope){
        $scope.menuState.show = false;
        
        $scope.toggleMenu = function(){
            $scope.menuState.show = !$scope.menuState.show;
        };
        
        // death ray functions left as exercise to reader
    };
    
###CSS类和样式

显而易见, 现在你可以在你的应用程序中通过使用{{ }}插值符号绑定数据的方式动态的设置类和样式. 甚至你可以在你的应用程序中组成匹配的类名. 例如, 你想根据条件禁用一些菜单, 你可以像下面这样从视觉上显示给用户.

有如下CSS:

    .menu-disabled-true {
        color: gray;
    }
    
你可以使用下面的模板在你的DeathRay指示`stun`函数来禁用某些元素:

    <div ng-controller="DeatrayMenuController">
        <ul>
            <li class="menu-disabled-{{isDisabled}}" ng-click="stun()">Stun</li>
            ...
        </ul>
    </div>
    
你可以通过控制器适当的设置`isDisabled`属性的值:

    function DeathrayMenuController($scope){
        $scope.isDisabled = false;
        
        $scope.stun = function(){
            //stun the target, then disable menu to allow regeneration
            $scope.isDisabled = 'true';
        };
    }
    
`stun`菜单项的class将设置为`menu-disabled-`加`$scope.isDisabled`的值. 因为它初始化为false, 默认情况下结果为`menu-disabled-false`. 而此时这里没有与CSS规则匹配的元素, 则没有效果. 当`$scope.isDisabled`设置为true时, CSS规则将变成`menu-disabled-true`, 此时则调用规则使文本为灰色.

这种技术也同样适用于嵌入内联样式, 例如`style="{{some expression}}"`.

虽然想法很好, 但是这里有一个缺点就是它使用了一个水平分割线来组合你的类名. 虽然在这个例子中很容易理解, 但是它可能很快就会变得难以管理, 你必须不断的阅读你的模板和JavaScript来正确的创建你的CSS规则.

因此, Angular提供了`ng-class`和`ng-style`指令. 它们都接受一个表达式. 这个表达式的计算结果可以是下列之一:

+ 一个使用空格分割类名的字符串
+ 一个类名数组
+ 类名到布尔值的映射

让我们想象一下, 你希望在应用程序头部的一个标准位置显示错误和警告给用户. 使用`ng-class`指令, 你可以这样做:

    .error {
        background-color: red;
    }
    .warning {
        background-color: yellow;
    }
    
    <div ng-controller="HeaderController">
        ...
        <div ng-class="{error: isError, warning: isWarning}">{{messageText}}</div>
        ...
        <button ng-click="showError()">Simulate Error</button>
        <button ng-click="showWarning()">Simulate Warning</button>
    </div>
    
    function HeaderController($scope){
        $scope.isError = false;
        $scope.isWarning = false;
        
        $scope.showError = function(){
            $scope.messageText = 'This is an error';
            $scope.isError = true;
            $scope.isWarning = false;
        };
        
        $scope.showWarning = function(){
            $scope.messageText = 'Just a warning. Please carry on';
            $scope.isWarning = true;
            $scope.isError = false;
        };
    }

你甚至可以做出更漂亮的事情, 例如高亮表格中选中的行. 比方说, 我们要构建一个餐厅目录并且希望高亮用户点击的那行.

在CSS中, 我们设置一个高亮行的样式:

    .selected {
        background-color: lightgreen;
    }

在模版中, 我们设置`ng-class`为`{selected: $index==selectedRow}`. 当模型中的`selectedRow`属性匹配ng-repeat的`$index`时设置class为selected. 我们还设置一个`ng-click`来通知控制器用户点击了哪一行:

    <table ng-controller="RestaurantTableController">
        <tr ng-repeat="restaurant in directory" ng-click="selectRestaurant($index)" ng-class="{selected: $index==selectedRow">
            <td>{{restaurant.name}}</td>
            <td>{{restaurant.cuisine}}</td>
        </tr>
    </table>

在我们的JavaScript中, 我们只设置虚拟的餐厅和创建`selectRow`函数:

    function RestuarantTableController($scope){
        $scope.directory = [{name: 'The Handsome Heifer', cuisine: 'BBQ'},
                            {name: 'Green\'s Green Greens', cuisine: 'Salads'},
                            {name: 'House of Fine Fish', cuisine: 'Seafood'}];
        $scope.selectRestaurant = function(row){
            $scope.selectedRow = row;
        };
    }
    
###`src`和`href`属性注意事项

当数据绑定给一个`<img>`或者`<a>`标签时, 像上面一样在`src`或者`href`属性中使用{{ }}处理路径将无法正常工作. 因为在浏览器中图片与其他内容是并行加载的, 所以Angular无法拦截数据绑定的请求.

对于`<img>`而言最明显的语法便是:

    <img src="/images/cats/{{favoriteCat}}">
    
相反, 你应该使用`ng-src`属性并像下面这样编写你的模板:

    <img ng-src="/images/cats/{{favoriteCat}}">

同样的道理, 对于`<a>`标签你应该使用`ng-href`:

    <a ng-href="/shop/category={{numberOfBalloons}}">some text</a>
    
###表达式

表达式背后的思想是让你巧妙的在你的模板, 应用程序逻辑以及数据之间创建钩子而与此同时防止应用程序逻辑偷偷摸摸的进入模版中.

直到现在, 我们一直主要是引用原生的数据作为表达式传递给Angular指令. 但是其实这些表达式可以做更多的事情. 你可以处理简单的数学运算(+, -, /, *, %), 进行比较(==, !=, >, <, >=, <=), 执行布尔逻辑运算(&&, !!, !)以及按位运算(\^, &, |). 你可以调用暴露在控制器的`$scope`对象上的函数, 你还可以引用数据和对象表示法([], {}, …).

下面都是有效表达式的例子:

    <div ng-controller="SomeController">
        <div>{{recompute() / 10}}<div>
        <ul ng-repeat="thing in things">
            <li ng-class="{highlight: $index % 4 >= threshold($index)}">
                {{otherFunction($index)}}
            </li>
        </ul>
    </div>

这里的第一个表达式`recompute() / 10`是有效的, 是在模板中设置逻辑很好的好例子, 但是应该避免这种方式. 保持视图和控制器之间的职责分离可以确保它们容易理解和测试.

虽然你可以使用表达式做很多事情, 它们由Angular自定义的解释器部分计算. 他们并不使用JavaScript的`eval()`执行, eval()有相当多的限制.

相反, 它们使用Angular自带的自定义解释器执行. 在里面, 你不会看到循环结构(for, while等等), 流程控制语句(if-else, throw)或者改变数据的运算符(++, --). 当你需要使用这些类型的运算时, 你应该在你的控制器中使用指令进行处理.

尽管表达式在很多方面比JavaScript更加严格, 但它们对`undefined`和`null`并不是很严格(更宽松). 模板只是简单的渲染一些东西, 并不会抛出一个`NullPointerException`的错误. 这样就允许你安全的使用模型而没有限制, 并且只要它们得到数据填充就让它们出现在用户界面中.

###分离用户界面(UI)和控制器职责

在你的应用程序中控制器有三个职责:

+ 在你的应用程序的模型中设置初试状态.[初始化应用程序]
+ 通过`$scope`暴露模型和函数到视图中.
+ 监控模型的改变并触发行为.

对于第一点第二点在本章的已经看过更多例子. 稍候我们会讨论最后一点. 然而, 控制器其概念上的目的, 是提供代码或者执行用户与视图交互愿望的逻辑. 

为了保持控制器的小巧和易于管理, 我们建议你针对视图的每一个区域创建一个控制器. 也就是说, 如果你有一个菜单则创建一个`MenuController`. 如果你有一个面包屑导航, 则编写一个`BreadcrumbController`, 等等.

你可能开始懂了, 但是需要明确的将控制器绑定到一个指定的DOM块中用于管理它们. 有两种主要的方式关联控制器与DOM节点, 一种方式是在模板中指定一个`ng-controller`属性, 另一种方式是通过`route`(路由)关联一个动态加载的DOM模板片段, 也称作视图.

我们将在本章的后面再讨论关于视图和路由的信息.

如果你的UI中有一个复杂的片段, 你可以通过创建嵌套的控制器, 通过继承树来共享模型和函数来保持你的代码间接性和可维护性. 嵌套控制器很简单, 你可以简单的在另一个DOM中分配一个控制器到一个DOM元素中做到这一点, 就像这样:

    <div ng-controller="ParentController">
        <div ng-controller="ChildController">…</div>
    </div>

虽然我们将这个表达为控制器嵌套, 实际的嵌套发生在作用域中($scope对象中). 传递给嵌套控制器的`$scope`继承自父控制器的`$scope`原型, 这意味着传递给`ChildController`的`$scope`将有权访问传递给`ParentController`的`$scope`的所有属性.

###使用作用域发布模型数据

将`$scope`对象传递给我们的控制器便是我们将模型数据暴露给视图的机制. 可能你的应用程序中还有其他的数据, 但Angular中只能够通过scope访问它可以访问的模型部分的属性. 你可以认为scope就是作为一个上下文环境用于在你的模型中观察变化的.

我们已经看过了很多明确设置作用域的例子, 就像`$scope.count = 5`. 也有一些间接的方法在模板内设置其自身的模型. 你可以像下面这样做:

1. 通过表达式. 由于表达式运行在控制器的作用域关联的元素的上下文中, 在表达式中设置属性与在控制器的作用域中设置一个属性一样. 

也就是像这样:  
   
    <button ng-click="count=3">Set count to three</button>
    
这样做也有相同的效果:

    <div ng-controller="CountController">
        <button ng-click="setCount()">Set count to three</button>
    </div>
    
CountController定义如下:

    function CountController($scope){
        $scope.setCount = function(){
            $scope.count = 3;
        }
    }
    
2. 在表单的输入框中使用`ng-model`. 在表达式中, 模型被指定为`ng-model`的参数也适用于控制器作用域范围. 此外, 这将在表单字段和你执行的模型之间创建一个双向数据绑定.

###使用$watch监控模型改变



