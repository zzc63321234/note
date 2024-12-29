## BDD 风格断言

BDD 风格包括 expect 和 should。两者都使用相同的链式语言来构造断言，但它们在断言的初始构造方式上有所不同。请查看样式指南以进行比较。

### API 参考

#### 语言链

以下内容作为链式获取器提供，以提高断言的可读性。

链

- to
- be
- been
- is
- that
- which
- and
- has
- have
- with
- at
- of
- same
- but
- does
- still
- also

#### .not

否定链中所有后续的断言。

```javascript
expect(function () {}).to.not.throw();
expect({a: 1}).to.not.have.property('b');
expect([1, 2]).to.be.an('array').that.does.not.include(3);
```

尽管可以使用 .not 来否定任何断言，但并不意味着应该这么做。强大的能力带来了巨大的责任。通常最好断言预期的输出，而不是断言某个未预期的输出没有产生。请参阅各个断言的具体指南。

```javascript
expect(2).to.equal(2); // 推荐
expect(2).to.not.equal(1); // 不推荐
```

#### .deep

使链中后续的 .equal, .include, .members, .keys 和 .property 断言使用深度相等而非严格 (===) 相等。有关深度相等算法的信息，请参阅 deep-eql 项目页面： https://github.com/chaijs/deep-eql。

```javascript
// 目标对象深度（但不严格）等于 `{a: 1}`
expect({a: 1}).to.deep.equal({a: 1});
expect({a: 1}).to.not.equal({a: 1});

// 目标数组深度（但不严格）包含 `{a: 1}`
expect([{a: 1}]).to.deep.include({a: 1});
expect([{a: 1}]).to.not.include({a: 1});

// 目标对象深度（但不严格）包含 `x: {a: 1}`
expect({x: {a: 1}}).to.deep.include({x: {a: 1}});
expect({x: {a: 1}}).to.not.include({x: {a: 1}});

// 目标数组深度（但不严格）包含成员 `{a: 1}`
expect([{a: 1}]).to.have.deep.members([{a: 1}]);
expect([{a: 1}]).to.not.have.members([{a: 1}]);

// 目标集合深度（但不严格）具有键 `{a: 1}`
expect(new Set([{a: 1}])).to.have.deep.keys([{a: 1}]);
expect(new Set([{a: 1}])).to.not.have.keys([{a: 1}]);

// 目标对象深度（但不严格）具有属性 `x: {a: 1}`
expect({x: {a: 1}}).to.have.deep.property('x', {a: 1});
expect({x: {a: 1}}).to.not.have.property('x', {a: 1});
```

#### .nested

启用链中所有 .property 和 .include 断言的点表示法和括号表示法。

```javascript
expect({a: {b: ['x', 'y']}}).to.have.nested.property('a.b[1]');
expect({a: {b: ['x', 'y']}}).to.nested.include({'a.b[1]': 'y'});
```

如果 . 或 [] 是实际属性名的一部分，可以通过在其前面加两个反斜杠来进行转义。

```javascript
expect({'.a': {'[b]': 'x'}}).to.have.nested.property('\\.a.\\[b\\]');
expect({'.a': {'[b]': 'x'}}).to.nested.include({'\\.a.\\[b\\]': 'x'});
```

.nested 不能与 .own 组合。

#### .own

使链中后续的 .property 和 .include 断言忽略继承属性。

```javascript
Object.prototype.b = 2;

expect({a: 1}).to.have.own.property('a');
expect({a: 1}).to.have.property('b');
expect({a: 1}).to.not.have.own.property('b');

expect({a: 1}).to.own.include({a: 1});
expect({a: 1}).to.include({b: 2}).but.not.own.include({b: 2});
```

.own 不能与 .nested 组合。

#### .ordered

使链中后续的 .members 断言要求成员的顺序相同。

```javascript
expect([1, 2]).to.have.ordered.members([1, 2])
  .but.not.have.ordered.members([2, 1]);
```

当 .include 和 .ordered 组合时，排序从两个数组的开始部分开始。

```javascript
expect([1, 2, 3]).to.include.ordered.members([1, 2])
  .but.not.include.ordered.members([2, 3]);
```

#### .any

使链中后续的 .keys 断言仅要求目标包含给定键中的至少一个。这与 .all 相反，.all 要求目标包含所有给定键。

```javascript
expect({a: 1, b: 2}).to.not.have.any.keys('c', 'd');
```

有关何时使用 .any 或 .all 的指导，请参阅 .keys 文档。

#### .all

使链中后续的 .keys 断言要求目标包含所有给定键。这与 .any 相反，.any 仅要求目标包含给定键中的至少一个。

```javascript
expect({a: 1, b: 2}).to.have.all.keys('a', 'b');
```

请注意，当链中没有添加 .all 或 .any 时，默认使用 .all。不过，通常最好还是添加 .all，因为这能提高可读性。

有关何时使用 .any 或 .all 的指导，请参阅 .keys 文档。

#### .a(type[, msg])

@param { String } type
@param { String } msg *可选*
断言目标的类型等于给定的字符串类型。类型不区分大小写。有关类型检测算法的信息，请参阅 type-detect 项目页面：https://github.com/chaijs/type-detect。

```javascript
expect('foo').to.be.a('string');
expect({a: 1}).to.be.an('object');
expect(null).to.be.a('null');
expect(undefined).to.be.an('undefined');
expect(new Error).to.be.an('error');
expect(Promise.resolve()).to.be.a('promise');
expect(new Float32Array).to.be.a('float32array');
expect(Symbol()).to.be.a('symbol');
```

.a 支持通过 Symbol.toStringTag 设置自定义类型的对象。

```javascript
var myObj = {
  [Symbol.toStringTag]: 'myCustomType'
};

expect(myObj).to.be.a('myCustomType').but.not.an('object');
```

通常最好在对目标进行更多断言之前，使用 .a 来检查目标的类型。这样可以避免由于目标的类型不同而导致断言出现意外行为。

```javascript
expect([1, 2, 3]).to.be.an('array').that.includes(2);
expect([]).to.be.an('array').that.is.empty;
```

在链中更早添加 .not 来否定 .a。不过，通常最好断言目标是预期的类型，而不是断言它不是许多未预期类型中的一个。

```javascript
expect('foo').to.be.a('string'); // 推荐
expect('foo').to.not.be.an('array'); // 不推荐
```

.a 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(1).to.be.a('string', 'nooo why fail??');
expect(1, 'nooo why fail??').to.be.a('string');
```

.a 也可以用作语言链，以提高断言的可读性。

```javascript
expect({b: 2}).to.have.a.property('b');
```

别名 .an 可以与 .a 互换使用。

#### .include(val[, msg])

@param { Mixed } val
@param { String } msg *可选*
当目标是字符串时，.include 断言给定的字符串 val 是目标的子字符串。

```javascript
expect('foobar').to.include('foo');
```

当目标是数组时，.include 断言给定的 val 是目标的成员。

```javascript
expect([1, 2, 3]).to.include(2);
```

当目标是对象时，.include 断言给定对象 val 的属性是目标的属性的子集。

```javascript
expect({a: 1, b: 2, c: 3}).to.include({a: 1, b: 2});
```

当目标是 Set 或 WeakSet 时，.include 断言给定的 val 是目标的成员。使用 SameValueZero 相等算法。

```javascript
expect(new Set([1, 2])).to.include(2);
```

当目标是 Map 时，.include 断言给定的 val 是目标的值之一。使用 SameValueZero 相等算法。

```javascript
expect(new Map([['a', 1], ['b', 2]])).to.include(2);
```

由于 .include 根据目标的类型做不同的事情，因此在使用 .include 之前检查目标的类型很重要。有关测试目标类型的信息，请参阅 .a 文档。

```javascript
expect([1, 2, 3]).to.be.an('array').that.includes(2);
```

默认情况下，使用严格 (===) 相等性来比较数组成员和对象属性。在链中更早添加 .deep 来使用深度相等而不是严格相等。有关深度相等算法的信息，请参阅 deep-eql 项目页面：https://github.com/chaijs/deep-eql。

```javascript
// 目标数组深度（但不严格）包含 `{a: 1}`
expect([{a: 1}]).to.deep.include({a: 1});
expect([{a: 1}]).to.not.include({a: 1});

// 目标对象深度（但不严格）包含 `x: {a: 1}`
expect({x: {a: 1}}).to.deep.include({x: {a: 1}});
expect({x: {a: 1}}).to.not.include({x: {a: 1}});
```

默认情况下，在处理对象时，搜索目标的所有属性。这包括继承和/或不可枚举的属性。在链中更早添加 .own 来排除目标的继承属性。

```javascript
Object.prototype.b = 2;

expect({a: 1}).to.own.include({a: 1});
expect({a: 1}).to.include({b: 2}).but.not.own.include({b: 2});
```

请注意，目标对象始终仅搜索 val 的自身可枚举属性。

.deep 和 .own 可以组合使用。

```javascript
expect({a: {b: 2}}).to.deep.own.include({a: {b: 2}});
```

在链中更早添加 .nested 来启用点和括号表示法引用嵌套属性。

```javascript
expect({a: {b: ['x', 'y']}}).to.nested.include({'a.b[1]': 'y'});
```

如果 . 或 [] 是实际属性名的一部分，可以通过在其前面加两个反斜杠来进行转义。

```javascript
expect({'.a': {'[b]': 2}}).to.nested.include({'\\.a.\\[b\\]': 2});
```

.deep 和 .nested 可以组合使用。

```javascript
expect({a: {b: [{c: 3}]}}).to.deep.nested.include({'a.b[0]': {c: 3}});
```

.own 和 .nested 不能组合使用。

在链中更早添加 .not 来否定 .include。

```javascript
expect('foobar').to.not.include('taco');
expect([1, 2, 3]).to.not.include(4);
```

但是，当目标是对象时，否定 .include 是危险的。问题在于它通过断言目标对象不具有 val 的所有键/值对但可能具有其中一些键/值对来创建不确定的预期。通常最好确定预期的确切输出，然后编写仅接受该确切输出的断言。

当目标对象不期望具有 val 的键时，通常最好断言确切的内容。

```javascript
expect({c: 3}).to.not.have.any.keys('a', 'b'); // 推荐
expect({c: 3}).to.not.include({a: 1, b: 2}); // 不推荐
```

当目标对象期望具有 val 的键时，通常最好断言每个属性具有其预期值，而不是断言每个属性不具有其中一个意外值。

```javascript
expect({a: 3, b: 4}).to.include({a: 3, b: 4}); // 推荐
expect({a: 3, b: 4}).to.not.include({a: 1, b: 2}); // 不推荐
```

.include 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect([1, 2, 3]).to.include(4, 'nooo why fail??');
expect([1, 2, 3], 'nooo why fail??').to.include(4);
```

.include 也可以用作语言链，使链中所有 .members 和 .keys 断言要求目标是预期集合的超集，而不是相同的集合。请注意，当 .include 添加时，.members 会忽略子集中重复的成员。

```javascript
// 目标对象的键是 ['a', 'b'] 的超集但不相同
expect({a: 1, b: 2, c: 3}).to.include.all.keys('a', 'b');
expect({a: 1, b: 2, c: 3}).to.not.have.all.keys('a', 'b');

// 目标数组是 [1, 2] 的超集但不相同
expect([1, 2, 3]).to.include.members([1, 2]);
expect([1, 2, 3]).to.not.have.members([1, 2]);

// 子集中的重复项被忽略
expect([1, 2, 3]).to.include.members([1, 2, 2, 2]);
```

请注意，在链中更早添加 .any 时，.keys 断言将忽略 .include。

```javascript
// 两个断言是相同的
expect({a: 1}).to.include.any.keys('a', 'b');
expect({a: 1}).to.have.any.keys('a', 'b');
```

别名 .includes, .contain 和 .contains 可以与 .include 互换使用。

#### .ok

断言目标是一个真值（在布尔上下文中被认为是真值）。不过，通常最好断言目标严格（===）或深度等于其预期值。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.be.ok; // 不推荐

expect(true).to.be.true; // 推荐
expect(true).to.be.ok; // 不推荐
```

在链中更早添加 .not 来否定 .ok。

```javascript
expect(0).to.equal(0); // 推荐
expect(0).to.not.be.ok; // 不推荐

expect(false).to.be.false; // 推荐
expect(false).to.not.be.ok; // 不推荐

expect(null).to.be.null; // 推荐
expect(null).to.not.be.ok; // 不推荐

expect(undefined).to.be.undefined; // 推荐
expect(undefined).to.not.be.ok; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect(false, 'nooo why fail??').to.be.ok;
```

#### .true

断言目标严格（===）等于 true。

```javascript
expect(true).to.be.true;
```

在链中更早添加 .not 来否定 .true。不过，通常最好断言目标等于其预期值，而不是不等于 true。

```javascript
expect(false).to.be.false; // 推荐
expect(false).to.not.be.true; // 不推荐

expect(1).to.equal(1); // 推荐
expect(1).to.not.be.true; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect(false, 'nooo why fail??').to.be.true;
```

#### .false

断言目标严格（===）等于 false。

```javascript
expect(false).to.be.false;
```

在链中更早添加 .not 来否定 .false。不过，通常最好断言目标等于其预期值，而不是不等于 false。

```javascript
expect(true).to.be.true; // 推荐
expect(true).to.not.be.false; // 不推荐

expect(1).to.equal(1); // 推荐
expect(1).to.not.be.false; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect(true, 'nooo why fail??').to.be.false;
```

#### .null

断言目标严格（===）等于 null。

```javascript
expect(null).to.be.null;
```

在链中更早添加 .not 来否定 .null。不过，通常最好断言目标等于其预期值，而不是不等于 null。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.not.be.null; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect(42, 'nooo why fail??').to.be.null;
```

#### .undefined

断言目标严格（===）等于 undefined。

```javascript
expect(undefined).to.be.undefined;
```

在链中更早添加 .not 来否定 .undefined。不过，通常最好断言目标等于其预期值，而不是断言它不等于 undefined。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.not.be.undefined; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect(42, 'nooo why fail??').to.be.undefined;
```

#### .NaN

断言目标严格（===）等于 NaN。

```javascript
expect(NaN).to.be.NaN;
```

在链中更早添加 .not 来否定 .NaN。不过，通常最好断言目标等于其预期值，而不是断言它不等于 NaN。

```javascript
expect('foo').to.equal('foo'); // 推荐
expect('foo').to.not.be.NaN; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect(42, 'nooo why fail??').to.be.NaN;
```

#### .exist

断言目标严格（===）不等于 null 或 undefined。不过，通常最好断言目标等于其预期值。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.exist; // 不推荐

expect(0).to.equal(0); // 推荐
expect(0).to.exist; // 不推荐
```

在链中更早添加 .not 来否定 .exist。

```javascript
expect(null).to.be.null; // 推荐
expect(null).to.not.exist; // 不推荐

expect(undefined).to.be.undefined; // 推荐
expect(undefined).to.not.exist; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect(null, 'nooo why fail??').to.exist;
```

别名 .exists 可以与 .exist 互换使用。

#### .empty

当目标是字符串或数组时，.empty 断言目标的 length 属性严格（===）等于 0。

```javascript
expect([]).to.be.empty;
expect('').to.be.empty;
```

当目标是 Map 或 Set 时，.empty 断言目标的 size 属性严格等于 0。

```javascript
expect(new Set()).to.be.empty;
expect(new Map()).to.be.empty;
```

当目标是非函数对象时，.empty 断言目标没有任何自身可枚举属性。具有 Symbol 键的属性不包括在计数中。

```javascript
expect({}).to.be.empty;
```

由于 .empty 根据目标的类型执行不同的操作，因此在使用 .empty 之前检查目标的类型非常重要。有关测试目标类型的信息，请参阅 .a 文档。

```javascript
expect([]).to.be.an('array').that.is.empty;
```

在链中更早添加 .not 来否定 .empty。不过，通常最好断言目标包含其预期数量的值，而不是断言它不为空。

```javascript
expect([1, 2, 3]).to.have.lengthOf(3); // 推荐
expect([1, 2, 3]).to.not.be.empty; // 不推荐

expect(new Set([1, 2, 3])).to.have.property('size', 3); // 推荐
expect(new Set([1, 2, 3])).to.not.be.empty; // 不推荐

expect(Object.keys({a: 1})).to.have.lengthOf(1); // 推荐
expect({a: 1}).to.not.be.empty; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect([1, 2, 3], 'nooo why fail??').to.be.empty;
```

#### .arguments

断言目标是 arguments 对象。

```javascript
function test () {
  expect(arguments).to.be.arguments;
}

test();
```

在链中更早添加 .not 来否定 .arguments。不过，通常最好断言目标是预期的类型，而不是断言它不是 arguments 对象。

```javascript
expect('foo').to.be.a('string'); // 推荐
expect('foo').to.not.be.arguments; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数给出。

```javascript
expect({}, 'nooo why fail??').to.be.arguments;
```

别名 .Arguments 可以与 .arguments 互换使用。

#### .equal(val[, msg])

@param { Mixed } val
@param { String } msg *可选*
断言目标严格（===）等于给定的值 val。

```javascript
expect(1).to.equal(1);
expect('foo').to.equal('foo');
```

在链中更早添加 .deep 来使用深度相等而不是严格相等。有关深度相等算法的信息，请参阅 deep-eql 项目页面：https://github.com/chaijs/deep-eql。

```javascript
// 目标对象深度（但不严格）等于 `{a: 1}`
expect({a: 1}).to.deep.equal({a: 1});
expect({a: 1}).to.not.equal({a: 1});

// 目标数组深度（但不严格）等于 `[1, 2]`
expect([1, 2]).to.deep.equal([1, 2]);
expect([1, 2]).to.not.equal([1, 2]);
```

在链中更早添加 .not 来否定 .equal。不过，通常最好断言目标等于其预期值，而不是不等于其中一个无数个意外值。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.not.equal(2); // 不推荐
```

.equal 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(1).to.equal(2, 'nooo why fail??');
expect(1, 'nooo why fail??').to.equal(2);
```

别名 .equals 和 .eq 可以与 .equal 互换使用。

#### .eql(obj[, msg])

@param { Mixed } obj
@param { String } msg *可选*
断言目标深度等于给定的对象 obj。有关深度相等算法的信息，请参阅 deep-eql 项目页面：https://github.com/chaijs/deep-eql。

```javascript
// 目标对象深度（但不严格）等于 {a: 1}
expect({a: 1}).to.eql({a: 1}).but.not.equal({a: 1});

// 目标数组深度（但不严格）等于 [1, 2]
expect([1, 2]).to.eql([1, 2]).but.not.equal([1, 2]);
```

在链中更早添加 .not 来否定 .eql。不过，通常最好断言目标深度等于其预期值，而不是不深度等于其中一个无数个意外值。

```javascript
expect({a: 1}).to.eql({a: 1}); // 推荐
expect({a: 1}).to.not.eql({b: 2}); // 不推荐
```

.eql 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect({a: 1}).to.eql({b: 2}, 'nooo why fail??');
expect({a: 1}, 'nooo why fail??').to.eql({b: 2});
```

别名 .eqls 可以与 .eql 互换使用。

.deep.equal 断言与 .eql 几乎相同，但有一个区别：.deep.equal 会使后续链中的任何其他断言也使用深度相等性比较。

#### .above(n[, msg])

@param { Number } n
@param { String } msg *可选*
断言目标是一个大于给定数字 n 的数字或日期。不过，通常最好断言目标等于其预期值。

```javascript
expect(2).to.equal(2); // 推荐
expect(2).to.be.above(1); // 不推荐
```

在链中更早添加 .lengthOf 来断言目标的长度或大小大于给定数字 n。

```javascript
expect('foo').to.have.lengthOf(3); // 推荐
expect('foo').to.have.lengthOf.above(2); // 不推荐

expect([1, 2, 3]).to.have.lengthOf(3); // 推荐
expect([1, 2, 3]).to.have.lengthOf.above(2); // 不推荐
```

在链中更早添加 .not 来否定 .above。

```javascript
expect(2).to.equal(2); // 推荐
expect(1).to.not.be.above(2); // 不推荐
```

.above 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(1).to.be.above(2, 'nooo why fail??');
expect(1, 'nooo why fail??').to.be.above(2);
```

别名 .gt 和 .greaterThan 可以与 .above 互换使用。

#### .least(n[, msg])

@param { Number } n
@param { String } msg *可选*
断言目标是一个大于或等于给定数字 n 的数字或日期。不过，通常最好断言目标等于其预期值。

```javascript
expect(2).to.equal(2); // 推荐
expect(2).to.be.at.least(1); // 不推荐
expect(2).to.be.at.least(2); // 不推荐
```

在链中更早添加 .lengthOf 来断言目标的长度或大小大于或等于给定数字 n。

```javascript
expect('foo').to.have.lengthOf(3); // 推荐
expect('foo').to.have.lengthOf.at.least(2); // 不推荐

expect([1, 2, 3]).to.have.lengthOf(3); // 推荐
expect([1, 2, 3]).to.have.lengthOf.at.least(2); // 不推荐
```

在链中更早添加 .not 来否定 .least。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.not.be.at.least(2); // 不推荐
```

.least 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(1).to.be.at.least(2, 'nooo why fail??');
expect(1, 'nooo why fail??').to.be.at.least(2);
```

别名 .gte 和 .greaterThanOrEqual 可以与 .least 互换使用。

#### .below(n[, msg])

@param { Number } n
@param { String } msg *可选*
断言目标是一个小于给定数字 n 的数字或日期。不过，通常最好断言目标等于其预期值。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.be.below(2); // 不推荐
```

在链中更早添加 .lengthOf 来断言目标的长度或大小小于给定数字 n。

```javascript
expect('foo').to.have.lengthOf(3); // 推荐
expect('foo').to.have.lengthOf.below(4); // 不推荐

expect([1, 2, 3]).to.have.length(3); // 推荐
expect([1, 2, 3]).to.have.lengthOf.below(4); // 不推荐
```

在链中更早添加 .not 来否定 .below。

```javascript
expect(2).to.equal(2); // 推荐
expect(2).to.not.be.below(1); // 不推荐
```

.below 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(2).to.be.below(1, 'nooo why fail??');
expect(2, 'nooo why fail??').to.be.below(1);
```

别名 .lt 和 .lessThan 可以与 .below 互换使用。

#### .most(n[, msg])

@param { Number } n
@param { String } msg *可选*
断言目标是一个小于或等于给定数字 n 的数字或日期。不过，通常最好断言目标等于其预期值。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.be.at.most(2); // 不推荐
expect(1).to.be.at.most(1); // 不推荐
```

在链中更早添加 .lengthOf 来断言目标的长度或大小小于或等于给定数字 n。

```javascript
expect('foo').to.have.lengthOf(3); // 推荐
expect('foo').to.have.lengthOf.at.most(4); // 不推荐

expect([1, 2, 3]).to.have.lengthOf(3); // 推荐
expect([1, 2, 3]).to.have.lengthOf.at.most(4); // 不推荐
```

在链中更早添加 .not 来否定 .most。

```javascript
expect(2).to.equal(2); // 推荐
expect(2).to.not.be.at.most(1); // 不推荐
```

.most 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(2).to.be.at.most(1, 'nooo why fail??');
expect(2, 'nooo why fail??').to.be.at.most(1);
```

别名 .lte 和 .lessThanOrEqual 可以与 .most 互换使用。

#### .within(start, finish[, msg])

@param { Number } start 下界（包含）
@param { Number } finish 上界（包含）
@param { String } msg *可选*
断言目标是一个大于或等于给定数字 start 并且小于或等于给定数字 finish 的数字或日期。不过，通常最好断言目标等于其预期值。

```javascript
expect(2).to.equal(2); // 推荐
expect(2).to.be.within(1, 3); // 不推荐
expect(2).to.be.within(2, 3); // 不推荐
expect(2).to.be.within(1, 2); // 不推荐
```

在链中更早添加 .lengthOf 来断言目标的长度或大小大于或等于给定数字 start 并且小于或等于给定数字 finish。

```javascript
expect('foo').to.have.lengthOf(3); // 推荐
expect('foo').to.have.lengthOf.within(2, 4); // 不推荐

expect([1, 2, 3]).to.have.lengthOf(3); // 推荐
expect([1, 2, 3]).to.have.lengthOf.within(2, 4); // 不推荐
```

在链中更早添加 .not 来否定 .within。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.not.be.within(2, 4); // 不推荐
```

.within 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(4).to.be.within(1, 3, 'nooo why fail??');
expect(4, 'nooo why fail??').to.be.within(1, 3);
```

#### .instanceof(constructor[, msg])

@param { Constructor } constructor
@param { String } msg *可选*
断言目标是给定构造函数的实例。

```javascript
function Cat () { }

expect(new Cat()).to.be.an.instanceof(Cat);
expect([1, 2]).to.be.an.instanceof(Array);
```

在链中更早添加 .not 来否定 .instanceof。

```javascript
expect({a: 1}).to.not.be.an.instanceof(Array);
```

.instanceof 接受一个可选的 msg 参数，即在断言失败时显示的自定义错误消息。消息也可以作为 expect 的第二个参数给出。

```javascript
expect(1).to.be.an.instanceof(Array, 'nooo why fail??');
expect(1, 'nooo why fail??').to.be.an.instanceof(Array);
```

由于 ES5 的限制，当使用 Babel 或 TypeScript 等转译器时，.instanceof 可能不会总是按预期工作。特别是在子类化内置对象如 Array、Error 和 Map 时，可能会产生意外结果。有关详细信息，请参阅转译器的文档：

(Babel)
(TypeScript)
别名 .instanceOf 可以与 .instanceof 互换使用。

#### .property(name[, val[, msg]])

@param { String } name
@param { Mixed } val (optional)
@param { String } msg *optional*
断言目标对象具有给定键名的属性。

```javascript
expect({a: 1}).to.have.property('a');
```

当提供 val 时，.property 还断言该属性的值等于给定的 val。

```javascript
expect({a: 1}).to.have.property('a', 1);
```

默认情况下，使用严格（===）相等性。可以在链中提前添加 .deep 以使用深度相等性。有关深度相等算法的信息，请参见 deep-eql 项目页面：https://github.com/chaijs/deep-eql。

```javascript
// 目标对象深度（但不严格）拥有属性 `x: {a: 1}`
expect({x: {a: 1}}).to.have.deep.property('x', {a: 1});
expect({x: {a: 1}}).to.not.have.property('x', {a: 1});
```

目标对象的枚举和不可枚举属性始终包含在搜索中。默认情况下，包含自有和继承的属性。在链中提前添加 .own 以排除继承的属性。

```javascript
Object.prototype.b = 2;

expect({a: 1}).to.have.own.property('a');
expect({a: 1}).to.have.own.property('a', 1);
expect({a: 1}).to.have.property('b');
expect({a: 1}).to.not.have.own.property('b');
```

.deep 和 .own 可以结合使用。

```javascript
expect({x: {a: 1}}).to.have.deep.own.property('x', {a: 1});
```

在链中提前添加 .nested 以启用在引用嵌套属性时使用点和方括号表示法。

```javascript
expect({a: {b: ['x', 'y']}}).to.have.nested.property('a.b[1]');
expect({a: {b: ['x', 'y']}}).to.have.nested.property('a.b[1]', 'y');
```

如果 . 或 [] 是实际属性名的一部分，可以通过在它们前面添加两个反斜杠进行转义。

```javascript
expect({'.a': {'[b]': 'x'}}).to.have.nested.property('\\.a.\\[b\\]');
```

.deep 和 .nested 可以结合使用。

```javascript
expect({a: {b: [{c: 3}]}})
  .to.have.deep.nested.property('a.b[0]', {c: 3});
```

.own 和 .nested 不能结合使用。

在链中提前添加 .not 以否定 .property。

```javascript
expect({a: 1}).to.not.have.property('b');
```

但是，当提供 val 时，否定 .property 是危险的。问题在于它会通过断言目标对象要么没有具有给定键名的属性，要么具有给定键名的属性但其值不等于给定的 val，从而产生不确定的期望。通常最好明确预期的输出，然后编写只接受该确切输出的断言。

当目标对象不期望具有给定键名的属性时，通常最好明确断言这一点。

```javascript
expect({b: 2}).to.not.have.property('a'); // 推荐
expect({b: 2}).to.not.have.property('a', 1); // 不推荐
```

当目标对象期望具有给定键名的属性时，通常最好断言该属性具有预期值，而不是断言它没有多个意外值之一。

```javascript
expect({a: 3}).to.have.property('a', 3); // 推荐
expect({a: 3}).to.not.have.property('a', 1); // 不推荐
```

.property 会将链中后续断言的目标更改为来自原始目标对象的该属性的值。

```javascript
expect({a: 1}).to.have.property('a').that.is.a('number');
```

.property 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。当不提供 val 时，只使用第二种形式。

```javascript
// 推荐
expect({a: 1}).to.have.property('a', 2, 'nooo why fail??');
expect({a: 1}, 'nooo why fail??').to.have.property('a', 2);
expect({a: 1}, 'nooo why fail??').to.have.property('b');

// 不推荐
expect({a: 1}).to.have.property('b', undefined, 'nooo why fail??');
```

上述断言与不提供 val 不同。相反，它断言目标对象具有一个 b 属性，该属性等于 undefined。

断言 .ownProperty 和 .haveOwnProperty 可以与 .own.property 互换使用。

#### .ownPropertyDescriptor(name[, descriptor[, msg]])

@param { String } name
@param { Object } descriptor *optional*
@param { String } msg *optional*
断言目标对象具有给定键名的自有属性描述符。枚举和不可枚举属性都包含在搜索中。

```javascript
expect({a: 1}).to.have.ownPropertyDescriptor('a');
```

当提供 descriptor 时，.ownPropertyDescriptor 还断言该属性的描述符与给定的描述符深度相等。有关深度相等算法的信息，请参见 deep-eql 项目页面：https://github.com/chaijs/deep-eql。

```javascript
expect({a: 1}).to.have.ownPropertyDescriptor('a', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 1,
});
```

在链中提前添加 .not 以否定 .ownPropertyDescriptor。

```javascript
expect({a: 1}).to.not.have.ownPropertyDescriptor('b');
```

但是，当提供 descriptor 时，否定 .ownPropertyDescriptor 是危险的。问题在于它会通过断言目标对象要么没有具有给定键名的属性描述符，要么具有给定键名的属性描述符但与给定描述符不深度相等，从而产生不确定的期望。通常最好明确预期的输出，然后编写只接受该确切输出的断言。

当目标对象不期望具有给定键名的属性描述符时，通常最好明确断言这一点。

```javascript
// 推荐
expect({b: 2}).to.not.have.ownPropertyDescriptor('a');

// 不推荐
expect({b: 2}).to.not.have.ownPropertyDescriptor('a', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 1,
});
```

当目标对象期望具有给定键名的属性描述符时，通常最好断言该属性具有预期的描述符，而不是断言它没有多个意外的描述符之一。

```javascript
// 推荐
expect({a: 3}).to.have.ownPropertyDescriptor('a', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 3,
});

// 不推荐
expect({a: 3}).to.not.have.ownPropertyDescriptor('a', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 1,
});
```

.ownPropertyDescriptor 会将链中后续断言的目标更改为来自原始目标对象的该属性描述符的值。

```javascript
expect({a: 1}).to.have.ownPropertyDescriptor('a')
  .that.has.property('enumerable', true);
```

.ownPropertyDescriptor 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。当不提供 descriptor 时，只使用第二种形式。

```javascript
// 推荐
expect({a: 1}).to.have.ownPropertyDescriptor('a', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 2,
}, 'nooo why fail??');

// 推荐
expect({a: 1}, 'nooo why fail??').to.have.ownPropertyDescriptor('a', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 2,
});

// 推荐
expect({a: 1}, 'nooo why fail??').to.have.ownPropertyDescriptor('b');

// 不推荐
expect({a: 1})
  .to.have.ownPropertyDescriptor('b', undefined, 'nooo why fail??');
```

上述断言与不提供 descriptor 不同。相反，它断言目标对象具有一个 b 属性描述符，该描述符与 undefined 深度相等。

别名 .haveOwnPropertyDescriptor 可以与 .ownPropertyDescriptor 互换使用。

#### .lengthOf(n[, msg])

@param { Number } n
@param { String } msg *optional*
断言目标对象的长度或大小等于给定的数字 n。

```javascript
expect([1, 2, 3]).to.have.lengthOf(3);
expect('foo').to.have.lengthOf(3);
expect(new Set([1, 2, 3])).to.have.lengthOf(3);
expect(new Map([['a', 1], ['b', 2], ['c', 3]])).to.have.lengthOf(3);
```

在链中提前添加 .not 以否定 .lengthOf。然而，通常最好断言目标对象的 length 属性等于其预期值，而不是不等于众多意外值之一。

```javascript
expect('foo').to.have.lengthOf(3); // 推荐
expect('foo').to.not.have.lengthOf(4); // 不推荐
```

.lengthOf 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect([1, 2, 3]).to.have.lengthOf(2, 'nooo why fail??');
expect([1, 2, 3], 'nooo why fail??').to.have.lengthOf(2);
```

.lengthOf 也可以作为一个语言链使用，使链中后续的 .above、.below、.least、.most 和 .within 断言使用目标对象的 length 属性作为目标。然而，通常最好断言目标对象的 length 属性等于其预期长度，而不是断言其 length 属性在某个范围内。

```javascript
// 推荐
expect([1, 2, 3]).to.have.lengthOf(3);

// 不推荐
expect([1, 2, 3]).to.have.lengthOf.above(2);
expect([1, 2, 3]).to.have.lengthOf.below(4);
expect([1, 2, 3]).to.have.lengthOf.at.least(3);
expect([1, 2, 3]).to.have.lengthOf.at.most(3);
expect([1, 2, 3]).to.have.lengthOf.within(2, 4);
```

由于兼容性问题，别名 .length 不能直接在未调用的方法如 .a 后链式调用。因此，.length 不能在所有情况下与 .lengthOf 互换使用。建议始终使用 .lengthOf 而不是 .length。

```javascript
expect([1, 2, 3]).to.have.a.length(3); // 不兼容；抛出错误
expect([1, 2, 3]).to.have.a.lengthOf(3);  // 通过预期
```

#### .match(re[, msg])

@param { RegExp } re
@param { String } msg *optional*
断言目标对象匹配给定的正则表达式 re。

```javascript
expect('foobar').to.match(/^foo/);
```

在链中提前添加 .not 以否定 .match。

```javascript
expect('foobar').to.not.match(/taco/);
```

.match 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect('foobar').to.match(/taco/, 'nooo why fail??');
expect('foobar', 'nooo why fail??').to.match(/taco/);
```

别名 .matches 可以与 .match 互换使用。

#### .string(str[, msg])

@param { String } str
@param { String } msg *optional*
断言目标字符串包含给定的子字符串 str。

```javascript
expect('foobar').to.have.string('bar');
```

在链中提前添加 .not 以否定 .string。

```javascript
expect('foobar').to.not.have.string('taco');
```

.string 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect('foobar').to.have.string('taco', 'nooo why fail??');
expect('foobar', 'nooo why fail??').to.have.string('taco');
```

#### .keys(key1[, key2[, …]])

@param { String | Array | Object } keys
断言目标对象、数组、Map 或 Set 具有给定的键。仅包含目标对象的自有属性。

当目标是对象或数组时，可以将键作为一个或多个字符串参数、单个数组参数或单个对象参数提供。在后一种情况下，只有给定对象的键才重要；值会被忽略。

```javascript
expect({a: 1, b: 2}).to.have.all.keys('a', 'b');
expect(['x', 'y']).to.have.all.keys(0, 1);

expect({a: 1, b: 2}).to.have.all.keys(['a', 'b']);
expect(['x', 'y']).to.have.all.keys([0, 1]);

expect({a: 1, b: 2}).to.have.all.keys({a: 4, b: 5}); // 忽略 4 和 5
expect(['x', 'y']).to.have.all.keys({0: 4, 1: 5}); // 忽略 4 和 5
```

当目标是 Map 或 Set 时，每个键必须作为单独的参数提供。

```javascript
expect(new Map([['a', 1], ['b', 2]])).to.have.all.keys('a', 'b');
expect(new Set(['a', 'b'])).to.have.all.keys('a', 'b');
```

由于 .keys 根据目标对象的类型执行不同的操作，因此在使用 .keys 之前检查目标对象的类型非常重要。有关测试目标对象类型的信息，请参见 .a 文档。

```javascript
expect({a: 1, b: 2}).to.be.an('object').that.has.all.keys('a', 'b');
```

默认情况下，严格（===）相等性用于比较 Map 和 Set 的键。在链中提前添加 .deep 以使用深度相等性。有关深度相等算法的信息，请参见 deep-eql 项目页面：https://github.com/chaijs/deep-eql。

```javascript
// 目标 Set 深度（但不严格）拥有键 `{a: 1}`
expect(new Set([{a: 1}])).to.have.all.deep.keys([{a: 1}]);
expect(new Set([{a: 1}])).to.not.have.all.keys([{a: 1}]);
```

默认情况下，目标必须具有所有给定的键且没有更多。在链中提前添加 .any 仅要求目标具有至少一个给定的键。另外，在链中提前添加 .not 以否定 .keys。否定 .keys 时，通常最好添加 .any，并在断言 .keys 时使用 .all 而不否定。

```javascript
// 推荐；断言目标没有任何给定的键
expect({a: 1, b: 2}).to.not.have.any.keys('c', 'd');

// 不推荐；断言目标没有所有给定的键，但可能有其中一些
expect({a: 1, b: 2}).to.not.have.all.keys('c', 'd');
```

断言 .keys 时不使用否定，通常最好使用 .all，因为 .all.keys 断言输出完全符合预期，而 .any.keys 则会产生不确定的期望。

```javascript
// 推荐；断言目标具有所有给定的键
expect({a: 1, b: 2}).to.have.all.keys('a', 'b');

// 不推荐；断言目标至少具有一个给定的键，但可能有更多
expect({a: 1, b: 2}).to.have.any.keys('a', 'b');
```

请注意，当链中没有出现 .all 或 .any 时，默认使用 .all。然而，通常最好仍然添加 .all 因为它提高了可读性。

```javascript
// 两个断言是相同的
expect({a: 1, b: 2}).to.have.all.keys('a', 'b'); // 推荐
expect({a: 1, b: 2}).to.have.keys('a', 'b'); // 不推荐
```

在链中提前添加 .include 以要求目标对象的键是预期键的超集，而不是相同的集合。

```javascript
// 目标对象的键是 ['a', 'b'] 的超集但不相同
expect({a: 1, b: 2, c: 3}).to.include.all.keys('a', 'b');
expect({a: 1, b: 2, c: 3}).to.not.have.all.keys('a', 'b');
```

但是，如果 .any 和 .include 结合使用，则只有 .any 生效。在这种情况下，.include 被忽略。

```javascript
// 两个断言是相同的
expect({a: 1}).to.have.any.keys('a', 'b');
expect({a: 1}).to.include.any.keys('a', 'b');
```

可以将自定义错误消息作为第二个参数提供给 expect。

```javascript
expect({a: 1}, 'nooo why fail??').to.have.key('b');
```

别名 .key 可以与 .keys 互换使用。

#### .throw([errorLike], [errMsgMatcher], [msg])

@param { Error | ErrorConstructor } errorLike
@param { String | RegExp } errMsgMatcher error message
@param { String } msg *optional*
@see https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Error#Error_types
当没有提供任何参数时，.throw 调用目标函数并断言抛出一个错误。

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw();
```

当提供一个参数且该参数是一个错误构造函数时，.throw 调用目标函数并断言抛出的错误是该错误构造函数的实例。

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw(TypeError);
```

当提供一个参数且该参数是一个错误实例时，.throw 调用目标函数并断言抛出的错误严格（===）等于该错误实例。

```javascript
var err = new TypeError('Illegal salmon!');
var badFn = function () { throw err; };
expect(badFn).to.throw(err);
```

当提供一个参数且该参数是一个字符串时，.throw 调用目标函数并断言抛出的错误信息包含该字符串。

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw('salmon');
```

当提供一个参数且该参数是一个正则表达式时，.throw 调用目标函数并断言抛出的错误信息匹配该正则表达式。

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw(/salmon/);
```

当提供两个参数且第一个参数是错误实例或构造函数，第二个参数是字符串或正则表达式时，.throw 调用目标函数并断言抛出的错误满足上述两个条件。

```javascript
var err = new TypeError('Illegal salmon!');
var badFn = function () { throw err; };
expect(badFn).to.throw(TypeError, 'salmon');
expect(badFn).to.throw(TypeError, /salmon/);
expect(badFn).to.throw(err, 'salmon');
expect(badFn).to.throw(err, /salmon/);
```

在链中提前添加 .not 以否定 .throw。

```javascript
var goodFn = function () {};
expect(goodFn).to.not.throw();
```

但是，当提供任何参数时，否定 .throw 是危险的。问题在于它会通过断言目标要么不抛出错误，要么抛出与给定类型不同的错误，要么抛出给定类型的错误但其信息不包含给定的字符串，从而产生不确定的期望。通常最好明确预期的输出，然后编写只接受该确切输出的断言。

当目标不期望抛出错误时，通常最好明确断言这一点。

```javascript
var goodFn = function () {};
expect(goodFn).to.not.throw(); // 推荐
expect(goodFn).to.not.throw(ReferenceError, 'x'); // 不推荐
```

当目标期望抛出错误时，通常最好断言错误是预期的类型，并且其信息包含预期的字符串，而不是断言它不具有众多意外类型之一，也不包含某些字符串。

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw(TypeError, 'salmon'); // 推荐
expect(badFn).to.not.throw(ReferenceError, 'x'); // 不推荐
```

.throw 会将链中后续断言的目标更改为抛出的错误对象。

```javascript
var err = new TypeError('Illegal salmon!');
err.code = 42;
var badFn = function () { throw err; };
expect(badFn).to.throw(TypeError).with.property('code', 42);
```

.throw 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。当不提供两个参数时，始终使用第二种形式。

```javascript
var goodFn = function () {};
expect(goodFn).to.throw(TypeError, 'x', 'nooo why fail??');
expect(goodFn, 'nooo why fail??').to.throw();
```

由于 ES5 的限制，当使用 Babel 或 TypeScript 等转译器时，.throw 可能并不像预期那样工作。特别是在子类化内置 Error 对象并将子类化的构造函数传递给 .throw 时，可能会产生意外结果。有关详细信息，请参阅转译器的文档：

(Babel)
(TypeScript)

在使用 throw 断言时要注意一些常见的错误。一个常见的错误是意外地自己调用函数，而不是让 throw 断言调用该函数。例如，当测试名为 fn 的函数是否抛出错误时，应提供 fn 而不是 fn() 作为断言的目标。

```javascript
expect(fn).to.throw();     // 好！按预期测试 `fn`
expect(fn()).to.throw();   // 坏！测试 `fn()` 的结果，而不是 `fn`
```

如果需要断言函数 fn 在传递某些参数时抛出错误，则将对 fn 的调用包装在另一个函数中。

```javascript
expect(function () { fn(42); }).to.throw();  // 函数表达式
expect(() => fn(42)).to.throw();             // ES6 箭头函数
```

另一个常见错误是将对象方法（或依赖于 this 的任何独立函数）作为断言的目标。这样做的问题在于，当函数被 .throw 调用时，this 上下文将丢失；它无法知道 this 应该是什么。解决此问题有两种方法。一种解决方案是将方法或函数调用包装在另一个函数中。另一种解决方案是使用 bind。

```javascript
expect(function () { cat.meow(); }).to.throw();  // 函数表达式
expect(() => cat.meow()).to.throw();             // ES6 箭头函数
expect(cat.meow.bind(cat)).to.throw();           // 绑定
```

最后，值得一提的是，在 JavaScript 中的最佳实践是只抛出 Error 及其派生类，例如 ReferenceError、TypeError 以及扩展 Error 的用户定义对象。初始化时没有其他类型的值会生成堆栈跟踪。话虽如此，throw 断言确实技术上支持抛出任何类型的值，而不仅仅是 Error 及其派生类。

别名 .throws 和 .Throw 可以与 .throw 互换使用。

#### .respondTo(method[, msg])

@param { String } method
@param { String } msg _optional_
当目标是非函数对象时，.respondTo 断言目标具有给定名称的方法 method。该方法可以是自身的也可以是继承的，并且可以是可枚举的或不可枚举的。

```javascript
function Cat () {}
Cat.prototype.meow = function () {};

expect(new Cat()).to.respondTo('meow');
```

当目标是函数时，.respondTo 断言目标的 prototype 属性具有给定名称的方法 method。同样，该方法可以是自身的也可以是继承的，并且可以是可枚举的或不可枚举的。

```javascript
function Cat () {}
Cat.prototype.meow = function () {};

expect(Cat).to.respondTo('meow');
```

在链中提前添加 .itself 以强制 .respondTo 将目标视为非函数对象，即使它是一个函数。因此，它断言目标具有给定名称的方法，而不是断言目标的 prototype 属性具有给定名称的方法。

```javascript
function Cat () {}
Cat.prototype.meow = function () {};
Cat.hiss = function () {};

expect(Cat).itself.to.respondTo('hiss').but.not.respondTo('meow');
```

在未添加 .itself 的情况下，在使用 .respondTo 之前检查目标的类型非常重要。有关检查目标对象类型的信息，请参见 .a 文档。

```javascript
function Cat () {}
Cat.prototype.meow = function () {};

expect(new Cat()).to.be.an('object').that.respondsTo('meow');
```

在链中提前添加 .not 以否定 .respondTo。

```javascript
function Dog () {}
Dog.prototype.bark = function () {};

expect(new Dog()).to.not.respondTo('meow');
```

.respondTo 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect({}).to.respondTo('meow', 'nooo why fail??');
expect({}, 'nooo why fail??').to.respondTo('meow');
```

别名 .respondsTo 可以与 .respondTo 互换使用。

#### .itself

强制所有后续的 .respondTo 断言将目标视为非函数对象，即使它是一个函数。因此，它会导致 .respondTo 断言目标具有给定名称的方法，而不是断言目标的 prototype 属性具有给定名称的方法。

```javascript
function Cat () {}
Cat.prototype.meow = function () {};
Cat.hiss = function () {};

expect(Cat).itself.to.respondTo('hiss').but.not.respondTo('meow');
```

#### .satisfy(matcher[, msg])

@param { Function } matcher
@param { String } msg *optional*
调用给定的匹配器函数，传递目标作为第一个参数，并断言返回值为真值。

```javascript
expect(1).to.satisfy(function(num) {
  return num > 0;
});
```

在链中提前添加 .not 以否定 .satisfy。

```javascript
expect(1).to.not.satisfy(function(num) {
  return num > 2;
});
```

.satisfy 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect(1).to.satisfy(function(num) {
  return num > 2;
}, 'nooo why fail??');

expect(1, 'nooo why fail??').to.satisfy(function(num) {
  return num > 2;
});
```

别名 .satisfies 可以与 .satisfy 互换使用。

#### .closeTo(expected, delta[, msg])

@param { Number } expected
@param { Number } delta
@param { String } msg *optional*
断言目标是一个数值，并且在给定数值 expected 的 +/- delta 范围内。然而，通常最好断言目标等于其预期值。

```javascript
// 推荐
expect(1.5).to.equal(1.5);

// 不推荐
expect(1.5).to.be.closeTo(1, 0.5);
expect(1.5).to.be.closeTo(2, 0.5);
expect(1.5).to.be.closeTo(1, 1);
```

在链中提前添加 .not 以否定 .closeTo。

```javascript
expect(1.5).to.equal(1.5); // 推荐
expect(1.5).to.not.be.closeTo(3, 1); // 不推荐
```

.closeTo 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect(1.5).to.be.closeTo(3, 1, 'nooo why fail??');
expect(1.5, 'nooo why fail??').to.be.closeTo(3, 1);
```

别名 .approximately 可以与 .closeTo 互换使用。

#### .members(set[, msg])

@param { Array } set
@param { String } msg *optional*
断言目标数组具有与给定数组 set 相同的成员。

```javascript
expect([1, 2, 3]).to.have.members([2, 1, 3]);
expect([1, 2, 2]).to.have.members([2, 1, 2]);
```

默认情况下，成员使用严格（===）相等性进行比较。提前在链中添加 .deep 以使用深度相等性。有关深度相等算法的信息，请参见 deep-eql 项目页面：https://github.com/chaijs/deep-eql。

```javascript
// 目标数组深度（但不严格）具有成员 `{a: 1}`
expect([{a: 1}]).to.have.deep.members([{a: 1}]);
expect([{a: 1}]).to.not.have.members([{a: 1}]);
```

默认情况下，顺序不重要。提前在链中添加 .ordered 以要求成员按相同顺序出现。

```javascript
expect([1, 2, 3]).to.have.ordered.members([1, 2, 3]);
expect([1, 2, 3]).to.have.members([2, 1, 3])
  .but.not.ordered.members([2, 1, 3]);
```

默认情况下，两个数组必须具有相同的大小。提前在链中添加 .include 以要求目标的成员是预期成员的超集。请注意，添加 .include 时子集中的重复项会被忽略。

```javascript
// 目标数组是 [1, 2] 的超集但不相同
expect([1, 2, 3]).to.include.members([1, 2]);
expect([1, 2, 3]).to.not.have.members([1, 2]);

// 子集中的重复项会被忽略
expect([1, 2, 3]).to.include.members([1, 2, 2, 2]);
```

.deep、.ordered 和 .include 可以结合使用。然而，如果 .include 和 .ordered 结合使用，则排序从两个数组的开头开始。

```javascript
expect([{a: 1}, {b: 2}, {c: 3}])
  .to.include.deep.ordered.members([{a: 1}, {b: 2}])
  .but.not.include.deep.ordered.members([{b: 2}, {c: 3}]);
```

提前在链中添加 .not 以否定 .members。然而，这样做是危险的。问题在于它通过断言目标数组没有与给定数组 set 相同的所有成员，但可能有其中一些，从而产生不确定的期望。通常最好明确预期的输出，然后编写只接受该确切输出的断言。

```javascript
expect([1, 2]).to.not.include(3).and.not.include(4); // 推荐
expect([1, 2]).to.not.have.members([3, 4]); // 不推荐
```

.members 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect([1, 2]).to.have.members([1, 2, 3], 'nooo why fail??');
expect([1, 2], 'nooo why fail??').to.have.members([1, 2, 3]);
```

#### .oneOf(list[, msg])

@param { Array.<*> } list
@param { String } msg *optional*
断言目标是给定数组 list 的成员。然而，通常最好断言目标等于其预期值。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.be.oneOf([1, 2, 3]); // 不推荐
```

比较使用严格（===）相等性进行。

提前在链中添加 .not 以否定 .oneOf。

```javascript
expect(1).to.equal(1); // 推荐
expect(1).to.not.be.oneOf([2, 3, 4]); // 不推荐
```

它还可以与 .contain 或 .include 链接，这将适用于数组和字符串：

```javascript
expect('Today is sunny').to.contain.oneOf(['sunny', 'cloudy']);
expect('Today is rainy').to.not.contain.oneOf(['sunny', 'cloudy']);
expect([1,2,3]).to.contain.oneOf([3,4,5]);
expect([1,2,3]).to.not.contain.oneOf([4,5,6]);
```

.oneOf 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。

```javascript
expect(1).to.be.oneOf([2, 3, 4], 'nooo why fail??');
expect(1, 'nooo why fail??').to.be.oneOf([2, 3, 4]);
```

#### .change(subject[, prop[, msg]])

@param { String } subject
@param { String } prop name *optional*
@param { String } msg *optional*
当提供一个参数时，.change 断言给定函数 subject 在调用目标函数之前与之后返回的值不同。然而，通常最好断言 subject 等于其预期值。

```javascript
var dots = ''
  , addDot = function () { dots += '.'; }
  , getDots = function () { return dots; };

// 推荐
expect(getDots()).to.equal('');
addDot();
expect(getDots()).to.equal('.');

// 不推荐
expect(addDot).to.change(getDots);
```

当提供两个参数时，.change 断言给定对象 subject 的 prop 属性在调用目标函数之前与之后的值不同。

```javascript
var myObj = {dots: ''}
  , addDot = function () { myObj.dots += '.'; };

// 推荐
expect(myObj).to.have.property('dots', '');
addDot();
expect(myObj).to.have.property('dots', '.');

// 不推荐
expect(addDot).to.change(myObj, 'dots');
```

严格（===）相等性用于比较前后的值。

提前在链中添加 .not 以否定 .change。

```javascript
var dots = ''
  , noop = function () {}
  , getDots = function () { return dots; };

expect(noop).to.not.change(getDots);

var myObj = {dots: ''}
  , noop = function () {};

expect(noop).to.not.change(myObj, 'dots');
```

.change 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。当不提供两个参数时，始终使用第二种形式。

```javascript
var myObj = {dots: ''}
  , addDot = function () { myObj.dots += '.'; };

expect(addDot).to.not.change(myObj, 'dots', 'nooo why fail??');

var dots = ''
  , addDot = function () { dots += '.'; }
  , getDots = function () { return dots; };

expect(addDot, 'nooo why fail??').to.not.change(getDots);
```

.change 还会导致所有后续链中的 .by 断言断言数值 subject 增加或减少了多少。然而，使用 .change.by 是危险的。问题在于它产生不确定的期望，断言 subject 增加了给定的 delta，或减少了给定的 delta。通常最好明确预期的输出，然后编写只接受该确切输出的断言。

```javascript
var myObj = {val: 1}
  , addTwo = function () { myObj.val += 2; }
  , subtractTwo = function () { myObj.val -= 2; };

expect(addTwo).to.increase(myObj, 'val').by(2); // 推荐
expect(addTwo).to.change(myObj, 'val').by(2); // 不推荐

expect(subtractTwo).to.decrease(myObj, 'val').by(2); // 推荐
expect(subtractTwo).to.change(myObj, 'val').by(2); // 不推荐
```

别名 .changes 可以与 .change 互换使用。

#### .increase(subject[, prop[, msg]])

@param { String | Function } subject
@param { String } prop name *optional*
@param { String } msg *optional*
当提供一个参数时，.increase 断言给定函数 subject 在调用目标函数之后返回的值大于调用之前返回的值。.increase 还会导致所有后续链中的 .by 断言断言返回的数值增加了多少。通常最好断言返回值增加了预期的量，而不是断言它增加了任何量。

```javascript
var val = 1
  , addTwo = function () { val += 2; }
  , getVal = function () { return val; };

expect(addTwo).to.increase(getVal).by(2); // 推荐
expect(addTwo).to.increase(getVal); // 不推荐
```

当提供两个参数时，.increase 断言给定对象 subject 的 prop 属性在调用目标函数之后的值大于调用之前的值。

```javascript
var myObj = {val: 1}
  , addTwo = function () { myObj.val += 2; };

expect(addTwo).to.increase(myObj, 'val').by(2); // 推荐
expect(addTwo).to.increase(myObj, 'val'); // 不推荐
```

提前在链中添加 .not 以否定 .increase。然而，这样做是危险的。问题在于它通过断言 subject 要么减少，要么保持不变，从而产生不确定的期望。通常最好明确预期的输出，然后编写只接受该确切输出的断言。

当期望 subject 减少时，通常最好断言它减少了预期的量。

```javascript
var myObj = {val: 1}
  , subtractTwo = function () { myObj.val -= 2; };

expect(subtractTwo).to.decrease(myObj, 'val').by(2); // 推荐
expect(subtractTwo).to.not.increase(myObj, 'val'); // 不推荐
```

当期望 subject 保持不变时，通常最好明确断言这一点。

```javascript
var myObj = {val: 1}
  , noop = function () {};

expect(noop).to.not.change(myObj, 'val'); // 推荐
expect(noop).to.not.increase(myObj, 'val'); // 不推荐
```

.increase 接受一个可选的 msg 参数，该参数是在断言失败时显示的自定义错误消息。该消息也可以作为第二个参数提供给 expect。当不提供两个参数时，始终使用第二种形式。

```javascript
var myObj = {val: 1}
  , noop = function () {};

expect(noop).to.increase(myObj, 'val', 'nooo why fail??');

var val = 1
  , noop = function () {}
  , getVal = function () { return val; };

expect(noop, 'nooo why fail??').to.increase(getVal);
```

别名 .increases 可以与 .increase 互换使用。

#### .decrease(subject[, prop[, msg]])

@param { String | Function } subject
@param { String } prop name *optional*
@param { String } msg *optional*

当提供一个参数时，.decrease 断言在调用目标函数后，给定函数 subject 返回的数值比调用之前返回的数值更小。 .decrease 还会导致链中后续的所有 .by 断言，断言返回的数值减少了多少。最好断言返回值减少了预期的数量，而不是断言减少了任何数量。

```javascript
var val = 1,
  subtractTwo = function () { val -= 2; },
  getVal = function () { return val; };

expect(subtractTwo).to.decrease(getVal).by(2); // 推荐
expect(subtractTwo).to.decrease(getVal); // 不推荐
```

当提供两个参数时，.decrease 断言在调用目标函数后，给定对象 subject 的 prop 属性的值比调用之前的值更小。

```javascript
var myObj = { val: 1 },
  subtractTwo = function () { myObj.val -= 2; };

expect(subtractTwo).to.decrease(myObj, 'val').by(2); // 推荐
expect(subtractTwo).to.decrease(myObj, 'val'); // 不推荐
```

在链中较早处添加 .not 以否定 .decrease。然而，这样做是危险的。问题在于它通过断言 subject 增加或保持不变来创建不确定的预期。最好是确定预期的确切输出，然后编写只接受该确切输出的断言。

当预期 subject 增加时，最好断言它增加了预期的数量。

```javascript
var myObj = { val: 1 },
  addTwo = function () { myObj.val += 2; };

expect(addTwo).to.increase(myObj, 'val').by(2); // 推荐
expect(addTwo).to.not.decrease(myObj, 'val'); // 不推荐
```

当预期 subject 保持不变时，最好明确断言这一点。

```javascript
var myObj = { val: 1 },
  noop = function () {};

expect(noop).to.not.change(myObj, 'val'); // 推荐
expect(noop).to.not.decrease(myObj, 'val'); // 不推荐
```

.decrease 接受一个可选的 msg 参数，这是在断言失败时显示的自定义错误消息。该消息也可以作为 expect 的第二个参数提供。当不提供两个参数时，请始终使用第二种形式。

```javascript
var myObj = { val: 1 },
  noop = function () {};

expect(noop).to.decrease(myObj, 'val', 'nooo why fail??');

var val = 1,
  noop = function () {},
  getVal = function () { return val; };

expect(noop, 'nooo why fail??').to.decrease(getVal);
```

可以互换使用别名 .decreases 和 .decrease。

#### .by(delta[, msg])

@param { Number } delta
@param { String } msg *optional*

在链中紧跟 .increase 断言时，.by 断言 .increase 断言的 subject 增加了给定的 delta。

```javascript
var myObj = { val: 1 },
  addTwo = function () { myObj.val += 2; };

expect(addTwo).to.increase(myObj, 'val').by(2);
```

在链中紧跟 .decrease 断言时，.by 断言 .decrease 断言的 subject 减少了给定的 delta。

```javascript
var myObj = { val: 1 },
  subtractTwo = function () { myObj.val -= 2; };

expect(subtractTwo).to.decrease(myObj, 'val').by(2);
```

在链中紧跟 .change 断言时，.by 断言 .change 断言的 subject 增加或减少了给定的 delta。然而，使用 .change.by 是危险的。问题在于它创建了不确定的预期。最好是确定预期的确切输出，然后编写只接受该确切输出的断言。

```javascript
var myObj = { val: 1 },
  addTwo = function () { myObj.val += 2; },
  subtractTwo = function () { myObj.val -= 2; };

expect(addTwo).to.increase(myObj, 'val').by(2); // 推荐
expect(addTwo).to.change(myObj, 'val').by(2); // 不推荐

expect(subtractTwo).to.decrease(myObj, 'val').by(2); // 推荐
expect(subtractTwo).to.change(myObj, 'val').by(2); // 不推荐
```

在链中较早处添加 .not 以否定 .by。然而，最好断言 subject 按预期的 delta 发生了变化，而不是断言它没有按无数意外的 delta 发生变化。

```javascript
var myObj = { val: 1 },
  addTwo = function () { myObj.val += 2; };

// 推荐
expect(addTwo).to.increase(myObj, 'val').by(2);

// 不推荐
expect(addTwo).to.increase(myObj, 'val').but.not.by(3);
```

.by 接受一个可选的 msg 参数，这是在断言失败时显示的自定义错误消息。该消息也可以作为 expect 的第二个参数提供。

```javascript
var myObj = { val: 1 },
  addTwo = function () { myObj.val += 2; };

expect(addTwo).to.increase(myObj, 'val').by(3, 'nooo why fail??');
expect(addTwo, 'nooo why fail??').to.increase(myObj, 'val').by(3);
```

#### .extensible

断言目标是可扩展的，这意味着可以向其添加新属性。原始类型永远不可扩展。

```javascript
expect({ a: 1 }).to.be.extensible;
```

在链中较早处添加 .not 以否定 .extensible。

```javascript
var nonExtensibleObject = Object.preventExtensions({}),
  sealedObject = Object.seal({}),
  frozenObject = Object.freeze({});

expect(nonExtensibleObject).to.not.be.extensible;
expect(sealedObject).to.not.be.extensible;
expect(frozenObject).to.not.be.extensible;
expect(1).to.not.be.extensible;
```

可以将自定义错误消息作为 expect 的第二个参数提供。

```javascript
expect(1, 'nooo why fail??').to.be.extensible;
```

#### .sealed

断言目标是密封的，这意味着不能向其添加新属性，且其现有属性不能重新配置或删除。然而，其现有属性仍然可能被重新赋值为不同的值。原始类型始终是密封的。

```javascript
var sealedObject = Object.seal({});
var frozenObject = Object.freeze({});

expect(sealedObject).to.be.sealed;
expect(frozenObject).to.be.sealed;
expect(1).to.be.sealed;
```

在链中较早处添加 .not 以否定 .sealed。

```javascript
expect({ a: 1 }).to.not.be.sealed;
```

可以将自定义错误消息作为 expect 的第二个参数提供。

```javascript
expect({ a: 1 }, 'nooo why fail??').to.be.sealed;
```

#### .frozen

断言目标是冻结的，这意味着不能向其添加新属性，且其现有属性不能重新赋值为不同的值、重新配置或删除。原始类型始终是冻结的。

```javascript
var frozenObject = Object.freeze({});

expect(frozenObject).to.be.frozen;
expect(1).to.be.frozen;
```

在链中较早处添加 .not 以否定 .frozen。

```javascript
expect({ a: 1 }).to.not.be.frozen;
```

可以将自定义错误消息作为 expect 的第二个参数提供。

```javascript
expect({ a: 1 }, 'nooo why fail??').to.be.frozen;
```

#### .finite

断言目标是一个数字，并且不是 NaN 或正/负无穷大。

```javascript
expect(1).to.be.finite;
```

在链中较早处添加 .not 以否定 .finite。然而，这样做是危险的。问题在于它通过断言 subject 不是一个数字、是 NaN、是正无穷大或是负无穷大来创建不确定的预期。最好是确定预期的确切输出，然后编写只接受该确切输出的断言。

当目标不预期是数字时，最好断言它是预期的类型，而不是断言它不是许多意外类型之一。

```javascript
expect('foo').to.be.a('string'); // 推荐
expect('foo').to.not.be.finite; // 不推荐
```

当预期目标是 NaN 时，最好明确断言这一点。

```javascript
expect(NaN).to.be.NaN; // 推荐
expect(NaN).to.not.be.finite; // 不推荐
```

当预期目标是正无穷大时，最好明确断言这一点。

```javascript
expect(Infinity).to.equal(Infinity); // 推荐
expect(Infinity).to.not.be.finite; // 不推荐
```

当预期目标是负无穷大时，最好明确断言这一点。

```javascript
expect(-Infinity).to.equal(-Infinity); // 推荐
expect(-Infinity).to.not.be.finite; // 不推荐
```

可以将自定义错误消息作为 expect 的第二个参数提供。

```javascript
expect('foo', 'nooo why fail??').to.be.finite;
```

#### .fail([message])

.fail(actual, expected, [message], [operator])
@param { Mixed } actual
@param { Mixed } expected
@param { String } message
@param { String } operator
抛出一个失败。

```javascript
expect.fail();
expect.fail("custom error message");
expect.fail(1, 2);
expect.fail(1, 2, "custom error message");
expect.fail(1, 2, "custom error message", ">");
expect.fail(1, 2, undefined, ">");
```

#### .fail([message])

.fail(actual, expected, [message], [operator])
@param { Mixed } actual
@param { Mixed } expected
@param { String } message
@param { String } operator
抛出一个失败。

```javascript
should.fail();
should.fail("custom error message");
should.fail(1, 2);
should.fail(1, 2, "custom error message");
should.fail(1, 2, "custom error message", ">");
should.fail(1, 2, undefined, ">");
```