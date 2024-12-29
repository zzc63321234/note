# MongoDB

## 简介

- MongoDB是为快速开发互联网Web应用而设计的数据库系统
- 官网地址https://www.mongodb.com/
- 下载地址 https://www.mongodb.com/download-center/community
- 数据库(DataBase)是按照数据结构来组织、存储和管理数据的仓库，是一个应用程序

## 数据库概念

- 一个mongodb服务可以创建多个数据库

  - 数据库(database)
    - 数据库是一个仓库，在仓库中可以存放集合
  - 集合(collection)
    - 集合类似于JS中的数组，在集合中可以存放文档
    - mongoDB中的集合名称会自动转化为复数形式
  - 文档(document)
    - 文档是数据库中的最小单位，类似于JS中的对象，在MongoDB中每一条数据都是一个JS的对象

  ![img](images/mongoDB_database.jpg)

## MongoDB使用

#### 准备操作

- 将mongod.exe所在文件夹的路径添加到环境变量PATH中
- 创建默认的仓库文件夹
- 打开命令行运行mongod启动数据库服务器
  - 在命令行中使用ctrl+c即为停止服务器
- 另起一个命令行运行mongo，用以输入指令和服务器交互（6.0之后的版本不会自带mongo.exe，需另外下载mongoDB shell，使用命令mongosh打开，下载地址https://www.mongodb.com/try/download/shell）

#### 常用命令

- 数据库集合命令

  - 数据库

    ~~~shell
    show dbs # 显示所有数据库
    show databases # 显示所有数据库
    use 数据库名 # 切换到指定的数据库，如果不存在，则创建
    db # 显示当前所在的数据库
    db.dropDatabase() # 删除当前数据库
    ~~~

  - 集合

    ~~~shell
    # 操作集合时，如果集合不存在，则会自动创建集合
    db.createCollection('user') # 在当前数据库下创建集合
    show collections # 查看当前数据库中的所有集合
    db.user.drop() # 删除集合 db.集合名称.drop()
    db.user.renameCollection('newName') # 重命名集合
    ~~~

- 文档命令

  - 插入文档

    ~~~shell
    db.集合名称.insert(文档对象)
    ~~~

  - 查询文档

    ~~~shell
    db.集合名称.find(查询条件) # 如果不写查询条件，则查询所有文档
    db.集合名称.findOne(查询条件) # 只显示查询到的第一个
    ~~~

  - 更新文档

    ~~~shell
    db.集合名称.update(查询条件, 新的文档对象 [, 配置对象]) # 新的文档会整个替换原来的文档对象
    db.集合名称.update(查询条件, {$set:{属性名:值}}) # 只修改文档中的某个属性
    # 配置对象
    {
        # 可选，upsert表示，如果没有找到符合条件的数据，就会创建这条数据，默认为false
        upsert: <boolean>,
        # 可选，multi表示，false只更新找到的第一条记录，true表示把按条件查询出来的多条记录全部更新，mongodb中默认为false
        multi: <boolean>
    }
    ~~~

  - 删除集合中的文档

    ~~~shell
    db.集合名称.remove(查询条件)
    # 很少直接删除，往往会使用属性表示替换，如：
    is_deleted:1
    delete_at:1
    is_remove:1
    ~~~

- 查询条件

  - 比较查询

    - 在mongodb中有些运算符不能直接使用，需要使用替代符号

    | 运算符 | 替代符号 |
    | :----: | :------: |
    |   >    |   $gt    |
    |   <    |   $lt    |
    |   >=   |   $gte   |
    |   <=   |   $lte   |
    |  !==   |   $ne    |

    - 查询songs集合中，hot的值大于等于2000的文档

      ~~~shell
      db.songs.find({hot: {$gte: 2000}})
      ~~~

  - 逻辑查询

    - $in

      ~~~shell
      db.songs.find({author: {$in: ['周杰伦', '许嵩']}}) # 查询作者为周杰伦或许嵩的歌曲
      ~~~

    - $or

      ~~~shell
      db.songs.find({$or: [{language: '国语'}, {hot: {$gte: 2000}}]}) # 查询国语歌或热度大于等于2000的歌曲
      ~~~

    - $and

      ~~~shell
      db.songs.find({$and: [{language: '国语'}, {hot: {$gte: 2000}}]}) # 查询热度大于等于2000的国语歌
      ~~~

  - 正则匹配

    - 可以直接使用JS的正则语法

      ~~~shell
      db.songs.find({author: /^周/}) # 查询周姓歌手的歌曲

      ~~~

- 查看MongoDB数据库端口号

  ~~~shell
  db.getMongo()
  ~~~

- 更改端口号

  ~~~shell
  mongod --port=27018
  ~~~

  ​

## Mongoose

#### 简介

- Mongoose是一个对象文档模型(ODM)库，它对Node原生的MongoDB模块进行了进一步的优化封装，并提供了更多的功能
- 官网 http://www.mongoosejs.net

#### 使用流程

1. 安装mongoose包

   ~~~shell
   npm i mongoose -s
   ~~~

2. 在运行文件中引入包

   ~~~javascript
   const mongoose = require('mongoose')
   ~~~

3. 连接数据库

   ~~~javascript
   mongoose.connect('mongodb://127.0.0.1:27017/数据库名称')

   //如果启动时遇到警告提醒， 则按照提示增加选项即可
   mongoose.connect('mongodb://127.0.0.1/data', {useNewUrlParser: true, useUnifiedTopology: true});
   ~~~

4. 监听连接事件

   ~~~javascript
   mongoose.connection.on('open', function(){
       // 下面编写数据库操作代码
       
       // 创建文档结构
       const SongSchema = new mongoose.Schema({
           title: String,
           author: String
       })
       
       // 创建文档模型
       const SongModel = mongoose.model('songs', SongSchema) 
       // 第一个参数为集合名称
       // 可设置第三个参数，用以重命名集合名称，防止集合名称自动转换为复数形式
       
       // 使用模型进行文档处理，以添加文档为例
       SongModel.create({
           
       }, (err, data) => {
           if (err) throw err // 判断错误
           
           // 下面写创建成功后的代码
           console.log(data)
           
           // 关闭数据库连接（可选，代码上线后一般不加）
           mongoose.connection.close();
       })
   })
   // 创建的文档中，_id是mongodb自动生成的文档的唯一编号，__v收到mongoose自动省生成的属性
   ~~~

   #### 数据类型

   文档结构可选的字段类型列表

   - ==String==
   - ==Number==
   - Date
   - Buffer
   - Boolean
   - Mixed   任意类型（使用 mongoose.Schema.Types.Mixed 设置）
   - ObjectId
   - ==Array==
   - Decimal128（4.3版本后加入，高精度的数字）

   #### CURD

   数据库的基本操作包括四个，增加（create），删除（delete），修改（update），查（read）

   - 增加

     - 插入一条

       ~~~javascript
       SongModel.create({
           title: '给我一首歌的事件',
           author: '周杰伦'
       }, (err, data) => {
           if (err) throw err
           console.log(data) // data为新插入的对象
       })
       // SongModel.create()的返回值和data相同，其余方法同理，可同于方法的链式调用
       ~~~

     - 批量插入

       ~~~javascript
       SongModel.insertMany([
           {
           title: '给我一首歌的事件',
           author: '周杰伦'
           },
           {
           title: '爱笑的眼睛',
           author: '林俊杰'
           },
           {
           title: '缘分一道桥',
           author: '王力宏'
           }
       ], (err, data) => {
           if (err) throw err
           console.log(data)
       })
       ~~~

   - 删除

     - 删除一条数据

       ~~~javascript
       SongModel.deleteOne({_id:'5dd65f32be6401035cb5b1ed'}, (err, data) => {
           if (err) throw err
           console.log(data) 
           /*
           * data内容为本次删除操作的信息对象，形式如{ok: 1,n: 2, deletedCount: 2}
           * ok表示删除操作是否成功，1: 成功，0: 失败
           * n表示匹配的条数
           * deletedCount代表删除成功的条数
           */
       })
       ~~~

     - 批量删除

       ~~~javascript
       SongModel.deleteMany({author: '周杰伦'}, (err, data) => {
           if (err) throw err
           console.log(data)
       })
       ~~~

   - 更新

     - 更新一条数据

       ~~~javascript
       SongModel.updateOne({author: '周杰伦'}, {author: 'Jay'}, (err, data) => {
           if (err) throw err
           console.log(data)
           /*
           * data内容为本次更新操作的信息对象，形式如{ok: 1,nModified: 2, n: 2}
           * ok表示更新操作是否成功，1: 成功，0: 失败
           * n表示匹配的条数
           * nModified表示已经更新的条数
           */
       })
       ~~~

     - 批量更新数据

       ~~~javascript
       SongModel.updateMany({author: '周杰伦'}, {author: 'Jay'}, (err, data) => {
           if (err) throw err
           console.log(data)
       })
       ~~~

   - 查询

     - 查询一条数据

       ~~~javascript
       // 根据查询条件查询一条数据
       SongModel.findOne({author: '周杰伦'}, (err, data) => {
           if (err) throw err
           console.log(data) // data内容为查询到的数据对象
       })

       // 根据id查询数据
       SongModel.findById('5dd662b5381fc316b44ce167', (err, data) => {
           if (err) throw err
           console.log(data) // data内容为查询到的数据对象
       })
       ~~~

     - 批量查询数据

       ~~~javascript
       // 不加条件查询
       SongModel.find((err, data) => {
           if (err) throw err
           console.log(data) // data内容为一个数组，包含查询到的数据对象
       })

       // 加条件查询
       SongModel.find({author: '周杰伦'}, (err, data) => {
           if (err) throw err
           console.log(data) // data内容为一个数组，包含查询到的数据对象
       })
       ~~~

     - 字段（属性）筛选

       ~~~javascript
       SongModel.find().select({_id: 0, title: 1}).exec((err, data) => {
           if (err) throw err
           console.log(data)
       })
       // 将字段设置为1即被筛选
       // __id如不需被筛选，需要将其单独设置为0
       ~~~

     - 数据排序

       ~~~javascript
       // 1代表升序，-1代表降序
       SongModel.find().sort({hot: 1, title: -1}).exec((err, data) => {
           if (err) throw err
           console.log(data) 
       })
       ~~~

     - 数据截取

       ~~~javascript
       // skip代表跳过的数据条数，limit代表截取的数据条数
       // 如果想从后往前取，可以结合sort()将排序方式倒过来
       SongModel.find()
           .skip(10)
           .limit(10)
           .exec((err, data) => {
               if (err) throw err
               console.log(data) 
       })
       ~~~


