# 数组与字符串

<hr>


## 寻找数组的中心索引

给你一个整数数组 nums ，请计算数组的 中心下标 。

数组 中心下标 是数组的一个下标，其左侧所有元素相加的和等于右侧所有元素相加的和。

如果中心下标位于数组最左端，那么左侧数之和视为 0 ，因为在下标的左侧不存在元素。这一点对于中心下标位于数组最右端同样适用。

如果数组有多个中心下标，应该返回 最靠近左边 的那一个。如果数组不存在中心下标，返回 -1 。

```java
题解：利用 右后缀和  = 总和 - 左前缀和（sum）- 中心值
其中左前缀和 = 右后缀和 => 所以 两倍左前缀和 = 总和
class Solution {
    public int pivotIndex(int[] nums) {
         int total = Arrays.stream(nums).sum();//总和
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            if (2*sum + nums[i] == total ){
                return i;
            }
            sum+=nums[i];
        }
        return -1;
    }
}
```

---

## 搜索插入位置

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。（数组升序，且无重复元素）

请必须使用时间复杂度为 O(log n) 的算法。

```java
二分查找
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = (left + right) / 2;
            if(nums[mid] == target) {
                return mid;
            } else if(nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return left;
    }
}
```

---

## 合并区间

以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回 一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间 。

```java
class Solution {
     public int[][] merge(int[][] intervals) {//合并区间
    Arrays.sort(intervals, new Comparator<int[]>() { //左前缀排序
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[0] - o2[0];
            }
        });
        //合并,使用动态数组添加
        ArrayList<int[]> outputs = new ArrayList<>();
        for (int i = 0; i < intervals.length; i++) {
            int currentInterval[] = intervals[i];//当前数组区间
            if (outputs.isEmpty()) {//第一次添加，直接添加
                outputs.add(currentInterval);
            } else {//非第一次添加，进行区间重叠判断
                int currentLight = currentInterval[0] ;//当前个区间 左
                int beforeRight = outputs.get(outputs.size()-1)[1];//前一个区间右
                if (beforeRight >= currentLight) {//需要合并
                    int currentRight = currentInterval[1];//当前区间 右
                    if (beforeRight < currentRight) {
                        currentInterval[0] = outputs.get(outputs.size()-1)[0];
                        currentInterval[1] = currentRight;
                        outputs.set(outputs.size()-1,currentInterval);
                    }
                } else {//不需要合并
                    outputs.add(currentInterval);
                }
            }
        }
        return outputs.toArray(new int[outputs.size()][]);
}
}
```

---

## 旋转矩阵
给你一幅由 N × N 矩阵表示的图像，其中每个像素的大小为 4 字节。请你设计一种算法，将图像旋转 90 度。

不占用额外内存空间能否做到？
```java
原地修改：水平翻转 (i,j)=>(n-i-1,j) 然后 对角线翻转：（i,j）=> (j,i)
class Solution {
        public void rotate(int[][] matrix) {//旋转90度
            int arr [][] = new int[matrix.length][matrix.length];
            for(int i = 0;i<matrix.length; i++){//遍历数组
                int len = matrix.length-(i+1);
                for(int j = 0; j<matrix[i].length;j++){
                    arr[j][i] = matrix[len][j];
                }
            }
           for(int i = 0;i<matrix.length;i++){
               for(int j = 0;j<matrix[i].length;j++){
                   matrix[i][j] = arr[i][j];
               }
           }
        }
    }
```

---

## 零矩阵

编写一种算法，若M × N矩阵中某个元素为0，则将其所在的行与列清零。
```java
class Solution {
       public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        boolean[] row = new boolean[m];
        boolean[] col = new boolean[n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 0) {
                    row[i] = col[j] = true;
                }
            }
        }
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (row[i] || col[j]) {
                    matrix[i][j] = 0;
                }
            }
        }
    }
}
```


---

## 对角线遍历

给你一个大小为 `m x n` 的矩阵 `mat` ，请以对角线遍历的顺序，用一个数组返回这个矩阵中的所有元素。


![[Pasted image 20230313211958.png]]




```java
class Solution {
    public int[] findDiagonalOrder(int[][] mat) {
        int m = mat.length;//行
        int n = mat[0].length;//列
        int res [] = new int[m*n];//用于接收数据
        int pos = 0;
        for (int i = 0; i < m+n-1; i++) {//遍历对角线,i是对角线编号，从零开始
            if (i%2==1){//i为偶数
                int x = i<n? 0 : i-n+1;
                int y = i<n? i : n-1;
                while (x<m&&y>=0){
                    res[pos] = mat[x][y];
                    pos++;
                    x++;
                    y--;
                }
            }else {//i为奇数
                int x = i<m? i : m-1;
                int y = i<m? 0 : i-m+1;
                while (x>=0&&y<n){
                    res[pos] = mat[x][y];
                    pos++;
                    x--;
                    y++;
                }
            }
        }
        return res ;
    }
}
```


---

## 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

```java
//整体思路，每一次以长度小的字符串来作为比较次数。因为要找公共前缀，其中字符串是一个重要的影响因素
class Solution {
  public String longestCommonPrefix(String[] strs) {
            if (strs.length==0||strs == null)return "";
            String target = strs[0];//第一个字符串
            for(int i =1;i<strs.length;i++){
                target = longestCommonPrefix(target,strs[i]);
                if (target.length() == 0)break;
            }
            return target;
        }
        public String longestCommonPrefix(String target , String target1) {
            int index = 0;
            int min = Math.min(target.length(), target1.length());//两个字符串的最小长度，用于后面的比较长度
            while (index < min&&target.charAt(index) == target1.charAt(index)) {//连续比较每一个字符
                    index++;
                }
            return target.substring(0,index);
        }
}
```

---

## 最长回文子串

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

如果字符串的反序与原始字符串相同，则该字符串称为回文字符串。

```java
中心扩散法
主要思想：回文子串的长度不确定，所以直接穷举所有奇数和偶数的子串作为中心，往两百不断扩散
class Solution {
    // 主函数
    public String longestPalindrome(String s) {
        // 记录最长回文串
        String res = "";
        // 穷举以所有点（奇数一个点，偶数两个点）为中心的回文串
        for (int i = 0; i < s.length(); i++) {
            // 当回文串是奇数时，由一个中心点向两边扩散
            String s1 = palindrome(s, i, i);
            // 当回文串是偶数时，由中间的两个中心点向两边扩散
            String s2 = palindrome(s, i, i + 1);
            // 三元运算符：判断为真时取冒号前面的值，为假时取冒号后面的值
            res = res.length() > s1.length() ? res : s1;
            res = res.length() > s2.length() ? res : s2;
        }
        return res;
    }
    // 辅助函数：寻找回文串
    private String palindrome(String s, int left, int right) {
        // 在区间 [0, s.length() - 1] 中寻找回文串，防止下标越界
        while (left >=0 && right < s.length()) {
            // 是回文串时，继续向两边扩散
            if (s.charAt(left) == s.charAt(right)) {
                left--;
                right++;
            } else {
                break;
            }
        }
        // 循环结束时的条件是 s.charAt(left) != s.charAt(right), 所以正确的区间为 [left + 1, right), 方法 substring(start, end) 区间是 [start, end), 不包含 end
        return s.substring(left + 1, right);
    }
}
```

---

## 翻转字符串里的单词

给你一个字符串 s ，请你反转字符串中 单词 的顺序。

单词 是由非空格字符组成的字符串。s 中使用至少一个空格将字符串中的 单词 分隔开。

返回 单词 顺序颠倒且 单词 之间用单个空格连接的结果字符串。

注意：输入字符串 s中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。

```java
解题思路：先遍历字符串将首尾的空格全部去除。再利用双端队列再头部插入单词拉来实现倒序
class Test1 {  
    public static void main(String[] args) {  
        new Test1().reverseWords("  Hello my friends ");  
    }  
    public  String reverseWords(String str){  
        //空格去除  
        int start = 0;  
        int end = str.length()-1;  
        while (start < end && str.charAt(start) == ' ') start++;//去首空格  
        while (start < end && str.charAt(end) == ' ') end--;//去尾空格  
        str = str.substring(start,end+1);  //截取去掉首位空格的字符串
        start =0;  
        end = str.length()-1;  
        //将单词压入队列头部  
        Deque<String> d = new ArrayDeque<String>();  //双端队列
        StringBuilder word = new StringBuilder();  
        while (start <= end) {  
            char c = str.charAt(start);  
            if ((word.length() != 0) && (c == ' ')) {//当前有单词且遇到字符空格，可以将单词压入队列  
                // 每次都将单词 push 到队列的头部，相当于倒叙添加  
                d.offerFirst(word.toString());  
                word.setLength(0);//字符串长度归零，相当于删除已有字符串  
            } else if (c != ' ') {//当前字符不为空格  
                word.append(c);//添加  
            }  
            ++start;  
        }  
        d.offerFirst(word.toString());//压入最后一个单词  
        return String.join(" ",d);  
    }  
}
```

---

## 实现 strStr()（KMP算法）

给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串的第一个匹配项的下标（下标从 0 开始）。如果 needle 不是 haystack 的一部分，则返回  -1 。

假设与第一个字符不匹配：

![image-20230211165654050](D:\app\Typora\md\images\image-20230211165654050.png)

```java
KMP算法
上图中主串：AAAAAAGC  子串(模式串)：AAAAGC
部分解释：前缀包含第一个字符但不包含最后一个字符。后缀包含第最后一个个字符但不包含第一个一个字符
class Solution {
    public int strStr(String haystack, String needle) {
        if(needle.length()==0) return 0;
        int m = haystack.length(), i = 0;
        int n = needle.length(),   j = 0;
        int[] next = nextBuilder(needle);//根据模式串构造next数组
        while(i<m && j<n) {
            if(j<0 || haystack.charAt(i) == needle.charAt(j)) {
                i++;j++;
            }else{j = next[j];}
        }
        if(j == n) { return i - j;}
        else return -1;
    }
    private int[] nextBuilder(String needle) {next数组的构造
        int m = needle.length();//数组长度
        int[] next = new int[m];
        next[0] = -1;//next数组第一个为-1
        int t = -1, j = 0;
        while(j < m-1) {//数组遍历
            if(t < 0 || needle.charAt(t) == needle.charAt(j)) {
                t++;
                j++;
                next[j] = t;
            } else { t = next[t];}
        }
        return next;
    }
}
```


---

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

题目：
给定一个非负索引 `rowIndex`，返回「杨辉三角」的第 `rowIndex` 行。（0作为第一行）

在「杨辉三角」中，每个数是它左上方和右上方的数的和。

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        //初始化最终数组
        List<Integer> arrlist = new ArrayList();
        if(rowIndex < 0)return arrlist;
        int [] a = new int[rowIndex+1];
        //初始化结果数组,用一维数组作为参考数组,以层数作为长度
        for(int i = 0;i<a.length;i++){
            a[i] = 1;
        }
        //不停迭代结果数组，直到我们想要的那一层
        for(int i = 0;i<rowIndex;i++){//外层循环次数为层数，i=0 为第一层
            //更新结果数组
            for(int j = i; j>0;j--){
                a[j] = a[j] + a[j-1];
            }
        }
        //赋值并返回最终数组
        for(int j = 0;j<a.length;j++){
                arrlist.add(a[j]);
        }
        return arrlist;
    }
}
```


## 反转字符串中的单词 III

给定一个字符串 `s` ，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

```java
双指针解法、空间复杂度o(n),时间复杂度O(n2)
class Solution {  
    public String reverseWords(String s) {  
        //转化成字符数组  
        char []s2 = (s+" ").toCharArray();  
        for (int i = 0; i < s2.length; i++) {  
            //循环找空格  
            int right = 0;//作为右指针  
            for (int j = i; j < s2.length; j++) {  
                if (s2[j] == ' '){  
                    right = j;  
                    break;  
                }  
            }  
            //双指针交换位置  
            char temp = 0;  
            int r = right;//记录空格的位置  
            for (int j = i; j < right; j++,right--) {  
                temp = s2[j];  
                s2[j] = s2[right-1];  
                s2[right-1] = temp;  
            }  
            i =r;//下一个循环i会自增1，相当于记录了第一次找到的空格的下一个位置（作为左指针）  
        }  
        return String.valueOf(s2).trim();  
    }  
}
```


```java
解法二、API调用
class Solution {
    public String reverseWords(String s) {
        String[] s1 = s.split("\\s+");//以空格分割字符串   
        for (int i = 0; i < s1.length; i++) {
            s1[i] = reverse(s1[i]);
        }
        return String.join(" ",s1);
    }
    private String reverse(String s1) {
        StringBuilder sb = new StringBuilder(s1);
        return sb.reverse().toString();//反转每一个字符串
    }
}
```


## 寻找旋转排序数组中的最小值

已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,2,4,5,6,7] 在变化后可能得到：
若旋转 4 次，则可以得到 [4,5,6,7,0,1,2]
若旋转 7 次，则可以得到 [0,1,2,4,5,6,7]
注意，数组 [a[0], a[1], a[2], ..., a[n-1]] 旋转一次 的结果为数组 [a[n-1], a[0], a[1], a[2], ..., a[n-2]] 。

给你一个元素值 互不相同 的数组 nums ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。

你必须设计一个时间复杂度为 O(log n) 的算法解决此问题。

分析：其中--怎样旋转的？

![[屏幕截图 2023-03-11 212355.png]]

```java
二分法
class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length -1;
        while(left<right){
            int mid = left + (right - left )/2;//每次左边值更新
            if(nums[mid]>nums[right]){//如果中间值大于右边值，则最小值一定在右边
                left = mid+1;
            }
            else if(nums[mid]<nums[right]){//否则最小值在左边
                right =mid;
            }
            else right--;//左边的与中间值相等，缩短右指针(因为最小值不可能在最右边)
        }
        return nums[left];
    }
}
```


## 删除排序数组中的重复项

给你一个 升序排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。

由于在某些语言中不能改变数组的长度，所以必须将结果放在数组nums的第一部分。更规范地说，如果在删除重复项之后有 k 个元素，那么 nums 的前 k 个元素应该保存最终结果。

将最终结果插入 nums 的前 k 个位置后返回 k 。

不要使用额外的空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

```java
class Solution {
    //快慢指针思想，快指针将不同与满指针的元素放到慢指针的下一位
    public int removeDuplicates(int[] nums) {
        if(nums == null||nums.length == 0)return 0;
         // 定义慢指针和快指针，慢指针指向不同元素的最后一个位置，快指针用于遍历数组
        int slow =0;
        for(int fast =1;fast<nums.length;fast++){
            if(nums[slow] != nums[fast]){//右指针元素和左指针不同，则将右指针的元素放到左指针的下一个位置
                slow++;
                nums[slow] = nums[fast];
            }
        }
        return slow+1;
    }
}
```

思路：上述代码段是一种时间复杂度为 O(n) 的最优解。算法的基本思想是维护一个慢指针和一个快指针，当快指针指向的元素不同时，将其放到慢指针的下一个位置，并更新慢指针位置。由于数组已排序，因此相同元素必定相邻，只需要遍历一次即可删除重复元素。时间复杂度为 O(n)，空间复杂度为 O(1)。


## 移动零
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

请注意 ，必须在不复制数组的情况下原地对数组进行操作。

```java
class Solution {
public void moveZeroes(int[] nums) {
思路：快指针遍历寻找非零元素，将非零元素放在前面。
    //定义快慢指针，快指针寻找不是0的数，分两种情况，第一种：当第一个数就是0时，快指针寻找的非零元素与慢指针元素交换。第二种：当第一个元素不是0时，快指针寻找的非零元素与慢指针的下一个元素交换位置。再定义一个count来记录0出现的次数，用于最后赋值
        int slow = 0;
        int count = 0;
        if(nums[slow] != 0){//第一种情况
            for(int fast = 1;fast<nums.length;fast++){
                if(nums[fast]!=0){//逆向思维，当前数不是0时
                    slow++;
                    nums[slow] = nums[fast];
                }else count++;
            }
        }else{//第二中情况
            count =1;
            for(int fast = 1;fast<nums.length;fast++){
                if(nums[fast]!=0){//逆向思维，当前数不是0时
                    nums[slow] = nums[fast];
                    slow++;
                }else count++;
            }
        }
        //逆向赋值0
        int len = nums.length;
        for(int i =0;i<count;i++){
            nums[len-1] = 0;
            len--;
        }
    }
}
```

