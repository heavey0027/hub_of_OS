
#### 第3周：内核启动流程（4月11日–4月17日）

**需求分析**

- **目标**：实现可启动内核，在QEMU中输出“Hello, OS!”。
- **功能需求**：
  - 初始化CPU为监督者模式。
  - 设置基础控制台输出。
- **非功能需求**：确保内核可靠启动，无崩溃。

**系统设计**

- **组件**：

  - 启动代码：汇编代码，设置堆栈并跳转到C代码。
  - 控制台驱动：RISC-V Virt机器的简单UART驱动。

- **图示**：

  ```
  [Boot.S] -> [设置堆栈，监督者模式] -> [kernel_main] -> [UART输出]
  ```

**实现细节**

- 编写`boot.S`初始化堆栈并跳转到`kernel_main`。

- 实现简单的UART驱动以支持控制台输出。

- **关键代码**：

  ```assembly
  # boot.S
  .section .text
  .global _start
  _start:
      la sp, stack_top  # 设置堆栈指针
      call kernel_main  # 跳转到C代码
  stack_top:
      .space 4096
  ```

  ```c
  // uart.c
  void uart_putc(char c) {
      volatile char *uart = (volatile char *)0x10000000; // UART基地址
      *uart = c;
  }
  void kernel_main() {
      const char *msg = "Hello, OS!\n";
      while (*msg) uart_putc(*msg++);
      while (1);
  }
  ```

**测试结果**

- **测试用例**：在QEMU中运行内核，验证“Hello, OS!”输出。
- **结果**：成功在QEMU控制台输出“Hello, OS!”。
- **截图**：QEMU终端显示“Hello, OS!”。

**总结与反思**

- **成果**：成功启动内核并显示输出。
- **挑战**：理解RISC-V启动序列和UART寻址较复杂，通过RISC-V文档解决。
- **改进建议**：下周实现基础内存管理。