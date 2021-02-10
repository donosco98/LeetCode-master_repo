### 1755.Closest-Subsequence-Sum

这道题的数据范围限定了解题方法。

如果nums的元素不超过20个，那么我们可以穷举所有的subset sum，最多有2^20 = 1e6，时间复杂度是可以接受的。我们遍历一下这些subset，找到最接近goal的那个即可。

如果nums的元素很多，但是值的范围很小，比如说限定了都是正数、所有的和不超过1000之类的，那么就可以当做动态规划来解。每处理一个新元素x，我们遍历所有subset sum的可能，更新dp[sum] = dp[sum-x]. 

对于本题而言，以上两种方法都不适用。对于有经验的同学而言，nums的元素个数是40，提示了一种非常巧妙的解法：就是将nums平均拆成两个数组nums1和nums2来处理。对于每个数组，我们分别可以穷举所有的subset sum，时间复杂度是o(2^N/2)，然后各自排序得到sum1和sum2。然后对于sum1的每个元素x而言，我们要在sum2里面找到最接近goal-x的那个。反之对于sum2的每个元素x而言，我们要在sum1里面找到最接近goal-x的那个。最后找到全局最接近的abs diff即可。

在上面的解法中，如果先求得所有的subset sum，然后排序，将会使得时间复杂度达到o(MlogM)，其中M=2^N/2. 有一种方法可以直接用o(2^N/2)得到有序的subset sum。假设nums的前i-1个元素已经得到了有序的subset sum的集合{a_k},那么加上第i个元素之后的subset sum的集合就是{a_k}和{a_k+nums[i]}的并集。我们发现这两个集合是各自有序的，这样我们可以用归并排序的方法将两者合并成一个更大的有序集合。