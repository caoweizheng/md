- string
- number
- 数组遍历
- 对象
	- is()
	- assign()
- Sybol
- Set





# ------ #
- 原型链
	- 每一个实例对象都有一个__proto__属性（隐式原型），在js内部用来查找原型链；每一个构造函数都有prototype属性（显示原型），用来显示修改对象的原型，实例.__proto__=构造函数.prototype=原型。原型链的特点就是：通过实例.__proto__查找原型上的属性，从子类一直向上查找对象原型的属性，继而形成一个查找链即原型链
	
	- function Person() {}
	- var p1 = new Person()
	- p1.__proto__ 指向原型对象
	- Person.propertype指向原型对象
	- 原型对象的constructor()指向构造函数Person
	- 继承方式
		- 原型链继承	
		- 构造函数继承
		- 组合继承
		- 寄生继承
- call apply bind
	- 改变this的指向
- this
	- 作用域不同，指向不同