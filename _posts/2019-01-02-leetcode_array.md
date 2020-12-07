---
title: Leetcode数组问题
categories: 编程相关
tags:
- 编程相关
---

### 1.1 [ 删除排序数组中的重复项 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

#### 1.1.1 题目描述

给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素最多出现两次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

实例 1：

```bash
给定 nums = [1,1,1,2,2,3],

函数应返回新长度 length = 5, 并且原数组的前五个元素被修改为 1, 1, 2, 2, 3 。

你不需要考虑数组中超出新长度后面的元素。
```

实例 2：

```bash
给定 nums = [0,0,1,1,1,1,2,3,3],

函数应返回新长度 length = 7, 并且原数组的前五个元素被修改为 0, 0, 1, 1, 2, 3, 3 。

你不需要考虑数组中超出新长度后面的元素。
```

#### 1.1.2 思路解析

对于没有排好序的数组，则需要引入哈希表来记录每个数字的出现频率，如果数组已经排好序，简单判断是否和前max_num_dulplicate元素是否重复即可，代码如下，可以通过调节`max_num_dulplicate` 变量来改变元素最大的出现频率。

#### 1.1.3 代码

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
      	// 多加const
        const int max_num_dulplicate = 2;
        // 边界条件判断
        if (nums.size() <= max_num_dulplicate) {
            return nums.size();
        }
        int index = max_num_dulplicate;
        for(int i = index; i < nums.size(); i++) {
          	// 判断是否重复
            if (nums[index-max_num_dulplicate] != nums[i]) {
                nums[index++] = nums[i];
            }
        }
        return index;
    }
};
```

### 1.2 [搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

#### 1.2.1 题目描述

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。

编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。

实例 1：

```bash
输入: nums = [2,5,6,0,0,1,2], target = 0
输出: true
```

实例 2：

```bash
输入: nums = [2,5,6,0,0,1,2], target = 3
输出: false
```

#### 1.2.2 思路解析

本题可以借鉴二分搜索的思路，首先去`left`和`right`的中间索引`middle`，判断`left`->`middle` 是否有序（发生旋转），如果有序（没有发生旋转），即可通过二分搜索的思路来判断`left`->`middle`之内是否可能存在`target`，否则判断`middle` ->`right`之间。

#### 1.2.3 代码

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        // 边界条件判断
        if (nums.empty()) {
            return false;
        }
        unsigned int left = 0, right = nums.size()-1;
        while(left <= right) {
            unsigned int middle = (left + right) / 2;
            if (nums[middle] == target) {
                return true;
            }
            // 判断left->middle是否有序
            else if (nums[middle] > nums[left]) {
                // 判断target 是否在left 和 middle 之间
                if (target >= nums[left] && target < nums[middle]) {
                    right = middle - 1;
                }
                else {
                    left = middle + 1;
                }
            }
            else if (nums[middle] < nums[left]) {
                if (target > nums[middle] && target <= nums[right]) {
                    left =  middle + 1;
                }
                else {
                    right = middle - 1;
                }
            }
            else{
                left++;
            }
        }
        return false;
    }
};
```

### 1.3 [寻找两个有序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

#### 1.3.1 题目描述

给定两个大小为 m 和 n 的有序数组 `nums1` 和 `。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设  `nums1` 和 `nums2` 不会同时为空。

实例 1：

```bash
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
```

实例 2：

```bash
nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5
```

#### 1.3.2 思路解析



#### 1.3.3 代码

```c++
class Solution {
public:
    double findMedianSortedArrays(const vector<int>& nums1, const vector<int>& nums2) {
        // 多使用const，加分项      
        const int l_1 = nums1.size();
        const int l_2 = nums2.size();
        const int l = l_1 + l_2;
        // 判断奇偶 (加分项)      
        if (l & 0x1) {
            return findKthSortedArrays(nums1.begin(), l_1, nums2.begin(), l_2, l / 2 + 1);
        }
        else {
            return (findKthSortedArrays(nums1.begin(), l_1, nums2.begin(), l_2, l / 2) + 
            findKthSortedArrays(nums1.begin(), l_1, nums2.begin(), l_2, l / 2 + 1)) / 2.0;
        }
    }
    double findKthSortedArrays(std::vector<int>::const_iterator A, const int m, 
                               std::vector<int>::const_iterator B, const int n,
                               const int k) {
        if (m > n) {
            return findKthSortedArrays(B, n, A, m, k);
        }
        if (m == 0) {
            return *(B + k - 1);
        }
        if (k == 1) {
            return min(*A, *B);
        }
        const int a_i = min(m, k/2);
        const int b_i = k - a_i;
        if (*(A + a_i - 1) < *(B + b_i - 1)) {
            return findKthSortedArrays(A + a_i, m - a_i, B, n, k - a_i);
        }
        else if (*(A + a_i - 1) > *(B + b_i - 1)) {
            return findKthSortedArrays(A, m, B + b_i, n - b_i, k - b_i);
        }
        else {
            return *(A + a_i - 1);
        }
 
    }
};
```

### 1.4 [最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

#### 1.4.1 题目描述

给定一个未排序的整数数组，找出最长连续序列的长度。

要求算法的时间复杂度为 *O(n)*。

实例 1：

```bash
输入: [100, 4, 200, 1, 3, 2]
输出: 4
解释: 最长连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

#### 1.4.2 思路解析



#### 1.4.3 代码

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        unordered_map<int, bool> used;
        for (auto num:nums) {
            used[num] = false;
        }
        unsigned int longest_length = 0;
        for (auto num:nums) {
            // 如果被查询过了
            if (used[num]) {
                continue;
            }
            used[num] = true;
            unsigned int tmp_length = 1;
            for (int j = num+1; used.find(j)!=used.end(); j++) {
                used[j] = true;
                ++tmp_length;
            }
            for (int j = num-1; used.find(j)!=used.end(); j--) {
                used[j] = true;
                ++tmp_length;
            }
            longest_length = max(longest_length, tmp_length);
        }
        return longest_length;
    }
};
```



### 1.5 [两数之和](https://leetcode-cn.com/problems/two-sum/)

#### 1.5.1 题目描述

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍

实例 1：

```bash
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

#### 1.5.2 思路解析



#### 1.5.3 代码

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mapping;
        vector<int> results;
        if (nums.empty()) {
            return results;
        }
        for(int i = 0; i < nums.size(); i++) {
            const int gap = target - nums[i];
            if (mapping.find(gap) != mapping.end() && mapping[gap] < i) {
                results.push_back(mapping[gap]);
                results.push_back(i);
                break;
            }
            mapping[nums[i]] = i;
        }
        return results;
    }
};
```





