# 基本类型和引用类型

## 基本类型
  - Undefined
  - Null
  - Number
  - String
  - Boolean
  - Symbo

## 引用类型
  - Object
  - Array
  - RegExp
  - Date
  - Function

## 两者区别
  - 基本类型使用的是栈内存，引用类型使用堆内存
  - 基本类型检测使用typeof, 引用类型使用instanceOf

## GC机制

以一个函数中的基本类型与引用类型举例

- 函数运行完以后基本类型数据自动进行垃圾回收
- 无任何变量引用该引用类型数据时才会进行回收  
   ```
  let a = {name:'Likely'}
  b = a
  b = a = null // 才会进行释放
  ```
