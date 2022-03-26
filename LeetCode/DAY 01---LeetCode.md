# DAY 01---二分查找

[TOC]

## 1. 704 二分查找 

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220123062245668.png" alt="image-20220123062245668" style="zoom: 80%;" />

```java
// 正确的解法
class Solution {
    public int search(int[] nums, int target) { //本身已经带了两个输入数据，一个是数组，一个是目标值
        int low = 0, high = nums.length - 1;
        while (low <= high) {
            int mid = (high - low) / 2 + low;
            int num = nums[mid];
            if (num == target) {
                return mid;
            } else if (num > target) {  
                high = mid - 1;  // 错误的地方
            } else { 
                low = mid + 1; //错误的地方
            }
        }
        return -1;
    }
}

//自己的解法
/**
	1. low = mid + 1;  high = mid -1;
	2. while 超时
**/
class Solution {
    public int search(int[] nums, int target) {
        int low =0,high=nums.length-1;
        if(low<=high){   //!! 肯定不是IF ，而是while
            int mid = (high - low)/2 + low ; // 中间的位置
            int num = nums[mid]; // 中间值
            if(num == target){ //目标值 = 中间值
                return mid;
            }else if(num < target){ //目标值 > 中间值，说明该数在右侧
                low = mid ; 
            }else{
                high = mid; 
            }
        }
        return -1;
    }
}
```

------

**二分查找法**是一种在有序数组中查找某一特定元素的搜索算法。搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜索过程结束；如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较。如果在某一步骤数组为空，则代表找不到。这种搜索算法每一次比较都使搜索范围缩小一半。

------

### **一、左闭右闭[left,right]** 

这里我们先给出二分查找法的第一种写法:

```Java
public int binarySearch(int[] nums, int target) {
        // 在区间[left,right]中查找元素，左闭右闭
        int left = 0;
        int right = nums.length - 1;

        // 由于是在区间[left,right]中查找
        // 因此当left=right时，区间内还有一个元素需要查找
        while (left <= right) {
            // 计算中间点
            int mid = left + (right-left)/2;

            // 如果target == nums[mid]则表示已经找到，返回mid
            if (target == nums[mid]) {
                return mid;
                // 如果target < nums[mid]，表示目标值可能在左半边
            } else if (target < nums[mid]){
                // 由于是在左闭右闭的区间[left,right]中查找
                // 而target < nums[mid]，因此mid不再需要考虑
                // 所以right = mid - 1,即在[left,mid-1]中继续查找
                right = mid - 1;

                // 如果target > nums[mid]，表示目标值可能在右半边
            } else if (target > nums[mid]){
                // 由于是在左闭右闭的区间[left,right]中查找
                // 而target > nums[mid]，因此mid不再需要考虑
                // 所以left = mid + 1，即在[mid+1,right]中继续查找
                left = mid + 1;
            }
        }

        // 未找到返回-1
        return -1;
    }
```

------

 对于折半查找但最终没找到的情况，根据题意是返回-1即可。

##### **1.程序什么时候停止** 

在上述代码中的第2行和第3行，分别定义了left=0，right=nums.length-1。这表明我们是在左闭右闭的区间[left, right]中查找目标值。

```java
int left = 0;
int right = nums.length - 1;
```


既然是查找目标值，那么就有找到和找不到的情况。对于能找到目标值的情况，只需直接将目标值返回即可，代码如下：

```java
int mid = left + (right-left)/2;
if (target == nums[mid]) {
    return mid;
}
```

对于折半查找但最终没找到的情况，根据题意是返回-1即可。那么，这时程序什么时候停止呢？由于这里定义的是在[left, right]这个左闭右闭的区间内查找，因此当left=right时，区间[left, right]依然包含一个有效的待查找元素。 所以在不存在目标值的情况下，程序终止的条件是left>right。

```java
while (left <= right) {
    // 查找逻辑    
}
```

##### **2、是当target < nums[mid]时，为什么right=mid-1呢？‍**

```java
if (target < nums[mid]){
    right = mid - 1;
}
```

首先，target小于nums[mid]，意味着接下来应该在左半边进行查找，所以要明确右边界right的值。

而这里定义的查找区间是 [left, right] 这个左闭右闭的区间，又，mid已经确认比target大了，因此right=mid-1。

##### **3、是当target > nums[mid]时，为什么left=mid+1呢？**

```java
if (target > nums[mid]){
    left = mid + 1;
}
```

同样的，当target大于nums[mid]时，意味着接下来应该去右半边进行查找，所以要明确左边界left的值。

又，我们是在 [left, right] 这个左闭右闭的区间内查找且已经明确target大于nums[mid]了，所以left=mid+1。

### **二、左闭右开[left,right)**

在理解了第一种写法后，接着看下第二种写法。第二种写法与第一种写法的一个主要区别是查找区间变为了左闭右开[left, right)。

这一变化使得代码相比第一种写法有两处不同：

##### 一是while循环继续的条件变为了left<right。

因为如果left=right，那么区间[left,right)是不存在的，比如区间[2,2)。

```java
while (left < right) {
    // 查找逻辑    
}
```

##### 二是当target<nums[mid]时，右侧边界right=mid而不是之前的mid-1。

原因还是由于这里定义的查找区间是[left, right)，那么当target<nums[mid]时，nums[mid-1]还是需要考察的，所以right=mid。

```java
if (target < nums[mid]){
    right = mid;
}
```

具体的代码实现如下，可参考详细注释进行理解。

```java
    public int binarySearch(int[] nums, int target) {
        // 在区间[left,right)中查找元素，左闭右开
        int left = 0;
        int right = nums.length;
    // 由于是在区间[left,right)中查找
    // 当left=right时，区间[left,right)不存在
    while (left < right) {
        // 计算中间点
        int mid = left + (right-left)/2;

        // 如果target == nums[mid]则表示已经找到，返回mid
        if (target == nums[mid]) {
            return mid;
            // 如果target < nums[mid]，表示目标值可能在左半边
        } else if (target < nums[mid]){
            // 由于是在左闭右开的区间[left,right)中查找
            // 而target < nums[mid]，因此mid不再需要考虑
            // 但nums[mid-1]还需考察，所以right = mid
            // 即在[left,mid)中继续查找
            right = mid;

            // 如果target > nums[mid]，表示目标值可能在右半边
        } else if (target > nums[mid]){
            // 由于是在左闭右开的区间[left,right)中查找
            // 而target > nums[mid]，因此mid不再需要考虑
            // 所以left = mid + 1,即在[mid+1,right)中继续查找
            left = mid + 1;
        }
    }

    // 未找到返回-1
    return -1;
}
```

## 2. 278第一个错误的版本 

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220215211145634.png" alt="image-20220215211145634" style="zoom: 80%;" />

第一次做对题哦！

```java
/* The isBadVersion API is defined in the parent class VersionControl.
      boolean isBadVersion(int version); */

public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left=1,right=n; // 左右边界
        while(left<right){
            int mid = (right -left)/2+left; //中间值
            if(isBadVersion(mid)){ 
                //如果中间版本是错误的，则说明，第一个出错的是在左半部分
                right = mid; 
            }else{
                // 否则出错的是在右半部分，且中间版本没有问题
                left = mid + 1 ;
            }
        }
        //跳出循环
         return left;
    }
}
```

因为题目要求尽量减少调用检查接口的次数，所以不能对每个版本都调用检查接口，而是应该将调用检查接口的次数降到最低。

注意到一个性质：当一个版本为正确版本，则该版本之前的所有版本均为正确版本；当一个版本为错误版本，则该版本之后的所有版本均为错误版本。我们可以利用这个性质进行二分查找。

具体地，将左右边界分别初始化为 1和 n，其中 n 是给定的版本数量。设定左右边界之后，每次我们都依据左右边界找到其中间的版本，检查其是否为正确版本。**如果该版本为正确版本，那么第一个错误的版本必然位于该版本的右侧，我们缩紧左边界；否则第一个错误的版本必然位于该版本及该版本的左侧，我们缩紧右边界。**

这样我们每判断一次都可以缩紧一次边界，而每次缩紧时两边界距离将变为原来的一半，因此我们至多只需要缩紧 O(logn) 次。

```java
 public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int left = 1, right = n;
        while (left < right) { // 循环直至区间左右端点相同
            int mid = left + (right - left) / 2; // 防止计算时溢出
            if (isBadVersion(mid)) {
                right = mid; // 答案在区间 [left, mid] 中
            } else {
                left = mid + 1; // 答案在区间 [mid+1, right] 中
            }
        }
        // 此时有 left == right，区间缩为一个点，即为答案
        return left;
    }
}
```

复杂度分析

时间复杂度：O(logn)，其中 n 是给定版本的数量。

空间复杂度：O(1)。我们只需要常数的空间保存若干变量。

## 35.搜索插入位置

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220216144913375.png" alt="image-20220216144913375" style="zoom:80%;" /><img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220216145022485.png" alt="image-20220216145022485" style="zoom:80%;" />

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left=0,right=nums.length-1;
        while(left<=right){
            int mid = (right -left)/2+left;
            int num = nums[mid];
            if(num==target){
                return mid;
            }else if(num > target){
                right = mid -1;
            }else{
                left = mid + 1;
            }
        }
        return left; 
    }
}
```

