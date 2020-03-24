# 文件操作

## 0. 基础知识

- **C++ 语言中没有输入输出语句**，但是**C++ 标准库（`STL`）中提供了面向对象的输入输出软件包，它就是 I/O 流类库**。
- 两种文件类型
    - **文本文件**：文件以文本的 `ASCII 码` 形式存储在计算机中
    - **二进制文件**：文件以文本的 `二进制形式` 存储在计算机中，用户一般不能直接读懂
- 操作文件的三大类：
    - `ofstream`: **文件输出流，写操作**，头文件包含**`<fstream>`**
    - `ifstream`: **文件输入流，读操作**, 头文件包含 **`<fstream>`**
    - `fstream`: **文件输入输出流类，文件读写**, 头文件包含 **`<fstream>`**

## 1. 文本文件

### 1.1 写文本文件

> 写文本文件步骤如下：
>
> 1. 包含头文件：**`#include <fstream>`**
> 2. 创建流对象：**`std::ofstream ofs;`**
> 3. 打开文件：**`ofs.open("文件路径"，打开方式)`**
>     - 文件的打开方式
>     - `std::ios::app`: 打开一个输入文件，每次写操作前均定位到文件末尾，在文件尾部添加数据
>     - `std::ios::ate`: 打开一个现存文件，用于输入或输出，并立即定位到文件末尾
>     - `std::ios::in`: 打开一个输入文件，对于一个 ofstream 文件，使用该方式打开，可避免删除一个现存文件中现有的内容。
>     - `std::ios::out`: 打开一个文件用于输出。对于所有 ofstream 对象，此模式是隐含的
>     - `std::ios::trunc`: 打开一个文件，如果它已经存在则删除其中原有的内容。如果指定了 `std::ios::out` 但是没有指定 `std::ios::ate`、`std::ios::app`、`std::ios::in` 则隐含为此模式。
>     - `std::ios::binary`: 以二进制方式进行 IO
>     - Note: 文件打开方式可以配合使用，使用 `|` 操作符
> 4. 写数据：**`ofs << "要写入的数据"<< std::endl;`**
> 5. 关闭打开的文件： **`ofs.close();`**

```c++
#include <iostream>
// 1. 包含头文件 注意是 fstream ！！！不是 ofstream
#include <fstream>

int main(int argc, char const *argv[])
{
    //2. 创建操作文件对象
    // ***** 一定要加 std 作用域运算符
    std::ofstream ofs;
    // 3. 指定文件打开方式

    // open-mode: 也可以是 std::ofstream::out
    ofs.open("writedContent.txt",std::ios::out);
    

    // 4.向文件中写入内容
    ofs << "Later is better than never." << std::endl;
    ofs << "Practice makes perfect." << std::endl;

    // 5.关闭文件对象
    ofs.close();

    system("pause");

    return 0;
}
```



