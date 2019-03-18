Given a string, find the length of the longest substring without repeating characters.

给定一个字符串，找出不带有重复字符的最长**子串**

是子串（连续的），而不是子序列（不连续的）

### 个人的想法

首先，一看到不重复，就想到用 Set 作为存储结构，另外，因为是要连续的子字符串，所以可以用两个指针来指定位置，如果遇到重复的，就把前面的删去，有点像滑动窗口的解决办法

最重要的就是用一个变量 max，来存放当前不重复子串的最大值

### 代码

```
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s.length() == 0){
            return 0;
        }
        Set<Character> set = new HashSet<>();
        int max = 0;
        char[] c = s.toCharArray();
        int i = 0, j = 0;
        while (j < s.length()){
            if (!set.contains(c[j])){
                set.add(c[j++]);
                max = Math.max(max, set.size());
            } else {
                set.remove(c[i]++);     //如果有重复，就把 i 位置的字符删去
            }
        }
        return max;
    }
}
```

### 样例说明

拿题目中的样例来过一遍代码：

```
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

首先，前三步都一样，加入到 set 中，记录 max = 3，在遇到 j = 3（也就是第二个 a 时），把 i = 0 的位置的字符删去，并且执行 i++，然后循环，将 j = 3 位置的字符 (a) 加入 set，set 的大小还是 3（内容为 bca），以此类推

第二个样例没什么好说的，来看看第三个样例：

```
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

首先，前两次循环加入 set，max = 2，然后遇到了 j = 2（第二个 w），这时候和第一个样例有一点不一样，先删去 i = 0 处的 p，这时候 set 中还是存在 w，所以还要执行一次删除，然后再把 j = 2 处的 w 加入 set，此时 set 大小为 1，以此类推

### 效率

![](https://upload-images.jianshu.io/upload_images/3426615-6ff502445ae73815.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

75% 左右

### 高效率的解答

接下来看看上图排名中考前的答案

```
public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        int[] index = new int[128]; // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            i = Math.max(index[s.charAt(j)], i);
            ans = Math.max(ans, j - i + 1);
            index[s.charAt(j)] = j + 1;
        }
        return ans;
    }
```

这个答案的想法是类似的，用两个指针 i 和 j，i 代表不重复子串的左侧，j 不停向右移动，max 记录最大值，如果 j 移动到了重复字符，则将 i 移动到 j，重新开始计算子串

一开始的 int[128] 以足够大到表示所有字符的位置，初始为 0，`i = Math.max(index[s.charAt(j)], i);` 表示在 j 移动的过程中，如果碰到重复字符 (s.charAt(j) > i)，就更新 i 的位置