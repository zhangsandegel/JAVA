[TOC]

## 1.15 三数之和

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220219203450815.png" alt="image-20220219203450815"  />

### 方法一：排序+双指针

题目中要求找到**所有「不重复」且和为 0的三元组**，这个「不重复」的要求使得我们无法简单地使用三重循环枚举所有的三元组。这是因为在最坏的情况下，数组中的元素全部为0，即

```
[0, 0, 0, 0, 0, ..., 0, 0, 0]
```

任意一个三元组的和都为 0。如果我们==直接使用三重循环枚举三元组==，会得到 O($N^3$) 个满足题目要求的三元组（其中 N是数组的长度）时间复杂度至少为 O($N^3$)。在这之后，我们还需要使用==哈希表进行去重操作==，得到不包含重复三元组的最终答案，又消耗了大量的空间。这个做法的时间复杂度和空间复杂度都很高，因此我们要换一种思路来考虑这个问题。

**==「不重复」的本质是什么？==**我们保持三重循环的大框架不变，只需要保证：

- 第二重循环枚举到的元素不小于当前第一重循环枚举到的元素；
- 第三重循环枚举到的元素不小于当前第二重循环枚举到的元素。

也就是说，我们枚举的三元组 (a, b, c)满足 a≤b≤c，保证了只有 (a, b, c)这个顺序会被枚举到，而 (b, a, c)、(c, b, a)等等这些不会，这样就减少了重复。要实现这一点，我们可以==将数组中的元素从小到大进行排序==，随后使用普通的三重循环就可以满足上面的要求。

同时，对于每一重循环而言，相邻两次枚举的元素不能相同，否则也会造成重复。举个例子，如果排完序的数组为

```
[0, 1, 2, 2, 2, 3]
 ^  ^  ^
```


我们使用三重循环枚举到的第一个三元组为 (0, 1, 2)，如果第三重循环继续枚举下一个元素，那么仍然是三元组 (0, 1, 2)，产生了重复。因此我们需要将第三重循环「跳到」下一个不相同的元素，即数组中的最后一个元素 3，枚举三元组 (0, 1, 3)。

下面给出了改进的方法的伪代码实现：

```java
nums.sort()
// 第一重循环
for first = 0 .. n-1
    // 只有和上一次枚举的元素不相同，我们才会进行枚举
    if first == 0 or nums[first] != nums[first-1] then
        //第二重循环
        for second = first+1 .. n-1
            if second == first+1 or nums[second] != nums[second-1] then
                //第三重循环
                for third = second+1 .. n-1
                    if third == second+1 or nums[third] != nums[third-1] then
                        // 判断是否有 a+b+c==0
                        check(first, second, third)
```

这个方法就是我们常说的**==「双指针」==**，

------

当我们需要枚举数组中的两个元素时，如果我们==发现随着第一个元素的递增，第二个元素是递减的====，那么就可以使用双指针的方法，将枚举的时间复杂度从 O($N^2$)  减少至 O(N ）==

为什么是 O(N) 呢？这是因为在枚举的过程每一步中，「左指针」会向右移动一个位置（也就是题目中的 b），而「右指针」会向左移动若干个位置，这个与数组的元素有关，但我们知道它一共会移动的位置数为 O(N)，均摊下来，每次也向左移动一个位置，因此时间复杂度为 O(N)。

注意到我们的伪代码中还有第一重循环，时间复杂度为 O(N)，因此枚举的总时间复杂度为 O($N^2$)。由于排序的时间复杂度为O(NlogN)，在渐进意义下小于前者，因此算法的总时间复杂度为 O($N^2$)

上述的伪代码中还有一些细节需要补充，例如我们需要保持左指针一直在右指针的左侧（即满足b≤c），具体可以参考下面的代码，均给出了详细的注释。

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        // 数组进行排序
        int n = nums.length;
        Arrays.sort(nums);
        // 定义List
        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        // 枚举 a
        for (int first = 0; first < n; ++first) { 
            // 需要和上一次枚举的数不相同
            if (first > 0 && nums[first] == nums[first - 1]) {
                continue; 
            }
            // c 对应的指针初始指向数组的最右端
            int third = n - 1;
            int target = -nums[first];
            // 枚举 b
            for (int second = first + 1; second < n; ++second) {
                // 需要和上一次枚举的数不相同
                if (second > first + 1 && nums[second] == nums[second - 1]) {
                    continue;
                }
                // 需要保证 b 的指针在 c 的指针的左侧
                while (second < third && nums[second] + nums[third] > target) {
                    --third;
                }
                // 如果指针重合，随着 b 后续的增加
                // 就不会有满足 a+b+c=0 并且 b<c 的 c 了，可以退出循环
                if (second == third) {
                    break;
                }
                if (nums[second] + nums[third] == target) {
                    List<Integer> list = new ArrayList<Integer>();
                    list.add(nums[first]);
                    list.add(nums[second]);
                    list.add(nums[third]);
                    ans.add(list);
                }
            }
        }
        return ans;
    }
} 
```

**复杂度分析**

- 时间复杂度：O($N^2$) ，其中 N 是数组nums 的长度。


- 空间复杂度:O(logN)。我们忽略存储答案的空间，额外的排序的空间复杂度为 O(logN)。然而我们修改了输入的数组 nums，在实际情况下不一定允许，因此也可以看成使用了一个额外的数组存储了 nums 的副本并进行排序，空间复杂度为 O(N) 。

  

更简洁版本：

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {// 总时间复杂度：O(n^2)
        List<List<Integer>> ans = new ArrayList<>();
        if (nums == null || nums.length <= 2) return ans;

        Arrays.sort(nums); // O(nlogn)

        for (int i = 0; i < nums.length - 2; i++) { // O(n^2)
            if (nums[i] > 0) break; // 第一个数大于 0，后面的数都比它大，肯定不成立了
            if (i > 0 && nums[i] == nums[i - 1]) continue; // 去掉重复情况
            // ！！！ 还可以这么使用
            int target = -nums[i];
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                if (nums[left] + nums[right] == target) {
                    ans.add(new ArrayList<>(Arrays.asList(nums[i], nums[left], nums[right])));
                    
                    // 现在要增加 left，减小 right，但是不能重复，比如: [-2, -1, -1, -1, 3, 3, 3], i = 0, left = 1, right = 6, [-2, -1, 3] 的答案加入后，需要排除重复的 -1 和 3
                    left++; right--; // 首先无论如何先要进行加减操作
                    while (left < right && nums[left] == nums[left - 1]) left++;
                    while (left < right && nums[right] == nums[right + 1]) right--;
                } else if (nums[left] + nums[right] < target) {
                    left++;
                } else {  // nums[left] + nums[right] > target
                    right--;
                }
            }
        }
        return ans;
    }
}
```

 

### 方法二：哈希

**题目解析**

超级好题，难度中等。

------

这道题用哈希的解法，可以参考之前的【两数之和】时的思路 。

两数之和是在数组中找出 a + b = target，每次枚举 a，找数组中有没有 target - b，即公式可以变成 b = target - a。

同样三数之和也可以用这个思路，既然是在数组中找出三元素 a + b + c = 0，那我们可以每次枚举 a，b，然后去数组中查询有无 -(a + b) 即可，即公式可以变成 c = -(a + b)。

通过两次 for 循环枚举 a 和 b 的值，然后用哈希在 O(1) 的时间去在一堆数中查找 -（a + b）。

思路其实说起来挺简单的，但是说实话，这道题用哈希来做的话，会把难度再提升一个 Level，不是在思路层面上，而是难在代码实现上。 

毕竟两重 for 循环的时间复杂度就已经到 O(n²) 了，而且这道题要求找出“不重复”的三元组，那就要求要考虑到去重这步操作。

一些细节方面稍不注意，就容易 Time Limit Error（超时）。

------

**图解**

以 nums = [-1,0,1,2,-1,-4] 为例。

在开始之前，先对 nums 进行排序，并初始化一个结果集合。

![fca0a01a957248ec926f3f52722da55](https://pic.leetcode-cn.com/1643162869-zHCXwE-20220103_095058373_0.jpg)

```java
# 对 nums 进行排序，这样在后面的时候可以减少重复带来的问题
nums.sort()
# res 存储最后的结果，set 集合的特点是无序且无重复
res = set()
```

之后就是双重 for 循环找 a，b。

第 1 步，i = 0，此时 a 即 nums[0] = -4，初始化哈希表 hash：

（1）j = 1，b 即 nums[1] = -1，此时 b = -1 不在哈希表中，将 -（a+b）= 5 加入哈希表中

<img src="https://pic.leetcode-cn.com/1643162869-urckIS-20220103_095134703_0.jpg" alt="59b7c2f546c1879bec51182422f2a12" style="zoom:80%;" />

```java
for i in range(len(nums)):
    # 初始化哈希表。
    hash = {}
    for j in range(i+1, len(nums)):
        # 如果不在哈希表中，加入哈希表
        if nums[j] not in hash:
            hash[-nums[i]-nums[j]] = 1
```

 这里我想特别说明一下，**为什么把 -(a + b) 加到哈希表中，判断的是 b 不在哈希表中。**这个也算是个小技巧吧。

```markdown
你想，如果 b 在哈希表中，那就证明此时 b 的值等于之前某一个 - (a + b) 的值，那就证明存在三个数相加等于 0。

为了看不晕，我把此时的 b 叫做 b1，之前 - (a + b) 中的 a 还是 a，b 还是 b。

那公式其实就成了 a + b + b1 = 0，即 b1 = -(a + b)。

这里的 b1 就是我们意义上的 c。
```

这里一定要仔细揣摩，很重要。

（2）j = 2，b 即 nums[2] = -1，此时 b = -1 不在哈希表中，将 -（a+b）= 5 加入 哈希表中（因为在哈希表中，值就为 1，而不是累加，所以和上图一样）。

<img src="https://pic.leetcode-cn.com/1643162869-vFnknm-20220103_095252191_0.jpg" alt="5fe8d44ad92e6da8c006d5333c8284e" style="zoom:80%;" />

直到 j = 5，都未发现等于零的三元组，此时数组及哈希表如下：

 ![18110a6500ed98bdf76415e6ab5f7f4](https://pic.leetcode-cn.com/1643162869-AfRuGQ-20220103_095343247_0.jpg)

第 2 步，i = 1，此时 a 即 nums[1] = -1，初始化哈希表 hash：

（1）j = 2，b 即 nums[2] = -1，此时 b = -1 不在哈希表中，将 -（a+b）= 2 加入哈希表中。

 ![af66ed6cf472630fb70ea1b93d58d5e](https://pic.leetcode-cn.com/1643162869-pxvpvd-20220103_095402422_0.jpg)

（2）j = 3，b 即 nums[3] = 0，此时 b = 0 不在哈希表中，所以将对应 -(a + b) = 1 加入哈希表中。

![b01b2534b750578d1663e6a72efd6d9](https://pic.leetcode-cn.com/1643162869-ZfsSyl-20220103_095419166_0.jpg)

（3）j = 4，b 即 nums[4] = 1，在哈希表中，此时的 a = nums[1] = -1，c = 此时的 b = 1，b = -(a + c) = 0。

```java
# 如果在哈希表中，则存在三元组
else:
    res.add((nums[i],-nums[i]-nums[j],nums[j]))
```

（4） j = 5，b 即 nums[5] = 2，在哈希表中，此时的 a = nums[1] = -1，c = 此时的 b = 2，b = -（a + c）= 1。

第 3 步，i = 2，此时 a 即 nums[2] = -1，此时 a 的值和 nums[1] 的值相同，直接跳过即可。

为啥子呢？

因为 a = nums[1] 的时候已经和后面的都玩过了，腻了，不需要再玩一遍（因为还是一样的结果）。

```java
# 元素去重

if i > 0 and nums[i] == nums[i -1]:
    continue
```

第 4 步，接下来的步骤和上面是一样的，答案已经全部找完，后面大家自己动手试一下。

Python版本：没看太懂！！！

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:

        # 如果 nums 个数 < 3，肯定组不成三元组
        if len(nums) < 3:
            return []
        # 对 nums 进行排序，这样在后面的时候可以减少重复带来的问题
        nums.sort()

        # res 存储最后的结果，set 集合的特点是无序且无重复
        res = set()

        for i in range(len(nums)):

            # 元素去重
            if i > 0 and nums[i] == nums[i -1]:
                continue

            # 初始化哈希表。
            hash = {}

            for j in range(i+1, len(nums)):

                # 如果不在哈希表中，加入哈希表
                if nums[j] not in hash:
                    hash[-nums[i]-nums[j]] = 1
                # 如果在哈希表中，则存在三元组
                else:
                    res.add((nums[i],-nums[i]-nums[j],nums[j]))

        return list(res)
```



### 方法三：代码随想录--哈希

两层for循环就可以确定 a 和b 的数值了，可以使用哈希法来确定 0-(a+b) 是否在 数组里出现过，其实这个思路是正确的，但是我们有一个非常棘手的问题，就是题目中说的不可以包含重复的三元组。

把符合条件的三元组放进vector中，然后再去重，这样是非常费时的，很容易超时，也是这道题目通过率如此之低的根源所在。

去重的过程不好处理，有很多小细节，如果在面试中很难想到位。

时间复杂度可以做到$O(n^2)$，但还是比较费时的，因为不好做剪枝操作。

大家可以尝试使用哈希法写一写，就知道其困难的程度了

C++版本：

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        // 找出a + b + c = 0
        // a = nums[i], b = nums[j], c = -(a + b)
        for (int i = 0; i < nums.size(); i++) {
            // 排序之后如果第一个元素已经大于零，那么不可能凑成三元组
            if (nums[i] > 0) {
                break;
            }
            if (i > 0 && nums[i] == nums[i - 1]) { //三元组元素a去重
                continue;
            }
            unordered_set<int> set;
            for (int j = i + 1; j < nums.size(); j++) {
                if (j > i + 2
                        && nums[j] == nums[j-1]
                        && nums[j-1] == nums[j-2]) { // 三元组元素b去重
                    continue;
                }
                int c = 0 - (nums[i] + nums[j]);
                if (set.find(c) != set.end()) {
                    result.push_back({nums[i], nums[j], c});
                    set.erase(c);// 三元组元素c去重
                } else {
                    set.insert(nums[j]);
                }
            }
        }
        return result;
    }
};
```



### 方法四： 双指针----代码随想录

**其实这道题目使用哈希法并不十分合适**，因为在去重的操作中有很多细节需要注意，在面试中很难直接写出没有bug的代码。

而且使用哈希法 在使用两层for循环的时候，能做的剪枝操作很有限，虽然时间复杂度是$O(n^2)$，也是可以在leetcode上通过，但是程序的执行时间依然比较长 。

接下来我来介绍另一个解法：双指针法，**这道题目使用双指针法 要比哈希法高效一些**，那么来讲解一下具体实现的思路。

动画效果：

![image-20220219215642408](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220219215642408.png)

![image-20220219215708471](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220219215708471.png)

https://github.com/youngyangyang04/leetcode-master/blob/master/problems/0015.%E4%B8%89%E6%95%B0%E4%B9%8B%E5%92%8C.md

------

拿这个nums数组来举例，==首先将数组排序，然后有一层for循环，i从下标0的地方开始，同时定一个下标left 定义在i+1的位置上，定义下标right 在数组结尾的位置上。==

依然还是在数组中找到 abc 使得a + b +c =0，我们这里相当于 a = nums[i] b = nums[left] c = nums[right]。

接下来如何移动left 和right呢， 如果nums[i] + nums[left] + nums[right] > 0 就说明 此时三数之和大了，因为数组是排序后了，所以right下标就应该向左移动，这样才能让三数之和小一些。

如果 nums[i] + nums[left] + nums[right] < 0 说明 此时 三数之和小了，left 就向右移动，才能让三数之和大一些，直到left与right相遇为止。

时间复杂度：$O(n^2)$。

------



## 思考题

既然三数之和可以使用双指针法，我们之前讲过的[1.两数之和](https://programmercarl.com/0001.两数之和.html)，可不可以使用双指针法呢？

如果不能，题意如何更改就可以使用双指针法呢？ **大家留言说出自己的想法吧！**

两数之和 就不能使用双指针法，因为[1.两数之和](https://programmercarl.com/0001.两数之和.html)要求返回的是索引下标， 而双指针法一定要排序，一旦排序之后原数组的索引就被改变了。

如果[1.两数之和](https://programmercarl.com/0001.两数之和.html)要求返回的是数值的话，就可以使用双指针法了。

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        // List列表
        List<List<Integer>> result = new ArrayList<>();
        // 对数组进行排序
        Arrays.sort(nums);
        
        // 对a进行循环枚举
        // 注意！！！！ 不是nums.length()
        for (int i = 0; i < nums.length; i++) {
            // 排序后，如果均大于0，那么及可以直接返回结果了
            if (nums[i] > 0) {
                return result;
            }
			// 如果连续两个枚举的数相同，就跳过
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
			
            // 定义left 和 right
            int left = i + 1;
            int right = nums.length - 1;
            
            // 内循环条件
            while (right > left) {
                //计算三个数的和
                int sum = nums[i] + nums[left] + nums[right];
                // 三个数之和大于0， right左移
                if (sum > 0) {
                    right--;
                    //三个数之和小于0 ,left右移
                } else if (sum < 0) {
                    left++;
                } else {
                    // 三数之和等于0,返回结果
                    // !!! 数组
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));
					// 继续移动right 指针，去掉重复的数
                    while (right > left && nums[right] == nums[right - 1]) right--;
                    //继续移动left指针，去掉重复的数
                    while (right > left && nums[left] == nums[left + 1]) left++;
                  
                    // 移动左右指针
                    right--; 
                    left++;
                }
            }
        }
        return result;
    }
}
```





### 补充List知识：

