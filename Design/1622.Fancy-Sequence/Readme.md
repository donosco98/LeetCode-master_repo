### 1622.Fancy-Sequence

我们想象一个这样的序列
```
0 1 2 3 4 5  6  7 8 9 ...
   *     + ** +
   a     b cd e
```
此时我们如果想getIdx(0)的话，除了取出nums[0]之外，还必须经过一系列的乘法和加法操作```(((((num[0]*a)+b)*c)*d)+e)...```，当然，最终我们肯定可以将其化简为```nums[0]*mul+add```.

此时我们如果想getIdx(1)的话，其实处理方法和nums[0]是一模一样的，所以我们可以直接用```nums[1]*mul+add```.

此时我们如果想getIdx(2)的话，情况就不一样了，不能用```nums[2]*mul+add```了，因为我们实际需要计算的是```(((((num[2])+b)*c)*d)+e)...```。那么我们还需要再重新把这一票算符再走一遍吗？可否直接利用之前化简得到的mul和add呢？其实是可以的。当我们在存nums[2]的时候，不存原始的数值，而是存入的是val = nums[2]/a的话，那么我们当我们调取getIdx(2)时，就可以使用```val*mul+add```这个表达式，因为它本质算的是```(((((num[2]/a*a)+b)*c)*d)+e)...```，恰好就是我们想要的结果。

所以我们发现了一个规律，如果当前的化简算子是{mul,add}的时候，我们想要append一个新的数字nums[i]时，我们不直接append原始的数值，我们append一个虚拟的数值val用来“抵消”这对算子的影响，即使得```nums[i] = val*mul+add```。这样我们调用getIdx(i)的时候，仍然apply当前的化简算子{mul,add}得到```val*mul+add```，恰好就还原了真实的nums[i]。即使此后的{mul,add}可能会更新为{mul',add'}，但是val只是抵消了i之前的运算符的效果，并不会影响i之后的运算的作用，所以当再次调用getIdx(i)的时候，就可以放心地将{mul',add'}用在val上然后输出答案。

接下来的一个关键问题，我们试图得到这个“抵消”后的val时，发现```nums[i] = val*mul+add```并不能保证得到一个整数的```val= (nums[i]-add) / mul```，如果存储的是小数的话，此后的误差会越来越大。另外一个更重要的问题是，mul会随着时间的推移有可能非常大直至越界。而如果为了避免mul越界而对其做了%M的操作的话，那么上面val的计算方法得到的结果就是错误的。```因为(a/b)%M != a / (b%M)```

怎么办呢？事实上，我们并不需要存一个真实的val，我们只要存一个与val关于M同余的值就行了，我们记做val'。因为val'与val（关于M）同余，那么```val'*mul+add```就会与```val*mul+add```同余，也就是和最终的答案同余。而题目要求输出的就只是将答案对M取模的结果。

想求```(nums[i]-add) / mul```关于M的同余，不能用“(nums[i]-add)关于M的同余”去除以“mul关于M的同余”，因为除法不满足 (a/b)%M = (a%M) / (b%M) 。正确的答案是 ```(nums[i]-add) * inv(mul)```。其中inv(mul)称之为mul的逆元。满足如下关系的a和b称之为逆元：
```
x / a ≡ x * b (mod M)
```
写作```b=inv(a)```。存在a的逆元的充要条件是a与M互质。

有了以上的知识，我们求val时，不要计算```val= (nums[i]-add) / mul```，而是计算```val= (nums[i]-add) * inv(mul) % M```。有人会疑问，前者是个小数，后者是个整数，怎么可能相等呢？确实不是相等的，而是同余的。注意查看之前的表达式，有了逆元的概念，同余并不依赖于x/a是否为整数。也就是说，允许小数val与某个整数val'同余，这样当我们同时乘以某个数k时，```val*k```依然与```val'*k```同余，而此时前者可能已经是个整数了。

关于逆元的解法inv(a)，请参见这里的[笔记](https://github.com/wisdompeak/LeetCode/tree/master/Template/Inverse_Element)。

