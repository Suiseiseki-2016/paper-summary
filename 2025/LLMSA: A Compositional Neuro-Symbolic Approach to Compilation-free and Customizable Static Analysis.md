LLMSA（Compositional Neuro-Symbolic Approach for Compilation-free and Customizable Static Analysis），用于解决编译依赖和定制化的问题。

采用 Datalog 语言分析，将问题分解成更多的小问题。通过针对较小片段的分析，可以获得更加准确的结果。

将一个 slice 划分成 symbolic, neural 和 intensional 三个部分。

通过形式化的格式来将 neural link 通过 datadependence 的方式加入到整个静态分析的过程。

整个 neurol link 是可以并行处理的。

采用增量的方法，每次增加最后一个，这样可以提高运行效率。

