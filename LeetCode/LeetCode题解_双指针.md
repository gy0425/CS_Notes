# Leetcode 题解 - 双指针

<!-- GFM-TOC -->

* [Leetcode 题解 - 双指针](#leetcode-题解---双指针)
  * [1. 有序数组的 Two Sum](#1-有序数组的-two-sum)
  * [2. 两数平方和](#2-两数平方和)
  * [3. 反转字符串中的元音字符](#3-反转字符串中的元音字符)
  * [4. 回文字符串](#4-回文字符串)
  * [5. 归并两个有序数组](#5-归并两个有序数组)
  * [6. 判断链表是否存在环](#6-判断链表是否存在环)
  * [7. 最长子序列](#7-最长子序列)
    <!-- GFM-TOC -->


双指针主要用于遍历数组，两个指针指向不同的元素，从而协同完成任务。

## 1. 有序数组的 Two Sum

167\. Two Sum II - Input array is sorted (Easy)

[Leetcode](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/) / [力扣](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/description/)

```html
Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2
```

题目描述：在有序数组中找出两个数，使它们的和为 target。

使用双指针，一个指针指向值较小的元素，一个指针指向值较大的元素。指向较小元素的指针从头向尾遍历，指向较大元素的指针从尾向头遍历。

- 如果两个指针指向元素的和 sum == target，那么得到要求的结果；
- 如果 sum \> target，移动较大的元素，使 sum 变小一些；
- 如果 sum \< target，移动较小的元素，使 sum 变大一些。

数组中的元素最多遍历一次，时间复杂度为 O(N)。只使用了两个额外变量，空间复杂度为  O(1)。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/437cb54c-5970-4ba9-b2ef-2541f7d6c81e.gif" width="200px"> </div><br>

```java
public int[] twoSum(int[] numbers, int target) {
        int left=0,right=numbers.length-1;
        while(left<right){
            int sum=numbers[left]+numbers[right];
            if(sum>target){
                right--;
            }else if(sum<target){
                left++;
            }else{
                return new int[]{left+1,right+1};
            }
        }
        return null;
    }
```

## 2. 两数平方和

633\. Sum of Square Numbers (Easy)

[Leetcode](https://leetcode.com/problems/sum-of-square-numbers/description/) / [力扣](https://leetcode-cn.com/problems/sum-of-square-numbers/description/)

```html
Input: 5
Output: True
Explanation: 1 * 1 + 2 * 2 = 5
```

题目描述：判断一个非负整数是否为两个整数的平方和。

可以看成是在元素为 0\~target 的有序数组中查找两个数，使得这两个数的平方和为 target，如果能找到，则返回 true，表示 target 是两个整数的平方和。

本题和 167\. Two Sum II - Input array is sorted 类似，只有一个明显区别：一个是和为 target，一个是平方和为 target。本题同样可以使用双指针得到两个数，使其平方和为 target。

本题的关键是右指针的初始化，实现剪枝，从而降低时间复杂度。设右指针为 x，左指针固定为 0，为了使 0<sup>2</sup> + x<sup>2</sup> 的值尽可能接近 target，我们可以将 x 取为 sqrt(target)。

因为最多只需要遍历一次 0\~sqrt(target)，所以时间复杂度为 O(sqrt(target))。又因为只使用了两个额外的变量，因此空间复杂度为 O(1)。

```java
 
 public boolean judgeSquareSum(int c) {
        long i=0,x=(long)Math.sqrt(c);//注意整理括号中的long不能省略，因为Math.sqrt(c)是double类型，不会隐式转换为long类型
        while(i<=x){
            long sqrtsum=i*i + x*x;//不用long的话会溢出
            if(sqrtsum<c){
                i++;
            }else if(sqrtsum>c){
                x--;
            }else{
                return true;
            }
        }
        return false;

    }
```

## 3. 反转字符串中的元音字符

345\. Reverse Vowels of a String (Easy)

[Leetcode](https://leetcode.com/problems/reverse-vowels-of-a-string/description/) / [力扣](https://leetcode-cn.com/problems/reverse-vowels-of-a-string/description/)

```html
Given s = "leetcode", return "leotcede".
```

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/a7cb8423-895d-4975-8ef8-662a0029c772.png" width="400px"> </div><br>

使用双指针，一个指针从头向尾遍历，一个指针从尾到头遍历，当两个指针都遍历到元音字符时，交换这两个元音字符。

为了快速判断一个字符是不是元音字符，我们将全部元音字符添加到集合 HashSet 中，从而以 O(1) 的时间复杂度进行该操作。

- 时间复杂度为 O(N)：只需要遍历所有元素一次
- 空间复杂度 O(1)：只需要使用两个额外变量

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/ef25ff7c-0f63-420d-8b30-eafbeea35d11.gif" width="400px"> </div><br>

```java
public String reverseVowels(String s) {
        int length=s.length();
        char[] charArray = s.toCharArray();
        int left=0,right=length-1;
        while(left<right){
           while(left<length&&!notinyuan(charArray[left])){//不要忘记left<length和right>0条件，因为可能会超出范围；这里是while不能用if
               left++;
           }while(right>0&&!notinyuan(charArray[right])){
               right--;
           }
           if(left<right){
               swap(charArray,left,right);//这里要传入数组和下表，不能charArray[left]，charArray[right],这样传入的是字符，不能改变原数组
               left++;
               right--;
           }
        }        
        return new String(charArray);        
    }
    public static boolean notinyuan(char c){
        return "aeiouAEIOU".indexOf(c)>=0;
    }
    public static void swap(char[] charArray,int left,int right){
        char temp=charArray[left];
        charArray[left]=charArray[right];
        charArray[right]=temp;
    }
```

## 4. 回文字符串

680\. Valid Palindrome II (Easy)

[Leetcode](https://leetcode.com/problems/valid-palindrome-ii/description/) / [力扣](https://leetcode-cn.com/problems/valid-palindrome-ii/description/)

```html
Input: "abca"
Output: True
Explanation: You could delete the character 'c'.
```

题目描述：可以删除一个字符，判断是否能构成回文字符串。

所谓的回文字符串，是指具有左右对称特点的字符串，例如 "abcba" 就是一个回文字符串。

使用双指针可以很容易判断一个字符串是否是回文字符串：令一个指针从左到右遍历，一个指针从右到左遍历，这两个指针同时移动一个位置，每次都判断两个指针指向的字符是否相同，如果都相同，字符串才是具有左右对称性质的回文字符串。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/fcc941ec-134b-4dcd-bc86-1702fd305300.gif" width="250px"> </div><br>

本题的关键是处理删除一个字符。在使用双指针遍历字符串时，如果出现两个指针指向的字符不相等的情况，我们就试着删除一个字符，再判断删除完之后的字符串是否是回文字符串。

在判断是否为回文字符串时，我们不需要判断整个字符串，因为左指针左边和右指针右边的字符之前已经判断过具有对称性质，所以只需要判断中间的子字符串即可。

在试着删除字符时，我们既可以删除左指针指向的字符，也可以删除右指针指向的字符。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/db5f30a7-8bfa-4ecc-ab5d-747c77818964.gif" width="300px"> </div><br>

```java
 public boolean validPalindrome(String s) {
        int length=s.length();
        int left=0,right=length-1;
        char arr[]=s.toCharArray();//这个题和上一个不同，上一个元素要交换位置，这个只需要判断相不相等，所以这个不用数组也可以，参考原作者或者力扣
        while(left<right){
            while(left<length&&right>0&&arr[left]==arr[right]){//不能省略left<length&&right>0&&因为aba的话判断完b=b之后left会变成3，right会变成-1
                left++;
                right--;
            }
            return ispali(s,left,right-1)||ispali(s,left+1,right);
        }
        return true;
    }
    public static boolean ispali(String s,int i,int j){
        while(i<j){
            if(s.charAt(i++)!=s.charAt(j--)){
                return false;
            }
        }
        return true;
    }
```

## 5. 归并两个有序数组

88\. Merge Sorted Array (Easy)

[Leetcode](https://leetcode.com/problems/merge-sorted-array/description/) / [力扣](https://leetcode-cn.com/problems/merge-sorted-array/description/)

```html
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```

题目描述：把归并结果存到第一个数组上。

需要从尾开始遍历，否则在 nums1 上归并得到的值会覆盖还未进行归并比较的值。

```java
 public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1, j = n - 1;
        int tail = m + n - 1;        
        while (i >= 0 && j >= 0) {
            if (nums1[i] >= nums2[j]) {
                nums1[tail] = nums1[i];
                tail--;
                i--;
            } else {
                nums1[tail] = nums2[j];
                tail--;
                j--;
            }
        }//一开始写到这，没写下面的，nums1 = [0], m = 0, nums2 = [1], n = 1测试样例通不过，因为这种情况没有考虑nums是空数组的情况 
        // 将 nums2 剩余的元素复制到 nums1 中
        while (j >= 0) {
            nums1[tail] = nums2[j];
            tail--;
            j--;
        }
    }
```

## 6. 判断链表是否存在环

141\. Linked List Cycle (Easy)

[Leetcode](https://leetcode.com/problems/linked-list-cycle/description/) / [力扣](https://leetcode-cn.com/problems/linked-list-cycle/description/)

使用双指针，一个指针每次移动一个节点，一个指针每次移动两个节点，如果存在环，那么这两个指针一定会相遇。

```java
public boolean hasCycle(ListNode head) {
        if(head==null||head.next==null){
            return false;
        }
        ListNode slow =head;
        ListNode fast=head.next;
        while(slow!=fast){
            if(fast==null||fast.next==null){
                return false;
            }
            slow=slow.next;
            fast=fast.next.next;
        }
        return true;
        
    }
```

## 7. 最长子序列

524\. Longest Word in Dictionary through Deleting (Medium)

[Leetcode](https://leetcode.com/problems/longest-word-in-dictionary-through-deleting/description/) / [力扣](https://leetcode-cn.com/problems/longest-word-in-dictionary-through-deleting/description/)

```
Input:
s = "abpcplea", d = ["ale","apple","monkey","plea"]

Output:
"apple"
```

题目描述：删除 s 中的一些字符，使得它构成字符串列表 d 中的一个字符串，找出能构成的最长字符串。如果有多个相同长度的结果，返回字典序的最小字符串。

通过删除字符串 s 中的一个字符能得到字符串 t，可以认为 t 是 s 的子序列，我们可以使用双指针来判断一个字符串是否为另一个字符串的子序列。

```java
  public String findLongestWord(String s, List<String> dictionary) {
        String res="";
        for(int i=0;i<dictionary.size();i++){
            String tnew=dictionary.get(i);//可以看官网解答，for循环的写法，注意这种写法是size(),不是length()
            int t1=0,s1=0;
            while(t1<tnew.length()&&s1<s.length()){
                while(t1<tnew.length()&&s1<s.length()&&s.charAt(s1)==tnew.charAt(t1)){
                    t1++;
                    s1++;
                }
                s1++;
            }
            if(t1==tnew.length()){
                if(tnew.length()>res.length()||(tnew.length()==res.length()&&tnew.compareTo(res)<0)){
                    res=tnew;
                }
            }
        }
        return res;
    }
```

