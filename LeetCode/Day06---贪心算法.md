

[TOC]

# 2.2 ---分配问题

## **01，455分发饼干**

![image-20220311234143305](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311234143305.png)

![image-20220311234158920](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220311234158920.png)

### 方法一：排序 + 贪心

 ![image-20220312000439043](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220312000439043.png) ![image-20220312000457329](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220312000457329.png)

 Java代码：

```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        // 对g 和 s 两个数组，进行排序
        Arrays.sort(g);
        Arrays.sort(s);
        //获取数组长度
        int numOfChildren = g.length, numOfCookies = s.length;
        int count = 0;
        // 遍历循环 
        // i: 第i个小孩   j: 第j块饼干
        for (int i = 0, j = 0; i < numOfChildren && j < numOfCookies; i++, j++) {
            // 遍历小孩数组g[]中的元素, 找到对于每个g[i]元素中，满足该元素的s[j]中的最小的元素
            while (j < numOfCookies && g[i] > s[j]) {
                j++;
            }
            // ！！！ 点睛之处
            if (j < numOfCookies) {
                count++;
            }
        }
        return count;
    }
} 
```

C++代码：

```c++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        // 对g 和 s 两个数组，进行排序
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        // 获取数组长度
        int numOfChildren = g.size(), numOfCookies = s.size();
        int count = 0;
        //！！！ 循环的写法需要学习
        for (int i = 0, j = 0; i < numOfChildren && j < numOfCookies; i++, j++) {
            while (j < numOfCookies && g[i] > s[j]) {
                j++;
            }
            if (j < numOfCookies) {
                count++;
            }
        }
        return count;
    }
};
```

C++代码：

```C++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());

        int child =0,cookie = 0;
        // 其实也就是正面的去做了
        while(child < g.size() && cookie < s.size()){
            if(g[child] <= s[cookie]) ++child;
            ++cookie;
        }
        return child;
    }
};
```

![image-20220312000529806](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220312000529806.png)

###  方法二：卡尔

**思路**

为了了满足更多的小孩，就不要造成饼干尺寸的浪费。

大尺寸的饼干既可以满足胃口大的孩子也可以满足胃口小的孩子，那么就应该优先满足胃口大的。

**这里的局部最优就是大饼干喂给胃口大的，充分利用饼干尺寸喂饱一个，全局最优就是喂饱尽可能多的小孩**。

可以尝试使用贪心策略，先将饼干数组和小孩数组排序。

然后从后向前遍历小孩数组，用大饼干优先满足胃口大的，并统计满足小孩数量。

如图：

[![455.分发饼干](https://camo.githubusercontent.com/68df1344894239ff2aa2b72a8fad341d3f2a563a5ff8ad028ba13dc262eefc09/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333136313830393632342e706e67)](https://camo.githubusercontent.com/68df1344894239ff2aa2b72a8fad341d3f2a563a5ff8ad028ba13dc262eefc09/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333136313830393632342e706e67)

这个例子可以看出饼干9只有喂给胃口为7的小孩，这样才是整体最优解，并想不出反例，那么就可以撸代码了。

C++代码如下：

```c++
// 时间复杂度：O(nlogn)
// 空间复杂度：O(1)
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int index = s.size() - 1; // 饼干数组的下标
        int result = 0;
        //对小孩进行遍历
        for (int i = g.size() - 1; i >= 0; i--) {
            if (index >= 0 && s[index] >= g[i]) {
                result++;
                index--;
            }
        }
        return result;
    }
};
```

从代码中可以看出我用了一个index来控制饼干数组的遍历，遍历饼干并没有再起一个for循环，而是采用自减的方式，这也是常用的技巧。

有的同学看到要遍历两个数组，就想到用两个for循环，那样逻辑其实就复杂了。

**也可以换一个思路，小饼干先喂饱小胃口**

C++代码如下：

```c++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());
        int index = 0;
        // 遍历饼干
        for(int i = 0;i < s.size();++i){
            if(index < g.size() && g[index] <= s[i]){
                index++;
            }
        }
        return index;
    }
};
```

**总结**

这道题是贪心很好的一道入门题目，思路还是比较容易想到的。

文中详细介绍了思考的过程，**想清楚局部最优，想清楚全局最优，感觉局部最优是可以推出全局最优，并想不出反例，那么就试一试贪心**。

Java代码如下：

```java
class Solution {
    // 思路1：优先考虑饼干，小饼干先喂饱小胃口
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int start = 0;
        int count = 0;
        for (int i = 0; i < s.length && start < g.length; i++) {
            if (s[i] >= g[start]) {
                start++;
                count++;
            }
        }
        return count;
    }
}

//
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int ans=0;
        for(int i=0,j=0;i<s.length && j<g.length; i++){
            if(s[i] >= g[j]){
                ans++;
                j++;
            }
        }
        return ans;
    }
}
```

```java

class Solution {
    // 思路2：优先考虑胃口，先喂饱大胃口
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int count = 0;
        //饼干大小长度
        int start = s.length - 1;
        // 遍历小孩胃口
        for (int index = g.length - 1; index >= 0; index--) {
            if(start >= 0 && g[index] <= s[start]) {
                start--;
                count++;
            }
        }
        return count;
    }
}
```



## 02，135. 分发糖果 

![image-20220312215930005](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220312215930005.png)

![image-20220312220024415](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220312220024415.png)

### 方法一：两次遍历

![image-20220312223231511](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220312223231511.png)

**Java代码：**

```java
class Solution {
    public int candy(int[] ratings) {
        // 孩子评分数组的长度
        int n = ratings.length;
        // 定义一个左规则数组
        int[] left = new int[n];
        // 第一次遍历
        for (int i = 0; i < n; i++) {
            // 计算左规则
            if (i > 0 && ratings[i] > ratings[i - 1]) {
                left[i] = left[i - 1] + 1;
            } else {
                // 当 i=0 时，定为1
                left[i] = 1;
            }
        }
        
        // 计算右规则
        int right = 0, ret = 0;
        for (int i = n - 1; i >= 0; i--) {
            // 右规则
            if (i < n - 1 && ratings[i] > ratings[i + 1]) {
                right++;
            } else {
                // 当i = n-1 时，定为1
                right = 1;
            }
            // 选择左右规则中，最大的那个数
            ret += Math.max(left[i], right);
 
        return ret;
    }
} 
```

C++代码：

```C++
int candy(vector<int>& ratings) {
    // 数组长度
    int size = ratings.size();
    // 边界条件
    if (size < 2) {
    	return size;
    }
    // 定义一个新数组
    vector<int> num(size, 1);
    //
    for (int i = 1; i < size; ++i) {
    	if (ratings[i] > ratings[i-1]) {
    		num[i] = num[i-1] + 1;
   		 } 
    } 
    for (int i = size - 1; i > 0; --i) {
        if (ratings[i] < ratings[i-1]) {
    		num[i-1] = max(num[i-1], num[i] + 1);
    	} 
    }
    return accumulate(num.begin(), num.end(), 0); // std::accumulate 可以很方便 地求和 
}
```

**复杂度分析**

- 时间复杂度：O(n)，其中 n是孩子的数量。**我们需要遍历两次数组以分别计算满足左规则或右规则的最少糖果数量。**
- 空间复杂度：O(n)，其中 n 是孩子的数量。我们需要保存所有的左规则对应的糖果数量。

#### **知识点补充：**

##### 1、 ++i和i++在某些地方的差异

a = i++; 类似于：

```c++
a = i;
i = i + 1；
```


a = ++i; 类似于：

```c++
i = i + 1；
a = i;
```


i++和++i的 最重要的区别就是 **+1和返回值顺序**从效率上来说++i比i++来的更有效率，因为i++多声明了一个过渡的变量。如下所示：

对于i++的实现是：

```C++
int temp;
temp = i;
i = i+1;
return temp;
```


而++i的实现是：

```c++
i = i+1;
return i;
```


比如printf("%d",i++);是先输出i值随后i自加，而printf("%d",++i);中i先加1再输出，即输出的结果为i大1的数字。

##### 2、for循环

**一、for循环的语句格式**
1、循环语句的组成

```
（1）初始化语句：一条或者多条语句，这些语句完成一些初始化操作
（2）判断条件语句：这是一个boolean 表达式，这个表达式能决定是否执行循环体
（3）循环体语句：这个部分是循环体语句，也就是我们要多次做的事情
（4）控制条件语句：这个部分在一次循环体结束后，下一次循环判断条件执行前执行。通过用于控制循 环条件中的变量，使得循环在合适的时候结束
```

2、循环语句的格式

```
for(初始化语句;判断条件语句;控制条件语句){
	循环体语句;
}
```

**二、for循环执行流程**
1、for循环执行顺势：

```
A:执行初始化语句
B:执行判断条件语句，看其结果是true还是false
	如果是false，循环结束。
	如果是true，继续执行。
C:执行循环体语句
D:执行控制条件语句
E:回到B继续
```

2、循环执行流程图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190409173617411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDUzODEwNw==,size_16,color_FFFFFF,t_70)

### 方法二：常数空间遍历（没学懂）

**思路及解法**

注意到**糖果总是尽量少给，且从 1 开始累计，每次要么比相邻的同学多给一个，要么重新置为 1**。依据此规则，我们可以画出下图：

<img src="https://assets.leetcode-cn.com/solution-static/135/1.png" alt="fig1" style="zoom: 33%;" />

其中相同颜色的柱状图的高度总恰好为 1,2,3 …。

而高度也不一定是升序，也可能是…3,2,1 的降序：

<img src="https://assets.leetcode-cn.com/solution-static/135/2.png" alt="fig2" style="zoom:33%;" />

注意到在上图中，对于第三个同学，**他既可以被认为是属于绿色的升序部分，也可以被认为是属于蓝色的降序部分。**因为他同时比两边的同学评分更高。我们对序列稍作修改：

<img src="https://assets.leetcode-cn.com/solution-static/135/3.png" alt="fig3" style="zoom:33%;" />

注意到右边的升序部分变长了，使得第三个同学不得不被分配 4 个糖果。

依据前面总结的规律，我们可以提出本题的解法。我们从左到右枚举每一个同学，**记前一个同学分得的糖果数量为 pre**：

1. 如果当前同学比上一个同学评分高，说明我们就在最近的递增序列中，直接分配给该同学pre+1 个糖果即可。
2. 否则我们就在一个递减序列中，我们直接分配给当前同学一个糖果，并把该同学所在的递减序列中所有的同学都再多分配一个糖果，以保证糖果数量还是满足条件。
   - 我们无需显式地额外分配糖果，只需要记录当前的递减序列长度，即可知道需要额外分配的糖果数量。
   - 同时注意当前的递减序列长度和上一个递增序列等长时，需要把最近的递增序列的最后一个同学也并进递减序列中。

这样，我们只要记录当前递减序列的长度dec，最近的递增序列的长度inc 和前一个同学分得的糖果数量 pre 即可。

**Java代码**

```java
class Solution {
    /*
    感觉这个很难想出来
    */
    public int candy(int[] ratings) {
        int n = ratings.length;
        // 定义最终的结果为1 
        int ret = 1;
        // dec: 当前递减序列的长度  inc:最近的递增序列的长度 pre: 前一个同学分得的糖果数量
        int inc = 1, dec = 0, pre = 1;
        //
        for (int i = 1; i < n; i++) {
            // 当前同学比上一个同学评分高 ， 说明我们就在最近的递增序列中
            if (ratings[i] >= ratings[i - 1]) {
                dec = 0;
                // 分配给该同学pre+1 个糖果即可 还是 1 个糖果
                pre = ratings[i] == ratings[i - 1] ? 1 : pre + 1;
                
                ret += pre;
                // 记录 pre的长度
                inc = pre;
            } else {
                // 
                dec++;
         // 当前的递减序列长度和上一个递增序列等长时，需要把最近的递增序列的最后一个同学也并进递减序列中
                if (dec == inc) {
                    dec++;
                }
                // 只需要记录当前的递减序列长度，即可知道需要额外分配的糖果数量
                ret += dec;
                pre = 1;
            }
        }
        return ret;
    }
}
```

复杂度分析

- 时间复杂度：O(n)，其中 n是孩子的数量。我们需要遍历两次数组以分别计算满足左规则或右规则的最少糖果数量。
- 空间复杂度：O(1)。我们只需要常数的空间保存若干变量。



#### 思路二: 进一步解释

只需扫一遍数组。

如何判断 i 位置需要多少糖果，我们需要处理有三种情况:

- **ratings[i - 1] == ratings[i]，那么我们只需要 1 糖果**

  ![1563430973450.png](https://pic.leetcode-cn.com/b8a0cf64abf988bef3da02801e81cf1580a9a18b0e96d6ba6b47b2c5b06f8bae-1563430973450.png)

- **ratings[i - 1] < ratings[i]，那么我们只需要比前一个多一块糖果**

  ![1563431091961.png](https://pic.leetcode-cn.com/669cf8f3db923ebaf4aac369f579c2348c245862e7f649260c7c50146aa99dd4-1563431091961.png)

- **ratings[i - 1] > ratings[i]，那么我们不知道如何判断了，比如:**

  ![1563431300625.png](https://pic.leetcode-cn.com/ac7842324de8da76899c631dee1c0c84cca355d8a09d218a9be887f1c4306283-1563431300625.png)

但是，如果知道递减的个数，我们就能判断最少的糖果了

<img src="https://pic.leetcode-cn.com/169784d31f20cca02a4d82c0946ce848ca369d7fd5a5fee5984ea92e96a7c562-1563431573161.png" alt="1563431573161.png" style="zoom:67%;" />

所以我们保证，递减序列，是从 1 开始加的(方向加)，再举个例子:

<img src="https://pic.leetcode-cn.com/0b278240d44a74e0e518bda7749d38b65276b63dce07dbecdf3c0e419db2b80b-1563431873123.png" alt="1563431873123.png" style="zoom:67%;" />

如何判读 `? `的糖果多少，我们发现 `ratings`是`3 - 2 `递减的，递减序列个数 `des_num`为，我们反向加，

有公差为` 1 `的求和公式$首项尾项项数 \frac{(首项 + 尾项) * 项数}{2}$，所以我们先假设 `rating `在等于 4 时候也是满足等差的，所以，有 $\frac{( 1 + des\_num ) * des\_num}{2} = \frac{(1 + 2) * 2}{2}$ 糖果,所以` ratings `是 `3 - 2 `对于的糖果说` 2 - 1`

但是还有一种可能, 如下图所示：

<img src="https://pic.leetcode-cn.com/a82f697e7be2aff71e3c47201f6cc89968b5a87eaa454d35fb83d6b33835f481-1563436200915.png" alt="1563436200915.png" style="zoom:80%;" />

所以

- 时间复杂度 : O(n)
- 空间复杂度 : O(1)

Java代码：

```java
class Solution {
    public int candy(int[] ratings) {
        int res = 1;
        int pre = 1;
        int des_num = 0;
        for (int i = 1; i < ratings.length; i++) {
            if (ratings[i] >= ratings[i - 1]) {
                if (des_num > 0) {
                    res += ((1 + des_num) * des_num) / 2;
                    if (des_num >= pre) res += (des_num - pre + 1);
                    pre = 1;
                    des_num = 0;
                }
                pre = ratings[i - 1] == ratings[i] ? 1 : pre + 1;
                res += pre;
            } else des_num++;
        }
        if (des_num > 0) {
            res += ((1 + des_num) * des_num) / 2;
            if (des_num >= pre) res += (des_num - pre + 1);
        }
        return res;
    }
} 
```



### 方法三：卡尔

**思路**

**==这道题目一定是要确定一边之后，再确定另一边==**，例如比较每一个孩子的左边，然后再比较右边，**如果两边一起考虑一定会顾此失彼**。

**先确定右边评分大于左边的情况**（也就是从前向后遍历）此时局部最优：

​		只要右边评分比左边大，右边的孩子就多一个糖果，全局最优：相邻的孩子中，评分高的右孩子获得比左边孩子更多的糖果局部最优可以推出全局最优。

- ​	如果ratings[i] > ratings[i - 1] 那么[i]的糖 一定要比[i - 1]的糖多一个，


​				所以贪心：candyVec[i] = candyVec[i - 1] + 1

代码如下：

```C++
// 从前向后
for (int i = 1; i < ratings.size(); i++) {
    if (ratings[i] > ratings[i - 1]) candyVec[i] = candyVec[i - 1] + 1;
}
```

如图：

[![135.分发糖果](https://camo.githubusercontent.com/70449883232e5300b4b31c96a324e6b05faa4c53273edd4d246a95668c155935/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313131373131343931363837382e706e67)](https://camo.githubusercontent.com/70449883232e5300b4b31c96a324e6b05faa4c53273edd4d246a95668c155935/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313131373131343931363837382e706e67)

**再确定左孩子大于右孩子的情况（从后向前遍历）**

遍历顺序这里有同学可能会有疑问，为什么不能从前向后遍历呢？

因为如果从前向后遍历，根据 ratings[i + 1] 来确定 ratings[i] 对应的糖果，那么每次都不能利用上前一次的比较结果了。

**所以确定左孩子大于右孩子的情况一定要从后向前遍历！**

如果 ratings[i] > ratings[i + 1]，此时candyVec[i]（第i个小孩的糖果数量）就有两个选择了，一个是candyVec[i + 1] + 1（从右边这个加1得到的糖果数量），一个是candyVec[i]（之前比较右孩子大于左孩子得到的糖果数量）。那么又要贪心了，局部最优：取candyVec[i + 1] + 1 和 candyVec[i] 最大的糖果数量，**保证第i个小孩的糖果数量即大于左边的也大于右边的。**全局最优：相邻的孩子中，评分高的孩子获得更多的糖果。

局部最优可以推出全局最优。所以就取candyVec[i + 1] + 1 和 candyVec[i] 最大的糖果数量，**candyVec[i]只有取最大的才能既保持对左边candyVec[i - 1]的糖果多，也比右边candyVec[i + 1]的糖果多**。

如图：

[![135.分发糖果1](https://camo.githubusercontent.com/fd569cc320e81cf432c6fc90366c90d9a640edee14ef179f353063b931dd4afd/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313131373131353635383739312e706e67)](https://camo.githubusercontent.com/fd569cc320e81cf432c6fc90366c90d9a640edee14ef179f353063b931dd4afd/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313131373131353635383739312e706e67)

所以该过程代码如下：

```c++
// 从后向前
for (int i = ratings.size() - 2; i >= 0; i--) {
    if (ratings[i] > ratings[i + 1] ) {
        candyVec[i] = max(candyVec[i], candyVec[i + 1] + 1);
    }
}
```

整体 C++代码如下：

```c++
class Solution {
public:
    int candy(vector<int>& ratings) {
        //定义一个大小为 ratings.size(),初始化为1
        vector<int> candyVec(ratings.size(), 1);
        // 从前向后
        for (int i = 1; i < ratings.size(); i++) {
            if (ratings[i] > ratings[i - 1]) candyVec[i] = candyVec[i - 1] + 1;
        }
        // 从后向前
        for (int i = ratings.size() - 2; i >= 0; i--) {
            if (ratings[i] > ratings[i + 1] ) {
                candyVec[i] = max(candyVec[i], candyVec[i + 1] + 1);
            }
        }
        // 统计结果
        int result = 0;
        for (int i = 0; i < candyVec.size(); i++) result += candyVec[i];
        return result;
    }
};
```

**总结**

这在leetcode上是一道困难的题目，其难点就在于贪心的策略，如果在考虑局部的时候想两边兼顾，就会顾此失彼。

那么本题我采用了两次贪心的策略：

- 一次是从左到右遍历，只比较右边孩子评分比左边大的情况。
- 一次是从右到左遍历，只比较左边孩子评分比右边大的情况。

这样从局部最优推出了全局最优，即：相邻的孩子中，评分高的孩子获得更多的糖果。

其他语言版本Java

```java
class Solution {
    /** 
         分两个阶段 
         1、起点下标1 从左往右，只要 右边 比 左边 大，右边的糖果=左边 + 1
         2、起点下标 ratings.length - 2 从右往左， 只要左边 比 右边 大，此时 左边的糖果应该 取本身的糖果数（符合比它左边大） 和 右边糖果数 + 1 二者的最大值，这样才符合 它比它左边的大，也比它右边大
    */
    public int candy(int[] ratings) {
        // 定义存放的数组
        int[] candyVec = new int[ratings.length];
        // 设第一个孩子，得到1个糖果
        candyVec[0] = 1;
        // 第一次遍历，从左到右
        for (int i = 1; i < ratings.length; i++) {
            if (ratings[i] > ratings[i - 1]) {
                candyVec[i] = candyVec[i - 1] + 1;
            } else {
                candyVec[i] = 1;
            }
        }
		
        //第二次遍历 
        for (int i = ratings.length - 2; i >= 0; i--) {
            if (ratings[i] > ratings[i + 1]) {
                candyVec[i] = Math.max(candyVec[i], candyVec[i + 1] + 1);
            }
        }
		// 计算最终的结果
        int ans = 0;
        // 遍历数组然后相加
        for (int s : candyVec) {
            ans += s;
        }
        return ans;
    }
}
```



# 2.3---区间问题

## 01，435无重叠区间 

![image-20220312230647846](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220312230647846.png)

### 方法一，卡尔

**相信很多同学看到这道题目都冥冥之中感觉要排序，但是究竟是按照右边界排序，还是按照左边界排序呢？**

这其实是一个难点！

**按照右边界排序**，就要从左向右遍历，因为右边界越小越好，只要右边界越小，留给下一个区间的空间就越大，所以从左向右遍历，优先选右边界小的。

**按照左边界排序**，就要从右向左遍历，因为左边界数值越大越好（越靠右），这样就给前一个区间的空间就越大，所以可以从右向左遍历。

如果按照左边界排序，还从左向右遍历的话，其实也可以，逻辑会有所不同。

一些同学做这道题目可能真的去模拟去重复区间的行为，这是比较麻烦的，还要去删除区间。

题目只是要求移除区间的个数，没有必要去真实的模拟删除区间！

**我来按照右边界排序，从左向右记录非交叉区间的个数。最后用区间总数减去非交叉区间的个数就是需要移除的区间个数了**。

此时问题就是**要求非交叉区间的最大个数**。

右边界排序之后，局部最优：优先选右边界小的区间，所以从左向右遍历，留给下一个区间的空间大一些，从而尽量避免交叉。全局最优：选取最多的非交叉区间。

局部最优推出全局最优，试试贪心！

这里记录非交叉区间的个数还是有技巧的，如图：

[![435.无重叠区间](https://camo.githubusercontent.com/9bb134241b2872c809006a5597e1f247dd219270ac581dc4437417e6bf6bd549/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313232313230313535333631382e706e67)](https://camo.githubusercontent.com/9bb134241b2872c809006a5597e1f247dd219270ac581dc4437417e6bf6bd549/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313232313230313535333631382e706e67)

区间，1，2，3，4，5，6都按照右边界排好序。

每次取非交叉区间的时候，都是可右边界最小的来做分割点（这样留给下一个区间的空间就越大），所以第一条分割线就是区间1结束的位置。

接下来就是找大于区间1结束位置的区间，是从区间4开始。**那有同学问了为什么不从区间5开始？别忘已经是按照右边界排序的了**。

区间4结束之后，在找到区间6，所以一共记录非交叉区间的个数是三个。

总共区间个数为6，减去非交叉区间的个数3。移除区间的最小数量就是3。

C++代码如下：

```c++
class Solution {
public:
    // 按照区间右边界排序
    static bool cmp (const vector<int>& a, const vector<int>& b) {
        return a[1] < b[1];
    }
    //
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        // 临界条件判断
        if (intervals.size() == 0) return 0;
        // 对区间进行排序
        sort(intervals.begin(), intervals.end(), cmp);
        
        int count = 1; // 记录非交叉区间的个数
        int end = intervals[0][1]; // 记录区间分割点
        for (int i = 1; i < intervals.size(); i++) {
            // 
            if (end <= intervals[i][0]) {
                end = intervals[i][1];
                count++;
            }
        }
        return intervals.size() - count;
    }
};
```

- 时间复杂度：$O(n\log n)$ ，有一个快排
- 空间复杂度：$O(1)$

大家此时会发现如此复杂的一个问题，代码实现却这么简单！



本题我认为难度级别可以算是hard级别的！

总结如下难点：

- 难点一：一看题就有感觉需要排序，但究竟怎么排序，按左边界排还是右边界排。
- 难点二：排完序之后如何遍历，如果没有分析好遍历顺序，那么排序就没有意义了。
- 难点三：直接求重复的区间是复杂的，转而求最大非重复区间个数。
- 难点四：求最大非重复区间个数时，需要一个分割点来做标记。

**这四个难点都不好想，但任何一个没想到位，这道题就解不了**。

一些录友可能看网上的题解代码很简单，照葫芦画瓢稀里糊涂的就过了，但是其题解可能并没有把问题难点讲清楚，然后自己再没有钻研的话，那么一道贪心经典区间问题就这么浪费掉了。

贪心就是这样，代码有时候很简单（不是指代码短，而是逻辑简单），但想法是真的难！

这和动态规划还不一样，动规的代码有个递推公式，可能就看不懂了，而贪心往往是直白的代码，但想法读不懂，哈哈。

**所以我把本题的难点也一一列出，帮大家不仅代码看的懂，想法也理解的透彻！**



本题其实和[452.用最少数量的箭引爆气球](https://programmercarl.com/0452.用最少数量的箭引爆气球.html)非常像，弓箭的数量就相当于是非交叉区间的数量，只要把弓箭那道题目代码里射爆气球的判断条件加个等号（认为$[0，1][1，2]$不是相邻区间），然后用总区间数减去弓箭数量 就是要移除的区间数量了。

把[452.用最少数量的箭引爆气球](https://programmercarl.com/0452.用最少数量的箭引爆气球.html)代码稍做修改，就可以AC本题。

#### C++

```c++
class Solution {
public:
    // 按照区间右边界排序
    static bool cmp (const vector<int>& a, const vector<int>& b) {
        return a[1] < b[1];
    }
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) return 0;
        sort(intervals.begin(), intervals.end(), cmp);

        int result = 1; // points 不为空至少需要一支箭
        for (int i = 1; i < intervals.size(); i++) {
            if (intervals[i][0] >= intervals[i - 1][1]) {
                result++; // 需要一支箭
            }
            else {  // 气球i和气球i-1挨着
                intervals[i][1] = min(intervals[i - 1][1], intervals[i][1]); // 更新重叠气球最小右边界
            }
        }
        return intervals.size() - result;
    }
};
```

这里按照 左区间遍历，或者按照右边界遍历，都可以AC，具体原因我还没有仔细看，后面有空再补充。

```C++
class Solution {
public:
    // 按照区间左边界排序
    static bool cmp (const vector<int>& a, const vector<int>& b) {
        return a[0] < b[0];
    }
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) return 0;
        sort(intervals.begin(), intervals.end(), cmp);

        int result = 1; // points 不为空至少需要一支箭
        for (int i = 1; i < intervals.size(); i++) {
            if (intervals[i][0] >= intervals[i - 1][1]) {
                result++; // 需要一支箭
            }
            else {  // 气球i和气球i-1挨着
                intervals[i][1] = min(intervals[i - 1][1], intervals[i][1]); // 更新重叠气球最小右边界
            }
        }
        return intervals.size() - result;
    }
};
```



#### Java

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        Arrays.sort(intervals, (a, b)->{
            if (a[0] == a[0]) return a[1] - b[1];
            return a[0] - b[0];
        });
        // 
        int count = 0;
        int edge = Integer.MIN_VALUE;
        for (int i = 0; i < intervals.length; i++) {
            if (edge <= intervals[i][0]) {
                edge = intervals[i][1];
            } else {
                count++;
            }
        }

        return count;
    }
}
```

按左边排序，不管右边顺序。相交的时候取最小的右边。

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {

        Arrays.sort(intervals,(a,b)->{
            return Integer.compare(a[0],b[0]);
        });
        int remove = 0;
        int pre = intervals[0][1];
        for(int i=1;i<intervals.length;i++){
            if(pre>intervals[i][0]) {
                remove++;
                pre = Math.min(pre,intervals[i][1]);
            }
            else pre = intervals[i][1];
        }
        return remove;
    }
}
```



## 02，452.用最少数量的箭引爆气球 

在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以纵坐标并不重要，因此只要知道开始和结束的横坐标就足够了。开始坐标总是小于结束坐标。

一支弓箭可以沿着 x 轴从不同点完全垂直地射出。在坐标 x 处射出一支箭，若有一个气球的直径的开始和结束坐标为 x_start，x_end， 且满足  x_start ≤ x ≤ x_end，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。

给你一个数组 points ，其中 points [i] = [x_start,x_end] ，返回引爆所有气球所必须射出的最小弓箭数。

示例 1：

```
输入：points = [[10,16],[2,8],[1,6],[7,12]]
输出：2
解释：对于该样例，x = 6 可以射爆 [2,8],[1,6] 两个气球，以及 x = 11 射爆另外两个气球
```

示例 2：

```
输入：points = [[1,2],[3,4],[5,6],[7,8]]
输出：4
```

示例 3：

```
输入：points = [[1,2],[2,3],[3,4],[4,5]]
输出：2
```

示例 4：

```
输入：points = [[1,2]]
输出：1
```

示例 5：

```
输入：points = [[2,3],[2,3]]
输出：1
```


提示：

```
1 <= points.length <= 10^4
points[i].length == 2
-2^31 <= x_start < x_end <= 2^31 - 1
```



# 2.4----数学证明

## 01，142. 环形链表 II

![image-20220313213602793](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220313213602793.png) 

![image-20220313213621756](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220313213621756.png)



# 2.5 ---序列问题

## 01，376摆动序列



# 2.X----练习

## 基础难度

### 1，605



