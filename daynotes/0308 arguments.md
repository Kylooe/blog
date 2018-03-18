- arguments

  即函数传入的参数，为一个类数组对象，其length值取决于**实参**的个数。arguments中的每个值都和参数一一对应，具体的每项argument都可以被自由覆盖赋值，且相应的参数也会被修改为同样的值，参数与argument的联系始终存在。但在函数调用时没有传入的实参，其本身和对应的argument都可以被赋值，但任何一方被赋值后都会使得参数和argument断开联系。

  ```js
  function foo(x, y, z) {
    console.log(z, arguments[2], z===arguments[2]);
    z = 1;
    console.log(this.z)
    arguments[2] = 10;
    console.log(z, arguments[2], z===arguments[2]);
  }
  foo(0, 0, 0);  // "0 0 true" "10 10 true"
  foo(0, 0);  // "undefined undefined true" "1 10 false"
  ```

  因为形参相当于函数的局部变量，所以如果函数的上层作用域存在同名变量，二者互不影响。

- 记住transform只对block元素生效