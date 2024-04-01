## Chapter1. 贪心算法

### 1. 分发饼干

> https://leetcode.cn/problems/assign-cookies/description/

假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。

对每个孩子 `i`，都有一个胃口值 `g[i]`，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 `j`，都有一个尺寸 `s[j]` 。如果 `s[j] >= g[i]`，我们可以将这个饼干 `j` 分配给孩子 `i` ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

示例：

```c++
输入: g = [1,2,3], s = [1,1]
输出: 1

输入: g = [1,2], s = [1,2,3]
输出: 2
```

解决方法：

自己：$O(mlogm+nlogn)$：采用贪心思想，先对数组s和数组g做升序排序，顺序遍历小孩的胃口值s[i]，当g[j] >= s[i]时即为既能满足小孩的胃口同时消耗饼干，接着遍历下一个胃口相同或更大的小孩，同时从上一轮的循环j+1开始遍历，j之前的饼干数肯定不能满足当前小孩胃口，不用再遍历一次。

```c++
int findContentChildren(vector<int>& g, vector<int>& s) {
    std::sort(g.begin(), g.end());
    std::sort(s.begin(), s.end());
    unsigned int childNum = g.size();
    unsigned int cookiesNum = s.size();
    unsigned int count = 0;
    int index = 0;
    for (int i = 0; i < childNum; ++i)
    {
        int j = index;
        while (j < cookiesNum)
        {
            if (g[i] <= s[j])
            {
                count++;
                index = j + 1;
                break;
            }
            j++;
        }
    }
    return count;
}
```

优化：用一个while循环，通过双指针遍历。

### 2. 分发糖果

`n` 个孩子站成一排。给你一个整数数组 `ratings` 表示每个孩子的评分。

你需要按照以下要求，给这些孩子分发糖果：

- 每个孩子至少分配到 `1` 个糖果。
- 相邻两个孩子评分更高的孩子会获得更多的糖果。

请你给每个孩子分发糖果，计算并返回需要准备的 **最少糖果数目** 。

示例：

```c++
输入：ratings = [1,0,2]
输出：5

输入：ratings = [1,2,2]
输出：4
```

解决方法：

从左往右，只关注左边小孩，从右往左，只关注右边小孩，注意比较评分的同时也要比较糖果数。

```c++
int candy(vector<int>& ratings) {
    int n = ratings.size();
    int count = 0;
    vector<int> candies(n, 1);
    for (int i = 1; i < n; ++i)
    {
        if (ratings[i] > ratings[i - 1])
        {
            candies[i] = candies[i - 1] + 1;
        }
    }
    for (int j = n - 1; j >= 1; --j)
    {
        if (ratings[j - 1] > ratings[j] && candies[j - 1] <= candies[j])
        {
            candies[j - 1] = candies[j] + 1;
        }
    }
    count = std::accumulate(candies.begin(), candies.end(), 0);
    return count;
}
```

### 3. 无重叠区间

给定一个区间的集合 `intervals` ，其中 `intervals[i] = [starti, endi]` 。返回 *需要移除区间的最小数量，使剩余区间互不重叠* 。

**提示:**

- `1 <= intervals.length <= 105`
- `intervals[i].length == 2`
- `-5 * 104 <= starti < endi <= 5 * 104`

示例：

```
输入: intervals = [[1,2],[2,3],[3,4],[1,3]]
输出: 1

输入: intervals = [ [1,2], [1,2], [1,2] ]
输出: 2
```

解决方法：利用贪心思想，每次所选择的区间应该占用的区间范围尽可能短，也就是说选择的区间结尾越小，预留给其他区间的范围越大。**利用区间结尾升序排序，每次选择结尾最小且与前一区间不重叠的区间。**

> 需要对区间结尾相同的区间以区间起始做排序吗？
>
> ：不用，因为结尾相同的区间所有都能遍历到，此时区间占用小没有意义，预留出的区间并不能被使用。

```c++
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    int len = intervals.size();
    if (len == 1) {
        return 0;
    }
    int count = 1;
    std::sort(intervals.begin(), intervals.end(), [](vector<int>& a, vector<int>& b) {
        if (a[1] < b[1]) {
            return true;
        } else {
            return false;
        }
    });
    int intervalEnd = intervals[0][1];
    for (int i = 1; i < len; ++i) {
        if (intervalEnd <= intervals[i][0]) {
            intervalEnd = intervals[i][1];
            count++;
        }
    }
    return len - count;
}
```

> sort()使用自定义比较函数排序时，若 > 返回true就是降序，< 返回true就是升序排序。

### 4. 种花问题

假设有一个很长的花坛，一部分地块种植了花，另一部分却没有。可是，花不能种植在相邻的地块上，它们会争夺水源，两者都会死去。

给你一个整数数组 `flowerbed` 表示花坛，由若干 `0` 和 `1` 组成，其中 `0` 表示没种植花，`1` 表示种植了花。另有一个数 `n` ，能否在不打破种植规则的情况下种入 `n` 朵花？能则返回 `true` ，不能则返回 `false` 。

**提示：**

- `1 <= flowerbed.length <= 2 * 104`
- `flowerbed[i]` 为 `0` 或 `1`
- `flowerbed` 中不存在相邻的两朵花
- `0 <= n <= flowerbed.length`

示例：

```c++
输入：flowerbed = [1,0,0,0,1], n = 1
输出：true

输入：flowerbed = [1,0,0,0,1], n = 2
输出：false
```

解决方法：

自研：先剔除不能种花的位置，赋值为2，然后用贪心思想，对于剩余为0的地方，如果与上一次为0的地方索引相差不为1，则代表可以种花。

```c++
bool canPlaceFlowers(vector<int>& flowerbed, int n) {
    int len = flowerbed.size();
    int flowerCanUse = 0;
    int i = 0;
    while(i < len) {
        if (flowerbed[i] == 1) {
            if (i - 1 >= 0) {
                flowerbed[i - 1] = 2;
            }
            if (i + 1 < len) {
                flowerbed[i + 1] = 2;
            }
        }
        i++;
    }
    int lastUsed = INT_MAX;
    for (int i = 0; i < len; ++i) {
        if (flowerbed[i] == 0 && i - 1 != lastUsed) {
            flowerCanUse++;
            lastUsed = i;
        }
    }
    return flowerCanUse >= n;
}
```

> 缺点：修改了原数组，进行了两轮遍历

官方解法：利用贪心思想，记录上一次可以种花位置的索引，统计连续两次种花区间的可种花数，同时分类计算：

* 第一次为1的位置
* 两次1之间
* 最后一次为1的位置
* 全为0的情况

```c++
bool canPlaceFlowers(vector<int>& flowerbed, int n) {
    int len = flowerbed.size();
    int count = 0;
    int prev = -1;
    for (int i = 0; i < len; ++i) {
        // flowerbed[prev]已种过花
        if (prev != -1 && flowerbed[i] == 1) {
            count += (i - prev - 2) / 2;
            prev = i;
        }
        // flowerbed[i]是第一个已种花位置
        else if (prev == -1 && flowerbed[i] == 1) {
            count += (i - prev - 1) / 2;
            prev = i;
        }
    }
    // 全为0的情况
    if (prev == -1) {
        count = (len + 1) / 2;
    }
    // flowerbed[prev]是最后一个已种花位置
    else if(prev != -1) {
        count += (len - prev - 1) / 2;
    }
    return count >= n;
}
```

