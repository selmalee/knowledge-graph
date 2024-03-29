# 编译语言和解释语言
编程语言可分为编译语言和解释语言。
 - 编译语言，需要先编译成计算机可以识别的机器码，然后计算机可以直接运行，因此运行速度较解释语言快。因为不同平台能识别的机器码不同，编译器也不同。所以有些高级语言，会先编译成字节码，即虚拟机能识别的中间码，由虚拟机来实现跨平台的兼容。
 - 解释语言，就是在执行虚拟机中一边翻译一边执行，即即时编译（JIT）。

# 编译过程
传统编译性语言的编译过程大致包括4个阶段：
 - 分词分析，将字符串分割成词法单元。
 - 语法分析，将词法单元组合，转换成抽象语法树AST。
 - 遍历分析，对AST进行优化等，增删改查。
 - 代码生成，转换成可执行代码，或中间码。

比如，C/C++、GO 等都是编译型语言，编译后会生成可执行文件。而Java是先编译成中间码，然后由JVM虚拟机一边解析一边执行。JavaScript则是一种解释语言，由虚拟机一边解析一边执行。

在现在JavaScript引擎中，大致的执行过程是：
 - parse，分词，语法分析，编译器将源代码编译成抽象语法树。
 - Ignition解释器根据 AST 生成字节码，并解释执行字节码。
  > 字节码就是介于 AST 和机器码之间的一种代码。但是与特定类型的机器码无关，字节码需要通过解释器将其转换为机器码后才能执行。
 - JIT工具，分析这些字节码并将其中的部分字节码转换成本地代码。在 lgnition 执行字节码的过程中，如果发现有热点代码（HotSpot），比如一段代码被重复执行多次，这种就称为热点代码，那么后台的编译器 TurboFan 就会把该段热点的字节码编译为高校的机器码，然后当再次执行这段被优化的代码时，只需要执行编译后的机器码就可以了，这样就大大提升了代码的执行效率。
 
这个过程和Java的编译和执行过程很像，只是Java语言中这两个阶段是分开执行的，编译阶段可以尽可能的生成高效的字节码，这样在执行阶段可以执行的更快。而对于Javascript而言，它的编译阶段是在网页和JavaScript文件下载后同执行阶段一起在网页的加载和渲染过程中来实施的，所以对于JavaScript引擎执行过程中的每个阶段时间越少越好。

详细看[V8工作原理](https://www.cnblogs.com/bala/p/12205485.html)和[javascript引擎工作原理的初步了解](https://segmentfault.com/a/1190000014242281)。
 ![编译器和解释器](https://img2018.cnblogs.com/common/945149/202001/945149-20200116135536725-1813073475.png)
 ![V8引擎](https://img2018.cnblogs.com/common/945149/202001/945149-20200116140226667-864961384.png)
