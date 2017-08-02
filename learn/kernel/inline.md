
		[第十五章：内联弊病](http://androidxref.com/kernel_3.18/xref/Documentation/zh_CN/CodingStyle#569)
        
有一个常见的误解是内联函数是gcc提供的可以让代码运行更快的一个选项。虽然使用内联
函数有时候是恰当的（比如作为一种替代宏的方式，请看第十二章），不过很多情况下不是
这样。inline关键字的过度使用会使内核变大，从而使整个系统运行速度变慢。因为大内核
会占用更多的指令高速缓存（译注：一级缓存通常是指令缓存和数据缓存分开的）而且会导
致pagecache的可用内存减少。想象一下，一次pagecache未命中就会导致一次磁盘寻址，将
耗时5毫秒。5毫秒的时间内CPU能执行很多很多指令。

一个基本的原则是如果一个函数有3行以上，就不要把它变成内联函数。这个原则的一个例
外是，如果你知道某个参数是一个编译时常量，而且因为这个常量你确定编译器在编译时能
优化掉你的函数的大部分代码，那仍然可以给它加上inline关键字。kmalloc()内联函数就
是一个很好的例子。

人们经常主张给static的而且只用了一次的函数加上inline，如此不会有任何损失，因为没
有什么好权衡的。虽然从技术上说这是正确的，但是实际上这种情况下即使不加inline gcc
也可以自动使其内联。而且其他用户可能会要求移除inline，由此而来的争论会抵消inline
自身的潜在价值，得不偿失。



Inline function is the optimization technique used by the compilers. One can simply prepend inline keyword to function prototype to make a function inline. Inline function instruct compiler to insert complete body of the function wherever that function got used in code.

## Advantages :- 
1) It does not require function calling overhead.
2) It also save overhead of variables push/pop on the stack, while function calling.
3) It also save overhead of return call from a function.
4) It increases locality of reference by utilizing instruction cache.
5) After in-lining compiler can also apply intraprocedural optmization if specified. This is the most important one, in this way compiler can now focus on dead code elimination, can give more stress on branch prediction, induction variable elimination etc..

## Disadvantages :-
1) May increase function size so that it may not fit on the cache, causing lots of cahce miss.
2) After in-lining function if variables number which are going to use register increases than they may create overhead on register variable resource utilization.
3) It may cause compilation overhead as if some body changes code inside inline function than all calling location will also be compiled.
4) If used in header file, it will make your header file size large and may also make it unreadable.
5) If somebody used too many inline function resultant in a larger code size than it may cause thrashing in memory. More and more number of page fault bringing down your program performance.
6) Its not useful for embeded system where large binary size is not preferred at all due to memory size constraints.

## Performance : -
Now covering the topic which most the people are interested in the "Performance".
In most of the cases Inline function boost performance if used cautiously as it saves lots of overhead as discussed in our Advantages section above but as we have also discussed its disadvantages one need to be very cautious while using them. Today's modern compiler inline functions automatically, so no need to specify explicitly in most of the cases. Although placing inline keyword only gives compiler a hint that this function can be optimized by doing in-lining, its ultimately compiler decision to make it inline. Though there are ways to instruct compiler too, for making a function call inline like one can use __forceinline to instruct compiler to inline a function while working with microsoft visual c++. I suggest not to use this keyword until you are very sure about performance gain. Making a function inline may or may not give you performance boost, it all depends on your code flows too. Don't expect a magical performance boost by prepending inline keyword before a function to your code as most of the compiler nowadays does that automatically.

As we have seen inline function serves in terms of performacen but one has to use it with extreme cautions.

I have prepared a few guidelines for its use.
## Uses Guidelines :-
1) Always use inline function when your are sure it will give performance.
2) Always prefer inline function over macros.
3) Don't inline function with larger code size, one should always inline small code size function to get performance.
4) If you want to inline a function in class, then prefer to use inkine keyword outside the class with the function definition.
5) In c++, by default member function declared and defined within class get linlined. So no use to specify for such cases.
6) Your function will not be inlined in case there is differences between exception handling model. Like if caller function follows c++ structure handling and your inline function follows structured exception handling.
7) For recursive function most of the compiler would not do in-lining but microsoft visual c++ compiler provides a special pragma for it i.e. pragma inline_recursion(on) and once can also control its limit with pragma inline_depth.
8) If the function is virtual and its called virtually then it would not be inlined. So take care for such cases, same hold true for the use of function pointers.
