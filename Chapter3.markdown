#第三章 AngularJS开发

现在, 我们已经探究了组成AngularJS的一些轮子. 我们已经知道用户进入我们的应用程序后如何获取数据, 如何显示文本, 以及如何做一些时髦的验证, 过滤和改变DOM. 但是我们要如何把它们组织在一起呢?

在本章, 我们将讨论以下内容:

+ 如何适应快速开发布局AngularJS应用程序
+ 启动服务器查看应用程序行为
+ 使用Karma编写和运行单元测试和场景测试
+ 针对产品部署编译和压缩你的AngularJS应用程序
+ 使用Batarang调试你的AngularJS应用程序
+ 简化开发流程(从创建文件到运行应用程序和测试)
+ 使用依赖管理库RequireJS整合AnguarJS项目

本章旨在提供一个20000英尺的视图以告诉你如何可行的布局你的AngularJS应用程序. 我们不会进入实际应用程序本身. 在第4章, 深入一个使用和展示了各种各样AngularJS特性的示例一用程序.

##项目组织

推荐使用Yeoman构建你的项目, 将会为你的AngularJS应用程序创建所有必要的辅助程序文件.

Yeoman是一个包含多个框架和客户端库的强大的工具. 它通过自动化一些日常任务的引导的方式提供了一个快速的开发环境和增强你的应用程序. 本章我们会使用一个完整的小节介绍如何安装和使用Yeoman, 但是在那之前, 我们先来简单的介绍以下使用Yeoman命令替代那些手动执行的操作.

我们还详细介绍了涉及到让你决定不使用Yeoman的情况, 因为在Windows平台的计算机上Yeoman确实存在一些问题, 并且设置它还稍微有一点挑战性.

对于那些不使用Yeoman的情况, 我们将会看看一个示例应用程序结构(可以在Github示例仓库的`chapter3/sample-app`目录中找到 - [链接](https://github.com/shyamseshadri/angularjs-book/tree/master/chapter3/sample-app)), 下面是推荐的结构, 也是使用Yeoman生成的结构. 应用程序的文件可以分为以下类别:

**JS源文件**

看看`app/scripts`目录. 这里是你所有的JS源文件所在目录. 一个主文件来给你的应用程序设置Angular模块和路由.

此外, 这里还有一个单独的文件夹--`app/scripts/controller`--这里面是各个控制器. 控制器提供行为和发布数据到作用域中, 然后显示在视图中. 通常, 它们与视图都是一一对应的.

指令, 过滤器和服务也可以在`app/scripts`下面找到, 不管是否优雅和复杂, 作为一个完整的文件(direcyives.js, filters.js, services.js)或者单个的都行.

**Angular HTML模板文件**

现在, 使用Yeoman创建的每一个AngularJS局部模板都可以在`app/views`目录中找到. 这是映射到大多数`app/scripts/controller`目录中.

还有另外一个重要的Angular模板文件, 就是主要的`app/index.html`. 这用户获取AngularJS源文件, 也是你为应用程序创建的任意源文件.

如果你最终会创建一个新的JS文件, 要确保把它添加到`index.html`中, 同时还要更新的主模块和路由(Yeoman也会为你做这些).

**JS库依赖**

Yeoman在`app/scripts/vendor`中为你提供了所有的JS源文件依赖. 想在应用程序中使用[Underscore](http://underscorejs.org/)和[SocketIO](http://socket.io/)? 没问题--将依赖添加到vendor目录中(还有你的`index.html`), 并开始在你的用用程序中引用它.

**静态资源**

最终你创建了一个HTML应用程序, 它还会考虑到你的应用程序还有作为需要的CSS和图像依赖. `app/styles`和`app/img`目录就是出于这个目的而产生的. 你只需要添加你需要的东西到目录中并在你的应用程序中引用它们(当然, 要使用正确的绝对路径).

> 默认情况下Yeoman不会创建`app/img`路径.

**单元测试**

测试是非常重要的, 并且当它涉及到AngularJS时是毫不费力的. 在测试方面`test/spec`目录应该映射到你的`app/scripts`目录. 每个文件都应该有一个包含它的单元测试的spec文件映射(镜像). 种子文件会给每个控制器文件创建一个存根文件, 在`test/spec/controllers`目录下, 与原来的控制器具有相同的名称. 它们都是Jasmine风格的规范, 描述了每个控制器预期行为的规范.

**集成测试**

AngularJS自带了端对端的测试支持以正确的方式内置到库里面. 你所有的Jasmine规范形式的E2E(端对端)测试, 都保存在`tests/e2e`目录下.

> 默认情况下Yeoman不会创建`test/目录`.

> 虽然E2E测试可能看起来像Jasmine, 但实际上不是的. 它们的函数是异步执行的, 来未来, 可以通过Angular场景运行器(Angular Scenario Runner)运行. 因此不要指望能够做正常情况下Jasmine测试所做的事情(像使用console.log重复输出一个值的情况).

还生成了一个简单的HTML文件, 可以在浏览器中打开它来手动的运行测试. 然而Yeoman不会生成存根文件, 但是它们遵循相似风格的单元测试.

**配置文件**

这里需要两个配置文件. 第一个是`karma.conf.js`, 这是Yeoman为你生成的用于运行单元测试的. 第二个, 是Yeoman不会生成的`karma.e2e.conf.js`. 这用于运行场景测试. 在本场尾部的继承RequireJS一节中有一个简单的文件. 这用于配置依赖关系的详情, 同时这个文件用在你使用karma运行单元测试的时候.

你可能会问: 如何运行我的应用程序? 什么是单元测试? 甚至我该如何编写你们所讨论的这种各样的零件?

别担心, 年轻的蚱蜢, 所有的这些在适当的时间都会解释. 在这一章里面, 我们将处理设置项目和开发环境的问题, 因此一旦我们掺入一些惊人的代码, 那些问题都可以快速的带过. 你所编写的代码以及如何将它们与你最终的惊人的应用程序联系在一起的问题, 我们将在接下来的几章中讨论.

##工具

AngularJS只是你开发实际网页的工具箱的一部分. 在这一节, 我们将一起开看看一些你用以确保高效和快速开发的不同的工具, 从IDEs到测试运行器到调试工具.

###IDEs

让我们从你如何编写源代码开始. 有大量的JavaScript编辑器可以选择, 有免费的也有付费的. 长时间以来的事实证明Emacs和Vi是开发JS的最好选择. 现在, 各种IDEs都自带了语法高亮, 自动完成以及其他功能, 它给你一个选择的余地, 这可能是值得的. 那么, 应该使用那一个呢?

如果你不介意付几十块钱(尽管它有一个30天的免费试用期), [WebStorm](www.jetbrains.com/webstorm/‎)是个不错的选择, 当今时代, WebStorm由JetBrains提供了最广泛的Web开发平台. 它所具有的特性, 在之前只有强类型语言才有, 包括代码自动完成(如图3-1所示, 指定浏览器版本), 代码导航, 语法和多无高亮, 同时支持多个库和框架的启动即可使用. 此外, 它还漂亮的集成了在IED中正确的调试JavaScript的功能, 而且这些都是基于Chrome执行的.

![ide](figure/3-1.png)

最大的你应该考虑使用WebStorm开发AngularJS原因是它是唯一继承AngularJS插件的IDEs. 这个插件支持在你的HTML模板中正确的自动完成AngularJS的HTML标签. 这些都是常用的代码片段, 否则你每次都要输入拼凑的代码片段. 因此不应该像下面这样输入:

	directive('$directiveName$', function factory($injectables$){
		var directiveDefinitionObject = {
			$directiveAttr$;
			compile: function complie(tElement, tAttrs, transclude){
				$END$;
				return function(scope, elements, attrs){
					//...
				}
			}
		};
		return directiveDefinitionObject;
	});

在WebStorm中, 你可以只输入以下内容:

	ngdc

然后按`Tab`键获取同样的代码. 这只是大多数代码自动完成插件提供的功能之一.

##运行你的应用程序

现在让我们讨论如何运行所有你所做的事情 - 查看应用程序活动起来, 在浏览器中. 真实的感受以下应用程序是如何工作, 我们需要一个服务器来服务于我们的HTML和JavaScript代码. 我将探讨两种方式, 一种非常简单的方式是使用Yeoman运行应用程序, 另外一种不是很容易的不用Yeoman的方法, 但是同样很好.

###使用Yeoman

Yeoman让你很简单的使用一个Web服务器服务你所有的静态资源和相关的JavaScript文件. 只需要运行以下命令:
	
	yeoman server

它将启动一个服务器同时在你的浏览器中打开AngularJS应用程序的主页. 每当你改变你的源代码时, 它甚至会刷新(自动刷新)浏览器. 很酷不是吗?

###不使用Yeoman

如果不使用Yeoman, 你可能需要配置一个服务器来服务你所有主目录中的文件. 如果你不知道一个简单的方法做到这一点, 或者不想浪费时间创建你自己的Web服务器, 你可以在Node.js中使用ExpressJS快速的编写一个简单的Web服务器(只要简单的使用`npm install -g express`来获取它). 它可能看起来像下面这样:

	//available at chapter3/sample-app/web-server.js

	var express = require('express'),
	    app = express(),
	    port = parseInt(process.env.PORT, 10) || 8080;
		app.configure(function(){
			app.use(express.methodOverride());
			app.use(express.bodyParser());
			app.use(express.static(__dirname + '/'));
			app.use(app.router);
		});

	app.listen(port);
	console.log("Now serving the app at http://localhost:" + port + "app");

一旦你有了这个文件, 你就可以使用Node运行这个文件, 通过使用下面的命令:

	node web-server.js

同时它将在8080端口启动服务器(或者你自己选择端口).

可选的, 在应用程序文件夹中使用Python你应该运行:

	python -m SimpleHTTPServer

无论你是否决定继续, 一旦你配置好服务器并运行起来, 都将被导航导下面的URL:

	http://localhost:[port-number]/app/index.html

然后你就可以在浏览器中查看你刚刚创建的应用程序. 注意, 你需要手动的刷新浏览器来查看改变, 不同于使用Yeoman.

##测试AngularJS

之前已经说过(甚至在本章的前面), 我们再重新说一次: 测试是必不可少的, AngularJS使编写合理的单元测试和集成测试变得很简单. 虽然AngularJS使用多个测试运行器运行的很好, 但我们坚信[Karma](http://karma-runner.github.io/0.8/index.html)胜过大多数你所需要的提供强大, 坚实和及其快速的运行器.

###Karma

Karma存在的主要的原因是它让你的测试驱动开发(TDD)流程变得简单, 快速和有趣. 它使用NodeJS和SocketIO(你不需要知道它们是什么, 只需要假设它们是很棒很酷的库), 并允许在多个浏览器中及其快速的运行你的代码和测试. 在[https:// github.com/vojtajina/karma/](https:// github.com/vojtajina/karma/)中可以找到更多信息.

> **TDD简介**
>
> 测试驱动开发或者TDD, 是一个通过确保在开发生命周期内首先编写测试的敏捷方法, 这是在代码实现之前进行的, 这就是测试驱动的开发(不只是作为一种验证工具).
>
> TDD的原则很简单:
> 
+ 代码只需要在一个所需要的代码测试失败时编写.
+  编写最少的代码以确保测试通过.
+  在每一步删除重复代码.
+  一旦所有的测试通过, 接下来就是给下一个所需要的功能添加失败测试.
>
> 以下是确保这些原则的简单规则:
>
+ 有组织的开发你的代码, 每一行代码都要有目的的编写.
+ 你的代码仍然是高度模块化, 紧密结合和可复用的(你需要能够测试它)
+ 提供一系列的全面的测试列表, 以防后期的破坏和Bugs.
+ 测试也应该充当规范和文档, 以适应后期需要和变化.
>
> 在AngularJS中我们发现这是真的, 同时在整个AngularJS代码库中我们都是使用TDD来开发. 对于像JavaScript这样的无需编译的动态语言, 我们坚信良好的单元测试可以减轻未来的头痛.

那么, 我们如何获取迷人的Karma呢? 好吧, 首先确保在你的机器上安装了NodeJS. 它自带了NPM(Node包管理器), 这使得它易于管理和安装成千上万的NodeJS可用的库.

一旦你安装了NodeJS和NPM, 安装Karma只需要简单的运行下面的命令:

	sudo npm install -g karma

到这里. 你只要简单的三部来开始使用Karma(我刚才说了, 请不要了解它现实上是怎么使用的).

**获取配置文件**:

如果你是用Yeoman创建应用程序骨架, 那么你就已经有一个现成的Karma配置文件等你来使用. 如果不是, 那么继续, 并且在你的应用程序目录的根文件夹中执行下面的命令:

	karma init

在你的终端控制器中执行(定位到目录文件夹,然后执行命令), 他会生成一个虚拟的配置文件(`karma.conf.js`), 你可以根据你的喜好来编辑它, 它默认带有一些很好的标准. 你可以使用它.

**启动Karma服务器**

只需要运行下面的命令:

	karma start [optionalPathToConfigFile]

这将会在9876端口启动Karma服务器(这是默认情况, 你可以通过编辑在上一步提到的`karma.conf.js`文件来改变). 虽然Karma应该打开一个浏览器并自动捕获它, 它将在控制台中打印所有其他浏览器中捕获所需要的指令. 如果你懒得这样做, 只需要在其他浏览器或者设备中浏览`http://localhost:9876`, 并且你最好在多个浏览器中运行测试.

> 虽然Karma可以自动捕获常用的浏览器, 在启动时.(FireFox, Chrome, IE, Opera, 甚至是PhantomJS), 但它不仅限于只是这些浏览器. 任何可以浏览一个URL的设备都可能可以作为Karma运行器. 因此如果你打开你的iPhone或者Android设备上浏览器并浏览`http://machinename:9876`(只要是可访问的), 你都可能在移动设备上运行同样的测试.

**运行测试**

执行下面的命令:

	karma run

就是这样. 运行这个命令之后, 你应该获得正好打印在控制台中的结果. 很简单, 不是吗?

##单元测试

AngularJS是的编写单元测试变得更简单, 默认情况下支持编写[Jasmine](http://pivotal.github.io/jasmine/)风格的测试(就是Karma). Jasmine就是我们所说的行为驱动开发框架, 它允许你编写规范来说明你的代码行为应该如何表现. 一个Jasmine测试范例看起来可能是这样子的.

	describe("MyController:", function(){
		it("to work correctly", function(){
			var a = 12;
			var b = a;

			expect(a).toBe(b);
			expect(a).not.toBe(null);
		});
	});

正如你可以看到, 它本身就是非常容易阅读的格式, 大部分的代码都可以用简单的英文来阅读理解. 它还提供了一个非常多样化和强大的匹配集合(就像`expect`从句), 当然它还有[xUnit](http://xunit.codeplex.com/)常用的`setUp`和`tearDowns`(函数在每个独立的测试用例之前或者之后执行).

AngularJS提供了一些优雅的原型, 和测试函数一样, 它允许你有在单元测试中创建服务, 控制器和过滤器的权利, 以及模拟`HTTPRequests`输出等等. 我们将在第五章讨论这个.

Karma可以使它很容易的集成到你的开发流程中, 以及在你编写的代码中获取即时的反馈.

**集成到IDEs中**

Karma并没有所有最新版和最好的(greatest)IDEs使用的插件(已经实现的还没有), 但实际上你并不需要. 所有你所需要做的就是在你的IDEs中添加一个执行"karma start"和"karma run"的快捷命令. 这通常可以通过添加一个简单的脚本来执行, 或者实际的shell命令, 依赖于你所选择的编辑器. 当然, 每次完成运行你都应该看到结果.

**在每一个变化上运行测试**

这是许多TDD开发者的理想国: 能够运行在它们所有的测试中, 每次它们按下保存, 在急毫秒之内迅速的得到返回结果. 使用AngularJS和Karma可以很容易做到这一点. 事实证明, Karma配置文件(记住就是前面的`karma.conf.js`)有一个看似无害的名为**`autoWatch`**的标志. 设置它为true来告诉Karma每次运行你的测试文件(这就是你的源代码和测试代码)都监控它的变化. 然后在你的IDE中执行"karma start", 猜猜会怎样? Karma运行结果将可供你的IDE使用. 你甚至不需要切换控制台或者终端来了解发生了什么.