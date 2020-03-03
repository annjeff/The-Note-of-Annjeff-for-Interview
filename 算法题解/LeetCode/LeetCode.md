# LeetCode

## 21. 合并两个有序链表

- 难度：简单
- 题目描述：
    
- 将两个有序链表合并为一个有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。
    
- 示例：

    > 输入：1->2->4，1->3->4
    >
    > 输出： 1->1->2->3->4->4

- 我的题解：

    ```c++
    
    ```


- 经典题解

    ```C++
    
    ```



## 53. 最大子序和

- 难度：简单
- 题目描述：
    
- 给定一个整数数组 `nums` ,找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
    
- 示例：

    > 输入：[-2, 1, -3, 4, -1, 2, 1, -5, 4]
    >
    > 输出：6
    >
    > 解释：连续子数组 [4, -1, 2, 1] 的和最大，为 6。

- 进阶：

    - 如果你已经实现复杂度为 O(*n*) 的解法，尝试使用更为精妙的分治法求解。

- 我的题解：

    ```C++
    int maxSubArray(vector<int>& nums) {
            
        }
    ```

- 经典题解：

    ```C++
    
    ```


## 58 最后一个单词的长度

- 难度：简单

- 题目描述：

    - 给定一个仅包含大小写字母和空格 `' '` 的字符串 `s`，返回其最后一个单词的长度。
    - 如果字符串从左向右滚动显示，那么最后一个单词就是最后出现的单词。
    - 如果不存在最后一个单词，请返回 0 。
    - **说明：**一个单词是指仅由字母组成、不包含任何空格的 **最大子字符串**。

- 示例：

    > 输入："Hello World"
    >
    > 输出：5

- 我的题解

    ```C++
    int lengthOfLastWord(string s) {
        int len = 0;
        int LEN_OF_S = s.size();
        if (LEN_OF_S == 0)
            return 0;
        for (int i = LEN_OF_S - 1; i >= 0; i--)
        {
            if ((s.at(i) >= 'a' && s.at(i) <= 'z') || (s.at(i) >= 'A' && s.at(i) <= 'Z'))
            {
                len++;
            }else if (s.at(i) == ' ')
            {
                if (len == 0)
                    continue;
                break;   
            }else{
                len = 0;
                break;
            }
    
        }
        return len;
    }
    ```

- 经典题解

    ```C++
    int lengthOfLastWord(string s) {
        // find_if 属于 algothrim 头文件
        // isalpha 函数判断当前字符是否为 字母，字母返回非 0
        auto first = find_if(s.rbegin(),s.rend(), ::isalpha);
        auto last = find_if_not(first, s.rend(), ::isalpha);
        // distance 函数计算两个迭代器之间的距离
        return distance(first, last);
    
    }
    ```

- 本题总结
    - 注意：空串
    - 注意: "abc "，最后一个单词是 "abc"，最后的空格要剔除
    - **使用 -- 时，最好不要使用 `size_t`**容易出现溢出

