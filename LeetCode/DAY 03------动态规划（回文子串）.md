[TOC]

## 1.647回文子串



<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220218163930481.png" alt="image-20220218163930481"  />

Python版本：

```python
class Solution:
    def countSubstrings(self, s):
        #aaababa #单数中心，双数中心 #aaaa
        # ababa #a,bab,ababa 
        # 定义一个函数
        def speard(l,r):
            count=0
            # 左右边界满足条件，并且 左边 = 右边
            while l>=0 and r<=len(s)-1 and s[l]==s[r]:
                l-=1
                r+=1
                count+=1
            return count
        # 定义一个结果
        ans=0
        # 单数中心
        for i in range(len(s)):
            ans+=speard(i,i)
        # 双数中心
        for i in range(len(s)-1): # -1 是为了防止溢出
            ans+=speard(i,i+1)
        return ans
```

### 方法0：暴力解法

两层for循环，遍历区间起始位置和终止位置，然后判断这个区间是不是回文。

时间复杂度：$O(n^3)$

### 方法一：中心拓展

**思路与算法**

计算有多少个回文子串的最朴素方法就是**枚举出所有的回文子串**，而枚举出所有的回文字串又有两种思路，分别是：

- 枚举出所有的子串，然后再判断这些子串是否是==回文==*==（正过来和倒过来读是一样的字符串）==*；
- 枚举每一个可能的==回文中心==，然后用两个指针分别向左右两边拓展，当两个指针指向的元素相同的时候就拓展，否则停止拓展。

 假设字符串的长度为 n。

1. 我们可以看出前者会用 ==O($n^2$)==的时间枚举出所有的子串S[$l_i$ $⋯r_i$]，然后再用 ==O($r_i$ - $l_i$ + 1)== 的时间检测当前的子串是否是回文，整个算法的时间复杂度是 O($n^3$)。
2. 而==*后者枚举回文中心的是 O(n)的*==，对于每个回文中心拓展的次数也是 O(n)的，所以时间复杂度是 O($n^2$)。所以我们选择第二种方法来枚举所有的回文子串。

- 比如对一个字符串 ==ababa==，选择最中间的 a 作为中心点，往两边扩散，第一次扩散发现 left 指向的是 b，right 指向的也是 b，所以是回文串，继续扩散，同理 ababa 也是回文串。这个是确定了一个中心点后的寻找的路径，然后我们只要寻找到所有的中心点，问题就解决了。
- ==中心点一共有多少个呢==？看起来像是和字符串长度相等，但你会发现，如果是这样，上面的例子永远也搜不到 abab，想象一下单个字符的哪个中心点扩展可以得到这个子串？似乎不可能。==所以中心点不能只有单个字符构成，还要包括两个字符==，比如上面这个子串 abab，就可以有中心点 ba 扩展一次得到，所以最终的中心点由 2 * len - 1 个，分别是 len 个单字符和 len - 1 个双字符。
  - [ ] 如果上面看不太懂的话，还可以看看下面几个问题：
    1. 为什么有 2 * len - 1 个中心点？
       aba 有5个中心点，分别是 a、b、a、ab、ba
       abba 有7个中心点，分别是 a、b、b、a、ab、bb、ba
    2. 什么是==中心点==？
       中心点即 left 指针和 right 指针初始化指向的地方，可能是一个也可能是两个
    3. 为什么不可能是三个或者更多？
       因为 3 个可以由 1 个扩展一次得到，4 个可以由两个扩展一次得到

在实现的时候，我们需要处理一个问题，即如何<u>==有序地枚举所有可能的回文中心==</u>，我们需要考虑回文长度是奇数和回文长度是偶数的两种情况。

1. 如果回文长度是奇数，那么回文中心是一个字符；

2. 如果回文长度是偶数，那么中心是两个字符。

   当然你可以做两次循环来分别枚举奇数长度和偶数长度的回文，但是我们也可以用一个循环搞定。我们不妨写一组出来观察观察，假设 n = 4 ，abad我们可以把可能的回文中心列出来：

   | 编号 i | 回文中心左起始位置 $l_i$ | 回文中心右起始位置 $r_i$ |
   | ------ | ------------------------ | ------------------------ |
   | 0      | 0                        | 0                        |
   | 1      | 0                        | 1                        |
   | 2      | 1                        | 1                        |
   | 3      | 1                        | 2                        |
   | 4      | 2                        | 2                        |
   | 5      | 2                        | 3                        |
   | 6      | 3                        | 3                        |

    由此我们可以看出长度为 n 的字符串会生成 2n-1 组回文中心 [$l_i$, $r_i$]，其中 $l_i$ =  ⌊ $i/2$⌋，$r_i$ = $l_i$ + (imod2)。这样我们只要从 ==0到 2n - 2遍历 i==，就可以得到所有可能的回文中心，==这样就把奇数长度和偶数长度两种情况统一起来了==。

   Java版本： 

   ```java
   class Solution {
       public int countSubstrings(String s) {
           int n = s.length(), ans = 0;
           // 遍历所有的回文中心，并将回文中心，奇数长度和偶数长度 统一起来
           for (int i = 0; i < 2 * n - 1; ++i) {
               // 左指针 右指针，找规律看出来的
               int l = i / 2, r = i / 2 + i % 2;
               //满足中心拓展的条件
               while (l >= 0 && r < n && s.charAt(l) == s.charAt(r)) {
                   // 进行中心拓展
                   --l;
                   ++r;
                   //进行一次拓展，说明存在一个回文子字符串，最终的结果+1
                   ++ans;
               }
           }
           return ans;
       }
   }
   ```

   **复杂度分析**

   - 时间复杂度：O($n^2$) 。
   - 空间复杂度：O(1) 。 

java版本：代码随想录---其实都差不多

```java
class Solution {
    public int countSubstrings(String s) {
        int len, ans = 0;
        if (s == null || (len = s.length()) < 1) return 0;
        //总共有2 * len - 1个中心点
        for (int i = 0; i < 2 * len - 1; i++) {
            //通过遍历每个回文中心，向两边扩散，并判断是否回文字串
            //有两种情况，left == right，right = left + 1，这两种回文中心是不一样的
            int left = i / 2, right = left + i % 2;
            while (left >= 0 && right < len && s.charAt(left) == s.charAt(right)) {
                //如果当前是一个回文串，则记录数量
                ans++;
                left--;
                right++;
            }
        }
        return ans;
    }
}
```

### 方法二：动态规划

------

动态规划五部曲：

1. 确定dp数组（dp table）以及下标的含义

   ```markdown
   布尔类型的dp[i][j]：表示区间范围[i,j] （注意是左闭右闭）的子串是否是回文子串，如果是dp[i][j]为true，否则为false。
   ```

2. 确定递推公式

   ```markdown
   在确定递推公式时，就要分析如下几种情况。
   整体上是两种，就是s[i]与s[j]相等，s[i]与s[j]不相等这两种。
   
   1. 当s[i]与s[j]不相等，那没啥好说的了，dp[i][j]一定是false。
   2. 当s[i]与s[j]相等时，这就复杂一些了，有如下三种情况
   	情况一：下标i 与 j相同，同一个字符例如a，当然是回文子串
   	情况二：下标i 与 j相差为1，例如aa，也是文子串
   	情况三：下标：i 与 j相差大于1的时候
   	例如cabac，此时s[i]与s[j]已经相同了，我们看i到j区间是不是回文子串就看aba是不是回文就可以了，那么aba的区间就是 i+1 与 j-1区间，这个区间是不是回文就看dp[i + 1][j - 1]是否为true。
   以上三种情况分析完了，那么递归公式如下：
   ```

   ```java
   if (s[i] == s[j]) {
       if (j - i <= 1) { // 情况一 和 情况二
           result++;
           dp[i][j] = true;
       } else if (dp[i + 1][j - 1]) { // 情况三
           result++;
           dp[i][j] = true;
       }
   }
   ```

   result就是统计回文子串的数量。注意这里我没有列出当s[i]与s[j]不相等的时候，因为在下面dp$[i][j]$初始化的时候，就初始为false。

3. dp数组如何初始化

   ```markdown
   dp[i][j]可以初始化为true么？ 当然不行，怎能刚开始就全都匹配上了。
   所以dp[i][j]初始化为false。
   ```

4. 确定遍历顺序

   ```markdown
   遍历顺序可有有点讲究了。
   
   首先从递推公式中可以看出，情况三是根据dp[i + 1][j - 1]是否为true，在对dp[i][j]进行赋值true的。
   
   dp[i + 1][j - 1] 在 dp[i][j]的左下角，如图：
   ```

   <img src="https://img-blog.csdnimg.cn/20210121171032473.jpg" alt="647.回文子串" style="zoom:67%;" />

   ```markdown
   如果这矩阵是从上到下，从左到右遍历，那么会用到没有计算过的dp[i + 1][j - 1]，也就是根据不确定是不是回文的区间[i+1,j-1]，来判断了[i,j]是不是回文，那结果一定是不对的。
   
   所以一定要从下到上，从左到右遍历，这样保证dp[i + 1][j - 1]都是经过计算的。
   
   有的代码实现是优先遍历列，然后遍历行，其实也是一个道理，都是为了保证dp[i + 1][j - 1]都是经过计算的。
   ```

   ```java
   // i从大到小，即从下到上遍历
   for (int i = s.size() - 1; i >= 0; i--) {  // 注意遍历顺序
       // j从小到大，即从左到右遍历
       for (int j = i; j < s.size(); j++) {
           // 递推公式的几种情况
           if (s[i] == s[j]) {
               if (j - i <= 1) { // 情况一 和 情况二
                   result++;
                   dp[i][j] = true;
               } else if (dp[i + 1][j - 1]) { // 情况三
                   result++;
                   dp[i][j] = true;
               }
           }
       }
   }
   ```

5. 举例推导dp数组

   举例，输入："aaa"，dp$[i][j]$状态如下：

   <img src="https://img-blog.csdnimg.cn/20210121171059951.jpg" alt="647.回文子串1" style="zoom:80%;" />

   图中有6个true，所以就是有6个回文子串。

   **注意因为dp$[i][j]$的定义，所以j一定是大于等于i的，那么在填充dp$[i][j]$的时候一定是只填充右上半部分**

   C++版本：

   ```c++
   class Solution {
   public:
       int countSubstrings(string s) {
           vector<vector<bool>> dp(s.size(), vector<bool>(s.size(), false));
           int result = 0;
           for (int i = s.size() - 1; i >= 0; i--) {  // 注意遍历顺序
               for (int j = i; j < s.size(); j++) {
                   if (s[i] == s[j]) {
                       if (j - i <= 1) { // 情况一 和 情况二
                           result++;
                           dp[i][j] = true;
                       } else if (dp[i + 1][j - 1]) { // 情况三
                           result++;
                           dp[i][j] = true;
                       }
                   }
               }
           }
           return result;
       }
   };
   ```

   以上代码是为了凸显情况一二三，当然是可以简洁一下的，如下：

   ```c++
   class Solution {
   public:
       int countSubstrings(string s) {
           vector<vector<bool>> dp(s.size(), vector<bool>(s.size(), false));
           int result = 0;
           for (int i = s.size() - 1; i >= 0; i--) {
               for (int j = i; j < s.size(); j++) {
                   if (s[i] == s[j] && (j - i <= 1 || dp[i + 1][j - 1])) {
                       result++;
                       dp[i][j] = true;
                   }
               }
           }
           return result;
       }
   };
   ```

   - 时间复杂度：$O(n^2)$
   - 空间复杂度：$O(n^2)$

Java版本：

```java
class Solution {
    public int countSubstrings(String s) {
        int len=s.length(), ans = 0;
        // 特殊情况
        if (s == null || (len = s.length()) < 1) return 0;
        //dp[i][j]：s字符串下标i到下标j的字串是否是一个回文串，即s[i, j]
        boolean[][] dp = new boolean[len][len];

    //  注意遍历顺序
        for (int i = s.length()-1; i>=0   ; i--) {
            for (int j = i; j < s.length(); j++) {
                //当两端字母一样时，才可以两端收缩进一步判断
                if (s.charAt(i) == s.charAt(j)) {
                    //i++，j--，即两端收缩之后i,j指针指向同一个字符或者i超过j了,必然是一个回文串
                    if (j - i <= 1 ) {
                        ans++;
                        dp[i][j] = true;
                    } else if(dp[i + 1][j - 1]){
                         ans++;
                         dp[i][j] = true;
                    }
                }
            }
        }
        return ans;
    }      
}
```

更简单一点：

```java
class Solution {
    public int countSubstrings(String s) {
        int len=s.length(), ans = 0;
        // 特殊情况
        if (s == null || (len = s.length()) < 1) return 0;
        //dp[i][j]：s字符串下标i到下标j的字串是否是一个回文串，即s[i, j]
        boolean[][] dp = new boolean[len][len];

    //  注意遍历顺序
        for (int i = s.length()-1; i>=0   ; i--) {
            for (int j = i; j < s.length(); j++) {
                //当两端字母一样时，才可以两端收缩进一步判断
                if (s.charAt(i) == s.charAt(j) && (j - i <= 1 || dp[i + 1][j - 1])) {
                    //i++，j--，即两端收缩之后i,j指针指向同一个字符或者i超过j了,必然是一个回文串
                         ans++;
                         dp[i][j] = true;
                    }
                }
        }
        return ans;
    }      
}
```



### 方法三： 官方解答

首先这一题可以使用动态规划来进行解决：

- 状态：dp$[i][j]$ 表示字符串s在[i,j]区间的子串是否是一个回文串。
- ==<u>状态转移方程：当 s[i] == s[j] && (j - i < 2 || dp$[i + 1][j - 1]$) 时</u>==，dp$[i][j]$=true，否则为false

这个状态转移方程是什么意思呢？

1. 当只有一个字符时，比如 a 自然是一个回文串。
2. 当有两个字符时，如果是相等的，比如 aa，也是一个回文串。
3. 当有三个及以上字符时，比如 ababa 这个字符记作串 1，把两边的 a 去掉，也就是 bab 记作串 2，可以看出只要串2是一个回文串，那么左右各多了一个 a 的串 1 必定也是回文串。所以当 s[i]==s[j] 时，自然要看 dp$[i+1][j-1]$ 是不是一个回文串。

Java版本： 

```java
class Solution {
    public int countSubstrings(String s) {
        // 动态规划法
        boolean[][] dp = new boolean[s.length()][s.length()];
        int ans = 0;
        //感觉动态规划，解决方法很简单，但是不是很容易想得到
        for (int j = 0; j < s.length(); j++) {
            for (int i = 0; i <= j; i++) {
                if (s.charAt(i) == s.charAt(j) && (j - i < 2 || dp[i + 1][j - 1])) {
                    dp[i][j] = true;
                    ans++;
                }
            }
        }
        return ans;
    }
}
```

这个方法的时间复杂度是 O($N^2$) ，空间复杂度是 O(1) 。

## 2.5最长回文子串

<img src="C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220219162215984.png" alt="image-20220219162215984" style="zoom:80%;" />

###  方法一：中心拓展

```java
class Solution {
    public String longestPalindrome(String s) {
        // ababa 求最长公共子串
        int len = s.length();
        String result = "";

        for (int i = 0; i < len * 2 - 1; i++) {
            int left = i / 2;
            int right = left + i % 2;
            while (left >= 0 && right < len && s.charAt(left) == s.charAt(right)) {
                String tmp = s.substring(left, right + 1);
                if (tmp.length() > result.length()) {
                    result = tmp;
                }
                left--;
                right++;
            }
        }
        return result;
    }
} 
```

### 方法二：动态规划

------

动规五部曲：

1. 确定dp数组（dp table）以及下标的含义

```
布尔类型的dp[i][j]：表示区间范围[i,j] （注意是左闭右闭）的子串是否是回文子串，如果是dp[i][j]为true，否则为false。
```

2. 确定递推公式

```
在确定递推公式时，就要分析如下几种情况。

整体上是两种，就是s[i]与s[j]相等，s[i]与s[j]不相等这两种。

1. 当s[i]与s[j]不相等，那没啥好说的了，dp[i][j]一定是false。

2. 当s[i]与s[j]相等时，这就复杂一些了，有如下三种情况

	情况一：下标i 与 j相同，同一个字符例如a，当然是回文子串
	情况二：下标i 与 j相差为1，例如aa，也是文子串
	情况三：下标：i 与 j相差大于1的时候，例如cabac，此时s[i]与s[j]已经相同了，我们看i到j区间是不是回文子串就看aba是不是回文就可以了，那么aba的区间就是 i+1 与 j-1区间，这个区间是不是回文就看dp[i + 1][j - 1]是否为true。
```

以上三种情况分析完了，那么递归公式如下：

```java
if (s[i] == s[j]) {
    if (j - i <= 1) { // 情况一 和 情况二
        dp[i][j] = true;
    } else if (dp[i + 1][j - 1]) { // 情况三
        dp[i][j] = true;
    }
}
```

注意这里我没有列出当s[i]与s[j]不相等的时候，因为在下面dp[i][j]初始化的时候，就初始为false。

在得到[i,j]区间是否是回文子串的时候，直接保存最长回文子串的左边界和右边界，代码如下：

```java
if (s[i] == s[j]) {
    if (j - i <= 1) { // 情况一 和 情况二
        dp[i][j] = true;
    } else if (dp[i + 1][j - 1]) { // 情况三
        dp[i][j] = true;
    }
}
if (dp[i][j] && j - i + 1 > maxlenth) {
    maxlenth = j - i + 1;
    left = i;
    right = j;
}
```

3. dp数组如何初始化

```
dp[i][j]可以初始化为true么？ 当然不行，怎能刚开始就全都匹配上了。

所以dp[i][j]初始化为false。确定遍历顺序

遍历顺序可有有点讲究了。

首先从递推公式中可以看出，情况三是根据dp[i + 1][j - 1]是否为true，在对dp[i][j]进行赋值true的。

dp[i + 1][j - 1] 在 dp[i][j]的左下角，如图：
```



[![647.回文子串](https://camo.githubusercontent.com/9cb222226e85be9b2772d59cbb1c7e1e0f3d263baf96134b8c0661008b56952c/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303231303132313137313033323437332e6a7067)](https://camo.githubusercontent.com/9cb222226e85be9b2772d59cbb1c7e1e0f3d263baf96134b8c0661008b56952c/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303231303132313137313033323437332e6a7067)

```
如果这矩阵是从上到下，从左到右遍历，那么会用到没有计算过的dp[i + 1][j - 1]，也就是根据不确定是不是回文的区间[i+1,j-1]，来判断了[i,j]是不是回文，那结果一定是不对的。

**所以一定要从下到上，从左到右遍历，这样保证dp[i + 1][j - 1]都是经过计算的**。

有的代码实现是优先遍历列，然后遍历行，其实也是一个道理，都是为了保证dp[i + 1][j - 1]都是经过计算的。
```

代码如下：

```java
for (int i = s.size() - 1; i >= 0; i--) { // 注意遍历顺序
    for (int j = i; j < s.size(); j++) {
        if (s[i] == s[j]) {
            if (j - i <= 1) { // 情况一 和 情况二
                dp[i][j] = true;
            } else if (dp[i + 1][j - 1]) { // 情况三
                dp[i][j] = true;
            }
        }
        if (dp[i][j] && j - i + 1 > maxlenth) {
            maxlenth = j - i + 1;
            left = i;
            right = j;
        }
    }

}
```

4. 举例推导dp数组

举例，输入："aaa"，dp$[i][j$]状态如下：

[![647.回文子串1](https://camo.githubusercontent.com/6ccc0bab6ea0416fa723e9290cfb0ac19b054bd203fa13d436bea7caf123a203/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303231303132313137313035393935312e6a7067)](https://camo.githubusercontent.com/6ccc0bab6ea0416fa723e9290cfb0ac19b054bd203fa13d436bea7caf123a203/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303231303132313137313035393935312e6a7067)

**注意因为dp$[i][j]$的定义，所以j一定是大于等于i的，那么在填充dp$[i][j]$的时候一定是只填充右上半部分**。

以上分析完毕，C++代码如下：

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        vector<vector<int>> dp(s.size(), vector<int>(s.size(), 0));
        int maxlenth = 0;
        int left = 0;
        int right = 0;
        for (int i = s.size() - 1; i >= 0; i--) {
            for (int j = i; j < s.size(); j++) {
                if (s[i] == s[j]) {
                    if (j - i <= 1) { // 情况一 和 情况二
                        dp[i][j] = true;
                    } else if (dp[i + 1][j - 1]) { // 情况三
                        dp[i][j] = true;
                    }
                }
                if (dp[i][j] && j - i + 1 > maxlenth) {
                    maxlenth = j - i + 1;
                    left = i;
                    right = j;
                }
            }

        }
        return s.substr(left, right - left + 1);
    }
};
```

以上代码是为了凸显情况一二三，当然是可以简洁一下的，如下：

```C++
class Solution {
public:
    string longestPalindrome(string s) {
        vector<vector<int>> dp(s.size(), vector<int>(s.size(), 0));
        int maxlenth = 0;
        int left = 0;
        int right = 0;
        for (int i = s.size() - 1; i >= 0; i--) {
            for (int j = i; j < s.size(); j++) {
                if (s[i] == s[j] && (j - i <= 1 || dp[i + 1][j - 1])) {
                    dp[i][j] = true;
                }
                if (dp[i][j] && j - i + 1 > maxlenth) {
                    maxlenth = j - i + 1;
                    left = i;
                    right = j;
                }
            }
        }
        return s.substr(left, maxlenth);
    }
};
```

- 时间复杂度：$O(n^2)$
- 空间复杂度：$O(n^2)$

java版本：

```java
public String longestPalindrome(String s) {
        if (s == null || s.length() < 2) {
            return s;
        }
        int strLen = s.length();
        int maxStart = 0;  //最长回文串的起点
        int maxEnd = 0;    //最长回文串的终点
        int maxLen = 1;  //最长回文串的长度

        boolean[][] dp = new boolean[strLen][strLen];

        for (int r = 1; r < strLen; r++) {
            for (int l = 0; l < r; l++) {
                if (s.charAt(l) == s.charAt(r) && (r - l <= 2 || dp[l + 1][r - 1])) {
                    dp[l][r] = true;
                    if (r - l + 1 > maxLen) {
                        maxLen = r - l + 1;
                        maxStart = l;
                        maxEnd = r;

                    }
                }

            }

        }
        return s.substring(maxStart, maxEnd + 1);

    } 
```

### 补充Java String类：

substring() 方法返回的子串包括 *start* 处的字符，但不包括 *stop* 处的字符。

如果参数 *start* 与 *stop* 相等，那么该方法返回的就是一个空串（即长度为 0 的字符串）。如果 *start* 比 *stop* 大，那么该方法在提取子串之前会先交换这两个参数。

- **beginIndex** -- 起始索引（包括）, 索引从 0 开始。

- **endIndex** -- 结束索引（不包括）。

  ```java
  public String substring(int beginIndex)
  
  或
  
  public String substring(int beginIndex, int endIndex)
  ```

 ![img](https://www.runoob.com/wp-content/uploads/2016/05/java-substring-20201208.png)

```
返回值 : is  is
返回值 : is is text
```

### 补充Java Boolean()类：

boolean是基本数据类型
Boolean是它的封装类，和其他类一样，有属性有方法，可以new，例如：

```
Boolean bl= new Boolean("true"); // boolean 则不可以！
```

Boolean 是boolean 的实例化对象类，和Integer对应int一样



| 方法                   | 返回值  | 功能                                                         |
| ---------------------- | ------- | ------------------------------------------------------------ |
| booleanValue()         | boolean | 将 Boolean 对象的值以对应的 boolean 值返回                   |
| equals(Object obj)     | boolean | 判断调用该方法的对象与 obj 是否相等。当且仅当参数不是 null，且与调用该 方法的对象一样都表示同一个 boolean 值的 Boolean 对象时，才返回 true |
| parseBoolean(String s) | boolean | 将字符串参数解析为 boolean 值                                |
| toString()             | string  | 返回表示该 boolean 值的 String 对象                          |
| valueOf(String s)      | boolean | 返回一个用指定的字符串表示的 boolean 值                      |
