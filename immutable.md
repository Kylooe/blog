immutable 2018.01.15

很久没有写过笔记啦，最近的项目都用到了[immutable](https://facebook.github.io/immutable-js/)，顺道学习一下以及整理整理笔记。

不少使用 React + Redux 或者 React + Flux 的项目都会同时使用 Immutable.js，原生 JavaScript 中由于使用引用赋值因此对象一般都是可变的，而这个库主要给开发者提供不变的数据类型和固定的数据结构，即用于创建不可变数据的集合，创建后不能被修改，其中用得最多最常见的是 `List` 和 `Map`。这些数据类型并非 JavaScript 原生支持的类型，要与 JavaScript 互相转换使用比较直接的方法是 `fromJS()` 和 `toJS()`。

- List

List 类型与原生 JavaScript 里的 Array 类似，都是数组，同时也都拥有 `push()`、`pop()`、`shift()`等等与原生 JavaScript 里的方法同名且具有相同表现的 API。但由于 Immutable 创建的数据是不可变的，原生 JavaScript 中`pop()`返回取出的最后一项，`push()`返回操作后数组的长度，而 Immutable 中都是返回一个执行方法后新的 List，作为操作目标的原数组其数据与结构都是不变的。`list.size`相当于`array.length`，List 没有`length`属性。以及取得 List 中的某一项值需要使用`get()`方法。

- Map

Map 类型与 ES 6 的 Map 结构性质是相同的，都是键值对的数据结构，只是键的类型范围宽泛了，任何类型的值都可以作为键，也就是实际上为“值-值对”。Immutable 中对 Map 的取值与设值 API 和 List 是差不多的。

- Seq

Seq 是 Immutable 中非常特别的一种结构类型，Map 和 List 都能通过`toSeq()`转换为 Seq。Seq 也可以使用一些针对集合的操作方法如`map()`、`filter()`等等，但尤为不同的是 Seq 是惰性的，只在你确切要取得 Seq 中的某个值时才会进行必要的计算。假设有

```js
var arr = [1, 2, 3, 4].map(val => val * 2);
```

在对`arr`赋值时对于数组`[1, 2, 3, 4]`的计算就已经完成了，同时将结果`[2, 4, 6, 8]`赋给`arr`。但如果是

```js
var seq = Immutable.Seq.of(1, 2, 3, 4).map(val => val * 2);
```

只有明确地取值如`seq.get(0)`时才会取得值`2`，同时 Immutable 只会进行对于位置0的值`1`的相关计算，不会计算开发者此时没有需求的其他值。因此 Immutable 能够有效地优化速度和节省内存。

除此以外 Immutable 一个明显的好处是得益于不可变的数据，在避免污染原数组或原对象的前提下插入新值要简单得多。比如向对象插入属性不需要再`Object.assign({}, obj, { key: value })`了，直接`obj.set('key', value)`就好。

不过任何库类都有缺点，学习成本就不必多言了，比较明显的问题是使用 Immutable 时容易与原生 JavaScript 对象混淆。我了解 Immutable 是一无所知的时候同时接触 React、Rudex 和 Immutable，最开始还以为是 React 的什么特性，API 又长得和原生的很相似，真是十分炸裂。虽然 List 和 Map 分别对应 Array 和 Object，但是 API 还是不同的，比如没有 length 而是 size，无法使用`list[0]`而是通过`list.get(0)`来获取等等等等。因为开发过程中也同时要频繁使用原生对象，同时也并没有约定好变量的命名规则，经常不知不觉就混淆了，跑不起来了才无奈地去改语句。 
