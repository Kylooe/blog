- addEventListener的最后一个参数

  默认为false，即事件监听处理以冒泡模式触发，如一个`div.parent`，其内有`div.child`，两个div都绑定了相同的事件监听，此时先从`div.child`开始触发事件，再冒泡到`div.parent`；而当值为true时则是以事件捕获的顺序触发，从顶层父节点开始，由外而内传播。

  ```js
  function listener(e) {
      console.log(e.target.id, this.id);
      e.stopPropagation();
  }
  parent.addEventListener('click', listener, true);  // child parent
  child.addEventListener('click', listener, true);
  parent.addEventListener('click', listener, false);
  child.addEventListener('click', listener, false);  // child child
  // 最终只会显示 child parent
  ```

- :root

  css3选择器，用以设置全局css变量：

  ```css
  :root {
      --card-padding: 10px;
  }
  .card {
      padding: var(--card-padding);
  }
  ```

  根据caniuse的统计，IE 9以下不支持。

- 拓展运算符的实现原理

  很多时候其实就是`apply()`，`foo(…arr)`等同于`foo.apply(undefined, arr)`。EcmaScript中的底层实现仍不清楚，待考究。

- 函数对象的作用域链和执行环境的作用域链不一样

  函数创建时，直接将外层执行环境对象的作用域链复制为自己的作用域链，也就是函数的作用域链是在函数创建时创建的，因而是静态的词法作用域。

- 进程和线程

  进程是CPU资源分配的最小单位，线程是CPU调度的最小单位。一段程序被CPU执行时，运行相关的资源都要先到位，也就是CPU加载程序上下文，之后CPU开始执行，执行完毕后，CPU保存此段程序的上下文，这一整个执行过程的CPU工作时间段，就是一个进程。进程的粒度太大，可能分了很多个模块，线程就是其中的小模块，同一进程里的线程共享进程的上下文，可将线程视为粒度更小的CPU工作时间段。

- setTimeout、Promise和IIFE的执行顺序或优先级

  同步任务都在主线程上执行，形成执行栈。setTimeout等异步任务存在于task queue中，也可以说是event loop queue，只有当执行栈为空时才会从任务队列中取出异步任务进入主线程执行处理。对于`setTimeout(function() { ... }, 0)`，表示当前同步任务执行完毕后，也就是执行栈清空了，才立即执行指定callback。 而Promise在job queue中，和事件循环一样都是先进先出，不同的是在JavaScript线程内只有一个事件循环的任务队列，但可以有多个job queue。在执行完当前事件队列中的同步任务后，再执行本次任务中的所有job queue，然后再进行下一次事件循环。综上所以有：

  ```js
  console.log('head');
  setTimeout(() => console.log('setTimeout0'), 0);
  new Promise((resolve) => {
    resolve();
  }).then(() => {
    return console.log('Promise0-1');
  }).then(() => {
    return console.log('Promise0-2');
  });
  new Promise((resolve) => {
    resolve();
  }).then(() => {
    return console.log('Promise1-1');
  }).then(() => {
    return console.log('Promise1-2');
  });
  console.log('tail');
  // head tail Promise0-1 Promise1-1 Promise0-2 Promise1-2 setTimeout0
  ```

  ​