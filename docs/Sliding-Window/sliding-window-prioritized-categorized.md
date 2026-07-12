# Sliding Window — Prioritized Pattern Catalog (Java)

> **Practical goal:** learn every *principle*, not every LeetCode number.
>
> Source catalog: [sliding-window-categorized.md](./sliding-window-categorized.md) (all **169** problems).
> This file compresses that list into **anchors → twists → drills** so ~**30–40** solves cover the pattern space.

---

## Priority system

| Priority | Name | Meaning | Action |
|:---:|---|---|---|
| **P0** | **Anchor** | Introduces a **unique** principle / data structure / reformulation | **Must solve** — own the template |
| **P1** | **Essential twist** | Same family, but a **non-obvious** change (not just different numbers) | **Should solve** after its anchor |
| **P2** | **Drill** | Same principle, different story / constraints | Skip if short on time; use only for reps |
| **P3** | **Niche** | Rare in interviews, heavy domain, or fully covered by P0+P1 | Skip unless you hit it in contest |

### How coverage works

```
Solve P0 ──covers──► all P2/P3 that only re-skin that principle
Solve P0 + P1 ──covers──► the whole category for interview purposes
```

If two problems need **different templates** (e.g. “longest with ≤K flips” vs “window − maxFreq ≤ K”), both stay **P0** even though both are “budget windows.”

---

## TL;DR — three study budgets

| Budget | What to do | ~Count | Outcome |
|---|---|---:|---|
| **Minimal** | All **P0** in study order | **24** | Every major principle once |
| **Interview-ready** | All **P0 + P1** | **40** | Anchors + meaningful twists |
| **Deep** | P0 + P1 + any P2 you miss in mocks | 40–60 | Pattern + speed |

**Do not grind all 169.** After P0+P1, extra problems are optional reps.

---

## Master priority table (study order)

Solve top → bottom. **Covers** = problems you can treat as “done” once you own this one (same principle).

### Wave 1 — Core muscle memory (fixed + variable)

| Order | Pri | # | Problem | Cat | Principle you learn | Covers (same principle) |
|---:|:---:|---:|---|:---:|---|---|
| 1 | **P0** | 643 | [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/) | A | Fixed window: add right, drop left, maintain sum | 1176, 1343, 1456, 2269, 3364, 3652 |
| 2 | **P0** | 3 | [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | E | Variable longest + uniqueness (shrink on duplicate) | 2743, 2260* |
| 3 | **P0** | 340 | [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) | E | At-most-K distinct (freq map + size) | 159, 904 |
| 4 | **P1** | 2958 | [Length of Longest Subarray With at Most K Frequency](https://leetcode.com/problems/length-of-longest-subarray-with-at-most-k-frequency/) | E | Cap is **per-value frequency**, not distinct count | 3090 |
| 5 | **P1** | 1695 | [Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/) | E | Same as unique-window but optimize **sum**, not length | — |
| 6 | **P0** | 1004 | [Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/) | F | Budget of **bad cells** (zeros ≤ K) | 487, 2024† |
| 7 | **P0** | 424 | [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) | F | Budget form: `len − maxFreq ≤ K` | 2024† |
| 8 | **P1** | 1493 | [Longest Subarray of 1's After Deleting One Element](https://leetcode.com/problems/longest-subarray-of-1-s-after-deleting-one-element/) | F | Must delete **exactly one** (not “at most”) | — |
| 9 | **P0** | 209 | [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) | G | Shortest valid while positives (shrink when valid) | 3095, 3097‡, 3795‡ |
| 10 | **P1** | 1658 | [Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/) | G | **Complement**: min ends removed ↔ longest middle sum = total−X | — |
| 11 | **P1** | 1234 | [Replace the Substring for Balanced String](https://leetcode.com/problems/replace-the-substring-for-balanced-string/) | G | Min window that absorbs **excess** counts | — |

\*2260 is min length until first duplicate — same uniqueness map, different objective.  
†2024 is either “zeros” style or “maxFreq” style depending on how you model T/F.  
‡OR/distinct-sum shortest need bit or set tracking; principle is still “shortest once valid.”

### Wave 2 — Covering, counting, fixed-window variants

| Order | Pri | # | Problem | Cat | Principle you learn | Covers |
|---:|:---:|---:|---|:---:|---|---|
| 12 | **P0** | 76 | [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | H | Need-map + `missing` / `formed`; shrink while covering | 3297, 3298 |
| 13 | **P0** | 567 | [Permutation in String](https://leetcode.com/problems/permutation-in-string/) | H | Fixed-length **anagram / multiset match** | 438, 187§ |
| 14 | **P1** | 30 | [Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/) | H | Covering with **word blocks** (not chars) | — |
| 15 | **P1** | 1358 | [Number of Substrings Containing All Three Characters](https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/) | H | Covering used for **counting** (all indices once valid) | 3305, 3306 |
| 16 | **P0** | 992 | [Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) | I | `exactly(K) = atMost(K) − atMost(K−1)` | 3859, 2799¶ |
| 17 | **P0** | 713 | [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/) | I | Count with shrink; `ans += right−left+1` | 3258 |
| 18 | **P1** | 1248 | [Count Number of Nice Subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays/) | I | Map problem → “exactly K **odd**” = binary atMost | 930 |
| 19 | **P1** | 2962 | [Count Subarrays Where Max Element Appears at Least K Times](https://leetcode.com/problems/count-subarrays-where-max-element-appears-at-least-k-times/) | I | Count with **≥ K** (not at-most); push left past last valid | — |
| 20 | **P1** | 2444 | [Count Subarrays With Fixed Bounds](https://leetcode.com/problems/count-subarrays-with-fixed-bounds/) | I | Distinct: last index of min/max/out-of-range | — |
| 21 | **P0** | 2461 | [Maximum Sum of Distinct Subarrays With Length K](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/) | B | Fixed k + distinct/freq validity | 1100, 1876, 2841, 1852 |
| 22 | **P0** | 2379 | [Minimum Recolors to Get K Consecutive Black Blocks](https://leetcode.com/problems/minimum-recolors-to-get-k-consecutive-black-blocks/) | C | Fixed k, **minimize bad cells** inside | — |
| 23 | **P0** | 1151 | [Minimum Swaps to Group All 1's Together](https://leetcode.com/problems/minimum-swaps-to-group-all-1-s-together/) | C | Window length = **count of 1s**, min zeros | 2134# |

§187 is fixed DNA window + “seen before” hash; close to fixed multiset.  
¶2799 = at-most until full distinct of array, then count.  
#2134 is circular version of 1151 (also listed under O).

### Wave 3 — Hard interview favorites (deque, prefix, reformulations)

| Order | Pri | # | Problem | Cat | Principle you learn | Covers |
|---:|:---:|---:|---|:---:|---|---|
| 24 | **P0** | 239 | [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | J | Monotonic deque of indices | — |
| 25 | **P0** | 1438 | [Longest Continuous Subarray With Absolute Diff ≤ Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) | J | Two deques: max & min; shrink when max−min > limit | 2762, 3578** |
| 26 | **P0** | 862 | [Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/) | J | Prefix + **increasing** deque (negatives break plain two-pointer) | — |
| 27 | **P0** | 1423 | [Maximum Points You Can Obtain from Cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/) | K | Prefix/suffix trade-off window | 1031†† |
| 28 | **P0** | 995 | [Minimum Number of K Consecutive Bit Flips](https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips/) | K | Diff array / queue of flip coverage | — |
| 29 | **P1** | 1871 | [Jump Game VII](https://leetcode.com/problems/jump-game-vii/) | K | Reachability as a sliding reachable range | — |

\*\*3578 often needs DP + mono queue; core max−min window idea is 1438.  
††1031 is two fixed non-overlapping windows (related, not identical).

### Wave 4 — Sort / bisect / circular / multi / special anchors

| Order | Pri | # | Problem | Cat | Principle you learn | Covers |
|---:|:---:|---:|---|:---:|---|---|
| 30 | **P0** | 1838 | [Frequency of the Most Frequent Element](https://leetcode.com/problems/frequency-of-the-most-frequent-element/) | L | Sort + cost window (ops to equalize) | 2968, 3346, 3347 |
| 31 | **P0** | 2009 | [Minimum Number of Operations to Make Array Continuous](https://leetcode.com/problems/minimum-number-of-operations-to-make-array-continuous/) | L | Sort unique + window of size n; min gaps | 3634 |
| 32 | **P1** | 1918 | [Kth Smallest Subarray Sum](https://leetcode.com/problems/kth-smallest-subarray-sum/) | L | Binsearch answer + **count** windows with sum ≤ mid | 3134‡‡ |
| 33 | **P1** | 658 | [Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/) | L | Binsearch the **left edge** of a fixed window of size k | — |
| 34 | **P0** | 2779 | [Maximum Beauty of an Array After Applying Operation](https://leetcode.com/problems/maximum-beauty-of-an-array-after-applying-operation/) | M | Sort values, then window with max−min ≤ 2k | 594, 2932 |
| 35 | **P0** | 1703 | [Minimum Adjacent Swaps for K Consecutive Ones](https://leetcode.com/problems/minimum-adjacent-swaps-for-k-consecutive-ones/) | M | Positions of ones + median cost window | 3086 |
| 36 | **P1** | 1610 | [Maximum Number of Visible Points](https://leetcode.com/problems/maximum-number-of-visible-points/) | M | Angular sort + circular-ish angle window | — |
| 37 | **P0** | 2401 | [Longest Nice Subarray](https://leetcode.com/problems/longest-nice-subarray/) | N | Bitmask window (AND conflict → shrink) | — |
| 38 | **P0** | 2134 | [Minimum Swaps to Group All 1's Together II](https://leetcode.com/problems/minimum-swaps-to-group-all-1-s-together-ii/) | O | Circular: double array, cap window ≤ n | 1652, 3206, 3208, 1888 |
| 39 | **P0** | 689 | [Maximum Sum of 3 Non-Overlapping Subarrays](https://leetcode.com/problems/maximum-sum-of-3-non-overlapping-subarrays/) | P | Precompute window sums + left/right best + mid | 3956, 3957 |
| 40 | **P0** | 219 | [Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/) | R | Last-index / set of last k | — |
| 41 | **P0** | 220 | [Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/) | R | **Bucket map** for value proximity (unique) | — |
| 42 | **P0** | 978 | [Longest Turbulent Subarray](https://leetcode.com/problems/longest-turbulent-subarray/) | S | Run-length / local pattern state | 413, 2110, 2760, 3254, 3255 |
| 43 | **P0** | 395 | [Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/) | S | Split on rare chars (D&C) — not plain two-pointer | — |
| 44 | **P0** | 480 | [Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) | D | Two heaps / TreeMap multiset order stats | 2653 |
| 45 | **P0** | 632 | [Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) | T | Multi-pointer heap “window” over k lists | — |
| 46 | **P0** | 1052 | [Grumpy Bookstore Owner](https://leetcode.com/problems/grumpy-bookstore-owner/) | T | Fixed window of **extra** gain on top of base | — |

‡‡3134 is binsearch on distinct-count + atMost-style counting.

**Minimal path (P0 only):** rows marked P0 above → **24 problems** if you also skip optional-domain P0s 480, 632, 1052, 220, 395 for later → true core **~20**.  
**Recommended interview path:** all **P0 + P1** in the table (**~40**).

---

## Starter set (absolute first 20)

If you only have time for twenty:

| # | Why |
|---:|---|
| 643 | Fixed sum |
| 3 | Longest unique |
| 340 | At-most-K distinct |
| 1004 | Flip budget |
| 424 | Replace budget |
| 209 | Shortest sum |
| 76 | Covering min window |
| 567 | Anagram fixed window |
| 992 | atMost − atMost |
| 713 | Count product |
| 2461 | Fixed + distinct |
| 2379 | Min bad in fixed k |
| 1151 | Window size = ones count |
| 239 | Mono deque max |
| 1438 | Max−min deques |
| 862 | Prefix + mono deque |
| 1423 | Prefix/suffix cards |
| 1838 | Sort + cost window |
| 2779 | Sort + value window |
| 2134 | Circular fixed window |

These twenty touch almost every **core** template. Add 1658, 1493, 1248, 2444, 995, 689, 2401, 480 when leveling up.

---

## Coverage map (principle → one solve)

| Principle | Learn from (P0) | Optional twists (P1) | Skip after that |
|---|---|---|---|
| Fixed sum / average | **643** | 2090 (centered radius) | 1176, 1343, 1456, 2269, 3364, 3652 |
| Fixed + distinct/freq | **2461** | — | 1100, 1876, 1852, 2841, 3318, 3321, 3672 |
| Fixed min “bad” cells | **2379** | — | — |
| Window size = #targets | **1151** | **2134** circular | — |
| Order statistics in window | **480** | — | 2653 |
| Longest unique | **3** | 1695 sum | 2743 |
| Longest ≤K distinct | **340** | — | 159, 904 |
| Longest ≤K freq each | **2958** | — | 3090 |
| Budget: zeros ≤ K | **1004** | 1493 exact delete | 487 |
| Budget: len − maxFreq ≤ K | **424** | — | 2024 (either model) |
| Shortest valid (positives) | **209** | 1658 complement, 1234 excess | 1208, 2516, 2875, 2904 |
| Covering need-map | **76** | 30 words, 1358 count | 3297/8, 2781*, 2953* |
| Fixed anagram | **567** | — | 438 |
| exactly K = atMost trick | **992** | 1248 odds, 2962 ≥K | 930, 2799, 3859 |
| Count with shrink | **713** | — | 3258, 2302† |
| Fixed bounds counting | **2444** | — | — |
| Deque window max | **239** | — | — |
| Deque max−min | **1438** | — | 2762 |
| Prefix + mono deque (negatives) | **862** | — | — |
| Prefix/suffix choose | **1423** | — | — |
| Diff-array flips | **995** | 1871 reachability | — |
| Sort + equalize cost | **1838** | 1918 kth sum, 658 k closest | 2968, 3346/7 |
| Sort unique continuous | **2009** | — | 3634 |
| Sort + value range | **2779** | 1610 angles, 1703 ones median | 594, 2271, 2555, 2932/5 |
| Bitwise no shared bits | **2401** | 2411, 3097 bit counts | 3411 |
| Circular window | **2134** | — | 1652, 1888, 3206, 3208 |
| M non-overlapping windows | **689** | — | 3956, 3957 |
| Nearby equal | **219** | — | — |
| Nearby almost-equal buckets | **220** | — | — |
| Run / pattern state | **978** | — | 413, 2110, 2760, 3254/5 |
| At least K freq split | **395** | — | — |
| K-list heap range | **632** | — | — |
| Base + bonus fixed window | **1052** | — | — |

\*2781/2953 are hard domain variants of covering — P1 if targeting hard contests.  
†2302 score = sum×len is a counting twist (P1 if you want).

---

## Categories (condensed by priority)

Each category lists **P0 / P1** with templates, then **P2 / P3** as a skip list.

### A. Fixed window — sum / average / count

**Principle:** length fixed → maintain running aggregate; O(1) per slide.

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 643 | Maximum Average Subarray I | **Anchor** |
| **P1** | 2090 | K Radius Subarray Averages | Centered window; edges → −1 |
| P2 | 1456, 1343, 1176, 2269 | Same add/drop | |
| P3 | 3364, 3652 | Longer / strategy skin | |

```java
// P0 template — 643
public double findMaxAverage(int[] nums, int k) {
    long sum = 0;
    for (int i = 0; i < k; i++) sum += nums[i];
    long best = sum;
    for (int r = k; r < nums.length; r++) {
        sum += nums[r] - nums[r - k];
        best = Math.max(best, sum);
    }
    return best * 1.0 / k;
}
```

---

### B. Fixed window — distinct / frequency

**Principle:** fixed k + map; validity = distinct/freq constraint.

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 2461 | Max Sum Distinct Subarrays Length K | **Anchor** |
| P1 | 187 | Repeated DNA Sequences | Hash full window content |
| P2 | 1100, 1876, 1852, 2841 | Same skeleton | |
| P3 | 3318, 3321, 3672 | Top-x / mode structures | |

```java
// P0 template — 2461
public long maximumSubarraySum(int[] nums, int k) {
    Map<Integer, Integer> freq = new HashMap<>();
    long sum = 0, best = 0;
    for (int r = 0; r < nums.length; r++) {
        sum += nums[r];
        freq.merge(nums[r], 1, Integer::sum);
        if (r >= k) {
            int out = nums[r - k];
            sum -= out;
            int f = freq.get(out) - 1;
            if (f == 0) freq.remove(out); else freq.put(out, f);
        }
        if (r >= k - 1 && freq.size() == k) best = Math.max(best, sum);
    }
    return best;
}
```

---

### C. Fixed window — minimize cost in a block

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 2379 | Min Recolors for K Black | Min whites in length k |
| **P0** | 1151 | Min Swaps Group All 1s | **Distinct:** k = count(1) |
| P1 | 1984 | Min Diff of K Scores | Sort first, then fixed k (also M-ish) |
| P2 | 3191 | Length-3 flips greedy | Different op model |
| P3 | 3422, 3768 | Median / inversions | |

```java
// P0 — 2379
public int minimumRecolors(String blocks, int k) {
    int white = 0, best = Integer.MAX_VALUE;
    for (int r = 0; r < blocks.length(); r++) {
        if (blocks.charAt(r) == 'W') white++;
        if (r >= k && blocks.charAt(r - k) == 'W') white--;
        if (r >= k - 1) best = Math.min(best, white);
    }
    return best;
}

// P0 — 1151 idea: ones = count; window of length ones; minimize zeros.
```

---

### D. Order statistics (unique structure)

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 480 | Sliding Window Median | Two heaps / TreeMap — **unique priority** |
| P2 | 2653 | Sliding Subarray Beauty | Same “order stat in window” idea |

---

### E. Variable longest — uniqueness / ≤K distinct / freq caps

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 3 | Longest Without Repeating | Uniqueness |
| **P0** | 340 | At Most K Distinct | General K (904 = K=2 skin) |
| **P1** | 2958 | At Most K Frequency | Different constraint |
| **P1** | 1695 | Maximum Erasure Value | Max **sum** unique |
| P2 | 159, 904, 3090, 2743 | Skins of 3/340/2958 | |
| P3 | 2730, 3641, 3679, 3851, 2260 | Semi-repetitive / domain | |

```java
// P0 — at most K distinct (340 / 904)
public int lengthOfLongestSubstringKDistinct(String s, int k) {
    Map<Character, Integer> freq = new HashMap<>();
    int left = 0, best = 0;
    for (int right = 0; right < s.length(); right++) {
        freq.merge(s.charAt(right), 1, Integer::sum);
        while (freq.size() > k) {
            char c = s.charAt(left++);
            int f = freq.get(c) - 1;
            if (f == 0) freq.remove(c); else freq.put(c, f);
        }
        best = Math.max(best, right - left + 1);
    }
    return best;
}
```

---

### F. Variable longest — ≤K flips / replacements / deletes

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 1004 | Max Consecutive Ones III | zeros ≤ K |
| **P0** | 424 | Longest Repeating Char Replacement | len − maxFreq ≤ K (**distinct formula**) |
| **P1** | 1493 | Delete One Element | Exactly one delete |
| P2 | 487, 2024, 1156 | k=1 or T/F or one swap | |
| P3 | 2831 | Equal after ≤K deletes | |

```java
// P0 — 1004
public int longestOnes(int[] nums, int k) {
    int left = 0, zeros = 0, best = 0;
    for (int right = 0; right < nums.length; right++) {
        if (nums[right] == 0) zeros++;
        while (zeros > k) if (nums[left++] == 0) zeros--;
        best = Math.max(best, right - left + 1);
    }
    return best;
}
// 424: valid while (right - left + 1 - maxFreq) <= k
```

---

### G. Variable shortest once valid

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 209 | Minimum Size Subarray Sum | **Anchor** positives |
| **P1** | 1658 | Reduce X to Zero | Complement reformulation |
| **P1** | 1234 | Balanced String | Min excess-cover window |
| P2 | 1208, 2516, 2904 | Budget / ends / beautiful | |
| P3 | 2875, 3095, 3097, 3795 | Infinite / OR / distinct sum | |

```java
// P0 — 209
public int minSubArrayLen(int target, int[] nums) {
    int left = 0, sum = 0, best = Integer.MAX_VALUE;
    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];
        while (sum >= target) {
            best = Math.min(best, right - left + 1);
            sum -= nums[left++];
        }
    }
    return best == Integer.MAX_VALUE ? 0 : best;
}
```

---

### H. Covering / anagram / multiset

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 76 | Minimum Window Substring | Need-map + missing |
| **P0** | 567 | Permutation in String | Fixed anagram (438 twin) |
| **P1** | 30 | Concatenation of All Words | Word-level cover |
| **P1** | 1358 | Substrings With All Three Chars | Cover → count |
| P2 | 438, 3297, 3298, 3305, 3306 | Same ideas | |
| P3 | 727, 1297, 2067, 2781, 2953 | Subsequence / forbidden / complete | |

```java
// P0 — 76 (core covering)
public String minWindow(String s, String t) {
    int[] need = new int[128];
    for (char c : t.toCharArray()) need[c]++;
    int missing = t.length(), left = 0, bestL = 0, bestLen = Integer.MAX_VALUE;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (need[c] > 0) missing--;
        need[c]--;
        while (missing == 0) {
            if (right - left + 1 < bestLen) {
                bestLen = right - left + 1;
                bestL = left;
            }
            char out = s.charAt(left++);
            need[out]++;
            if (need[out] > 0) missing++;
        }
    }
    return bestLen == Integer.MAX_VALUE ? "" : s.substring(bestL, bestL + bestLen);
}
```

---

### I. Counting — at-most / exactly-K family

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 992 | K Different Integers | **The** atMost trick |
| **P0** | 713 | Product Less Than K | Count skeleton without “exactly” |
| **P1** | 1248 | Nice Subarrays | Odds → exactly K |
| **P1** | 2962 | Max Appears ≥ K Times | ≥ K counting |
| **P1** | 2444 | Fixed Bounds | Unique last-seen technique |
| P2 | 930, 2799, 2537, 3258, 3325 | atMost skins | |
| P3 | 2302, 3261, 3329, 3859, 3969, 3972 | Harder / query / niche | |

```java
// P0 — 992
public int subarraysWithKDistinct(int[] nums, int k) {
    return atMost(nums, k) - atMost(nums, k - 1);
}
private int atMost(int[] nums, int k) {
    if (k < 0) return 0;
    Map<Integer, Integer> freq = new HashMap<>();
    int left = 0, ans = 0;
    for (int right = 0; right < nums.length; right++) {
        freq.merge(nums[right], 1, Integer::sum);
        while (freq.size() > k) {
            int out = nums[left++];
            int f = freq.get(out) - 1;
            if (f == 0) freq.remove(out); else freq.put(out, f);
        }
        ans += right - left + 1;
    }
    return ans;
}
```

---

### J. Monotonic deque

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 239 | Sliding Window Maximum | Deque max |
| **P0** | 1438 | Max−Min ≤ Limit | Two deques |
| **P0** | 862 | Shortest Sum ≥ K (negatives) | Prefix + mono deque |
| P1 | 2762 | Continuous Subarrays | Count with max−min ≤ 2 |
| P2 | 1425, 1499, 2398 | DP/equation/budget + deque | |
| P3 | 3013, 3420, 3578 | Hard DP hybrids | |

```java
// P0 — 239
public int[] maxSlidingWindow(int[] nums, int k) {
    Deque<Integer> dq = new ArrayDeque<>();
    int n = nums.length;
    int[] res = new int[n - k + 1];
    for (int r = 0; r < n; r++) {
        while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[r]) dq.pollLast();
        dq.offerLast(r);
        if (dq.peekFirst() <= r - k) dq.pollFirst();
        if (r >= k - 1) res[r - k + 1] = nums[dq.peekFirst()];
    }
    return res;
}
```

---

### K. Prefix / diff / reachability

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 1423 | Max Points from Cards | Prefix ↔ suffix trade |
| **P0** | 995 | K Consecutive Bit Flips | Diff / flip queue |
| **P1** | 1871 | Jump Game VII | Sliding reachable range |
| P2 | 1031, 1477, 837 | Two windows / DP window sum | |
| P3 | 2106, 3413, 3439 | Geometry / gaps | |

---

### L. Binary search on answer + window

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 1838 | Frequency of Most Frequent | Sort + cost window (often no explicit bisect) |
| **P0** | 2009 | Make Array Continuous | Window of size n on sorted unique |
| **P1** | 1918 | Kth Smallest Subarray Sum | Bisect + count |
| **P1** | 658 | K Closest Elements | Bisect window edge |
| P2 | 2779* | Also under M; same sort+window | |
| P3 | 1044, 2528, 2968, 2981/2, 3134, 3346/7, 3634 | Hash / power / thrice / uniqueness | |

\*2779 is listed as M anchor; principle overlaps.

---

### M. Sort first, then window on values

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 2779 | Maximum Beauty After ±k | Sort + max−min ≤ 2k |
| **P0** | 1703 | K Consecutive Ones Swaps | Positions + median |
| **P1** | 1610 | Visible Points | Angles (unique domain) |
| P2 | 594, 1040, 2107, 2271, 2555, 2932 | Sort+window skins | |
| P3 | 1839, 2935, 3086, 3323, 3505 | State machine / trie / hard | |

```java
// P0 — 2779
public int maximumBeauty(int[] nums, int k) {
    Arrays.sort(nums);
    int left = 0, best = 0;
    for (int right = 0; right < nums.length; right++) {
        while (nums[right] - nums[left] > 2L * k) left++;
        best = Math.max(best, right - left + 1);
    }
    return best;
}
```

---

### N. Bitwise window

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 2401 | Longest Nice Subarray | Bit conflict shrink |
| P1 | 2411 | Smallest Subarrays Max OR | Bit last-seen / greedy |
| P2 | 3411 | Equal products GCD/LCM | |
| P3 | 3845 | XOR range hard | |

```java
// P0 — 2401
public int longestNiceSubarray(int[] nums) {
    int left = 0, used = 0, best = 0;
    for (int right = 0; right < nums.length; right++) {
        while ((used & nums[right]) != 0) used ^= nums[left++];
        used |= nums[right];
        best = Math.max(best, right - left + 1);
    }
    return best;
}
```

---

### O. Circular / wrap-around

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 2134 | Group All 1s Together II | Double array + cap n |
| P2 | 1652, 3206, 3208, 1888 | Circular sum / alternating | |

One circular P0 is enough; rest are story changes.

---

### P. Multi-window / M non-overlapping

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 689 | Max Sum of 3 Non-Overlapping | **The** template |
| P3 | 3956, 3957 | Generalize to M | |

---

### Q. Rolling hash / stream / string search

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P1** | 1044 | Longest Duplicate Substring | Bisect length + rolling hash (also L) |
| P2 | 2156 | Given hash value | |
| P3 | 3023, 3037, 3135, 3694 | Stream KMP / niche | |

Treat Q as **optional depth** after core windows. Not required for most interviews.

---

### R. Near-duplicate windows

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 219 | Contains Duplicate II | Last index / set of k |
| **P0** | 220 | Contains Duplicate III | **Buckets** — unique priority |

Both are short; both are distinct. Do both.

---

### S. Run-length / pattern structure

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 978 | Longest Turbulent Subarray | Local pattern state |
| **P0** | 395 | At Least K Repeating | D&C / split — **not** plain SW |
| P1 | 413 | Arithmetic Slices | Count runs |
| P2 | 2110, 2760, 3254, 3255 | Descent / power windows | |
| P3 | 3445, 3589 | Hard frequency / primes | |

---

### T. Special domain

| Pri | # | Problem | Note |
|:---:|---:|---|---|
| **P0** | 632 | Smallest Range from K Lists | Heap multi-pointer |
| **P0** | 1052 | Grumpy Bookstore Owner | Base + best fixed bonus |
| P2 | 718, 1016, 1763, 2747 | DP/hash/time skins | |
| P3 | 683, 1956, 2524, 2902 | Rare hard domain | |

---

## Priority counts (this file)

| Priority | Role | Count in master table |
|:---:|---|---:|
| **P0** | Unique principles (anchors) | **32** |
| **P1** | Essential twists | **14** |
| **P0 + P1** | Interview-ready path | **46** |
| **Starter set** | First-pass core only | **20** |
| **P2 + P3** | Skip unless drilling / contest | **~123** |
| | Source total | **169** |

**46 targeted solves cover the principles of 169.** The starter **20** is enough to open most interviews; finish P0+P1 for full coverage.

---

## Suggested progression (weeks)

| Week | Focus | Problems |
|---:|---|---|
| 1 | Fixed + variable core | 643, 3, 340, 2958, 1695, 1004, 424, 1493, 209, 1658 |
| 2 | Cover + count | 76, 567, 30, 1358, 992, 713, 1248, 2962, 2444, 2461 |
| 3 | Cost blocks + deque | 2379, 1151, 239, 1438, 862, 1423, 995 |
| 4 | Sort/bisect/circular/multi | 1838, 2009, 1918, 2779, 1703, 2134, 689, 2401 |
| 5 | Short unique structures | 219, 220, 978, 395, 480, 632, 1052, 1610, 1871, 1234 |

After week 5: only open P2 when a mock fails a skin of a principle you already “know.”

---

## Decision rule when you see a new SW problem

```
1. Is window length fixed?
     yes → fixed sum/freq/cost (A/B/C) or deque max (J/239)
     no  → variable
2. Optimize length max or min?
     max + constraint  → E/F (what is the “budget”?)
     min once valid    → G/H (sum? cover? complement?)
3. Count subarrays?
     → I: try atMost / exactly / last-seen bounds
4. Need max/min inside window or negatives in sum?
     → J mono deque / 862
5. Contiguity is on values after sort?
     → M / L cost window
6. Circular? multi segments? k lists?
     → O / P / T
```

If the problem matches a row in the **Coverage map**, solve the **P0** (or P1 if twist matches) and move on — do not collect every number.

---

## Relation to the full catalog

| File | Use when |
|---|---|
| **This file** (`sliding-window-prioritized-categorized.md`) | Planning what to solve; interviews; limited time |
| [sliding-window-categorized.md](./sliding-window-categorized.md) | Looking up *any* of the 169, full tables, every one-liner |

Templates here are the same master shapes as the full catalog; only **priority + coverage** are new.

---

*Prioritization heuristic: unique principle → P0; non-obvious reformulation → P1; reskin → P2; rare domain → P3. Re-rank a problem up if it appears often in your target company set.*
