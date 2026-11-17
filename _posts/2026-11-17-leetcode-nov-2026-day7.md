---
layout:     post
title:      "LeetCode - Nov 2026 | Day 7"
subtitle:   "LeetCode 229, 238 & 448 - Majority Element II, Product of Array Except Self & Find All Numbers Disappeared in an Array"
date:       2026-11-17 13:03:00
author:     "Eric Zhou"
catalog:    true
published:  true
header-img: "img/post-bg-js-module.jpg"
tags:
    - Algorithms
    - LeetCode
    - Java
---

## Introduction
{:.no_toc}

Today, I solved three array problems: LeetCode 448, 238, and 229. They illustrate three different techniques: checking gaps after sorting, combining prefix and suffix products, and tracking candidates with the Boyer–Moore Voting Algorithm.

## Table of contents
{:.no_toc}

* TOC
{:toc}

---

## LeetCode 448 - Find All Numbers Disappeared in an Array

### Question

Given an array `nums` of length `n`, where every number is in the range `[1, n]`, return all numbers in `[1, n]` that do not appear in `nums`. The output can be in any order.

**Example:**

```text
Input:  nums = [4,3,2,7,8,2,3,1]
Output: [5,6]
```

### Intuition

After sorting, missing values can occur in three places: before the first element, between adjacent elements, or after the last element. I check all three regions separately.

### Approach

1. Sort `nums` using `Arrays.sort(nums)`.
2. Starting from `nums[0]`, add any missing positive integers below the smallest value.
3. For every pair of adjacent sorted elements, use a temporary variable `check` to add the integers between them. This avoids modifying the sorted array.
4. Starting from `n`, add any integers greater than the largest value.
5. Return the result list. The problem allows any output order, so the decreasing order within a gap is fine.

### Time Complexity

$$
T(n) = O(n\log n) + O(n) = O(n\log n)
$$

Sorting takes $O(n\log n)$. The gap checks traverse the array and add each missing value at most once, for a combined $O(n)$ after sorting.

### Space Complexity

$$
S(n) = O(\log n)
$$

The returned list is excluded from auxiliary-space analysis. Java's `Arrays.sort(int[])` can use $O(\log n)$ stack space; the gap checks otherwise use a constant number of variables.

### Code

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Arrays;

class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        int n = nums.length;
        Arrays.sort(nums);
        ArrayList result = new ArrayList<>();

        int begin = nums[0];
        while (begin - 1 > 0) {
            result.add(begin - 1);
            begin--;
        }

        for (int i = 0; i < n - 1; i++) {
            int check = nums[i + 1];
            while (check - nums[i] > 1) {
                result.add(check - 1);
                check = check - 1;
            }
        }

        int addi = n;
        while (addi - nums[n - 1] > 0) {
            result.add(addi);
            addi--;
        }

        return result;
    }
}
```

**Note:** This is my accepted sorting solution. It is not the follow-up's $O(n)$-time, $O(1)$-auxiliary-space approach.

---

## LeetCode 238 - Product of Array Except Self

### Question

Return an array `result` where `result[i]` equals the product of all elements of `nums` except `nums[i]`. The algorithm must run in $O(n)$ time without division.

**Example:**

```text
Input:  nums = [1,2,3,4]
Output: [24,12,8,6]
```

### Intuition

For every index, the required product is the product of all elements to its left multiplied by the product of all elements to its right. I can calculate these contributions in two passes without storing separate prefix and suffix arrays.

### Approach

1. Initialize `result` and set `leftsum = 1`, `rightsum = 1`.
2. Traverse left to right. Save `leftsum` in `result[i]`, then multiply `leftsum` by `nums[i]`.
3. Traverse right to left. Multiply `result[i]` by `rightsum`, then multiply `rightsum` by `nums[i]`.
4. Return `result`.

For example, when `nums = [1,2,3,4]`, the first pass stores `[1,1,2,6]`; the second pass multiplies in the right-side products and produces `[24,12,8,6]`.

### Time Complexity

$$
T(n) = O(n) + O(n) = O(n)
$$

Each pass traverses the input once.

### Space Complexity

$$
S(n) = O(1)
$$

Only two running products are used beyond the output array. If the output array is counted, total space is $O(n)$.

### Code

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int[] result = new int[nums.length];
        int leftsum = 1;
        int rightsum = 1;

        for (int i = 0; i < nums.length; i++) {
            result[i] = leftsum;
            leftsum = leftsum * nums[i];
        }

        for (int i = nums.length - 1; i >= 0; i--) {
            result[i] = result[i] * rightsum;
            rightsum = rightsum * nums[i];
        }

        return result;
    }
}
```

---

## LeetCode 229 - Majority Element II

### Question

Given an array of length `n`, return every element that appears more than $\lfloor n/3\rfloor$ times.

**Example:**

```text
Input:  nums = [3,2,3]
Output: [3]
```

### Intuition

There can be at most two distinct elements that appear more than $n/3$ times. I use two candidates and two counters, extending the Boyer–Moore Voting Algorithm. A second pass is necessary because the first pass finds only potential majority elements.

### Approach

1. Maintain candidates `candi1`, `candi2` and counts `count1`, `count2`.
2. For each number, increment the corresponding count if it matches a candidate.
3. Otherwise, fill a candidate slot whose count is zero. If both slots are active and neither matches, decrement both counts.
4. Reset both counts and traverse the array again to count actual occurrences.
5. Add a candidate only if its count exceeds `n / 3`. Check that the two candidates are different to avoid duplicates.

### Time Complexity

$$
T(n) = O(n) + O(n) = O(n)
$$

The candidate-selection pass and the verification pass each traverse the array once.

### Space Complexity

$$
S(n) = O(1)
$$

The algorithm stores two candidates and two counters. The result list holds at most two integers.

### Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> majorityElement(int[] nums) {
        int candi1 = 0;
        int candi2 = 0;
        int count1 = 0;
        int count2 = 0;
        int n = nums.length;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == candi1) {
                count1++;
            } else if (nums[i] == candi2) {
                count2++;
            } else if (count1 == 0) {
                candi1 = nums[i];
                count1 = 1;
            } else if (count2 == 0) {
                candi2 = nums[i];
                count2 = 1;
            } else {
                count1--;
                count2--;
            }
        }

        count1 = 0;
        count2 = 0;

        for (int i = 0; i < n; i++) {
            if (nums[i] == candi1) {
                count1++;
            } else if (nums[i] == candi2) {
                count2++;
            }
        }

        List result = new ArrayList<>();
        if (count1 > n / 3) {
            result.add(candi1);
        }
        if (candi1 != candi2 && count2 > n / 3) {
            result.add(candi2);
        }

        return result;
    }
}
```
