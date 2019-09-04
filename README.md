# function-
a里面有一个函数b，变量i，函数b调用了变量i，最后return函数b。
最后在外面场景调用a函数的时候，每次调用，i会变成一个永久变量，每次结束运行之后并不销毁，而且i在外面的场景不能接触到（安全）
闭包的应用场景：
call（）和applay（）看做某个对象的方法，通过调用方法的形式来间接调用
1. 
f.call(o),其中o是对象，f是方法
2.
o.m = f
0.m()
delete o.m  // o中事前没有m
1和2同种功能
方法链，一个方法运行了紧着运行另外一个，不过每个方法返回不能是明确的参数
回调函数，和主函数同时进行（所以会加快进入的速度，但是如果我的主函数是登录操作的话，也可能会使得后面的数据来不及获取token？）

异步是同步发生的，我在login里面请求登录之后，调用callback就可以在登录之后再执行 一些异步的请求
或者我也可以不用callback，直接就login().then(()=>{})
# 立即执行函数
立即执行函数就是（1）定义一个匿名函数，（2）马上调用，无需绑定和等待任何异步，立刻执行。jq开篇就是用的这个
隔离作用域：避免变量污染和命名冲突
（function（name）{
  let greet = ‘hello’
  let sayHi = () => console.log(`${greet}${name}`)
}）
！！下面这个setTimeout是异步执行，操作会被挂起，指导for循环结束，
变量i是有变量提升效果的var定义，i覆盖全局，每次结束console都被挂起，直到结束i变为10
吧var变成let！！！：每次循环，i是块作用域，所以执行的值都会保留不会被下次循环覆盖掉
立即执行函数也利用率保护执行的思想
for(var i = 0; i < 10; i++){
  setTimeout(function() {
    console.log(i);   // 为什么输出了十个10，而不是0-9
  }, 0)
}
for （var i = 0； i<10; i++）{
 (function(ii) {
    setTimeout(function() {
    console.log(i);   // 为什么输出了十个10，而不是0-9
  }, 0)
 })(i)
}
# 变量提升
只把变量声明，函数声明提前（作用域内），不包括赋值（就是=）
# 模块化
1.独立的作用域
2.私有的变量方法，形成内逻辑
3.暴露出的方法变量，提供对外方法
（最好控制自己死有余，隐藏变量和逻辑处理，原理类似闭包）
# 柯里化
闭包是在函数内部可以返回一个函数，除了函数本身传入的参数，还以传入一个参数
var plus = function (a) {
  
  return function (b) {
    console.log(a + b);
  }
}

var add5 = plus(5);
var add8 = plus(8);

plus(20)(15);  // 35
add5(10); // 15
add8(10);  // 18
# 构造函数
不同对象方法，属性无法共享数据，这个时候需要用到prototype
var Person = function () {
  this.name = 'zfs';
  this.age = 25
  this.intro = function () {
    console.log(this.name + ' age '  +  this.age)
  }
}

var p1 = new Person()
console.log(p1.name)  // zfs
p1.intro ()  // zfs age 25

#构造函数的Prototype 
// 创建构造函数
function Person (name = 'sg') {
  this.name = name;  // 实例属性 name
  this.visited= [];  // 实例属性 travel
  this.sayHi = function () {  // 实例方法 sayHi()
    console.log(`Hello, ${name}`)
  }
}

Person.prototype.city = 'beijing'  // 原型属性 city
Person.prototype.travel = function (place) {
  this.visited.push(place)
  console.log(this.visited)
}

let zfs = new Person('zfs')
let borui = new Person('borui')

console.log(zfs.name)  // 'zfs'
zfs.sayHi()  // Hello, zfs
console.log(zfs.city);  // beijing
zfs.travel('shanghai'); // ['shanghai']

console.log(borui.name)  // borui  实例属性值各自私有
console.log(borui.city)  // beijing 实例属性被共享
zfs.travel('chongqin')  // ['shagnhai', 'chongqin'] 
// 原型共享属性中，当其中一个实例对象的属性值被修改，不会影响其他实例对象。
zfs.city = 'fujian'; 
console.log(zfs.city);  // fujian
console.log(borui.city);  // beijing
prototype是用来在构造函数添加共享属性，共享方法的，
（静态属性？实例属性？原型属性？）
# 自定义对象
# Memoization - 解决递归卡死
memoize用于优化比较耗时的计算，计算结果缓存到内存中，这样对于同一的输入值，只需要从内存中获取
n为40次回调的时候会堵塞
var fibonacci = function (n) {
  return n < 2 ? n : arguments.callee(n - 1) + arguments.callee(n - 2);
}
console.log(fibonacci(8))
function memoizeFunc (func) {
  var cache = {};
  return function () {
    var key = arguments[0];
    if (!cache[key]) {
      cache[key] = func.apply(this, arguments)
    }
    return cache[key]
  }
}
# 函数重载 
根据传入不同的参数个数，匹配查找到不同的结果，类似搜索显示
["Dean Edwards", "Sam Stephenson", "Alex Russell", "Dean Tom"]
// 分别传入0， 1， 2个参数测试
console.log(people.find());  // ["Dean Edwards", "Alex Russell", "Dean Tom"]
console.log(people.find("Dean"));  // ["Dean Edwards", "Dean Tom"]
console.log(people.find("Dean Edwards"));  // ["Dean Edwards"]

