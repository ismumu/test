
# 堆的构成

## V8将堆分为了几个不同的区域

>**一、新生区**
>
>大多数对象被分配在这里。新生区是一个很小的区域，垃圾回收在这个区域非常频繁，与其他区域相独立。

>**二、老生指针区**
>
>这里包含大多数可能存在指向其他对象的指针的对象。大多数在新生区存活一段时间之后的对象都会被挪到这里。

>**三、老生数据区**
>
>这里存放只包含原始数据的对象（这些对象没有指向其他对象的指针）。字符串、封箱的数字以及未封箱的双精度数字数组，在新生区存活一段时间后会被移动到这里。

>**四、大对象区**
>
>这里存放体积超越其他区大小的对象。每个对象有自己mmap产生的内存。垃圾回收器从不移动大对象。

>**五、代码区**
>
>代码对象，也就是包含JIT之后指令的对象，会被分配到这里。这是唯一拥有执行权限的内存区（不过如果代码对象因过大而放在大对象区，则该大对象所对应的内存也是可执行的。
*译注：但是大对象内存区本身不是可执行的内存区）。*

>**六、Cell区、属性Cell区、Map区**
>
>这些区域存放Cell、属性Cell和Map，每个区域因为都是存放相同大小的元素，因此内存结构很简单。


每个区域都由一组内存页构成。内存页是一块连续的内存，经mmap（或者Windows的什么等价物）由操作系统分配而来。除大对象区的内存页较大之外，每个区的内存页都是1MB大小，且按1MB内存对齐。除了存储对象，内存页还含有一个页头（包含一些元数据和标识信息）以及一个位图区（用以标记哪些对象是活跃的）。另外，每个内存页还有一个单独分配在另外内存区的槽缓冲区，里面放着一组对象，这些对象可能指向其他存储在该页的对象。