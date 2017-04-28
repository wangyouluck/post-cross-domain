# post-cross-domain
post cross-domain
跨域GET,POST的实现是不一样的。
GET方式，用JSONP的即可解决
POST方式，用iframe,form来解决

下面详细的讲解一下POST方式的iframe，form跨域解决方式与大致流程：

首先将需要请求的页面中添加隐藏的form表单，以及一个空的iframe，表单的target指向iframe的name，即
两者要保持一致性，表示表单提交之后的数据将会填入iframe内

response页：主用于服务器将请求所返回的数据存入到此页，即：所有的参数组合成链接的形式进行iframe src的重定向，重定向到Jump页，其iframe的src是jump页地址+一系列参数
Jump页：就是iframe里的内容，主用于对其src的各种参数的分割与取callback函数名，callback的内容。。
注意：response, jump页请放入主程序html页同一层级

最后将取回的callback函数名与JS内挂在window上的函数名对接，传入参数内容，进行JS处理即可

其中还有用到window.postMessage(message,targetOrigin)  方法
此法是html5新引进的特性，可以使用它来向其它的window对象发送消息，无论这个window对象是属于同源或不同源，目前IE8+、FireFox、Chrome、Opera等浏览器都已经支持window.postMessage方法。
if (window.postMessage) {
    window.parent.postMessage(JSON.stringify(config), '*');
}
调用postMessage方法的window对象是指要接收消息的那一个window对象，该方法的第一个参数message为要发送的消息，类型只能为字符串；第二个参数targetOrigin用来限定接收消息的那个window对象所在的域，如果不想限定域，可以使用通配符 *  。
使用postMessage来跨域传送数据还是比较直观和方便的，但是缺点是IE6、IE7不支持，所以用不用还得根据实际需要来决定。

Jsonp的执行过程如下：

首先在客户端注册一个callback (如:'jsoncallback'), 然后把callback的名字(如:jsonp1236827957501)传给服务器。注意：服务端得到callback的数值后，要用jsonp1236827957501(......)把将要输出的json内容包括起来，此时，服务器生成 json 数据才能被客户端正确接收。

然后以 javascript 语法的方式，生成一个function， function 名字就是传递上来的参数 'jsoncallback'的值 jsonp1236827957501 .

最后将 json 数据直接以入参的方式，放置到 function 中，这样就生成了一段 js 语法的文档，返回给客户端。

客户端浏览器，解析script标签，并执行返回的 javascript 文档，此时javascript文档数据，作为参数， 传入到了客户端预先定义好的 callback 函数(如上例中jquery $.ajax()方法封装的的success: function (json))里。

可以说jsonp的方式原理上和<script src="http://跨域/...xx.js"></script>是一致的(qq空间就是大量采用这种方式来实现跨域数据交换的)。JSONP是一种脚本注入(Script Injection)行为，所以有一定的安全隐患。



