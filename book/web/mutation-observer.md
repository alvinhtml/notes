# MutationObserver

MutationObserver翻译过来就是变动观察器，字面上就可以理解这是用来观察Node（节点）变化的。MutationObserver是在DOM4规范中定义的，它的前身是MutationEvent事件，该事件最初在DOM2事件规范中介绍，到来了DOM3事件规范中正式定义，但是由于该事件存在兼容性以及性能上的问题被弃用。

## MutationEvent

虽然MutationEvent已经被弃用，但是我们还是需要了解它，可能你会为了浏览器兼容性的问题而遇到它（万恶的浏览器兼容性）。

MutationEvent总共有7种事件：DOMNodeInserted、DOMNodeRemoved、DOMSubtreeModified、DOMAttrModified、
DOMCharacterDataModified、DOMNodeInsertedIntoDocument和DOMNodeRemovedFromDocument。

MutationEvent的兼容性：

- MutationEvent在IE浏览器中最低支持到IE9
- 在webkit内核的浏览器中，不支持DOMAttrModified事件
- IE，Edge以及Firefox浏览器下不支持DOMNodeInsertedIntoDocument和DOMNodeRemovedFromDocument事件

MutationEvent中的所有事件都被设计成无法取消，如果可以取消MutationEvent事件则会导致现有的DOM接口无法对文档进行改变，比如appendChild，remove等添加和删除节点的DOM操作。
MutationEvent中最令人诟病的就是性能以及安全性的问题，比如下面这个例子：

```js
document.addEventListener('DOMNodeInserted', function() {
    const newEl = document.createElement('div');
    document.body.appendChild(newEl);
});
```

document下的所有DOM添加操作都会触发DOMNodeInserted方法，这时就会出现循环调用DOMNodeInserted方法，导致浏览器崩溃。还有就是MutationEvent是事件机制，因此会有一般事件都存在的捕获和冒泡阶段，此时如果在捕获和冒泡阶段又对DOM进行了操作会拖慢浏览器的运行。

另一点就是MutationEvent事件机制是同步的，也就是说每次DOM修改就会触发，修改几次就触发几次,严重降低浏览器的运行，严重时甚至导致线程崩溃。

## MutationObserver

MutationObserver的出现就是为了解决MutationEvent带来的问题。

MutationObserver在IE中最低要就是IE11，如果你的网站不需要支持IE或者只支持到IE11，那么你可以放心的使用MutationObserver，否则你可能需要用到上面提到的MutationEvent事件，当然如果你的网站还要支持IE8及以下版本，那么你只能和Mutation说拜拜了。

MutationObserver是一个构造器，接受一个callback参数，用来处理节点变化的回调函数，返回两个参数，mutations：节点变化记录列表（sequence<MutationRecord>），observer：构造MutationObserver对象。

```js
const observe = new MutationObserver(function(mutations, observer){
})
```

MutationObserver对象有三个方法，分别如下：

1. observe：设置观察目标，接受两个参数，target：观察目标，options：通过对象成员来设置观察选项
1. disconnect：阻止观察者观察任何改变
1. takeRecords：清空记录队列并返回里面的内容

关于observe方法中options参数有已下几个选项：

1. childList：设置true，表示观察目标子节点的变化，比如添加或者删除目标子节点，不包括修改子节点以及子节点后代的变化
1. attributes：设置true，表示观察目标属性的改变
1. characterData：设置true，表示观察目标数据的改变
1. subtree：设置为true，目标以及目标的后代改变都会观察
1. attributeOldValue：如果属性为true或者省略，则相当于设置为true，表示需要记录改变前的目标属性值，设置了attributeOldValue可以省略attributes设置
1. characterDataOldValue：如果characterData为true或省略，则相当于设置为true,表示需要记录改变之前的目标数据，设置了characterDataOldValue可以省略characterData设置
1. attributeFilter：如果不是所有的属性改变都需要被观察，并且attributes设置为true或者被忽略，那么设置一个需要观察的属性本地名称（不需要命名空间）的列表

下表描述了MutationObserver选项与MutationEvent名称之间的对应关系：

MutationEvent|MutationObserver options
--|--
DOMNodeInserted|{ childList: true, subtree: true }
DOMNodeRemoved|{ childList: true, subtree: true }
DOMSubtreeModified|{ childList: true, subtree: true }
DOMAttrModified|{ attributes: true, subtree: true }
DOMCharacterDataModified|{ characterData: true, subtree: true }

从上表我们也可以看出相比与MutationEvent而言MutationObserver极大地增加了灵活性，可以设置各种各样的选项来满足程序员对目标的观察。

我们简单看几个例子：

```html
<div id='target' class='block' name='target'>
    target的第一个子节点
    <p>
       <span>target的后代</span>
    </p>
</div>
```


1.callback的回调次数

```js
var target=document.getElementById('target');
var i=0
var observe=new MutationObserver(function (mutations,observe) {
    i++   
});
observe.observe(target,{ childList: true});
target.appendChild(docuemnt.createTextNode('1'));
target.appendChild(docuemnt.createTextNode('2'));
target.appendChild(docuemnt.createTextNode('3'));
console.log(i) //1
```

MutationObserver的callback回调函数是异步的，只有在全部DOM操作完成之后才会调用callback。

2.当只设置{ childList: true}时,表示观察目标子节点的变化

```js
var observe=new MutationObserver(function (mutations,observe) {
    debugger;
    console.log(mutations);
    //observe.discount();     
});

observe.observe(target,{ childList: true});
target.appendChild(document.createTextNode('新增Text节点'));   //增加节点，观察到变化
target.childNodes[0].remove();                                //删除节点，可以观察到
target.childNodes[0].textContent='改变子节点的后代';             //不会观察到
```

如果想要观察到子节点以及后代的变化需设置{childList: true, subtree: true}


3.我们需要注意的是characterData这个选项，它是用来观察CharacterData类型的节点的，只有在改变节点数据时才会观察到，如果你删除或者增加节点都不会进行观察，还有如果对不是CharacterData类型的节点的改变不会观察到，比如：

```js
observe.observe(target,{ characterData: true, subtree: true});
target.childNodes[0].textContent='改变Text节点';              //观察到
target.childNodes[1].textContent='改变p元素内容';              //不会观察到
target.appendChild(document.createTextNode('新增Text节点'));  //不会观察到
target.childNodes[0].remove();                               //删除TEXT节点也不会观察到
```

我们只需要记住只有对CharacterData类型的节点的数据改变才会被characterData为true的选项所观察到。

4.最后关注一个特别有用的选项attributeFilter，这个选项主要是用来筛选要观察的属性，比如你只想观察目标style属性的变化，这时可以如下设置：

```js
observe.observe(target,{ attributeFilter: ['style'], subtree: true});
target.style='color:red';                      //可以观察到
target.removeAttribute('name');                //删除name属性，无法观察到
```

disconnect方法是用来阻止观察的，当你不再想观察目标节点的变化时可以调用observe.disconnect()方法来取消观察。

takeRecords方法是用来取出记录队列中的记录。它的一个作用是，比如你对一个节点的操作你不想马上就做出反应，过段时间在显示改变了节点的内容。

```js
var observe=new MutationObserver(function(){});
observe.observe(target,{ childList: true});
target.appendChild(document.createTextNode('新增Text节点'));
var record = observe.takeRecords();              //此时record保存了改变记录列表  
//当调用takeRecords方法时，记录队列被清空因此不会触发MutationObserver中的callback回调方法。
target.appendChild(document.createElement('span'));
observe.disconnect();                            //停止对target的观察。
//MutationObserver中的回调函数只有一个记录，只记录了新增span元素

//之后可以对record进行操作
//...
```

MutationRecord 变动记录中的属性如下：

1. type：如果是属性变化，返回"attributes"，如果是一个CharacterData节点（Text节点、Comment节点）变化，返回"characterData"，节点树变化返回"childList"
1. target：返回影响改变的节点
1. addedNodes：返回添加的节点列表
1. removedNodes：返回删除的节点列表
1. previousSibling：返回分别添加或删除的节点的上一个兄弟节点，否则返回null
1. nextSibling：返回分别添加或删除的节点的下一个兄弟节点，否则返回null
1. attributeName：返回已更改属性的本地名称，否则返回null
1. attributeNamespace：返回已更改属性的名称空间，否则返回null
1. oldValue：返回值取决于type。对于"attributes"，它是更改之前的属性的值。对于"characterData"，它是改变之前节点的数据。对于"childList"，它是null

其中 type、target这两个属性不管是哪种观察方式都会有返回值，其他属性返回值与观察方式有关，比如只有当attributeOldValue或者characterDataOldValue为true时oldValue才有返回值，只有改变属性时，attributeName才有返回值等。
