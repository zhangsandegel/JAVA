[TOC]

# 1. 77组合

```
给定两个整数 n 和 k，返回范围 [1, n] 中所有可能的 k 个数的组合。
你可以按 任何顺序 返回答案。
```

```
示例1：
	输入：n = 4, k = 2
    输出：
    [
      [2,4],
      [3,4],
      [2,3],
      [1,2],
      [1,3],
      [1,4],
    ]
   
示例2：
	输入：n = 1, k = 1
    输出：[[1]]
	 
提示：
· 1 <= n <= 20
· 1 <= k <= n
```

**本题这是回溯法的经典题⽬**

直接的解法当然是使⽤for循环，例如示例中k为2，很容易想到 ⽤两个for循环，这样就可以输出 和示例

中⼀样的结果。

代码如下：

```java
int n = 4;
for (int i = 1; i <= n; i++) {
 for (int j = i + 1; j <= n; j++) {
 	cout << i << " " << j << endl;
 	}
}
```

输⼊：n = 100, k = 3

那么就三层for循环，代码如下：

```java
int n = 100;
for (int i = 1; i <= n; i++) {
 for (int j = i + 1; j <= n; j++) {
 	for (int u = j + 1; u <= n; n++) {
 		cout << i << " " << j << " " << u << endl;
 		}
 	}
}
```

**如果n为100，k为50呢，那就50层for循环，是不是开始窒息**。

**此时就会发现虽然想暴力搜索，但是用for循环嵌套连暴力都写不出来！**

咋整？

回溯搜索法来了，虽然回溯法也是暴⼒，但⾄少能写出来，不像for循环嵌套k层让⼈绝望。

那么回溯法怎么暴⼒搜呢？

上面我们说了**要解决 n为100，k为50的情况，暴力写法需要嵌套50层for循环，那么回溯法就用递归来解决嵌套层数的问题**。

递归来做层叠嵌套（可以理解是开k层for循环），**每一次的递归中嵌套一个for循环，那么递归就可以用于解决多层嵌套循环的问题了**。

此时递归的层数大家应该知道了，例如：n为100，k为50的情况下，就是递归50层。

一些同学本来对递归就懵，回溯法中递归还要嵌套for循环，可能就直接晕倒了！

如果脑洞模拟回溯搜索的过程，绝对可以让人窒息，所以需要抽象图形结构来进一步理解。

**我们在[关于回溯算法，你该了解这些！ (opens new window)](https://programmercarl.com/回溯算法理论基础.html)中说道回溯法解决的问题都可以抽象为树形结构（N叉树），用树形结构来理解回溯就容易多了**。

那么我把组合问题抽象为如下树形结构：

![77.组合](https://img-blog.csdnimg.cn/20201123195223940.png)

可以看出这个棵树，一开始集合是 1，2，3，4， 从左向右取数，取过的数，不在重复取。

第一次取1，集合变为2，3，4 ，因为k为2，我们只需要再取一个数就可以了，分别取2，3，4，得到集合[1,2] [1,3] [1,4]，以此类推。

**每次从集合中选取元素，可选择的范围随着选择的进行而收缩，调整可选择的范围**。

**图中可以发现n相当于树的宽度，k相当于树的深度**。

那么如何在这个树上遍历，然后收集到我们要的结果集呢？

**图中每次搜索到了叶子节点，我们就找到了一个结果**。

相当于只需要把达到叶子节点的结果收集起来，就可以求得 n个数中k个数的组合集合。

在[关于回溯算法，你该了解这些！ (opens new window)](https://programmercarl.com/回溯算法理论基础.html)中我们提到了回溯法三部曲，那么我们按照回溯法三部曲开始正式讲解代码了。

### 回溯法三部曲

#### 1.递归函数的返回值以及参数

在这里要定义两个全局变量，==一个用来存放符合条件单一结果，一个用来存放符合条件结果的集合==。

```cpp
vector<vector<int>> result; // 存放符合条件结果的集合
vector<int> path; // 用来存放符合条件结果
```

其实不定义这两个全局遍历也是可以的，把这两个变量放进递归函数的参数里，但函数里参数太多影响可读性，所以我定义全局变量了。

函数里一定有两个参数，既然是集合n里面取k的数，那么n和k是两个int型的参数。

然后==还需要一个参数，为int型变量startIndex==，这个参数用来记录本层递归的中，集合从哪里开始遍历（集合就是[1,...,n] ）。

为什么要有这个startIndex呢？

**每次从集合中选取元素，可选择的范围随着选择的进行而收缩，调整可选择的范围，就是要靠startIndex**。

从下图中红线部分可以看出，在集合[1,2,3,4]取1之后，下一层递归，就要在[2,3,4]中取数了，那么下一层递归如何知道从[2,3,4]中取数呢，靠的就是startIndex。

![77.组合2](https://img-blog.csdnimg.cn/20201123195328976.png)所以需要startIndex来记录下一层递归，搜索的起始位置。

```cpp
vector<vector<int>> result; // 存放符合条件结果的集合
vector<int> path; // 用来存放符合条件单一结果
void backtracking(int n, int k, int startIndex) 
```

#### 2.回溯函数终止条件

什么时候到达所谓的叶子节点了呢？

path这个数组的大小如果达到k，说明我们找到了一个子集大小为k的组合了，在图中path存的就是根节点到叶子节点的路径。

如图红色部分：

![77.组合3](https://img-blog.csdnimg.cn/20201123195407907.png)

此时用result二维数组，把path保存起来，并终止本层递归。

所以终止条件代码如下：

```
if (path.size() == k) {
    result.push_back(path);
    return;
}
```

#### 3.单层搜索的过程

回溯法的搜索过程就是一个树型结构的遍历过程，在如下图中，可以看出for循环用来横向遍历，递归的过程是纵向遍历。

![77.组合1](https://img-blog.csdnimg.cn/20201123195242899.png)如此我们才遍历完图中的这棵树。

for循环每次从startIndex开始遍历，然后用path保存取到的节点i。

代码如下：

```cpp
for (int i = startIndex; i <= n; i++) { // 控制树的横向遍历
    path.push_back(i); // 处理节点 
    backtracking(n, k, i + 1); // 递归：控制树的纵向遍历，注意下一层搜索要从i+1开始
    path.pop_back(); // 回溯，撤销处理的节点
}
```

可以看出backtracking（递归函数）通过不断调用自己一直往深处遍历，总会遇到叶子节点，遇到了叶子节点就要返回。

backtracking的下面部分就是回溯的操作了，撤销本次处理的结果。

关键地方都讲完了，组合问题C++完整代码如下：

```c++
class Solution {
private:
    vector<vector<int>> result; // 存放符合条件结果的集合
    vector<int> path; // 用来存放符合条件结果
    // 定义回溯函数
    // startIndex用来收缩集合可选择的范围
    void backtracking(int n, int k, int startIndex) {
        // 用result二维数组，把path保存起来，并终止本层递归。
        if (path.size() == k) {
            result.push_back(path);
            return;
        }
        // 遍历N叉树
        // for循环，横向遍历
        for (int i = startIndex; i <= n; i++) {
            path.push_back(i); // 处理节点 
            //递归,纵向遍历
            backtracking(n, k, i + 1); // 递归
            path.pop_back(); // 回溯，撤销处理的节点
        }
    }
public:
    vector<vector<int>> combine(int n, int k) {
        result.clear(); // 可以不写
        path.clear();   // 可以不写
        backtracking(n, k, 1);
        return result;
    }
};
```

 还记得我们在[关于回溯算法，你该了解这些！ (opens new window)](https://programmercarl.com/回溯算法理论基础.html)中给出的回溯法模板么？

如下：

```text
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```

**对比一下本题的代码，是不是发现有点像！** 所以有了这个模板，就有解题的大体方向，不至于毫无头绪。

Java版本：

```java
class Solution {
    // 定义存放符合条件结果的集合
    List<List<Integer>> result = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();

    public List<List<Integer>> combine(int n, int k) {
        // 为什么startIndex是从1 开始的呢？？？
        // 因为是从集合[1,2....n]
        backtracking(n,k,1);
        return result;
    }

    private void backtracking(int n,int k,int startIndex){
        //终止条件
        if(path.size() == k){
            // 注意！！! 不是Arraylist
            result.add(new ArrayList<>(path));
            return ;
        }

        for(int i=startIndex;i<=n;i++){
            path.add(i);
            backtracking(n,k,i+1);
            path.removeLast();
        }
    }
}
```

剪枝优化版本：

```java
class Solution {
    //存放符合条件结果的集合
    List<List<Integer>> result = new ArrayList<>();
    // 用来存放符合条件的结果
    LinkedList<Integer> path = new LinkedList<>();
    //
    public List<List<Integer>> combine(int n, int k) {
        combineHelper(n, k, 1);
        return result;
    }
    /**
     * 每次从集合中选取元素，可选择的范围随着选择的进行而收缩，调整可选择的范围，就是要靠startIndex
     * @param startIndex 用来记录本层递归的中，集合从哪里开始遍历（集合就是[1,...,n] ）。
     */
    private void combineHelper(int n, int k, int startIndex){
        //终止条件
        if (path.size() == k){
            result.add(new ArrayList<>(path));
            return;
        }
        for (int i = startIndex; i <= n - (k - path.size()) + 1; i++){
            path.add(i);
            combineHelper(n, k, i + 1);
            path.removeLast();
        }
    }
}
```

###  总结

组合问题是回溯法解决的经典问题，我们开始的时候给大家列举一个很形象的例子，就是n为100，k为50的话，直接想法就需要50层for循环。

==从而引出了回溯法就是解决这种k层for循环嵌套的问题==。

==然后进一步把回溯法的搜索过程抽象为树形结构，可以直观的看出搜索的过程。==

接着用回溯法三部曲，逐步分析了函数参数、终止条件和单层搜索的过程。

#### 剪枝优化

我们说过，回溯法虽然是暴力搜索，但也有时候可以有点剪枝优化一下的。

在遍历的过程中有如下代码：

```cpp
for (int i = startIndex; i <= n; i++) {
    path.push_back(i);
    backtracking(n, k, i + 1);
    path.pop_back();
}
```

这个遍历的范围是可以剪枝优化的，怎么优化呢？

来举一个例子，n = 4，k = 4的话，那么第一层for循环的时候，从元素2开始的遍历都没有意义了。 在第二层for循环，从元素3开始的遍历都没有意义了。

这么说有点抽象，如图所示：

![77.组合4](https://img-blog.csdnimg.cn/20210130194335207.png)

图中每一个节点（图中为矩形），就代表本层的一个for循环，那么每一层的for循环从第二个数开始遍历的话，都没有意义，都是无效遍历。

**所以，可以剪枝的地方就在递归中每一层的for循环所选择的起始位置**。

**如果for循环选择的起始位置之后的元素个数 已经不足 我们需要的元素个数了，那么就没有必要搜索了**。

注意代码中i，就是for循环里选择的起始位置。

```text
for (int i = startIndex; i <= n; i++) {
```

接下来看一下优化过程如下：

1. 已经选择的元素个数：path.size();
2. 还需要的元素个数为: k - path.size();
3. 在集合n中至多要从该起始位置 : n - (k - path.size()) + 1，开始遍历

为什么有个+1呢，因为包括起始位置，我们要是一个左闭的集合。

举个例子，n = 4，k = 3， 目前已经选取的元素为0（path.size为0），n - (k - 0) + 1 即 4 - ( 3 - 0) + 1 = 2。

从2开始搜索都是合理的，可以是组合[2, 3, 4]。

这里大家想不懂的话，建议也举一个例子，就知道是不是要+1了。

所以优化之后的for循环是：

```text
for (int i = startIndex; i <= n - (k - path.size()) + 1; i++) // i为本次搜索的起始位置
```

优化后整体代码如下：

C++版本：

```cpp
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    void backtracking(int n, int k, int startIndex) {
        if (path.size() == k) {
            result.push_back(path);
            return;
        }
        for (int i = startIndex; i <= n - (k - path.size()) + 1; i++) { // 优化的地方
            path.push_back(i); // 处理节点
            backtracking(n, k, i + 1);
            path.pop_back(); // 回溯，撤销处理的节点
        }
    }
public:

    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);
        return result;
    }
};
```

### 剪枝总结

本篇我们准对求组合问题的回溯法代码做了剪枝优化，这个优化如果不画图的话，其实不好理解，也不好讲清楚。所以我依然是把整个回溯过程抽象为一棵树形结构，然后可以直观的看出，剪枝究竟是剪的哪里。

# 2. 17电话号码的字母组合 

![image-20220220123637225](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220123637225.png)

说明：尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

## 方法一：回溯---代码随想录

```
从示例上来说，输入"23"，最直接的想法就是两层for循环遍历了吧，正好把组合的情况都输出了。

如果输入"233"呢，那么就三层for循环，如果"2333"呢，就四层for循环.......

大家应该感觉出和[77.组合]遇到的一样的问题，就是这for循环的层数如何写出来，此时又是回溯法登场的时候了。
```

理解本题后，要解决如下三个问题：

1. 数字和字母如何映射
2. 两个字母就两个for循环，三个字符我就三个for循环，以此类推，然后发现代码根本写不出来
3. 输入1 * #按键等等异常情况

#### 1. 数字和字母如何映射

可以使用**map**或者定义一个**二维数组**，例如：***string letterMap[10]***，来做映射，我这里定义一个二维数组，代码如下：

```
const string letterMap[10] = {
    "", // 0
    "", // 1
    "abc", // 2
    "def", // 3
    "ghi", // 4
    "jkl", // 5
    "mno", // 6
    "pqrs", // 7
    "tuv", // 8
    "wxyz", // 9
};
```

#### 2. 回溯法来解决n个for循环的问题

 例如：输入："23"，抽象为树形结构，如图所示：

[![17. 电话号码的字母组合](https://camo.githubusercontent.com/e3ea2109e556ee3dec50d10bd777bd8361a198f347f1fd15e633291d51b924e3/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333230303330343436392e706e67)](https://camo.githubusercontent.com/e3ea2109e556ee3dec50d10bd777bd8361a198f347f1fd15e633291d51b924e3/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333230303330343436392e706e67)

图中可以看出遍历的深度，就是输入"23"的长度，而叶子节点就是我们要收集的结果，输出["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]。

### 回溯三部曲：

#### 1.确定回溯函数参数

首先==需要一个字符串s来收集叶子节点的结果==，然后用一个字符串数组result保存起来，这两个变量我依然定义为全局。

再来看参数，==参数指定是有题目中给的string digits==，==然后还要有一个参数就是int型的index==。

注意这个index可不是 [77.组合](https://programmercarl.com/0077.组合.html)和[216.组合总和III](https://programmercarl.com/0216.组合总和III.html)中的startIndex了。

==这个index是记录遍历第几个数字了，就是用来遍历digits的（题目中给出数字字符串）==，同时index也表示树的深度。

代码如下：

```c++
vector<string> result;
string s;
void backtracking(const string& digits, int index)
```

#### 2.确定终止条件

例如输入用例"23"，两个数字，那么根节点往下递归两层就可以了，叶子节点就是要收集的结果集。

那么终止条件就是如果==index 等于 输入的数字个数（digits.size）了（本来index就是用来遍历digits的）==。

然后收集结果，结束本层递归。

代码如下：

```c++
if (index == digits.size()) {
    result.push_back(s);
    return;
}
```

#### 3.确定单层遍历逻辑

首先==要取index指向的数字，并找到对应的字符集==（手机键盘的字符集）。

然后for循环来处理这个字符集，代码如下：

```c++
int digit = digits[index] - '0';        // 将index指向的数字（字符串）转为int
string letters = letterMap[digit];      // 取数字对应的字符集
for (int i = 0; i < letters.size(); i++) {
    s.push_back(letters[i]);            // 处理
    backtracking(digits, index + 1);    // 递归，注意index+1，一下层要处理下一个数字了
    s.pop_back();                       // 回溯
}
```

**注意这里for循环，可不像是在[回溯算法：求组合问题！](https://programmercarl.com/0077.组合.html)和[回溯算法：求组合总和！](https://programmercarl.com/0216.组合总和III.html)中从startIndex开始遍历的**。

==**因为本题每一个数字代表的是不同集合，也就是求不同集合之间的组合，而[77. 组合](https://programmercarl.com/0077.组合.html)和[216.组合总和III](https://programmercarl.com/0216.组合总和III.html)都是是求同一个集合中的组合！**==

注意：输入1 * #按键等等异常情况

代码中最好考虑这些异常情况，但题目的测试数据中应该没有异常情况的数据，所以我就没有加了。

**但是要知道会有这些异常，如果是现场面试中，一定要考虑到！**

C++代码：

```c++
// 版本一
class Solution {
private:
    // 字母和数字之间的映射
    const string letterMap[10] = {
        "", // 0
        "", // 1
        "abc", // 2
        "def", // 3
        "ghi", // 4
        "jkl", // 5
        "mno", // 6
        "pqrs", // 7
        "tuv", // 8
        "wxyz", // 9
    };
public:
    // 定义结果数组
    vector<string> result;
    string s;
    // 回溯函数，两个参数，一个是 digits：处理的字符串 一个是index:遍历的第几个数字
    void backtracking(const string& digits, int index) {
        // 用result二维数组，把s保存起来，并终止本层递归。
        if (index == digits.size()) {
            result.push_back(s);
            return;
        }
       // !!! digits[index]指的是字符
        int digit = digits[index] - '0';        // 将index指向的数字转为int
        string letters = letterMap[digit];      // 取数字对应的字符集
        // 遍历每个数字
        for (int i = 0; i < letters.size(); i++) {
            s.push_back(letters[i]);            // 处理
            backtracking(digits, index + 1);    // 递归，注意index+1，一下层要处理下一个数字了
            s.pop_back();                       // 回溯
        }
    }
    
    // 函数入口
    vector<string> letterCombinations(string digits) {
        s.clear();
        result.clear();
        // 字符串为空
        if (digits.size() == 0) {
            return result;
        }
        //调用回溯函数
        backtracking(digits, 0);
        // 返回result函数
        return result;
    }
};
```

一些写法，是把回溯的过程放在递归函数里了，例如如下代码，我可以写成这样：（注意注释中不一样的地方）

```c++
// 版本二
class Solution {
private:
        const string letterMap[10] = {
            "", // 0
            "", // 1
            "abc", // 2
            "def", // 3
            "ghi", // 4
            "jkl", // 5
            "mno", // 6
            "pqrs", // 7
            "tuv", // 8
            "wxyz", // 9
        };
public:
    vector<string> result;
    void getCombinations(const string& digits, int index, const string& s) { // 注意参数的不同
        if (index == digits.size()) {
            result.push_back(s);
            return;
        }
        int digit = digits[index] - '0';
        string letters = letterMap[digit];
        for (int i = 0; i < letters.size(); i++) {
            getCombinations(digits, index + 1, s + letters[i]);  // 注意这里的不同
        }
    }
    vector<string> letterCombinations(string digits) {
        result.clear();
        if (digits.size() == 0) {
            return result;
        }
        getCombinations(digits, 0, "");
        return result;

    }
};
```

我不建议把回溯藏在递归的参数里这种写法，很不直观，我在[二叉树：以为使用了递归，其实还隐藏着回溯](https://programmercarl.com/二叉树中递归带着回溯.html)这篇文章中也深度分析了，回溯隐藏在了哪里。

所以大家可以按照版本一来写就可以了。

Java版本：

```java
class Solution {
	// !!!
    //设置全局列表存储最后的结果
    List<String> list = new ArrayList<>();

    public List<String> letterCombinations(String digits) {
        // 字符串为空
        if (digits == null || digits.length() == 0) {
            return list;
        }
        //初始对应所有的数字，为了直接对应2-9，新增了两个无效的字符串""
        String[] numString = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        //迭代处理
        backTracking(digits, numString, 0);
        return list;

    }

    //!!!
    //每次迭代获取一个字符串，所以会设计大量的字符串拼接，所以这里选择更为高效的 StringBuild
    StringBuilder temp = new StringBuilder();

    //比如digits如果为"23",num 为0，则str表示2对应的 abc
    public void backTracking(String digits, String[] numString, int num) {
        //需要一个字符串s来收集叶子节点的结果
        //遍历全部一次记录一次得到的字符串
        if (num == digits.length()) {
            list.add(temp.toString());
            return;
        }
        //str 表示当前num对应的字符串
        String str = numString[digits.charAt(num) - '0'];
        for (int i = 0; i < str.length(); i++) {
            temp.append(str.charAt(i));
            //c
            backTracking(digits, numString, num + 1);
            //剔除末尾的继续尝试
            temp.deleteCharAt(temp.length() - 1);
        }
    }
}
```



### 总结

本篇将题目的三个要点一一列出，并重点强调了和前面讲解过的[77. 组合](https://programmercarl.com/0077.组合.html)和[216.组合总和III](https://programmercarl.com/0216.组合总和III.html)的区别，本题是多个集合求组合，所以在回溯的搜索过程中，都有一些细节需要注意的。

### java知识点补充：

#### 1.String类：

1. 创建字符串：

   String 类是不可改变的，所以你一旦创建了 String 对象，那它的值就无法改变了

   如果需要对字符串做很多修改，那么应该选择使用 [StringBuffer & StringBuilder 类](https://www.runoob.com/java/java-stringbuffer.html)

   ![image-20220220205708524](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205708524.png)

   ![img](https://www.runoob.com/wp-content/uploads/2013/12/java-string-1-2020-12-01.png)

   

2. 字符串长度

   ![image-20220220205900365](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205900365.png)

3. 连接字符串

   ![image-20220220205929042](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205929042.png)

   ![image-20220220205945496](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205945496.png)

   | SN(序号) | 方法描述                                                     |
   | :------- | :----------------------------------------------------------- |
   | 1        | [char charAt(int index)](https://www.runoob.com/java/java-string-charat.html) 返回指定索引处的 char 值。 |
   | 2        | [int compareTo(Object o)](https://www.runoob.com/java/java-string-compareto.html) 把这个字符串和另一个对象比较。 |
   | 3        | [int compareTo(String anotherString)](https://www.runoob.com/java/java-string-compareto.html) 按字典顺序比较两个字符串。 |
   | 4        | [int compareToIgnoreCase(String str)](https://www.runoob.com/java/java-string-comparetoignorecase.html) 按字典顺序比较两个字符串，不考虑大小写。 |
   | 5        | [String concat(String str)](https://www.runoob.com/java/java-string-concat.html) 将指定字符串连接到此字符串的结尾。 |
   | 6        | [boolean contentEquals(StringBuffer sb)](https://www.runoob.com/java/java-string-contentequals.html) 当且仅当字符串与指定的StringBuffer有相同顺序的字符时候返回真。 |
   | 7        | [static String copyValueOf(char[\] data)](https://www.runoob.com/java/java-string-copyvalueof.html) 返回指定数组中表示该字符序列的 String。 |
   | 8        | [static String copyValueOf(char[\] data, int offset, int count)](https://www.runoob.com/java/java-string-copyvalueof.html) 返回指定数组中表示该字符序列的 String。 |
   | 9        | [boolean endsWith(String suffix)](https://www.runoob.com/java/java-string-endswith.html) 测试此字符串是否以指定的后缀结束。 |
   | 10       | [boolean equals(Object anObject)](https://www.runoob.com/java/java-string-equals.html) 将此字符串与指定的对象比较。 |
   | 11       | [boolean equalsIgnoreCase(String anotherString)](https://www.runoob.com/java/java-string-equalsignorecase.html) 将此 String 与另一个 String 比较，不考虑大小写。 |
   | 12       | [byte[\] getBytes()](https://www.runoob.com/java/java-string-getbytes.html)  使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
   | 13       | [byte[\] getBytes(String charsetName)](https://www.runoob.com/java/java-string-getbytes.html) 使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。 |
   | 14       | [void getChars(int srcBegin, int srcEnd, char[\] dst, int dstBegin)](https://www.runoob.com/java/java-string-getchars.html) 将字符从此字符串复制到目标字符数组。 |
   | 15       | [int hashCode()](https://www.runoob.com/java/java-string-hashcode.html) 返回此字符串的哈希码。 |
   | 16       | [int indexOf(int ch)](https://www.runoob.com/java/java-string-indexof.html) 返回指定字符在此字符串中第一次出现处的索引。 |
   | 17       | [int indexOf(int ch, int fromIndex)](https://www.runoob.com/java/java-string-indexof.html) 返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索。 |
   | 18       | [int indexOf(String str)](https://www.runoob.com/java/java-string-indexof.html)  返回指定子字符串在此字符串中第一次出现处的索引。 |
   | 19       | [int indexOf(String str, int fromIndex)](https://www.runoob.com/java/java-string-indexof.html) 返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始。 |
   | 20       | [String intern()](https://www.runoob.com/java/java-string-intern.html)  返回字符串对象的规范化表示形式。 |
   | 21       | [int lastIndexOf(int ch)](https://www.runoob.com/java/java-string-lastindexof.html)  返回指定字符在此字符串中最后一次出现处的索引。 |
   | 22       | [int lastIndexOf(int ch, int fromIndex)](https://www.runoob.com/java/java-string-lastindexof.html) 返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索。 |
   | 23       | [int lastIndexOf(String str)](https://www.runoob.com/java/java-string-lastindexof.html) 返回指定子字符串在此字符串中最右边出现处的索引。 |
   | 24       | [int lastIndexOf(String str, int fromIndex)](https://www.runoob.com/java/java-string-lastindexof.html)  返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索。 |
   | 25       | [int length()](https://www.runoob.com/java/java-string-length.html) 返回此字符串的长度。 |
   | 26       | [boolean matches(String regex)](https://www.runoob.com/java/java-string-matches.html) 告知此字符串是否匹配给定的正则表达式。 |
   | 27       | [boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)](https://www.runoob.com/java/java-string-regionmatches.html) 测试两个字符串区域是否相等。 |
   | 28       | [boolean regionMatches(int toffset, String other, int ooffset, int len)](https://www.runoob.com/java/java-string-regionmatches.html) 测试两个字符串区域是否相等。 |
   | 29       | [String replace(char oldChar, char newChar)](https://www.runoob.com/java/java-string-replace.html) 返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。 |
   | 30       | [String replaceAll(String regex, String replacement)](https://www.runoob.com/java/java-string-replaceall.html) 使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。 |
   | 31       | [String replaceFirst(String regex, String replacement)](https://www.runoob.com/java/java-string-replacefirst.html)  使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。 |
   | 32       | [String[\] split(String regex)](https://www.runoob.com/java/java-string-split.html) 根据给定正则表达式的匹配拆分此字符串。 |
   | 33       | [String[\] split(String regex, int limit)](https://www.runoob.com/java/java-string-split.html) 根据匹配给定的正则表达式来拆分此字符串。 |
   | 34       | [boolean startsWith(String prefix)](https://www.runoob.com/java/java-string-startswith.html) 测试此字符串是否以指定的前缀开始。 |
   | 35       | [boolean startsWith(String prefix, int toffset)](https://www.runoob.com/java/java-string-startswith.html) 测试此字符串从指定索引开始的子字符串是否以指定前缀开始。 |
   | 36       | [CharSequence subSequence(int beginIndex, int endIndex)](https://www.runoob.com/java/java-string-subsequence.html)  返回一个新的字符序列，它是此序列的一个子序列。 |
   | 37       | [String substring(int beginIndex)](https://www.runoob.com/java/java-string-substring.html) 返回一个新的字符串，它是此字符串的一个子字符串。 |
   | 38       | [String substring(int beginIndex, int endIndex)](https://www.runoob.com/java/java-string-substring.html) 返回一个新字符串，它是此字符串的一个子字符串。 |
   | 39       | [char[\] toCharArray()](https://www.runoob.com/java/java-string-tochararray.html) 将此字符串转换为一个新的字符数组。 |
   | 40       | [String toLowerCase()](https://www.runoob.com/java/java-string-tolowercase.html) 使用默认语言环境的规则将此 String 中的所有字符都转换为小写。 |
   | 41       | [String toLowerCase(Locale locale)](https://www.runoob.com/java/java-string-tolowercase.html)  使用给定 Locale 的规则将此 String 中的所有字符都转换为小写。 |
   | 42       | [String toString()](https://www.runoob.com/java/java-string-tostring.html)  返回此对象本身（它已经是一个字符串！）。 |
   | 43       | [String toUpperCase()](https://www.runoob.com/java/java-string-touppercase.html) 使用默认语言环境的规则将此 String 中的所有字符都转换为大写。 |
   | 44       | [String toUpperCase(Locale locale)](https://www.runoob.com/java/java-string-touppercase.html) 使用给定 Locale 的规则将此 String 中的所有字符都转换为大写。 |
   | 45       | [String trim()](https://www.runoob.com/java/java-string-trim.html) 返回字符串的副本，忽略前导空白和尾部空白。 |
   | 46       | [static String valueOf(primitive data type x)](https://www.runoob.com/java/java-string-valueof.html) 返回给定data type类型x参数的字符串表示形式。 |
   | 47       | [contains(CharSequence chars)](https://www.runoob.com/java/java-string-contains.html) 判断是否包含指定的字符系列。 |
   | 48       | [isEmpty()](https://www.runoob.com/java/java-string-isempty.html) 判断字符串是否为空。 |



#### 2. ArrayList

```
ArrayList 类是一个可以动态修改的数组，与普通数组的区别就是它是没有固定大小的限制，我们可以添加或删除元素。
ArrayList 继承了 AbstractList ，并实现了 List 接口。
```

![img](https://www.runoob.com/wp-content/uploads/2020/06/ArrayList-1-768x406-1.png)

```java
ArrayList<E> objectName =new ArrayList<>();　 // 初始化
/*
E: 泛型数据类型，用于设置 objectName 的数据类型，只能为引用数据类型。
objectName: 对象名。
*/
```

ArrayList 是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。

1. ArrayList 类提供了很多有用的方法，添加元素到 ArrayList 可以使用 add() 方法:

   ```java
   **import** java.util.ArrayList;
   
   **public** **class** RunoobTest {
     **public** **static** **void** main(String[] args) {
       ArrayList<String> sites = **new** ArrayList<String>();
       sites.add("Google");
       sites.add("Runoob");
       sites.add("Taobao");
       sites.add("Weibo");
       System.out.println(sites);
     }
   }
   //  [Google, Runoob, Taobao, Weibo
   ```

   

2. get()方法：访问元素

   ![image-20220220205320052](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205320052.png)

3. set()方法：修改元素

   ![image-20220220205344149](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205344149.png)

4. remove()方法：删除元素

   ![image-20220220205420619](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205420619.png)

5. 计算大小：size()方法

   ![image-20220220205447250](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220220205447250.png)

6. 

   ```java
   
   ```

​			

2. 

## 方法二：官方解答（哈希+回溯）

其实本题不算难，但也处处是细节，大家还要自己亲自动手写一写。

首先使用==哈希表==存储每个数字对应的所有可能的字母，然后进行==回溯==操作。

回溯过程中维护一个字符串，表示已有的字母排列（如果未遍历完电话号码的所有数字，则已有的字母排列是不完整的）。该字符串初始为空。每次取电话号码的一位数字，从哈希表中获得该数字对应的所有可能的字母，并将其中的一个字母插入到已有的字母排列后面，然后继续处理电话号码的后一位数字，直到处理完电话号码中的所有数字，即得到一个完整的字母排列。然后进行回退操作，遍历其余的字母排列。

回溯算法用于寻找所有的可行解，如果发现一个解不可行，则会舍弃不可行的解。在这道题中，由于每个数字对应的每个字母都可能进入字母组合，因此不存在不可行的解，直接穷举所有的解即可。

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        //
        List<String> combinations = new ArrayList<String>();
        if (digits.length() == 0) {
            return combinations;
        }
        //
        Map<Character, String> phoneMap = new HashMap<Character, String>() {{
            put('2', "abc");
            put('3', "def");
            put('4', "ghi");
            put('5', "jkl");
            put('6', "mno");
            put('7', "pqrs");
            put('8', "tuv");
            put('9', "wxyz");
        }};
        // 调用回溯函数
        backtrack(combinations, phoneMap, digits, 0, new StringBuffer());
        return combinations;
    }
    public void backtrack(List<String> combinations, Map<Character, String> phoneMap, String digits, int index, StringBuffer combination) {
        //
        if (index == digits.length()) {
            combinations.add(combination.toString());
        } else {
            //
            char digit = digits.charAt(index);
            String letters = phoneMap.get(digit);
            int lettersCount = letters.length();
            for (int i = 0; i < lettersCount; i++) {
                combination.append(letters.charAt(i));
                backtrack(combinations, phoneMap, digits, index + 1, combination);
                combination.deleteCharAt(index);
            }
        }
    }
} 
```

复杂度分析

- 时间复杂度：O($3^m \times 4^n$)  ，其中 m是输入中对应 3 个字母的数字个数（包括数字 2、3、4、5、6、8），n 是输入中对应 4 个字母的数字个数（包括数字 7、9），m+n是输入数字的总个数。当输入包含 m个对应 3 个字母的数字和 n 个对应 4 个字母的数字时，不同的字母组合一共有 $3^m \times 4^n$种，需要遍历每一种字母组合。

- 空间复杂度：O(m+n)，其中 m 是输入中对应 3 个字母的数字个数，n 是输入中对应4 个字母的数字个数，m+n 是输入数字的总个数。除了返回值以外，空间复杂度主要取决于哈希表以及回溯过程中的递归调用层数，哈希表的大小与输入无关，可以看成常数，递归调用层数最大为 m+n。


# 3.216组合综合|||

 ![image-20220308114120754](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220308114120754.png)![image-20220308114151981](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220308114151981.png)

## 方法一：二进制（子集）枚举

### 1. 思路与算法

**「组合中只允许含有 1-9 的正整数，并且每种组合中不存在重复的数字」**意味着这个组合中最多包含 9个数字。我们可以把原问题转化成集合 S = \{ 1,2,3,4,5,6,7,8,9 \} ，我们要找出 S 的当中满足如下条件的子集：

大小为 k集合中元素的和为 n。因此我们可以用**==子集枚举==**的方法来做这道题。**即原序列中有 9个数，每个数都有两种状态，「被选择到子集中」和「不被选择到子集中」，所以状态的总数为 $2^9$**。我们用一个 **9位二进制数mask 来记录当前所有位置的状态**，从第0到高第 i位为 0 表示 i 不被选择到子集中，为 1 表示 i 被选择到子集中。当我们按顺序枚举 $[0, 2^9 - 1]$ 中的所有整数的时候，就可以不重不漏地把每个状态枚举到，对于一个状态mask，我们可以用**==位运算==**的方法得到对应的子集序列，然后再判断是否满足上面的两个条件，如果满足，就记录答案。

如何通过位运算来得到mask 各个位置的信息？对于第 i 个位置我们可以判断 (1 << i) & mask 是否为 0，如果不为 0 则说明 i 在子集当中。当然，这里要注意的是，一个 9 位二进制数 i 的范围是 $[0, 8]$，而可选择的数字是 $[1, 9]$，所以我们需要做一个映射，**最简单的办法就是当我们知道 i位置不为 0 的时候将 i + 1 加入子集**。

当然，子集枚举也可以用递归实现。在「77. 组合的官方题解」的方法一中提及了子集枚举递归实现的基本框架，感兴趣的同学可以参考。

![image-20220308155127686](C:\Users\zy\AppData\Roaming\Typora\typora-user-images\image-20220308155127686.png)

代码如下。

```java
class Solution {
    // 定义两个数组
    // 存放每一个临时加入的状态数组
    List<Integer> temp = new ArrayList<Integer>();
    // 存放最终，数组结果集合
    List<List<Integer>> ans = new ArrayList<List<Integer>>();
    
    // 我们按顺序枚举[0, 2^9 - 1] 中的所有整数的时候，就可以不重不漏地把每个状态枚举到
 
	public List<List<Integer>> combinationSum3(int k, int n) {
        // mask:每个状态被记为mask
        // 9位二进制数mask 来记录当前所有位置的状态
        // mask < (1<<9) : 枚举所有的状态 
        // 1 << 9 : 左移九位
        // 0000 0000 01  ----->    1000 0000 00
    	for (int mask = 0; mask < (1 << 9); ++mask) {
            // 调用函数 check
        	if (check(mask, k, n)) {
                //如果满足条件，就加入到结果数组中
            	ans.add(new ArrayList<Integer>(temp));
        	}
   		}
    	return ans;
	}
	
    // 布尔函数check: 是否满足条件
	public boolean check(int mask, int k, int n) {
    	temp.clear();
    	for (int i = 0; i < 9; ++i) {
            // (1 << i) & mask 是否为 0，如果不为 0 则说明 i 在子集当中
            // 第i位和mask相与
        	if (((1 << i) & mask) != 0) {
            	temp.add(i + 1);
        	}
    	}
        // 是否为k个数
    	if (temp.size() != k) {
        	return false;
    	}
        // 记录总共的
    	int sum = 0;
    	for (int num : temp) {
        	sum += num;
    	}
   	 return sum == n;
	}
}
```

复杂度分析

- 时间复杂度： O($M×2^M$ )，其中 M 为集合的大小，本题中 M固定为 99。一共有 $2^M$个状态，每个状态需要 O(M + k) = O(M) 的判断（k≤M），故时间复杂度为 O($M×2^M$)。
- 空间复杂度：O(M) 。即temp 的空间代价。

### 2. Java移位运算符和位运算

**左移运算符（<<）**

按二进制形式把所有的数字向左移动对应的位数，高位移出(舍弃)，低位的空位补零。

例如: 4<<3 就是将4的二进制值左移三位。

4的二进制值为0000 0000 0000 0000 0000 0000 0000 0100,左移两位得到的结果值就是0000 0000 0000 0000 0000 0000 0010 0000 换算成十进制就是32。实际上就是将4乘以2的3次方。

在数字没有溢出的前提下，对于正数和负数，左移一位都相当于乘以2的1次方，左移n位就相当于乘以2的n次方。

**按位与"&"**

对比两个二进制数，同位对比，如果两个数的同位都为1则为1，否则都为0，例如：

```sql
0011 0011  --- 51的二进制值	
1111 0101  --- 243的二进制值	
---------- 				
0011 0001  --- 计算的结果	
```

## 方法二：组合枚举

思路与算法

我们可以换一个思路：我们需要在 9个数中选择 k个数，让它们的和为 n。

这样问题就变成了一个组合枚举问题。组合枚举有两种处理方法——递归法和字典序法，在「77. 组合的官方题解」中有详细的说明。

这里我们要做的是做一个**「在 9个数中选择 k个数」**的组合枚举，对于枚举到的所有组合，判断这个组合内元素之和是否为 n。

代码如下。

```java
class Solution {
    List<Integer> temp = new ArrayList<Integer>();
    List<List<Integer>> ans = new ArrayList<List<Integer>>();
	public List<List<Integer>> combinationSum3(int k, int n) {
    dfs(1, 9, k, n);
    return ans;
}

public void dfs(int cur, int n, int k, int sum) {
    if (temp.size() + (n - cur + 1) < k || temp.size() > k) {
        return;
    }
    if (temp.size() == k) {
        int tempSum = 0;
        for (int num : temp) {
            tempSum += num;
        }
        if (tempSum == sum) {
            ans.add(new ArrayList<Integer>(temp));
            return;
        }
    }
    temp.add(cur);
    dfs(cur + 1, n, k, sum);
    temp.remove(temp.size() - 1);
    dfs(cur + 1, n, k, sum);
}
```
}
复杂度分析

- 时间复杂度： O($M\choose k$×k)，其中 M 为集合的大小，本题中 M 固定为 9。一共有 $M \choose k$  个组合，每次判断需要的时间代价是 O(k)O(k)。
- 空间复杂度：O(M)。temp 数组的空间代价是 O(k)，递归栈空间的代价是 O(M)，故空间复杂度为 O(M + k) = O(M) .

 

## 方法三：卡尔

本题就是在[1,2,3,4,5,6,7,8,9] 这个集合中找到和为 n的 k个数的组合。

相对于[77. 组合](https://programmercarl.com/0077.组合.html)，无非就是多了一个限制，本题是要找到和为n的k个数的组合，而整个集合已经是固定的了[1,...,9]。想到这一点了，做过[77. 组合](https://programmercarl.com/0077.组合.html)之后，本题是简单一些了。

本题 k 相当于了树的深度，9（因为整个集合就是9个数）就是树的宽度。

例如 k = 2，n = 4的话，就是在集合[1,2,3,4,5,6,7,8,9]中求 k（个数） = 2, n（和） = 4的组合。

选取过程如图：

[![216.组合总和III](https://camo.githubusercontent.com/a70514625cc45a5497139d8e13afdb279cd7625584092f92112ff252b7b623e1/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333139353731373937352e706e67)](https://camo.githubusercontent.com/a70514625cc45a5497139d8e13afdb279cd7625584092f92112ff252b7b623e1/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333139353731373937352e706e67)

图中，可以看出，只有最后取到集合（1，3）和为4 符合条件。

### 1，回溯三部曲

#### 1. **确定递归函数参数**

和[77. 组合](https://programmercarl.com/0077.组合.html)一样，依然需要一维数组path来存放符合条件的结果，二维数组result来存放结果集。

这里我依然定义path 和 result为全局变量。

至于为什么取名为path？从上面树形结构中，可以看出，结果其实就是一条根节点到叶子节点的路径。

```c++
vector<vector<int>> result; // 存放结果集
vector<int> path; // 符合条件的结果
```

接下来还需要如下参数：

- targetSum（int）目标和，也就是题目中的n。
- k（int）就是题目中要求k个数的集合。
- sum（int）为已经收集的元素的总和，也就是path里元素的总和。
- startIndex（int）为下一层for循环搜索的起始位置。

所以代码如下：

```c++
vector<vector<int>> result;
vector<int> path;
void backtracking(int targetSum, int k, int sum, int startIndex)
```

其实这里sum这个参数也可以省略，每次targetSum减去选取的元素数值，然后判断如果targetSum为0了，说明收集到符合条件的结果了，我这里为了直观便于理解，还是加一个sum参数。

还要强调一下，回溯法中递归函数参数很难一次性确定下来，一般先写逻辑，需要啥参数了，填什么参数。

#### 2. 确定终止条件

什么时候终止呢？

在上面已经说了，k其实就已经限制树的深度，因为就取k个元素，树再往下深了没有意义。

所以如果path.size() 和 k相等了，就终止。

如果此时path里收集到的元素和（sum） 和targetSum（就是题目描述的n）相同了，就用result收集当前的结果。

所以 终止代码如下：

```c++
if (path.size() == k) {
    if (sum == targetSum) result.push_back(path);
    return; // 如果path.size() == k 但sum != targetSum 直接返回
}
```

#### 3.**单层搜索过程**

本题和[77. 组合](https://programmercarl.com/0077.组合.html)区别之一就是集合固定的就是9个数[1,...,9]，所以for循环固定i<=9

如图：

 [![216.组合总和III](https://camo.githubusercontent.com/a70514625cc45a5497139d8e13afdb279cd7625584092f92112ff252b7b623e1/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333139353731373937352e706e67)](https://camo.githubusercontent.com/a70514625cc45a5497139d8e13afdb279cd7625584092f92112ff252b7b623e1/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333139353731373937352e706e67)

处理过程就是 path收集每次选取的元素，相当于树型结构里的边，sum来统计path里元素的总和。

代码如下：

```c++
for (int i = startIndex; i <= 9; i++) {
    sum += i;
    path.push_back(i);
    backtracking(targetSum, k, sum, i + 1); // 注意i+1调整startIndex
    sum -= i; // 回溯
    path.pop_back(); // 回溯
}
```

**别忘了处理过程 和 回溯过程是一一对应的，处理有加，回溯就要有减！**

参照[关于回溯算法，你该了解这些！](https://programmercarl.com/回溯算法理论基础.html)中的模板，不难写出如下C++代码：

```c++
class Solution {
private:
    vector<vector<int>> result; // 存放结果集
    vector<int> path; // 符合条件的结果
    // targetSum：目标和，也就是题目中的n。
    // k：题目中要求k个数的集合。
    // sum：已经收集的元素的总和，也就是path里元素的总和。
    // startIndex：下一层for循环搜索的起始位置。
    void backtracking(int targetSum, int k, int sum, int startIndex) {
        if (path.size() == k) {
            if (sum == targetSum) result.push_back(path);
            return; // 如果path.size() == k 但sum != targetSum 直接返回
        }
        for (int i = startIndex; i <= 9; i++) {
            sum += i; // 处理
            path.push_back(i); // 处理
            backtracking(targetSum, k, sum, i + 1); // 注意i+1调整startIndex
            sum -= i; // 回溯
            path.pop_back(); // 回溯
        }
    }

public:
    vector<vector<int>> combinationSum3(int k, int n) {
        result.clear(); // 可以不加
        path.clear();   // 可以不加
        backtracking(n, k, 0, 1);
        return result;
    }
};
```

### 2，剪枝

这道题目，剪枝操作其实是很容易想到了，想必大家看上面的树形图的时候已经想到了。

如图： [![216.组合总和III1](https://camo.githubusercontent.com/a818f81e098f42efdbaec969f86c9f2bbfdd63fdad304c2d4aa8453055b2bff6/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f323032303131323331393538303437362e706e67)](https://camo.githubusercontent.com/a818f81e098f42efdbaec969f86c9f2bbfdd63fdad304c2d4aa8453055b2bff6/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f323032303131323331393538303437362e706e67)

已选元素总和如果已经大于n（图中数值为4）了，那么往后遍历就没有意义了，直接剪掉。

那么剪枝的地方一定是在递归终止的地方剪，剪枝代码如下：

```
if (sum > targetSum) { // 剪枝操作
    return;
}
```

和[回溯算法：组合问题再剪剪枝](https://programmercarl.com/0077.组合优化.html) 一样，for循环的范围也可以剪枝，i <= 9 - (k - path.size()) + 1就可以了。

C++代码：

```c++
class Solution {
private:
    vector<vector<int>> result; // 存放结果集
    vector<int> path; // 符合条件的结果
    void backtracking(int targetSum, int k, int sum, int startIndex) {
        if (sum > targetSum) { // 剪枝操作
            return; // 如果path.size() == k 但sum != targetSum 直接返回
        }
        if (path.size() == k) {
            if (sum == targetSum) result.push_back(path);
            return;
        }
        for (int i = startIndex; i <= 9 - (k - path.size()) + 1; i++) { // 剪枝
            sum += i; // 处理
            path.push_back(i); // 处理
            backtracking(targetSum, k, sum, i + 1); // 注意i+1调整startIndex
            sum -= i; // 回溯
            path.pop_back(); // 回溯
        }
    }

public:
    vector<vector<int>> combinationSum3(int k, int n) {
        result.clear(); // 可以不加
        path.clear();   // 可以不加
        backtracking(n, k, 0, 1);
        return result;
    }
};
```

### 3，总结

开篇就介绍了本题与[回溯算法：求组合问题！](https://programmercarl.com/0077.组合.html)的区别，相对来说加了元素总和的限制，如果做完[回溯算法：求组合问题！](https://programmercarl.com/0077.组合.html)再做本题在合适不过。

分析完区别，依然把问题抽象为树形结构，按照回溯三部曲进行讲解，最后给出剪枝的优化。

相信做完本题，大家对组合问题应该有初步了解了。



Java版本：

```java
class Solution {
	List<List<Integer>> result = new ArrayList<>();
	LinkedList<Integer> path = new LinkedList<>();

	public List<List<Integer>> combinationSum3(int k, int n) {
		backTracking(n, k, 1, 0);
		return result;
	}

	private void backTracking(int targetSum, int k, int startIndex, int sum) {
		// 减枝
		if (sum > targetSum) {
			return;
		}

		if (path.size() == k) {
			if (sum == targetSum) result.add(new ArrayList<>(path));
			return;
		}
		
		// 减枝 9 - (k - path.size()) + 1
		for (int i = startIndex; i <= 9 - (k - path.size()) + 1; i++) {
			path.add(i);
			sum += i;
			backTracking(targetSum, k, i + 1, sum);
			//回溯
			path.removeLast();
			//回溯
			sum -= i;
		}
	}
}
```

