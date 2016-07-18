# lua programming

Lua 是大小写敏感的. 注释:单行注释:-- 多行注释:--[[ --]]

Lua 是动态类型语言,变量不要类型定义。Lua 中有 8 个基本类型分别为:nil、boolean、 number、string、userdata、function、thread 和 table。	函数 type 可以测试给定变量或者值 的类型。
	
	print(type("Hello world"))	--> string























当变量个数和值的个数不一致时,Lua 会一直以变量个数为基础采取以下策略:




### 局部变量
使用 local 创建一个局部变量,与全局变量不同,局部变量只在被声明的那个代码块 内有效。代码块:指一个控制结构内,一个函数体,或者一个 chunk(变量被声明的那 个文件或者文本串)。
### break 和 return 语句












### table

table 是 Lua 中唯一的数据结构,其他语言所提供的数据结构,如:arrays、records、 lists、queues、sets 等,Lua 都是通过 table 来实现,并且在 lua 中 table 很好的实现了这 些数据结构。


在 lua 中通过整数下标访问 table 中元素,即是数组。并且数组大小不固定,可动态增长。通常我们初始化数组时,就间接地定义了数组的大小,例如




习惯上,Lua 的下标从 1 开始。Lua 的标准库遵循此惯例,因此你的数组下标 必须也是从 1 开始,才可以使用标准库的函数。




Lua 中有两种表示矩阵的方法,一是“数组的数组”

	mt = {} -- create the matrix for i=1,N do
	for i=1,N do








	Lua 中用 tables 很容易实现链表,每一个节点是一个 table,指针是这个表的一个域 (field),并且指向另一个节点(table)。例如,要实现一个只有两个域:值和指针的基 本链表,代码如下:





### 集合和包

	


	还可以使用辅助函数更加清晰的构造集合:




对于每一个算术运算符,metatable 都有对应的域名与其对应,除了__add、__mul 外,还有__sub(减)、__div(除)、__unm(负)、__pow(幂),我们也可以定义__concat 定义 连接行为


Lua 选择 metamethod 的原则:如果第一个参数存在带有__add 域的 metatable,Lua 使用它作为 metamethod,和第二个参数无关;否则第二个参数存在带有__add 域的 metatable,Lua 使用它作为 metamethod 否则报 错。

Metatables 也允许我们使用 metamethods:__eq(等于),__lt(小于),和__le(小于 等于)给关系运算符赋予特殊的含义。对剩下的三个关系运算符没有专门的 metamethod, 因为Lua将a ~= b转换为not (a == b);a > b转换为b < a;a >= b转换为 b <= a。

如果你试图比较两个带有不同 metamethods 的 对象,Lua 也将抛出错误。



### Table库
Table 库定义了两个函数操纵 array 的大小:getn,返回 array 的大小;setn,设置 array 的大小



### String 库


### IO 库
I/O 库为文件操作提供两种模式。简单模式(simple model)拥有一个当前输入文件 和一个当前输出文件,并且提供针对这些文件相关的操作。完全模式(complete model) 使用外部的文件句柄来实现。它以一种面对对象的形式,将所有的文件操作定义为文件 句柄的方法。简单模式在做一些简单的文件操作时较为合适。在本书的前面部分我们一 直都在使用它。但是在进行一些高级的文件操作的时候,简单模式就显得力不从心。例 如同时读取多个文件这样的操作,使用完全模式则较为合适。I/O 库的所有函数都放在 表(table)io 中。


io.input 和 io.output 函数来改变当前文件。例如 io.input(filename)就是打开给定文件(以读模式),并将其设置为当前输入文件。接下来 所有的输入都来自于该文,直到再次使用 io.input。io.output 函数。类似于 io.input。一旦 产生错误两个函数都会产生错误。如果你想直接控制错误必须使用完全模式中 io.read 函 数。


read 函数从当前输入文件读取串,由它的参数控制读取的内容:

	"*all" 读取整个文件 
	"*line" 读取下一行 
	"*number"从串中转换出一个数值 
	num 读取 num 个字符到串



