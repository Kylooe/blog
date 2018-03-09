20180130

- prototype的具体表现

  ```js
  var Parent = function() {
    this.text = 'Parent';
  }
  Parent.prototype.log = function() {
    var text = this.text;
    console.log(text);
  };
  var parent = new Parent();
  parent.log();  // 'Parent'
  parent.text = 'parent';
  parent.log();  // 'parent'
  ```

  直接new，parent为Parent的实例，Parent是构造函数，每一个构造函数都有一个原型对象，而每个实例都有一个指向原型对象的指针。**<u>原型的作用是包含所有实例共享的属性和方法</u>**。`parent.prototype`为undefined，实例没有原型。

  ES5的继承主要为原型链继承：

  ```js
  var Child = function() {
    this.text = 'Child';
  }
  Child.prototype = new Parent();
  var child = new Child();
  child.log();  // 'Child'
  ```

  因为原型对象是所有实例共享的，所以可以通过原型来实现继承。父类中所有构造函数与原型对象上的方法都会被继承。

  写在构造函数中的方法会覆盖写在原型对象的方法：

  ```js
  var Parent = function() {
    this.text = 'Parent';
    this.log = function() {
      console.log(this.text + ' constructor');
    };
  }
  Parent.prototype.log = function() {
    console.log(this.text + ' proto');
  };
  var parent = new Parent();
  parent.log();  // 'Panret constructor'
  ```

  之所以会这样是因为写在构造函数中的方法在每个实例上都会重新创建一遍，而写在原型对象里的都是所有实例共享的方法。

  子类的方法也会覆盖父类中的同名方法，不论这个方法在父类中的构造函数还是原型上定义。

  因此ES6的Class与extends都是语法糖，Class中constructor()里的属性与方法都是构造函数中的方法，Class中constructor()以外的方法则对应原型上的方法。继承也一样，但使用ES6继承时，子类的contructor中必须执行一次super()，代表调用父类的构造函数，也就是相当于`Child.prototype = new Parent()`。

- ES6的super()

  如上所述，作为函数调用时代表**<u>父类的构造函数</u>**。在普通方法中，super作为对象时则指向父类的原型，即相当于`Parent.prototype`。

- saga是什么？

  Redux-saga，管理一些异步操作或访问浏览器缓存等应用中比较复杂的操作。