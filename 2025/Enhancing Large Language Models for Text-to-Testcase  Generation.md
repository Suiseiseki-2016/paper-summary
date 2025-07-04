以下是论文的简明总结与text-to-symbol生成的方法建议：
- **核心目标**：解决测试驱动开发（TDD）中“**先写测试后写代码**”的自动化难题，直接从**需求文本生成测试用例**（Text→Testcase），无需已有代码。
- **方法**：  
  - **微调GPT-3.5**：使用16.3万条Java需求-测试用例对训练模型。  
  - **结构化提示设计**：明确指令生成规范化的JUnit测试（含断言模板、避免冗余）。  
- **结果**：  
  - 在5个开源项目中生成7k测试用例，达到**78.5%语法正确率**、**67.09%需求对齐**和**61.7%代码覆盖率**。  
  - 显著优于原生GPT-3.5、Bloom和CodeT5。  
- **关键结论**：  
  微调与提示设计**缺一不可**，直接使用基础ChatGPT效果不佳。
