# GCD
```c++
int gcd(int a, int b) {
    if (b == 0) {
        return a;
    }
    return gcd(b, a % b);
}
```
---
# LCM
```c++
int lcm(int a, int b) {
    return a / gcd(a, b) * b;
}
```
---
# 下一个排列
实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须原地修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
```1,2,3``` → ```1,3,2```
```3,2,1``` → ```1,2,3```
```1,1,5``` → ```1,5,1```
## 思路：  
1.从后向前查找第一个相邻升序的元素对 ```(i, i+1)```，满足 ```A[i] < A[i+1]```。此时 ```[i+1,end)``` 必然是降序.  
2.在 ```[i+1,end)``` 从后向前查找第一个满足 ```A[i] < A[j]``` 的 ```j```.  
3.将 ```A[i]``` 与 ```A[j]``` 交换.  
4.可以断定这时 ```[i+1,end)``` 必然是降序，逆置 ```[i+1,end)```，使其升序.  
5.如果在步骤 1 找不到符合的相邻元素对，说明当前 ```[begin,end)``` 为一个降序顺序，则直接跳到步骤 4.  
```c++
void swap(vector<int>&nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}

void reverse(vector<int>&nums, int start) {
    int i = start, j = nums.size() - 1;
    while (i < j) {
        swap(nums, i, j);
        i++;
        j--;
    }
}

void nextPermutation(vector<int>& nums) {
    int i = nums.size() - 2;
    //找到第一个升序对
    while (i >= 0 && nums[i] >= nums[i+1]) {
        i--;
    }
    if (i >= 0) {
        int j = nums.size() - 1;
        //从右到左找到第一个比 nums[i] 大的值并交换
        while (j >= 0 && nums[j] <= nums[i]) {
            j--;
        }
        swap(nums, i, j);
    }
    //反转 i 后面的数组(因为后面的数组一定是降序，反转成升序即为最小的下一个排列)
    reverse(nums, i + 1);
}
```
## next_permutation
```c++
vector<vector<int>> permute(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    do {
        result.push_back(nums);
    }while (next_permutation(nums.begin(), nums.end()));

    return result;
}
```
---
# 矩阵旋转
给定一个 n × n 的二维矩阵表示一个图像。  

将图像顺时针旋转 90 度。   

说明：  

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。  

示例1:
```
给定 matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

原地旋转输入矩阵，使其变为:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```
示例2:
```
给定 matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 

原地旋转输入矩阵，使其变为:
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```
## 方法一：转置加翻转
思路：先将矩阵转置，再反转每一行.
```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        unsigned long n_size = matrix.size();
        for (int i = 0; i < n_size; i++) {
            for (int j = i; j < n_size; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        for (int i = 0; i < matrix.size(); i++) {
            reverse(matrix[i].begin(), matrix[i].end());
        }
    }
};
```
## 方法二：旋转四个矩形
思路：方法 1 使用了两次矩阵操作，但是有只使用一次操作的方法完成旋转。

为了实现这一点，我们来研究每个元素在旋转的过程中如何移动。
![picture_1](https://pic.leetcode-cn.com/12605efb60d2efc64e6ecfcf6562a98a49acb3ce696b0c1ad3da46ab8977fa16-48_angles.png)
这提供给我们了一个思路，将给定的矩阵分成四个矩形并且将原问题划归为旋转这些矩形的问题。
![picture_2](https://pic.leetcode-cn.com/7a684b207a95188ff6450e4724d6ee8bdf425fc483775a8e30082ed25060dac1-48_rectangles.png)
现在的解法很直接 - 可以在第一个矩形中移动元素并且在 长度为 ```4```个元素的临时列表中移动它们。
### picture_1
![picture_3](https://pic.leetcode-cn.com/72f9aff037ca0f598e76d5f1fdb80be699dbc651bb7ae98c6f8fab75446f4ec0-image.png)  
### picture_2
![picture_4](https://pic.leetcode-cn.com/d2b8312ed463bceb9ac3feb88e00c70abc4edc02c55ade3fea4723e757e269d9-image.png)  
### picture_3
![picture_5](https://pic.leetcode-cn.com/dc9a6b302b2f980a834ec41ca20b9c5e33e616f176517922573eca3a3a5e4cf4-image.png)  
### picture_4
![picture_6](https://pic.leetcode-cn.com/0a690b4158ebe5f63b7362046ef64fc7963bcbec9367e2538ae3a4c12c33f21f-image.png)  
### picture_5
![picture_7](https://pic.leetcode-cn.com/dbe1585333c8ccb989b62c8c0a6bb672f309724967af92119ef949d81bbeebc4-image.png)  
### picture_6
![picture_8](https://pic.leetcode-cn.com/a314b77e71329bdb7ade17f0a1a9aa29c347517c55f1a1731a6c0cd51e3606a3-image.png)  

```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        unsigned long n = matrix.size();
        for (int i = 0; i < n / 2 + n % 2; i++) {
            for (int j = 0; j < n / 2; j++) {
                int tmp[4];
                int row = i;
                int col = j;
                for (int k = 0; k < 4; k++) {
                    tmp[k] = matrix[row][col];
                    int x = row;
                    row = col;
                    col = n - 1 - x;
                }
                for (int k = 0; k < 4; k++) {
                    matrix[row][col] = tmp[(k + 3) % 4];
                    int x = row;
                    row = col;
                    col = n - 1 - x;
                }
            }
        }
    }
};
```
## 方法三：在单次循环中旋转 4 个矩形
思路：该想法和方法 2 相同，但是所有的操作可以在单次循环内完成并且这是更精简的方法。
```c++
class Solution {
  public void rotate(int[][] matrix) {
    int n = matrix.length;
    for (int i = 0; i < (n + 1) / 2; i ++) {
      for (int j = 0; j < n / 2; j++) {
        int temp = matrix[n - 1 - j][i];
        matrix[n - 1 - j][i] = matrix[n - 1 - i][n - j - 1];
        matrix[n - 1 - i][n - j - 1] = matrix[j][n - 1 -i];
        matrix[j][n - 1 - i] = matrix[i][j];
        matrix[i][j] = temp;
      }
    }
  }
}
```
---




















