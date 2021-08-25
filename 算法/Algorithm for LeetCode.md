# Algorithm for LeetCode

## 1. double pointer

### (1) 167. 有序数组的Two Sum

题目描述：

给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。

函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。



解题思路：

因为需要找两个数，所以可以使用双指针。

首先进行异常输入判断，如果数组为空，那么直接返回空。

因为数组是已经升序排列的，所以有两个指针。

一个小指针从前向后找小数

一个大指针从后向前找大数

如果和相同，直接返回

如果和比target小，说明小数过小，小指针向后移动一个

如果和比target大，说明大数过大，大指针向前移动一个

知道大小指针相等的时候，说明没有找到，直接返回null



代码：

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        if(numbers == null) return null;
        //i是小指针	j是大指针
        int i = 0, j = numbers.length - 1;
        //在i == j之前
        while(i < j){
            int sum = numbers[i] + numbers[j];
            if(sum == target){
                return new int[]{i + 1, j + 1};
            }else if(sum < target){
                i ++;
            }else{
                j --;
            }
        }
        return null;
    }
}
```



数组中的元素最多遍历一次，时间复杂度为 O(N)。只使用了两个额外变量，空间复杂度为 O(1)。



### (2) 633. 平方数之和

题目描述：

给定一个非负整数 `c` ，你要判断是否存在两个整数 `a` 和 `b`，使得 a2 + b2 = c。

**示例1:**

```
输入: 5
输出: True
解释: 1 * 1 + 2 * 2 = 5
```

 

**示例2:**

```
输入: 3
输出: False
```

解题思路：

同上面那道题差不多，只不过是从和改成平方和。

同样是使用双指针方法，但是这种方法的时间和内存都不怎么好。

```java
class Solution {
    public boolean judgeSquareSum(int c) {
        if(c < 0) return false;
        int i = 0, j = (int) Math.sqrt(c);
        while(i <= j){
            int sum = i * i + j * j;
            if(sum == c) return true;
            else if(sum < c) i++;
            else j--;
        }
        return false;
    }
}
```

有其他的方法，这里介绍一个时间空间都比较好的

费马平方和

费马平方和定理告诉我们：

一个非负整数 c 能够表示为两个整数的平方和，当且仅当 c 的所有形如 4k+3 的质因子的幂次均为偶数。

证明方法可以见 这里。

因此我们对 c 进行质因数分解，再判断形如 4k+3 的质因子的幂次是否均为偶数即可。

```java
public class Solution {
    public boolean judgeSquareSum(int c) {
        for (int i = 2; i * i <= c; i++) {
            int count = 0;
            if (c % i == 0) {
                while (c % i == 0) {
                    count++;
                    c /= i;
                }
                if (i % 4 == 3 && count % 2 != 0)
                    return false;
            }
        }
        return c % 4 != 3;
    }
}
```



### (3) 345. 反转字符串中的元音字符

题目描述：

编写一个函数，以字符串作为输入，反转该字符串中的元音字母。

示例 1:

输入: "hello"
输出: "holle"
示例 2:

输入: "leetcode"
输出: "leotcede"



解题思路：

双指针没跑了

首先为了判断元音字母，可以使用HashSet将所有大小写的元音字母放进去，这样就能用contains(String s)来判断该字符是否是原因字符了。

然后用一个字符数组char[]来存储遍历过的字符。最后使用 new String来将字符数组转为字符串

左边指针向后找一个元音字母，右边指针向前找一个元音字母。

如果指针指向的字符不是元音字母，就直接插入数组

如果指针指向的字符是元音字母，就交换位置插入数组



代码：

```java
class Solution {
    private final static HashSet<Character> vowels = new HashSet<>(
        Arrays.asList('a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'));
    public String reverseVowels(String s) {
        if(s == null) return null;
        int i = 0, j = s.length() - 1;
        char[] res = new char[s.length()];
        while(i <= j){
            char ci = s.charAt(i);
            char cj = s.charAt(j);

            if(!vowels.contains(ci)) res[i++] = ci;
            else if(!vowels.contains(cj)) res[j--] = cj;
            else{
                res[i++] = cj;
                res[j--] = ci;
            }
        }
        return new String(res);
    }
}
```

- 时间复杂度为 O(N)：只需要遍历所有元素一次
- 空间复杂度 O(1)：只需要使用两个额外变量



### (4) 680.  回文字符串

题目描述：

给定一个非空字符串 `s`，**最多**删除一个字符。判断是否能成为回文字符串。

**示例 1:**

```
输入: "aba"
输出: True
```

**示例 2:**

```
输入: "abca"
输出: True
解释: 你可以删除c字符。
```



解题思路：

回文字符是指：正着读或者反着读出来的字符串是一模一样的

题目条件给了一个可以删除一个字符再进行判断是否是回文字符

删除有两种情况，一种是删左边的，一种是删右边的

那么就可以在遇见前后两个指针的字符不相同的时候，再判断剩下的字符串是否是回文字符串就行



代码：

```java
class Solution {
    public boolean validPalindrome(String s) {
        for(int i = 0, j = s.length() - 1; i < j; i++, j--){
            if(s.charAt(i) != s.charAt(j)) return isPalindrome(s, i+1, j) | isPalindrome(s, i, j-1);
        }
        return true;
    }
    public boolean isPalindrome(String s, int i, int j){
        while(i < j){
            if(s.charAt(i++) != s.charAt(j--)) return false;
        }
        return true;
    }
}
```



### (5) 88. 归并两个有序数组

题目描述：

给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 num1 成为一个有序数组。

 

说明:

初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。
你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。


示例:

输入:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

输出: [1,2,2,3,5,6]



解题思路：

这属于一个原地合并的问题，两个数组就双指针啦

需要从尾部开始遍历，否则在num1上归并得到的值会覆盖还未进行比较的值

index1和index2分别为两个指针，indexMerge为归并后的num1的指针

分情况：

1.index1 < 0：这种情况说明num1的所有的数都比num2的其中某个或几个数大，这时候num1已经归并完成并且放到了num1的后面，此时只需要把num2的剩下的元素放到num1当中即可。

2.index2 < 0：同上。

3.这是进行正常判断的条件，判断两个元素大小，然后放到num1当中。



代码：

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int index1 = m - 1, index2 = n - 1;
        int indexMerge = m + n - 1;
        while(index1 >= 0 || index2 >= 0){
            if(index1 < 0){
                nums1[indexMerge--] = nums2[index2--];
            }else if(index2 < 0){
                nums1[indexMerge--] = nums1[index1--];
            }else if(nums1[index1] > nums2[index2]){
                nums1[indexMerge--] = nums1[index1--];
            }else {
                nums1[indexMerge--] = nums2[index2--];
            }
        }
    }
}
```



### (6) 141. 判断链表是否存在环

题目描述：


给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos`是 `-1`，则在该链表中没有环。

 

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

**示例 2：**

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)



解题思路：

使用双指针，一个指针每次移动**一个节点**，一个指针每次移动**两个节点**，如果存在环，那么这两个指针一定会相遇。



代码：

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null) return false;
        ListNode l1 = head, l2 = head.next;
        while(l1 != null && l2 != null && l2.next != null){
            if(l1 == l2) return true;
            l1 = l1.next;
            l2 = l2.next.next;
        }
        return false;
    }
}
```



### (7) 524. 最长子序列

题目描述：


给定一个字符串和一个字符串字典，找到字典里面最长的字符串，该字符串可以通过删除给定字符串的某些字符来得到。如果答案不止一个，返回长度最长且字典顺序最小的字符串。如果答案不存在，则返回空字符串。

**示例 1:**

```
输入:
s = "abpcplea", d = ["ale","apple","monkey","plea"]

输出: 
"apple"
```

**示例 2:**

```
输入:
s = "abpcplea", d = ["a","b","c"]

输出: 
"a"
```

**说明:**

1. 所有输入的字符串只包含小写字母。
2. 字典的大小不会超过 1000。
3. 所有输入的字符串长度不会超过 1000。



解题思路：

首先用一个变量存储最长字符串。

开始遍历d中的所有字符串

l1是最长字符串的长度，l2是当前遍历字符串的长度，如果l2比l1小，或者l1==l2且字符串相同，就直接continue

否则，说明该字符串比最长字符串要长。现在可以判断该字符串是否是s的子字符串，如果是，就把该字符串赋值给最长字符串

最终返回最长字符串。



代码：

```java
class Solution {
    public String findLongestWord(String s, List<String> d) {
        String longestWord = "";
        for(String target : d){
            int l1 = longestWord.length(), l2 = target.length();
            if(l1 > l2 || (l1 == l2 && longestWord.compareTo(target) < 0)) continue;
            if(isSubstr(s, target)){
                longestWord = target;
            }
        }
        return longestWord;
    }
    public boolean isSubstr(String s, String target){
        int i = 0, j = 0;
        while(i < s.length() && j < target.length()){
            if(s.charAt(i) == target.charAt(j)) j++;
            i++;
        }
        return j == target.length();
    }
}
```



## 2. sort

### (1) 215. Kth Element

题目描述：


在未排序的数组中找到第 **k** 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

**示例 1:**

```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

**示例 2:**

```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```



解题思路：

这个在java里面有三种方法

第一种是首先把数组进行排序，然后直接返回num.length - k的元素

第二种是利用小顶堆来实现，Java当中有个类是PriorityQueue，这个队列底层是用小顶堆（小根堆）来实现的。首先用一个增强for循环把所有的元素都入队列，如果队列的大小大于K的时候，就把队首元素出队列，每次出队的元素都比当前队列中的元素要小（因为小根堆的根结点就是整个堆中的最小元素）。当所有元素都遍历完成之后，队列中只有K个元素，而且队首元素就是队列中最小的元素，也就是数组中的第K个最大元素，这时候直接获取队首元素即可。

第三种就是quickselect算法。这个算法听起来很像一个排序算法：quicksort。事实上，这两种算法很相似，quickselect借用了quicksort的思想。

quickselect算法思想：

首先找到一个pivot元素

1.根据pivot元素将空间分成两部分，一部分比pivot小，一部分比pivot大

2.根据比pivot小的元素的个数，判断Kth Element在哪一部分当中，此时分成三种情况：

（1）有K - 1个比pivot小的元素，那么pivot即为Kth Element

（2）有小于K - 1个比pivot小的元素，那么Kth Element在比pivot大的部分，那么就可以在比pivot大的部分当中重复上述步骤

（3）有大于K - 1个比pivot小的元素，那么Kth Element在比pivot小的部分，那么就可以在比pivot小的部分当中重复上述步骤

因此，整个算法当中的核心就是partition（分割），将整个空间分成两部分。

因为quickselect算法只是查找Kth Element，所以可以省去对无用部分的检索，时间效率也比quicksort要好一些。



代码：

```java
排序 ：时间复杂度 O(NlogN)，空间复杂度 O(1)
public int findKthLargest(int[] nums, int k) {
    Arrays.sort(nums);
    return nums[nums.length - k];
}

堆 ：时间复杂度 O(NlogK)，空间复杂度 O(K)。
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>(); // 小顶堆
    for (int val : nums) {
        pq.add(val);
        if (pq.size() > k)  // 维护堆的大小为 K
            pq.poll();
    }
    return pq.peek();
}

快速选择 ：时间复杂度 O(N)，空间复杂度 O(1)
public int findKthLargest(int[] nums, int k) {
    k = nums.length - k;//为了与下面的j做判断，设置为Kth Element元素的真实索引
    int l = 0, h = nums.length - 1;//起始l为0 为第一个元素，h为nums.length - 1为最后一个元素
    while (l < h) {
        int j = partition(nums, l, h);//开始进行分组操作
        if (j == k) {
            break;
        } else if (j < k) {
            l = j + 1;
        } else {
            h = j - 1;
        }
    }
    return nums[k];
}

private int partition(int[] a, int l, int h) {
    int i = l, j = h + 1;//选取第一个元素为pivot，也就是a[l]
    while (true) {
        while (a[++i] < a[l] && i < h) ;//找到第一个比a[l]大的元素
        while (a[--j] > a[l] && j > l) ;//找到第一个比a[l]小的元素
        if (i >= j) {
            break;
        }
        swap(a, i, j);//交换a[i]和a[j]元素的位置
    }
    swap(a, l, j);//交换a[l]和a[j]元素的位置
    return j;
}

private void swap(int[] a, int i, int j) {
    int t = a[i];
    a[i] = a[j];
    a[j] = t;
}
```



### (1) 347. Top K Frquent Elements

题目描述：


给定一个非空的整数数组，返回其中出现频率前 ***k\*** 高的元素。

**示例 1:**

```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```

**示例 2:**

```
输入: nums = [1], k = 1
输出: [1]
```

**说明：**

- 你可以假设给定的 *k* 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。
- 你的算法的时间复杂度**必须**优于 O(*n* log *n*) , *n* 是数组的大小。



解题思路：





代码：

```java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> res = new HashMap<>();
        for(Integer num : nums)  res.put(num, res.getOrDefault(num, 0) + 1);
        List<Integer>[] bucket = new ArrayList[nums.length + 1];
        for(Integer key : res.keySet()){
            int frequency = res.get(key);
            if(bucket[frequency] == null) bucket[frequency] = new ArrayList<>();
            bucket[frequency].add(key);
        }
        List<Integer> topK = new ArrayList<>();
        for(int i = bucket.length - 1; i >= 0 && topK.size() < k; i--){
            if(bucket[i] == null) continue;
            if(bucket[i].size() <= (k - topK.size())) topK.addAll(bucket[i]);
            else topK.addAll(bucket[i].subList(0, k - topK.size()));
        }
        return topK;
    }
}
```





















题目描述：





解题思路：





代码：

```java

```

