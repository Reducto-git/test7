# Java07-异常处理

## **Task1.异常Exception和错误Error**

### 1Exception 和 Error 的区别

- **Exception**：表示程序在运行时可能会发生的可控的错误情况，通常可以通过代码进行处理。
- **Error**：表示更严重的问题，通常是程序无法控制的，例如虚拟机错误或系统资源耗尽，程序一般无法或不应处理这些错误。

| Throwable |          类型          | 发生原因                                             | 处理态度                                           |
| :-------: | :--------------------: | :--------------------------------------------------- | -------------------------------------------------- |
| Exception |      IOException       | 读取或写入文件时发生错误，例如文件不存在或没有权限。 | 通常可以捕获并进行处理，例如提示用户选择其他文件。 |
| Exception |  NullPointerException  | 尝试访问或操作一个为null的对象。                     | 可以通过检查对象是否为null来避免此异常。           |
| Exception | ClassNotFoundException | 试图加载一个不存在的类。                             | 可以通过确保类在类路径中存在来避免此异常。         |
|   Error   |    OutOfMemoryError    | JVM无法分配更多内存。                                | 通常不应处理，应查找程序内存泄漏的根本原因。       |
|   Error   |   StackOverflowError   | 递归调用过深，导致堆栈溢出。                         | 通常不应处理，应检查递归的终止条件。               |

### 2Checked 和 Unchecked 的区别

- **Checked Exception**：在编译时强制要求捕获或声明，程序员必须处理这些异常。适用于可以预见的异常情况。
- **Unchecked Exception**：在运行时抛出，编译器不强制要求捕获，通常用于程序逻辑错误，应该通过代码优化来避免。

| 异常      | 示例                 | 发生原因                   | 处理态度                     |
| --------- | -------------------- | -------------------------- | ---------------------------- |
| Checked   | IOException          | 输入输出操作失败。         | 必须在代码中显式处理。       |
| Checked   | SQLException         | 数据库操作失败。           | 必须在代码中显式处理。       |
| Unchecked | NullPointerException | 访问空对象。               | 可以选择不处理，但应当避免。 |
| Unchecked | ArithmeticException  | 算术计算错误，例如除以零。 | 可以选择不处理，但应当避免。 |

## **Task2.异常的处理**

### 3.程序运行的可能流程

程序开始，创建`BankAccount`对象，随机生成一个初始余额（假设在0到200之间）。

使用`account.getBalance()`方法打印当前余额。

调用`account.withdraw(150.0)`

- 如果生成的随机余额大于或等于150.0：

  余额足够，减去150.0，余额更新。

  程序打印“取款成功。”。

- 如果生成的随机余额小于150.0：

  抛出`InsufficientFundsException`，异常被捕获到`catch`块中。

  程序打印“错误: 余额不足，无法取款。当前余额: X”（X为当前余额）。

程序继续执行，打印“程序结束”。

### 4.文件读取与数据处理

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.FileNotFoundException;
import java.io.IOException;

class EmptyFileException extends Exception {
    public EmptyFileException(String message) {
        super(message);
    }
}

public class FileReadExample {
    public static void main(String[] args) {
        String fileName = "data.txt";
        double sum = 0;
        int count = 0;

        try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
            String line;
            boolean isEmpty = true;

            while ((line = br.readLine()) != null) {
                isEmpty = false;
                try {
                    int number = Integer.parseInt(line);
                    sum += number;
                    count++;
                } catch (NumberFormatException e) {
                    System.err.println("格式错误: 读取到的内容 '" + line + "' 不是有效的整数。");
                }
            }

            if (isEmpty) {
                throw new EmptyFileException("文件为空，无法计算平均值。");
            }

            if (count == 0) {
                System.out.println("文件中没有有效的整数，无法计算平均值。");
            } else {
                double average = sum / count;
                System.out.println("平均值: " + average);
            }

        } catch (FileNotFoundException e) {
            System.err.println("错误: 文件未找到 - " + e.getMessage());
        } catch (EmptyFileException e) {
            System.err.println("错误: " + e.getMessage());
        } catch (IOException e) {
            System.err.println("读取文件时发生错误: " + e.getMessage());
        } finally {
            System.out.println("文件处理完成。");
        }
    }
}

```

