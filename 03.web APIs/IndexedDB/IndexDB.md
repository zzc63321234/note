## 简介

`IndexDB` 用于在客户端存储大量的结构化数据（包括文件或二进制大型对象，如 `blobs`），它是由浏览器提供的本地数据库。

具有以下特点：

- **键值对存储**：`IndexedDB` 内部使用对象仓库（object store）存储数据。对象仓库中，数据以键值对的形式保存，每一个数据记录都有对应的独一无二的主键。
- **异步**：`IndexedDB` 操作是异步的，防止大量数据读写时，用户的操作被阻塞。这与 `Web Storage` 不同。
- **事务**（`transaction`）：一定要zhu yi
- **同源限制**：`IndexedDB` 中的每一个数据库对应创建它的域名。网页只能访问自身域名下的数据库。
- **存储空间大**：对比 `Web Storage`，`indexedDB` 的存储空间要大的多。
- **支持二进制存储**。

## 基本概念

`IndexedDB` 是一个比较复杂的 `API`，涉及不少概念。它把不同的实体，抽象成一个个对象接口。学习这个 `API`，就是学习它的各种对象接口：

- 数据库：`IDBDatabase` 对象
- 对象仓库：`IDBObjectStore` 对象
- 索引： `IDBIndex` 对象
- 事务： `IDBTransaction` 对象
- 操作请求：`IDBRequest` 对象
- 指针： `IDBCursor` 对象
- 主键集合：`IDBKeyRange` 对象

主要概念：

- 数据库

  数据库是一系列相关数据的容器。每个域（协议 + 域名 + 端口）都可以新建任意多个数据库。

  `IndexedDB` 数据库有版本的概念。同一个时刻，只能有一个版本的数据库存在。如果要修改数据库结构（新增或删除表、索引或者主键），只能通过升级数据库版本完成。

- 对象仓库

  每个数据库包含若干个对象仓库，类似于关系型数据库中的表格。

- 数据记录

  对象仓库中保存的是数据记录。每条记录类似于关系型数据库中的行，但是只有主键和数据体两部分。主键用来建立默认的索引，不能重复，数据体可以是数据记录里的一个属性，也可以指定为递增的整数编号。

- 索引

  为了加速数据的检索，可以在对象仓库里，为不同的属性简历索引。

- 事务

  数据记录的增删改查都要通过事务完成。事务对象提供 `error`、`abort`、`complete` 三个事件，用来监听操作结果。

## 操作流程

https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API/Using_IndexedDB

### 打开数据库

`indexDB.open(name[, version])` 用于打开一个数据库。

- `name` 是一个字符串，表示数据库的名字，如果指定的数据库不存在，则会新建数据库。`version` 是一个整数，表示数据库的版本，则打开已有数据库时省略版本，则默认打开当前版本，新建数据库时默认版本为 `1`。
- 返回一个 `IDBOpenDBRequest` 对象，继承自 `IDBRequest`。会发出的事件有继承自 `IDBRequest` 的 `success` 和`error`，以及特有的 `blocked` 和 `upgradeneeded`。其中可以通过 `success` 和 `upgradeneeded` 的事件对象的 `target.result`属性拿到数据库实例。
  - `success` 事件表示成功打开数据库。
  - `error` 事件表示打开数据库失败。
  - `block` 事件表示当有其他数据库连接阻止当前版本更改事务。
  - `upgradeneeded` 事件表示尝试打开的版本号高于当前版本的数据库，而导致该数据升级。

~~~javascript
const request = window.indexedDB.open(databaseName, version);

request.onerror = function (event) {
  console.log('数据库打开报错');
};

request.onsuccess = function (event) {
  const db = event.target.result;
  console.log('数据库打开成功');
};

request.onupgradeneeded = function (event) {
  const db = event.target.result;
}
~~~

### 新建数据库

当 `indexDB.open(name[, version])` 指定的数据库不存在时，则会触发 `upgradeneeded` 事件并新建数据库。

通常新建数据库之后的第一件事是新建对象仓库：

~~~javascript
request.onupgradeneeded = function(event) {
  const db = event.target.result;
  const objectStore = db.createObjectStore('person', { keyPath: 'id' });
}
~~~

上面代码中，数据库新建成功以后，新增一张叫做 `person` 的表格，主键是 `id`。

最好先判断一下该仓库名是否存在：

~~~javascript
request.onupgradeneeded = function (event) {
  const db = event.target.result;
  if (!db.objectStoreNames.contains('person')) {
    const objectStore = db.createObjectStore('person', { keyPath: 'id' });
  }
}
~~~

主键（key）是默认建立索引的属性。比如，数据记录是 `{ id: 1, name: '张三' }` ，那么 `'id'` 属性可以作为主键。主键也可以指定为下一层对象的属性，比如 `{ foo: { bar: 'baz' } }` 的 `'foo.bar'` 也可以指定为主键。

如果数据记录里面没有合适作为主键的属性，那么可以让 `IndexedDB` 自动生成递增的整数作为主键：

~~~javascript
const objectStore = db.createObjectStore(
  'person',
  { autoIncrement: true }
);
~~~

上面代码中，指定主键为一个递增的整数。

新建对象仓库以后，下一步可以新建索引。

~~~javascript
request.onupgradeneeded = function(event) {
  const db = event.target.result;
  const objectStore = db.createObjectStore('person', { keyPath: 'id' });
  objectStore.createIndex('name', 'name', { unique: false });
  objectStore.createIndex('email', 'email', { unique: true });
}
~~~

上面代码中，`IDBObject.createIndex()`的三个参数分别为索引名称、索引所在的属性、配置对象（说明该属性是否包含重复的值）。

### 新增数据

新增数据指的是向对象仓库写入数据记录。这需要通过事务完成。

~~~javascript
function add() {
  const request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .add({ id: 1, name: '张三', age: 24, email: 'zhangsan@example.com' });

  request.onsuccess = function (event) {
    console.log('数据写入成功');
  };

  request.onerror = function (event) {
    console.log('数据写入失败');
  }
}

add();
~~~

上面代码中，写入数据需要新建一个事务。新建时必须指定表格名称和操作模式（"只读"或"读写"）。新建事务以后，通过 `IDBTransaction.objectStore(name)` 方法，拿到 `IDBObjectStore` 对象，再通过 `IDBObjectStore` 的 `add()` 方法，向表格写入一条记录。写入操作是一个异步操作，通过监听连接对象的 `success` 事件和 `error` 事件，了解是否写入成功。

### 读取数据

`objectStore.get()` 方法用于读取数据，参数是主键的值：

~~~javascript
function read() {
   const transaction = db.transaction(['person']);
   const objectStore = transaction.objectStore('person');
   const request = objectStore.get(1);

   request.onerror = function(event) {
     console.log('事务失败');
   };

   request.onsuccess = function( event) {
      if (request.result) {
        console.log('Name: ' + request.result.name);
        console.log('Age: ' + request.result.age);
        console.log('Email: ' + request.result.email);
      } else {
        console.log('未获得数据记录');
      }
   };
}

read();
~~~

### 遍历数据

遍历数据表格的所有记录，要使用指针对象 `IDBCursor`：

~~~javascript
function readAll() {
  const objectStore = db.transaction('person').objectStore('person');

   objectStore.openCursor().onsuccess = function (event) {
     const cursor = event.target.result;

     if (cursor) {
       console.log('Id: ' + cursor.key);
       console.log('Name: ' + cursor.value.name);
       console.log('Age: ' + cursor.value.age);
       console.log('Email: ' + cursor.value.email);
       cursor.continue();
    } else {
      console.log('没有更多数据了！');
    }
  };
}

readAll();
~~~

### 更新数据

更新数据要使用 `IDBObject.put()` 方法：

~~~javascript
function update() {
  const request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .put({ id: 1, name: '李四', age: 35, email: 'lisi@example.com' });

  request.onsuccess = function (event) {
    console.log('数据更新成功');
  };

  request.onerror = function (event) {
    console.log('数据更新失败');
  }
}

update();
~~~

上面代码中，`put()` 方法自动更新了主键为 `1` 的记录。

### 使用索引

索引的意义在于，可以让你搜索任意字段，也就是说从任意字段拿到数据记录。如果不建立索引，默认只能搜索主键（即从主键取值）。

假定新建表格的时候，对 `name` 字段建立了索引：

~~~javascript
objectStore.createIndex('name', 'name', { unique: false });
~~~

那么之后就可以从 `name` 找到对应的数据记录了：

~~~javascript
const transaction = db.transaction(['person'], 'readonly');
const store = transaction.objectStore('person');
const index = store.index('name');
const request = index.get('李四');

request.onsuccess = function (e) {
  const result = e.target.result;
  if (result) {
    // ...
  } else {
    // ...
  }
}
~~~

如果需要根据索引的值获取到主键，那么需要使用到 `cursor`：

~~~javascript
const transaction = db.transaction(['person'], 'readonly');
const store = transaction.objectStore('person');
const index = store.index('name');

request.onsuccess = function (e) {
  const cursor = e.target.result;
  if (cursor) {
    if (cursor.value === '李四') {
      console.log(cursor.primaryKey)
    };
    cursor.continue();
  } else {
    console.log('没有更多数据了！');
  }
}
~~~

