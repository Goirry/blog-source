title: "Block在ARC下的循环引用问题"
date: 2015-11-23 16:55:17
categories: iOS
tags: [iOS,OC,block,weakSelf,strongSelf]
---

从iOS4.2开始,苹果公司就推出了ARC的内存管理机制,通过自动引用计数对内存进行管理,让程序员们可以把更多的精力都集中到实现功能需求,而不是去处理那些无趣却又偏偏容易脑子一糊涂就出错的内存引用计数.但是ARC机制并不是万能的,在程序为了能够正常运行的情况下,有时会隐式地持有或复制对象,出现两个对象互相持有,导致两个对象都无法被释放的情况,我们称为循环引用.由于两个对象都无法被释放,就出现了内存泄露问题.

而循环引用的打破,则需要两个对象中的一个使用`__weak`来修饰去进行弱引用,弱引用并不真正去持有对象.

比如:`A对象`在强引用失效后,即使仍有`其他对象`对`A对象`进行弱引用,`A对象`仍然会被自动销毁,`A对象`引用的内存空间被系统回收.

一般情况下, 在ViewController中使用Block,如何使用weakSelf

所以以下的self指的就是ViewController了

当时的我进入了一个误区,既然使用了weakSelf对self弱引用,为什么又看见有人在block中写`__strong`把weakSelf转换成strongSelf,我当时想又把弱引用转换成强引用,这不有循环引用了吗?事实肯定不是这样的,只是我还没有理解.

最后终于经过一番折腾,终于把这个困扰已久的问题给搞明白了,在此进行记录和讲解,希望给碰到同样问题的朋友一个满意的答案.

weakSelf相对于Self对象的`内存空间`维持一个弱引用, self对象本身对自己的内存空间保持一个强引用, 此时在block当中写``__strong typeof(weakSelf)strongSelf = weakSelf;``并不是把weakSelf转成strongSelf,而是在block的作用域当中又初始化了一个strongSelf去对self对象的内存空间进行强引用.
此时self对象的内存空间就有3个引用, weakSelf是一个弱引用, self和strongSelf是强引用 
当self=nil的时候 还有strongSelf在强引用在维持self对象的内存空间不被回收,去保证block当中的代码全部执行完毕
当block执行完毕后 strongSelf的作用域失效 局部变量strongSelf也就置为nil了 此时self和strongSelf这两个强引用都已经失效 weakSelf由于是弱引用 并不能维持对象的内存空间不被回收,所以self对象的内存空间就可以被释放了.

使用strongSelf既能保证block当中的方法全部被执行完毕,也能保证self不被block循环引用造成内存泄露了

strongSelf的应用时机