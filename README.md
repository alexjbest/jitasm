# jitasm
Automatically exported from code.google.com/p/jitasm


Introduction
============
[En]
jitasm is C++ library for runtime code generation of x86/x64. You can write the code like a inline assembler.

[Ja]
jitasmはx86/x64のコードを動的に生成するためのC++ライブラリです。
インラインアセンブラのような直感的な記述で動的なコード生成・実行を行うことができます。

License
=======
jitasm is open source software. You can distribute it under the terms of
the new BSD license.

Compiler
========
 - VisualC++ 2005 or later
 - GCC 4.1 or later

How to use
==========
You just copy and include jitasm.h. Please see the sample code in
"samples" directory.

Features
========

[En]

    Header only library
    Support for x86, x64, mmx, sse, sse2, sse3, ssse3, sse4.1, sse4.2, avx, fma, xop, fma4
    Automatic code generation of prolog and epilog according to function calling convention
    Register allocation
    Support for Windows, Linux, FreeBSD, Mac
    [Ja]
    単一のヘッダファイルのみのライブラリ
    x86, x64, mmx, sse, sse2, sse3, ssse3, sse4.1, sse4.2, avx, fma, xop, fma4サポート
    関数呼び出し規約に従ったプロローグ・エピローグの自動生成
    レジスタアロケーション
    Windows, Linux, FreeBSD, Macサポート

Example
```
// int plus(int a, int b)


// {


//   return a + b;


// }


struct Plus : public jitasm::function<int, Plus, int, int>


{


  Result main(Reg32 a, Reg32 b)


  {


    add(a, b);


    return a;


  }


};





// Generate plus function and call.


Plus plus;


int c = plus(1, 2);
```




Build
=====

jitasm is header-only library. You just copy and include jitasm.h to use. Please build when you want to try jitasm sample or test.

jitasm supports following compilers: * Microsoft Visual C++ 2005 or later * GCC 4.1 or later
Microsoft Visual C++

To build samples, please open samples/samples.sln. The pixel_calc sample needs Boost C++ Library.

To build test, please open test/test.sln. It needs Microsoft Visual C++ 2012 and yasm.
GCC

jitasm needs some compiler options: * -fno-operator-names * -march=i686 for 32bit * -mmmx/-msse/-msse2 when you use __m64/__m128/__m128d/__m128i as jitasm function argument or result.

To build samples, please make in each subdirectories. The pixel_calc sample doe's not support gcc.
Assembler

```
include "stdio.h"
include "jitasm.h"

// int add1(int arg1) // { // return arg1 + 1; // } struct add1 : jitasm::function { Result main(Addr arg1) { // arg1 refers to address of first argument value on stack. mov(ecx, dword_ptr[arg1]); add(ecx, 1); return ecx; } };

int main() { // Make function instance add1 f;

// Runtime code genaration and run int result = f(99);

printf("Result : %d\n", result);

return 0; } ```

This is a small example how to use jitasm as assembler. add1 is JIT assembled function class and call it in main(). add1 class is derived from jitasm::function template class. First template argument is result type of function. Second one is derived class itself and third one is argument type of function. If you want to pass more arguments, you can add template parameters until 10th argument.

push ebp mov ebp,esp mov eax,dword ptr [ebp+8] add eax,1 mov eax,ecx pop ebp ret

This is a generated code as add1 function. jitasm generates function prolog and epilog automatically. If you don't need prolog and epilog, you can write naked_main. ``` struct add1 : jitasm::function { void naked_main() { mov(eax, dword_ptr[esp + 4]); add(eax, 1); ret(); } };

mov eax,dword ptr [esp+4] add eax,1 ret ```
