# iOS 工程师算法面试学习手册答案

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
