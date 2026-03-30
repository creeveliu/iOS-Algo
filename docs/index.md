---
layout: handbook
title: iOS 工程师算法面试学习手册
description: 适合熟练使用 Objective-C 的 iOS 工程师，按章节系统准备算法面试。
---

> 适合熟练使用 Objective-C 的 iOS 工程师
> 目标：用难度适中的题目，系统覆盖面试必会算法知识点

---

## 第 0 章 课程说明与学习方法

### 0.1 课程目标

这份手册不是给竞赛选手准备的，而是给正在求职、准备面试、希望在有限时间内建立算法面试能力的 iOS 工程师准备的。

学完后，你应该具备以下能力：

- 能独立完成常见 `Easy` 和主流 `Medium` 算法题。
- 能用 `Objective-C` 写出结构清晰、边界完整的代码。
- 能在面试中讲清楚思路、复杂度、优化过程。
- 能把算法题与 iOS 工程经验联系起来，提高表达说服力。

### 0.2 iOS 岗为什么也要准备算法

很多 iOS 工程师会有一个误区：日常开发主要写业务代码、架构、性能优化，算法题似乎“不常用”。但实际面试中，算法题依然是常见筛选项，原因通常有三个：

- 算法题能检验候选人的抽象能力、思维清晰度和编码稳定性。
- 算法题方便统一评价不同背景的候选人。
- 算法训练能间接体现工程习惯，比如边界处理、复杂度意识、代码组织能力。

所以你的目标不是“卷到很难”，而是把高频基础题做熟，把常见套路讲顺。

### 0.3 Objective-C 刷题的常用数据结构与写法

你需要先熟悉 OC 刷题的几种常见容器：

- `NSMutableArray`：动态数组，适合顺序遍历、栈模拟。
- `NSMutableDictionary`：键值映射，适合哈希查找、计数。
- `NSMutableSet`：去重和快速判断是否存在。
- 自定义 `ListNode`、`TreeNode`：链表和二叉树题常用。

刷题时要特别注意：

- `NSMutableArray` 删除头部元素开销较大，不适合直接当高性能队列。
- `NSString` 不可变，频繁修改时建议转换思路，不要在题目里硬拼接。
- OC 的判空、对象访问、下标边界需要更谨慎。

### 0.4 面试中如何讲清楚思路、复杂度和边界条件

一道题的标准表达流程建议固定下来：

1. 复述题意，确认输入输出。
2. 说最直接的思路。
3. 分析时间复杂度和空间复杂度。
4. 看是否能优化。
5. 开始写代码。
6. 写完后手动走样例。
7. 主动补充边界情况。

一个常用表达模板：

> 这题我先用哈希表来做，核心是把查找时间从 O(n) 降到 O(1)。  
> 整体时间复杂度是 O(n)，空间复杂度是 O(n)。  
> 代码写完后我会验证空数组、重复值和找不到解的情况。

### 0.5 刷题顺序与复习方法

推荐顺序：

1. 数组 / 字符串
2. 哈希表
3. 双指针
4. 链表
5. 栈 / 队列
6. 二叉树
7. 回溯
8. 二分查找
9. 动态规划
10. 图

复习建议：

- 第一次做题：学套路。
- 第二次做题：脱离答案独立写。
- 第三次做题：练表达和优化。

---

## 第 1 章 数组基础

### 1.1 数组遍历与常见陷阱

数组题是面试最常见的起点。你需要先建立三个意识：

- 是否允许原地修改。
- 是否有序。
- 是否需要额外空间换时间。

常见陷阱：

- 下标越界。
- 忽略空数组。
- 修改数组后没有维护有效区间。

### 1.2 原地修改数组

原地修改通常会用到双指针或覆盖写入的方式。

核心思想：

- 一个指针负责遍历原数组。
- 一个指针负责维护“有效结果区间”。

这类题的重点不是删除动作本身，而是“保留什么元素”。

### 1.3 前缀和基础

前缀和适用于“快速求区间和”的题。

定义：

`prefix[i]` 表示前 `i` 个元素之和。

好处：

- 任意区间和都能在 O(1) 时间内算出。
- 常用于子数组和、区间统计问题。

### 1.4 差分思想入门

差分适用于大量区间增减操作。

如果题目频繁出现：

- 某一段全部加 1
- 某一段全部减 1

就可以考虑差分数组，最后再恢复原数组。

### 1.5 经典题：移除元素

题目描述：给定一个数组 `nums` 和一个值 `val`，请你原地移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。元素的相对顺序可以改变，你不需要考虑新长度之后的元素内容。

知识点：

- 原地修改
- 快慢指针

思路：

- 快指针遍历数组。
- 遇到不等于目标值的元素，就写到慢指针位置。
- 最后慢指针就是新数组长度。

示例：

- 输入：`nums = [3,2,2,3], val = 3`
- 输出：`2`
- 解释：新的有效数组可以视为 `[2,2]`

方法名定义：

```objc
- (NSInteger)removeElement:(NSMutableArray<NSNumber *> *)nums val:(NSInteger)val;
```

调用示例代码：

```objc
NSMutableArray<NSNumber *> *nums = [@[@3, @2, @2, @3] mutableCopy];
NSInteger length = [self removeElement:nums val:3];
NSLog(@"length = %ld", (long)length);
NSLog(@"valid nums = %@", [nums subarrayWithRange:NSMakeRange(0, length)]);
```

参考答案：见 [答案手册 1.5 移除元素](./ios-algorithm-interview-handbook-answers#15-移除元素)

### 1.6 经典题：合并两个有序数组

题目描述：给你两个有序整数数组 `nums1` 和 `nums2`，其中 `nums1` 末尾有足够空间容纳 `nums2`。请把 `nums2` 合并进 `nums1`，使合并后的 `nums1` 仍然有序。

知识点：

- 有序数组
- 双指针
- 从后往前填充

面试重点：

- 如果从前往后合并，会覆盖未处理数据。
- 从后往前写，代码更稳。

示例：

- 输入：`nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3`
- 输出：`[1,2,2,3,5,6]`

方法名定义：

```objc
- (void)merge:(NSMutableArray<NSNumber *> *)nums1
            m:(NSInteger)m
         nums2:(NSArray<NSNumber *> *)nums2
            n:(NSInteger)n;
```

调用示例代码：

```objc
NSMutableArray<NSNumber *> *nums1 = [@[@1, @2, @3, @0, @0, @0] mutableCopy];
NSArray<NSNumber *> *nums2 = @[@2, @5, @6];
[self merge:nums1 m:3 nums2:nums2 n:3];
NSLog(@"merged nums1 = %@", nums1);
```

参考答案：见 [答案手册 1.6 合并两个有序数组](./ios-algorithm-interview-handbook-answers#16-合并两个有序数组)

### 1.7 经典题：买卖股票的最佳时机

题目描述：给定一个数组 `prices`，`prices[i]` 表示某只股票第 `i` 天的价格。你只能买入一次、卖出一次，并且卖出必须在买入之后，求最大利润。

知识点：

- 遍历维护最小值
- 局部最优

思路：

- 遍历时维护历史最低价格。
- 用当前价格减去历史最低价格，更新最大利润。

示例：

- 输入：`prices = [7,1,5,3,6,4]`
- 输出：`5`
- 解释：第 2 天买入，第 5 天卖出，利润为 `6 - 1 = 5`

方法名定义：

```objc
- (NSInteger)maxProfit:(NSArray<NSNumber *> *)prices;
```

调用示例代码：

```objc
NSArray<NSNumber *> *prices = @[@7, @1, @5, @3, @6, @4];
NSInteger profit = [self maxProfit:prices];
NSLog(@"max profit = %ld", (long)profit);
```

参考答案：见 [答案手册 1.7 买卖股票的最佳时机](./ios-algorithm-interview-handbook-answers#17-买卖股票的最佳时机)

### 1.8 本章小结与模板整理

本章你需要掌握：

- 原地数组修改
- 快慢指针
- 前缀和的基本意义
- 遍历时维护最优值

推荐题单：

- `27. 移除元素`
- `88. 合并两个有序数组`
- `121. 买卖股票的最佳时机`

---

## 第 2 章 字符串基础

### 2.1 字符串遍历与字符统计

字符串题本质上常常还是数组题，只是对象从数字变成了字符。

常见操作：

- 遍历统计字符出现次数
- 判断字符类型
- 比较左右指针位置字符

### 2.2 双指针处理字符串

双指针在字符串里非常常见，尤其适合：

- 回文判断
- 跳过无效字符
- 原地反转

### 2.3 回文类问题入门

回文类问题最核心的是“左右对称”。

常见思路：

- 左右双指针向中间收缩
- 先预处理无效字符

### 2.4 经典题：验证回文串

题目描述：给定一个字符串，忽略大小写和非字母数字字符，判断它是否是回文串。也就是说，从左往右读和从右往左读是否一致。

知识点：

- 双指针
- 字符过滤

思路：

- 左右指针分别从头尾开始。
- 遇到非字母数字字符时跳过。
- 比较有效字符是否相等。

示例：

- 输入：`"A man, a plan, a canal: Panama"`
- 输出：`true`

方法名定义：

```objc
- (BOOL)isPalindrome:(NSString *)s;
```

调用示例代码：

```objc
NSString *s = @"A man, a plan, a canal: Panama";
BOOL result = [self isPalindrome:s];
NSLog(@"is palindrome = %@", result ? @"YES" : @"NO");
```

参考答案：见 [答案手册 2.4 验证回文串](./ios-algorithm-interview-handbook-answers#24-验证回文串)

### 2.5 经典题：最长公共前缀

题目描述：给定一个字符串数组，找出这些字符串的最长公共前缀。如果不存在公共前缀，返回空字符串。

知识点：

- 横向比较
- 字符串边界处理

思路：

- 先把第一个字符串当作前缀。
- 逐个与后面的字符串比较，不断缩短前缀。

示例：

- 输入：`["flower","flow","flight"]`
- 输出：`"fl"`

方法名定义：

```objc
- (NSString *)longestCommonPrefix:(NSArray<NSString *> *)strs;
```

调用示例代码：

```objc
NSArray<NSString *> *strs = @[@"flower", @"flow", @"flight"];
NSString *prefix = [self longestCommonPrefix:strs];
NSLog(@"prefix = %@", prefix);
```

参考答案：见 [答案手册 2.5 最长公共前缀](./ios-algorithm-interview-handbook-answers#25-最长公共前缀)

### 2.6 经典题：反转字符串

题目描述：给定一个字符数组，请你原地将它反转，不能额外开辟一个同样大小的新数组。

知识点：

- 原地交换
- 相向双指针

示例：

- 输入：`["h","e","l","l","o"]`
- 输出：`["o","l","l","e","h"]`

方法名定义：

```objc
- (void)reverseString:(NSMutableArray<NSString *> *)s;
```

调用示例代码：

```objc
NSMutableArray<NSString *> *chars = [@[@"h", @"e", @"l", @"l", @"o"] mutableCopy];
[self reverseString:chars];
NSLog(@"reversed = %@", chars);
```

参考答案：见 [答案手册 2.6 反转字符串](./ios-algorithm-interview-handbook-answers#26-反转字符串)

### 2.7 经典题：第一个唯一字符

题目描述：给定一个字符串，找出第一个只出现一次的字符下标；如果不存在这样的字符，返回 `-1`。

知识点：

- 哈希计数
- 二次遍历

示例：

- 输入：`"leetcode"`
- 输出：`0`

方法名定义：

```objc
- (NSInteger)firstUniqChar:(NSString *)s;
```

调用示例代码：

```objc
NSString *s = @"leetcode";
NSInteger index = [self firstUniqChar:s];
NSLog(@"first unique index = %ld", (long)index);
```

参考答案：见 [答案手册 2.7 第一个唯一字符](./ios-algorithm-interview-handbook-answers#27-第一个唯一字符)

### 2.8 本章小结与模板整理

本章重点是：

- 字符串统计
- 双指针判断
- 前缀比较

推荐题单：

- `125. 验证回文串`
- `14. 最长公共前缀`
- `344. 反转字符串`
- `387. 字符串中的第一个唯一字符`

---

## 第 3 章 哈希表与集合

### 3.1 为什么哈希表能提升效率

哈希表的核心价值是：

- 查找快
- 插入快
- 判断存在性快

平均时间复杂度通常是 O(1)。

### 3.2 计数、去重、查找三类典型场景

哈希表最常见的三种用途：

- 计数：某元素出现了多少次
- 去重：某元素是否已经出现过
- 查找：是否存在某个补数或目标

### 3.3 经典题：两数之和

题目描述：给定一个整数数组 `nums` 和目标值 `target`，请你在数组中找出两个数，它们的和等于 `target`，并返回这两个数的下标。题目通常默认每种输入只会对应一个答案，且同一个元素不能重复使用。

知识点：

- 哈希查找
- 空间换时间

思路：

- 遍历数组时，先看 `target - nums[i]` 是否已存在。
- 如果存在，直接返回结果。
- 否则把当前值加入哈希表。

示例：

- 输入：`nums = [2,7,11,15], target = 9`
- 输出：`[0,1]`

方法名定义：

```objc
- (NSArray<NSNumber *> *)twoSum:(NSArray<NSNumber *> *)nums target:(NSInteger)target;
```

调用示例代码：

```objc
NSArray<NSNumber *> *nums = @[@2, @7, @11, @15];
NSArray<NSNumber *> *result = [self twoSum:nums target:9];
NSLog(@"two sum index = %@", result);
```

参考答案：见 [答案手册 3.3 两数之和](./ios-algorithm-interview-handbook-answers#33-两数之和)

### 3.4 经典题：有效的字母异位词

题目描述：给定两个字符串 `s` 和 `t`，判断 `t` 是否是 `s` 的字母异位词。也就是说，它们包含的字符种类和每种字符的数量都完全相同，只是顺序不同。

知识点：

- 频次统计

思路：

- 统计第一个字符串每个字符出现次数。
- 再用第二个字符串抵消。
- 最后判断计数是否全部为 0。

示例：

- 输入：`s = "anagram", t = "nagaram"`
- 输出：`true`

方法名定义：

```objc
- (BOOL)isAnagram:(NSString *)s t:(NSString *)t;
```

调用示例代码：

```objc
BOOL result = [self isAnagram:@"anagram" t:@"nagaram"];
NSLog(@"is anagram = %@", result ? @"YES" : @"NO");
```

参考答案：见 [答案手册 3.4 有效的字母异位词](./ios-algorithm-interview-handbook-answers#34-有效的字母异位词)

### 3.5 经典题：存在重复元素

题目描述：给定一个整数数组，判断其中是否存在某个值至少出现两次。如果有重复元素返回 `YES`，否则返回 `NO`。

知识点：

- 集合判重

思路：

- 遍历数组。
- 如果元素已在集合中，返回 `YES`。
- 否则加入集合。

示例：

- 输入：`[1,2,3,1]`
- 输出：`true`

方法名定义：

```objc
- (BOOL)containsDuplicate:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSArray<NSNumber *> *nums = @[@1, @2, @3, @1];
BOOL result = [self containsDuplicate:nums];
NSLog(@"contains duplicate = %@", result ? @"YES" : @"NO");
```

参考答案：见 [答案手册 3.5 存在重复元素](./ios-algorithm-interview-handbook-answers#35-存在重复元素)

### 3.6 经典题：只出现一次的数字

题目描述：给定一个非空整数数组，除了某个元素只出现一次外，其余每个元素都出现两次。请找出那个只出现一次的元素。

这题有两种常见解法：

- 哈希计数
- 位运算异或

面试时建议先说哈希，再补充异或是更优解。

示例：

- 输入：`[4,1,2,1,2]`
- 输出：`4`

方法名定义：

```objc
- (NSInteger)singleNumber:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSArray<NSNumber *> *nums = @[@4, @1, @2, @1, @2];
NSInteger result = [self singleNumber:nums];
NSLog(@"single number = %ld", (long)result);
```

参考答案：见 [答案手册 3.6 只出现一次的数字](./ios-algorithm-interview-handbook-answers#36-只出现一次的数字)

### 3.7 本章小结与模板整理

本章必须形成一个反射：

只要题目里出现下面这些信号，就先想哈希：

- 查找某个值是否出现过
- 统计出现次数
- 找配对
- 去重

推荐题单：

- `1. 两数之和`
- `242. 有效的字母异位词`
- `217. 存在重复元素`
- `136. 只出现一次的数字`

---

## 第 4 章 双指针专题

### 4.1 相向双指针

适用场景：

- 有序数组
- 回文判断
- 两端夹逼最优解

### 4.2 快慢指针

适用场景：

- 原地删除
- 链表找环
- 维护窗口中的有效区间

### 4.3 滑动窗口入门

滑动窗口适用于：

- 连续子数组
- 连续子串
- 求最短、最长满足条件区间

核心在于：

- 右指针负责扩张窗口
- 左指针负责收缩窗口

### 4.4 经典题：删除有序数组中的重复项

题目描述：给定一个非递减有序数组，请你原地删除重复元素，使每个元素只出现一次，并返回新数组长度。元素的相对顺序要保持不变。

知识点：

- 有序数组
- 快慢指针

示例：

- 输入：`nums = [1,1,2]`
- 输出：`2`
- 解释：前两个有效元素可视为 `[1,2]`

方法名定义：

```objc
- (NSInteger)removeDuplicates:(NSMutableArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSMutableArray<NSNumber *> *nums = [@[@1, @1, @2] mutableCopy];
NSInteger length = [self removeDuplicates:nums];
NSLog(@"length = %ld", (long)length);
NSLog(@"valid nums = %@", [nums subarrayWithRange:NSMakeRange(0, length)]);
```

参考答案：见 [答案手册 4.4 删除有序数组中的重复项](./ios-algorithm-interview-handbook-answers#44-删除有序数组中的重复项)

### 4.5 经典题：移动零

题目描述：给定一个数组，把所有 `0` 移动到数组末尾，同时保持非零元素的相对顺序，要求尽量原地完成。

知识点：

- 原地覆盖
- 保持相对顺序

示例：

- 输入：`nums = [0,1,0,3,12]`
- 输出：`[1,3,12,0,0]`

方法名定义：

```objc
- (void)moveZeroes:(NSMutableArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSMutableArray<NSNumber *> *nums = [@[@0, @1, @0, @3, @12] mutableCopy];
[self moveZeroes:nums];
NSLog(@"nums = %@", nums);
```

参考答案：见 [答案手册 4.5 移动零](./ios-algorithm-interview-handbook-answers#45-移动零)

### 4.6 经典题：盛最多水的容器

题目描述：给定若干条竖线的高度，第 `i` 条线在横坐标 `i` 处。任选两条线与 x 轴组成一个容器，求这个容器最多能盛多少水。

知识点：

- 相向双指针
- 贪心思想

面试重点：

- 总是移动较短的一边，因为移动较长的一边不会提高上界。

示例：

- 输入：`height = [1,8,6,2,5,4,8,3,7]`
- 输出：`49`

方法名定义：

```objc
- (NSInteger)maxArea:(NSArray<NSNumber *> *)height;
```

调用示例代码：

```objc
NSArray<NSNumber *> *height = @[@1, @8, @6, @2, @5, @4, @8, @3, @7];
NSInteger area = [self maxArea:height];
NSLog(@"max area = %ld", (long)area);
```

参考答案：见 [答案手册 4.6 盛最多水的容器](./ios-algorithm-interview-handbook-answers#46-盛最多水的容器)

### 4.7 经典题：长度最小的子数组

题目描述：给定一个正整数数组和一个目标值 `target`，找出和大于等于 `target` 的最短连续子数组长度；如果不存在，返回 `0`。

知识点：

- 滑动窗口
- 连续区间

示例：

- 输入：`target = 7, nums = [2,3,1,2,4,3]`
- 输出：`2`
- 解释：最短连续子数组是 `[4,3]`

方法名定义：

```objc
- (NSInteger)minSubArrayLen:(NSInteger)target nums:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSArray<NSNumber *> *nums = @[@2, @3, @1, @2, @4, @3];
NSInteger length = [self minSubArrayLen:7 nums:nums];
NSLog(@"min subarray length = %ld", (long)length);
```

参考答案：见 [答案手册 4.7 长度最小的子数组](./ios-algorithm-interview-handbook-answers#47-长度最小的子数组)

### 4.8 本章小结与模板整理

做双指针题时先问自己：

- 是从两端逼近，还是一快一慢？
- 是静态有序问题，还是动态区间问题？

推荐题单：

- `26. 删除有序数组中的重复项`
- `283. 移动零`
- `11. 盛最多水的容器`
- `209. 长度最小的子数组`

---

## 第 5 章 链表基础

### 5.1 链表结构与面试考法

链表题常见考点：

- 指针修改是否正确
- 是否会使用虚拟头节点
- 是否能用快慢指针简化逻辑

### 5.2 虚拟头节点技巧

虚拟头节点是链表题的稳定器。

适合场景：

- 删除头节点
- 插入到头部
- 合并链表

### 5.3 快慢指针判断环

快慢指针是链表面试中的高频技巧：

- 快指针一次走两步
- 慢指针一次走一步
- 如果相遇，说明有环

### 5.4 链表反转模板

反转链表是必须默写的模板。

核心变量：

- `prev`
- `cur`
- `next`

链表题最小节点定义：

```objc
@interface ListNode : NSObject
@property (nonatomic, assign) NSInteger val;
@property (nonatomic, strong, nullable) ListNode *next;
- (instancetype)initWithVal:(NSInteger)val next:(ListNode * _Nullable)next;
@end

@implementation ListNode

- (instancetype)initWithVal:(NSInteger)val next:(ListNode * _Nullable)next {
    self = [super init];
    if (self) {
        _val = val;
        _next = next;
    }
    return self;
}

@end
```

### 5.5 经典题：反转链表

题目描述：给定一个单链表的头节点 `head`，请将链表反转，并返回新的头节点。

必会原因：

- 是很多链表题的基础子问题。
- 能检验你对指针操作的掌握程度。

示例：

- 输入：`1 -> 2 -> 3 -> 4 -> 5`
- 输出：`5 -> 4 -> 3 -> 2 -> 1`

方法名定义：

```objc
- (ListNode *)reverseList:(ListNode *)head;
```

调用示例代码：

```objc
ListNode *n5 = [[ListNode alloc] initWithVal:5 next:nil];
ListNode *n4 = [[ListNode alloc] initWithVal:4 next:n5];
ListNode *n3 = [[ListNode alloc] initWithVal:3 next:n4];
ListNode *n2 = [[ListNode alloc] initWithVal:2 next:n3];
ListNode *head = [[ListNode alloc] initWithVal:1 next:n2];
ListNode *newHead = [self reverseList:head];
NSLog(@"new head = %ld", (long)newHead.val);
```

参考答案：见 [答案手册 5.5 反转链表](./ios-algorithm-interview-handbook-answers#55-反转链表)

### 5.6 经典题：合并两个有序链表

题目描述：给定两个按升序排列的链表，将它们合并为一个新的升序链表并返回新链表头节点。

知识点：

- 虚拟头节点
- 双指针

示例：

- 输入：`1 -> 2 -> 4` 和 `1 -> 3 -> 4`
- 输出：`1 -> 1 -> 2 -> 3 -> 4 -> 4`

方法名定义：

```objc
- (ListNode *)mergeTwoLists:(ListNode *)list1 list2:(ListNode *)list2;
```

调用示例代码：

```objc
ListNode *a3 = [[ListNode alloc] initWithVal:4 next:nil];
ListNode *a2 = [[ListNode alloc] initWithVal:2 next:a3];
ListNode *list1 = [[ListNode alloc] initWithVal:1 next:a2];

ListNode *b3 = [[ListNode alloc] initWithVal:4 next:nil];
ListNode *b2 = [[ListNode alloc] initWithVal:3 next:b3];
ListNode *list2 = [[ListNode alloc] initWithVal:1 next:b2];

ListNode *merged = [self mergeTwoLists:list1 list2:list2];
NSLog(@"merged head = %ld", (long)merged.val);
```

参考答案：见 [答案手册 5.6 合并两个有序链表](./ios-algorithm-interview-handbook-answers#56-合并两个有序链表)

### 5.7 经典题：删除链表的倒数第 N 个结点

题目描述：给定一个链表，删除链表的倒数第 `N` 个节点，并返回删除后的头节点。

知识点：

- 快慢指针
- 虚拟头节点

示例：

- 输入：`1 -> 2 -> 3 -> 4 -> 5, n = 2`
- 输出：`1 -> 2 -> 3 -> 5`

方法名定义：

```objc
- (ListNode *)removeNthFromEnd:(ListNode *)head n:(NSInteger)n;
```

调用示例代码：

```objc
ListNode *n5 = [[ListNode alloc] initWithVal:5 next:nil];
ListNode *n4 = [[ListNode alloc] initWithVal:4 next:n5];
ListNode *n3 = [[ListNode alloc] initWithVal:3 next:n4];
ListNode *n2 = [[ListNode alloc] initWithVal:2 next:n3];
ListNode *head = [[ListNode alloc] initWithVal:1 next:n2];
ListNode *newHead = [self removeNthFromEnd:head n:2];
NSLog(@"head after remove = %ld", (long)newHead.val);
```

参考答案：见 [答案手册 5.7 删除链表的倒数第 N 个结点](./ios-algorithm-interview-handbook-answers#57-删除链表的倒数第-n-个结点)

### 5.8 经典题：环形链表

题目描述：给定一个链表，判断链表中是否存在环。如果某个节点通过不断沿着 `next` 指针访问可以再次回到自己经过的节点，就说明有环。

知识点：

- 快慢指针判环

示例：

- 输入：`3 -> 2 -> 0 -> -4`，尾节点连接到值为 `2` 的节点
- 输出：`true`

方法名定义：

```objc
- (BOOL)hasCycle:(ListNode *)head;
```

调用示例代码：

```objc
ListNode *n4 = [[ListNode alloc] initWithVal:-4 next:nil];
ListNode *n3 = [[ListNode alloc] initWithVal:0 next:n4];
ListNode *n2 = [[ListNode alloc] initWithVal:2 next:n3];
ListNode *head = [[ListNode alloc] initWithVal:3 next:n2];
n4.next = n2;
BOOL hasCycle = [self hasCycle:head];
NSLog(@"has cycle = %@", hasCycle ? @"YES" : @"NO");
```

参考答案：见 [答案手册 5.8 环形链表](./ios-algorithm-interview-handbook-answers#58-环形链表)

### 5.9 本章小结与模板整理

本章至少要默写三套模板：

- 反转链表
- 合并链表
- 快慢指针删节点

推荐题单：

- `206. 反转链表`
- `21. 合并两个有序链表`
- `19. 删除链表的倒数第 N 个结点`
- `141. 环形链表`

---

## 第 6 章 栈与队列

### 6.1 栈的典型应用场景

栈常用于：

- 括号匹配
- 最近更大 / 更小元素
- 递归转迭代

### 6.2 队列的典型应用场景

队列常用于：

- 层序遍历
- BFS
- 维护先进先出顺序

### 6.3 单调栈入门

单调栈适合解决：

- 下一个更大元素
- 温度上升等待天数
- 柱状图面积类问题

### 6.4 经典题：有效的括号

题目描述：给定一个只包含 `()[]{}` 的字符串，判断括号是否有效。有效要求左括号必须用相同类型的右括号闭合，且顺序正确。

知识点：

- 栈
- 匹配关系

示例：

- 输入：`"()[]{}"`
- 输出：`true`

方法名定义：

```objc
- (BOOL)isValid:(NSString *)s;
```

调用示例代码：

```objc
BOOL valid = [self isValid:@"()[]{}"];
NSLog(@"valid = %@", valid ? @"YES" : @"NO");
```

参考答案：见 [答案手册 6.4 有效的括号](./ios-algorithm-interview-handbook-answers#64-有效的括号)

### 6.5 经典题：用栈实现队列

题目描述：请仅使用两个栈来实现一个队列，支持入队、出队、查看队头和判断是否为空等基本操作。

知识点：

- 双栈模拟
- 数据流转

示例：

- 输入：依次执行 `push(1), push(2), peek(), pop(), empty()`
- 输出：`2, 1, false`

类定义：

```objc
@interface MyQueue : NSObject
- (void)push:(NSInteger)x;
- (NSInteger)pop;
- (NSInteger)peek;
- (BOOL)empty;
@end
```

调用示例代码：

```objc
MyQueue *queue = [[MyQueue alloc] init];
[queue push:1];
[queue push:2];
NSLog(@"peek = %ld", (long)[queue peek]);
NSLog(@"pop = %ld", (long)[queue pop]);
NSLog(@"empty = %@", [queue empty] ? @"YES" : @"NO");
```

参考答案：见 [答案手册 6.5 用栈实现队列](./ios-algorithm-interview-handbook-answers#65-用栈实现队列)

### 6.6 经典题：最小栈

题目描述：设计一个栈，除普通的 `push`、`pop`、`top` 操作外，还要支持在 O(1) 时间内获取当前栈中的最小元素。

知识点：

- 辅助栈
- 同步维护最小值

示例：

- 输入：依次执行 `push(-2), push(0), push(-3), getMin(), pop(), top(), getMin()`
- 输出：`-3, 0, -2`

类定义：

```objc
@interface MinStack : NSObject
- (void)push:(NSInteger)val;
- (void)pop;
- (NSInteger)top;
- (NSInteger)getMin;
@end
```

调用示例代码：

```objc
MinStack *stack = [[MinStack alloc] init];
[stack push:-2];
[stack push:0];
[stack push:-3];
NSLog(@"min = %ld", (long)[stack getMin]);
[stack pop];
NSLog(@"top = %ld", (long)[stack top]);
NSLog(@"min = %ld", (long)[stack getMin]);
```

参考答案：见 [答案手册 6.6 最小栈](./ios-algorithm-interview-handbook-answers#66-最小栈)

### 6.7 经典题：每日温度

题目描述：给定一个整数数组 `temperatures`，表示每天的气温。请返回一个数组，`answer[i]` 表示从第 `i` 天开始还要等几天才会有更高的温度；如果之后都不会升高，就填 `0`。

知识点：

- 单调栈
- 等待下一个更大元素

示例：

- 输入：`temperatures = [73,74,75,71,69,72,76,73]`
- 输出：`[1,1,4,2,1,1,0,0]`

方法名定义：

```objc
- (NSArray<NSNumber *> *)dailyTemperatures:(NSArray<NSNumber *> *)temperatures;
```

调用示例代码：

```objc
NSArray<NSNumber *> *temperatures = @[@73, @74, @75, @71, @69, @72, @76, @73];
NSArray<NSNumber *> *result = [self dailyTemperatures:temperatures];
NSLog(@"result = %@", result);
```

参考答案：见 [答案手册 6.7 每日温度](./ios-algorithm-interview-handbook-answers#67-每日温度)

### 6.8 本章小结与模板整理

你需要形成这样的判断：

- 只要题目是“配对”“最近一次”“下一次更大”，先想栈。
- 只要题目是“按层”“最短步数”，先想队列。

推荐题单：

- `20. 有效的括号`
- `232. 用栈实现队列`
- `155. 最小栈`
- `739. 每日温度`

---

## 第 7 章 二叉树基础

### 7.1 二叉树结构与遍历框架

二叉树题不要一开始就纠结代码，先建立递归框架意识：

- 当前节点要做什么
- 左子树要做什么
- 右子树要做什么

### 7.2 前序、中序、后序遍历

三种遍历的访问顺序：

- 前序：根左右
- 中序：左根右
- 后序：左右根

### 7.3 层序遍历

层序遍历通常用队列完成，是 BFS 在树上的直接应用。

### 7.4 递归与迭代的区别

递归写法更自然，迭代写法更接近显式控制流程。

面试里：

- 先写递归通常更稳。
- 如果面试官追问，再补充迭代。

二叉树题最小节点定义：

```objc
@interface TreeNode : NSObject
@property (nonatomic, assign) NSInteger val;
@property (nonatomic, strong, nullable) TreeNode *left;
@property (nonatomic, strong, nullable) TreeNode *right;
- (instancetype)initWithVal:(NSInteger)val left:(TreeNode * _Nullable)left right:(TreeNode * _Nullable)right;
@end

@implementation TreeNode

- (instancetype)initWithVal:(NSInteger)val left:(TreeNode * _Nullable)left right:(TreeNode * _Nullable)right {
    self = [super init];
    if (self) {
        _val = val;
        _left = left;
        _right = right;
    }
    return self;
}

@end
```

### 7.5 经典题：二叉树的最大深度

题目描述：给定一棵二叉树，返回它的最大深度。最大深度是从根节点到最远叶子节点的最长路径上的节点数。

知识点：

- 递归返回值
- 左右子树取最大

示例：

- 输入：`[3,9,20,null,null,15,7]`
- 输出：`3`

方法名定义：

```objc
- (NSInteger)maxDepth:(TreeNode *)root;
```

调用示例代码：

```objc
TreeNode *n15 = [[TreeNode alloc] initWithVal:15 left:nil right:nil];
TreeNode *n7 = [[TreeNode alloc] initWithVal:7 left:nil right:nil];
TreeNode *n20 = [[TreeNode alloc] initWithVal:20 left:n15 right:n7];
TreeNode *n9 = [[TreeNode alloc] initWithVal:9 left:nil right:nil];
TreeNode *root = [[TreeNode alloc] initWithVal:3 left:n9 right:n20];
NSLog(@"depth = %ld", (long)[self maxDepth:root]);
```

参考答案：见 [答案手册 7.5 二叉树的最大深度](./ios-algorithm-interview-handbook-answers#75-二叉树的最大深度)

### 7.6 经典题：对称二叉树

题目描述：给定一棵二叉树，判断它是否轴对称。也就是说，左子树和右子树是否互为镜像。

知识点：

- 镜像比较
- 递归比较两棵子树

示例：

- 输入：`[1,2,2,3,4,4,3]`
- 输出：`true`

方法名定义：

```objc
- (BOOL)isSymmetric:(TreeNode *)root;
```

调用示例代码：

```objc
TreeNode *n3a = [[TreeNode alloc] initWithVal:3 left:nil right:nil];
TreeNode *n4a = [[TreeNode alloc] initWithVal:4 left:nil right:nil];
TreeNode *n4b = [[TreeNode alloc] initWithVal:4 left:nil right:nil];
TreeNode *n3b = [[TreeNode alloc] initWithVal:3 left:nil right:nil];
TreeNode *left = [[TreeNode alloc] initWithVal:2 left:n3a right:n4a];
TreeNode *right = [[TreeNode alloc] initWithVal:2 left:n4b right:n3b];
TreeNode *root = [[TreeNode alloc] initWithVal:1 left:left right:right];
NSLog(@"symmetric = %@", [self isSymmetric:root] ? @"YES" : @"NO");
```

参考答案：见 [答案手册 7.6 对称二叉树](./ios-algorithm-interview-handbook-answers#76-对称二叉树)

### 7.7 经典题：二叉树的层序遍历

题目描述：给定一棵二叉树，按从上到下、从左到右逐层返回节点值。

知识点：

- 队列
- 分层处理

示例：

- 输入：`[3,9,20,null,null,15,7]`
- 输出：`[[3],[9,20],[15,7]]`

方法名定义：

```objc
- (NSArray<NSArray<NSNumber *> *> *)levelOrder:(TreeNode *)root;
```

调用示例代码：

```objc
TreeNode *n15 = [[TreeNode alloc] initWithVal:15 left:nil right:nil];
TreeNode *n7 = [[TreeNode alloc] initWithVal:7 left:nil right:nil];
TreeNode *n20 = [[TreeNode alloc] initWithVal:20 left:n15 right:n7];
TreeNode *n9 = [[TreeNode alloc] initWithVal:9 left:nil right:nil];
TreeNode *root = [[TreeNode alloc] initWithVal:3 left:n9 right:n20];
NSLog(@"levels = %@", [self levelOrder:root]);
```

参考答案：见 [答案手册 7.7 二叉树的层序遍历](./ios-algorithm-interview-handbook-answers#77-二叉树的层序遍历)

### 7.8 经典题：翻转二叉树

题目描述：给定一棵二叉树，将每个节点的左右子树交换，返回翻转后的根节点。

知识点：

- 递归交换左右子树

示例：

- 输入：`[4,2,7,1,3,6,9]`
- 输出：`[4,7,2,9,6,3,1]`

方法名定义：

```objc
- (TreeNode *)invertTree:(TreeNode *)root;
```

调用示例代码：

```objc
TreeNode *n1 = [[TreeNode alloc] initWithVal:1 left:nil right:nil];
TreeNode *n3 = [[TreeNode alloc] initWithVal:3 left:nil right:nil];
TreeNode *n6 = [[TreeNode alloc] initWithVal:6 left:nil right:nil];
TreeNode *n9 = [[TreeNode alloc] initWithVal:9 left:nil right:nil];
TreeNode *left = [[TreeNode alloc] initWithVal:2 left:n1 right:n3];
TreeNode *right = [[TreeNode alloc] initWithVal:7 left:n6 right:n9];
TreeNode *root = [[TreeNode alloc] initWithVal:4 left:left right:right];
TreeNode *newRoot = [self invertTree:root];
NSLog(@"root after invert = %ld", (long)newRoot.val);
```

参考答案：见 [答案手册 7.8 翻转二叉树](./ios-algorithm-interview-handbook-answers#78-翻转二叉树)

### 7.9 本章小结与模板整理

树题最重要的是先想清楚递归函数定义。

推荐题单：

- `104. 二叉树的最大深度`
- `101. 对称二叉树`
- `102. 二叉树的层序遍历`
- `226. 翻转二叉树`

---

## 第 8 章 二叉树进阶

### 8.1 二叉搜索树的性质

BST 的核心性质：

- 左子树所有节点都小于根
- 右子树所有节点都大于根

这会带来：

- 中序遍历有序
- 查找效率更高

### 8.2 路径问题入门

树的路径题常见两种：

- 从根到叶
- 任意路径

基础面试更多考“从根到叶”。

### 8.3 最近公共祖先基础

最近公共祖先题的关键在于：

- 目标节点是否分布在左右子树两侧
- 当前节点是否就是分叉点

### 8.4 经典题：验证二叉搜索树

题目描述：给定一棵二叉树，判断它是否是有效的二叉搜索树。要求任意节点左子树都小于该节点，右子树都大于该节点，并且左右子树本身也必须是 BST。

知识点：

- 中序遍历递增
- 上下界约束

示例：

- 输入：`[2,1,3]`
- 输出：`true`

方法名定义：

```objc
- (BOOL)isValidBST:(TreeNode *)root;
```

调用示例代码：

```objc
TreeNode *left = [[TreeNode alloc] initWithVal:1 left:nil right:nil];
TreeNode *right = [[TreeNode alloc] initWithVal:3 left:nil right:nil];
TreeNode *root = [[TreeNode alloc] initWithVal:2 left:left right:right];
NSLog(@"is valid bst = %@", [self isValidBST:root] ? @"YES" : @"NO");
```

参考答案：见 [答案手册 8.4 验证二叉搜索树](./ios-algorithm-interview-handbook-answers#84-验证二叉搜索树)

### 8.5 经典题：二叉搜索树中的搜索

题目描述：给定二叉搜索树的根节点和一个值 `val`，在树中找到值等于 `val` 的节点并返回该节点；如果不存在则返回 `nil`。

知识点：

- 利用 BST 性质剪枝

示例：

- 输入：BST = `[4,2,7,1,3]`, `val = 2`
- 输出：以值 `2` 为根的子树

方法名定义：

```objc
- (TreeNode *)searchBST:(TreeNode *)root val:(NSInteger)val;
```

调用示例代码：

```objc
TreeNode *n1 = [[TreeNode alloc] initWithVal:1 left:nil right:nil];
TreeNode *n3 = [[TreeNode alloc] initWithVal:3 left:nil right:nil];
TreeNode *n7 = [[TreeNode alloc] initWithVal:7 left:nil right:nil];
TreeNode *left = [[TreeNode alloc] initWithVal:2 left:n1 right:n3];
TreeNode *root = [[TreeNode alloc] initWithVal:4 left:left right:n7];
TreeNode *node = [self searchBST:root val:2];
NSLog(@"search result = %ld", (long)node.val);
```

参考答案：见 [答案手册 8.5 二叉搜索树中的搜索](./ios-algorithm-interview-handbook-answers#85-二叉搜索树中的搜索)

### 8.6 经典题：路径总和

题目描述：给定一棵二叉树和一个目标和 `targetSum`，判断树中是否存在一条从根节点到叶子节点的路径，使路径上所有节点值之和等于 `targetSum`。

知识点：

- 递归减目标值
- 叶子节点终止判断

示例：

- 输入：`root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22`
- 输出：`true`

方法名定义：

```objc
- (BOOL)hasPathSum:(TreeNode *)root targetSum:(NSInteger)targetSum;
```

调用示例代码：

```objc
TreeNode *n7 = [[TreeNode alloc] initWithVal:7 left:nil right:nil];
TreeNode *n2 = [[TreeNode alloc] initWithVal:2 left:nil right:nil];
TreeNode *n11 = [[TreeNode alloc] initWithVal:11 left:n7 right:n2];
TreeNode *n13 = [[TreeNode alloc] initWithVal:13 left:nil right:nil];
TreeNode *n1 = [[TreeNode alloc] initWithVal:1 left:nil right:nil];
TreeNode *n4r = [[TreeNode alloc] initWithVal:4 left:nil right:n1];
TreeNode *n4l = [[TreeNode alloc] initWithVal:4 left:n11 right:nil];
TreeNode *n8 = [[TreeNode alloc] initWithVal:8 left:n13 right:n4r];
TreeNode *root = [[TreeNode alloc] initWithVal:5 left:n4l right:n8];
NSLog(@"has path sum = %@", [self hasPathSum:root targetSum:22] ? @"YES" : @"NO");
```

参考答案：见 [答案手册 8.6 路径总和](./ios-algorithm-interview-handbook-answers#86-路径总和)

### 8.7 经典题：二叉树的最近公共祖先

题目描述：给定一棵二叉树以及其中两个节点 `p` 和 `q`，找出它们的最近公共祖先。最近公共祖先指的是离这两个节点最近、且同时是它们祖先的那个节点。

知识点：

- 分治递归

示例：

- 输入：`root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1`
- 输出：`3`

方法名定义：

```objc
- (TreeNode *)lowestCommonAncestor:(TreeNode *)root p:(TreeNode *)p q:(TreeNode *)q;
```

调用示例代码：

```objc
TreeNode *n6 = [[TreeNode alloc] initWithVal:6 left:nil right:nil];
TreeNode *n7 = [[TreeNode alloc] initWithVal:7 left:nil right:nil];
TreeNode *n4 = [[TreeNode alloc] initWithVal:4 left:nil right:nil];
TreeNode *n2 = [[TreeNode alloc] initWithVal:2 left:n7 right:n4];
TreeNode *n0 = [[TreeNode alloc] initWithVal:0 left:nil right:nil];
TreeNode *n8 = [[TreeNode alloc] initWithVal:8 left:nil right:nil];
TreeNode *p = [[TreeNode alloc] initWithVal:5 left:n6 right:n2];
TreeNode *q = [[TreeNode alloc] initWithVal:1 left:n0 right:n8];
TreeNode *root = [[TreeNode alloc] initWithVal:3 left:p right:q];
TreeNode *ancestor = [self lowestCommonAncestor:root p:p q:q];
NSLog(@"ancestor = %ld", (long)ancestor.val);
```

参考答案：见 [答案手册 8.7 二叉树的最近公共祖先](./ios-algorithm-interview-handbook-answers#87-二叉树的最近公共祖先)

### 8.8 本章小结与模板整理

本章的重点不是题多，而是学会利用树的结构性质进行剪枝。

推荐题单：

- `98. 验证二叉搜索树`
- `700. 二叉搜索树中的搜索`
- `112. 路径总和`
- `236. 二叉树的最近公共祖先`

---

## 第 9 章 递归与回溯

### 9.1 什么题一眼看出可以回溯

如果题目里出现以下关键词，通常可以考虑回溯：

- 所有组合
- 所有排列
- 所有子集
- 列出全部可能结果

### 9.2 回溯的通用模板

回溯本质是：

- 做选择
- 递归进入下一层
- 撤销选择

你必须清楚三个变量：

- 当前路径
- 可选列表
- 结束条件

### 9.3 去重与剪枝基础

回溯题最容易写出超时代码，所以要学会：

- 去重
- 提前剪枝
- 控制起始下标

### 9.4 经典题：子集

题目描述：给定一个互不相同的整数数组，返回该数组所有可能的子集，也就是幂集。结果中不能包含重复子集。

知识点：

- 选或不选
- 路径收集

示例：

- 输入：`nums = [1,2,3]`
- 输出：`[[],[1],[2],[3],[1,2],[1,3],[2,3],[1,2,3]]`

方法名定义：

```objc
- (NSArray<NSArray<NSNumber *> *> *)subsets:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSArray<NSArray<NSNumber *> *> *result = [self subsets:@[@1, @2, @3]];
NSLog(@"subsets = %@", result);
```

参考答案：见 [答案手册 9.4 子集](./ios-algorithm-interview-handbook-answers#94-子集)

### 9.5 经典题：全排列

题目描述：给定一个不含重复数字的数组，返回它的所有可能排列。

知识点：

- `used` 数组或集合
- 每层选择一个未使用元素

示例：

- 输入：`nums = [1,2,3]`
- 输出：`[[1,2,3],[1,3,2],[2,1,3]...]`

方法名定义：

```objc
- (NSArray<NSArray<NSNumber *> *> *)permute:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSArray<NSArray<NSNumber *> *> *result = [self permute:@[@1, @2, @3]];
NSLog(@"permute = %@", result);
```

参考答案：见 [答案手册 9.5 全排列](./ios-algorithm-interview-handbook-answers#95-全排列)

### 9.6 经典题：组合总和

题目描述：给定一个无重复元素的数组 `candidates` 和一个目标值 `target`，找出所有和为 `target` 的不同组合。每个数字可以被无限次选取。

知识点：

- 剪枝
- 同层 / 同枝概念

示例：

- 输入：`candidates = [2,3,6,7], target = 7`
- 输出：`[[2,2,3],[7]]`

方法名定义：

```objc
- (NSArray<NSArray<NSNumber *> *> *)combinationSum:(NSArray<NSNumber *> *)candidates target:(NSInteger)target;
```

调用示例代码：

```objc
NSArray<NSArray<NSNumber *> *> *result = [self combinationSum:@[@2, @3, @6, @7] target:7];
NSLog(@"combination sum = %@", result);
```

参考答案：见 [答案手册 9.6 组合总和](./ios-algorithm-interview-handbook-answers#96-组合总和)

### 9.7 经典题：括号生成

题目描述：给定整数 `n`，表示括号对数，请生成所有由 `n` 对括号组成且有效的括号组合。

知识点：

- 状态约束
- 左右括号数量控制

示例：

- 输入：`n = 3`
- 输出：`["((()))","(()())","(())()","()(())","()()()"]`

方法名定义：

```objc
- (NSArray<NSString *> *)generateParenthesis:(NSInteger)n;
```

调用示例代码：

```objc
NSArray<NSString *> *result = [self generateParenthesis:3];
NSLog(@"parenthesis = %@", result);
```

参考答案：见 [答案手册 9.7 括号生成](./ios-algorithm-interview-handbook-answers#97-括号生成)

### 9.8 本章小结与模板整理

回溯题的面试表达重点：

- 先说树形搜索结构。
- 再说路径和终止条件。
- 最后说明如何去重和剪枝。

推荐题单：

- `78. 子集`
- `46. 全排列`
- `39. 组合总和`
- `22. 括号生成`

---

## 第 10 章 二分查找

### 10.1 二分查找的适用条件

二分查找不一定只用于“查找数字”，更常用于：

- 有序数组查找
- 单调性问题
- 查找边界

### 10.2 左闭右闭与左闭右开写法

这部分容易混乱，所以建议你只熟练掌握一种写法。

建议：

- 面试时优先使用左闭右闭 `[left, right]`
- 每次循环都明确更新边界

### 10.3 查找边界问题

很多二分题难点不在“能不能找到”，而在：

- 找第一个等于目标的位置
- 找最后一个等于目标的位置
- 找第一个大于等于目标的位置

### 10.4 经典题：二分查找

题目描述：给定一个升序数组和一个目标值 `target`，如果目标值存在则返回其下标，否则返回 `-1`。

目标：

- 稳定写出标准模板

示例：

- 输入：`nums = [-1,0,3,5,9,12], target = 9`
- 输出：`4`

方法名定义：

```objc
- (NSInteger)search:(NSArray<NSNumber *> *)nums target:(NSInteger)target;
```

调用示例代码：

```objc
NSInteger index = [self search:@[@-1, @0, @3, @5, @9, @12] target:9];
NSLog(@"index = %ld", (long)index);
```

参考答案：见 [答案手册 10.4 二分查找](./ios-algorithm-interview-handbook-answers#104-二分查找)

### 10.5 经典题：搜索插入位置

题目描述：给定一个升序数组和目标值 `target`，如果目标值存在则返回其下标；如果不存在，返回它应当被插入的位置。

目标：

- 理解“找不到时应该插在哪里”

示例：

- 输入：`nums = [1,3,5,6], target = 5`
- 输出：`2`

方法名定义：

```objc
- (NSInteger)searchInsert:(NSArray<NSNumber *> *)nums target:(NSInteger)target;
```

调用示例代码：

```objc
NSInteger index = [self searchInsert:@[@1, @3, @5, @6] target:5];
NSLog(@"insert index = %ld", (long)index);
```

参考答案：见 [答案手册 10.5 搜索插入位置](./ios-algorithm-interview-handbook-answers#105-搜索插入位置)

### 10.6 经典题：在排序数组中查找元素的第一个和最后一个位置

题目描述：给定一个升序数组和目标值 `target`，找出目标值在数组中的起始位置和结束位置。如果目标值不存在，返回 `[-1, -1]`。

目标：

- 熟悉边界二分

示例：

- 输入：`nums = [5,7,7,8,8,10], target = 8`
- 输出：`[3,4]`

方法名定义：

```objc
- (NSArray<NSNumber *> *)searchRange:(NSArray<NSNumber *> *)nums target:(NSInteger)target;
```

调用示例代码：

```objc
NSArray<NSNumber *> *range = [self searchRange:@[@5, @7, @7, @8, @8, @10] target:8];
NSLog(@"range = %@", range);
```

参考答案：见 [答案手册 10.6 在排序数组中查找元素的第一个和最后一个位置](./ios-algorithm-interview-handbook-answers#106-在排序数组中查找元素的第一个和最后一个位置)

### 10.7 经典题：搜索旋转排序数组

题目描述：给定一个经过旋转的升序数组和目标值 `target`，请在 O(log n) 时间内找到目标值下标；如果不存在返回 `-1`。

目标：

- 识别局部有序区间

示例：

- 输入：`nums = [4,5,6,7,0,1,2], target = 0`
- 输出：`4`

方法名定义：

```objc
- (NSInteger)searchInRotated:(NSArray<NSNumber *> *)nums target:(NSInteger)target;
```

调用示例代码：

```objc
NSInteger index = [self searchInRotated:@[@4, @5, @6, @7, @0, @1, @2] target:0];
NSLog(@"index = %ld", (long)index);
```

参考答案：见 [答案手册 10.7 搜索旋转排序数组](./ios-algorithm-interview-handbook-answers#107-搜索旋转排序数组)

### 10.8 本章小结与模板整理

二分查找最关键的是不写错边界。

推荐题单：

- `704. 二分查找`
- `35. 搜索插入位置`
- `34. 在排序数组中查找元素的第一个和最后一个位置`
- `33. 搜索旋转排序数组`

---

## 第 11 章 动态规划入门

### 11.1 什么问题适合用动态规划

动态规划一般满足两个条件：

- 最优子结构
- 重叠子问题

常见信号：

- 最多 / 最少
- 方案数
- 是否可达

### 11.2 状态、转移、初始化三要素

做 DP 题时强制自己回答：

- `dp[i]` 或 `dp[i][j]` 表示什么
- 状态如何转移
- 初始值是什么

### 11.3 一维 DP 基础

适合：

- 爬楼梯
- 打家劫舍
- 线性状态转移

### 11.4 二维 DP 基础

适合：

- 网格路径
- 两个字符串比较

### 11.5 经典题：爬楼梯

题目描述：你正在爬楼梯，每次可以爬 `1` 阶或 `2` 阶。给定楼梯总阶数 `n`，求有多少种不同的方法爬到楼顶。

入门价值：

- 最简单的状态转移题

示例：

- 输入：`n = 3`
- 输出：`3`

方法名定义：

```objc
- (NSInteger)climbStairs:(NSInteger)n;
```

调用示例代码：

```objc
NSLog(@"ways = %ld", (long)[self climbStairs:3]);
```

参考答案：见 [答案手册 11.5 爬楼梯](./ios-algorithm-interview-handbook-answers#115-爬楼梯)

### 11.6 经典题：打家劫舍

题目描述：给定一个数组，`nums[i]` 表示第 `i` 间房屋的金额。相邻房屋不能在同一晚偷取，求在不触发警报的情况下最多能偷到多少钱。

知识点：

- 选当前 or 不选当前

示例：

- 输入：`nums = [1,2,3,1]`
- 输出：`4`

方法名定义：

```objc
- (NSInteger)rob:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSLog(@"max rob = %ld", (long)[self rob:@[@1, @2, @3, @1]]);
```

参考答案：见 [答案手册 11.6 打家劫舍](./ios-algorithm-interview-handbook-answers#116-打家劫舍)

### 11.7 经典题：不同路径

题目描述：一个机器人位于 `m x n` 网格左上角，只能向右或向下移动一步。求它从左上角走到右下角一共有多少条不同路径。

知识点：

- 网格 DP

示例：

- 输入：`m = 3, n = 7`
- 输出：`28`

方法名定义：

```objc
- (NSInteger)uniquePaths:(NSInteger)m n:(NSInteger)n;
```

调用示例代码：

```objc
NSLog(@"paths = %ld", (long)[self uniquePaths:3 n:7]);
```

参考答案：见 [答案手册 11.7 不同路径](./ios-algorithm-interview-handbook-answers#117-不同路径)

### 11.8 经典题：最小路径和

题目描述：给定一个非负整数网格，每次只能向右或向下移动一步，求从左上角到右下角路径上的数字总和最小值。

知识点：

- 路径最优值转移

示例：

- 输入：`grid = [[1,3,1],[1,5,1],[4,2,1]]`
- 输出：`7`

方法名定义：

```objc
- (NSInteger)minPathSum:(NSArray<NSArray<NSNumber *> *> *)grid;
```

调用示例代码：

```objc
NSArray<NSArray<NSNumber *> *> *grid = @[
  @[@1, @3, @1],
  @[@1, @5, @1],
  @[@4, @2, @1]
];
NSLog(@"min path sum = %ld", (long)[self minPathSum:grid]);
```

参考答案：见 [答案手册 11.8 最小路径和](./ios-algorithm-interview-handbook-answers#118-最小路径和)

### 11.9 本章小结与模板整理

不要把 DP 当成玄学。先定义状态，很多题就清楚了。

推荐题单：

- `70. 爬楼梯`
- `198. 打家劫舍`
- `62. 不同路径`
- `64. 最小路径和`

---

## 第 12 章 动态规划进阶

### 12.1 子序列问题入门

子序列题常见于：

- 最长递增子序列
- 最长公共子序列

### 12.2 背包问题入门

背包题是 DP 里的经典模型。

你不需要一开始就刷很难，但要先理解：

- 容量
- 物品
- 选或不选

### 12.3 经典题：最长递增子序列

题目描述：给定一个整数数组，找出其中最长严格递增子序列的长度。这里的子序列不要求连续。

知识点：

- 一维 DP
- 枚举前驱状态

示例：

- 输入：`nums = [10,9,2,5,3,7,101,18]`
- 输出：`4`

方法名定义：

```objc
- (NSInteger)lengthOfLIS:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSLog(@"lis = %ld", (long)[self lengthOfLIS:@[@10, @9, @2, @5, @3, @7, @101, @18]]);
```

参考答案：见 [答案手册 12.3 最长递增子序列](./ios-algorithm-interview-handbook-answers#123-最长递增子序列)

### 12.4 经典题：最长公共子序列

题目描述：给定两个字符串，返回它们的最长公共子序列长度。子序列要求相对顺序一致，但不要求连续。

知识点：

- 二维 DP
- 两字符串匹配

示例：

- 输入：`text1 = "abcde", text2 = "ace"`
- 输出：`3`

方法名定义：

```objc
- (NSInteger)longestCommonSubsequence:(NSString *)text1 text2:(NSString *)text2;
```

调用示例代码：

```objc
NSLog(@"lcs = %ld", (long)[self longestCommonSubsequence:@"abcde" text2:@"ace"]);
```

参考答案：见 [答案手册 12.4 最长公共子序列](./ios-algorithm-interview-handbook-answers#124-最长公共子序列)

### 12.5 经典题：零钱兑换

题目描述：给定不同面额的硬币数组 `coins` 和总金额 `amount`，求凑成总金额所需的最少硬币数；如果无法凑出则返回 `-1`。

知识点：

- 完全背包
- 最少数量

示例：

- 输入：`coins = [1,2,5], amount = 11`
- 输出：`3`

方法名定义：

```objc
- (NSInteger)coinChange:(NSArray<NSNumber *> *)coins amount:(NSInteger)amount;
```

调用示例代码：

```objc
NSLog(@"coin count = %ld", (long)[self coinChange:@[@1, @2, @5] amount:11]);
```

参考答案：见 [答案手册 12.5 零钱兑换](./ios-algorithm-interview-handbook-answers#125-零钱兑换)

### 12.6 经典题：分割等和子集

题目描述：给定一个只包含正整数的数组，判断是否可以把它分成两个子集，使得两个子集的元素和相等。

知识点：

- 0-1 背包
- 能否恰好装满

示例：

- 输入：`nums = [1,5,11,5]`
- 输出：`true`

方法名定义：

```objc
- (BOOL)canPartition:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSLog(@"can partition = %@", [self canPartition:@[@1, @5, @11, @5]] ? @"YES" : @"NO");
```

参考答案：见 [答案手册 12.6 分割等和子集](./ios-algorithm-interview-handbook-answers#126-分割等和子集)

### 12.7 本章小结与模板整理

如果时间不多，这一章做到“理解模型 + 能讲思路”就已经很不错。

推荐题单：

- `300. 最长递增子序列`
- `1143. 最长公共子序列`
- `322. 零钱兑换`
- `416. 分割等和子集`

---

## 第 13 章 图论与搜索基础

### 13.1 图的表示方法

图题常见表示：

- 邻接表
- 邻接矩阵
- 用二维网格隐式表示

在面试里，网格题其实就是图题的最常见形态。

### 13.2 深度优先搜索 DFS

DFS 适合：

- 连通块遍历
- 路径搜索
- 递归探索

### 13.3 广度优先搜索 BFS

BFS 适合：

- 最短步数
- 分层扩散
- 按层推进

### 13.4 经典题：岛屿数量

题目描述：给定一个由 `'1'` 和 `'0'` 组成的二维网格，`'1'` 表示陆地，`'0'` 表示水域。岛屿由水平或垂直方向相邻的陆地组成，求网格中岛屿的数量。

知识点：

- DFS / BFS
- 连通块计数

示例：

- 输入：二维网格中有 3 个独立陆地区域
- 输出：`3`

方法名定义：

```objc
- (NSInteger)numIslands:(NSMutableArray<NSMutableArray<NSString *> *> *)grid;
```

调用示例代码：

```objc
NSMutableArray *grid = [@[
  [@[@"1", @"1", @"0", @"0", @"0"] mutableCopy],
  [@[@"1", @"1", @"0", @"0", @"0"] mutableCopy],
  [@[@"0", @"0", @"1", @"0", @"0"] mutableCopy],
  [@[@"0", @"0", @"0", @"1", @"1"] mutableCopy]
] mutableCopy];
NSLog(@"islands = %ld", (long)[self numIslands:grid]);
```

参考答案：见 [答案手册 13.4 岛屿数量](./ios-algorithm-interview-handbook-answers#134-岛屿数量)

### 13.5 经典题：腐烂的橘子

题目描述：在一个网格中，`0` 表示空格，`1` 表示新鲜橘子，`2` 表示腐烂橘子。每分钟，腐烂橘子会让相邻的新鲜橘子腐烂。求全部橘子都腐烂所需的最短时间；如果无法全部腐烂，返回 `-1`。

知识点：

- 多源 BFS
- 分层扩散

示例：

- 输入：`grid = [[2,1,1],[1,1,0],[0,1,1]]`
- 输出：`4`

方法名定义：

```objc
- (NSInteger)orangesRotting:(NSArray<NSArray<NSNumber *> *> *)grid;
```

调用示例代码：

```objc
NSArray *grid = @[
  @[@2, @1, @1],
  @[@1, @1, @0],
  @[@0, @1, @1]
];
NSLog(@"minutes = %ld", (long)[self orangesRotting:grid]);
```

参考答案：见 [答案手册 13.5 腐烂的橘子](./ios-algorithm-interview-handbook-answers#135-腐烂的橘子)

### 13.6 经典题：课程表

题目描述：一共有 `numCourses` 门课程，课程之间可能有先修关系。给定若干先修课对，判断你是否能够完成所有课程。

知识点：

- 有向图
- 拓扑排序
- 入度统计

示例：

- 输入：`numCourses = 2, prerequisites = [[1,0]]`
- 输出：`true`

方法名定义：

```objc
- (BOOL)canFinish:(NSInteger)numCourses prerequisites:(NSArray<NSArray<NSNumber *> *> *)prerequisites;
```

调用示例代码：

```objc
NSArray<NSArray<NSNumber *> *> *prerequisites = @[@[@1, @0]];
NSLog(@"can finish = %@", [self canFinish:2 prerequisites:prerequisites] ? @"YES" : @"NO");
```

参考答案：见 [答案手册 13.6 课程表](./ios-algorithm-interview-handbook-answers#136-课程表)

### 13.7 本章小结与模板整理

判断 DFS 还是 BFS 的一个简单方法：

- 要遍历所有情况，多数先想 DFS。
- 要找最短步数，多数先想 BFS。

推荐题单：

- `200. 岛屿数量`
- `994. 腐烂的橘子`
- `207. 课程表`

---

## 第 14 章 堆、Top K 与高频补充

### 14.1 堆的基本思想

堆适合快速得到“当前最大”或“当前最小”。

常见用途：

- Top K
- 合并多个有序序列
- 动态维护最值

### 14.2 Top K 问题的常见解法

常见方案：

- 排序
- 小顶堆 / 大顶堆
- 快速选择

如果面试题难度适中，能说出“排序和堆”的区别就够用了。

### 14.3 经典题：数组中的第 K 个最大元素

题目描述：给定一个无序数组，返回其中第 `k` 个最大的元素。这里要求的是排序后第 `k` 大的值，不是第 `k` 个不同的值。

知识点：

- 堆
- Top K

示例：

- 输入：`nums = [3,2,1,5,6,4], k = 2`
- 输出：`5`

方法名定义：

```objc
- (NSInteger)findKthLargest:(NSArray<NSNumber *> *)nums k:(NSInteger)k;
```

调用示例代码：

```objc
NSLog(@"kth largest = %ld", (long)[self findKthLargest:@[@3, @2, @1, @5, @6, @4] k:2]);
```

参考答案：见 [答案手册 14.3 数组中的第 K 个最大元素](./ios-algorithm-interview-handbook-answers#143-数组中的第-k-个最大元素)

### 14.4 经典题：前 K 个高频元素

题目描述：给定一个整数数组和整数 `k`，返回其中出现频率前 `k` 高的元素。

知识点：

- 哈希计数 + 堆

示例：

- 输入：`nums = [1,1,1,2,2,3], k = 2`
- 输出：`[1,2]`

方法名定义：

```objc
- (NSArray<NSNumber *> *)topKFrequent:(NSArray<NSNumber *> *)nums k:(NSInteger)k;
```

调用示例代码：

```objc
NSLog(@"top k = %@", [self topKFrequent:@[@1, @1, @1, @2, @2, @3] k:2]);
```

参考答案：见 [答案手册 14.4 前 K 个高频元素](./ios-algorithm-interview-handbook-answers#144-前-k-个高频元素)

### 14.5 经典题：合并 K 个升序链表

题目描述：给定 `k` 个已经按升序排列的链表，请把它们合并成一个升序链表并返回头节点。

知识点：

- 堆
- 多路归并

示例：

- 输入：`lists = [[1,4,5],[1,3,4],[2,6]]`
- 输出：`1 -> 1 -> 2 -> 3 -> 4 -> 4 -> 5 -> 6`

方法名定义：

```objc
- (ListNode *)mergeKLists:(NSArray<ListNode *> *)lists;
```

调用示例代码：

```objc
ListNode *a = [[ListNode alloc] initWithVal:1 next:[[ListNode alloc] initWithVal:4 next:[[ListNode alloc] initWithVal:5 next:nil]]];
ListNode *b = [[ListNode alloc] initWithVal:1 next:[[ListNode alloc] initWithVal:3 next:[[ListNode alloc] initWithVal:4 next:nil]]];
ListNode *c = [[ListNode alloc] initWithVal:2 next:[[ListNode alloc] initWithVal:6 next:nil]];
ListNode *merged = [self mergeKLists:@[a, b, c]];
NSLog(@"merged head = %ld", (long)merged.val);
```

参考答案：见 [答案手册 14.5 合并 K 个升序链表](./ios-algorithm-interview-handbook-answers#145-合并-k-个升序链表)

### 14.6 本章小结与模板整理

这一章属于加分项，但在中高级岗位面试里比较常见。

推荐题单：

- `215. 数组中的第 K 个最大元素`
- `347. 前 K 个高频元素`
- `23. 合并 K 个升序链表`

---

## 第 15 章 面试高频技巧专题

### 15.1 如何快速判断题型

面试时不要急着写代码，先找题型信号：

- 配对、去重、统计：哈希
- 原地修改、有序数组：双指针
- 链表中间、环、倒数第 N 个：快慢指针
- 树遍历：递归 / BFS
- 连续子数组：滑动窗口 / 前缀和
- 最优值、方案数：动态规划

### 15.2 如何从暴力解法优化到最优解

一个成熟的表达方式是：

- 先说暴力解法
- 说明为什么慢
- 再说优化方向

比如：

> 暴力解法是双重循环，时间复杂度 O(n^2)。  
> 由于我们频繁查找某个值是否存在，可以用哈希表把查找降到 O(1)，整体优化到 O(n)。

### 15.3 如何分析时间复杂度和空间复杂度

面试里不要只说结论，要能简单解释来源：

- 单层遍历通常是 O(n)
- 双重嵌套通常是 O(n^2)
- 递归深度会带来额外栈空间
- 哈希表、辅助数组会带来额外空间

### 15.4 如何处理边界条件和异常输入

常见边界：

- 空数组、空字符串
- 只有一个元素
- 所有元素都相同
- 找不到目标
- 树为空、链表为空

### 15.5 如何把代码写得更适合面试官阅读

建议：

- 变量名表达含义
- 分支判断简单清楚
- 少写花哨技巧
- 先稳，再谈极致优化

### 15.6 如何用 Objective-C 稳定手写代码

OC 面试手写注意点：

- 判空写在前面
- 容器类型和元素类型心里要明确
- 如果平台不方便写完整类定义，可以先口头约定节点结构

---

## 第 16 章 iOS 工程师面试表达专题

### 16.1 算法题如何结合实际工程经验来回答

你不需要把算法题答成纯理论题，可以适当联系工程经验。

例如：

- 哈希表可以类比缓存索引。
- BFS 可以类比按层传播状态。
- 堆可以类比动态维护优先级任务。

### 16.2 链表、数组、哈希表在 iOS 开发中的类比

- 数组：适合顺序存储、随机访问。
- 链表：适合频繁插入删除，但实际业务里较少直接手写。
- 哈希表：适合缓存、映射、去重、快速索引。

### 16.3 缓存、查找、遍历在实际项目中的应用表达

你可以这样说：

> 这题本质上是空间换时间，和工程里做缓存索引有点类似。  
> 通过额外的字典结构减少重复遍历，换来更快的查询效率。

### 16.4 面试时常见追问与答题策略

常见追问：

- 如果数据量更大怎么办？
- 能不能再优化空间？
- 如果输入无序 / 有序会怎样？
- 如果允许修改原数组呢？

策略：

- 不要慌着答最优。
- 先说明现有方案。
- 再说可以如何进一步改进。

### 16.5 手写代码后的自检流程

写完后至少自检这几项：

- 判空了吗
- 下标会不会越界
- 指针有没有断链
- 返回值是不是题目要求
- 样例是否跑通

---

## 第 17 章 模拟面试与复习计划

### 17.1 30 分钟模拟面试怎么练

建议流程：

- 5 分钟审题和讲思路
- 15 分钟手写代码
- 5 分钟跑样例
- 5 分钟回答追问

### 17.2 高频错题如何复盘

错题复盘不要只写“不会”。

建议记录：

- 这题属于什么类型
- 当时卡在哪里
- 正确套路是什么
- 下次看到什么信号应该联想到它

### 17.3 一周冲刺计划

- 第 1 天：数组、字符串
- 第 2 天：哈希表、双指针
- 第 3 天：链表、栈、队列
- 第 4 天：树
- 第 5 天：回溯、二分
- 第 6 天：DP
- 第 7 天：模拟面试 + 错题复盘

### 17.4 两周冲刺计划

- 第一周：基础题型全覆盖
- 第二周：高频中等题 + 模拟面试

### 17.5 面试前最后一天看什么

最后一天不要再开新坑，重点看：

- 高频模板
- 错题本
- 常见边界
- 讲题表达

---

## 第 18 章 排序专题

### 18.1 为什么排序值得单独学

排序不是一类“只会出原题”的知识点，而是很多题目的前置操作。

你在面试里经常会遇到这种场景：

- 先排序，再双指针
- 先排序，再合并区间
- 先排序，再做去重
- 用排序作为比哈希更稳的解法

所以排序本身要会，排序后的“题型迁移”更要会。

### 18.2 常见排序复杂度和稳定性

建议至少记住下面这些：

- 冒泡排序：`O(n^2)`，稳定
- 选择排序：`O(n^2)`，不稳定
- 插入排序：`O(n^2)`，稳定
- 快速排序：平均 `O(n log n)`，不稳定
- 归并排序：`O(n log n)`，稳定
- 堆排序：`O(n log n)`，不稳定

面试里最常见追问：

- 哪些排序是稳定的
- 快排和归并的区别
- 为什么很多题先排序再做

### 18.3 Objective-C 里的排序写法

如果题目重点不在“手写排序算法”，你完全可以先用系统排序 API：

```objc
NSArray<NSNumber *> *sorted = [nums sortedArrayUsingComparator:^NSComparisonResult(NSNumber *a, NSNumber *b) {
    return [a compare:b];
}];
```

如果要求降序：

```objc
NSArray<NSNumber *> *sorted = [nums sortedArrayUsingComparator:^NSComparisonResult(NSNumber *a, NSNumber *b) {
    return [b compare:a];
}];
```

面试表达建议：

- 如果题目重点在后续逻辑，可以先用系统排序说明主流程
- 如果面试官继续追问，再补充快排 / 归并的实现思路

### 18.4 经典题：排序数组

题目描述：给你一个整数数组 `nums`，请你将该数组升序排列后返回。

知识点：

- 排序基础
- 比较器
- 系统排序 API

示例：

- 输入：`nums = [5,2,3,1]`
- 输出：`[1,2,3,5]`

方法名定义：

```objc
- (NSArray<NSNumber *> *)sortArray:(NSArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSLog(@"sorted = %@", [self sortArray:@[@5, @2, @3, @1]]);
```

参考答案：见 [答案手册 18.4 排序数组](./ios-algorithm-interview-handbook-answers#184-排序数组)

### 18.5 经典题：合并区间

题目描述：给定一个区间数组 `intervals`，其中 `intervals[i] = [start_i, end_i]`。请你合并所有重叠的区间，并返回一个不重叠的区间数组。

知识点：

- 排序
- 区间合并

示例：

- 输入：`intervals = [[1,3],[2,6],[8,10],[15,18]]`
- 输出：`[[1,6],[8,10],[15,18]]`

方法名定义：

```objc
- (NSArray<NSArray<NSNumber *> *> *)mergeIntervals:(NSArray<NSArray<NSNumber *> *> *)intervals;
```

调用示例代码：

```objc
NSArray *intervals = @[
    @[@1, @3],
    @[@2, @6],
    @[@8, @10],
    @[@15, @18]
];
NSLog(@"merged = %@", [self mergeIntervals:intervals]);
```

参考答案：见 [答案手册 18.5 合并区间](./ios-algorithm-interview-handbook-answers#185-合并区间)

### 18.6 经典题：颜色分类

题目描述：给定一个包含红色、白色和蓝色，一共 `n` 个元素的数组 `nums`，原地对它们进行排序，使得相同颜色的元素相邻，并按照红、白、蓝顺序排列。这里用 `0`、`1` 和 `2` 分别表示红、白、蓝。

知识点：

- 三路划分
- 双指针
- 原地排序

示例：

- 输入：`nums = [2,0,2,1,1,0]`
- 输出：`[0,0,1,1,2,2]`

方法名定义：

```objc
- (void)sortColors:(NSMutableArray<NSNumber *> *)nums;
```

调用示例代码：

```objc
NSMutableArray<NSNumber *> *nums = [@[@2, @0, @2, @1, @1, @0] mutableCopy];
[self sortColors:nums];
NSLog(@"colors = %@", nums);
```

参考答案：见 [答案手册 18.6 颜色分类](./ios-algorithm-interview-handbook-answers#186-颜色分类)

### 18.7 本章小结与模板整理

这一章要掌握的不只是“会排”，更重要的是：

- 知道什么时候先排序
- 知道排序后常接双指针 / 区间合并
- 知道稳定性和复杂度怎么答

推荐题单：

- `912. 排序数组`
- `56. 合并区间`
- `75. 颜色分类`

---

## 附录 A Objective-C 刷题模板速查

### A.1 数组与字符串模板

```objc
for (NSInteger i = 0; i < nums.count; i++) {
    // 处理 nums[i]
}
```

双指针模板：

```objc
NSInteger left = 0;
NSInteger right = nums.count - 1;
while (left < right) {
    // 根据条件移动 left / right
}
```

### A.2 哈希表模板

```objc
NSMutableDictionary<NSNumber *, NSNumber *> *map = [NSMutableDictionary dictionary];
for (NSInteger i = 0; i < nums.count; i++) {
    NSNumber *key = @(nums[i]);
    map[key] = @([map[key] integerValue] + 1);
}
```

### A.3 链表模板

反转链表：

```objc
ListNode *prev = nil;
ListNode *cur = head;
while (cur) {
    ListNode *next = cur.next;
    cur.next = prev;
    prev = cur;
    cur = next;
}
return prev;
```

### A.4 二叉树递归模板

```objc
- (void)dfs:(TreeNode *)root {
    if (!root) {
        return;
    }
    [self dfs:root.left];
    [self dfs:root.right];
}
```

### A.5 BFS / DFS 模板

DFS 核心：

- 先判边界
- 标记访问
- 递归四个方向或所有邻居

BFS 核心：

- 队列初始化
- 每轮处理当前层
- 扩展下一层

### A.6 二分查找模板

```objc
NSInteger left = 0;
NSInteger right = nums.count - 1;
while (left <= right) {
    NSInteger mid = left + (right - left) / 2;
    if (nums[mid] == target) {
        return mid;
    } else if (nums[mid] < target) {
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
return -1;
```

### A.7 回溯模板

```objc
- (void)backtrack:(NSMutableArray *)path nums:(NSArray *)nums used:(NSMutableArray *)used {
    if (path.count == nums.count) {
        // 收集结果
        return;
    }

    for (NSInteger i = 0; i < nums.count; i++) {
        if ([used[i] boolValue]) {
            continue;
        }
        used[i] = @YES;
        [path addObject:nums[i]];
        [self backtrack:path nums:nums used:used];
        [path removeLastObject];
        used[i] = @NO;
    }
}
```

### A.8 动态规划模板

```objc
NSMutableArray<NSNumber *> *dp = [NSMutableArray arrayWithCapacity:n + 1];
for (NSInteger i = 0; i <= n; i++) {
    [dp addObject:@0];
}

dp[0] = @0;
for (NSInteger i = 1; i <= n; i++) {
    // 状态转移
}
```

---

## 附录 B 推荐练习题清单

### B.1 必刷 Easy 题

- `1. 两数之和`
- `20. 有效的括号`
- `21. 合并两个有序链表`
- `26. 删除有序数组中的重复项`
- `27. 移除元素`
- `70. 爬楼梯`
- `88. 合并两个有序数组`
- `104. 二叉树的最大深度`
- `121. 买卖股票的最佳时机`
- `125. 验证回文串`
- `136. 只出现一次的数字`
- `141. 环形链表`
- `206. 反转链表`
- `217. 存在重复元素`
- `226. 翻转二叉树`
- `242. 有效的字母异位词`
- `344. 反转字符串`
- `387. 字符串中的第一个唯一字符`
- `700. 二叉搜索树中的搜索`

### B.2 必刷 Medium 题

- `11. 盛最多水的容器`
- `19. 删除链表的倒数第 N 个结点`
- `22. 括号生成`
- `33. 搜索旋转排序数组`
- `34. 在排序数组中查找元素的第一个和最后一个位置`
- `39. 组合总和`
- `46. 全排列`
- `62. 不同路径`
- `64. 最小路径和`
- `78. 子集`
- `98. 验证二叉搜索树`
- `102. 二叉树的层序遍历`
- `112. 路径总和`
- `198. 打家劫舍`
- `200. 岛屿数量`
- `207. 课程表`
- `209. 长度最小的子数组`
- `215. 数组中的第 K 个最大元素`
- `300. 最长递增子序列`
- `322. 零钱兑换`
- `347. 前 K 个高频元素`
- `416. 分割等和子集`
- `56. 合并区间`
- `75. 颜色分类`
- `912. 排序数组`
- `739. 每日温度`
- `994. 腐烂的橘子`
- `1143. 最长公共子序列`

### B.3 面试前重刷题单

如果时间紧，只重刷下面这些：

- `1. 两数之和`
- `20. 有效的括号`
- `21. 合并两个有序链表`
- `26. 删除有序数组中的重复项`
- `70. 爬楼梯`
- `88. 合并两个有序数组`
- `104. 二叉树的最大深度`
- `121. 买卖股票的最佳时机`
- `125. 验证回文串`
- `141. 环形链表`
- `206. 反转链表`
- `11. 盛最多水的容器`
- `19. 删除链表的倒数第 N 个结点`
- `39. 组合总和`
- `46. 全排列`
- `102. 二叉树的层序遍历`
- `198. 打家劫舍`
- `200. 岛屿数量`
- `209. 长度最小的子数组`
- `56. 合并区间`
- `75. 颜色分类`
- `322. 零钱兑换`

### B.4 错题复盘记录模板

你可以按下面这个格式记录：

```markdown
## 题目名称

- 题号：
- 类型：
- 难度：
- 第一次错误原因：
- 正确思路关键词：
- 这题的标准模板：
- 容易忽略的边界：
- 下次再遇到的识别信号：
```

---

## 结语

对于 iOS 工程师来说，算法面试最重要的不是“做超难题”，而是：

- 基础题型覆盖全
- 高频套路足够熟
- Objective-C 代码写得稳
- 能把思路和工程经验讲清楚

如果你按这份手册把题单至少过两轮，再配合模拟面试去练表达，已经足够覆盖绝大多数中初级到中高级 iOS 岗位的算法面试需求。

---

## 附录 C 手册阅读方式

由于 `Typora` 对 `<details>` 折叠支持不稳定，这份手册改为“双文件模式”：

- 主手册：只放题目、示例、知识点
- 答案手册：单独放思路、代码、易错点

这样你在 `Typora` 里默认看到的就是题目本身，只有在需要时才点进答案文件查看。

示例格式如下：

```markdown
### 经典题：两数之和

题目描述：给定一个整数数组 `nums` 和目标值 `target`，找出和为 `target` 的两个元素下标。

示例：
- 输入：`nums = [2,7,11,15], target = 9`
- 输出：`[0,1]`

参考答案：见 [答案手册中的对应题目](./ios-algorithm-interview-handbook-answers#33-两数之和)
```
