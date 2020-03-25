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

### 1.2 读文本文件

> 读文本文件步骤如下：
>
> 1. 包含头文件：**`#include <fstream>`**
> 2. 创建流对象：**`ifstream ifs;`**
> 3. 打开文件并判断文件是否打开成功：**`ifs.open("文件路径"，打开方式);if(ifs.is_open()){打开成功}`**
> 4. 读取数据
> 5. 关闭文件：**`ifs.close();`**

```c++
#include <iostream>

// s1.包含头文件
#include <fstream>

int main(int argc, char const *argv[])
{
    //s2.创建流对象 ！！！勿忘 std::作用域
    std::ifstream ifs;

    //s3.打开文件，并判断文件是否打开成功
    ifs.open("writedContent.txt", std::ios::in);
    if (!ifs.is_open())
    {
        std::cout << "Failed to open File." << std::endl;
        return -1;
    }

    //s4. 读取文件，有四种方式
    // 方式一：每当遇到空格就开始下一次循环
    // char buf[1024] = {0};
    // while(ifs >> buf)
    // {
    //     std::cout << buf << std::endl;
    // }

    // 方式二：可以完成按行读取
    // char buf[1024] = {0};
    // while (ifs.getline(buf, sizeof(buf)))
    // {
    //     std::cout << buf << std::endl;
    // }

    // 方式三：使用全局的 getline 函数
    // std::string buf;
    // while (getline(ifs, buf))
    // {
    //     std::cout << buf << std::endl;
    // }

    // 方式四：不推荐 一个字符一个字符的输出
    char c;
    while ((c = ifs.get()) != EOF)
    {
        std::cout << c << std::endl;
    }
    
    
    //s5. 关闭文件
    ifs.close();

    system("pause");
    return 0;
}
```

## 2. 二进制文件

### 2.1 写二进制文件

> 使用二进制的方式向文件中写入内容，主要**使用流对象调用成员函数 write**
>
> write 函数原型：`ostream& write(const char* buffer, int len)`
>
> 参数解释：@param1: 字符指针 buffer 指向内存中一段存储空间（需要写入文件的数据的首地址）；@param2: 指出写入的字节数

```c++
#include <iostream>
#include <cstring>

// s1. 包含头文件
#include <fstream>

class Student{
public:
    Student(){}
    Student(char name[], int age)
    {
        strcpy(m_name, name);
        m_age = age;
    }
    void printInfo()
    {
        std::cout <<"Name: "<<m_name << " ;Age: " <<m_age <<std::endl;
    }
private:
    char m_name[20];
    int m_age;
};

int main(int argc, char const *argv[])
{
    // s2. 创建输出流对象
    std::ofstream ofs;

    // s3. 打开输出文件，指定打开方式
    ofs.open("writedBinaryContent.txt", std::ofstream::out | std::ofstream::binary);

    Student stu[2] = {{"annjeff",25},{"angle",27}};
    // s4. 写文件
    ofs.write(reinterpret_cast<char*>(stu), sizeof(stu));

    // s5. 关闭文件
    ofs.close();


    system("pause");
    return 0;
}
```

> **Note:** 在类中保存姓名，尽量使用 char 数组，避免使用 std::string，保存二进制会有问题

### 2.2 读二进制文件

> 读取二进制方式存储的文件，主要**使用流对象调用成员函数 read**
>
> read 函数的原型：`ifstream& read(char *buffer, int len)`
>
> 参数解释：@para1: 读取内容的存储空间；@para2: 读写的字节数

```c++
#include <iostream>
#include <cstring>

// s1. 包含头文件
#include <fstream>

class Student{
public:
    Student(){}
    Student(char name[], int age)
    {
        strcpy(m_name, name);
        m_age = age;
    }
    void printInfo()
    {
        std::cout <<"Name: "<<m_name << " Age: " <<m_age <<std::endl;
    }
private:
    char m_name[20];
    int m_age;
};

int main(int argc, char const *argv[])
{
    // s2.创建流对象 并打开要读取的文件
    std::ifstream ifs("writedBinaryContent.txt",std::ifstream::in | std::ifstream::binary);

    // s3. 判断文件是否打开成功
    if (!ifs.is_open())
    {
        std::cout << "Failed to open the file." << std::endl;
        return -1;
    }
    // s4.读取文件内容
    Student stu[2];
    ifs.read(reinterpret_cast<char*>(stu), sizeof(stu));

    // s5. 显示读取的内容
    for (size_t i = 0; i < 2; i++)
    {
        stu[i].printInfo();
    }
    
    // s6.关闭打开的文件
    ifs.close();

    system("pause");
    return 0;
}
```



