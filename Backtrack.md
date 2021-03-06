# 电话号码的字母组合
给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。  
给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。  
![picture](https://assets.leetcode-cn.com/aliyun-lc-upload/original_images/17_telephone_keypad.png)  
示例：
```
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```
说明:  
尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。
## 回溯法
回溯是一种通过穷举所有可能情况来找到所有解的算法。如果一个候选解最后被发现并不是可行解，回溯算法会舍弃它，并在前面的一些步骤做出一些修改，并重新尝试找到可行解。
```c++
class Solution {
public:
    vector<string>result;
    map<string, string>phone = {
        {"2","abc"},{"3","def"},{"4","ghi"},{"5","jkl"},
        {"6","mno"},{"7","pqrs"},{"8","tuv"},{"9","wxyz"}
    };
    void backtrack(string combination, string next_digits) {
        if (next_digits == "") {
            result.push_back(combination);
        }
        else {
            string digits = next_digits.substr(0,1);
            string letters = phone[digits];
            for (int i=0; i<letters.length(); i++) {
                string letter = letters.substr(i, 1);
                backtrack(combination + letter, next_digits.substr(1));
            }
        }
        
    }
    
    vector<string> letterCombinations(string digits) {
        if (digits != "") {
            backtrack("", digits);
        }
        return result;
    }
};
```
复杂度分析

- 时间复杂度: O(3^N * 4^M), 其中 N 是输入数字中对应 3 个字母的数目（比方说 2，3，4，5，6，8), M 是输入数字中对应 4 个字母的数目（比方说 7，9）,N+M 是输入数字的总数。  

- 空间复杂度: O(3^N * 4^M), 这是因为需要保存(3^N * 4^M)个结果。
---
# 括号生成
给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。  
例如，给出 n = 3，生成结果为：
```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```
## 暴力法
思路：用递归将每一种可能都列出来再判断是否合法
```c++
vector<string> result;
bool isvaild(string s) {
    int balance = 0;
    for(auto c : s) {
        if (c == '(') {
            balance++;
        }
        else
            balance--;
        if (balance < 0) {
            return false;
        }
    }
    return balance == 0;
}

void generateAll(string s, int n) {
    if (s.length() == 2 * n) {
        if (isvaild(s)) {
            result.push_back(s);
        }
    }
    else {
        string left = s + '(';
        generateAll(left, n);
        string right = s + ')';
        generateAll(right, n);
    }
}

vector<string> generateParenthesis(int n) {
    
    if (n <= 0) {
        return result;
    }
    
    string s;
    generateAll(s, n);
    
    return result;
}
```
复杂度分析：  
- 时间复杂度：O(2^2n * n),对于 2^2n 个序列中的每一个，我们用于建立和验证该序列的复杂度为O(n).
- 空间复杂度：O(2^2n * n),简单地,每个序列都视作是有效的.
## 回溯法
思路：不跟递归一样一一列举，就只判断有用的情况
```c++
void backtrace(string s, int open, int close, int n) {
    if (s.length() == 2 * n) {
        result.push_back(s);
    }
    if (open < n) {
        backtrace(s + '(', open + 1, close, n);
    }
    if (close < open) {
        backtrace(s + ')', open, close + 1, n);
    }
}

vector<string> generateParenthesis(int n) {
    
    if (n <= 0) {
        return result;
    }

    backtrace("", 0, 0, n);
    return result;
}
```
---
# 组合总和
给定一个无重复元素的数组 ```candidates``` 和一个目标数 ```target``` ，找出 ```candidates``` 中所有可以使数字和为 ```target``` 的组合。

```candidates``` 中的数字可以无限制重复被选取。

说明：

- 所有数字（包括 target）都是正整数。
- 解集不能包含重复的组合。 

示例1:
```
输入: candidates = [2,3,6,7], target = 7,
所求解集为:
[
  [7],
  [2,2,3]
]
```
示例2:
```
输入: candidates = [2,3,5], target = 8,
所求解集为:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```
## 回溯算法 + 剪枝
针对示例1:
输入: ```candidates = [2, 3, 6, 7]```，```target = 7```，所求解集为: ```[[7], [2, 2, 3]]```  
思路：以 ```target = 7``` 为根结点，每一个分支做减法。减到 0 或者负数的时候，剪枝。其中，减到 0 的时候结算，这里 “结算” 的意思是添加到结果集。  
![picture_1](https://pic.leetcode-cn.com/fe32ae9cee9ec8e2545d038d80a8da70d809eed01c153c6f0076801baab5010d-39-1.png)

把其中文字的部分去掉了，这样看得清楚一点：
![picture_2](https://pic.leetcode-cn.com/6e40e8001540f336dacbef4baa7710f31ca00a31ad286b7aa4109a13657d8960-39-2.png)

说明：

1. 一个蓝色正方形表示的是 “尝试将这个数到数组 candidates 中找组合”，那么怎么找呢？挨个减掉那些数就可以了.  

2. 在减的过程中，会得到 0 和负数，也就是被标红色和粉色的结点:  

- 得到 0 是我们最喜欢的，从 0 这一点向根结点走的路径（很可能只走过一条边，也算一个路径），就是一个组合，在这一点要做一次结算（把根结点到 0 所经过的路径，加入结果集). 

- 得到负数就说明这条路走不通，没有必要再走下去了.  

总结一下：在减的过程中，得到 0 或者负数，就没有必要再走下去，所以这两种情况就分别表示成为叶子结点。此时递归结束，然后要发生回溯。  

画出图以后,这张图画出的结果有 4 个 0 ,对应的路径是 ```[[2, 2, 3], [2, 3, 2], [3, 2, 2], [7]]```，而示例中的解集只有 ```[[7], [2, 2, 3]]```,问题是很显然的，结果集出现了重复。重复的原因是: **后面分支的更深层的边出现了前面分支低层的边的值**.  

但是这个问题也不难解决，把候选数组排个序就好了（想一下，结果数组排个序是不是也可以去重），后面选取的数不能比前面选的数还要小，即 “更深层的边上的数值不能比它上层的边上的数值小”，按照这种策略，剪枝就可以去掉重复的组合。  
![picture_3](https://pic.leetcode-cn.com/ade93b4f0678b2b1385ad1362ff426ce0a5a800a5b0ae07dfb65f58677374559-39-3.png)

```c++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    vector<int> candidates;
public:
    
    void DFS(int start, int target) {
        if (target == 0) {
            result.push_back(path);
            return;
        }
        for (int i=start; i < candidates.size() && target - candidates[i] >= 0; i++) {
            path.push_back(candidates[i]);
            DFS(i, target - candidates[i]);
            path.pop_back(); //为了 path 重复使用
        }
    }
    
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        this->candidates = candidates;
        DFS(0, target);
        
        return result;
    }
};

```
# 全排列
给定一个**没有重复数字**的序列，返回其所有可能的全排列。  

示例:
```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```
## 回溯法 + 深度优先搜索
思路：其实回溯法就是需要把图画清楚，画成树形图，当某一条路走到**底**时开始**回溯**，思想跟深度优先搜索差不多.
![picture](https://pic.leetcode-cn.com/9a8de9fcaf46ddb7bfce26071c81b98336420d066c71a8567230235fc56771ac-file_1575263193028)
```c++
class Solution {
private:
    vector<int> path;
    vector<vector<int>> result;
    unsigned long n_size;
public:
    void backtrack(vector<int>nums) {
        
        if (path.size() == n_size) {
            result.push_back(path);
            return;
        }
        
        for (int i = 0; i < nums.size(); i++) {
            path.push_back(nums[i]);
            vector<int> temp = nums;
            temp.erase(temp.begin() + i);
            backtrack(temp);
            path.pop_back();
        }
    }
    
    vector<vector<int>> permute(vector<int>& nums) {
        
        this->n_size = nums.size();
        backtrack(nums);
        
        return result;
    }
};
```
## Next permutation
思路：将数组排好序不断加入其下一个排列
```c++
vector<vector<int>> permute(vector<int>& nums) {
        
    sort(nums.begin(), nums.end());
    do {
        result.push_back(nums);
    }while (next_permutation(nums.begin(), nums.end()));

    return result;
    }
```
















