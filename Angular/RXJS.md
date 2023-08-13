[Handbook](https://reactive.how/)
# Basic Concept
## Observable
Rx 库中的核心对象，它代表将来即将产生的事件或者值的集合
## Observer
一系列回调函数的集合，它监听 Observable 对象产生的值
## Subscription
- 一个 Observable 只有在被 Observer 订阅以后，才会产生值
- 这个订阅的行为称为 Subscription，订阅可以取消，取消以后 Observable 不再产生值

## Operators
纯函数，用于处理集合

## Subject
它既是一个 Observer，可以监听 Observable
同时也是一个 Observable，可以被其他多个 Observer 监听
它是实现数据广播的方式
