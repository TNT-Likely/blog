# ES5如何实现继承

- 原型链继承  
  让子类的原型链对象等于父类的实例
  ```
  function Dad() {
    this.firstName = 'sun'
    this.likes = {
      first: 'ball',
      second: 'game'
    }
  }

  Dad.prototype.sayHello = function() {
    console.log(`i like ${JSON.stringify(this.likes)}`)
  }

  function Son() {

  }

  Son.prototype = new Dad()

  const son1 = new Son()
  const son2 = new Son()
  son1.likes.first = 'read'

  console.log(son1.likes, son2.likes)
  son1.sayHello()
  son2.sayHello()
  ```
  ***缺陷***
  - 如果某个属性为引用类型，则会被所有实例共享，例如上面的likes
  - 无法实现参数传递
- 构造函数继承  
  在子类构造函数内调用父类构造函数
  ```
  function Dad(likes) {
    this.firstName = 'sun'
    this.likes = likes || {
      first: 'ball',
      second: 'game'
    }
  }

  Dad.prototype.sayHello = function() {
    console.log(`i like ${JSON.stringify(this.likes)}`)
  }

  function Son(likes) {
    Dad.call(this, likes)
  }

  const son1 = new Son()
  const son2 = new Son()
  son1.likes.first = 'read'

  console.log(son1.likes, son2.likes)
  son1.sayHello()
  son2.sayHello()
  ```
  ***缺陷***
  - 属性无法共享
  - 无法继承父类原型链上的方法
- 寄生继承  
  使用Object.create来创建一个新对象，使用现在的对象来提供新创建对象的__proto__(原型)
  ```
  const dad = {
    firstName: 'sun',
    likes: {
      first: 'ball',
      second: 'game'
    },
    sayHello: function() {
      console.log(`i like ${JSON.stringify(this.likes)}`)
    }
  }

  const son1 = Object.create(dad)
  const son2 = Object.create(dad)
  son1.likes.first = 'read'

  console.log(son1.likes, son2.likes)
  son1.sayHello()
  son2.sayHello()
  ```
  ***缺陷***
  - 如果某个属性为引用类型，则会被所有实例共享，例如上面的likes
  - 无法实现参数传递
- 寄生组合式继承  ***推荐使用***  
  ```
  function Dad(likes) {
    this.firstName = 'sun'
    this.likes = likes || {
      first: 'ball',
      second: 'game'
    }
  }

  Dad.prototype.sayHello = function() {
    console.log(`i like ${JSON.stringify(this.likes)}`)
  }

  function Son(likes) {
    Dad.call(this, likes)
  }
  Son.prototype = Object.create(Dad.prototype, {
    constructor: {
      value: Son,
      enumerable: false,
      writable: true,
      configurable: true
    }
  })

  const son1 = new Son()
  const son2 = new Son()
  son1.likes.first = 'read'

  console.log(son1.likes, son2.likes)
  son1.sayHello()
  son2.sayHello()
  ```

> ES6中的extend语法糖实际上也是采取了“寄生组合”这种方式来实现，具体可以查看babel等转义后的代码