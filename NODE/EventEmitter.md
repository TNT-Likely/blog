# 如何实现EventEmitter

EventEmitter是node中非常常见的一个实现"观察者"设计模式的内置模块, net、http等模块也都使用了它，在介绍如何实现EventEmitter之前我们先来梳理一下该模块的方法和属性

## 内置属性

- defaultMaxListeners 单个事件能够被注册的最大监听者数量，默认为10

## 内置方法

- addListener(eventName, listener) 对某个时间添加监听者
- removeListener(eventName, listener) 移除某个事件的特定监听器
- removeAllListeners(eventName) 移除某个事件所有的监听器
- emit(eventName[, ...args]) 触发某个事件，并且可以附带上参数
- eventNames() 获取所有的事件名称
- getMaxListeners() 获取单个事件能够被注册的最大监听者数量
- listenerCount(eventName) 获取某个事件的监听者数量
- listeners(eventName) 获取某个事件所有的监听者
- once(eventName, listener) 对某个事件添加监听器，该监听器只生效一次
- prependListener(eventName, listener) 对某个事件添加监听器，该监听器第一个触发
- prependOnceListener(eventName, listener) 对某个事件添加监听器，该监听器第一个触发且只生效一次
- setMaxListeners(n) 设置单个事件能够被注册的最大监听者数量
- on 同addListener
- off 同removeListener
- rawListeners 同listeners

## 如何实现

- 抽象出_addListener(eventName, listener[, isPrepend])
- once中的listener包装成wrap() => { removeListener(xx); listener(); }

### 简易版实现

```
class EventEmitter {
  constructor() {
    this._events = {}
    this._maxListenerCount = 10
  }

  _addListener(eventName, listener, prepend = false) {
    if(!this._events[eventName]) this._events[eventName] = []
    if(!this._events[eventName].length >= this.defaultMaxListener) return
    if(prepend) {
      this._events[eventName].unshift(listener)
    } else {
      this._events[eventName].push(listener)
    }
  }

  _onceWrap(eventName, listener) {
    const wrapFunc = () => {
      this.removeListener(eventName, listener)
      listener()
    }
    return wrapFunc
  }

  once(eventName, listener) {
    this.addListener(eventName, this._onceWrap(eventName, listener))
  }

  removeListener(eventName, listener) {
    if(this._events[eventName]) {
      this._events[eventName] = this._events[eventName].filter(i => {
        return i !== listener && i.realListener !== listener
      })
    }
  }

  get defaultMaxListener() {
    return this._maxListenerCount
  }

  addListener(eventName, listener) {
    this._addListener(eventName, listener, false)
  }

  prependLisenter(eventName, listener) {
    this._addListener(eventName, listener, true)
  }

  emit(eventName, ...args) {
    if(this._events[eventName]) {
      this._events[eventName].forEach(i => {
        i(...args)
      })
    }
  }
}
```

***[eventEmitter官方实现](https://github.com/nodejs/node/blob/master/lib/events.js)***