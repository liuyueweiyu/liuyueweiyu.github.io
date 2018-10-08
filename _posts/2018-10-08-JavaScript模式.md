---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript模式               # 标题 
subtitle: JavaScript模式读书笔记
date:       2018-10-08              # 时间
author:     顾小五                      # 作者
catalog: true                       # 是否归档
tags:                               #标签
    - 读书笔记
    - 前端

typora-copy-images-to: images

---

#### 第一章 简介

1. 尽量多使用对象的组合，而不是使用类的继承。

   通过已有的对象组合来获取新对象，是比通过很长的父—子继承链来创建新的对象更好的一种方法

#### 第二章 基本技巧

1. 创建隐式全局变量的反模式是带有var声明的链式赋值

   ```javascript
   function foo(){
       var a = b = 0;
       //此时，a是局部变量，b是全局变量
   }
   ```

2. 变量释放时的副作用

   隐含全局变量和明确定义的全局变量有细微的不同，不同之处在于能否使用delete操作撤销变量

   - 使用var创建的全局变量不能删除
   - 不适用var创建的隐含全局变量可以删除

   这变量隐含全局变量严格意义上来讲不是真正的变量，而是全局对象属性，属性可以通过delete操作符删除

3. 单一var模式

   只使用一个var在函数顶部进行变量声明是一种非常有用的模式，它的好处在于:

   - 提供一个单一的地址以查找到函数需要的所有局部变量
   - 防止出现变量在定义前就被使用的逻辑错误
   - 帮助牢记要声明的变量，尽可能的少使用全局变量
   - 更少的编码

   ```javascript
   function func(){
       var a = 1,
           b = 2,
           i,
           j;
   }
   ```

4. DOM操作是非常耗时的，所以注意在for循环中存储DOM容器的长度

5. for-in循环

   在循环中使用hasOwnProperty过滤原型属性

   ```javascript
   var man = {
       hands:2,
       legs:2,
       head:1
   }
   
   if (typeof Object.prototype.clone === 'undefined') {
       Object.prototype.clone = ()=>{
           console.log('colne!');
       }
   }
   
   for(var i in man){
       if(man.hasOwnProperty(i)){
           console.log(i,':',man[i]);
       }
   }
   // hands: 2
   // legs: 2
   // head: 1
   
   for (var i in man) {
       console.log(i, ':', man[i]);
   }
   // hands: 2
   // legs: 2
   // head: 1
   // clone: () => {
   //     console.log('colne!');
   // }
   ```

6. 不要增加内置的原型

7. Switch模式

   可以使用以下模式来提高switch语句的可读性和健壮性

   ```javascript
   var inspect_me = 0,
       result = '';
   switch(inspect_me){
       case 0:
           result = 'zero';
           break;
       case 1:
           result = 'one';
           break;
       default:
           result = 'unknown';
   }
   ```

   switch语句通常采用如下格式：

   - 使每个case和switch纵向排列整齐
   - 在每个case语句中使用代码缩进
   - 在每个case语句结尾有一个明确的break语句
   - 避免使用fall-throughs（也就是有意不使用break语句，以使得程序会按顺序一直向下执行）。如果确实希望采用fall-throughs，那么请确信在代码中使用fall-throughs的确是最好的途径，因为在代码中这样做会让其他阅读代码的人以为代码是有错误的（维护成本）
   - 用default语句来作为switch的结束

8. 避免使用隐式类型转换

   尽可能使用 === 比较

9. 避免使用eval()

10. setInterval(),setTimeout(),function()等构造函数来传递参数，在大部分情形下，会导致类似eval()的隐患，因此应该尽量避免使用这些函数。在幕后，JavaScript仍然不得不评估和执行以程序代码方式传递过来的字符串：

  ```javascript
  //反模式
  setTimeout('myFunc()',1000);
  setTimeout('myFunc(1,2,3)',1000);
  //推荐的模式
  setTimeout(myFunc,1000);
  setTimeout(function(){
      myFunc(1,2,3);
  },1000);
  ```

11. 使用new Function()构造函数和eval()比较类似，因此该函数的使用也需要十分小心。

    如果一定需要使用eval，可以尝试使用new Function代替eval。这样做的一个潜在好处是由于在new Function中的代码将在局部函数空间中运行，因此代码中任何采用var定义的变量不会自动成为全局变量

    ```javascript
    console.log(typeof un); //undefined
    console.log(typeof deux); //undefined
    console.log(typeof trios); //undefined
    
    var a = 'var un = 1;console.log(un);';
    eval(a);//1
    
    a = 'var deux = 2;console.log(deux);';
    new Function(a)();//2
    
    a = 'var trios = 3;console.log(trios);';
    (function () {
        eval(a);
    })();//3
    
    console.log(typeof un); //number
    console.log(typeof deux); //undefined
    console.log(typeof trios); //undefined
    ```

    另一个new Function和eval的区别在于eval会影响到作用域链，而function更多的类似一个沙盒，无论在哪执行都只能看到全局作用域

    ```javascript
    (function () {
        var local =1;
        eval('local = 3;console.log(local);');  //3
        console.log(local); //3
    })();
    (function () {
        var local = 1;
        new Function('console.log(typeof local);')();   //undefined
        console.log(local); //1
    })();
    ```

12. 使用parseInt()的数值约定

    为了避免不一致性和未预期的结果，请每次都具体制定进制参数：

    ```javascript
    var month = '06',
        year = '09';
    //第二个参数表示进制
    console.log(parseInt(month,8));	//6
    console.log(parseInt(year,10));	//9
    ```

    拓展=>

    ```javascript
    console.log([1,2,3].map(parseInt));
    //[ 1, NaN, NaN ]
    ```

#### 第三章 字面量和构造函数

1. 强制使用new的模式

   new操作符会改变this指向

#### 第四章 函数

1. 注意回调函数的this指向

2. 惰性函数定义，具有向后惰性，执行了更少的工作

3. 即时函数的参数

   ```javascript
   (function (who,when) {
       console.log(who,when);
   })('Tom',new Date());
   //Tom 2018-10-05T04:39:53.003Z
   ```

4. 即时对象初始化

   这种模式使用带有init()方法的对象，该方法在创建对象后将会立即执行。init()函数需要负责所以的初始化任务

   ```javascript
   ({
       maxwidth: 600,
       maxheight: 400,
       gimmeMax: function() {
           return this.maxwidth + "x" + this.maxheight;
       },
       init: function() {
           console.log(this.gimmeMax());
       }
   }).init();
   ```

   这种模式主要适用于一次性的任务，而且在init完毕之后也没有对该对象的访问。如果想在init完毕之后保存改对象的一个引用，可以通过init尾部加一个return this、

5. 函数属性——备忘录模式

   函数添加一个自定义属性缓存函数的结果，因此，在一下次调用的时候就不用重做潜在的重复计算。

   ```javascript
   var myFunc = function (params) {
       if(!myFunc.cache[params]){
           var result = {};
           //...开销很大的操作
           result = new Date();
           myFunc.cache[params] = result;
       }
       return myFunc.cache[params];
   }
   myFunc.cache = {};
   
   console.log(myFunc(1)); //2018-10-05T06:29:39.959Z
   setTimeout(() => {
       console.log(myFunc(1)); //2018-10-05T06:29:39.959Z
   }, 1000);
   //复杂的参数可以序列化参数——JSON.stringify
   ```

6. Curry

   通用curry化函数的实例

   ```javascript
   function schonfinkelize(fn) {
       var slice = Array.prototype.slice,
           stored_args = slice.call(arguments,1);
           return function () {
               var new_args = slice.call(arguments),
                   args = stored_args.concat(new_args);
               return fn.apply(null,args);
           }
   }
   function add(a,b,c) {
       return a+b+c;
   }
   
   add = schonfinkelize(add,1);
   console.log(add(2,3));//6
   ```

7. 小结

   函数的模式包括：

   - API模式，它们可以为函数提供更好且更整洁的接口。这些模式包括以下几个

     - 回调模式

       将函数作为参数进行传递

     - 配置对象

       有助于保持收到控制的函数的参数数量

     - 返回函数

       当一个函数的返回值是另一个函数式

     - curry化

       当新函数是基于现有函数，并加上部分参数列表创建时

   - 初始化模式，可以在不污染全局命名空间的情况下，使用临时变量以一种更加整洁、结构化的方式执行初始化以及设置任务

     - 即时函数

       只要定义之后就会立即执行

     - 即时对象初始化

       匿名对象组织了初始化任务，提供了可被立即调用的方法

     - 初始化时分支

       帮助分支代码在初始化代码执行过程中仅检测一次，这与以后在程序生命周期内多次检测相反

   - 性能模式，可以帮助加速代码运行

     - 备忘模式

       使用函数属性以便使得计算过的值无须再次计算

     - 自定义模式

       以新的主体重写本身，以使得在第二次或以后调用时仅需要执行更少的工作

#### 第五章 对象创建模式

1. 声明依赖关系

   在函数或模块顶部声明代码所依赖的模块是一个非常好的主意。该声明仅涉及创建一个局部变量并使其指向所需的模块。

   ```javascript
   var myFunc = function(){
       //依赖
       var event = YAHOO.util.Event,
           dom = YAHOO.util.Dom;
       //....
   }
   ```

   - 显式的依赖声明向您的用户表明了他们确定需要的特定脚本文件已经包含在该页面中
   - 在函数顶部的前期声明可以更容易的发现并解析依赖
   - 解析局部变量（比如dom）的速度总要比解析全局变量（比如YAHOO）要快，甚至比使用全局变量的嵌套属性（比如YAHOO.util.Dom）还要快，这导致了更好的性能。当使用这种的依赖声明模式时，全局符号解析只会在函数中执行一次。

2. 私有成员

   虽然JavaScript语言中没有用于私有成员的特殊语法，但是可以通过使用闭包来实现这种功能

   ```javascript
   function Gadget() {
       //私有成员
       var name = 'TOM';
       this.getName = function () {
           return name;
       };
   }
   
   const tom = new Gadget();
   console.log(tom.name); //undefined
   console.log(tom.getName()); //TOM
   //不同实例会共享
   const tommy = new Gadget();
   console.log(tommy.getName());//TOM
   ```

3. 对象字面量以及私有性

   ```javascript
   var myObj;
   (function () {
       //私有成员
       var name = 'TOM';
       myObj = {
           //特权方法
           getName:function () {
               return name;
           }
       }
   })();
   console.log(myObj.getName());	//TOM
   
   //或者
   
   var myObj = (function () {
       var name = 'TOM';
       return {
           getName:function () {
               return name;
           }
       }
   })();
   
   console.log(myObj.getName());//TOM
   ```

4. 将私有方法揭示为公共方法

   ```javascript
   var myarray;
   (function () {
       var astr = '[object Array]',
           toString = Object.prototype.toString;
       function isArray(a) {
           return toString.call(a) === astr;
       }
       function indexOf(haystack,needle) {
           var i = 0,
               max = haystack.length;
           for(;i<max;i++){
               if(haystack[i] === needle){
                   return i;
               }
           }
           return -1;
       }
       myarray = {
           isArray,
           indexOf,
           inArray:indexOf
       }
   })();
   console.log(myarray.isArray([1,2]));        //true
   console.log(myarray.isArray({0:1}));        //false
   console.log(myarray.indexOf([1, 2, 3], 3)); //2
   console.log(myarray.inArray([1, 2, 3], 3)); //2
   
   myarray.indexOf = null;
   console.log(myarray.inArray([1, 2, 3], 3)); //2
   ```

5. 链模式

   链模式能够一个接一个的调用对象的方式，而无需将一个操作返回的值赋给变量，并且无需将调用分割为多行

   ```javascript
   myobj.method1('hello').method2().method3('world').method4();
   ```

   当创建的方法其返回的是无任何意义的值时，可以使它们返回this，即正在使用的对象的实例。这将使对象的用户调用前面链接的下一个方法。

   ```javascript
   var obj = {
       value:1,
       increment:function () {
           this.value += 1;
           return this;
       },
       add:function (v) {
           this.value += v;
           return this;
       },
       show:function () {
           console.log(this.value);
           return this;
       }
   }
   obj.increment().add(2).show();//4
   ```

#### 第七章 设计模式

1. 单例模式

   - 静态属性中的实例

     ```javascript
     function Universe() {
         //判断是否已经有实例
         if (typeof Universe.instance === 'object') {
             return Universe.instance;
         }
         //创建实例
         this.start_time = 0;
         this.bang = 'big';
     
         //缓存实例
         Universe.instance = this;
     }
     
     const uni1 = new Universe();
     const uni2 = new Universe();
     console.log(uni1 === uni2); //true
     ```

     缺点在于其instance属性是公开的。其他代码有修改它的可能

   - 闭包中的实例

     ```javascript
     function Universe() {
         //缓存实例
         var _this = this;
         //正常进行
         this.start_time = 0;
         this.bang = 'big';
         //重写构造函数
         Universe = function () {
             return _this;
         };
     }
     
     const uni1 = new Universe();
     const uni2 = new Universe();
     console.log(uni1 === uni2); //true
     ```

     这样做的缺点会丢失所有在初始定义和重定义时刻之间添加到它里面的属性

     ```javascript
     function Universe() {
         //缓存实例
         var _this = this;
         //正常进行
         this.start_time = 0;
         this.bang = 'big';
         //重写构造函数
         Universe = function () {
             return _this;
         };
     }
     
     Universe.prototype.nothing = true;
     
     const uni1 = new Universe();
     
     Universe.prototype.everything = true;
     
     const uni2 = new Universe();
     
     console.log(uni1.nothing);      //true
     console.log(uni2.nothing);      //true
     console.log(uni1.everything);   //undefined
     console.log(uni2.everything);   //undefined
     //此时uni1.constructor仍然指向了初始的构造函数
     console.log(uni1.constructor === Universe); //false
     ```

     所以做如下修改

     ```javascript
     function Universe() {
         //缓存实例
         var _this;
     
         //重写构造函数
         Universe = function () {
             return _this;
         };
         //保留原型属性
         Universe.prototype = this;
         //实例
         _this = new Universe();
         _this.constructor = Universe;
     
         //正常进行
         this.start_time = 0;
         this.bang = 'big';
         
         return _this;
     }
     
     Universe.prototype.nothing = true;
     
     const uni1 = new Universe();
     
     Universe.prototype.everything = true;
     
     const uni2 = new Universe();
     
     console.log(uni1.nothing);      //true
     console.log(uni2.nothing);      //true
     console.log(uni1.everything);   //true
     console.log(uni2.everything);   //true
     console.log(uni1.constructor === Universe); //true
     ```

2. 工厂模式

   ```javascript
   //父构造函数
   function CarMaker() {}
   
   //a method of the parent
   CarMaker.prototype.drive = function () {
       console.log('Vroom,I have '+this.doors + 'doors');
   }
   
   //静态工厂方法
   CarMaker.factory = function (type) {
       var constr = type,
           newcar;
       //如果构造函数不存在，则会发生错误
       if (typeof CarMaker[constr] !== 'function') {
           throw {
               name :'Error',
               message: constr+ 'does not exist'
           }
       }
       //在这里，构造函数是已知存在的
       //我们是的原型继承父类，但仅继承一次
       if (typeof CarMaker[constr].prototype.drive !== 'function') {
           CarMaker[constr].prototype = new CarMaker();
       }
   
       //创建一个新的实例
       newcar = new CarMaker[constr]();
       return newcar;
   }
   
   CarMaker.Compact = function () {
       this.doors = 4;
   };
   CarMaker.Convertible = function () {
       this.doors = 2;
   };
   CarMaker.SUV = function () {
       this.doors = 17;
   }
   
   CarMaker.factory('Compact').drive(); //Vroom,I have 4doors
   CarMaker.factory('Convertible').drive(); //Vroom,I have 2doors
   CarMaker.factory('SUV').drive(); //Vroom,I have 17doors
   ```

3. 装饰者模式

   ```javascript
   function Sale(price = 100) {
       this.price = price;
   }
   
   Sale.prototype.getPrice = function () {
       return this.price;
   }
   
   Sale.decorators = {};
   
   Sale.decorators.fedtax = {
       getPrice:function () {
           var price = this.uber.getPrice();
           price += price * 5 / 100;
           return price;
       }
   }
   
   Sale.decorators.quebec = {
       getPrice: function () {
           var price = this.uber.getPrice();
           price += price * 7.5 / 100;
           return price;
       }
   }
   
   
   Sale.decorators.money = {
       getPrice: function () {
           return '$' + this.uber.getPrice().toFixed(2);
       }
   }
   
   Sale.decorators.cdn = {
       getPrice: function () {
           return 'cdn$' + this.uber.getPrice().toFixed(2);
       }
   }
   
   Sale.prototype.decorate = function (decorator) {
       var F = function (){},
           overrides = this.constructor.decorators[decorator],
           i,newobj;
       F.prototype = this;
       newobj = new F();
       newobj.uber = F.prototype;
       for (i in overrides){
           if(overrides.hasOwnProperty(i)){
               newobj[i] = overrides[i];
           }
       }
       return newobj;
   }
   
   // var sale = new Sale(100);
   
   // console.log(sale.decorate('quebec').getPrice());
   ```

4. 策略模式

   ```javascript
   var validator = {
       //所有可用的检查
       types : {},
   
       //在当前验证会话中的错误消息
       messages:[],
       //当前验证配置
       //名称:验证类型
       config :{},
       //接口方法
       // `data` 为 键-值对
       validate:function (data) {
           var i,
               msg,
               type,
               checker,
               result_ok;
           //重置所有消息
           this.messages = [];
           for (i in data){
               if(data.hasOwnProperty(i)){
                   type = this.config[i];
                   checker = this.types[type];
                   if(!type){
                       continue;   //不需要验证
                   }
                   if(!checker){
                       throw {
                           name : 'ValidationError',
                           messages:'No handler to validate type '+ type
                       };
                   }
                   result_ok = checker.validate(data[i]);
                   if(!result_ok){
                       msg = 'Invalid value for *'+i+'*,'+checker.instructions;
                       this.messages.push(msg);
                   }
               }
           }
           return this.hasErrors();
       },
       //帮助程序
       hasErrors:function () {
           return this.messages.length !== 0;
       }
   }
   
   validator.types.isNonEmpty = {
       validate :function (value) {
           return value !== '';
       },
       instructions:'the value cannot be empty'
   }
   
   validator.types.isNumber = {
       validate:function (value) {
           return !isNaN(value);
       },
       instructions:'the value can only be a valid number'
   }
   
   validator.types.isAlphaNum = {
       validate:function (value) {
           return !/[^a-z0-9]/i.test(value);
       },
       instructions: 'the value can only contain charactors and numbers,no special symbols'
   }
   
   var data = {
       first_name : 'Super',
       last_name: 'Man',
       age:'unknown',
       username:'test**'
   }
   
   validator.config = {
       first_name: 'isNonEmpty',
       age : 'isNumber',
       username: 'isAlphaNum'
   }
   
   validator.validate(data);
   if (validator.hasErrors()){
       console.log(validator.messages.join('\n'));
       //Invalid value for *age*,the value can only be a valid number
   	//Invalid value for *username*,the value can only contain charactors and numbers,no special symbols
   }
   ```

5. 中介者模式和观察者模式咕了（xxx

6. 小结

   - 单体模式

   - 工厂模式

     根据字符串指定的类型在运行时创建对象的方法

   - 迭代器模式

     提供一个API来遍历或操纵复杂的自定义数据结构

   - 装饰者模式

     通过预定义装饰者对象中添加功能，从而在运行时调整对象

   - 策略模式

     在选择最佳策略以处理特定任务的时候仍然保持相同的接口

   - 外观模式

     通过把常用方式包装到一个新方法中，从而提供一个更为便利的api

   - 代理模式

     使对象不是直接"通话"，而是仅仅通过一个中介者进行通信，从而松散耦合

   - 观察者模式

     通过创建"可观察的"对象，当发生一个感兴趣的事件时可将该事件通告给所有观察者，从而松散耦合

#### DOM和浏览器模式

1. 关注分离

   关注分离意味着

   - 通过将css关闭来测试页面是否仍然可用，内容是否依然可读
   - 将JavaScript关闭来测试页面仍然可以执行其正常功能，所有的连接是否能正常工作，所有表单可以正常工作并且能正确提交信息
   - 不适用内联处理器和内联样式
   - 标签语义化

2. DOM访问

   - 避免在循环中使用dom访问
   - 将dom引用分配给局部变量，并使用这些局部变量
   - 在可能的情况下使用selector API
   - 当阻碍html容器中重复使用时，缓存重复的次数

3. 添加节点时，使用文档碎片

   ```javascript
   var frag = document.createDocumentFragment();
   // 往 frag 添加子节点
   //...
   // 最后
   document.getElementById('root').appendChild(frag);
   ```

4. 修改节点时，克隆镜像

   ```javascript
   var oldnode = document.getElementById('root'),
       clone = oldnode.cloneNode(true);
   
   //处理克隆对象...
   
   //最后
   oldnode.parentNode.replaceChild(clone,oldnode);
   ```
