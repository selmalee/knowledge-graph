# webassembly

## 1 概念

[MDN文档](https://developer.mozilla.org/zh-CN/docs/WebAssembly)

[文档](http://webassembly.org.cn/)

WebAssembly 或者 wasm 是一个可移植、体积小、加载快并且兼容 Web 的全新格式。为诸如C、C++和Rust等低级源语言提供一个高效的编译目标，为客户端app提供了一种在网络平台以接近本地速度的方式运行多种语言编写的代码的方式。



使用方式：

- 使用Emscripten移植一个C/C++应用程序。
- 直接在汇编层，编写或生成WebAssembly代码。
- 编写Rust程序，将WebAssembly作为它的输出。



## 2 emscripten

[Emscripten文档](https://emcc.zcopy.site/docs/)

![img](https://mdn.mozillademos.org/files/14647/emscripten-diagram.png)

Emscripten工具能够将一段C/C++代码，编译出：

- 一个.wasm模块

- 用来加载和运行该模块的JavaScript”胶水“代码

  ``` js
  fetch('simple.wasm')
    .then(res =>
      res.arrayBuffer()
    ).then(bytes =>
      WebAssembly.instantiate(bytes, importObject)
    ).then(results => {
      results.instance.exports.exported_func();
    });
  ```

- 一个用来展示代码运行结果的HTML文档



### 2.1 原理

[WebAssembly 系列（四）WebAssembly 工作原理](https://www.w3ctech.com/topic/2024)

先了解下LLVM和Clang：

#### 2.1.1 LLVM

传统的编译器架构：

![](https://img-blog.csdnimg.cn/20200319163136739.png)

- Frontend前端：词法分析、语法分析、语义分析、生成中间代码
  - 词法分析：对构成源程序的字符串进行扫描和分解，识别出一个个单词
  - 语法分析：在词法分析的基础上，根据语言的语法规则，把单词符号串分解成各类语法单位，确定整个输入串是否构成语法上正确的“程序”。
  - 语义分析：对语法分析所识别出的各类语法范畴，分析其含义（变量是否定义、类型是否正确等），并进行初步翻译（产生中间代码）。
- Optimizer优化器：中间代码优化（循环优化、删除无用代码等等）
- Backend后端：生成目标代码。如目标代码是绝对指令代码（机器码），则这种目标代码可立即执行。如果目标代码是汇编指令代码，则需汇编器汇编之后（生成机器码）才能运行。

 LLVM ：

![](https://img-blog.csdnimg.cn/20200319165247343.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NzU0NzQ3,size_16,color_FFFFFF,t_70)

![img](https://huzidaha.github.io/images-store/201703/19-1.png)

不同的前端后端使用统一的中间代码LLVM Intermediate Representation (LLVM IR)
如果需要支持一种新的编程语言，那么只需要实现一个新的前端
如果需要支持一种新的硬件设备，那么只需要实现一个新的后端
优化阶段是一个通用的阶段，它针对的是统一的LLVM IR，不论是支持新的编程语言，还是支持新的硬件设备，都不需要对优化阶段做修改

总结： GCC的前端和后端没分得太开，前端后端耦合在了一起。所以GCC为了支持一门新的语言，或者为了支持一个新的目标平台，就 变得特别困难。LLVM现在被作为实现各种静态和运行时编译语言的通用基础结构(GCC家族、Java、.NET、Python、Ruby、Scheme、Haskell、D等)。



#### 2.1.2 Clang

Clang是LLVM的一个子项目，基于LLVM架构的C/C++/Objective-C编译器前端.

clang编译过程：源代码（c/c++）经过clang--> 中间代码(经过一系列的优化，优化用的是Pass) --> 机器码.

LLVM整体架构：前端用的是clang，广义的LLVM是指整个LLVM架构，一般狭义的LLVM指的是LLVM后端（包含代码优化和目标代码生成



#### 2.1.3 emscripten的原理

Emscripten是一个WebAssembly编译器工具链：

- 将 C 和 C++ 代码或任何其他使用 LLVM 的语言编译成 WebAssembly，并在 Web、Node.js 或其他 wasm 运行时上运行它。
- 将其他语言的 C/C++ 运行时编译成 WebAssembly，然后以间接方式运行其他语言的代码（例如，Python 和 Lua 已经这样做了

具体来说，就是C/C++等语言，经过 clang 前端变成 LLVM 中间代码（IR），再从LLVM IR到wasm。然后浏览器把 WebAssembly 下载下来，然后先经过 WebAssembly 模块，再到目标机器的汇编代码，再到机器码（x86/ARM等）。

![img](https://huzidaha.github.io/images-store/201703/19-2.png)



## 3 asm.js

[asm.js 和 Emscripten 入门教程](https://www.ruanyifeng.com/blog/2017/09/asmjs_emscripten.html)

C / C++ 编译成 JS 有两个最大的困难。

> - C / C++ 是静态类型语言，而 JS 是动态类型语言。
> - C / C++ 是手动内存管理，而 JS 依靠垃圾回收机制。

**asm.js 就是为了解决这两个问题而设计的：它的变量一律都是静态类型，并且取消垃圾回收机制。**

一旦 JavaScript 引擎发现运行的是 asm.js，就知道这是经过优化的代码，可以跳过语法分析这一步，直接转成汇编语言。另外，浏览器还会调用 WebGL 通过 GPU 执行 asm.js，即 asm.js 的执行引擎与普通的 JavaScript 脚本不同。这些都是 asm.js 运行较快的原因。据称，asm.js 在浏览器里的运行速度，大约是原生代码的50%左右。

![img](https://www.ruanyifeng.com/blogimg/asset/2017/bg2017090302.jpg)



### 3.1 与Webassembly比较

- 都可以使用emscrtipten编译生成
- asm.js 是文本，WebAssembly 是二进制字节码，因此运行速度更快、体积更小。
- 所有浏览器都支持 asm.js，不会有兼容性问题。



## 4 解析和运行

![img](https://user-gold-cdn.xitu.io/2018/5/18/16370ede3db4ac97?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

V8解析js的过程：

1. 首先，源码先把字符串转换为记号以便于解析，之后生成一个语法抽象树（语法分析）。
2. 然后，生成中间码（未优化）。TurboFan监视运行得慢的代码，引起性能瓶颈的地方及热点（内存使用过高的地方）以便优化它们。它把以上监视得到的代码推向后端，即优化过的即时编译器，该编译器把消耗大量 CPU 资源的函数转换为性能更优的代码。（中间码优化）
3. 生成机器码。

wasm 在编译阶段就已经通过了代码优化。总之，解析也不需要了。你拥有优化后的二进制代码可以直接插入到后端（即时编译器）并生成机器码。由于跳过了编译过程中的不少步骤，这使得 wasm 的执行更加高效。

