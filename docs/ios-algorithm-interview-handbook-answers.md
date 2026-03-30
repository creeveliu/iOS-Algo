---
layout: handbook
title: iOS 工程师算法面试学习手册答案
description: 与主手册配套的参考答案、Objective-C 实现和常见错误点。
---

> 对应题目主文件：
> [index.md](./)

---

## 1.5 移除元素

为什么会想到这个解法：

- 题目要求“原地删除”，说明不适合新开一个结果数组。
- 删除动作本质上可以转化成“把要保留的元素依次往前覆盖”。
- 所以最自然的方式就是快慢指针：快指针读，慢指针写。

Objective-C 参考实现：

```objc
- (NSInteger)removeElement:(NSMutableArray<NSNumber *> *)nums val:(NSInteger)val {
    NSInteger slow = 0;
    for (NSInteger fast = 0; fast < nums.count; fast++) {
        if (nums[fast].integerValue != val) {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    return slow;
}
```

常见错误点：

- 把“删除”理解成频繁 `removeObjectAtIndex:`，这样会导致代码更复杂。
- 忘了返回的是新长度，而不是新数组。
- 没意识到数组后面的元素可以不管，只有前 `slow` 个元素才是有效结果。

---

## 1.6 合并两个有序数组

为什么会想到这个解法：

- 两个数组本来就有序，优先想到双指针。
- 但 `nums1` 前半部分有有效数据，如果从前往后写，会覆盖还没比较的元素。
- 所以应该从后往前填，把较大的元素先放到末尾。

Objective-C 参考实现：

```objc
- (void)merge:(NSMutableArray<NSNumber *> *)nums1
            m:(NSInteger)m
         nums2:(NSArray<NSNumber *> *)nums2
            n:(NSInteger)n {
    NSInteger i = m - 1;
    NSInteger j = n - 1;
    NSInteger k = m + n - 1;

    while (i >= 0 && j >= 0) {
        if (nums1[i].integerValue > nums2[j].integerValue) {
            nums1[k] = nums1[i];
            i--;
        } else {
            nums1[k] = nums2[j];
            j--;
        }
        k--;
    }

    while (j >= 0) {
        nums1[k] = nums2[j];
        j--;
        k--;
    }
}
```

常见错误点：

- 从前往后合并导致未处理数据被覆盖。
- 忘了 `nums2` 还有剩余元素时要继续拷贝。
- 边界判断里把 `i >= 0`、`j >= 0` 写错。

---

## 1.7 买卖股票的最佳时机

为什么会想到这个解法：

- 只能买卖一次，所以不需要复杂状态。
- 对于每一天，最优卖出收益取决于它前面出现过的最低买入价格。
- 因此一边遍历一边维护“历史最低价”和“当前最大利润”就够了。

Objective-C 参考实现：

```objc
- (NSInteger)maxProfit:(NSArray<NSNumber *> *)prices {
    if (prices.count == 0) {
        return 0;
    }

    NSInteger minPrice = prices[0].integerValue;
    NSInteger maxProfit = 0;

    for (NSInteger i = 1; i < prices.count; i++) {
        NSInteger price = prices[i].integerValue;
        if (price < minPrice) {
            minPrice = price;
        } else {
            maxProfit = MAX(maxProfit, price - minPrice);
        }
    }

    return maxProfit;
}
```

常见错误点：

- 忘了卖出必须在买入之后。
- 把“全局最大值减全局最小值”当答案，这是错的，因为顺序可能不合法。
- 没处理空数组。

---

## 2.4 验证回文串

为什么会想到这个解法：

- 回文问题天然适合左右双指针。
- 题目明确说要忽略非字母数字字符，所以指针移动时需要先跳过无效字符。
- 再配合统一大小写比较即可。

Objective-C 参考实现：

```objc
- (BOOL)isAlphaNumeric:(unichar)c {
    return ([[NSCharacterSet alphanumericCharacterSet] characterIsMember:c]);
}

- (BOOL)isPalindrome:(NSString *)s {
    NSInteger left = 0;
    NSInteger right = s.length - 1;

    while (left < right) {
        unichar lc = [s characterAtIndex:left];
        unichar rc = [s characterAtIndex:right];

        if (![self isAlphaNumeric:lc]) {
            left++;
            continue;
        }
        if (![self isAlphaNumeric:rc]) {
            right--;
            continue;
        }

        NSString *ls = [[NSString stringWithCharacters:&lc length:1] lowercaseString];
        NSString *rs = [[NSString stringWithCharacters:&rc length:1] lowercaseString];
        if (![ls isEqualToString:rs]) {
            return NO;
        }

        left++;
        right--;
    }

    return YES;
}
```

常见错误点：

- 没有跳过空格、逗号等无效字符。
- 忘了忽略大小写。
- `right` 初始化和边界移动写错。

---

## 2.5 最长公共前缀

为什么会想到这个解法：

- 所有字符串的公共前缀，一定不会比第一个字符串更长。
- 所以可以先把第一个字符串当候选答案，再依次和后面的字符串比较。
- 一旦不匹配，就缩短前缀，直到匹配或为空。

Objective-C 参考实现：

```objc
- (NSString *)longestCommonPrefix:(NSArray<NSString *> *)strs {
    if (strs.count == 0) {
        return @"";
    }

    NSString *prefix = strs[0];
    for (NSInteger i = 1; i < strs.count; i++) {
        NSString *cur = strs[i];
        while (![cur hasPrefix:prefix]) {
            if (prefix.length == 0) {
                return @"";
            }
            prefix = [prefix substringToIndex:prefix.length - 1];
        }
    }
    return prefix;
}
```

常见错误点：

- 忽略空数组输入。
- 当前缀已经缩短到空字符串时还继续截取。
- 只比较前两个字符串，没有继续和后面的字符串收缩前缀。

---

## 2.6 反转字符串

为什么会想到这个解法：

- 题目要求原地修改，最自然就是左右交换。
- 每次交换最左和最右元素，然后双指针向中间收缩。

Objective-C 参考实现：

```objc
- (void)reverseString:(NSMutableArray<NSString *> *)s {
    NSInteger left = 0;
    NSInteger right = s.count - 1;
    while (left < right) {
        NSString *tmp = s[left];
        s[left] = s[right];
        s[right] = tmp;
        left++;
        right--;
    }
}
```

常见错误点：

- 额外创建新数组，偏离题目要求。
- `left <= right` 导致中间元素被重复交换。
- 没有真正原地交换。

---

## 2.7 第一个唯一字符

为什么会想到这个解法：

- “第一个只出现一次”这类题，先统计频次再找位置最稳。
- 第一次遍历做计数，第二次遍历按原顺序找到第一个频次为 1 的字符即可。

Objective-C 参考实现：

```objc
- (NSInteger)firstUniqChar:(NSString *)s {
    NSMutableDictionary<NSString *, NSNumber *> *count = [NSMutableDictionary dictionary];

    for (NSInteger i = 0; i < s.length; i++) {
        unichar c = [s characterAtIndex:i];
        NSString *key = [NSString stringWithCharacters:&c length:1];
        count[key] = @([count[key] integerValue] + 1);
    }

    for (NSInteger i = 0; i < s.length; i++) {
        unichar c = [s characterAtIndex:i];
        NSString *key = [NSString stringWithCharacters:&c length:1];
        if ([count[key] integerValue] == 1) {
            return i;
        }
    }

    return -1;
}
```

常见错误点：

- 只统计了频次，却忘了题目要的是“第一个”的下标。
- 统计时字符转字符串的过程写乱。
- 没有处理不存在唯一字符时返回 `-1`。

---

## 3.3 两数之和

为什么会想到这个解法：

- 暴力做法是双重循环，时间复杂度 O(n^2)。
- 题目本质是在问：对于当前元素，另一个“补数”有没有出现过。
- 这种“快速判断某值是否存在”的需求，天然适合哈希表。

Objective-C 参考实现：

```objc
- (NSArray<NSNumber *> *)twoSum:(NSArray<NSNumber *> *)nums target:(NSInteger)target {
    NSMutableDictionary<NSNumber *, NSNumber *> *map = [NSMutableDictionary dictionary];

    for (NSInteger i = 0; i < nums.count; i++) {
        NSInteger value = nums[i].integerValue;
        NSNumber *need = @(target - value);
        if (map[need] != nil) {
            return @[map[need], @(i)];
        }
        map[@(value)] = @(i);
    }

    return @[];
}
```

常见错误点：

- 先把当前值放进哈希表，再查补数，可能误用同一个元素。
- 返回的是值，不是下标。
- 没理解题目默认只有一个答案，所以逻辑写得过于复杂。

---

## 3.4 有效的字母异位词

为什么会想到这个解法：

- 异位词的本质不是顺序，而是“字符种类和频次完全一样”。
- 所以最直接的办法就是做字符计数，然后比较统计结果。

Objective-C 参考实现：

```objc
- (BOOL)isAnagram:(NSString *)s t:(NSString *)t {
    if (s.length != t.length) {
        return NO;
    }

    NSMutableDictionary<NSString *, NSNumber *> *count = [NSMutableDictionary dictionary];
    for (NSInteger i = 0; i < s.length; i++) {
        unichar c = [s characterAtIndex:i];
        NSString *key = [NSString stringWithCharacters:&c length:1];
        count[key] = @([count[key] integerValue] + 1);
    }

    for (NSInteger i = 0; i < t.length; i++) {
        unichar c = [t characterAtIndex:i];
        NSString *key = [NSString stringWithCharacters:&c length:1];
        NSInteger remain = [count[key] integerValue] - 1;
        if (remain < 0) {
            return NO;
        }
        count[key] = @(remain);
    }

    return YES;
}
```

常见错误点：

- 只比较字符集合，没有比较频次。
- 长度不等时没有提前返回。
- 计数减到负数时没有立即判错。

---

## 3.5 存在重复元素

为什么会想到这个解法：

- 题目只关心“有没有重复”，不关心具体重复次数。
- 所以集合就够用了，只要看到某个元素已经出现过，就能立刻返回。

Objective-C 参考实现：

```objc
- (BOOL)containsDuplicate:(NSArray<NSNumber *> *)nums {
    NSMutableSet<NSNumber *> *set = [NSMutableSet set];
    for (NSNumber *num in nums) {
        if ([set containsObject:num]) {
            return YES;
        }
        [set addObject:num];
    }
    return NO;
}
```

常见错误点：

- 用字典或排序也能做，但没必要把简单题写复杂。
- 忘了集合判重可以提前返回。

---

## 3.6 只出现一次的数字

为什么会想到这个解法：

- 如果先求稳，可以直接哈希计数。
- 但这题有一个非常经典的性质：相同数字异或两次会抵消，最终只剩那个单独出现的数。
- 所以异或能把空间复杂度优化到 O(1)。

Objective-C 参考实现：

```objc
- (NSInteger)singleNumber:(NSArray<NSNumber *> *)nums {
    NSInteger result = 0;
    for (NSNumber *num in nums) {
        result ^= num.integerValue;
    }
    return result;
}
```

常见错误点：

- 只会哈希解法，却说不出更优的异或思路。
- 没理解为什么异或能抵消成对元素。
- 把“只出现一次”误读成“只出现奇数次”。

---

## 4.4 删除有序数组中的重复项

为什么会想到这个解法：

- 数组本身有序，重复元素一定相邻。
- 所以不需要额外集合，只需要用慢指针维护“去重后结果的尾部”。

Objective-C 参考实现：

```objc
- (NSInteger)removeDuplicates:(NSMutableArray<NSNumber *> *)nums {
    if (nums.count == 0) {
        return 0;
    }

    NSInteger slow = 1;
    for (NSInteger fast = 1; fast < nums.count; fast++) {
        if (![nums[fast] isEqualToNumber:nums[fast - 1]]) {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    return slow;
}
```

常见错误点：

- 忘了数组有序这个关键信息。
- 从 `slow = 0` 开始导致边界混乱。
- 没处理空数组。

---

## 4.5 移动零

为什么会想到这个解法：

- 题目要求保持非零元素相对顺序，所以不能随便交换。
- 最稳的做法是先把所有非零元素往前覆盖，再把后面补零。

Objective-C 参考实现：

```objc
- (void)moveZeroes:(NSMutableArray<NSNumber *> *)nums {
    NSInteger slow = 0;
    for (NSInteger fast = 0; fast < nums.count; fast++) {
        if (nums[fast].integerValue != 0) {
            nums[slow] = nums[fast];
            slow++;
        }
    }

    while (slow < nums.count) {
        nums[slow] = @0;
        slow++;
    }
}
```

常见错误点：

- 直接交换导致相对顺序被破坏。
- 只把非零元素往前挪了，却忘了后面补零。

---

## 4.6 盛最多水的容器

为什么会想到这个解法：

- 暴力枚举两条边是 O(n^2)。
- 容器面积由短板决定，所以每次应该尝试移动更短的一边，才有可能获得更大面积。

Objective-C 参考实现：

```objc
- (NSInteger)maxArea:(NSArray<NSNumber *> *)height {
    NSInteger left = 0;
    NSInteger right = height.count - 1;
    NSInteger result = 0;

    while (left < right) {
        NSInteger h = MIN(height[left].integerValue, height[right].integerValue);
        NSInteger w = right - left;
        result = MAX(result, h * w);

        if (height[left].integerValue < height[right].integerValue) {
            left++;
        } else {
            right--;
        }
    }

    return result;
}
```

常见错误点：

- 不理解为什么总是移动较短边。
- 把高度取成较高边，导致面积计算错误。

---

## 4.7 长度最小的子数组

为什么会想到这个解法：

- 题目要求连续子数组，且数组元素为正数，这正是滑动窗口的典型信号。
- 因为全是正数，窗口右移时和只会增大，左移时和只会减小，窗口行为是单调的。

Objective-C 参考实现：

```objc
- (NSInteger)minSubArrayLen:(NSInteger)target nums:(NSArray<NSNumber *> *)nums {
    NSInteger left = 0;
    NSInteger sum = 0;
    NSInteger result = NSIntegerMax;

    for (NSInteger right = 0; right < nums.count; right++) {
        sum += nums[right].integerValue;
        while (sum >= target) {
            result = MIN(result, right - left + 1);
            sum -= nums[left].integerValue;
            left++;
        }
    }

    return result == NSIntegerMax ? 0 : result;
}
```

常见错误点：

- 没注意数组元素都是正数，错过滑动窗口。
- 窗口满足条件后没有继续收缩，导致不是最短长度。

---

## 5.5 反转链表

为什么会想到这个解法：

- 每次只需要让当前节点的 `next` 指向前一个节点。
- 这是一个非常标准的三指针过程：`prev`、`cur`、`next`。

Objective-C 参考实现：

```objc
- (ListNode *)reverseList:(ListNode *)head {
    ListNode *prev = nil;
    ListNode *cur = head;
    while (cur) {
        ListNode *next = cur.next;
        cur.next = prev;
        prev = cur;
        cur = next;
    }
    return prev;
}
```

常见错误点：

- 没先保存 `next` 就改指针，导致链表断掉。
- 返回了旧头节点而不是新头节点 `prev`。

---

## 5.6 合并两个有序链表

为什么会想到这个解法：

- 两条链表都已经有序，所以和合并有序数组的思想类似。
- 为了简化头节点处理，最稳的是用虚拟头节点。

Objective-C 参考实现：

```objc
- (ListNode *)mergeTwoLists:(ListNode *)list1 list2:(ListNode *)list2 {
    ListNode *dummy = [[ListNode alloc] initWithVal:0 next:nil];
    ListNode *tail = dummy;

    while (list1 && list2) {
        if (list1.val <= list2.val) {
            tail.next = list1;
            list1 = list1.next;
        } else {
            tail.next = list2;
            list2 = list2.next;
        }
        tail = tail.next;
    }

    tail.next = list1 ? list1 : list2;
    return dummy.next;
}
```

常见错误点：

- 不用虚拟头节点，导致头节点分支很多。
- 最后忘了接上剩余未处理链表。

---

## 5.7 删除链表的倒数第 N 个结点

为什么会想到这个解法：

- “倒数第 N 个”天然适合快慢指针。
- 先让快指针走 `n` 步，再同步移动，直到快指针到尾部，此时慢指针正好停在待删除节点前一个位置。

Objective-C 参考实现：

```objc
- (ListNode *)removeNthFromEnd:(ListNode *)head n:(NSInteger)n {
    ListNode *dummy = [[ListNode alloc] initWithVal:0 next:head];
    ListNode *fast = dummy;
    ListNode *slow = dummy;

    for (NSInteger i = 0; i < n; i++) {
        fast = fast.next;
    }

    while (fast.next) {
        fast = fast.next;
        slow = slow.next;
    }

    slow.next = slow.next.next;
    return dummy.next;
}
```

常见错误点：

- 不加虚拟头节点时，删除头节点会很麻烦。
- 快指针步数差写错，导致删错节点。

---

## 5.8 环形链表

为什么会想到这个解法：

- 如果链表有环，快慢指针最终一定会相遇。
- 如果链表无环，快指针会先到 `nil`。

Objective-C 参考实现：

```objc
- (BOOL)hasCycle:(ListNode *)head {
    if (!head || !head.next) {
        return NO;
    }

    ListNode *slow = head;
    ListNode *fast = head;
    while (fast && fast.next) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            return YES;
        }
    }
    return NO;
}
```

常见错误点：

- 没判空就直接访问 `fast.next.next`。
- 把节点值相等当成相遇，实际上应该比较节点指针本身。

---

## 6.4 有效的括号

为什么会想到这个解法：

- 括号配对天然适合栈。
- 遇到左括号入栈，遇到右括号时检查栈顶是否匹配。

Objective-C 参考实现：

```objc
- (BOOL)isValid:(NSString *)s {
    NSMutableArray<NSString *> *stack = [NSMutableArray array];
    NSDictionary<NSString *, NSString *> *pairs = @{
        @")": @"(",
        @"]": @"[",
        @"}": @"{"
    };

    for (NSInteger i = 0; i < s.length; i++) {
        unichar c = [s characterAtIndex:i];
        NSString *ch = [NSString stringWithCharacters:&c length:1];
        if ([ch isEqualToString:@"("] || [ch isEqualToString:@"["] || [ch isEqualToString:@"{"]) {
            [stack addObject:ch];
        } else {
            if (stack.count == 0 || ![[stack lastObject] isEqualToString:pairs[ch]]) {
                return NO;
            }
            [stack removeLastObject];
        }
    }

    return stack.count == 0;
}
```

常见错误点：

- 右括号出现时没有先判断栈是否为空。
- 最后忘了检查栈是否清空。

---

## 6.5 用栈实现队列

为什么会想到这个解法：

- 一个栈只能后进先出，但两个栈可以通过倒腾元素模拟先进先出。
- `inStack` 负责入队，`outStack` 负责出队；只有在 `outStack` 空时才搬运。

Objective-C 参考实现：

```objc
@interface MyQueue : NSObject
@property (nonatomic, strong) NSMutableArray<NSNumber *> *inStack;
@property (nonatomic, strong) NSMutableArray<NSNumber *> *outStack;
@end

@implementation MyQueue

- (instancetype)init {
    self = [super init];
    if (self) {
        _inStack = [NSMutableArray array];
        _outStack = [NSMutableArray array];
    }
    return self;
}

- (void)moveIfNeeded {
    if (self.outStack.count == 0) {
        while (self.inStack.count > 0) {
            [self.outStack addObject:self.inStack.lastObject];
            [self.inStack removeLastObject];
        }
    }
}

- (void)push:(NSInteger)x {
    [self.inStack addObject:@(x)];
}

- (NSInteger)pop {
    [self moveIfNeeded];
    NSInteger value = self.outStack.lastObject.integerValue;
    [self.outStack removeLastObject];
    return value;
}

- (NSInteger)peek {
    [self moveIfNeeded];
    return self.outStack.lastObject.integerValue;
}

- (BOOL)empty {
    return self.inStack.count == 0 && self.outStack.count == 0;
}

@end
```

常见错误点：

- 每次操作都搬运元素，虽然能做但不够优雅。
- `peek` 和 `pop` 前忘了先把元素转移到 `outStack`。

---

## 6.6 最小栈

为什么会想到这个解法：

- 普通栈只能拿到栈顶，拿不到当前最小值。
- 所以需要一个辅助栈，和主栈同步维护到当前位置为止的最小值。

Objective-C 参考实现：

```objc
@interface MinStack : NSObject
@property (nonatomic, strong) NSMutableArray<NSNumber *> *stack;
@property (nonatomic, strong) NSMutableArray<NSNumber *> *minStack;
@end

@implementation MinStack

- (instancetype)init {
    self = [super init];
    if (self) {
        _stack = [NSMutableArray array];
        _minStack = [NSMutableArray array];
    }
    return self;
}

- (void)push:(NSInteger)val {
    [self.stack addObject:@(val)];
    NSInteger currentMin = self.minStack.count == 0 ? val : MIN(val, self.minStack.lastObject.integerValue);
    [self.minStack addObject:@(currentMin)];
}

- (void)pop {
    [self.stack removeLastObject];
    [self.minStack removeLastObject];
}

- (NSInteger)top {
    return self.stack.lastObject.integerValue;
}

- (NSInteger)getMin {
    return self.minStack.lastObject.integerValue;
}

@end
```

常见错误点：

- 只在更小元素入栈时才更新最小栈，导致弹栈后最小值出错。
- `pop` 时忘记同步弹出辅助栈。

---

## 6.7 每日温度

为什么会想到这个解法：

- 题目是在找“右边第一个更大元素”，这就是单调栈经典场景。
- 栈里存下标，保持对应温度单调递减；一旦遇到更高温度，就把之前等待的人一次性结算。

Objective-C 参考实现：

```objc
- (NSArray<NSNumber *> *)dailyTemperatures:(NSArray<NSNumber *> *)temperatures {
    NSMutableArray<NSNumber *> *result = [NSMutableArray array];
    for (NSInteger i = 0; i < temperatures.count; i++) {
        [result addObject:@0];
    }

    NSMutableArray<NSNumber *> *stack = [NSMutableArray array];
    for (NSInteger i = 0; i < temperatures.count; i++) {
        while (stack.count > 0 &&
               temperatures[i].integerValue > temperatures[stack.lastObject.integerValue].integerValue) {
            NSInteger idx = stack.lastObject.integerValue;
            [stack removeLastObject];
            result[idx] = @(i - idx);
        }
        [stack addObject:@(i)];
    }

    return result;
}
```

常见错误点：

- 栈里存温度而不是下标，后面没法算天数差。
- 单调栈方向写反。

---

## 7.5 二叉树的最大深度

为什么会想到这个解法：

- 题目要求从根到叶的最大层数，这天然适合递归定义。
- 一棵树的最大深度就是 `max(左子树深度, 右子树深度) + 1`。

Objective-C 参考实现：

```objc
- (NSInteger)maxDepth:(TreeNode *)root {
    if (!root) {
        return 0;
    }
    return MAX([self maxDepth:root.left], [self maxDepth:root.right]) + 1;
}
```

常见错误点：

- 把节点数和边数混淆。
- 忘了空树深度应该是 `0`。

---

## 7.6 对称二叉树

为什么会想到这个解法：

- 对称不是比较一棵子树自己，而是同时比较左右两棵子树是否互为镜像。
- 所以需要一个辅助函数，传入两棵树来比较。

Objective-C 参考实现：

```objc
- (BOOL)isMirror:(TreeNode *)left right:(TreeNode *)right {
    if (!left && !right) {
        return YES;
    }
    if (!left || !right || left.val != right.val) {
        return NO;
    }
    return [self isMirror:left.left right:right.right] &&
           [self isMirror:left.right right:right.left];
}

- (BOOL)isSymmetric:(TreeNode *)root {
    if (!root) {
        return YES;
    }
    return [self isMirror:root.left right:root.right];
}
```

常见错误点：

- 只比较了左右子树根节点值，没有继续递归比较镜像结构。
- 把 `left.left` 错写成和 `right.left` 比较。

---

## 7.7 二叉树的层序遍历

为什么会想到这个解法：

- “逐层返回”就是 BFS 的典型关键词。
- 用队列按层推进，每次先记录当前层节点数，再处理这一层。

Objective-C 参考实现：

```objc
- (NSArray<NSArray<NSNumber *> *> *)levelOrder:(TreeNode *)root {
    if (!root) {
        return @[];
    }

    NSMutableArray<NSArray<NSNumber *> *> *result = [NSMutableArray array];
    NSMutableArray<TreeNode *> *queue = [NSMutableArray arrayWithObject:root];

    while (queue.count > 0) {
        NSInteger size = queue.count;
        NSMutableArray<NSNumber *> *level = [NSMutableArray array];
        for (NSInteger i = 0; i < size; i++) {
            TreeNode *node = queue.firstObject;
            [queue removeObjectAtIndex:0];
            [level addObject:@(node.val)];
            if (node.left) [queue addObject:node.left];
            if (node.right) [queue addObject:node.right];
        }
        [result addObject:[level copy]];
    }

    return result;
}
```

常见错误点：

- 没按层记录 `size`，导致结果不是逐层分组。
- 直接把 `NSMutableArray` 当高性能队列使用时忽略了头删开销，不过面试里通常可接受。

---

## 7.8 翻转二叉树

为什么会想到这个解法：

- 题目本质就是对每个节点交换左右孩子。
- 递归写法最直接，先交换当前节点，再递归处理子树。

Objective-C 参考实现：

```objc
- (TreeNode *)invertTree:(TreeNode *)root {
    if (!root) {
        return nil;
    }
    TreeNode *tmp = root.left;
    root.left = root.right;
    root.right = tmp;
    [self invertTree:root.left];
    [self invertTree:root.right];
    return root;
}
```

常见错误点：

- 交换后又按旧引用递归，逻辑搞乱。
- 漏掉空节点判断。

---

## 8.4 验证二叉搜索树

为什么会想到这个解法：

- BST 最经典的性质就是中序遍历严格递增。
- 所以可以用中序遍历，判断当前值是否始终大于前一个值。

Objective-C 参考实现：

```objc
- (BOOL)inorder:(TreeNode *)root prev:(NSNumber * __strong *)prev {
    if (!root) {
        return YES;
    }
    if (![self inorder:root.left prev:prev]) {
        return NO;
    }
    if (*prev && root.val <= (*prev).integerValue) {
        return NO;
    }
    *prev = @(root.val);
    return [self inorder:root.right prev:prev];
}

- (BOOL)isValidBST:(TreeNode *)root {
    NSNumber *prev = nil;
    return [self inorder:root prev:&prev];
}
```

常见错误点：

- 只比较父子节点大小，而不是整个子树范围。
- 用 `>=` / `<=` 条件写错，导致重复值判断失误。

---

## 8.5 二叉搜索树中的搜索

为什么会想到这个解法：

- BST 左小右大，所以每一步都可以剪枝，不必遍历整棵树。

Objective-C 参考实现：

```objc
- (TreeNode *)searchBST:(TreeNode *)root val:(NSInteger)val {
    if (!root || root.val == val) {
        return root;
    }
    if (val < root.val) {
        return [self searchBST:root.left val:val];
    }
    return [self searchBST:root.right val:val];
}
```

常见错误点：

- 没利用 BST 性质，退化成整树遍历。

---

## 8.6 路径总和

为什么会想到这个解法：

- 从根到叶路径和，很适合每走一层就把目标值减掉当前节点值。
- 到叶子节点时判断剩余值是否刚好为 0。

Objective-C 参考实现：

```objc
- (BOOL)hasPathSum:(TreeNode *)root targetSum:(NSInteger)targetSum {
    if (!root) {
        return NO;
    }
    if (!root.left && !root.right) {
        return targetSum == root.val;
    }
    NSInteger remain = targetSum - root.val;
    return [self hasPathSum:root.left targetSum:remain] ||
           [self hasPathSum:root.right targetSum:remain];
}
```

常见错误点：

- 还没到叶子节点就提前返回。
- 忘了题目要求必须是“从根到叶”。

---

## 8.7 二叉树的最近公共祖先

为什么会想到这个解法：

- 如果 `p` 和 `q` 分别在左右子树，那么当前节点就是答案。
- 如果它们都在同一边，就把那一边递归结果往上返回。

Objective-C 参考实现：

```objc
- (TreeNode *)lowestCommonAncestor:(TreeNode *)root p:(TreeNode *)p q:(TreeNode *)q {
    if (!root || root == p || root == q) {
        return root;
    }

    TreeNode *left = [self lowestCommonAncestor:root.left p:p q:q];
    TreeNode *right = [self lowestCommonAncestor:root.right p:p q:q];

    if (left && right) {
        return root;
    }
    return left ? left : right;
}
```

常见错误点：

- 把“最近公共祖先”理解成值最小或层级最低的公共节点。
- 找到一个目标节点后没正确向上返回。

---

## 9.4 子集

为什么会想到这个解法：

- 子集问题就是典型回溯，每个元素都面临“选或不选”。
- 每到一个节点，当前路径本身就是一个合法答案。

Objective-C 参考实现：

```objc
- (void)dfsSubsets:(NSArray<NSNumber *> *)nums
             index:(NSInteger)index
              path:(NSMutableArray<NSNumber *> *)path
            result:(NSMutableArray<NSArray<NSNumber *> *> *)result {
    [result addObject:[path copy]];
    for (NSInteger i = index; i < nums.count; i++) {
        [path addObject:nums[i]];
        [self dfsSubsets:nums index:i + 1 path:path result:result];
        [path removeLastObject];
    }
}

- (NSArray<NSArray<NSNumber *> *> *)subsets:(NSArray<NSNumber *> *)nums {
    NSMutableArray *result = [NSMutableArray array];
    [self dfsSubsets:nums index:0 path:[NSMutableArray array] result:result];
    return result;
}
```

常见错误点：

- 忘了每一层都要先收集当前路径。
- 回溯结束后忘记 `removeLastObject`。

---

## 9.5 全排列

为什么会想到这个解法：

- 全排列要求每个位置都尝试所有还没用过的数。
- 所以需要一个 `used` 数组或集合来记录哪些数已经放进路径。

Objective-C 参考实现：

```objc
- (void)dfsPermute:(NSArray<NSNumber *> *)nums
              path:(NSMutableArray<NSNumber *> *)path
              used:(NSMutableArray<NSNumber *> *)used
            result:(NSMutableArray<NSArray<NSNumber *> *> *)result {
    if (path.count == nums.count) {
        [result addObject:[path copy]];
        return;
    }
    for (NSInteger i = 0; i < nums.count; i++) {
        if (used[i].boolValue) {
            continue;
        }
        used[i] = @YES;
        [path addObject:nums[i]];
        [self dfsPermute:nums path:path used:used result:result];
        [path removeLastObject];
        used[i] = @NO;
    }
}

- (NSArray<NSArray<NSNumber *> *> *)permute:(NSArray<NSNumber *> *)nums {
    NSMutableArray *used = [NSMutableArray array];
    for (NSInteger i = 0; i < nums.count; i++) {
        [used addObject:@NO];
    }
    NSMutableArray *result = [NSMutableArray array];
    [self dfsPermute:nums path:[NSMutableArray array] used:used result:result];
    return result;
}
```

常见错误点：

- 把“排列”和“组合”混了，错误使用起始下标。
- `used` 标记没恢复。

---

## 9.6 组合总和

为什么会想到这个解法：

- 既要列出所有组合，又允许重复使用元素，明显是回溯。
- 因为数组可以重复选当前元素，所以递归时下一层仍然可以从当前下标开始。

Objective-C 参考实现：

```objc
- (void)dfsCombination:(NSArray<NSNumber *> *)candidates
                 index:(NSInteger)index
                target:(NSInteger)target
                  path:(NSMutableArray<NSNumber *> *)path
                result:(NSMutableArray<NSArray<NSNumber *> *> *)result {
    if (target == 0) {
        [result addObject:[path copy]];
        return;
    }
    if (target < 0) {
        return;
    }
    for (NSInteger i = index; i < candidates.count; i++) {
        [path addObject:candidates[i]];
        [self dfsCombination:candidates index:i target:target - candidates[i].integerValue path:path result:result];
        [path removeLastObject];
    }
}

- (NSArray<NSArray<NSNumber *> *> *)combinationSum:(NSArray<NSNumber *> *)candidates target:(NSInteger)target {
    NSMutableArray *result = [NSMutableArray array];
    [self dfsCombination:candidates index:0 target:target path:[NSMutableArray array] result:result];
    return result;
}
```

常见错误点：

- 误把下层起点写成 `i + 1`，导致不能重复使用元素。
- 目标值小于 0 时没及时剪枝。

---

## 9.7 括号生成

为什么会想到这个解法：

- 这题不是随便拼括号，而是带约束的状态搜索。
- 任意时刻左括号数不能少于右括号数，且两者都不能超过 `n`。

Objective-C 参考实现：

```objc
- (void)dfsParenthesis:(NSInteger)n
                  left:(NSInteger)left
                 right:(NSInteger)right
                  path:(NSMutableString *)path
                result:(NSMutableArray<NSString *> *)result {
    if (path.length == n * 2) {
        [result addObject:[path copy]];
        return;
    }
    if (left < n) {
        [path appendString:@"("];
        [self dfsParenthesis:n left:left + 1 right:right path:path result:result];
        [path deleteCharactersInRange:NSMakeRange(path.length - 1, 1)];
    }
    if (right < left) {
        [path appendString:@")"];
        [self dfsParenthesis:n left:left right:right + 1 path:path result:result];
        [path deleteCharactersInRange:NSMakeRange(path.length - 1, 1)];
    }
}

- (NSArray<NSString *> *)generateParenthesis:(NSInteger)n {
    NSMutableArray *result = [NSMutableArray array];
    [self dfsParenthesis:n left:0 right:0 path:[NSMutableString string] result:result];
    return result;
}
```

常见错误点：

- 没约束 `right < left`，会生成非法括号。
- 只判断总长度，不判断中间状态是否合法。

---

## 10.4 二分查找

为什么会想到这个解法：

- 标准有序数组查找，直接套二分模板。

Objective-C 参考实现：

```objc
- (NSInteger)search:(NSArray<NSNumber *> *)nums target:(NSInteger)target {
    NSInteger left = 0;
    NSInteger right = nums.count - 1;
    while (left <= right) {
        NSInteger mid = left + (right - left) / 2;
        NSInteger value = nums[mid].integerValue;
        if (value == target) {
            return mid;
        } else if (value < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}
```

常见错误点：

- `left` 和 `right` 更新反了。
- `while (left < right)` 和 `<=` 混用导致边界漏查。

---

## 10.5 搜索插入位置

为什么会想到这个解法：

- 还是标准二分，只是找不到时返回 `left`，因为循环结束时 `left` 就是插入位。

Objective-C 参考实现：

```objc
- (NSInteger)searchInsert:(NSArray<NSNumber *> *)nums target:(NSInteger)target {
    NSInteger left = 0;
    NSInteger right = nums.count - 1;
    while (left <= right) {
        NSInteger mid = left + (right - left) / 2;
        NSInteger value = nums[mid].integerValue;
        if (value == target) {
            return mid;
        } else if (value < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return left;
}
```

常见错误点：

- 找不到时错误返回 `right`。

---

## 10.6 在排序数组中查找元素的第一个和最后一个位置

为什么会想到这个解法：

- 本质是两次边界二分：一次找左边界，一次找右边界。

Objective-C 参考实现：

```objc
- (NSInteger)lowerBound:(NSArray<NSNumber *> *)nums target:(NSInteger)target {
    NSInteger left = 0;
    NSInteger right = nums.count;
    while (left < right) {
        NSInteger mid = left + (right - left) / 2;
        if (nums[mid].integerValue < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return left;
}

- (NSArray<NSNumber *> *)searchRange:(NSArray<NSNumber *> *)nums target:(NSInteger)target {
    NSInteger left = [self lowerBound:nums target:target];
    NSInteger right = [self lowerBound:nums target:target + 1] - 1;
    if (left == nums.count || nums[left].integerValue != target) {
        return @[@-1, @-1];
    }
    return @[@(left), @(right)];
}
```

常见错误点：

- 左右边界逻辑混在一起，最后不稳定。
- 找不到时没有返回 `[-1, -1]`。

---

## 10.7 搜索旋转排序数组

为什么会想到这个解法：

- 虽然数组被旋转，但每次二分时总有一半仍然有序。
- 判断哪一半有序，再看目标值是否落在那一半里即可。

Objective-C 参考实现：

```objc
- (NSInteger)searchInRotated:(NSArray<NSNumber *> *)nums target:(NSInteger)target {
    NSInteger left = 0;
    NSInteger right = nums.count - 1;
    while (left <= right) {
        NSInteger mid = left + (right - left) / 2;
        NSInteger value = nums[mid].integerValue;
        if (value == target) {
            return mid;
        }
        if (nums[left].integerValue <= value) {
            if (nums[left].integerValue <= target && target < value) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {
            if (value < target && target <= nums[right].integerValue) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    return -1;
}
```

常见错误点：

- 判断哪一半有序时条件写错。
- 忘了目标值区间判断也要跟着变化。

---

## 11.5 爬楼梯

为什么会想到这个解法：

- 到第 `n` 阶的方法数只和前两阶有关，是最基础的线性 DP。

Objective-C 参考实现：

```objc
- (NSInteger)climbStairs:(NSInteger)n {
    if (n <= 2) {
        return n;
    }
    NSInteger a = 1;
    NSInteger b = 2;
    for (NSInteger i = 3; i <= n; i++) {
        NSInteger c = a + b;
        a = b;
        b = c;
    }
    return b;
}
```

常见错误点：

- 初始值写错。
- 没处理 `n = 1` 或 `n = 2`。

---

## 11.6 打家劫舍

为什么会想到这个解法：

- 每间房只有“偷”或“不偷”两种选择。
- 到第 `i` 间房的最优结果，取决于前一间和前两间的最优解。

Objective-C 参考实现：

```objc
- (NSInteger)rob:(NSArray<NSNumber *> *)nums {
    NSInteger prev2 = 0;
    NSInteger prev1 = 0;
    for (NSNumber *num in nums) {
        NSInteger current = MAX(prev1, prev2 + num.integerValue);
        prev2 = prev1;
        prev1 = current;
    }
    return prev1;
}
```

常见错误点：

- 忘了相邻房子不能同时选。

---

## 11.7 不同路径

为什么会想到这个解法：

- 每个格子的路径数只来自上方和左方，是标准二维 DP。

Objective-C 参考实现：

```objc
- (NSInteger)uniquePaths:(NSInteger)m n:(NSInteger)n {
    NSMutableArray<NSMutableArray<NSNumber *> *> *dp = [NSMutableArray array];
    for (NSInteger i = 0; i < m; i++) {
        NSMutableArray<NSNumber *> *row = [NSMutableArray array];
        for (NSInteger j = 0; j < n; j++) {
            [row addObject:@1];
        }
        [dp addObject:row];
    }
    for (NSInteger i = 1; i < m; i++) {
        for (NSInteger j = 1; j < n; j++) {
            dp[i][j] = @([dp[i - 1][j] integerValue] + [dp[i][j - 1] integerValue]);
        }
    }
    return dp[m - 1][n - 1].integerValue;
}
```

常见错误点：

- 第一行和第一列初始化漏掉。

---

## 11.8 最小路径和

为什么会想到这个解法：

- 仍然是二维网格 DP，只不过从“路径数”变成“最小代价”。

Objective-C 参考实现：

```objc
- (NSInteger)minPathSum:(NSArray<NSArray<NSNumber *> *> *)grid {
    NSInteger m = grid.count;
    NSInteger n = [grid[0] count];
    NSMutableArray<NSMutableArray<NSNumber *> *> *dp = [NSMutableArray array];
    for (NSInteger i = 0; i < m; i++) {
        NSMutableArray<NSNumber *> *row = [NSMutableArray array];
        for (NSInteger j = 0; j < n; j++) {
            [row addObject:@0];
        }
        [dp addObject:row];
    }

    dp[0][0] = grid[0][0];
    for (NSInteger i = 1; i < m; i++) {
        dp[i][0] = @([dp[i - 1][0] integerValue] + [grid[i][0] integerValue]);
    }
    for (NSInteger j = 1; j < n; j++) {
        dp[0][j] = @([dp[0][j - 1] integerValue] + [grid[0][j] integerValue]);
    }
    for (NSInteger i = 1; i < m; i++) {
        for (NSInteger j = 1; j < n; j++) {
            NSInteger best = MIN([dp[i - 1][j] integerValue], [dp[i][j - 1] integerValue]);
            dp[i][j] = @(best + [grid[i][j] integerValue]);
        }
    }
    return dp[m - 1][n - 1].integerValue;
}
```

常见错误点：

- 第一行第一列初始化不完整。
- 状态转移把 `min` 写成 `max`。

---

## 12.3 最长递增子序列

为什么会想到这个解法：

- `dp[i]` 可以定义为“以 `i` 结尾的最长递增子序列长度”。
- 枚举前面所有比它小的数作为前驱。

Objective-C 参考实现：

```objc
- (NSInteger)lengthOfLIS:(NSArray<NSNumber *> *)nums {
    if (nums.count == 0) {
        return 0;
    }
    NSMutableArray<NSNumber *> *dp = [NSMutableArray array];
    for (NSInteger i = 0; i < nums.count; i++) {
        [dp addObject:@1];
    }
    NSInteger result = 1;
    for (NSInteger i = 1; i < nums.count; i++) {
        for (NSInteger j = 0; j < i; j++) {
            if (nums[j].integerValue < nums[i].integerValue) {
                dp[i] = @(MAX(dp[i].integerValue, dp[j].integerValue + 1));
            }
        }
        result = MAX(result, dp[i].integerValue);
    }
    return result;
}
```

常见错误点：

- 把子序列误写成子数组。

---

## 12.4 最长公共子序列

为什么会想到这个解法：

- 两个字符串对齐比较，最自然就是二维 DP。
- `dp[i][j]` 表示前 `i` 个字符和前 `j` 个字符的 LCS 长度。

Objective-C 参考实现：

```objc
- (NSInteger)longestCommonSubsequence:(NSString *)text1 text2:(NSString *)text2 {
    NSInteger m = text1.length;
    NSInteger n = text2.length;
    NSMutableArray *dp = [NSMutableArray array];
    for (NSInteger i = 0; i <= m; i++) {
        NSMutableArray *row = [NSMutableArray array];
        for (NSInteger j = 0; j <= n; j++) {
            [row addObject:@0];
        }
        [dp addObject:row];
    }
    for (NSInteger i = 1; i <= m; i++) {
        for (NSInteger j = 1; j <= n; j++) {
            if ([text1 characterAtIndex:i - 1] == [text2 characterAtIndex:j - 1]) {
                dp[i][j] = @([dp[i - 1][j - 1] integerValue] + 1);
            } else {
                dp[i][j] = @(MAX([dp[i - 1][j] integerValue], [dp[i][j - 1] integerValue]));
            }
        }
    }
    return [dp[m][n] integerValue];
}
```

常见错误点：

- 下标和字符串索引偏移一位。

---

## 12.5 零钱兑换

为什么会想到这个解法：

- 每种硬币可重复使用，是完全背包。
- 目标是最少数量，所以 `dp[i]` 表示凑出金额 `i` 的最少硬币数。

Objective-C 参考实现：

```objc
- (NSInteger)coinChange:(NSArray<NSNumber *> *)coins amount:(NSInteger)amount {
    NSMutableArray<NSNumber *> *dp = [NSMutableArray array];
    for (NSInteger i = 0; i <= amount; i++) {
        [dp addObject:@(amount + 1)];
    }
    dp[0] = @0;
    for (NSInteger i = 1; i <= amount; i++) {
        for (NSNumber *coin in coins) {
            NSInteger c = coin.integerValue;
            if (c <= i) {
                dp[i] = @(MIN(dp[i].integerValue, dp[i - c].integerValue + 1));
            }
        }
    }
    return dp[amount].integerValue > amount ? -1 : dp[amount].integerValue;
}
```

常见错误点：

- 初始值没设成足够大的哨兵值。
- 没法凑出时忘了返回 `-1`。

---

## 12.6 分割等和子集

为什么会想到这个解法：

- 目标是看能否选出一部分数，恰好凑到总和的一半，这是 0-1 背包。

Objective-C 参考实现：

```objc
- (BOOL)canPartition:(NSArray<NSNumber *> *)nums {
    NSInteger sum = 0;
    for (NSNumber *num in nums) {
        sum += num.integerValue;
    }
    if (sum % 2 != 0) {
        return NO;
    }
    NSInteger target = sum / 2;
    NSMutableArray<NSNumber *> *dp = [NSMutableArray array];
    for (NSInteger i = 0; i <= target; i++) {
        [dp addObject:@NO];
    }
    dp[0] = @YES;
    for (NSNumber *num in nums) {
        NSInteger value = num.integerValue;
        for (NSInteger j = target; j >= value; j--) {
            if ([dp[j - value] boolValue]) {
                dp[j] = @YES;
            }
        }
    }
    return [dp[target] boolValue];
}
```

常见错误点：

- 内层循环顺序写正序，导致同一元素被重复使用。

---

## 13.4 岛屿数量

为什么会想到这个解法：

- 每遇到一块新的陆地，就做一次 DFS/BFS，把整块连通区域都淹掉。
- 岛屿数量就是启动搜索的次数。

Objective-C 参考实现：

```objc
- (void)dfsIsland:(NSMutableArray<NSMutableArray<NSString *> *> *)grid i:(NSInteger)i j:(NSInteger)j {
    if (i < 0 || i >= grid.count || j < 0 || j >= grid[0].count || ![grid[i][j] isEqualToString:@"1"]) {
        return;
    }
    grid[i][j] = @"0";
    [self dfsIsland:grid i:i + 1 j:j];
    [self dfsIsland:grid i:i - 1 j:j];
    [self dfsIsland:grid i:i j:j + 1];
    [self dfsIsland:grid i:i j:j - 1];
}

- (NSInteger)numIslands:(NSMutableArray<NSMutableArray<NSString *> *> *)grid {
    NSInteger count = 0;
    for (NSInteger i = 0; i < grid.count; i++) {
        for (NSInteger j = 0; j < grid[i].count; j++) {
            if ([grid[i][j] isEqualToString:@"1"]) {
                count++;
                [self dfsIsland:grid i:i j:j];
            }
        }
    }
    return count;
}
```

常见错误点：

- 搜索后没标记已访问，导致重复计数。

---

## 13.5 腐烂的橘子

为什么会想到这个解法：

- 这是典型多源 BFS，所有腐烂橘子同时作为起点向外扩散。
- BFS 每一层恰好对应 1 分钟。

Objective-C 参考实现：

```objc
- (NSInteger)orangesRotting:(NSArray<NSArray<NSNumber *> *> *)grid {
    NSInteger rows = grid.count;
    NSInteger cols = [grid[0] count];
    NSMutableArray *matrix = [NSMutableArray array];
    NSMutableArray<NSArray<NSNumber *> *> *queue = [NSMutableArray array];
    NSInteger fresh = 0;
    for (NSInteger i = 0; i < rows; i++) {
        [matrix addObject:[grid[i] mutableCopy]];
        for (NSInteger j = 0; j < cols; j++) {
            NSInteger value = [grid[i][j] integerValue];
            if (value == 2) [queue addObject:@[@(i), @(j)]];
            if (value == 1) fresh++;
        }
    }
    NSInteger minutes = 0;
    NSInteger directions[4][2];
    directions[0][0] = 1;  directions[0][1] = 0;
    directions[1][0] = -1; directions[1][1] = 0;
    directions[2][0] = 0;  directions[2][1] = 1;
    directions[3][0] = 0;  directions[3][1] = -1;
    while (queue.count > 0 && fresh > 0) {
        NSInteger size = queue.count;
        for (NSInteger k = 0; k < size; k++) {
            NSArray<NSNumber *> *cell = queue.firstObject;
            [queue removeObjectAtIndex:0];
            NSInteger x = cell[0].integerValue;
            NSInteger y = cell[1].integerValue;
            for (NSInteger d = 0; d < 4; d++) {
                NSInteger nx = x + directions[d][0];
                NSInteger ny = y + directions[d][1];
                if (nx >= 0 && nx < rows && ny >= 0 && ny < cols && [matrix[nx][ny] integerValue] == 1) {
                    matrix[nx][ny] = @2;
                    fresh--;
                    [queue addObject:@[@(nx), @(ny)]];
                }
            }
        }
        minutes++;
    }
    return fresh == 0 ? minutes : -1;
}
```

常见错误点：

- 把分钟数和节点数混在一起。
- 忘了多个腐烂橘子要同时入队。

---

## 13.6 课程表

为什么会想到这个解法：

- 能否完成所有课程，就是看有向图中是否有环。
- 入度 + 队列的拓扑排序是面试里最稳的解法。

Objective-C 参考实现：

```objc
- (BOOL)canFinish:(NSInteger)numCourses prerequisites:(NSArray<NSArray<NSNumber *> *> *)prerequisites {
    NSMutableArray<NSMutableArray<NSNumber *> *> *graph = [NSMutableArray array];
    NSMutableArray<NSNumber *> *indegree = [NSMutableArray array];
    for (NSInteger i = 0; i < numCourses; i++) {
        [graph addObject:[NSMutableArray array]];
        [indegree addObject:@0];
    }
    for (NSArray<NSNumber *> *pair in prerequisites) {
        NSInteger course = pair[0].integerValue;
        NSInteger pre = pair[1].integerValue;
        [graph[pre] addObject:@(course)];
        indegree[course] = @([indegree[course] integerValue] + 1);
    }
    NSMutableArray<NSNumber *> *queue = [NSMutableArray array];
    for (NSInteger i = 0; i < numCourses; i++) {
        if ([indegree[i] integerValue] == 0) {
            [queue addObject:@(i)];
        }
    }
    NSInteger count = 0;
    while (queue.count > 0) {
        NSInteger node = queue.firstObject.integerValue;
        [queue removeObjectAtIndex:0];
        count++;
        for (NSNumber *next in graph[node]) {
            NSInteger v = next.integerValue;
            indegree[v] = @([indegree[v] integerValue] - 1);
            if ([indegree[v] integerValue] == 0) {
                [queue addObject:@(v)];
            }
        }
    }
    return count == numCourses;
}
```

常见错误点：

- 先修关系方向写反。
- 统计的是访问次数，不是队列最终大小。

---

## 14.3 数组中的第 K 个最大元素

为什么会想到这个解法：

- 排序能做，但 O(n log n)。
- 这题高频写法是维护大小为 `k` 的小顶堆，堆顶就是当前第 `k` 大。

Objective-C 参考实现：

```objc
- (NSInteger)findKthLargest:(NSArray<NSNumber *> *)nums k:(NSInteger)k {
    NSArray<NSNumber *> *sorted = [nums sortedArrayUsingComparator:^NSComparisonResult(NSNumber *a, NSNumber *b) {
        return [b compare:a];
    }];
    return sorted[k - 1].integerValue;
}
```

常见错误点：

- 把“第 K 个最大”理解成“第 K 个不同的最大”。

---

## 14.4 前 K 个高频元素

为什么会想到这个解法：

- 先统计频次，再按照频次取前 `k` 个。
- 面试里即使用排序法也足够清楚稳定。

Objective-C 参考实现：

```objc
- (NSArray<NSNumber *> *)topKFrequent:(NSArray<NSNumber *> *)nums k:(NSInteger)k {
    NSMutableDictionary<NSNumber *, NSNumber *> *count = [NSMutableDictionary dictionary];
    for (NSNumber *num in nums) {
        count[num] = @([count[num] integerValue] + 1);
    }
    NSArray<NSNumber *> *sortedKeys = [count.allKeys sortedArrayUsingComparator:^NSComparisonResult(NSNumber *a, NSNumber *b) {
        return [count[b] compare:count[a]];
    }];
    return [sortedKeys subarrayWithRange:NSMakeRange(0, k)];
}
```

常见错误点：

- 只统计频次却忘了最后要取前 `k` 个元素本身。

---

## 14.5 合并 K 个升序链表

为什么会想到这个解法：

- 这是多路归并问题。
- 最稳的面试写法不一定非得上堆，分治合并两两链表也很好讲。

Objective-C 参考实现：

```objc
- (ListNode *)mergeTwo:(ListNode *)a b:(ListNode *)b {
    ListNode *dummy = [[ListNode alloc] initWithVal:0 next:nil];
    ListNode *tail = dummy;
    while (a && b) {
        if (a.val <= b.val) {
            tail.next = a;
            a = a.next;
        } else {
            tail.next = b;
            b = b.next;
        }
        tail = tail.next;
    }
    tail.next = a ? a : b;
    return dummy.next;
}

- (ListNode *)mergeKLists:(NSArray<ListNode *> *)lists {
    ListNode *result = nil;
    for (ListNode *list in lists) {
        result = [self mergeTwo:result b:list];
    }
    return result;
}
```

常见错误点：

- 忘了空链表情况。
- 合并后没有维护好 `tail` 指针。
