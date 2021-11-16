> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6942108227416883214) // 父类构造函数 function Parent(name) { this.name = name; this.arr = [1, 2] } // 父类原型 Parent.prototype.sayName = function () { console.log(this.name); } // 子类构造函数 function Child() { } // 将父类实例挂载到子类构造函数的原型上=>（实现继承的主要代码） // 所有由子类构造函数生成的实例都可以访问到父类构造函数的方法和属性 Child.prototype = new Parent('father'); // 需要手动改回constructor指向 Child.prototype.constructor = Child let child1 = new Child('child') let child2 = new Child() //无法向父类构造函数传参 child1.sayName()//father // 修改child1属性 child2会跟着改变 多个实例相互影响 Object.getPrototypeOf(child1).name = '修改后的名字' console.log(child1.name, child2.name);//修改后的名字 修改后的名字 child1.arr.push(3) console.log(child1.arr, child2.arr);//1,2,3 1,2,3 复制代码

**优点** `父类的方法子类同样可以访问 简单易实现 实现方式灵活`

**缺点** `多个实例之间相互影响 子类无法向父类传参`

构造函数继承
------

```
// 父类构造函数
        function Parent(name) {
            this.name = name;
            this.arr = [1, 2]
        }
        // 父类原型
        Parent.prototype.sayName = function () {
            console.log(this.name);
        }
        // 子类构造函数
        function Child(name) {
            // 将父类构造函数的this指向子类构造函数内部
            Parent.call(this, name);
        }

        let child1 = new Child('child1')
        let child2 = new Child('child2');
        // 可以向父类构造函数传参
        console.log(child1.name, child2.name);//child1 child2

        // 修改child1 child2不会跟着改变
        child1.arr.push(3)
        console.log(child1.arr, child2.arr);//1,2,3  1,2


        //无法访问到父类原型上的方法        
        child1.sayName()//undefined
复制代码
```

**优点**

> 复制父类的实例给子类
> 
> 解决了原型中包含引用类型值被所有实例共享的问题
> 
> 可以向父级函数传参

**缺点**

> 只能使用父类的属性和方法
> 
> 不能继承原型的属性和方法
> 
> 无法复用

组合继承
----

```
// 父类构造函数
        function Parent(name) {
            this.name = name;
            this.arr = [1, 2]
        }
        // 父类原型的方法
        Parent.prototype.sayName = function () {
            console.log(this.name);
        }
        // 子类构造函数
        function Child(name) {
            // 将父类构造函数的this指向子类构造函数内部
            Parent.call(this, name);//第二次调用父函数
        }
        // 父类实例挂载到子类原型上
        Child.prototype = new Parent('parent')//第一次调用父类构造函数
		 // 手动改回constructor指向
        Child.prototype.constructor = Child;


        let child1 = new Child('child1')
        let child2 = new Child('child2');

        console.log(child1);

        //可以访问到父类原型上的方法 并且可以传参      
        child1.sayName()//child1
        child2.sayName()//child2
复制代码
```

**优点**

> 利用 call，apply，bind 继承父类自身的属性
> 
> 利用子类原型继承父类实例可以实现共享父类原型的属性和方法
> 
> 函数可以复用

**缺点**

> 第一次调用构造函数显然是没有必要的
> 
> 因为第一次调用构造函数时候不需要函数内部的那些实例属性
> 
> 这么写只是想获得其原型上的方法罢了

寄生组合继承
------

```
// 父类构造函数
        function Parent(name) {
            this.name = name;
            this.arr = [1, 2]
        }
        // 父类原型的方法
        Parent.prototype.sayName = function () {
            console.log(this.name);
        }
        // 子类构造函数
        function Child(name) {
            // 将父类构造函数的this指向子类构造函数内部
            Parent.call(this, name);
        }


        // 通过中间函数将父类方法继承过来
        function inheritPrototype(Parent, Child) {
            // Object.create将父类原型的方法赋予子类构造函数
            Child.prototype = Object.create(Parent.prototype);
            // 手动改回constructor指向
            Child.prototype.constructor = Child;
        }

        inheritPrototype(Parent, Child);

        let child1 = new Child('child1')
        let child2 = new Child('child2');

        console.log(child1);//可以看到子类原型中已经没有父类构造函数的属性name了

        //可以访问到父类原型上的方法 并且可以传参      
        child1.sayName()//child1
        child2.sayName()//child2
复制代码
```

**优点：**

> *   只调用一次父类构造函数
> *   Child 可以向 Parent 传参
> *   父类方法可以复用
> *   父类的引用属性不会被共享

**由此可见寄生组合继承是最理想的继承方式，es6 继承正是组合继承的翻版**

ES6 继承
------

> ES6 中继承实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。

```
class Parent {
            //父类构造函数
            constructor(name) {
                this.name = name
            }
            //父类原型上的方法
            sayName() {
                console.log(this.name);
            }
        }

        class Child extends Parent {
            constructor(name) {
                //必须调用super函数=>
                //Parent.prototype.constructor.call(this)
                super(name);
            }
        }

        let child1 = new Child('child1');
        let child2 = new Child('child2');

        child1.sayName()//child1
        child2.sayName()//child2

复制代码
```

正是江南好风景，落花时节又逢君。
----------------