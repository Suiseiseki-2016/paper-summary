# Core Engine

每次选择一个 Ready 的 state，然后进行符号执行，将其命名为 busy，如果结束了就跳转到 termination。

但是如果其存在分叉，则将这个状态分裂成若干个 ready 的状态。这里可以并行。

# Native Execution Module

通过抽象模拟 CPU、内存、操作系统来实现符号执行。

1. CPU：直接遵循 ISA 规定，将每一个寄存器的内容都作为符号来模拟。
2. Memory：需要根据不同的场景进行不同的符号模拟，包括完全模拟内存的符号。
3. OS：模拟系统的调用，以及涉及到的程序的地址。系统调用必须要采用符号化的输入。

# Ethereum Execution Module

所有数据的符号化需要维护哪一个函数需要在合约中运行以及这些函数的参数。

价格和数据都应当是符号化的。

甚至可以模拟整个网络之间的交互。

# Auxiliary Modules

对于一些传统的方式，也可以使用 SMT 求解器，以及 Monticore 提供了可以增加处理方式的接口。
