## IndexedDB

## IndexedDB 特点

- 键值对储存
  - IndexedDB 内部采用对象仓库（object store）存放数据。所有类型的数据都可以直接存入，包括 JavaScript 对象。对象仓库中，数据以"键值对"的形式保存，每一个数据记录都有对应的主键，主键是独一无二的，不能有重复，否则会抛出一个错误。

- 异步
  - IndexedDB 操作时不会锁死浏览器，用户依然可以进行其他操作，这与 LocalStorage 形成对比，后者的操作是同步的。异步 设计是为了防止大量数据的读写，拖慢网页的表现。

- 支持事务
  - IndexedDB 支持事务（transaction），这意味着一系列操作步骤之中，只要有一步失败，整个事务就都取消，数据库回滚到事务发生之前的状态，不存在只改写一部分数据的情况。

- 同源限制
  - IndexedDB 受到同源限制，每一个数据库对应创建它的域名。网页只能访问自身域名下的数据库，而不能访问跨域的数据库。

- 储存空间大
  - IndexedDB 的储存空间比 LocalStorage 大得多，一般来说不少于 250MB，甚至没有上限。（6）支持二进制储存。 IndexedDB 不仅可以储存字符串，还可以储存二进制数据（ArrayBuffer 对象和 Blob 对象）。

## 基本概念

IndexedDB 是一个比较复杂的 API，涉及不少概念。它把不同的实体，抽象成一个个对象接口。学习这个 API，就是学习它的各种对象接口。

```
数据库：IDBDatabase 对象
对象仓库：IDBObjectStore 对象
索引： IDBIndex 对象
事务： IDBTransaction 对象
操作请求：IDBRequest 对象
指针： IDBCursor 对象
主键集合：IDBKeyRange 对象
```


下面是一些主要的概念。

### 数据库

数据库是一系列相关数据的容器。每个域名（严格的说，是协议 + 域名 + 端口）都可以新建任意多个数据库。

IndexedDB 数据库有版本的概念。同一个时刻，只能有一个版本的数据库存在。如果要修改数据库结构（新增或删除表、索引或者主键），只能通过升级数据库版本完成。

### 对象仓库

每个数据库包含若干个对象仓库（object store）。它类似于关系型数据库的表格。

### 数据记录

对象仓库保存的是数据记录。每条记录类似于关系型数据库的行，但是只有主键和数据体两部分。主键用来建立默认的索引，必须是不同的，否则会报错。主键可以是数据记录里面的一个属性，也可以指定为一个递增的整数编号。

```js
{ id: 1, text: 'foo' }
```

上面的对象中，id属性可以当作主键。

数据体可以是任意数据类型，不限于对象。

### 索引

为了加速数据的检索，可以在对象仓库里面，为不同的属性建立索引。

### 事务

数据记录的读写和删改，都要通过事务完成。事务对象提供error、abort和complete三个事件，用来监听操作结果。

## 接口

为了获取数据库的访问权限，需要在 window 对象的 indexedDB 属性上调用 open() 方法。该方法返回一个 IDBRequest 对象；异步操作通过在 IDBRequest 对象上触发事件来和调用程序进行通信。

### 连接数据库

#### IDBEnvironment

提供 IndexedDB 功能。它由 window 和 worker 实现，这个接口不再是 2.0 规范的一部分。

#### IDBFactory

提供数据库访问。这是全局对象 indexedDB 实现的接口，因此是 API 的入口。

#### IDBOpenDBRequest

表示一个打开数据库的请求。

#### IDBDatabase

表示一个数据库连接。这是在数据库中获取事务的唯一方式。

### 接收和修改数据

#### IDBTransaction

表示一个事务。在数据库上创建一个事务，指定作用域（例如要访问的存储对象），并确定所需的访问类型（只读或读写）。

#### IDBRequest

处理数据库请求并提供对结果访问的通用接口。

#### IDBObjectStore

表示允许访问通过主键查找的 IndexedDB 数据库中的一组数据的对象存储区。

#### IDBIndex

也是为了允许访问 IndexedDB 数据库中的数据子集，但使用索引来检索记录而不是主键。这有时比使用 IDBObjectStore 更快。

#### IDBCursor

迭代对象存储和索引。

#### IDBCursorWithValue

迭代对象存储和索引并返回游标的当前值。

#### IDBKeyRange

定义可用于从特定范围内的数据库检索数据的键范围。

#### IDBLocaleAwareKeyRange

定义一个键范围，可用于从特定范围内的数据库中检索数据，并根据为特定索引指定的语言环境的规则进行排序（详见 createIndex() 的参数）。这个接口不再是 2.0 规范的一部分。

### 自定义事件接口

此规范使用以下自定义接口触发事件：

#### IDBVersionChangeEvent

作为 IDBOpenDBRequest.onupgradeneeded 事件的处理程序的结果，IDBVersionChangeEvent 接口表示数据库的版本已经发生了改变。

## 安全

IndexedDB 使用同源原则，这意味着它把存储空间绑定到了创建它的站点的源（典型情况下，就是站点的域或是子域），所以它不能被任何其他源访问。

第三方窗口内容（比如 <iframe> 内容）可以访问它所嵌入的源的 IndexedDB 仓库，除非浏览器被设置成从不接受第三方 cookies

## 浏览器关闭警告

当浏览器关闭（由于用户选择关闭或退出选项），包含数据库的磁盘被意外移除，或者数据库存储的权限丢失，将发生以下问题：

受影响的数据库（在浏览器关闭的场景下，所有打开的数据库）的所有事务会以 AbortError 错误中断。该影响和在每个事务中调用 IDBTransaction.abort() 相同。

所有的事务完成后，数据库连接就会关闭。

最终，表示数据库连接的 IDBDatabase 对象收到一个 close 事件。你可以使用 IDBDatabase.onclose 事件句柄来监听这些事件，这样你就可以知道什么时候数据库被意外关闭了。

## 使用 IndexedDB

### 使用 indexedDB 存储客户数据

```js
const customerData = [
  { ssn: "444-44-4444", name: "Bill", age: 35, email: "bill@company.com" },
  { ssn: "555-55-5555", name: "Donna", age: 32, email: "donna@home.org" }
];

window.indexedDB = window.indexedDB || window.mozIndexedDB || window.webkitIndexedDB || window.msIndexedDB;

window.IDBTransaction = window.IDBTransaction || window.webkitIDBTransaction || window.msIDBTransaction;
window.IDBKeyRange = window.IDBKeyRange || window.webkitIDBKeyRange || window.msIDBKeyRange

const dbName = "the_name";

var request = indexedDB.open(dbName, 2);

request.onerror = function(event) {
  // 错误处理
};
request.onupgradeneeded = function(event) {
  var db = event.target.result;

  // 建立一个对象仓库来存储我们客户的相关信息，我们选择 ssn 作为键路径（key path）
  // 因为 ssn 可以保证是不重复的
  var objectStore = db.createObjectStore("customers", { keyPath: "ssn" });

  // 建立一个索引来通过姓名来搜索客户。名字可能会重复，所以我们不能使用 unique 索引
  objectStore.createIndex("name", "name", { unique: false });

  // 使用邮箱建立索引，我们向确保客户的邮箱不会重复，所以我们使用 unique 索引
  objectStore.createIndex("email", "email", { unique: true });

  // 使用事务的 oncomplete 事件确保在插入数据前对象仓库已经创建完毕
  objectStore.transaction.oncomplete = function(event) {
    // 将数据保存到新创建的对象仓库
    var customerObjectStore = db.transaction("customers", "readwrite").objectStore("customers");
    customerData.forEach(function(customer) {
      customerObjectStore.add(customer);
    });
  };
};
```

正如前面提到的，`onupgradeneeded` 是我们唯一可以修改数据库结构的地方。在这里面，我们可以创建和删除对象存储空间以及构建和删除索引。

对象仓库仅调用 `createObjectStore()` 就可以创建。这个方法使用仓库的名称，和一个参数对象。即便这个参数对象是可选的，它还是非常重要的，因为它可以让你定义重要的可选属性，并完善你希望创建的对象存储空间的类型。在我们的示例中，我们创建了一个名为 “customers” 的对象仓库并且定义了一个使得每个仓库中每个对象都独一无二的 `keyPath` 。在这个示例中的属性是 “ssn”，因为社会安全号码被确保是唯一的。被存储在该仓库中的所有对象都必须存在 “ssn”。

我们也请求了一个名为 “name” 的着眼于存储的对象的 name 属性的索引。如同 `createObjectStore()`，`createIndex()` 提供了一个可选地 options 对象，该对象细化了我们希望创建的索引类型。新增一个不带 name 属性的对象也会成功，但是这个对象不会出现在 "name" 索引中。

### 使用键生成器

在创建对象仓库时设置 autoIncrement 标记会为该仓库开启键生成器。默认该设置是不开启的。

使用键生成器，当你向对象仓库新增记录时键会自动生成。对象仓库生成的键往往从 1 开始，然后自动生成的新的键会在之前的键的基础上加 1。生成的键的值从来不会减小，除非数据库操作结果被回滚，比如，数据库事务被中断。因此删除一条记录，甚至清空对象仓库里的所有记录都不会影响对象仓库的键生成器。

我们可以使用键生成器创建一个对象仓库：

```js
// 打开 indexedDB.
var request = indexedDB.open(dbName, 3);

request.onupgradeneeded = function (event) {

    var db = event.target.result;

    // 设置 autoIncrement 标志为 true 来创建一个名为 names 的对象仓库  
    var objStore = db.createObjectStore("names", { autoIncrement : true });

    // 因为 names 对象仓库拥有键生成器，所以它的键会自动生成。
    // 被插入的数据可以表示如下：
    // key : 1 => value : "Bill"
    // key : 2 => value : "Donna"
    customerData.forEach(function(customer) {
        objStore.add(customer.name);
    });
};
```


### 增加、读取和删除数据

你需要开启一个事务才能对你的创建的数据库进行操作。事务来自于数据库对象，而且你必须指定你想让这个事务跨越哪些对象仓库。一旦你处于一个事务中，你就可以目标对象仓库发出请求。你要决定是对数据库进行更改还是只需从中读取数据。事务提供了三种模式：`readonly`、`readwrite` 和 `versionchange`。

想要修改数据库模式或结构——包括新建或删除对象仓库或索引，只能在 versionchange 事务中才能实现。该事务由一个指定了 version 的 `IDBFactory.open` 方法启动。（在仍未实现最新标准的 WebKit 浏览器 ，IDBFactory.open 方法只接受一个参数，即数据库的 name，这样你必须调用 IDBVersionChangeRequest.setVersion 来建立 versionchange 事务）。

使用 `readonly` 或 `readwrite` 模式都可以从已存在的对象仓库里读取记录。但只有在 `readwrite` 事务中才能修改对象仓库。你需要使用 `IDBDatabase.transaction` 启动一个事务。该方法接受两个参数：storeNames (作用域，一个你想访问的对象仓库的数组），事务模式 mode（`readonly` 或 `readwrite`）。该方法返回一个包含 `IDBIndex.objectStore` 方法的事务对象，使用 `IDBIndex.objectStore` 你可以访问你的对象仓库。未指定 `mode` 时，默认为 `readyonly` 模式。

### 向数据库中增加数据

```js
var transaction = db.transaction(["customers"], "readwrite");

// 在所有数据添加完毕后的处理
transaction.oncomplete = function(event) {
  alert("All done!");
};

transaction.onerror = function(event) {
  // 不要忘记错误处理！
};

var objectStore = transaction.objectStore("customers");
customerData.forEach(function(customer) {
  var request = objectStore.add(customer);
  request.onsuccess = function(event) {
    // event.target.result === customer.ssn;
  };
});
```

### 从数据库中删除数据

删除数据是非常类似的：

```js
var request = db.transaction(["customers"], "readwrite")
                .objectStore("customers")
                .delete("444-44-4444");
request.onsuccess = function(event) {
  // 删除成功！
};
```

### 从数据库中获取数据

现在数据库里已经有了一些信息，你可以通过几种方法对它进行提取。首先是简单的 get()。你需要提供键来提取值，像这样：

```js
var transaction = db.transaction(["customers"]);
var objectStore = transaction.objectStore("customers");
var request = objectStore.get("444-44-4444");
request.onerror = function(event) {
  // 错误处理!
};
request.onsuccess = function(event) {
  // 对 request.result 做些操作！
  alert("Name for SSN 444-44-4444 is " + request.result.name);
};
```

对于一个“简单”的提取这里的代码有点多了。下面看我们怎么把它再缩短一点，假设你在数据库的级别上来进行的错误处理：

```js
db.transaction("customers").objectStore("customers").get("444-44-4444").onsuccess = function(event) {
  alert("Name for SSN 444-44-4444 is " + event.target.result.name);
};
```

看看这是怎么回事。因为这里只用到一个对象仓库，你可以只传该对象仓库的名字作为参数，而不必传一个列表。并且，你只需读取数据，所以不需要 readwrite 事务。不指定事务模式来调用 transaction 你会得到一个 readonly 事务。另外一个微妙的地方在于你并没有保存请求对象到变量中。因为 DOM 事件把请求作为他的目标（target），你可以使用该事件来获取 result 属性。

### 更新数据库中的记录

现在我们已经去除了一些数据，修改一下并把它插回数据库的操作时非常简单的。让我们来稍微更新一下上例中的数据。

```js
var objectStore = db.transaction(["customers"], "readwrite").objectStore("customers");
var request = objectStore.get("444-44-4444");
request.onerror = function(event) {
  // 错误处理
};
request.onsuccess = function(event) {
  // 获取我们想要更新的数据
  var data = event.target.result;

  // 更新你想修改的数据
  data.age = 42;

  // 把更新过的对象放回数据库
  var requestUpdate = objectStore.put(data);
   requestUpdate.onerror = function(event) {
     // 错误处理
   };
   requestUpdate.onsuccess = function(event) {
     // 完成，数据已更新！
   };
};
```

所以这里我们创建了一个 objectStore，并通过指定 ssn 值（444-44-4444）从中请求了一条客户记录。然后我们把请求的结果保存在变量 data 中，并更新了该对象的 age 属性，之后创建了第二个请求（requestUpdate）将客户数据放回 objectStore 来覆盖之前的值。

## 使用游标
使用 `get()` 要求你知道你想要检索哪一个键。如果你想要遍历对象存储空间中的所有值，那么你可以使用游标。看起来会像下面这样：

```js
var objectStore = db.transaction("customers").objectStore("customers");

objectStore.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    alert("Name for SSN " + cursor.key + " is " + cursor.value.name);
    cursor.continue();
  }
  else {
    alert("No more entries!");
  }
};
```

`openCursor()` 函数需要几个参数。首先，你可以使用一个 key range 对象来限制被检索的项目的范围。第二，你可以指定你希望进行迭代的方向。在上面的示例中，我们在以升序迭代所有的对象。游标成功的回调有点特别。游标对象本身是请求的 result （上面我们使用的是简写形式，所以是 event.target.result）。然后实际的 key 和 value 可以根据游标对象的 key 和 value 属性被找到。如果你想要保持继续前行，那么你必须调用游标上的 continue() 。当你已经到达数据的末尾时（或者没有匹配 openCursor() 请求的条目）你仍然会得到一个成功回调，但是 result 属性是 undefined。

使用游标的一种常见模式是提取出在一个对象存储空间中的所有对象然后把它们添加到一个数组中，像这样：

```js
var customers = [];

objectStore.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    customers.push(cursor.value);
    cursor.continue();
  }
  else {
    alert("以获取所有客户信息: " + customers);
  }
};
```

### 使用索引

使用 SSN 作为键来存储客户数据是合理的，因为 SSN 唯一地标识了一个个体（对隐私来说这是否是一个好的想法是另外一个话题，不在本文的讨论范围内）。如果你想要通过姓名来查找一个客户，那么，你将需要在数据库中迭代所有的 SSN 直到你找到正确的那个。以这种方式来查找将会非常的慢，相反你可以使用索引。

```js
// 首先，确定你已经在 request.onupgradeneeded 中创建了索引:
// objectStore.createIndex("name", "name");
// 否则你将得到 DOMException。

var index = objectStore.index("name");

index.get("Donna").onsuccess = function(event) {
  alert("Donna's SSN is " + event.target.result.ssn);
};
```

“name” 游标不是唯一的，因此 name 被设成 "Donna" 的记录可能不止一条。在这种情况下，你总是得到键值最小的那个。

如果你需要访问带有给定 name 的所有的记录你可以使用一个游标。你可以在索引上打开两个不同类型的游标。一个常规游标映射索引属性到对象存储空间中的对象。一个键索引映射索引属性到用来存储对象存储空间中的对象的键。不同之处被展示如下：

```js
index.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // cursor.key 是一个 name, 就像 "Bill", 然后 cursor.value 是整个对象。
    alert("Name: " + cursor.key + ", SSN: " + cursor.value.ssn + ", email: " + cursor.value.email);
    cursor.continue();
  }
};

index.openKeyCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // cursor.key 是一个 name, 就像 "Bill", 然后 cursor.value 是那个 SSN。
    // 没有办法可以得到存储对象的其余部分。
    alert("Name: " + cursor.key + ", SSN: " + cursor.value);
    cursor.continue();
  }
};
```

### 指定游标的范围和方向

如果你想要限定你在游标中看到的值的范围，你可以使用一个 key range 对象然后把它作为第一个参数传给 openCursor() 或是 openKeyCursor()。你可以构造一个只允许一个单一 key 的 key range，或者一个具有下限或上限，或者一个既有上限也有下限。边界可以是“闭合的”（也就是说 key range 包含给定的值）或者是“开放的”（也就是说 key range 不包括给定的值）。这里是它如何工作的：

```js
// 仅匹配 "Donna"
var singleKeyRange = IDBKeyRange.only("Donna");

// 匹配所有超过“Bill”的，包括“Bill”
var lowerBoundKeyRange = IDBKeyRange.lowerBound("Bill");

// 匹配所有超过“Bill”的，但不包括“Bill”
var lowerBoundOpenKeyRange = IDBKeyRange.lowerBound("Bill", true);

// 匹配所有不超过“Donna”的，但不包括“Donna”
var upperBoundOpenKeyRange = IDBKeyRange.upperBound("Donna", true);

// 匹配所有在“Bill”和“Donna”之间的，但不包括“Donna”
var boundKeyRange = IDBKeyRange.bound("Bill", "Donna", false, true);

// 使用其中的一个键范围，把它作为 openCursor()/openKeyCursor 的第一个参数
index.openCursor(boundKeyRange).onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // 当匹配时进行一些操作
    cursor.continue();
  }
};
```

有时候你可能想要以倒序而不是正序（所有游标的默认顺序）来遍历。切换方向是通过传递 prev 到 openCursor() 方法来实现的：

```js
objectStore.openCursor(boundKeyRange, "prev").onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // 进行一些操作
    cursor.continue();
  }
};
```

如果你只是想改变遍历的方向，而不想对结果进行筛选，你只需要给第一个参数传入 null。

```js
objectStore.openCursor(null, "prev").onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // Do something with the entries.
    cursor.continue();
  }
};
```

因为 “name” 索引不是唯一的，那就有可能存在具有相同 name 的多条记录。要注意的是这种情况不可能发生在对象存储空间上，因为键必须永远是唯一的。如果你想要在游标在索引迭代过程中过滤出重复的，你可以传递 nextunique （或 prevunique 如果你正在向后寻找）作为方向参数。 当 nextunique 或是 prevunique 被使用时，被返回的那个总是键最小的记录。

```js
index.openKeyCursor(null, IDBCursor.nextunique).onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // Do something with the entries.
    cursor.continue();
  }
};
```

## 参考

[IndexedDB 接口参考](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)

[Indexed Database 接口说明](https://www.w3.org/TR/IndexedDB/)

[Using IndexedDB in chrome](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Using_IndexedDB_in_chrome)
