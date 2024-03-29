# JVM设计及原理

## 虚拟机概述

### 1.兼容性实现

JAVA是`write once,run anywhere`，但是Java是高级语言，对于CPU来说并不能识别，因此需要进行转换和兼容，才能实现上述的目标，在这样的情况下就出现了中间语言(ML)和虚拟机(JVM)就出现了，前者负责将java转换为字节码，后者实现各个平台的兼容运行

常见的兼容底层系统的方式分为两种

* 通过编译器实现
  * C/C++ 为了实现兼容性，通过针对不同的硬件平台和操作系统，开发不同的编译器，编译器将相同的代码编译成与平台匹配的机器指令，从而实现编程语言的兼容性
  * 但是在涉及到底层或者系统调用的时候，还是需要修改程序
* 通过中间语言实现
  * Java、C#都是采用这种模式
  * 在被编译后，生成中间语言(ML)，中间语言指令有虚拟机负责解释和执行。虚拟机在运行期将中间语言实时编译成与特定底层平台匹配的机器指令并执行。这种情况下，无论在任何平台上面，源代码被编译成的中间语言指令都是相同的，其兼容性由虚拟机负责完成

### 