# 数组与字符串

<hr>


**题目**：

主串A：a、b、c、d

子串B：b、c

判断B是否存在于A中，存在返回A下标，不存在放回-1

## BF暴力算法


题解：比较次数n-m+1

```java
public class Test10 {
    public int strStr(String haystack, String needle) {
        int n = haystack.length(), m = needle.length();
        for (int i = 0; i < n - m + 1; i++) {
            String sub = haystack.substring(i, i + m);
            if (sub.equals(needle)) return i;
        }
        return -1;
    }
}
```

## KMP算法

假设与第一个字符不匹配：

![image-20230211165654050](D:\app\Typora\md\images\image-20230211165654050.png)

<hr>

**题目**：

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 s 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

示例 1：
```
输入：s = ["h","e","l","l","o"]
输出：["o","l","l","e","h"]
```

---
## 双指针

<img src="D:\app\Typora\md\images\image-20230213141626279.png" alt="image-20230213141626279" style="zoom:50%;" />


```java
class Solution {
    public void reverseString(char[] s) {
        //使用双指针
        int j = s.length-1;//右
        //两个指针同时往中间移动，当两个指针相遇时结束
        char temp = ' ';//中间变量
        for(int i = 0;i<j;i++,j-- ){//i>j时条件不满足即可退出循环
            temp = s[i]; 
            s[i] = s[j];
            s[j] = temp; 
        }
    }
}
```

<hr>


## 两数之和 I

给定长度为 2n 的整数数组 nums ，你的任务是将这些数分成 n 对, 例如 (a1, b1), (a2, b2), ..., (an, bn) ，使得从 1 到 n 的 min(ai, bi) 总和最大。返回该 最大总和 。 

示例 1：
```
输入：nums = [1,4,3,2]
输出：4
解释：所有可能的分法（忽略元素顺序）为：
1. (1, 4), (2, 3) -> min(1, 4) + min(2, 3) = 1 + 2 = 3
2. (1, 3), (2, 4) -> min(1, 3) + min(2, 4) = 1 + 2 = 3
3. (1, 2), (3, 4) -> min(1, 2) + min(3, 4) = 1 + 3 = 4
所以最大总和为 4
```

解法：
## 数组拆分

将数组进行由小到大的排序，两个一组选取其中最小的数相加即可。

如：[6、2、6、5、1、2]  排序 => [1、2、2、5、6、6]

最大的（6，6）中最小值为6，第二大的（2，5）中最小值为2，第三大的（1，2）中最小值为1

6+1+2=9

```java
class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int ans = 0;
        for (int i = 0; i < nums.length; i += 2) {
            ans += nums[i];
        }
        return ans;
    }
}
```

<hr>


## 两数之和 II - 输入有序数组

给你一个下标从 1 开始的整数数组 numbers ，该数组已按 非递减顺序排列  ，请你从数组中找出满足相加之和等于目标数 target 的两个数。如果设这两个数分别是 numbers[index1] 和 numbers[index2] ，则 1 <= index1 < index2 <= numbers.length 。
以长度为 2 的整数数组 [index1, index2] 的形式返回这两个整数的下标 index1 和 index2。
你可以假设每个输入 只对应唯一的答案 ，而且你 不可以 重复使用相同的元素。
你所设计的解决方案必须只使用常量级O(1)的额外空间

解法一：
```java
时间复杂度O(n)
public int[] twoSum(int[] numbers, int target) {  
    int nums [] = new int[2];  
    int len = numbers.length;  
    int j = 1;  
    int i = 0;  
    while(i<len-1){  
            if (numbers[i]+numbers[j] == target) {  
                nums[0] = i + 1;  
                nums[1] = j + 1;  
                return nums;  
            }  
        if(j == len-1){  
            i++;  
            j = i;  
        }  
        j++;  
    }  
    return new int[]{0,0};  
}
```

解法二：
## 二分查找

二分查找适用于数组，mid对应left 和 right下标相减除以2。本题时间复杂度为 : nlog(n)
```java
mid = (right - left )/2
```

![[二分查找.gif]]
```java
时间复杂度nlog(n)
class Solution {//二分法解题思路：先确定第一个数，再用二分法从剩下的数中寻找第二个数
    public int[] twoSum(int[] numbers, int target) {
        for (int i = 0; i < numbers.length; ++i) {
          int low = i + 1, high = numbers.length - 1;//下标为1的数低位,最后一位为高位,来计算中间位
            while (low <= high) {
                int mid = (high - low) / 2 + low;
                if (numbers[mid] == target - numbers[i]) {//中间数=目标数-确定的第一个数
                    return new int[]{i + 1, mid + 1};
                } else if (numbers[mid] > target - numbers[i]) {
                    high = mid - 1;
                } else {
                    low = mid + 1;
                }
            }
        }
        return new int[]{-1, -1};//为找到返回{-1,-1}
    }
}
```


## 移除元素
给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。
不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 ==原地== 修改输入数组。
元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

```java
解法一、双指针
时间复杂度O(n)
解题思路：要求原地修改数组，用双指针。且不用修改后面多余的数->直接返回赋值次数。
class Solution {
    public int removeElement(int[] nums, int val) {
    int j = 0;    //慢指针
        for(int i =0;i<nums.length;i++){//快指针
            if(nums[i] != val){
                nums[j]=nums[i];
                j++;
            }
        }
        return j;//返回指针长度即可(赋值执行次数)
    }
}
```

```java
双指针优化-->在上述解法中如果情况如[1,2,4,5] 寻找1，只有第一个值是1，每个元素都会赋值一遍。比较耗时。所以针对val值较少的情况下，可以使用双指针从左右两边往中间移动。因为题目不要求顺序，如果左指针的值等于val，则将右指针的值给它。以此减少时间损耗。**避免了需要保留的元素的重复赋值操作**。

时间复杂度O(n)
class Solution {
    public int removeElement(int[] nums, int val) {
    int left = 0;//左指针
    int right = nums.length;//右指针开始位置是最右边，但是在外面给定nums.length-1会导致循环次数少一次，少比较一次。所以在里面赋值时给定即可。
    while(left<right){//两个指针往中间移动，比较次数为n次（总共n个元素）
	    if(nums[left] == val){//当左边指针所在位置元素等于目标值时，用右指针元素代替。（相当于移除了左指针的元素）
			nums[left] = nums[rigth-1];
			right--;//左指针位置不变，右指针左移
	    }
	    else{
		    left++;
	    }    
    }
    return left;
    }
}
```


## 最大连续1的个数
给定一个二进制数组 `nums` ， 计算其中最大连续 `1` 的个数。

解题思路：数组中非零即一。遍历一遍数组，记录1出现的最大个数。
```java
时间复杂度O(n)
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int maxlen = 0;//记录最大长度
        int count = 0;//记录1的次数
        int a = nums.length;
        for(int i =0;i<a;i++){
            if(nums[i] == 1){//等于1次数加加
               count++;
            }
            else{
                maxlen = Math.max(count,maxlen);
                count =0;
            }
        }
        //循环结束时，可能最后也是1，但是没有更新长度
        maxlen = Math.max(count,maxlen);
      return maxlen;
}
}
```

---

## 长度最小的子数组

给定一个含有 n 个正整数的数组和一个正整数 target 。
找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。

解法一、暴力题解
```java
时间复杂度O(n)
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int n = nums.length;
        if (n == 0) {
            return 0;
        }
        int ans = Integer.MAX_VALUE;//最小长度
        for (int i = 0; i < n; i++) {
            int sum = 0;
            for (int j = i; j < n; j++) {
                sum += nums[j];
                if (sum >= s) {//当满足条件时
                    ans = Math.min(ans, j - i + 1);//更新最小长度
                    break;
                }
            }
        }
        return ans == Integer.MAX_VALUE ? 0 : ans;//最大值未变，说明没有，返回0。否则返回ans
    }
}
```

解法二、滑动指针
```java
时间复杂度O(n),左右指针最多移动n次
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        // 初始化左右指针和最小子数组长度
        int left = 0, right = 0, sum = 0, minLen = Integer.MAX_VALUE;
        
        // 遍历整个数组
        while (right < nums.length) {
            // 将右指针指向的元素加入子数组
            sum += nums[right];
            // 当子数组元素之和大于或等于目标值时，收缩左指针，缩小子数组
            while (sum >= target) {
                // 更新最小子数组长度
                minLen = Math.min(minLen, right - left + 1);
                
                // 移除左指针指向的元素
                sum -= nums[left];
                left++;
            } 
            // 右指针继续向右移动
            right++;
        }
        // 如果最小子数组长度没有被更新过，说明不存在符合条件的子数组，返回0
        return minLen == Integer.MAX_VALUE ? 0 : minLen;
    }
}
```

如果数组是无序的，则可以使用快速排序或堆排序等算法进行排序，然后再使用双指针法求解。排序的时间复杂度为 O(nlogn)，加上双指针的时间复杂度 O(n)，总时间复杂度为 O(nlogn)。

如果不能改变原数组的顺序，则可以先将原数组复制一份，然后对复制后的数组排序，求解完最小子数组长度后，再根据排序后的数组找到对应的原数组子数组。时间复杂度和空间复杂度都为 O(nlogn)。

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        Arrays.sort(nums);  // 排序
        int left = 0, right = 0, sum = 0, minLen = Integer.MAX_VALUE;
        while (right < nums.length) {
            sum += nums[right];
            while (sum >= target) {
                minLen = Math.min(minLen, right - left + 1);
                sum -= nums[left];
                left++;
            }
            right++;
        }
        return minLen == Integer.MAX_VALUE ? 0 : minLen;
    }
}

```

## 杨辉三角

题目：给定一个非负整数 _`numRows`，_生成「杨辉三角」的前 _`numRows`_ 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。
```java
解题思路：用集合数组来放入集合数组。形成二维数组。首先判断给定数是否是0，0即返回空数组。
第一行初始化为1。从第二行开始逐行计算，首数和尾数都是1，中间数需要用到前一行的第j和j-1的数。
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> triangle = new ArrayList<>();
        if (numRows == 0) {
            return triangle;
        }
        // 初始化第一行
        List<Integer> firstRow = new ArrayList<>();
        firstRow.add(1);
        triangle.add(firstRow);
        // 逐行计算
        for (int i = 1; i < numRows; i++) {//第二行开始
            List<Integer> prevRow = triangle.get(i - 1);
            List<Integer> row = new ArrayList<>();
            // 第一个元素为1
            row.add(1);
            // 计算中间的元素
            for (int j = 1; j < i; j++) {//遍历前一行的j-1和j的数
                row.add(prevRow.get(j - 1) + prevRow.get(j));
            }
            // 最后一个元素为1
            row.add(1);
            triangle.add(row);
        }
        return triangle;
    }
}
```

优化代码
```java
将二维数组变为一位数组来存储结果，降低空间复杂度
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> triangle = new ArrayList<>();
        if (numRows == 0) {
            return triangle;
        }
        // 初始化结果数组
int[] row = new int[numRows];//用一行数组作为下一行的参考[1*numRows] 如:输入5 -> [1,1,1,1,1]
        for (int i = 0; i < numRows; i++) {
            row[i] = 1;
        }
        // 逐行计算
        for (int i = 0; i < numRows; i++) {
            List<Integer> currRow = new ArrayList<>();
            // 添加当前行的元素
            for (int j = 0; j <= i; j++) {
                currRow.add(row[j]);
            }
            triangle.add(currRow);
            // 更新前一行的结果数组
            for (int j = i; j > 0; j--) {
                row[j] = row[j] + row[j - 1];
            }
        }
        return triangle;
    }
}
首先初始化一个长度为 numRows 的数组 row，用于存储每一行的结果。将数组中的所有元素初始化为1，这是因为每一行的首尾元素都为1。然后，遍历每一行，逐个计算每个元素的值，并添加到当前行的列表中。计算每个元素的值时，需要使用前一行的结果数组。在计算完每一行后，需要更新结果数组，将其更新为当前行的结果数组，以便下一行计算时使用。

这种优化方法可以将空间复杂度从 O(n^2) 降低到 O(n)，因此在 numRows 很大时，可以显著减少内存消耗。
```
