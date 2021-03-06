# Chapter9

# 9.1
- 通常使用`Vector`是最好的选择，除非你有很好的理由选择其他容器。
- 如果有很多小的元素和空间的额外开销很重要，则不要使用`list`或`forward_list`
- 要求随机访问元素，则使用`vector`或`deque`
- 要求在容器中间插入或删除元素，则使用`list`或`forward_list`
- 要求在元素头尾位置插入或删除元素，则使用`deque`

# 9.2.5
除`array`外，`swap`不对任何元素进行拷贝、删除或插入操作，因此可以保证在常数时间完成。
元素不会被移动的事实意味着，除`string`外，指向容器的迭代器、引用和指针在`swap`操作之后都不会失效。

# 9.3.6
向容器中添加元素和从容器中删除元素的操作可能会使指向容器元素的指针、引用或迭代器失效。一个失效的指针、引用或迭代器不再表示任何元素。

1.在容器中添加元素后
- 如果容器是vector或string，且存储空间被重新分配，则指向容器的迭代器、指针和引用都会失效。如果存储空间为重新分配，指向插入位置之前的元素迭代器、指针和引用仍有效，但指向插入位置之后元素的迭代器、指针和引用均会失效；

- 对于deque，插入到首尾位置之外的任何位置都会导致迭代器、指针和引用失效。如果在首尾位置添加元素，迭代器会失效，但指向存在的元素的引用和指针不会失效

- 对于list和forward_list，指向容器的迭代器（包括首前和尾后迭代器）、指针和引用仍有效；

2.在容器中删除元素后
- 对于list和forward_list，指向容器其他位置的迭代器（包括首前和尾后迭代器）、指针和引用仍然有效（它使用了不连续分配的内存，并且它的erase方法也会返回下一个有效的iterator，所以其使用迭代器前移或者使用erase返回的迭代器均可）；

- 对于deque，如果在首尾之外的任何位置删除位置，那么指向被删除元素外其他元素的迭代器、引用或指针也会失效。如果是删除deque的首元素或尾元素，则尾后迭代器也会失效，但其他迭代器、引用和指针不受影响；

- 对于vector和string，指向被删除元素之前元素的迭代器、引用和指针仍有效，之后的全部失效。注意：当我们删除元素时，尾后迭代器总是会失效。（删除当前的iterator会使后面所有元素的iterator都失效。这是因为vetor,deque使用了连续分配的内存，删除一个元素导致后面所有的元素会向前移动一个位置。还好erase方法可以返回下一个有效的iterator，即后面的迭代器全部前移后的位置）

- 对于关联容器(如map, set, multimap,multiset)，删除当前的iterator，仅仅会使当前的iterator失效，只要在erase时，递增当前iterator即可。这是因为map之类的容器，使用了红黑树来实现，插入、删除一个结点不会对其他结点造成影响。