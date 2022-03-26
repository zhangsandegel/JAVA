[TOC]

## 1.242 有效的字母异位词（数组）

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220216154931409.png" alt="image-20220216154931409" style="zoom:80%;" />

**说明:** 你可以假设字符串只包含小写字母。

### 方法一：暴力解法---排序

t 是 s 的异位词等价于**「两个字符串排序后相等」**。因此我们可以对字符串 s和 t 分别排序，**看排序后的字符串是否相等即可判断**。此外，如果 s 和 t的长度不同，t 必然不是 s 的异位词。

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        // s 和 t的长度不同，t必然不是s的异位词
        if (s.length() != t.length()) {
            return false;
        }
        //将两个字符串转化为数组
        // toCharArray() 方法将字符串转换为字符数组
        char[] str1 = s.toCharArray();
        char[] str2 = t.toCharArray();
        //对数组进行排序
        //Arrays.sort()是经过调优排序算法，性能能达到n*log(n)
        /*
        sort(T[] a)：对指定T型数组按数字升序排序。
		sort(T[] a,int formIndex, int toIndex)：对指定T型数组的指定范围按数字升序排序。
		sort(T[] a, Comparator<? supre T> c): 根据指定比较器产生的顺序对指定对象数组进行排序。
		sort(T[] a, int formIndex, int toIndex, Comparator<? supre T> c): 根据指定比较器产生的顺序对指定对象数组的指定对象数组进行排序。
        */
        Arrays.sort(str1);
        Arrays.sort(str2);
        //两个数组是否相等
        return Arrays.equals(str1, str2);
    }
}
```

------

**复杂度分析**

- 时间复杂度：O(nlogn)，其中 n为 s的长度。排序的时间复杂度为 O(nlogn)，比较两个字符串是否相等时间复杂度为 O(n)，因此总体时间复杂度为 O(nlogn+n)= O(nlogn)。

- 空间复杂度：O(logn)。排序需要O(logn) 的空间复杂度。注意，在某些语言（比如 Java & JavaScript）中字符串是不可变的，因此我们需要额外的 O(n) 的空间来拷贝字符串。但是我们忽略这一复杂度分析，因为：

这依赖于语言的细节；
这取决于函数的设计方式，例如，可以将函数参数类型更改为 char[]。 

### 方法二：数组作为哈希表

**数组其实就是一个简单哈希表**，而且这道题目中字符串只有小写字符，那么就可以定义一个数组，来记录字符串s里字符出现的次数。

### 知识补充

 **哈希表**

------

1. **数组其实就是一个简单哈希表**，而且这道题目中字符串只有小写字符，那么就可以定义一个数组，来记录字符串s里字符出现的次数。

2. 定一个数组叫做record，大小为26 就可以了，初始化为0，因为字符a到字符z的ASCII也是26个连续的数值。

3. 需要把字符映射到数组也就是哈希表的索引下标上，**因为字符a到字符z的ASCII是26个连续的数值，所以字符a映射为下标0，相应的字符z映射为下标25。**

   再遍历 字符串s的时候，**只需要将 s[i] - ‘a’ 所在的元素做+1 操作即可，并不需要记住字符a的ASCII，只要求出一个相对数值就可以了。** 这样就将字符串s中字符出现的次数，统计出来了。

4. 最后检查一下，**record数组如果有的元素不为零0，说明字符串s和t一定是谁多了字符或者谁少了字符，return false。**最后如果record数组所有元素都为零0，说明字符串s和t是字母异位词，return true。

------

Java 版本：

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220217213851904.png" alt="image-20220217213851904"  />

```java

class Solution {
    public boolean isAnagram(String s, String t) {

        int[] record = new int[26];
        // 字符循环的特点
        for (char c : s.toCharArray()) {
            record[c - 'a'] += 1;
        }
        for (char c : t.toCharArray()) {
            record[c - 'a'] -= 1;
        }
        //这一版本,更简单一点
        for (int i : record) {
            if (i != 0) {
                return false;
            }
        }
        return true;
    }
}
```

C++版本：

```c++

class Solution {
public:
    bool isAnagram(string s, string t) {
        int record[26] = {0};
        for (int i = 0; i < s.size(); i++) {
            // 并不需要记住字符a的ASCII，只要求出一个相对数值就可以了
            record[s[i] - 'a']++;
        }
        for (int i = 0; i < t.size(); i++) {
            record[t[i] - 'a']--;
        }
        for (int i = 0; i < 26; i++) {
            if (record[i] != 0) {
                // record数组如果有的元素不为零0，说明字符串s和t 一定是谁多了字符或者谁少了字符。
                return false;
            }
        }
        // record数组所有元素都为零0，说明字符串s和t是字母异位词
        return true;
    }
};
```

从另一个角度考虑，t是 s 的异位词等价于**「两个字符串中字符出现的种类和次数均相等」**。由于字符串只包含 26 个小写字母，因此我们可以维护一个长度为 26 的频次数组 table，先遍历记录字符串 s 中字符出现的频次，然后遍历字符串 t，减去 table 中对应的频次，如果出现table[i]<0，则说明 t 包含一个不在 s中的额外字符，返回false 即可。

```java
class Solution {
    //这个版本更复杂一点
    public boolean isAnagram(String s, String t) {
        // 判断两个字符串长度是否相等
        if (s.length() != t.length()) {
            return false;
        }
        //长度为26的频次数组
        int[] table = new int[26];
        //遍历记录字符串s中字符出现的频次
        for (int i = 0; i < s.length(); i++) {
            table[s.charAt(i) - 'a']++;
        }
        //遍历记录字符串t中字符出现的频次
        for (int i = 0; i < t.length(); i++) {
            table[t.charAt(i) - 'a']--;
            //如果出现table[i]<0，则说明 t 包含一个不在 s中的额外字符
            if (table[t.charAt(i) - 'a'] < 0) {
                return false;
            }
        }
        return true;
    }
}
```

 

对于进阶问题，Unicode 是为了解决传统字符编码的局限性而产生的方案，它为每个语言中的字符规定了一个唯一的二进制编码。**而Unicode 中可能存在一个字符对应多个字节的问题**，为了让计算机知道多少字节表示一个字符，面向传输的编码方式的 UTF − 8 和UTF−16 也随之诞生逐渐广泛使用，具体相关的知识读者可以继续查阅相关资料拓展视野，这里不再展开。

回到本题，进阶问题的核心点在于**「字符是离散未知的**」*，因此我们用哈希表维护对应字符的频次即可。同时读者需要注意Unicode 一个字符可能对应多个字节的问题，不同语言对于字符串读取处理的方式是不同的。

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        //这个是字符串的长度
        // nums1.length 是数组的长度
        if (s.length() != t.length()) {
            return false;
        }
        Map<Character, Integer> table = new HashMap<Character, Integer>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            table.put(ch, table.getOrDefault(ch, 0) + 1);
        }
        for (int i = 0; i < t.length(); i++) {
            char ch = t.charAt(i);
            table.put(ch, table.getOrDefault(ch, 0) - 1);
            if (table.get(ch) < 0) {
                return false;
            }
        }
        return true;
    }
} 
```

**复杂度分析**

- 时间复杂度：O(n)，其中 n为 s的长度。
- 空间复杂度：O(S)，其中 S 为字符集大小，此处 S=26。

## 2.349两个数组的交集（set）

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220217214129286.png" alt="image-20220217214129286" style="zoom:80%;" />

**说明：** 输出结果中的每个元素一定是唯一的。 我们可以不考虑输出结果的顺序。

###  思路

这道题目，主要要学会使用一种**哈希数据结构**：unordered_set，这个数据结构可以解决很多类似的问题。

注意题目特意说明：**输出结果中的每个元素一定是唯一的，也就是说输出的结果的去重的， 同时可以不考虑输出结果的顺序**

用数组来做哈希表也是不错的选择，例如[242. 有效的字母异位词 ] 

但是要注意，**使用数组来做哈希的题目，是因为题目都限制了数值的大小。**而这道题目没有限制数值的大小，就无法使用数组来做哈希表了。**而且如果哈希值比较少、特别分散、跨度非常大，使用数组就造成空间的极大浪费。**

此时就要使用另一种结构体了，set ，关于set，C++ 给提供了如下三种可用的数据结构：

- std::set
- std::multiset
- std::unordered_set

std::set和std::multiset底层实现都是红黑树，std::unordered_set的底层实现是哈希表， 使用unordered_set 读写效率是最高的，并不需要对数据进行排序，而且还不要让数据重复，所以选择unordered_set。

思路如图所示：

<img src="https://img-blog.csdnimg.cn/2020080918570417.png" alt="set哈希法" style="zoom:67%;" />

### 方法一：多定义了一个函数

------

计算两个数组的交集，直观的方法是遍历数组 nums1，对于其中的每个元素，遍历数组 nums2 判断该元素是否在数组 nums2 中，如果存在，则将该元素添加到返回值。

假设数组 nums1 和 nums2 的长度分别是 m和 n，则遍历数组 nums1 需要 O(m)的时间，判断 nums1 中的每个元素是否在数组 nums2 中需要 O(n)的时间，因此总时间复杂度是 O(mn)。

如果使用哈希集合存储元素，则可以在 O(1) 的时间内判断一个元素是否在集合中，从而降低时间复杂度。

**首先使用两个集合分别存储两个数组中的元素，然后遍历较小的集合，判断其中的每个元素是否在另一个集合中，如果元素也在另一个集合中，则将该元素添加到返回值。**该方法的时间复杂度可以降低到 O(m+n)。

------

Java 版本：

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        // 定义两个set
        Set<Integer> set1 = new HashSet<Integer>();
        Set<Integer> set2 = new HashSet<Integer>();
        // 遍历两个数组，去重
        for (int num : nums1) {
            set1.add(num);
        }
        for (int num : nums2) {
            set2.add(num);
        }
        //调用一个函数，返回结果
        return getIntersection(set1, set2);
    }
	
    // 返回结果数组
    public int[] getIntersection(Set<Integer> set1, Set<Integer> set2) {
        // set1长度要小于set2长度
        if (set1.size() > set2.size()) {
            return getIntersection(set2, set1);
        }
        // 定义一个新的迭代集合
        Set<Integer> intersectionSet = new HashSet<Integer>();
        
        // 记录两个集合的交集
        for (int num : set1) {
            if (set2.contains(num)) {
                intersectionSet.add(num);
            }
        }
        
        // 定义一个数组，返回结果
        int[] intersection = new int[intersectionSet.size()];
        int index = 0;
        for (int num : intersectionSet) {
            intersection[index++] = num;
        }
        return intersection;
    }
}
```

复杂度分析

- 时间复杂度：O(m+n)，其中 m和 n分别是两个数组的长度。使用两个集合分别存储两个数组中的元素需要 O(m+n) 的时间，遍历较小的集合并判断元素是否在另一个集合中需要 O(min(m,n)) 的时间，因此总时间复杂度是 O(m+n)。


- 空间复杂度：O(m+n)，其中 m 和 n 分别是两个数组的长度。空间复杂度主要取决于两个集合。

### 方法二：set作为哈希表

Java 版本：

```java
import java.util.HashSet;
import java.util.Set;
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        // 如果其中一个数组为空，或者数组长度为0，则说明两者没有交集，直接返回一个新的空数组
        if (nums1 == null || nums1.length == 0 || nums2 == null || nums2.length == 0) {
            return new int[0];
        }
        // 定义两个set集合
        Set<Integer> set1 = new HashSet<>(); 
        Set<Integer> resSet = new HashSet<>(); 
        //遍历数组1
        for (int i : nums1) {
            set1.add(i);  //将数组1的元素去重
        }
        //遍历数组2的过程中判断哈希表中是否存在该元素
        for (int i : nums2) {
            if (set1.contains(i)) {
                resSet.add(i); // 记录两个数组的交集
            }
        }
        // 将结果转换为数组输出
        int[] resArr = new int[resSet.size()];
        int index = 0;
        //将结果几何转为数组
        for (int i : resSet) {
            resArr[index++] = i;
        }
        return resArr; //可以直接返回一个数组
    }
}
```

C++ 版本：

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> result_set; // 存放结果
        unordered_set<int> nums_set(nums1.begin(), nums1.end());
        for (int num : nums2) {
            // 发现nums2的元素 在nums_set里又出现过
            if (nums_set.find(num) != nums_set.end()) {
                result_set.insert(num);
            }
        }
        return vector<int>(result_set.begin(), result_set.end());
    }
};
```

### 方法三： 排序 + 双指针

如果两个数组是有序的，则可以使用双指针的方法得到两个数组的交集。

首先对两个数组进行排序，然后使用两个指针遍历两个数组。可以预见的是加入答案的数组的元素一定是递增的，为了保证加入元素的唯一性，我们需要额外记录变量pre 表示上一次加入答案数组的元素。

初始时，两个指针分别指向两个数组的头部。每次比较两个指针指向的两个数组中的数字，如果两个数字不相等，则将指向较小数字的指针右移一位，如果两个数字相等，且该数字不等于 pre ，将该数字添加到答案并更新 pre 变量，同时将两个指针都右移一位。当至少有一个指针超出数组范围时，遍历结束。

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        // 对两个数组进行排序
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        int length1 = nums1.length, length2 = nums2.length;
        // 最终的数组
        int[] intersection = new int[length1 + length2];
        // index1 和 index2 是两个指针
        int index = 0, index1 = 0, index2 = 0;
        
        // 指针遍历两个数组
        // ! ! ! 是< 不是 <=
        while (index1 < length1 && index2 < length2) {
            // 获取指针当前所指向的两个数
            int num1 = nums1[index1], num2 = nums2[index2];
            // 如果两个数相等，且不等于 pre
            if (num1 == num2) {
                // 保证加入元素的唯一性
                if (index == 0 || num1 != intersection[index - 1]) {
                    intersection[index++] = num1;
                }
                // 两个数组都往后移动
                index1++;
                index2++;
            } else if (num1 < num2) {  // 较小的数，其数组指针往后面移动一位
                index1++;
            } else {
                index2++;
            }
        }
        //输出数组
        return Arrays.copyOfRange(intersection, 0, index);
    }
} 
```

------

1. Arrays.copyOfRange(original,int from,int to)该方法是从original数组的下标from开始赋值，到下标to结束，不包括下标为to的元素。 

2. Arrays.copyOfRange(n, 0, 5);意思是返回一个长度为to-from即5-0=5的数组，从n数组下标0开始复制，由于n数组只有一个元素，故复制一个就没有可以复制的，剩下的4个元素默认为0。

3. Arrays.copyOfRange(n, 0, 3);的意思是返回一个长的为3-0=3的数组，从n数组0开始复制，由于数组n的长度大于3，因此直接从n数组0下标开始复制3个元素返回即可。

   ------

   

复杂度分析

- 时间复杂度：O(m log m+nlogn)，其中 m 和 n 分别是两个数组的长度。对两个数组排序的时间复杂度分别是 O(m log m)和 O(n log n)，双指针寻找交集元素的时间复杂度是 O(m+n)，因此总时间复杂度是 O(m log m+n log n)


- 空间复杂度：O(log m+log n)，其中 m 和 n分别是两个数组的长度。空间复杂度主要取决于排序使用的额外空间。

## 3. 1两数之和（map)

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220216154521595.png" alt="、" style="zoom:80%;" />

### 方法一：暴力枚举

**思路及算法**

最容易想到的方法是枚举数组中的每一个数 x，寻找数组中是否存在 target - x。

当我们使用遍历整个数组的方式寻找 target - x 时，需要注意到每一个位于 x 之前的元素都已经和 x 匹配过，因此不需要再进行匹配。而每一个元素不能被使用两次，所以我们只需要在 x 后面的元素中寻找 target - x。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (nums[i] + nums[j] == target) {
                    //!!!这种返回数组的形式，十分值得学习
                    return new int[]{i, j};
                }
            }
        }
        return new int[0];
    }
} 
```

### 方法二：map作为哈希表

来看一下使用数组和set来做哈希法的局限。

- 数组的大小是受限制的，而且如果元素很少，而哈希值太大会造成内存空间的浪费。
- set是一个集合，里面放的元素只能是一个key，而两数之和这道题目，不仅要判断y是否存在而且还要记录y的下标位置，因为要返回x 和 y的下标。所以set 也不能用。

此时就要选择另一种数据结构：map ，**map是一种key value的存储结构，可以用key保存数值，用value在保存数值所在的下标。**

C++中map，有三种类型：

| 映射               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::map           | 红黑树   | key有序  | key不可重复      | key不可修改  | O(log n) | O(log n) |
| std::multimap      | 红黑树   | key有序  | key可重复        | key不可修改  | O(log n) | O(log n) |
| std::unordered_map | 哈希表   | key无序  | key不可重复      | key不可修改  | O(1)     | O(1)     |

std::unordered_map 底层实现为哈希表，std::map 和std::multimap 的底层实现是红黑树。

同理，std::map 和std::multimap 的key也是有序的（这个问题也经常作为面试题，考察对语言容器底层的理解）。

**这道题目中并不需要key有序，选择std::unordered_map 效率更高！**

------

注意到方法一的时间复杂度较高的原因是寻找 target - x 的时间复杂度过高。因此，我们需要一种更优秀的方法，能够快速寻找数组中是否存在目标元素。如果存在，我们需要找出它的索引。

使用哈希表，可以将寻找 target - x 的时间复杂度降低到从 O(N)O(N) 降低到 O(1)O(1)。

这样我们创建一个哈希表，对于每一个 x，我们首先查询哈希表中是否存在 target - x，然后将 x 插入到哈希表中，即可保证不会让 x 和自己匹配。

------

Java版本：

```java
public int[] twoSum(int[] nums, int target) {
    //定义一个返回的数组
    int[] res = new int[2];
    //感觉这个不需要判断也是可以的？？
    if(nums == null || nums.length == 0){
        //如果数组为空，或者数组的数值为null，直接返回一个res
        return res;
    }
    // map数据结构
    Map<Integer, Integer> map = new HashMap<>();
    //循环遍历数组nums
    for(int i = 0; i < nums.length; i++){
        // 定义一个临时变量
        int temp = target - nums[i];
        //如果map中存在temp，满足temp + nums[i] = target
        if(map.containsKey(temp)){
            // 返回两个对应的下标
            res[1] = i;
            res[0] = map.get(temp); //如果存在，肯定时已经放入到map里面了
        }
        //将每一个数值和它对应的数组下标，存入map中去
        map.put(nums[i], i);
    }
    // 否则直接返回一个res
    return res;
}
```

Java版本：这个更简洁

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
       //定义一个Map容器
        Map<Integer, Integer> hashtable = new HashMap<Integer, Integer>();
        for (int i = 0; i < nums.length; ++i) {
            if (hashtable.containsKey(target - nums[i])) {
                return new int[]{hashtable.get(target - nums[i]), i}; //get的是value值
            }
            //存入map容器中
            hashtable.put(nums[i], i);
        }
        //否则返回一个空数组 
        //!!! 这个和上一个不太一样，很值得品味
        return new int[0];
    }
} 
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N是数组中的元素数量。对于每一个元素 x，我们可以 O(1)地寻找 target - x。


- 空间复杂度：O(N)，其中 N是数组中的元素数量。主要为哈希表的开销。


 