title: keng_of_block
date: 2015-11-02 16:55:17
categories: iOS
tags: [iOS,OC,block,weakSelf,strongSelf]
---

weakSelf相对于Self对象的内存空间维持一个弱引用, self对象本身对自己的内存空间保持一个强引用, 此时的block当中写``__strong typeof(weakSelf)strongSelf = weakSelf;``并不是把weakSelf转成strongSelf,而是在block的作用域当中又初始化了一个strongSelf去对self对象的内存空间进行强引用.
此时self对象的内存空间就有3个引用, weakSelf是一个弱引用, self和strongSelf是强引用 
当self=nil的时候 还有strongSelf在强引用在维持self对象的内存空间不被回收,去保证block当中的代码全部执行完毕
当block执行完毕后 strongSelf的作用域失效 局部变量strongSelf也就置为nil了 此时self和strongSelf这两个强引用都已经失效 weakSelf由于是弱引用 并不能维持对象的内存空间不被回收,所以self对象的内存空间就可以被释放了.

使用strongSelf既能保证block当中的方法全部被执行完毕,也能保证self不被block循环引用造成内存泄露了 