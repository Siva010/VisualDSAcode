# Sliding Window — Full Pattern Catalog (Java)

> All **169** LeetCode problems tagged [Sliding Window](https://leetcode.com/problem-list/sliding-window/), grouped by **shared solving method**, with **Java templates** per category.

**Short on time?** Use the prioritized companion instead of grinding all 169:  
→ [sliding-window-prioritized-categorized.md](./sliding-window-prioritized-categorized.md) (**~20 / ~46** high-ROI problems + coverage map).

Use this full catalog as a lookup of every problem; use the prioritized file as the study plan.

---

## How to read this

| Field | Meaning |
|---|---|
| **Idea** | What the category is asking conceptually |
| **Method** | The shared technique / data structure |
| **Java template** | Copy-pasteable skeleton (adapt names/constraints) |
| **One-liner** | Vague idea of that specific problem |

Snippets are **pattern templates**, not full accepted submissions for every edge case. Each problem is listed once under its **primary** method.

---

## Category map (quick index)

| ID | Category | Count | Core move |
|:---:|---|---:|---|
| [A](#a-fixed-window-sum-average-count) | Fixed window — sum / average / count | 8 | `fixed-sum` |
| [B](#b-fixed-window-distinct-frequency-constraints) | Fixed window — distinct / frequency constraints | 9 | `fixed-freq` |
| [C](#c-fixed-window-minimize-cost-inside-a-block) | Fixed window — minimize cost inside a block | 6 | `fixed-cost` |
| [D](#d-fixed-window-order-statistics-median-k-th-beauty) | Fixed window — order statistics (median / k-th / beauty) | 2 | `fixed-order-stat` |
| [E](#e-variable-window-longest-with-uniqueness-at-most-k-distinct-freq-caps) | Variable window — longest with uniqueness / at-most-K distinct / freq caps | 13 | `var-longest-valid` |
| [F](#f-variable-window-longest-with-k-flips-replacements-deletes) | Variable window — longest with ≤ K flips / replacements / deletes | 7 | `var-budget-k` |
| [G](#g-variable-window-minimum-length-once-valid) | Variable window — minimum length once valid | 10 | `var-shortest-valid` |
| [H](#h-covering-window-multiset-anagram-match) | Covering window & multiset / anagram match | 14 | `cover-need-map` |
| [I](#i-counting-subarrays-at-most-exactly-k-family) | Counting subarrays — at-most / exactly-K family | 16 | `count-at-most` |
| [J](#j-monotonic-deque-window-max-min-max-min-constraints) | Monotonic deque — window max/min & max−min constraints | 10 | `mono-deque` |
| [K](#k-prefix-sums-difference-arrays-reachability-windows) | Prefix sums, difference arrays & reachability windows | 9 | `prefix-window` |
| [L](#l-binary-search-on-answer-window-feasibility) | Binary search on answer + window feasibility | 14 | `bisect-window` |
| [M](#m-sort-first-then-sliding-window-on-values) | Sort first, then sliding window on values | 13 | `sort-then-window` |
| [N](#n-bitwise-sliding-window-and-or-xor) | Bitwise sliding window (AND / OR / XOR) | 4 | `bitwise-window` |
| [O](#o-circular-wrap-around-windows) | Circular / wrap-around windows | 5 | `circular` |
| [P](#p-multi-window-m-non-overlapping-subarrays) | Multi-window / M non-overlapping subarrays | 3 | `multi-window-dp` |
| [Q](#q-rolling-hash-stream-pattern-string-search-windows) | Rolling hash, stream pattern & string search windows | 5 | `hash-kmp` |
| [R](#r-near-duplicate-value-close-windows-maps-buckets) | Near-duplicate / value-close windows (maps, buckets) | 2 | `nearby-duplicate` |
| [S](#s-run-length-pattern-structure-turbulent-arithmetic-descent) | Run-length / pattern structure (turbulent, arithmetic, descent) | 9 | `run-pattern` |
| [T](#t-special-domain-multi-list-merge-geometry-time-inventory) | Special domain — multi-list merge, geometry, time, inventory | 10 | `special-domain` |
| | **Total** | **169** | |

---

## Master Java templates

### 1. Fixed window
```java
// Example shape: max average of length-k subarray (LC 643)
public double findMaxAverage(int[] nums, int k) {
    long sum = 0;
    for (int i = 0; i < k; i++) sum += nums[i];
    long best = sum;
    for (int r = k; r < nums.length; r++) {
        sum += nums[r] - nums[r - k];   // add right, drop left
        best = Math.max(best, sum);
    }
    return best * 1.0 / k;
}
```

### 2. Variable window — longest valid
```java
// Example shape: longest substring with ≤ K distinct (LC 340 / 3 / 904)
public int lengthOfLongestSubstringKDistinct(String s, int k) {
    Map<Character, Integer> freq = new HashMap<>();
    int left = 0, best = 0;
    for (int right = 0; right < s.length(); right++) {
        freq.merge(s.charAt(right), 1, Integer::sum);
        while (freq.size() > k) {                 // INVALID → shrink
            char c = s.charAt(left++);
            int f = freq.get(c) - 1;
            if (f == 0) freq.remove(c); else freq.put(c, f);
        }
        best = Math.max(best, right - left + 1);  // window is valid
    }
    return best;
}

// Unique-only (LC 3): use int[128] lastIndex or Map, shrink while duplicate.
// Freq cap (LC 2958): shrink while any value's count > k.
```

### 3. Variable window — shortest valid
```java
// Example shape: shortest subarray with sum ≥ target (LC 209, positives)
public int minSubArrayLen(int target, int[] nums) {
    int left = 0, sum = 0, best = Integer.MAX_VALUE;
    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];
        while (sum >= target) {                   // VALID → try shrink
            best = Math.min(best, right - left + 1);
            sum -= nums[left++];
        }
    }
    return best == Integer.MAX_VALUE ? 0 : best;
}

// Covering variant: shrink only while still covering need-map (see H).
```

### 4. Count subarrays with at-most / exactly-K
```java
// Example shape: subarrays with exactly K distinct (LC 992)
// exactly(K) = atMost(K) - atMost(K - 1)
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
        ans += right - left + 1; // all subarrays ending at right are valid
    }
    return ans;
}

// Product < k (LC 713): same skeleton with long prod, shrink while prod >= k.
// Exactly K odds (LC 1248): treat odd as 1, even as 0, then atMost sum.
```

### 5. Monotonic deque (window max)
```java
// Example shape: sliding window maximum (LC 239)
public int[] maxSlidingWindow(int[] nums, int k) {
    Deque<Integer> dq = new ArrayDeque<>(); // indices, values decreasing
    int n = nums.length;
    int[] res = new int[n - k + 1];
    for (int r = 0; r < n; r++) {
        while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[r]) dq.pollLast();
        dq.offerLast(r);
        if (dq.peekFirst() <= r - k) dq.pollFirst(); // out of window
        if (r >= k - 1) res[r - k + 1] = nums[dq.peekFirst()];
    }
    return res;
}

// Max - min ≤ limit (LC 1438): two deques (max & min), shrink left when invalid.
// Prefix sum + mono deque for sum ≥ K with negatives (LC 862):
//   maintain increasing prefix deque; front is best left for nums[r].
```

### 6. Covering / need-map (min window substring)
```java
// Example shape: minimum window substring (LC 76) + anagram (LC 438/567)
public String minWindow(String s, String t) {
    int[] need = new int[128];
    for (char c : t.toCharArray()) need[c]++;
    int missing = t.length();                     // how many still needed
    int left = 0, bestL = 0, bestLen = Integer.MAX_VALUE;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (need[c] > 0) missing--;               // consumed a needed char
        need[c]--;                                // can go negative = extra
        while (missing == 0) {                    // fully covers
            if (right - left + 1 < bestLen) {
                bestLen = right - left + 1;
                bestL = left;
            }
            char out = s.charAt(left++);
            need[out]++;
            if (need[out] > 0) missing++;         // broke coverage
        }
    }
    return bestLen == Integer.MAX_VALUE ? "" : s.substring(bestL, bestL + bestLen);
}

// Fixed anagram window (LC 567): window size = p.length(), compare freq arrays.
```

---

## Categories

### A. Fixed window — sum / average / count

**8 problems** · 🟢 4 · 🟡 4 · 🔴 0 · template: `fixed-sum`

- **Idea:** Window length is fixed (`k` or `2k+1`). Maintain a running aggregate (sum, vowel count, score). Slide: add right, drop left.
- **Method:** O(n) single pass; only update the delta when the window moves by 1.

#### Java template

```java
// Example shape: max average of length-k subarray (LC 643)
public double findMaxAverage(int[] nums, int k) {
    long sum = 0;
    for (int i = 0; i < k; i++) sum += nums[i];
    long best = sum;
    for (int r = k; r < nums.length; r++) {
        sum += nums[r] - nums[r - k];   // add right, drop left
        best = Math.max(best, sum);
    }
    return best * 1.0 / k;
}
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 643 | [643. Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/) | 🟢 Easy | 48.4% | Max average of any length-k subarray |
| 1176 | [1176. Diet Plan Performance](https://leetcode.com/problems/diet-plan-performance/) | 🟢 Easy | 56.1% | Score of each length-k diet window vs thresholds |
| 1343 | [1343. Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold](https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/) | 🟡 Medium | 73.4% | Count length-k windows with average ≥ threshold |
| 1456 | [1456. Maximum Number of Vowels in a Substring of Given Length](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | 🟡 Medium | 62.3% | Max vowel count in any length-k substring |
| 2090 | [2090. K Radius Subarray Averages](https://leetcode.com/problems/k-radius-subarray-averages/) | 🟡 Medium | 46.4% | Average of each centered window of radius k |
| 2269 | [2269. Find the K-Beauty of a Number](https://leetcode.com/problems/find-the-k-beauty-of-a-number/) | 🟢 Easy | 63.6% | Count k-digit windows of a number divisible by k |
| 3364 | [3364. Minimum Positive Sum Subarray](https://leetcode.com/problems/minimum-positive-sum-subarray/) | 🟢 Easy | 45.3% | Min positive sum among subarrays with length in [l, r] |
| 3652 | [3652. Best Time to Buy and Sell Stock using Strategy](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-using-strategy/) | 🟡 Medium | 59.7% | Max profit using a fixed-length strategy window on prices |

### B. Fixed window — distinct / frequency constraints

**9 problems** · 🟢 2 · 🟡 6 · 🔴 1 · template: `fixed-freq`

- **Idea:** Fixed length `k`, but validity depends on how many unique values or frequencies appear inside the window (map + counter).
- **Method:** Freq map in O(1) amortized; track distinct count as keys hit 0/1.

#### Java template

```java
// Example shape: max sum of length-k subarray with all distinct (LC 2461)
public long maximumSubarraySum(int[] nums, int k) {
    Map<Integer, Integer> freq = new HashMap<>();
    long sum = 0, best = 0;
    for (int r = 0; r < nums.length; r++) {
        sum += nums[r];
        freq.merge(nums[r], 1, Integer::sum);
        if (r >= k) {                         // shrink to size k
            int out = nums[r - k];
            sum -= out;
            int f = freq.get(out) - 1;
            if (f == 0) freq.remove(out); else freq.put(out, f);
        }
        if (r >= k - 1 && freq.size() == k) { // all distinct
            best = Math.max(best, sum);
        }
    }
    return best;
}
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 187 | [187. Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/) | 🟡 Medium | 53.6% | Find 10-length DNA strings that appear more than once |
| 1100 | [1100. Find K-Length Substrings With No Repeated Characters](https://leetcode.com/problems/find-k-length-substrings-with-no-repeated-characters/) | 🟡 Medium | 76.5% | Count length-k substrings with all unique chars |
| 1852 | [1852. Distinct Numbers in Each Subarray](https://leetcode.com/problems/distinct-numbers-in-each-subarray/) | 🟡 Medium | 77.4% | Distinct count for every length-k subarray |
| 1876 | [1876. Substrings of Size Three with Distinct Characters](https://leetcode.com/problems/substrings-of-size-three-with-distinct-characters/) | 🟢 Easy | 76.9% | Count length-3 substrings with 3 distinct chars |
| 2461 | [2461. Maximum Sum of Distinct Subarrays With Length K](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/) | 🟡 Medium | 43.2% | Max sum of a length-k subarray of all distinct elements |
| 2841 | [2841. Maximum Sum of Almost Unique Subarray](https://leetcode.com/problems/maximum-sum-of-almost-unique-subarray/) | 🟡 Medium | 41.4% | Max sum of length-k with at least m distinct |
| 3318 | [3318. Find X-Sum of All K-Long Subarrays I](https://leetcode.com/problems/find-x-sum-of-all-k-long-subarrays-i/) | 🟢 Easy | 76.1% | X-sum (top-x frequent) of every length-k window — small constraints |
| 3321 | [3321. Find X-Sum of All K-Long Subarrays II](https://leetcode.com/problems/find-x-sum-of-all-k-long-subarrays-ii/) | 🔴 Hard | 41.2% | Same as 3318 with large constraints (ordered multisets) |
| 3672 | [3672. Sum of Weighted Modes in Subarrays](https://leetcode.com/problems/sum-of-weighted-modes-in-subarrays/) | 🟡 Medium | 53.4% | Sum of weighted modes over all length-k windows |

### C. Fixed window — minimize cost inside a block

**6 problems** · 🟢 2 · 🟡 3 · 🔴 1 · template: `fixed-cost`

- **Idea:** You must cover / form a contiguous block of known size (e.g. all ones packed, k blacks, k scores). Slide a window of that size and minimize zeros / whites / range / swaps.
- **Method:** Window size = target block size; minimize #bad cells or max−min.

#### Java template

```java
// Example shape: min whites in any length-k block of blacks (LC 2379)
public int minimumRecolors(String blocks, int k) {
    int white = 0, best = Integer.MAX_VALUE;
    for (int r = 0; r < blocks.length(); r++) {
        if (blocks.charAt(r) == 'W') white++;
        if (r >= k && blocks.charAt(r - k) == 'W') white--;
        if (r >= k - 1) best = Math.min(best, white); // min "bad" cells
    }
    return best;
}

// Variant: group all 1s — window size = count of 1s, minimize zeros (LC 1151)
// int ones = count(nums, 1);  then fixed window of length `ones`, min zeros.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 1151 | [1151. Minimum Swaps to Group All 1's Together](https://leetcode.com/problems/minimum-swaps-to-group-all-1-s-together/) | 🟡 Medium | 61.3% | Min swaps to group all 1s → window size = count(1s), min zeros |
| 1984 | [1984. Minimum Difference Between Highest and Lowest of K Scores](https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/) | 🟢 Easy | 66.4% | Sort scores; min (max−min) over any k consecutive |
| 2379 | [2379. Minimum Recolors to Get K Consecutive Black Blocks](https://leetcode.com/problems/minimum-recolors-to-get-k-consecutive-black-blocks/) | 🟢 Easy | 68.8% | Min recolors so some length-k block is all black |
| 3191 | [3191. Minimum Operations to Make Binary Array Elements Equal to One I](https://leetcode.com/problems/minimum-operations-to-make-binary-array-elements-equal-to-one-i/) | 🟡 Medium | 80.4% | Flip every 0 using fixed length-3 flip operations |
| 3422 | [3422. Minimum Operations to Make Subarray Elements Equal](https://leetcode.com/problems/minimum-operations-to-make-subarray-elements-equal/) | 🟡 Medium | 46.4% | Min ops to make all elements in a window equal (median) |
| 3768 | [3768. Minimum Inversion Count in Subarrays of Fixed Length](https://leetcode.com/problems/minimum-inversion-count-in-subarrays-of-fixed-length/) | 🔴 Hard | 42.8% | Min inversion count among all fixed-length subarrays |

### D. Fixed window — order statistics (median / k-th / beauty)

**2 problems** · 🟢 0 · 🟡 1 · 🔴 1 · template: `fixed-order-stat`

- **Idea:** Need median, k-th smallest, or other order statistic in every window. Plain sum is not enough — use two heaps, policy multiset, or fenwick.
- **Method:** Balanced structure for add/remove + query order statistic in log k.

#### Java template

```java
// Example shape: sliding window median with two multisets (LC 480 idea)
// small = max-side (negated values), large = min-side; keep sizes balanced.
// For interviews, TreeMap as multiset works:

class WindowMedian {
    TreeMap<Integer, Integer> low = new TreeMap<>();  // max half
    TreeMap<Integer, Integer> high = new TreeMap<>(); // min half
    int lowSize = 0, highSize = 0;

    void add(int x) {
        if (lowSize == 0 || x <= low.lastKey()) { inc(low, x); lowSize++; }
        else { inc(high, x); highSize++; }
        rebalance();
    }
    void remove(int x) {
        if (low.containsKey(x)) { dec(low, x); lowSize--; }
        else { dec(high, x); highSize--; }
        rebalance();
    }
    double median() {
        if (lowSize > highSize) return low.lastKey();
        return ((double) low.lastKey() + high.firstKey()) / 2.0;
    }
    void rebalance() {
        while (lowSize > highSize + 1) {
            int x = low.lastKey(); dec(low, x); lowSize--;
            inc(high, x); highSize++;
        }
        while (highSize > lowSize) {
            int x = high.firstKey(); dec(high, x); highSize--;
            inc(low, x); lowSize++;
        }
    }
    static void inc(TreeMap<Integer, Integer> m, int x) {
        m.merge(x, 1, Integer::sum);
    }
    static void dec(TreeMap<Integer, Integer> m, int x) {
        int f = m.get(x) - 1;
        if (f == 0) m.remove(x); else m.put(x, f);
    }
}
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 480 | [480. Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) | 🔴 Hard | 39.0% | Median of every length-k window (two heaps / multiset) |
| 2653 | [2653. Sliding Subarray Beauty](https://leetcode.com/problems/sliding-subarray-beauty/) | 🟡 Medium | 37.4% | x-th smallest negative ('beauty') in every length-k window |

### E. Variable window — longest with uniqueness / at-most-K distinct / freq caps

**13 problems** · 🟢 1 · 🟡 12 · 🔴 0 · template: `var-longest-valid`

- **Idea:** Expand `right`. When the constraint breaks (duplicate, >K distinct, freq > limit), shrink `left` until valid. Track max length (or max sum).
- **Method:** Classic two-pointer + hashmap. Monotonic validity if elements ≥ 0 / constraint is 'at most'.

#### Java template

```java
// Example shape: longest substring with ≤ K distinct (LC 340 / 3 / 904)
public int lengthOfLongestSubstringKDistinct(String s, int k) {
    Map<Character, Integer> freq = new HashMap<>();
    int left = 0, best = 0;
    for (int right = 0; right < s.length(); right++) {
        freq.merge(s.charAt(right), 1, Integer::sum);
        while (freq.size() > k) {                 // INVALID → shrink
            char c = s.charAt(left++);
            int f = freq.get(c) - 1;
            if (f == 0) freq.remove(c); else freq.put(c, f);
        }
        best = Math.max(best, right - left + 1);  // window is valid
    }
    return best;
}

// Unique-only (LC 3): use int[128] lastIndex or Map, shrink while duplicate.
// Freq cap (LC 2958): shrink while any value's count > k.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 3 | [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | 🟡 Medium | 39.4% | Longest substring with all unique characters |
| 159 | [159. Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/) | 🟡 Medium | 57.1% | Longest substring with ≤ 2 distinct characters |
| 340 | [340. Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) | 🟡 Medium | 50.1% | Longest substring with ≤ K distinct characters |
| 904 | [904. Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) | 🟡 Medium | 51.7% | Fruit Into Baskets = longest subarray with ≤ 2 distinct |
| 1695 | [1695. Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/) | 🟡 Medium | 64.4% | Max sum of a subarray with all unique elements |
| 2260 | [2260. Minimum Consecutive Cards to Pick Up](https://leetcode.com/problems/minimum-consecutive-cards-to-pick-up/) | 🟡 Medium | 53.9% | Min length to pick until a duplicate card appears |
| 2730 | [2730. Find the Longest Semi-Repetitive Substring](https://leetcode.com/problems/find-the-longest-semi-repetitive-substring/) | 🟡 Medium | 39.1% | Longest semi-repetitive (≤ 1 adjacent equal pair) |
| 2743 | [2743. Count Substrings Without Repeating Character](https://leetcode.com/problems/count-substrings-without-repeating-character/) | 🟡 Medium | 75.9% | Count substrings with all unique characters |
| 2958 | [2958. Length of Longest Subarray With at Most K Frequency](https://leetcode.com/problems/length-of-longest-subarray-with-at-most-k-frequency/) | 🟡 Medium | 56.7% | Longest subarray where every value appears ≤ K times |
| 3090 | [3090. Maximum Length Substring With Two Occurrences](https://leetcode.com/problems/maximum-length-substring-with-two-occurrences/) | 🟢 Easy | 65.8% | Longest substring where each char appears ≤ 2 times |
| 3641 | [3641. Longest Semi-Repeating Subarray](https://leetcode.com/problems/longest-semi-repeating-subarray/) | 🟡 Medium | 63.5% | Longest semi-repeating subarray (≤ 1 value with freq > 1) |
| 3679 | [3679. Minimum Discards to Balance Inventory](https://leetcode.com/problems/minimum-discards-to-balance-inventory/) | 🟡 Medium | 35.4% | Min discards so inventory never exceeds window limits |
| 3851 | [3851. Maximum Requests Without Violating the Limit](https://leetcode.com/problems/maximum-requests-without-violating-the-limit/) | 🟡 Medium | 64.2% | Max requests in a window without violating rate/limit |

### F. Variable window — longest with ≤ K flips / replacements / deletes

**7 problems** · 🟢 0 · 🟡 7 · 🔴 0 · template: `var-budget-k`

- **Idea:** Same expand/shrink skeleton, but the 'budget' is how many bad cells you may fix (flip 0→1, replace char, delete one element).
- **Method:** Valid while `window_len - best_freq ≤ K` (or zeros ≤ K). Shrink when budget exceeded.

#### Java template

```java
// Example shape: max consecutive 1s with ≤ k flips (LC 1004 / 424 / 2024)
public int longestOnes(int[] nums, int k) {
    int left = 0, zeros = 0, best = 0;
    for (int right = 0; right < nums.length; right++) {
        if (nums[right] == 0) zeros++;
        while (zeros > k) {                       // budget exceeded
            if (nums[left++] == 0) zeros--;
        }
        best = Math.max(best, right - left + 1);
    }
    return best;
}

// Char replacement (LC 424): valid while (windowLen - maxFreq) <= k
// int[] cnt = new int[26]; track maxFreq of any char in window.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 424 | [424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) | 🟡 Medium | 60.1% | Longest repeating char if you may replace ≤ K chars |
| 487 | [487. Max Consecutive Ones II](https://leetcode.com/problems/max-consecutive-ones-ii/) | 🟡 Medium | 52.1% | Max consecutive 1s with at most one 0 flip |
| 1004 | [1004. Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/) | 🟡 Medium | 68.0% | Max consecutive 1s with ≤ K zero flips |
| 1156 | [1156. Swap For Longest Repeated Character Substring](https://leetcode.com/problems/swap-for-longest-repeated-character-substring/) | 🟡 Medium | 44.7% | Longest repeated char substring allowing one swap |
| 1493 | [1493. Longest Subarray of 1's After Deleting One Element](https://leetcode.com/problems/longest-subarray-of-1-s-after-deleting-one-element/) | 🟡 Medium | 71.3% | Longest 1s after deleting exactly one element |
| 2024 | [2024. Maximize the Confusion of an Exam](https://leetcode.com/problems/maximize-the-confusion-of-an-exam/) | 🟡 Medium | 70.2% | Maximize exam confusion: longest T/F with ≤ K changes |
| 2831 | [2831. Find the Longest Equal Subarray](https://leetcode.com/problems/find-the-longest-equal-subarray/) | 🟡 Medium | 38.4% | Longest equal subarray after ≤ K deletions |

### G. Variable window — minimum length once valid

**10 problems** · 🟢 1 · 🟡 9 · 🔴 0 · template: `var-shortest-valid`

- **Idea:** Expand until the window satisfies a goal (sum ≥ target, covers need, has ≥ K ones, …). Then shrink from the left to minimize length while still valid.
- **Method:** Track min length whenever valid. Needs monotonicity (usually positives).

#### Java template

```java
// Example shape: shortest subarray with sum ≥ target (LC 209, positives)
public int minSubArrayLen(int target, int[] nums) {
    int left = 0, sum = 0, best = Integer.MAX_VALUE;
    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];
        while (sum >= target) {                   // VALID → try shrink
            best = Math.min(best, right - left + 1);
            sum -= nums[left++];
        }
    }
    return best == Integer.MAX_VALUE ? 0 : best;
}

// Covering variant: shrink only while still covering need-map (see H).
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 209 | [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) | 🟡 Medium | 52.2% | Shortest subarray with sum ≥ target (positives) |
| 1208 | [1208. Get Equal Substrings Within Budget](https://leetcode.com/problems/get-equal-substrings-within-budget/) | 🟡 Medium | 59.9% | Longest equal-cost substring within budget (max under cost) |
| 1234 | [1234. Replace the Substring for Balanced String](https://leetcode.com/problems/replace-the-substring-for-balanced-string/) | 🟡 Medium | 41.1% | Min window to replace so string becomes balanced |
| 1658 | [1658. Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/) | 🟡 Medium | 40.7% | Min ops to reduce X to 0 ↔ longest subarray sum = total−X |
| 2516 | [2516. Take K of Each Character From Left and Right](https://leetcode.com/problems/take-k-of-each-character-from-left-and-right/) | 🟡 Medium | 51.5% | Take K of each char from ends ↔ min middle to discard |
| 2875 | [2875. Minimum Size Subarray in Infinite Array](https://leetcode.com/problems/minimum-size-subarray-in-infinite-array/) | 🟡 Medium | 32.4% | Min subarray sum = target on infinite repetition of array |
| 2904 | [2904. Shortest and Lexicographically Smallest Beautiful String](https://leetcode.com/problems/shortest-and-lexicographically-smallest-beautiful-string/) | 🟡 Medium | 41.3% | Shortest (then lex-smallest) substring with ≥ K ones |
| 3095 | [3095. Shortest Subarray With OR at Least K I](https://leetcode.com/problems/shortest-subarray-with-or-at-least-k-i/) | 🟢 Easy | 44.8% | Shortest subarray with bitwise OR ≥ K (easy constraints) |
| 3097 | [3097. Shortest Subarray With OR at Least K II](https://leetcode.com/problems/shortest-subarray-with-or-at-least-k-ii/) | 🟡 Medium | 50.2% | Same OR ≥ K with efficient bit tracking |
| 3795 | [3795. Minimum Subarray Length With Distinct Sum At Least K](https://leetcode.com/problems/minimum-subarray-length-with-distinct-sum-at-least-k/) | 🟡 Medium | 32.0% | Min length whose sum of distinct values ≥ K |

### H. Covering window & multiset / anagram match

**14 problems** · 🟢 0 · 🟡 8 · 🔴 6 · template: `cover-need-map`

- **Idea:** Maintain a need/have frequency map and a `formed` / `missing` counter. Used for 'cover all of t', 'is permutation of p', 'concat of words'.
- **Method:** Template from LC76: expand right, shrink left while still covering.

#### Java template

```java
// Example shape: minimum window substring (LC 76) + anagram (LC 438/567)
public String minWindow(String s, String t) {
    int[] need = new int[128];
    for (char c : t.toCharArray()) need[c]++;
    int missing = t.length();                     // how many still needed
    int left = 0, bestL = 0, bestLen = Integer.MAX_VALUE;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (need[c] > 0) missing--;               // consumed a needed char
        need[c]--;                                // can go negative = extra
        while (missing == 0) {                    // fully covers
            if (right - left + 1 < bestLen) {
                bestLen = right - left + 1;
                bestL = left;
            }
            char out = s.charAt(left++);
            need[out]++;
            if (need[out] > 0) missing++;         // broke coverage
        }
    }
    return bestLen == Integer.MAX_VALUE ? "" : s.substring(bestL, bestL + bestLen);
}

// Fixed anagram window (LC 567): window size = p.length(), compare freq arrays.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 30 | [30. Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/) | 🔴 Hard | 34.7% | Concatenation of all words (fixed total length + word map) |
| 76 | [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | 🔴 Hard | 47.9% | Minimum window substring covering all of t |
| 438 | [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | 🟡 Medium | 54.1% | All start indices of anagrams of p (fixed |p| window) |
| 567 | [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/) | 🟡 Medium | 49.2% | Whether s2 contains a permutation of s1 |
| 727 | [727. Minimum Window Subsequence](https://leetcode.com/problems/minimum-window-subsequence/) | 🔴 Hard | 43.8% | Minimum window subsequence (order-preserving cover; DP/two pointers) |
| 1297 | [1297. Maximum Number of Occurrences of a Substring](https://leetcode.com/problems/maximum-number-of-occurrences-of-a-substring/) | 🟡 Medium | 54.6% | Max occurrences of a substring with size/distinct limits |
| 1358 | [1358. Number of Substrings Containing All Three Characters](https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/) | 🟡 Medium | 74.4% | Count substrings that contain a, b, and c at least once |
| 2067 | [2067. Number of Equal Count Substrings](https://leetcode.com/problems/number-of-equal-count-substrings/) | 🟡 Medium | 45.6% | Count substrings where every present char has equal count |
| 2781 | [2781. Length of the Longest Valid Substring](https://leetcode.com/problems/length-of-the-longest-valid-substring/) | 🔴 Hard | 38.6% | Longest substring that avoids a list of forbidden words |
| 2953 | [2953. Count Complete Substrings](https://leetcode.com/problems/count-complete-substrings/) | 🔴 Hard | 30.9% | Count complete substrings (each char appears exactly k times) |
| 3297 | [3297. Count Substrings That Can Be Rearranged to Contain a String I](https://leetcode.com/problems/count-substrings-that-can-be-rearranged-to-contain-a-string-i/) | 🟡 Medium | 43.4% | Count substrings rearrangeable to contain word2 (cover freqs) |
| 3298 | [3298. Count Substrings That Can Be Rearranged to Contain a String II](https://leetcode.com/problems/count-substrings-that-can-be-rearranged-to-contain-a-string-ii/) | 🔴 Hard | 56.3% | Same as 3297, larger limits |
| 3305 | [3305. Count of Substrings Containing Every Vowel and K Consonants I](https://leetcode.com/problems/count-of-substrings-containing-every-vowel-and-k-consonants-i/) | 🟡 Medium | 42.1% | Count substrings with all vowels + exactly K consonants |
| 3306 | [3306. Count of Substrings Containing Every Vowel and K Consonants II](https://leetcode.com/problems/count-of-substrings-containing-every-vowel-and-k-consonants-ii/) | 🟡 Medium | 40.7% | Same as 3305, larger limits |

### I. Counting subarrays — at-most / exactly-K family

**16 problems** · 🟢 1 · 🟡 8 · 🔴 7 · template: `count-at-most`

- **Idea:** Often hard to count 'exactly K' directly. Compute `f(at most K) − f(at most K−1)`. Inner `f` is a variable window that counts all valid subarrays ending at `right`.
- **Method:** While expanding right, shrink left to keep constraint; add `(right−left+1)`.

#### Java template

```java
// Example shape: subarrays with exactly K distinct (LC 992)
// exactly(K) = atMost(K) - atMost(K - 1)
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
        ans += right - left + 1; // all subarrays ending at right are valid
    }
    return ans;
}

// Product < k (LC 713): same skeleton with long prod, shrink while prod >= k.
// Exactly K odds (LC 1248): treat odd as 1, even as 0, then atMost sum.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 713 | [713. Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/) | 🟡 Medium | 54.7% | Count subarrays with product < K |
| 930 | [930. Binary Subarrays With Sum](https://leetcode.com/problems/binary-subarrays-with-sum/) | 🟡 Medium | 69.3% | Count binary subarrays with sum = goal (at-most trick or prefix) |
| 992 | [992. Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) | 🔴 Hard | 68.6% | Subarrays with exactly K different integers |
| 1248 | [1248. Count Number of Nice Subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays/) | 🟡 Medium | 75.4% | Count subarrays with exactly K odd numbers |
| 2302 | [2302. Count Subarrays With Score Less Than K](https://leetcode.com/problems/count-subarrays-with-score-less-than-k/) | 🔴 Hard | 62.2% | Count subarrays with score (sum × length) < K |
| 2444 | [2444. Count Subarrays With Fixed Bounds](https://leetcode.com/problems/count-subarrays-with-fixed-bounds/) | 🔴 Hard | 69.2% | Count subarrays whose min/max are fixed bounds |
| 2537 | [2537. Count the Number of Good Subarrays](https://leetcode.com/problems/count-the-number-of-good-subarrays/) | 🟡 Medium | 65.7% | Count 'good' subarrays (pair count / condition on window) |
| 2799 | [2799. Count Complete Subarrays in an Array](https://leetcode.com/problems/count-complete-subarrays-in-an-array/) | 🟡 Medium | 76.0% | Count complete subarrays (contain all distinct of the array) |
| 2962 | [2962. Count Subarrays Where Max Element Appears at Least K Times](https://leetcode.com/problems/count-subarrays-where-max-element-appears-at-least-k-times/) | 🟡 Medium | 62.4% | Count subarrays where the global max appears ≥ K times |
| 3258 | [3258. Count Substrings That Satisfy K-Constraint I](https://leetcode.com/problems/count-substrings-that-satisfy-k-constraint-i/) | 🟢 Easy | 79.3% | Count substrings satisfying K-constraint on 0/1 counts |
| 3261 | [3261. Count Substrings That Satisfy K-Constraint II](https://leetcode.com/problems/count-substrings-that-satisfy-k-constraint-ii/) | 🔴 Hard | 23.9% | K-constraint counts with queries (harder) |
| 3325 | [3325. Count Substrings With K-Frequency Characters I](https://leetcode.com/problems/count-substrings-with-k-frequency-characters-i/) | 🟡 Medium | 56.2% | Count substrings where some char has frequency ≥ K |
| 3329 | [3329. Count Substrings With K-Frequency Characters II](https://leetcode.com/problems/count-substrings-with-k-frequency-characters-ii/) | 🔴 Hard | 70.2% | Same as 3325, larger limits |
| 3859 | [3859. Count Subarrays With K Distinct Integers](https://leetcode.com/problems/count-subarrays-with-k-distinct-integers/) | 🔴 Hard | 20.5% | Count subarrays with exactly K distinct integers |
| 3969 | [3969. Valid Subarrays With Matching Sum Digits I](https://leetcode.com/problems/valid-subarrays-with-matching-sum-digits-i/) | 🟡 Medium | 46.7% | Valid subarrays with matching sum-digits property (I) |
| 3972 | [3972. Valid Subarrays With Matching Sum Digits II](https://leetcode.com/problems/valid-subarrays-with-matching-sum-digits-ii/) | 🔴 Hard | 69.4% | Same property, harder constraints (II) |

### J. Monotonic deque — window max/min & max−min constraints

**10 problems** · 🟢 0 · 🟡 3 · 🔴 7 · template: `mono-deque`

- **Idea:** Deque stores indices in decreasing/increasing order so front is always max or min of the current window. Also used when validity depends on max−min ≤ limit.
- **Method:** Pop back while order breaks; pop front when out of window.

#### Java template

```java
// Example shape: sliding window maximum (LC 239)
public int[] maxSlidingWindow(int[] nums, int k) {
    Deque<Integer> dq = new ArrayDeque<>(); // indices, values decreasing
    int n = nums.length;
    int[] res = new int[n - k + 1];
    for (int r = 0; r < n; r++) {
        while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[r]) dq.pollLast();
        dq.offerLast(r);
        if (dq.peekFirst() <= r - k) dq.pollFirst(); // out of window
        if (r >= k - 1) res[r - k + 1] = nums[dq.peekFirst()];
    }
    return res;
}

// Max - min ≤ limit (LC 1438): two deques (max & min), shrink left when invalid.
// Prefix sum + mono deque for sum ≥ K with negatives (LC 862):
//   maintain increasing prefix deque; front is best left for nums[r].
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 239 | [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | 🔴 Hard | 49.0% | Max of every length-k window |
| 862 | [862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/) | 🔴 Hard | 32.9% | Shortest subarray sum ≥ K with negatives (prefix + mono deque) |
| 1425 | [1425. Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/) | 🔴 Hard | 56.5% | Constrained subsequence sum (DP + mono deque) |
| 1438 | [1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) | 🟡 Medium | 57.8% | Longest subarray with max−min ≤ limit |
| 1499 | [1499. Max Value of Equation](https://leetcode.com/problems/max-value-of-equation/) | 🔴 Hard | 45.1% | Max yi+yj+|xi−xj| under distance constraint |
| 2398 | [2398. Maximum Number of Robots Within Budget](https://leetcode.com/problems/maximum-number-of-robots-within-budget/) | 🔴 Hard | 38.8% | Max robots in a budget window (max charge via deque) |
| 2762 | [2762. Continuous Subarrays](https://leetcode.com/problems/continuous-subarrays/) | 🟡 Medium | 58.0% | Count continuous subarrays (max−min ≤ 2) |
| 3013 | [3013. Divide an Array Into Subarrays With Minimum Cost II](https://leetcode.com/problems/divide-an-array-into-subarrays-with-minimum-cost-ii/) | 🔴 Hard | 54.7% | Min cost to split using k-sized choice windows |
| 3420 | [3420. Count Non-Decreasing Subarrays After K Operations](https://leetcode.com/problems/count-non-decreasing-subarrays-after-k-operations/) | 🔴 Hard | 24.9% | Count non-decreasing subarrays after ≤ K ops |
| 3578 | [3578. Count Partitions With Max-Min Difference at Most K](https://leetcode.com/problems/count-partitions-with-max-min-difference-at-most-k/) | 🟡 Medium | 58.5% | Count partitions with max−min ≤ K (DP + mono queue) |

### K. Prefix sums, difference arrays & reachability windows

**9 problems** · 🟢 0 · 🟡 7 · 🔴 2 · template: `prefix-window`

- **Idea:** Convert the problem to prefix sums, then use two pointers / a queue of candidate left ends. Includes difference-array flip tracking and BFS reachability windows.
- **Method:** prefix[r]−prefix[l]; maintain candidates for l in a queue/deque.

#### Java template

```java
// Example shape: max points from cards (LC 1423) — prefix + suffix
public int maxScore(int[] cardPoints, int k) {
    int n = cardPoints.length, sum = 0;
    for (int i = 0; i < k; i++) sum += cardPoints[i]; // take first k
    int best = sum;
    for (int i = 0; i < k; i++) { // trade left cards for right cards
        sum -= cardPoints[k - 1 - i];
        sum += cardPoints[n - 1 - i];
        best = Math.max(best, sum);
    }
    return best;
}

// Diff-array flips (LC 995 idea):
// int flips = 0; queue or diff[i] tracks whether a flip starting earlier covers i.
// if ((nums[i] + flips) % 2 == 0) must start a new flip of length k at i.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 837 | [837. New 21 Game](https://leetcode.com/problems/new-21-game/) | 🟡 Medium | 51.9% | New 21 Game — DP optimized with sliding window sum |
| 995 | [995. Minimum Number of K Consecutive Bit Flips](https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips/) | 🔴 Hard | 62.4% | Min k-bit flips — queue of flip end indices / diff array |
| 1031 | [1031. Maximum Sum of Two Non-Overlapping Subarrays](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/) | 🟡 Medium | 61.0% | Max sum of two non-overlapping fixed-length subarrays |
| 1423 | [1423. Maximum Points You Can Obtain from Cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/) | 🟡 Medium | 58.0% | Max points from k cards = max of prefix+suffix of length k |
| 1477 | [1477. Find Two Non-overlapping Sub-arrays Each With Target Sum](https://leetcode.com/problems/find-two-non-overlapping-sub-arrays-each-with-target-sum/) | 🟡 Medium | 36.9% | Two non-overlapping subarrays each summing to target |
| 1871 | [1871. Jump Game VII](https://leetcode.com/problems/jump-game-vii/) | 🟡 Medium | 35.6% | Jump Game VII — reachable range slides forward |
| 2106 | [2106. Maximum Fruits Harvested After at Most K Steps](https://leetcode.com/problems/maximum-fruits-harvested-after-at-most-k-steps/) | 🔴 Hard | 61.0% | Max fruits with ≤ K steps (prefix on positions) |
| 3413 | [3413. Maximum Coins From K Consecutive Bags](https://leetcode.com/problems/maximum-coins-from-k-consecutive-bags/) | 🟡 Medium | 25.8% | Max coins from k consecutive bags (positions + window) |
| 3439 | [3439. Reschedule Meetings for Maximum Free Time I](https://leetcode.com/problems/reschedule-meetings-for-maximum-free-time-i/) | 🟡 Medium | 54.0% | Max free time by rescheduling k meetings (gap window) |

### L. Binary search on answer + window feasibility

**14 problems** · 🟢 0 · 🟡 8 · 🔴 6 · template: `bisect-window`

- **Idea:** Answer is monotonic in some size/cost/median. Binary search the answer; for each mid, a sliding window / two-pointer check decides feasibility or counts how many subarrays satisfy it.
- **Method:** lo/hi on answer; `check(mid)` is usually O(n) window or count.

#### Java template

```java
// Example shape: max frequency after ≤ k increments (LC 1838)
// Sort, then for each right binary-search / two-pointer left so cost <= k
public int maxFrequency(int[] nums, int k) {
    Arrays.sort(nums);
    long sum = 0;
    int left = 0, best = 1;
    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];
        // cost to make all nums[left..right] equal to nums[right]
        while ((long) nums[right] * (right - left + 1) - sum > k) {
            sum -= nums[left++];
        }
        best = Math.max(best, right - left + 1);
    }
    return best;
}

// Pure bisect pattern:
// int lo = 1, hi = n;
// while (lo < hi) { int mid = (lo+hi+1)/2; if (check(mid)) lo = mid; else hi = mid-1; }
// check(mid) usually runs a sliding window in O(n).
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 658 | [658. Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/) | 🟡 Medium | 49.9% | K closest elements — binsearch left edge of window of size k |
| 1044 | [1044. Longest Duplicate Substring](https://leetcode.com/problems/longest-duplicate-substring/) | 🔴 Hard | 31.3% | Longest duplicate substring — binsearch length + rolling hash |
| 1838 | [1838. Frequency of the Most Frequent Element](https://leetcode.com/problems/frequency-of-the-most-frequent-element/) | 🟡 Medium | 45.0% | Max frequency after ≤ K increments (sort + cost window) |
| 1918 | [1918. Kth Smallest Subarray Sum](https://leetcode.com/problems/kth-smallest-subarray-sum/) | 🟡 Medium | 53.4% | K-th smallest subarray sum — binsearch sum + count windows |
| 2009 | [2009. Minimum Number of Operations to Make Array Continuous](https://leetcode.com/problems/minimum-number-of-operations-to-make-array-continuous/) | 🔴 Hard | 51.8% | Min ops to make array continuous — window of size n on sorted unique |
| 2528 | [2528. Maximize the Minimum Powered City](https://leetcode.com/problems/maximize-the-minimum-powered-city/) | 🔴 Hard | 61.5% | Maximize min powered city — binsearch power + range add window |
| 2779 | [2779. Maximum Beauty of an Array After Applying Operation](https://leetcode.com/problems/maximum-beauty-of-an-array-after-applying-operation/) | 🟡 Medium | 58.5% | Max beauty after ±k — sort + longest window with max−min ≤ 2k |
| 2968 | [2968. Apply Operations to Maximize Frequency Score](https://leetcode.com/problems/apply-operations-to-maximize-frequency-score/) | 🔴 Hard | 39.4% | Max frequency score with ops — sort + cost window |
| 2981 | [2981. Find Longest Special Substring That Occurs Thrice I](https://leetcode.com/problems/find-longest-special-substring-that-occurs-thrice-i/) | 🟡 Medium | 61.8% | Longest special substring occurring thrice — binsearch length |
| 2982 | [2982. Find Longest Special Substring That Occurs Thrice II](https://leetcode.com/problems/find-longest-special-substring-that-occurs-thrice-ii/) | 🟡 Medium | 39.3% | Same as 2981 with larger constraints |
| 3134 | [3134. Find the Median of the Uniqueness Array](https://leetcode.com/problems/find-the-median-of-the-uniqueness-array/) | 🔴 Hard | 30.5% | Median of uniqueness array — binsearch distinct count + count windows |
| 3346 | [3346. Maximum Frequency of an Element After Performing Operations I](https://leetcode.com/problems/maximum-frequency-of-an-element-after-performing-operations-i/) | 🟡 Medium | 40.2% | Max frequency after range ops (I) |
| 3347 | [3347. Maximum Frequency of an Element After Performing Operations II](https://leetcode.com/problems/maximum-frequency-of-an-element-after-performing-operations-ii/) | 🔴 Hard | 53.9% | Max frequency after range ops (II) |
| 3634 | [3634. Minimum Removals to Balance Array](https://leetcode.com/problems/minimum-removals-to-balance-array/) | 🟡 Medium | 47.9% | Min removals to balance — longest window with max ≤ min·k |

### M. Sort first, then sliding window on values

**13 problems** · 🟢 2 · 🟡 6 · 🔴 5 · template: `sort-then-window`

- **Idea:** Contiguity is on the *sorted* order (values), not original indices. Sort, then a window of consecutive values optimizes range / coverage.
- **Method:** Sort O(n log n), then two pointers on the sorted array.

#### Java template

```java
// Example shape: max beauty after ±k ops (LC 2779) — sort + window
public int maximumBeauty(int[] nums, int k) {
    Arrays.sort(nums);
    int left = 0, best = 0;
    for (int right = 0; right < nums.length; right++) {
        while (nums[right] - nums[left] > 2L * k) left++; // cannot meet
        best = Math.max(best, right - left + 1);
    }
    return best;
}

// Ones positions + median window (LC 1703 idea):
// List<Integer> pos of all 1s; fixed window of k ones; cost = moves to median.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 594 | [594. Longest Harmonious Subsequence](https://leetcode.com/problems/longest-harmonious-subsequence/) | 🟢 Easy | 64.8% | Longest harmonious subsequence (values differ by 1) — map or sort+window |
| 1040 | [1040. Moving Stones Until Consecutive II](https://leetcode.com/problems/moving-stones-until-consecutive-ii/) | 🟡 Medium | 59.0% | Moving stones until consecutive II — sort + window |
| 1610 | [1610. Maximum Number of Visible Points](https://leetcode.com/problems/maximum-number-of-visible-points/) | 🔴 Hard | 38.3% | Max visible points — sort by angle + angular window |
| 1703 | [1703. Minimum Adjacent Swaps for K Consecutive Ones](https://leetcode.com/problems/minimum-adjacent-swaps-for-k-consecutive-ones/) | 🔴 Hard | 42.4% | Min swaps for k consecutive ones — ones' positions + median window |
| 1839 | [1839. Longest Substring Of All Vowels in Order](https://leetcode.com/problems/longest-substring-of-all-vowels-in-order/) | 🟡 Medium | 52.1% | Longest nice vowel substring in order — state machine / window |
| 2107 | [2107. Number of Unique Flavors After Sharing K Candies](https://leetcode.com/problems/number-of-unique-flavors-after-sharing-k-candies/) | 🟡 Medium | 62.3% | Max unique flavors after giving away k consecutive candies |
| 2271 | [2271. Maximum White Tiles Covered by a Carpet](https://leetcode.com/problems/maximum-white-tiles-covered-by-a-carpet/) | 🟡 Medium | 36.1% | Max white tiles a carpet can cover — sort tiles + window |
| 2555 | [2555. Maximize Win From Two Segments](https://leetcode.com/problems/maximize-win-from-two-segments/) | 🟡 Medium | 38.0% | Max prizes from two segments of length k |
| 2932 | [2932. Maximum Strong Pair XOR I](https://leetcode.com/problems/maximum-strong-pair-xor-i/) | 🟢 Easy | 76.3% | Max XOR of a strong pair (small) — sort + window |x−y|≤min |
| 2935 | [2935. Maximum Strong Pair XOR II](https://leetcode.com/problems/maximum-strong-pair-xor-ii/) | 🔴 Hard | 33.2% | Max XOR strong pair (large) — sort + window + trie |
| 3086 | [3086. Minimum Moves to Pick K Ones](https://leetcode.com/problems/minimum-moves-to-pick-k-ones/) | 🔴 Hard | 21.8% | Min moves to pick k ones — positions + cost window |
| 3323 | [3323. Minimize Connected Groups by Inserting Interval](https://leetcode.com/problems/minimize-connected-groups-by-inserting-interval/) | 🟡 Medium | 50.1% | Minimize connected groups by inserting one interval |
| 3505 | [3505. Minimum Operations to Make Elements Within K Subarrays Equal](https://leetcode.com/problems/minimum-operations-to-make-elements-within-k-subarrays-equal/) | 🔴 Hard | 28.8% | Min ops so elements in k subarrays become equal |

### N. Bitwise sliding window (AND / OR / XOR)

**4 problems** · 🟢 1 · 🟡 2 · 🔴 1 · template: `bitwise-window`

- **Idea:** Window state is a bit aggregate. Expand while AND/OR/XOR satisfies a property; shrink or jump using bit-count arrays when broken.
- **Method:** Track bit counts (for OR/AND) or use trie for XOR; two pointers when monotonic.

#### Java template

```java
// Example shape: longest nice subarray — pairwise AND 0 (LC 2401)
public int longestNiceSubarray(int[] nums) {
    int left = 0, used = 0, best = 0;
    for (int right = 0; right < nums.length; right++) {
        while ((used & nums[right]) != 0) { // bit conflict → shrink
            used ^= nums[left++];           // remove bits of left
        }
        used |= nums[right];
        best = Math.max(best, right - left + 1);
    }
    return best;
}

// OR ≥ K shortest (LC 3097): maintain bit counts[32]; shrink while OR >= k.
// void add(int x) { for (b=0;b<32;b++) if ((x>>b)&1) if (cnt[b]++==0) or |= 1<<b; }
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 2401 | [2401. Longest Nice Subarray](https://leetcode.com/problems/longest-nice-subarray/) | 🟡 Medium | 64.8% | Longest nice subarray (pairwise AND = 0 → no shared bits) |
| 2411 | [2411. Smallest Subarrays With Maximum Bitwise OR](https://leetcode.com/problems/smallest-subarrays-with-maximum-bitwise-or/) | 🟡 Medium | 61.9% | For each end, smallest subarray achieving max OR so far |
| 3411 | [3411. Maximum Subarray With Equal Products](https://leetcode.com/problems/maximum-subarray-with-equal-products/) | 🟢 Easy | 47.0% | Max length subarray whose product equals LCM·GCD property |
| 3845 | [3845. Maximum Subarray XOR with Bounded Range](https://leetcode.com/problems/maximum-subarray-xor-with-bounded-range/) | 🔴 Hard | 33.2% | Max subarray XOR within a bounded range |

### O. Circular / wrap-around windows

**5 problems** · 🟢 2 · 🟡 3 · 🔴 0 · template: `circular`

- **Idea:** Array is circular: double the array (or use mod index) and apply a fixed/variable window with length cap n.
- **Method:** Work on `arr+arr` but never use a window longer than n.

#### Java template

```java
// Example shape: circular group all 1s (LC 2134) — double the array, cap length
public int minSwaps(int[] nums) {
    int n = nums.length, ones = 0;
    for (int x : nums) ones += x;
    if (ones == 0) return 0;
    int[] a = new int[2 * n];
    System.arraycopy(nums, 0, a, 0, n);
    System.arraycopy(nums, 0, a, n, n);
    int zeros = 0, best;
    for (int i = 0; i < ones; i++) if (a[i] == 0) zeros++;
    best = zeros;
    for (int r = ones; r < n + ones; r++) { // only n windows
        if (a[r] == 0) zeros++;
        if (a[r - ones] == 0) zeros--;
        best = Math.min(best, zeros);
    }
    return best;
}
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 1652 | [1652. Defuse the Bomb](https://leetcode.com/problems/defuse-the-bomb/) | 🟢 Easy | 79.4% | Defuse the bomb — circular next/prev k sums |
| 1888 | [1888. Minimum Number of Flips to Make the Binary String Alternating](https://leetcode.com/problems/minimum-number-of-flips-to-make-the-binary-string-alternating/) | 🟡 Medium | 53.6% | Min flips to make binary string alternating (circular) |
| 2134 | [2134. Minimum Swaps to Group All 1's Together II](https://leetcode.com/problems/minimum-swaps-to-group-all-1-s-together-ii/) | 🟡 Medium | 65.7% | Group all 1s together II — circular version of 1151 |
| 3206 | [3206. Alternating Groups I](https://leetcode.com/problems/alternating-groups-i/) | 🟢 Easy | 69.3% | Count alternating groups of length 3 on a circle |
| 3208 | [3208. Alternating Groups II](https://leetcode.com/problems/alternating-groups-ii/) | 🟡 Medium | 60.0% | Count alternating groups of length k on a circle |

### P. Multi-window / M non-overlapping subarrays

**3 problems** · 🟢 0 · 🟡 0 · 🔴 3 · template: `multi-window-dp`

- **Idea:** Choose several fixed-length (or constrained) subarrays that do not overlap. Usually DP where transition uses a precomputed window best.
- **Method:** Precompute best window ending ≤ i; DP over (#subarrays used, index).

#### Java template

```java
// Example shape: max sum of 3 non-overlapping subarrays of length k (LC 689)
// 1) windowSum[i] = sum of nums[i..i+k)
// 2) leftBest[i]  = index of best window in [0..i]
// 3) rightBest[i] = index of best window in [i..n-k]
// 4) try every mid window m; ans = leftBest[m-k] + m + rightBest[m+k]
public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
    int n = nums.length, m = n - k + 1;
    int[] w = new int[m];
    long sum = 0;
    for (int i = 0; i < n; i++) {
        sum += nums[i];
        if (i >= k) sum -= nums[i - k];
        if (i >= k - 1) w[i - k + 1] = (int) sum;
    }
    int[] left = new int[m], right = new int[m];
    left[0] = 0;
    for (int i = 1; i < m; i++) left[i] = w[i] > w[left[i - 1]] ? i : left[i - 1];
    right[m - 1] = m - 1;
    for (int i = m - 2; i >= 0; i--) right[i] = w[i] >= w[right[i + 1]] ? i : right[i + 1];
    int best = -1;
    int[] ans = new int[3];
    for (int mid = k; mid + k < m; mid++) {
        int l = left[mid - k], r = right[mid + k];
        int total = w[l] + w[mid] + w[r];
        if (total > best) { best = total; ans[0] = l; ans[1] = mid; ans[2] = r; }
    }
    return ans;
}
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 689 | [689. Maximum Sum of 3 Non-Overlapping Subarrays](https://leetcode.com/problems/maximum-sum-of-3-non-overlapping-subarrays/) | 🔴 Hard | 59.9% | Max sum of 3 non-overlapping subarrays of length k |
| 3956 | [3956. Maximum Sum of M Non-Overlapping Subarrays I](https://leetcode.com/problems/maximum-sum-of-m-non-overlapping-subarrays-i/) | 🔴 Hard | 26.1% | Max sum of M non-overlapping subarrays (I) |
| 3957 | [3957. Maximum Sum of M Non-Overlapping Subarrays II](https://leetcode.com/problems/maximum-sum-of-m-non-overlapping-subarrays-ii/) | 🔴 Hard | 15.2% | Max sum of M non-overlapping subarrays (II) |

### Q. Rolling hash, stream pattern & string search windows

**5 problems** · 🟢 0 · 🟡 3 · 🔴 2 · template: `hash-kmp`

- **Idea:** Fixed or variable window compared via rolling hash / KMP against a pattern, or used to find duplicates.
- **Method:** Rabin–Karp rolling hash, or KMP automaton over a stream.

#### Java template

```java
// Example shape: rolling hash fixed window (Rabin–Karp)
// Useful for duplicate substring / pattern in stream (LC 1044, 2156, 3023)
static final long MOD = 1_000_000_007L;
static final long BASE = 911382323L;

long windowHash(String s, int k) {
    long h = 0, pow = 1;
    for (int i = 0; i < k; i++) {
        h = (h * BASE + s.charAt(i)) % MOD;
        if (i > 0) pow = pow * BASE % MOD;
    }
    // h is hash of s[0..k)
    // slide: h = ( (h - s[i]*pow) * BASE + s[i+k] ) % MOD;  fix negatives
    return h;
}

// Stream KMP (LC 3023/3037): build LPS of pattern; feed stream bits one by one.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 2156 | [2156. Find Substring With Given Hash Value](https://leetcode.com/problems/find-substring-with-given-hash-value/) | 🔴 Hard | 26.5% | Find substring with given hash value (rolling hash from right) |
| 3023 | [3023. Find Pattern in Infinite Stream I](https://leetcode.com/problems/find-pattern-in-infinite-stream-i/) | 🟡 Medium | 57.4% | Find pattern in infinite stream I |
| 3037 | [3037. Find Pattern in Infinite Stream II](https://leetcode.com/problems/find-pattern-in-infinite-stream-ii/) | 🔴 Hard | 67.8% | Find pattern in infinite stream II |
| 3135 | [3135. Equalize Strings by Adding or Removing Characters at Ends](https://leetcode.com/problems/equalize-strings-by-adding-or-removing-characters-at-ends/) | 🟡 Medium | 56.5% | Equalize strings by adding/removing chars at ends |
| 3694 | [3694. Distinct Points Reachable After Substring Removal](https://leetcode.com/problems/distinct-points-reachable-after-substring-removal/) | 🟡 Medium | 54.2% | Distinct points reachable after removing one substring |

### R. Near-duplicate / value-close windows (maps, buckets)

**2 problems** · 🟢 1 · 🟡 0 · 🔴 1 · template: `nearby-duplicate`

- **Idea:** Check whether two equal or nearly equal values appear within index distance k (or value distance t). Sliding window of last k indices.
- **Method:** Hash map of last index, or bucket map of size t for value proximity.

#### Java template

```java
// Example shape: contains duplicate II (LC 219)
public boolean containsNearbyDuplicate(int[] nums, int k) {
    Map<Integer, Integer> last = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        Integer p = last.get(nums[i]);
        if (p != null && i - p <= k) return true;
        last.put(nums[i], i);
        // optional: if map size > k remove nums[i-k] (true sliding set)
    }
    return false;
}

// Almost duplicate (LC 220): bucket map with width t+1, keep last k indices only.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 219 | [219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/) | 🟢 Easy | 51.5% | Duplicate within index distance ≤ k |
| 220 | [220. Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/) | 🔴 Hard | 24.9% | Almost-duplicate within index ≤ k and value ≤ t |

### S. Run-length / pattern structure (turbulent, arithmetic, descent)

**9 problems** · 🟢 1 · 🟡 7 · 🔴 1 · template: `run-pattern`

- **Idea:** Validity is a local pattern (alternating up/down, constant difference, strict descent). Extend a run; reset when pattern breaks.
- **Method:** Track current run length; O(1) state transitions — lightweight window.

#### Java template

```java
// Example shape: longest turbulent subarray (LC 978)
public int maxTurbulenceSize(int[] arr) {
    int n = arr.length, best = 1, up = 1, down = 1;
    for (int i = 1; i < n; i++) {
        if (arr[i] > arr[i - 1]) { up = down + 1; down = 1; }
        else if (arr[i] < arr[i - 1]) { down = up + 1; up = 1; }
        else { up = down = 1; }
        best = Math.max(best, Math.max(up, down));
    }
    return best;
}

// Arithmetic slices (LC 413): extend run while diff constant; add runLen-1 each step.
// Consecutive power window (LC 3254): track length of increasing-by-1 run.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 395 | [395. Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/) | 🟡 Medium | 46.4% | Longest substring where every char has freq ≥ K (D&C or advanced window) |
| 413 | [413. Arithmetic Slices](https://leetcode.com/problems/arithmetic-slices/) | 🟡 Medium | 64.8% | Count arithmetic slices (extend runs of constant diff) |
| 978 | [978. Longest Turbulent Subarray](https://leetcode.com/problems/longest-turbulent-subarray/) | 🟡 Medium | 49.3% | Longest turbulent (alternating) subarray |
| 2110 | [2110. Number of Smooth Descent Periods of a Stock](https://leetcode.com/problems/number-of-smooth-descent-periods-of-a-stock/) | 🟡 Medium | 67.7% | Count smooth descent periods of a stock |
| 2760 | [2760. Longest Even Odd Subarray With Threshold](https://leetcode.com/problems/longest-even-odd-subarray-with-threshold/) | 🟢 Easy | 32.3% | Longest even-odd alternating subarray under threshold |
| 3254 | [3254. Find the Power of K-Size Subarrays I](https://leetcode.com/problems/find-the-power-of-k-size-subarrays-i/) | 🟡 Medium | 62.1% | Power of each k-size window if strictly consecutive |
| 3255 | [3255. Find the Power of K-Size Subarrays II](https://leetcode.com/problems/find-the-power-of-k-size-subarrays-ii/) | 🟡 Medium | 31.8% | Same as 3254, optimized / larger |
| 3445 | [3445. Maximum Difference Between Even and Odd Frequency II](https://leetcode.com/problems/maximum-difference-between-even-and-odd-frequency-ii/) | 🔴 Hard | 48.6% | Max difference between even/odd char frequencies in a window |
| 3589 | [3589. Count Prime-Gap Balanced Subarrays](https://leetcode.com/problems/count-prime-gap-balanced-subarrays/) | 🟡 Medium | 23.7% | Count prime-gap balanced subarrays |

### T. Special domain — multi-list merge, geometry, time, inventory

**10 problems** · 🟢 1 · 🟡 4 · 🔴 5 · template: `special-domain`

- **Idea:** Sliding window idea applied outside plain arrays: k sorted lists, time-based requests, virus spread, stock strategy, etc.
- **Method:** Same expand/shrink or fixed-k idea on a transformed sequence.

#### Java template

```java
// Example shape: smallest range covering k lists (LC 632)
// Min-heap of current heads; track global max; advance the min pointer.
public int[] smallestRange(List<List<Integer>> nums) {
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
    // int[]{value, listIndex, elementIndex}
    int max = Integer.MIN_VALUE;
    for (int i = 0; i < nums.size(); i++) {
        int v = nums.get(i).get(0);
        pq.offer(new int[]{v, i, 0});
        max = Math.max(max, v);
    }
    int bestL = 0, bestR = Integer.MAX_VALUE;
    while (pq.size() == nums.size()) {
        int[] cur = pq.poll();
        int v = cur[0], li = cur[1], ei = cur[2];
        if (max - v < bestR - bestL) { bestL = v; bestR = max; }
        if (ei + 1 == nums.get(li).size()) break;
        int nv = nums.get(li).get(ei + 1);
        pq.offer(new int[]{nv, li, ei + 1});
        max = Math.max(max, nv);
    }
    return new int[]{bestL, bestR};
}

// Offline time windows (LC 2747): sort queries + events, two pointers on time.
```

| # | Problem | Diff | Acc | One-liner |
|---:|---|:---:|---:|---|
| 632 | [632. Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) | 🔴 Hard | 70.2% | Smallest range covering ≥1 element from each of k lists |
| 683 | [683. K Empty Slots](https://leetcode.com/problems/k-empty-slots/) | 🔴 Hard | 38.0% | K empty slots — bloom days + neighbor empty window |
| 718 | [718. Maximum Length of Repeated Subarray](https://leetcode.com/problems/maximum-length-of-repeated-subarray/) | 🟡 Medium | 51.5% | Max length of repeated subarray (DP / rolling-hash windows) |
| 1016 | [1016. Binary String With Substrings Representing 1 To N](https://leetcode.com/problems/binary-string-with-substrings-representing-1-to-n/) | 🟡 Medium | 58.6% | Binary representations of 1..N appear as substrings |
| 1052 | [1052. Grumpy Bookstore Owner](https://leetcode.com/problems/grumpy-bookstore-owner/) | 🟡 Medium | 64.1% | Grumpy bookstore — best fixed window of calm minutes |
| 1763 | [1763. Longest Nice Substring](https://leetcode.com/problems/longest-nice-substring/) | 🟢 Easy | 64.5% | Longest nice substring (both cases of each letter present) |
| 1956 | [1956. Minimum Time For K Virus Variants to Spread](https://leetcode.com/problems/minimum-time-for-k-virus-variants-to-spread/) | 🔴 Hard | 50.6% | Min time for k virus variants to spread |
| 2524 | [2524. Maximum Frequency Score of a Subarray](https://leetcode.com/problems/maximum-frequency-score-of-a-subarray/) | 🔴 Hard | 36.1% | Maximum frequency score of a subarray |
| 2747 | [2747. Count Zero Request Servers](https://leetcode.com/problems/count-zero-request-servers/) | 🟡 Medium | 36.2% | Zero-request servers over query time windows |
| 2902 | [2902. Count of Sub-Multisets With Bounded Sum](https://leetcode.com/problems/count-of-sub-multisets-with-bounded-sum/) | 🔴 Hard | 22.6% | Count sub-multisets with bounded sum |

---

## Suggested study order

Learn patterns in this order (highest interview ROI first):

1. **A → E → F → G** — pure fixed & variable windows (core muscle memory)
2. **H → I** — need-map covering + counting with at-most trick
3. **J → K** — mono deque & prefix tricks (hard interview favorites)
4. **B → C → O** — fixed window variants & circular
5. **L → M** — binary search + sort-then-window
6. **N → P → Q → R → S → T → D** — specialized / rarer patterns

### Starter set (~20)

3, 76, 209, 239, 340, 424, 438, 567, 643, 713, 862, 904, 992, 1004, 1248, 1438, 1456, 1695, 2461, 2958

---

## Full index (problem → category)

| # | Problem | Diff | Category |
|---:|---|:---:|---|
| 3 | [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 30 | [30. Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/) | 🔴 Hard | **H** · Covering window & multiset / anagram match |
| 76 | [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | 🔴 Hard | **H** · Covering window & multiset / anagram match |
| 159 | [159. Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 187 | [187. Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/) | 🟡 Medium | **B** · Fixed window — distinct / frequency constraints |
| 209 | [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 219 | [219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/) | 🟢 Easy | **R** · Near-duplicate / value-close windows (maps, buckets) |
| 220 | [220. Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/) | 🔴 Hard | **R** · Near-duplicate / value-close windows (maps, buckets) |
| 239 | [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) | 🔴 Hard | **J** · Monotonic deque — window max/min & max−min constraints |
| 340 | [340. Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 395 | [395. Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/) | 🟡 Medium | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 413 | [413. Arithmetic Slices](https://leetcode.com/problems/arithmetic-slices/) | 🟡 Medium | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 424 | [424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/) | 🟡 Medium | **F** · Variable window — longest with ≤ K flips / replacements / deletes |
| 438 | [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 480 | [480. Sliding Window Median](https://leetcode.com/problems/sliding-window-median/) | 🔴 Hard | **D** · Fixed window — order statistics (median / k-th / beauty) |
| 487 | [487. Max Consecutive Ones II](https://leetcode.com/problems/max-consecutive-ones-ii/) | 🟡 Medium | **F** · Variable window — longest with ≤ K flips / replacements / deletes |
| 567 | [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 594 | [594. Longest Harmonious Subsequence](https://leetcode.com/problems/longest-harmonious-subsequence/) | 🟢 Easy | **M** · Sort first, then sliding window on values |
| 632 | [632. Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/) | 🔴 Hard | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 643 | [643. Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/) | 🟢 Easy | **A** · Fixed window — sum / average / count |
| 658 | [658. Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 683 | [683. K Empty Slots](https://leetcode.com/problems/k-empty-slots/) | 🔴 Hard | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 689 | [689. Maximum Sum of 3 Non-Overlapping Subarrays](https://leetcode.com/problems/maximum-sum-of-3-non-overlapping-subarrays/) | 🔴 Hard | **P** · Multi-window / M non-overlapping subarrays |
| 713 | [713. Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 718 | [718. Maximum Length of Repeated Subarray](https://leetcode.com/problems/maximum-length-of-repeated-subarray/) | 🟡 Medium | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 727 | [727. Minimum Window Subsequence](https://leetcode.com/problems/minimum-window-subsequence/) | 🔴 Hard | **H** · Covering window & multiset / anagram match |
| 837 | [837. New 21 Game](https://leetcode.com/problems/new-21-game/) | 🟡 Medium | **K** · Prefix sums, difference arrays & reachability windows |
| 862 | [862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/) | 🔴 Hard | **J** · Monotonic deque — window max/min & max−min constraints |
| 904 | [904. Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 930 | [930. Binary Subarrays With Sum](https://leetcode.com/problems/binary-subarrays-with-sum/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 978 | [978. Longest Turbulent Subarray](https://leetcode.com/problems/longest-turbulent-subarray/) | 🟡 Medium | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 992 | [992. Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/) | 🔴 Hard | **I** · Counting subarrays — at-most / exactly-K family |
| 995 | [995. Minimum Number of K Consecutive Bit Flips](https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips/) | 🔴 Hard | **K** · Prefix sums, difference arrays & reachability windows |
| 1004 | [1004. Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/) | 🟡 Medium | **F** · Variable window — longest with ≤ K flips / replacements / deletes |
| 1016 | [1016. Binary String With Substrings Representing 1 To N](https://leetcode.com/problems/binary-string-with-substrings-representing-1-to-n/) | 🟡 Medium | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 1031 | [1031. Maximum Sum of Two Non-Overlapping Subarrays](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/) | 🟡 Medium | **K** · Prefix sums, difference arrays & reachability windows |
| 1040 | [1040. Moving Stones Until Consecutive II](https://leetcode.com/problems/moving-stones-until-consecutive-ii/) | 🟡 Medium | **M** · Sort first, then sliding window on values |
| 1044 | [1044. Longest Duplicate Substring](https://leetcode.com/problems/longest-duplicate-substring/) | 🔴 Hard | **L** · Binary search on answer + window feasibility |
| 1052 | [1052. Grumpy Bookstore Owner](https://leetcode.com/problems/grumpy-bookstore-owner/) | 🟡 Medium | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 1100 | [1100. Find K-Length Substrings With No Repeated Characters](https://leetcode.com/problems/find-k-length-substrings-with-no-repeated-characters/) | 🟡 Medium | **B** · Fixed window — distinct / frequency constraints |
| 1151 | [1151. Minimum Swaps to Group All 1's Together](https://leetcode.com/problems/minimum-swaps-to-group-all-1-s-together/) | 🟡 Medium | **C** · Fixed window — minimize cost inside a block |
| 1156 | [1156. Swap For Longest Repeated Character Substring](https://leetcode.com/problems/swap-for-longest-repeated-character-substring/) | 🟡 Medium | **F** · Variable window — longest with ≤ K flips / replacements / deletes |
| 1176 | [1176. Diet Plan Performance](https://leetcode.com/problems/diet-plan-performance/) | 🟢 Easy | **A** · Fixed window — sum / average / count |
| 1208 | [1208. Get Equal Substrings Within Budget](https://leetcode.com/problems/get-equal-substrings-within-budget/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 1234 | [1234. Replace the Substring for Balanced String](https://leetcode.com/problems/replace-the-substring-for-balanced-string/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 1248 | [1248. Count Number of Nice Subarrays](https://leetcode.com/problems/count-number-of-nice-subarrays/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 1297 | [1297. Maximum Number of Occurrences of a Substring](https://leetcode.com/problems/maximum-number-of-occurrences-of-a-substring/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 1343 | [1343. Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold](https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/) | 🟡 Medium | **A** · Fixed window — sum / average / count |
| 1358 | [1358. Number of Substrings Containing All Three Characters](https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 1423 | [1423. Maximum Points You Can Obtain from Cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/) | 🟡 Medium | **K** · Prefix sums, difference arrays & reachability windows |
| 1425 | [1425. Constrained Subsequence Sum](https://leetcode.com/problems/constrained-subsequence-sum/) | 🔴 Hard | **J** · Monotonic deque — window max/min & max−min constraints |
| 1438 | [1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) | 🟡 Medium | **J** · Monotonic deque — window max/min & max−min constraints |
| 1456 | [1456. Maximum Number of Vowels in a Substring of Given Length](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/) | 🟡 Medium | **A** · Fixed window — sum / average / count |
| 1477 | [1477. Find Two Non-overlapping Sub-arrays Each With Target Sum](https://leetcode.com/problems/find-two-non-overlapping-sub-arrays-each-with-target-sum/) | 🟡 Medium | **K** · Prefix sums, difference arrays & reachability windows |
| 1493 | [1493. Longest Subarray of 1's After Deleting One Element](https://leetcode.com/problems/longest-subarray-of-1-s-after-deleting-one-element/) | 🟡 Medium | **F** · Variable window — longest with ≤ K flips / replacements / deletes |
| 1499 | [1499. Max Value of Equation](https://leetcode.com/problems/max-value-of-equation/) | 🔴 Hard | **J** · Monotonic deque — window max/min & max−min constraints |
| 1610 | [1610. Maximum Number of Visible Points](https://leetcode.com/problems/maximum-number-of-visible-points/) | 🔴 Hard | **M** · Sort first, then sliding window on values |
| 1652 | [1652. Defuse the Bomb](https://leetcode.com/problems/defuse-the-bomb/) | 🟢 Easy | **O** · Circular / wrap-around windows |
| 1658 | [1658. Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 1695 | [1695. Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 1703 | [1703. Minimum Adjacent Swaps for K Consecutive Ones](https://leetcode.com/problems/minimum-adjacent-swaps-for-k-consecutive-ones/) | 🔴 Hard | **M** · Sort first, then sliding window on values |
| 1763 | [1763. Longest Nice Substring](https://leetcode.com/problems/longest-nice-substring/) | 🟢 Easy | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 1838 | [1838. Frequency of the Most Frequent Element](https://leetcode.com/problems/frequency-of-the-most-frequent-element/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 1839 | [1839. Longest Substring Of All Vowels in Order](https://leetcode.com/problems/longest-substring-of-all-vowels-in-order/) | 🟡 Medium | **M** · Sort first, then sliding window on values |
| 1852 | [1852. Distinct Numbers in Each Subarray](https://leetcode.com/problems/distinct-numbers-in-each-subarray/) | 🟡 Medium | **B** · Fixed window — distinct / frequency constraints |
| 1871 | [1871. Jump Game VII](https://leetcode.com/problems/jump-game-vii/) | 🟡 Medium | **K** · Prefix sums, difference arrays & reachability windows |
| 1876 | [1876. Substrings of Size Three with Distinct Characters](https://leetcode.com/problems/substrings-of-size-three-with-distinct-characters/) | 🟢 Easy | **B** · Fixed window — distinct / frequency constraints |
| 1888 | [1888. Minimum Number of Flips to Make the Binary String Alternating](https://leetcode.com/problems/minimum-number-of-flips-to-make-the-binary-string-alternating/) | 🟡 Medium | **O** · Circular / wrap-around windows |
| 1918 | [1918. Kth Smallest Subarray Sum](https://leetcode.com/problems/kth-smallest-subarray-sum/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 1956 | [1956. Minimum Time For K Virus Variants to Spread](https://leetcode.com/problems/minimum-time-for-k-virus-variants-to-spread/) | 🔴 Hard | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 1984 | [1984. Minimum Difference Between Highest and Lowest of K Scores](https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/) | 🟢 Easy | **C** · Fixed window — minimize cost inside a block |
| 2009 | [2009. Minimum Number of Operations to Make Array Continuous](https://leetcode.com/problems/minimum-number-of-operations-to-make-array-continuous/) | 🔴 Hard | **L** · Binary search on answer + window feasibility |
| 2024 | [2024. Maximize the Confusion of an Exam](https://leetcode.com/problems/maximize-the-confusion-of-an-exam/) | 🟡 Medium | **F** · Variable window — longest with ≤ K flips / replacements / deletes |
| 2067 | [2067. Number of Equal Count Substrings](https://leetcode.com/problems/number-of-equal-count-substrings/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 2090 | [2090. K Radius Subarray Averages](https://leetcode.com/problems/k-radius-subarray-averages/) | 🟡 Medium | **A** · Fixed window — sum / average / count |
| 2106 | [2106. Maximum Fruits Harvested After at Most K Steps](https://leetcode.com/problems/maximum-fruits-harvested-after-at-most-k-steps/) | 🔴 Hard | **K** · Prefix sums, difference arrays & reachability windows |
| 2107 | [2107. Number of Unique Flavors After Sharing K Candies](https://leetcode.com/problems/number-of-unique-flavors-after-sharing-k-candies/) | 🟡 Medium | **M** · Sort first, then sliding window on values |
| 2110 | [2110. Number of Smooth Descent Periods of a Stock](https://leetcode.com/problems/number-of-smooth-descent-periods-of-a-stock/) | 🟡 Medium | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 2134 | [2134. Minimum Swaps to Group All 1's Together II](https://leetcode.com/problems/minimum-swaps-to-group-all-1-s-together-ii/) | 🟡 Medium | **O** · Circular / wrap-around windows |
| 2156 | [2156. Find Substring With Given Hash Value](https://leetcode.com/problems/find-substring-with-given-hash-value/) | 🔴 Hard | **Q** · Rolling hash, stream pattern & string search windows |
| 2260 | [2260. Minimum Consecutive Cards to Pick Up](https://leetcode.com/problems/minimum-consecutive-cards-to-pick-up/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 2269 | [2269. Find the K-Beauty of a Number](https://leetcode.com/problems/find-the-k-beauty-of-a-number/) | 🟢 Easy | **A** · Fixed window — sum / average / count |
| 2271 | [2271. Maximum White Tiles Covered by a Carpet](https://leetcode.com/problems/maximum-white-tiles-covered-by-a-carpet/) | 🟡 Medium | **M** · Sort first, then sliding window on values |
| 2302 | [2302. Count Subarrays With Score Less Than K](https://leetcode.com/problems/count-subarrays-with-score-less-than-k/) | 🔴 Hard | **I** · Counting subarrays — at-most / exactly-K family |
| 2379 | [2379. Minimum Recolors to Get K Consecutive Black Blocks](https://leetcode.com/problems/minimum-recolors-to-get-k-consecutive-black-blocks/) | 🟢 Easy | **C** · Fixed window — minimize cost inside a block |
| 2398 | [2398. Maximum Number of Robots Within Budget](https://leetcode.com/problems/maximum-number-of-robots-within-budget/) | 🔴 Hard | **J** · Monotonic deque — window max/min & max−min constraints |
| 2401 | [2401. Longest Nice Subarray](https://leetcode.com/problems/longest-nice-subarray/) | 🟡 Medium | **N** · Bitwise sliding window (AND / OR / XOR) |
| 2411 | [2411. Smallest Subarrays With Maximum Bitwise OR](https://leetcode.com/problems/smallest-subarrays-with-maximum-bitwise-or/) | 🟡 Medium | **N** · Bitwise sliding window (AND / OR / XOR) |
| 2444 | [2444. Count Subarrays With Fixed Bounds](https://leetcode.com/problems/count-subarrays-with-fixed-bounds/) | 🔴 Hard | **I** · Counting subarrays — at-most / exactly-K family |
| 2461 | [2461. Maximum Sum of Distinct Subarrays With Length K](https://leetcode.com/problems/maximum-sum-of-distinct-subarrays-with-length-k/) | 🟡 Medium | **B** · Fixed window — distinct / frequency constraints |
| 2516 | [2516. Take K of Each Character From Left and Right](https://leetcode.com/problems/take-k-of-each-character-from-left-and-right/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 2524 | [2524. Maximum Frequency Score of a Subarray](https://leetcode.com/problems/maximum-frequency-score-of-a-subarray/) | 🔴 Hard | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 2528 | [2528. Maximize the Minimum Powered City](https://leetcode.com/problems/maximize-the-minimum-powered-city/) | 🔴 Hard | **L** · Binary search on answer + window feasibility |
| 2537 | [2537. Count the Number of Good Subarrays](https://leetcode.com/problems/count-the-number-of-good-subarrays/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 2555 | [2555. Maximize Win From Two Segments](https://leetcode.com/problems/maximize-win-from-two-segments/) | 🟡 Medium | **M** · Sort first, then sliding window on values |
| 2653 | [2653. Sliding Subarray Beauty](https://leetcode.com/problems/sliding-subarray-beauty/) | 🟡 Medium | **D** · Fixed window — order statistics (median / k-th / beauty) |
| 2730 | [2730. Find the Longest Semi-Repetitive Substring](https://leetcode.com/problems/find-the-longest-semi-repetitive-substring/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 2743 | [2743. Count Substrings Without Repeating Character](https://leetcode.com/problems/count-substrings-without-repeating-character/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 2747 | [2747. Count Zero Request Servers](https://leetcode.com/problems/count-zero-request-servers/) | 🟡 Medium | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 2760 | [2760. Longest Even Odd Subarray With Threshold](https://leetcode.com/problems/longest-even-odd-subarray-with-threshold/) | 🟢 Easy | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 2762 | [2762. Continuous Subarrays](https://leetcode.com/problems/continuous-subarrays/) | 🟡 Medium | **J** · Monotonic deque — window max/min & max−min constraints |
| 2779 | [2779. Maximum Beauty of an Array After Applying Operation](https://leetcode.com/problems/maximum-beauty-of-an-array-after-applying-operation/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 2781 | [2781. Length of the Longest Valid Substring](https://leetcode.com/problems/length-of-the-longest-valid-substring/) | 🔴 Hard | **H** · Covering window & multiset / anagram match |
| 2799 | [2799. Count Complete Subarrays in an Array](https://leetcode.com/problems/count-complete-subarrays-in-an-array/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 2831 | [2831. Find the Longest Equal Subarray](https://leetcode.com/problems/find-the-longest-equal-subarray/) | 🟡 Medium | **F** · Variable window — longest with ≤ K flips / replacements / deletes |
| 2841 | [2841. Maximum Sum of Almost Unique Subarray](https://leetcode.com/problems/maximum-sum-of-almost-unique-subarray/) | 🟡 Medium | **B** · Fixed window — distinct / frequency constraints |
| 2875 | [2875. Minimum Size Subarray in Infinite Array](https://leetcode.com/problems/minimum-size-subarray-in-infinite-array/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 2902 | [2902. Count of Sub-Multisets With Bounded Sum](https://leetcode.com/problems/count-of-sub-multisets-with-bounded-sum/) | 🔴 Hard | **T** · Special domain — multi-list merge, geometry, time, inventory |
| 2904 | [2904. Shortest and Lexicographically Smallest Beautiful String](https://leetcode.com/problems/shortest-and-lexicographically-smallest-beautiful-string/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 2932 | [2932. Maximum Strong Pair XOR I](https://leetcode.com/problems/maximum-strong-pair-xor-i/) | 🟢 Easy | **M** · Sort first, then sliding window on values |
| 2935 | [2935. Maximum Strong Pair XOR II](https://leetcode.com/problems/maximum-strong-pair-xor-ii/) | 🔴 Hard | **M** · Sort first, then sliding window on values |
| 2953 | [2953. Count Complete Substrings](https://leetcode.com/problems/count-complete-substrings/) | 🔴 Hard | **H** · Covering window & multiset / anagram match |
| 2958 | [2958. Length of Longest Subarray With at Most K Frequency](https://leetcode.com/problems/length-of-longest-subarray-with-at-most-k-frequency/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 2962 | [2962. Count Subarrays Where Max Element Appears at Least K Times](https://leetcode.com/problems/count-subarrays-where-max-element-appears-at-least-k-times/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 2968 | [2968. Apply Operations to Maximize Frequency Score](https://leetcode.com/problems/apply-operations-to-maximize-frequency-score/) | 🔴 Hard | **L** · Binary search on answer + window feasibility |
| 2981 | [2981. Find Longest Special Substring That Occurs Thrice I](https://leetcode.com/problems/find-longest-special-substring-that-occurs-thrice-i/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 2982 | [2982. Find Longest Special Substring That Occurs Thrice II](https://leetcode.com/problems/find-longest-special-substring-that-occurs-thrice-ii/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 3013 | [3013. Divide an Array Into Subarrays With Minimum Cost II](https://leetcode.com/problems/divide-an-array-into-subarrays-with-minimum-cost-ii/) | 🔴 Hard | **J** · Monotonic deque — window max/min & max−min constraints |
| 3023 | [3023. Find Pattern in Infinite Stream I](https://leetcode.com/problems/find-pattern-in-infinite-stream-i/) | 🟡 Medium | **Q** · Rolling hash, stream pattern & string search windows |
| 3037 | [3037. Find Pattern in Infinite Stream II](https://leetcode.com/problems/find-pattern-in-infinite-stream-ii/) | 🔴 Hard | **Q** · Rolling hash, stream pattern & string search windows |
| 3086 | [3086. Minimum Moves to Pick K Ones](https://leetcode.com/problems/minimum-moves-to-pick-k-ones/) | 🔴 Hard | **M** · Sort first, then sliding window on values |
| 3090 | [3090. Maximum Length Substring With Two Occurrences](https://leetcode.com/problems/maximum-length-substring-with-two-occurrences/) | 🟢 Easy | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 3095 | [3095. Shortest Subarray With OR at Least K I](https://leetcode.com/problems/shortest-subarray-with-or-at-least-k-i/) | 🟢 Easy | **G** · Variable window — minimum length once valid |
| 3097 | [3097. Shortest Subarray With OR at Least K II](https://leetcode.com/problems/shortest-subarray-with-or-at-least-k-ii/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 3134 | [3134. Find the Median of the Uniqueness Array](https://leetcode.com/problems/find-the-median-of-the-uniqueness-array/) | 🔴 Hard | **L** · Binary search on answer + window feasibility |
| 3135 | [3135. Equalize Strings by Adding or Removing Characters at Ends](https://leetcode.com/problems/equalize-strings-by-adding-or-removing-characters-at-ends/) | 🟡 Medium | **Q** · Rolling hash, stream pattern & string search windows |
| 3191 | [3191. Minimum Operations to Make Binary Array Elements Equal to One I](https://leetcode.com/problems/minimum-operations-to-make-binary-array-elements-equal-to-one-i/) | 🟡 Medium | **C** · Fixed window — minimize cost inside a block |
| 3206 | [3206. Alternating Groups I](https://leetcode.com/problems/alternating-groups-i/) | 🟢 Easy | **O** · Circular / wrap-around windows |
| 3208 | [3208. Alternating Groups II](https://leetcode.com/problems/alternating-groups-ii/) | 🟡 Medium | **O** · Circular / wrap-around windows |
| 3254 | [3254. Find the Power of K-Size Subarrays I](https://leetcode.com/problems/find-the-power-of-k-size-subarrays-i/) | 🟡 Medium | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 3255 | [3255. Find the Power of K-Size Subarrays II](https://leetcode.com/problems/find-the-power-of-k-size-subarrays-ii/) | 🟡 Medium | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 3258 | [3258. Count Substrings That Satisfy K-Constraint I](https://leetcode.com/problems/count-substrings-that-satisfy-k-constraint-i/) | 🟢 Easy | **I** · Counting subarrays — at-most / exactly-K family |
| 3261 | [3261. Count Substrings That Satisfy K-Constraint II](https://leetcode.com/problems/count-substrings-that-satisfy-k-constraint-ii/) | 🔴 Hard | **I** · Counting subarrays — at-most / exactly-K family |
| 3297 | [3297. Count Substrings That Can Be Rearranged to Contain a String I](https://leetcode.com/problems/count-substrings-that-can-be-rearranged-to-contain-a-string-i/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 3298 | [3298. Count Substrings That Can Be Rearranged to Contain a String II](https://leetcode.com/problems/count-substrings-that-can-be-rearranged-to-contain-a-string-ii/) | 🔴 Hard | **H** · Covering window & multiset / anagram match |
| 3305 | [3305. Count of Substrings Containing Every Vowel and K Consonants I](https://leetcode.com/problems/count-of-substrings-containing-every-vowel-and-k-consonants-i/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 3306 | [3306. Count of Substrings Containing Every Vowel and K Consonants II](https://leetcode.com/problems/count-of-substrings-containing-every-vowel-and-k-consonants-ii/) | 🟡 Medium | **H** · Covering window & multiset / anagram match |
| 3318 | [3318. Find X-Sum of All K-Long Subarrays I](https://leetcode.com/problems/find-x-sum-of-all-k-long-subarrays-i/) | 🟢 Easy | **B** · Fixed window — distinct / frequency constraints |
| 3321 | [3321. Find X-Sum of All K-Long Subarrays II](https://leetcode.com/problems/find-x-sum-of-all-k-long-subarrays-ii/) | 🔴 Hard | **B** · Fixed window — distinct / frequency constraints |
| 3323 | [3323. Minimize Connected Groups by Inserting Interval](https://leetcode.com/problems/minimize-connected-groups-by-inserting-interval/) | 🟡 Medium | **M** · Sort first, then sliding window on values |
| 3325 | [3325. Count Substrings With K-Frequency Characters I](https://leetcode.com/problems/count-substrings-with-k-frequency-characters-i/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 3329 | [3329. Count Substrings With K-Frequency Characters II](https://leetcode.com/problems/count-substrings-with-k-frequency-characters-ii/) | 🔴 Hard | **I** · Counting subarrays — at-most / exactly-K family |
| 3346 | [3346. Maximum Frequency of an Element After Performing Operations I](https://leetcode.com/problems/maximum-frequency-of-an-element-after-performing-operations-i/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 3347 | [3347. Maximum Frequency of an Element After Performing Operations II](https://leetcode.com/problems/maximum-frequency-of-an-element-after-performing-operations-ii/) | 🔴 Hard | **L** · Binary search on answer + window feasibility |
| 3364 | [3364. Minimum Positive Sum Subarray](https://leetcode.com/problems/minimum-positive-sum-subarray/) | 🟢 Easy | **A** · Fixed window — sum / average / count |
| 3411 | [3411. Maximum Subarray With Equal Products](https://leetcode.com/problems/maximum-subarray-with-equal-products/) | 🟢 Easy | **N** · Bitwise sliding window (AND / OR / XOR) |
| 3413 | [3413. Maximum Coins From K Consecutive Bags](https://leetcode.com/problems/maximum-coins-from-k-consecutive-bags/) | 🟡 Medium | **K** · Prefix sums, difference arrays & reachability windows |
| 3420 | [3420. Count Non-Decreasing Subarrays After K Operations](https://leetcode.com/problems/count-non-decreasing-subarrays-after-k-operations/) | 🔴 Hard | **J** · Monotonic deque — window max/min & max−min constraints |
| 3422 | [3422. Minimum Operations to Make Subarray Elements Equal](https://leetcode.com/problems/minimum-operations-to-make-subarray-elements-equal/) | 🟡 Medium | **C** · Fixed window — minimize cost inside a block |
| 3439 | [3439. Reschedule Meetings for Maximum Free Time I](https://leetcode.com/problems/reschedule-meetings-for-maximum-free-time-i/) | 🟡 Medium | **K** · Prefix sums, difference arrays & reachability windows |
| 3445 | [3445. Maximum Difference Between Even and Odd Frequency II](https://leetcode.com/problems/maximum-difference-between-even-and-odd-frequency-ii/) | 🔴 Hard | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 3505 | [3505. Minimum Operations to Make Elements Within K Subarrays Equal](https://leetcode.com/problems/minimum-operations-to-make-elements-within-k-subarrays-equal/) | 🔴 Hard | **M** · Sort first, then sliding window on values |
| 3578 | [3578. Count Partitions With Max-Min Difference at Most K](https://leetcode.com/problems/count-partitions-with-max-min-difference-at-most-k/) | 🟡 Medium | **J** · Monotonic deque — window max/min & max−min constraints |
| 3589 | [3589. Count Prime-Gap Balanced Subarrays](https://leetcode.com/problems/count-prime-gap-balanced-subarrays/) | 🟡 Medium | **S** · Run-length / pattern structure (turbulent, arithmetic, descent) |
| 3634 | [3634. Minimum Removals to Balance Array](https://leetcode.com/problems/minimum-removals-to-balance-array/) | 🟡 Medium | **L** · Binary search on answer + window feasibility |
| 3641 | [3641. Longest Semi-Repeating Subarray](https://leetcode.com/problems/longest-semi-repeating-subarray/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 3652 | [3652. Best Time to Buy and Sell Stock using Strategy](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-using-strategy/) | 🟡 Medium | **A** · Fixed window — sum / average / count |
| 3672 | [3672. Sum of Weighted Modes in Subarrays](https://leetcode.com/problems/sum-of-weighted-modes-in-subarrays/) | 🟡 Medium | **B** · Fixed window — distinct / frequency constraints |
| 3679 | [3679. Minimum Discards to Balance Inventory](https://leetcode.com/problems/minimum-discards-to-balance-inventory/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 3694 | [3694. Distinct Points Reachable After Substring Removal](https://leetcode.com/problems/distinct-points-reachable-after-substring-removal/) | 🟡 Medium | **Q** · Rolling hash, stream pattern & string search windows |
| 3768 | [3768. Minimum Inversion Count in Subarrays of Fixed Length](https://leetcode.com/problems/minimum-inversion-count-in-subarrays-of-fixed-length/) | 🔴 Hard | **C** · Fixed window — minimize cost inside a block |
| 3795 | [3795. Minimum Subarray Length With Distinct Sum At Least K](https://leetcode.com/problems/minimum-subarray-length-with-distinct-sum-at-least-k/) | 🟡 Medium | **G** · Variable window — minimum length once valid |
| 3845 | [3845. Maximum Subarray XOR with Bounded Range](https://leetcode.com/problems/maximum-subarray-xor-with-bounded-range/) | 🔴 Hard | **N** · Bitwise sliding window (AND / OR / XOR) |
| 3851 | [3851. Maximum Requests Without Violating the Limit](https://leetcode.com/problems/maximum-requests-without-violating-the-limit/) | 🟡 Medium | **E** · Variable window — longest with uniqueness / at-most-K distinct / freq caps |
| 3859 | [3859. Count Subarrays With K Distinct Integers](https://leetcode.com/problems/count-subarrays-with-k-distinct-integers/) | 🔴 Hard | **I** · Counting subarrays — at-most / exactly-K family |
| 3956 | [3956. Maximum Sum of M Non-Overlapping Subarrays I](https://leetcode.com/problems/maximum-sum-of-m-non-overlapping-subarrays-i/) | 🔴 Hard | **P** · Multi-window / M non-overlapping subarrays |
| 3957 | [3957. Maximum Sum of M Non-Overlapping Subarrays II](https://leetcode.com/problems/maximum-sum-of-m-non-overlapping-subarrays-ii/) | 🔴 Hard | **P** · Multi-window / M non-overlapping subarrays |
| 3969 | [3969. Valid Subarrays With Matching Sum Digits I](https://leetcode.com/problems/valid-subarrays-with-matching-sum-digits-i/) | 🟡 Medium | **I** · Counting subarrays — at-most / exactly-K family |
| 3972 | [3972. Valid Subarrays With Matching Sum Digits II](https://leetcode.com/problems/valid-subarrays-with-matching-sum-digits-ii/) | 🔴 Hard | **I** · Counting subarrays — at-most / exactly-K family |

---

*Generated from `list.txt` · Java templates per category · re-run `categorize.py` to refresh*
