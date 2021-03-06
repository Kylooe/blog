- GC

  当一个对象直接占用或间接占用（保持对其他对象的引用）内存时，GC无法对其进行内存回收。当某个对象无法从GC root遍历到时，其使用到的内存就会被回收。只要依然被引用，哪怕只有其中一个属性，对象也不会被回收。JavaScript GC使用标记清除算法，也就是从root对象（全局对象）开始定期地寻找所有引用到的对象，遍历出来的零引用对象则是应该被回收的。对象从全局对象出发无法被获取、对象或元素无法从根被获取，则都将会触发GC。

- node

  + 操作文件

    node内置`fs`对象，提供了基本的文件操作API，可以对文件进行操作，如实现复制：

      ```Js
    var fs = require('fs');
    function copy(src, target) {
      fs.writeFileSync(target, fs.readFileSync(src));  // 一次性将文件读取到内存中再写入磁盘
      fs.createReadStream(src).pipe(fs.createWriteStream(target));  // 大文件复制使用数据流形式，否则内存扛不住
    }
      ```

  + Buffer

    二进制数据类型，可以通过`new Buffer()`创建，如：

    ```js
    var bin = new Buffer([ 0x68, 0x65, 0x6c, 0x6c, 0x6f ]);
    var str = bin.toString('utf-8');  // hello
    bin = new Buffer('hello', 'utf-8');  // <Buffer 68 65 6c 6c 6f>
    ```

    指定编码，字符串与Buffer可以互相转换。同时Buffer类型有`length`属性，可以得到字节长度；并可以通过`index`读取指定位置的字节，且和数组类型一样是可以修改指定位的。

    `slice()`方法会修改原Buffer，如有`var sub = bin.slice(1);`，那么对于sub的修改如`sub[0] = 0x6c`，会导致`bin[1]`的值也变为`0x6c`。因此如果想要复制Buffer，需要先创建一个新的Buffer再将数据复制进去：

    ```js
    var dup = new Buffer(bin.length);
    bin.copy(dup);
    ```

    这样bin和dup就互不影响了。

  + Stream

    当内存无法一次性容纳需要处理的数据或者追求更高效的数据处理时，需要使用数据流。Stream基于事件机制，例如data事件会在Stream被创建后不断地被触发：

    ```js
    var readStream = fs.createReadStream(path);
    readStream.on('data', function(chunk) { ... });
    ```

    数据流结束时则触发`end`。

    但如果data事件的监听回调处理速度跟不上读取或写入速度，Stream内部的缓存会溢出，因此往往需要`write`方法判断传入的数据是临时放入了缓存中还是已被处理，同时还有`drain`事件判断缓存中的数据是否都已全部写入完毕：

    ```js
    var readStream = fs.createReadStream(src);
    var writeStream = fs.createWriteStream(target);
    readStream.on('data', function(chunk) {
        if(!writeStream.write(chunk)) readStream.pause();  // 数据未写入，暂停数据读取
    });
    writeStream.on('drain', function() {
        readStream.resume();  // 当前缓存的数据已全部写入目标，继续只读数据流的数据读取
    });
    readStream.on('end', function() {
        writeStream.end();
    });
    ```

    ​
