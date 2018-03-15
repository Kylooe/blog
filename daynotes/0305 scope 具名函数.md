- this值

  在抽象逻辑意义上有一种类型，称为Reference，描述了一个变量的性质，由**base value**、**reference name**和**strict reference**组成。例如`var x = 1`，其Reference的base是EnvironmentRecord，name是x，strict为false。而一个对象里的属性或方法，则其Reference的base是所属对象。

  没写完，太抽象了不造咋用语言表述出来好……

- [[Scopes]]作用域链

  JavaScript创建执行上下文时会为当前的上下文创建相应的[[Scopes]]，当函数创建时，会保存所有父级变量对象到其内部属性[[Scopes]]中。例如：

  ```js
  function parent() {  // [[Scopes]]为globalContext.VO
    function child() {}  // [[Scopes]]为[parent.AO, globalContext.VO]
  }
  ```

  当进入某个函数的上下文、创建相应的变量对象后，会将函数的AO添加到[[Scopes]]的前端，形成当前执行上下文的作用域链。函数在定义时[[Scopes]]存储了父级的VO，但此时只是在进入执行上下文的阶段，这个VO是一个空引用，当代码进入执行阶段时父级的执行上下文才被创建，此时存储的VO才有内容。

- 具名函数

  函数表达式中函数的识别名是可选的，具名函数的识别名只在函数的主体中起作用，可以将具名函数的识别名看作是代理函数名，真正对函数起到识别作用的名称是被赋值的变量识别名。

  ```js
  var real = function agent() {
    return typeof agent;
  }
  typeof agent;  // undefined
  real();  // function
  ```

  且在函数内部，具名函数的名称所代表的变量无法被覆盖修改。不过如果重新声明同名的变量就不一样了。

  对于函数声明或匿名函数的变量赋值，其name属性为函数名或变量名；而对于具名函数，name属性值为具名函数的识别名。也就是具名函数的`arguments.callee.name`为它的识别名：

  ```js
  var real = function agent() {
    console.log(arguments.callee.name);  // agent
  };
  var real = function() {
    console.log(arguments.callee.name);  // real
  }
  function real() {
    console.log(arguments.callee.name);  // real
  }
  ```

  ​