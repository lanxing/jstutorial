---
title: document节点
layout: page
category: dom
date: 2014-05-18
modifiedOn: 2014-05-18
---

## document节点概述

`document`节点是文档的根节点，每张网页都有自己的`document`节点。`window.document`属性就指向这个节点。也就是说，只要浏览器开始载入HTML文档，这个节点对象就存在了，可以直接调用。

document节点有不同的办法可以获取。

- 对于正常的网页，直接使用`document`或`window.document`。
- 对于`iframe`载入的网页，使用`iframe`节点的`contentDocument`属性。
- 对Ajax操作返回的文档，使用XMLHttpRequest对象的`responseXML`属性。
- 对于某个节点包含的文档，使用该节点的`ownerDocument`属性。

上面这四种`document`节点，都部署了[Document接口](http://dom.spec.whatwg.org/#interface-document)，因此有共同的属性和方法。当然，各自也有一些自己独特的属性和方法，比如HTML和XML文档的`document`节点就不一样。

## document节点的属性

document节点有很多属性，用得比较多的是下面这些。

### doctype，documentElement，defaultView，body，head，activeElement

以下属性指向文档内部的某个节点。

**（1）doctype**

对于HTML文档来说，document对象一般有两个子节点。第一个子节点是document.doctype，它是一个对象，包含了当前文档类型（Document Type Declaration，简写DTD）信息。对于HTML5文档，该节点就代表&lt;!DOCTYPE html&gt;。如果网页没有声明DTD，该属性返回null。

```javascript

var doctype = document.doctype;

doctype // "<!DOCTYPE html>"
doctype.name // "html"

```

document.firstChild通常就返回这个节点。

**（2）documentElement**

document.documentElement属性，表示当前文档的根节点（root）。它通常是document节点的第二个子节点，紧跟在`document.doctype`节点后面。

对于HTML网页，该属性返回HTML节点，代表&lt;html lang="en"&gt;。

**（3）defaultView**

defaultView属性，在浏览器中返回document对象所在的window对象，否则返回null。

```javascript

var win = document.defaultView;

```

**（4）body**

body属性返回当前文档的body或frameset节点，如果不存在这样的节点，就返回null。这个属性是可写的，如果对其写入一个新的节点，会导致原有的所有子节点被移除。

**（4）head**

head属性返回当前文档的head节点。如果当前文档有多个head，则返回第一个。

```javascript
document.head === document.querySelector('head')
```

**（5）activeElement**

activeElement属性返回当前文档中获得焦点的那个元素。用户通常可以使用tab键移动焦点，使用空格键激活焦点，比如如果焦点在一个链接上，此时按一下空格键，就会跳转到该链接。

### documentURI，URL，domain，lastModified，location，referrer，title，characterSet

以下属性返回文档信息。

**（1）documentURI，URL**

`documentURI`属性和`URL`属性都返回一个字符串，表示当前文档的网址。不同之处是`documentURI`属性是所有文档都具备的，`URL`属性则是HTML文档独有的。

```javascript
document.documentURI === document.URL
// true
```

另外，如果文档的锚点（`#anchor`）变化，这两个属性都不会跟着变化。但是，`document.location`会跟着变化。

**（2）domain**

`domain`属性返回当前文档的域名。比如，某张网页的网址是 http://www.example.com/hello.html ，`domain`属性就等于`www.example.com`。如果无法获取域名，该属性返回`null`。

```javascript
var badDomain = 'www.example.xxx';
if (document.domain === badDomain)
  window.close();
```

上面代码判断，如果当前域名等于指定域名，则关闭窗口。

二级域名的情况下，domain属性可以设置为对应的一级域名。比如，当前域名是sub.example.com，则domain属性可以设置为example.com。除此之外的写入，都是不可以的。

**（3）lastModified**

lastModified属性返回当前文档最后修改的时间戳，格式为字符串。

```javascript
document.lastModified
// Tuesday, July 10, 2001 10:19:42
```

注意，`lastModified`属性的值是字符串，所以不能用来直接比较，两个文档谁的日期更新，需要用`Date.parse`方法转成时间戳格式，才能进行比较。

```javascript
if (Date.parse(doc1.lastModified) > Date.parse(doc2.lastModified)) {
  // ...
}
```

### document.location

`document.location`属性返回`location`对象，提供了当前文档的URL信息。

```javascript
// 当前网址为 http://user:passwd@www.example.com:4097/path/a.html?x=111#part1
document.location.href // "http://user:passwd@www.example.com:4097/path/a.html?x=111#part1"
document.location.protocol // "http:"
document.location.host // "www.example.com:4097"
document.location.hostname // "www.example.com"
document.location.port // "4097"
document.location.pathname // "/path/a.html"
document.location.search // "?x=111"
document.location.hash // "#part1"
document.location.user // "user"
document.location.password // "passed"
```

`location`对象有以下方法。

- `location.assign()`
- `location.reload()`
- `location.toString()`

```javascript
// 跳转到另一个网址
document.location.assign('http://www.google.com')
// 优先从服务器重新加载
document.location.reload(true)
// 优先从本地缓存重新加载（默认值）
document.location.reload(false)
// 跳转到新网址，并将取代掉history对象中的当前记录
document.location.replace('http://www.google.com');
// 将location对象转为字符串，等价于document.location.href
document.location.toString()
```

如果将新的网址赋值给`location`对象，网页就会自动跳转到新网址。

```javascript
document.location = 'http://www.example.com';
// 等同于
document.location.href = 'http://www.example.com';
```

也可以指定相对URL。

```javascript
document.location = 'page2.html';
```

如果指定的是锚点，浏览器会自动滚动到锚点处。

```javascript
document.location = '#top';
```

注意，采用上面的方法重置URL，跟用户点击链接跳转的效果是一样的。上一个网页依然将保存在浏览器历史之中，点击“后退”按钮就可以回到前一个网页。

如果不希望用户看到前一个网页，可以使用`location.replace`方法，浏览器`history`对象就会用新的网址，取代当前网址，这样的话，“后退”按钮就不会回到当前网页了。它的一个应用就是，当脚本发现当前是移动设备时，就立刻跳转到移动版网页。

`document.location`属性与`window.location`属性等价。

```javascript
document.location === window.location // true
```

历史上，IE曾经不允许对`document.location`赋值，为了保险起见，建议优先使用`window.location`。如果只是单纯地获取当前网址，建议使用`document.URL`，语义性更好。

### document.referrer，document.title，document.characterSet

`document.referrer`属性返回一个字符串，表示当前文档的访问来源，如果是无法获取来源或是用户直接键入网址，而不是从其他网页点击，则返回一个空字符串。

`document.title`属性返回当前文档的标题，该属性是可写的。

```javascript
document.title = '新标题';
```

`document.characterSet`属性返回渲染当前文档的字符集，比如UTF-8、ISO-8859-1。

### readyState，designMode

以下属性与文档行为有关。

**（1）readyState**

`readyState`属性返回当前文档的状态，共有三种可能的值。

- `loading`：加载HTML代码阶段（尚未完成解析）
- `interactive`：加载外部资源阶段时
- `complete`：加载完成时

这个属性变化的过程如下。

1. 浏览器开始解析HTML文档，`document.readyState`属性等于`loading`。
1. 浏览器遇到HTML文档中的`<script>`元素，并且没有`async`或`defer`属性，就暂停解析，开始执行脚本，这时`document.readyState`属性还是等于`loading`。
1. HTML文档解析完成，`document.readyState`属性变成`interactive`。
1. 浏览器等待图片、样式表、字体文件等外部资源加载完成，一旦全部加载完成，`document. readyState`属性变成`complete`。

下面的代码用来检查网页是否加载成功。

```javascript
// 基本检查
if (document.readyState === 'complete') {
  // ...
}

// 轮询检查
var interval = setInterval(function() {
  if (document.readyState === 'complete') {
    clearInterval(interval);
    // ...
  }
}, 100);
```

**（2）designMode**

designMode属性控制当前document是否可编辑。通常会打开iframe的designMode属性，将其变为一个所见即所得的编辑器。

```javascript

iframe_node.contentDocument.designMode = "on";

```

### implementation，compatMode

以下属性返回文档的环境信息。

**（1）implementation**

implementation属性返回一个对象，用来甄别当前环境部署了哪些DOM相关接口。implementation属性的hasFeature方法，可以判断当前环境是否部署了特定版本的特定接口。

```javascript
document.implementation.hasFeature( 'HTML', '2.0')
// true

document.implementation.hasFeature('MutationEvents','2.0')
// true
```

上面代码表示，当前环境部署了DOM HTML 2.0版和MutationEvents的2.0版。

**（2）compatMode**

`compatMode`属性返回浏览器处理文档的模式，可能的值为`BackCompat`（向后兼容模式）和`CSS1Compat`（严格模式）。

一般来说，如果网页代码的第一行设置了明确的`DOCTYPE`（比如`<!doctype html>`），`document.compatMode`的值都为`CSS1Compat`。

### anchors，embeds，forms，images，links，scripts，styleSheets

以下属性返回文档内部特定元素的集合（即HTMLCollection对象，详见下文）。这些集合都是动态的，原节点有任何变化，立刻会反映在集合中。

**（1）anchors**

anchors属性返回网页中所有的a节点元素。注意，只有指定了name属性的a元素，才会包含在anchors属性之中。

**（2）embeds**

embeds属性返回网页中所有嵌入对象，即embed标签，返回的格式为类似数组的对象（nodeList）。

**（3）forms**

forms属性返回页面中所有表单。

```javascript

var selectForm = document.forms[index];
var selectFormElement = document.forms[index].elements[index];

```

上面代码获取指定表单的指定元素。

**（4）images**

images属性返回页面所有图片元素（即img标签）。

```javascript

var ilist = document.images;

for(var i = 0; i < ilist.length; i++) {
  if(ilist[i].src == "banner.gif") {
    // ...
  }
}

```

上面代码在所有img标签中，寻找特定图片。

**（4）links**

links属性返回当前文档所有的链接元素（即a标签，或者说具有href属性的元素）。

**（5）scripts**

scripts属性返回当前文档的所有脚本（即script标签）。

```javascript
var scripts = document.scripts;
if (scripts.length !== 0 ) {
  console.log("当前网页有脚本");
}
```

**（6）styleSheets**

styleSheets属性返回一个类似数组的对象，包含了当前网页的所有样式表。该属性提供了样式表操作的接口。然后，每张样式表对象的cssRules属性，返回该样式表的所有CSS规则。这又方便了操作具体的CSS规则。

```javascript

var allSheets = [].slice.call(document.styleSheets);

```

上面代码中，使用slice方法将document.styleSheets转为数组，以便于进一步处理。

### document.cookie

`document.cookie`属性用来操作浏览器Cookie，详见《浏览器环境》一章的《Cookie》部分。

## document对象的方法

document对象主要有以下一些方法。

### open()，close()，write()，writeln()

document.open方法用于新建一个文档，供write方法写入内容。它实际上等于清除当前文档，重新写入内容。不要将此方法与window.open()混淆，后者用来打开一个新窗口，与当前文档无关。

document.close方法用于关闭open方法所新建的文档。一旦关闭，write方法就无法写入内容了。如果再调用write方法，就等同于又调用open方法，新建一个文档，再写入内容。

`document.write`方法用于向当前文档写入内容。只要当前文档还没有用`close`方法关闭，它所写入的内容就会追加在已有内容的后面。

```javascript
// 页面显示“helloworld”
document.open();
document.write('hello');
document.write('world');
document.close();
```

如果页面已经解析完成（DOMContentLoaded事件发生之后），再调用write方法，它会先调用open方法，擦除当前文档所有内容，然后再写入。

```javascript
document.addEventListener("DOMContentLoaded", function(event) {
  document.write('<p>Hello World!</p>');
});

// 等同于

document.addEventListener("DOMContentLoaded", function(event) {
  document.open();
  document.write('<p>Hello World!</p>');
  document.close();
});
```

如果在页面渲染过程中调用`write`方法，并不会调用`open`方法。（可以理解成，open方法已调用，但close方法还未调用。）

```html
<html>
<body>
hello
<script type="text/javascript">
  document.write("world")
</script>
</body>
</html>
```

在浏览器打开上面网页，将会显示“hello world”。

需要注意的是，虽然调用close方法之后，无法再用write方法写入内容，但这时当前页面的其他DOM节点还是会继续加载。

```html
<html>
<head>
<title>write example</title>
<script type="text/javascript">
  document.open();
  document.write("hello");
  document.close();
</script>
</head>
<body>
world
</body>
</html>
```

在浏览器打开上面网页，将会显示“hello world”。

总之，除了某些特殊情况，应该尽量避免使用`document.write`这个方法。

`document.writeln`方法与`write`方法完全一致，除了会在输出内容的尾部添加换行符。

```js
document.write(1);
document.write(2);
// 12

document.writeln(1);
document.writeln(2);
// 1
// 2
//
```

注意，`writeln`方法添加的是ASCII码的换行符，渲染成HTML网页时不起作用。

### hasFocus()

document.hasFocus方法返回一个布尔值，表示当前文档之中是否有元素被激活或获得焦点。

```javascript

focused = document.hasFocus();

```

注意，有焦点的文档必定被激活（active），反之不成立，激活的文档未必有焦点。比如如果用户点击按钮，从当前窗口跳出一个新窗口，该新窗口就是激活的，但是不拥有焦点。

### querySelector()，getElementById()，querySelectorAll()，getElementsByTagName()，getElementsByClassName()，getElementsByName()，elementFromPoint()

以下方法用来选中当前文档中的元素。

**（1）querySelector()**

`querySelector`方法返回匹配指定的CSS选择器的元素节点。如果有多个节点满足匹配条件，则返回第一个匹配的节点。如果没有发现匹配的节点，则返回`null`。

```javascript
var el1 = document.querySelector('.myclass');
var el2 = document.querySelector('#myParent > [ng-click]');
```

`querySelector`方法无法选中CSS伪元素。

**（2）getElementById()**

`getElementById`方法返回匹配指定ID属性的元素节点。如果没有发现匹配的节点，则返回null。

```javascript
var elem = document.getElementById("para1");
```

注意，在搜索匹配节点时，`id`属性是大小写敏感的。比如，如果某个节点的`id`属性是`main`，那么`document.getElementById("Main")`将返回`null`，而不是指定节点。

`getElementById`方法与`querySelector`方法都能获取元素节点，不同之处是`querySelector`方法的参数使用CSS选择器语法，`getElementById`方法的参数是HTML标签元素的id属性。

```javascript
document.getElementById('myElement')
document.querySelector('#myElement')
```

上面代码中，两个方法都能选中id为myElement的元素，但是getElementById()比querySelector()效率高得多。

**（3）querySelectorAll()**

`querySelectorAll`方法返回匹配指定的CSS选择器的所有节点，返回的是NodeList类型的对象。NodeList对象不是动态集合，所以元素节点的变化无法实时反映在返回结果中。

```javascript
elementList = document.querySelectorAll(selectors);
```

querySelectorAll方法的参数，可以是逗号分隔的多个CSS选择器，返回所有匹配其中一个选择器的元素。

```javascript
var matches = document.querySelectorAll('div.note, div.alert');
```

上面代码返回class属性是note或alert的div元素。

querySelectorAll方法支持复杂的CSS选择器。

```javascript

// 选中data-foo-bar属性等于someval的元素
document.querySelectorAll('[data-foo-bar="someval"]');

// 选中myForm表单中所有不通过验证的元素
document.querySelectorAll('#myForm :invalid');

// 选中div元素，那些class含ignore的除外
document.querySelectorAll('DIV:not(.ignore)');

// 同时选中div，a，script三类元素
document.querySelectorAll('DIV, A, SCRIPT');

```

如果`querySelectorAll`方法和`getElementsByTagName`方法的参数是字符串`*`，则会返回文档中的所有HTML元素节点。

与querySelector方法一样，querySelectorAll方法无法选中CSS伪元素。

**（4）getElementsByClassName()**

getElementsByClassName方法返回一个类似数组的对象（HTMLCollection类型的对象），包括了所有class名字符合指定条件的元素（搜索范围包括本身），元素的变化实时反映在返回结果中。这个方法不仅可以在document对象上调用，也可以在任何元素节点上调用。

```javascript
// document对象上调用
var elements = document.getElementsByClassName(names);
// 非document对象上调用
var elements = rootElement.getElementsByClassName(names);
```

getElementsByClassName方法的参数，可以是多个空格分隔的class名字，返回同时具有这些节点的元素。

```javascript
document.getElementsByClassName('red test');
```

上面代码返回class同时具有red和test的元素。

**（5）getElementsByTagName()**

getElementsByTagName方法返回所有指定标签的元素（搜索范围包括本身）。返回值是一个HTMLCollection对象，也就是说，搜索结果是一个动态集合，任何元素的变化都会实时反映在返回的集合中。这个方法不仅可以在document对象上调用，也可以在任何元素节点上调用。

```javascript
var paras = document.getElementsByTagName("p");
```

上面代码返回当前文档的所有p元素节点。

注意，getElementsByTagName方法会将参数转为小写后，再进行搜索。

**（6）getElementsByName()**

getElementsByName方法用于选择拥有name属性的HTML元素，比如form、img、frame、embed和object，返回一个NodeList格式的对象，不会实时反映元素的变化。

```javascript
// 表单为 <form name="x"></form>
var forms = document.getElementsByName("x");
forms[0].tagName // "FORM"
```

注意，在IE浏览器使用这个方法，会将没有name属性、但有同名id属性的元素也返回，所以name和id属性最好设为不一样的值。

**（7）elementFromPoint()**

elementFromPoint方法返回位于页面指定位置的元素。

```javascript
var element = document.elementFromPoint(x, y);
```

上面代码中，elementFromPoint方法的参数x和y，分别是相对于当前窗口左上角的横坐标和纵坐标，单位是CSS像素。elementFromPoint方法返回位于这个位置的DOM元素，如果该元素不可返回（比如文本框的滚动条），则返回它的父元素（比如文本框）。如果坐标值无意义（比如负值），则返回null。

### createElement()，createTextNode()，createAttribute()，createDocumentFragment()

以下方法用于生成元素节点。

**（1）createElement()**

createElement方法用来生成HTML元素节点。

```javascript
var element = document.createElement(tagName);
// 实例
var newDiv = document.createElement("div");
```

createElement方法的参数为元素的标签名，即元素节点的tagName属性。如果传入大写的标签名，会被转为小写。如果参数带有尖括号（即&lt;和&gt;）或者是null，会报错。

**（2）createTextNode()**

`document.createTextNode`方法用来生成文本节点，参数为所要生成的文本节点的内容。

```javascript
var newDiv = document.createElement('div');
var newContent = document.createTextNode('Hello');
newDiv.appendChild(newContent);
```

上面代码新建一个`div`节点和一个文本节点，然后将文本节点插入`div`节点。

这个方法可以确保返回的节点，被浏览器当作文本渲染，而不是当作HTML代码渲染。因此，可以用来展示用户的输入，避免XSS攻击。

```javascript
var div = document.createElement('div');
div.appendChild(document.createTextNode('<span>Foo & bar</span>'));
console.log(div.innerHTML)
// &lt;span&gt;Foo &amp; bar&lt;/span&gt;
```

上面代码中，`createTextNode`方法对大于号和小于号进行转义，从而保证即使用户输入的内容包含恶意代码，也能正确显示。

需要注意的是，该方法不对单引号和双引号转义，所以不能用来对HTML属性赋值。

```html
function escapeHtml(str) {
  var div = document.createElement('div');
  div.appendChild(document.createTextNode(str));
  return div.innerHTML;
};

var userWebsite = '" onmouseover="alert(\'derp\')" "';
var profileLink = '<a href="' + escapeHtml(userWebsite) + '">Bob</a>';
var div = document.getElemenetById('target');
div.innerHtml = profileLink;
// <a href="" onmouseover="alert('derp')" "">Bob</a>
```

上面代码中，由于`createTextNode`方法不转义双引号，导致`onmouseover`方法被注入了代码。

**（3）createAttribute()**

`document.createAttribute`方法生成一个新的属性对象节点，并返回它。

```javascript
attribute = document.createAttribute(name);
```

createAttribute方法的参数name，是属性的名称。

```javascript

var node = document.getElementById("div1");
var a = document.createAttribute("my_attrib");
a.value = "newVal";
node.setAttributeNode(a);

// 等同于

var node = document.getElementById("div1");
node.setAttribute("my_attrib", "newVal");

```

**（4）createDocumentFragment()**

createDocumentFragment方法生成一个DocumentFragment对象。

```javascript

var docFragment = document.createDocumentFragment();

```

DocumentFragment对象是一个存在于内存的DOM片段，但是不属于当前文档，常常用来生成较复杂的DOM结构，然后插入当前文档。这样做的好处在于，因为DocumentFragment不属于当前文档，对它的任何改动，都不会引发网页的重新渲染，比直接修改当前文档的DOM有更好的性能表现。

```javascript

var docfrag = document.createDocumentFragment();

[1, 2, 3, 4].forEach(function(e) {
  var li = document.createElement("li");
  li.textContent = e;
  docfrag.appendChild(li);
});

document.body.appendChild(docfrag);

```

### createEvent()

createEvent方法生成一个事件对象，该对象可以被element.dispatchEvent方法使用，触发指定事件。

```javascript
var event = document.createEvent(type);
```

createEvent方法的参数是事件类型，比如UIEvents、MouseEvents、MutationEvents、HTMLEvents。

```javascript
var event = document.createEvent('Event');
event.initEvent('build', true, true);
document.addEventListener('build', function (e) {
  // ...
}, false);
document.dispatchEvent(event);
```

### createNodeIterator()，createTreeWalker()

以下方法用于遍历元素节点。

**（1）createNodeIterator()**

createNodeIterator方法返回一个DOM的子节点遍历器。

```javascript
var nodeIterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_ELEMENT
);
```

上面代码返回body元素的遍历器。createNodeIterator方法的第一个参数为遍历器的根节点，第二个参数为所要遍历的节点类型，这里指定为元素节点。其他类型还有所有节点（NodeFilter.SHOW_ALL）、文本节点（NodeFilter.SHOW_TEXT）、评论节点（NodeFilter.SHOW_COMMENT）等。

所谓“遍历器”，在这里指可以用nextNode方法和previousNode方法依次遍历根节点的所有子节点。

```javascript
var nodeIterator = document.createNodeIterator(document.body);
var pars = [];
var currentNode;

while (currentNode = nodeIterator.nextNode()) {
  pars.push(currentNode);
}
```

上面代码使用遍历器的nextNode方法，将根节点的所有子节点，按照从头部到尾部的顺序，读入一个数组。nextNode方法先返回遍历器的内部指针所在的节点，然后会将指针移向下一个节点。所有成员遍历完成后，返回null。previousNode方法则是先将指针移向上一个节点，然后返回该节点。

```javascript
var nodeIterator = document.createNodeIterator(
  document.body,
  NodeFilter.SHOW_ELEMENT
);

var currentNode = nodeIterator.nextNode();
var previousNode = nodeIterator.previousNode();

currentNode === previousNode // true
```

上面代码中，currentNode和previousNode都指向同一个的节点。

有一个需要注意的地方，遍历器返回的第一个节点，总是根节点。

**（2）createTreeWalker()**

createTreeWalker方法返回一个DOM的子树遍历器。它与createNodeIterator方法的区别在于，后者只遍历子节点，而它遍历整个子树。

createTreeWalker方法的第一个参数，是所要遍历的根节点，第二个参数指定所要遍历的节点类型。

```javascript
var treeWalker = document.createTreeWalker(
  document.body,
  NodeFilter.SHOW_ELEMENT
);

var nodeList = [];

while(treeWalker.nextNode()) nodeList.push(treeWalker.currentNode);
```

上面代码遍历body节点下属的所有元素节点，将它们插入nodeList数组。

### adoptNode()，importNode()

以下方法用于获取外部文档的节点。

**（1）adoptNode()**

adoptNode方法将某个节点，从其原来所在的文档移除，插入当前文档，并返回插入后的新节点。

```javascript
node = document.adoptNode(externalNode);
```

importNode方法从外部文档拷贝指定节点，插入当前文档。

```javascript
var node = document.importNode(externalNode, deep);
```

**（2）importNode()**

importNode方法用于创造一个外部节点的拷贝，然后插入当前文档。它的第一个参数是外部节点，第二个参数是一个布尔值，表示对外部节点是深拷贝还是浅拷贝，默认是浅拷贝（false）。虽然第二个参数是可选的，但是建议总是保留这个参数，并设为true。

另外一个需要注意的地方是，importNode方法只是拷贝外部节点，这时该节点的父节点是null。下一步还必须将这个节点插入当前文档的DOM树。

```javascript
var iframe = document.getElementsByTagName("iframe")[0];
var oldNode = iframe.contentWindow.document.getElementById("myNode");
var newNode = document.importNode(oldNode, true);
document.getElementById("container").appendChild(newNode);
```

上面代码从iframe窗口，拷贝一个指定节点myNode，插入当前文档。

### addEventListener()，removeEventListener()，dispatchEvent()

以下三个方法与Document节点的事件相关。这些方法都继承自EventTarget接口，详细介绍参见《Event对象》章节的《EventTarget》部分。

```javascript
// 添加事件监听函数
document.addEventListener('click', listener, false);

// 移除事件监听函数
document.removeEventListener('click', listener, false);

// 触发事件
var event = new Event('click');
document.dispatchEvent(event);
```


