# 字符串算法

1. to-lower-case
2. length-of-last-word
3. jewels-and-stones

### first-unique-character-in-a-string

给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

```
s = "leetcode"
返回 0

s = "loveleetcode"
返回 2
```

```
class Solution {
    func firstUniqChar(_ s: String) -> Int {

        /// 字符出现次数 hash 表
        var table: [Character: Int] = [:]

        /// 遍历
        for char in s {
            /// 是否添加过
            if table.keys.contains(where: { $0 == char }) {
                /// set value 次数 + 1 for char
                table[char] = table[char]! + 1
            }else {
                /// set value(次数 = 1)  for char
                table[char] = 1
            }
        }

        print(table)

        /// 再次遍历
        for (index, char) in s.enumerated() {
            /// 如果 get 次数 for char，次数 == 1，获取上一个索引，如果没有上一个则返回 -1
            if table[char]! == 1 {
                return index
            }
        }

        ///  如果都没有返回 -1
        return -1
    }
}
```

### longest-common-prefix

```
class Solution {
    func longestCommonPrefix(_ strs: [String]) -> String {

        // 保存最长公共子串
        var subStr = ""

        if strs.count == 0 { return subStr }
        if strs.count == 1 { return strs.first! } // 不存在

        else {

            // 临时保存公共子串
            var tmpSubStr = ""

            // 开始遍历第一个元素
            for (index, char) in strs.first!.enumerated() {

                // 前缀过滤
                var filterStrs = strs.filter({
                    $0.hasPrefix(tmpSubStr + String(char))
                })

                // 是的话更新 tmpSubStr，
                if filterStrs.count == strs.count {
                    tmpSubStr.append(char)
                }

                // 前缀判断
                if tmpSubStr == "" {
                    return ""
                }

                // 否则 tmpSubStr 和 subStr 进行比较，判断 subStr 是否比这个短，如果 index 是最后一个也走这个流程
                if index == strs.first!.count - 1 || filterStrs.count != strs.count {

                    // 是的话更新 subStr
                    // 否则不处理
                    if tmpSubStr.count > subStr.count {
                        subStr = tmpSubStr
                    }

                    return subStr

                }
            }

        }

        return subStr

    }
}

Solution.init().longestCommonPrefix(["flower", "flow", "flight"])

/// 题目: 最长公共子串
/// 输入: strs = ["flower", "flow", "flight"]
/// 输出: "fl"

/// 条件
/// 1 <= strs.length <= 200
/// 0 <= strs[i].length <= 200
/// strs[i] 仅由小写英文字母组成

/// 最长公共子串，也就是说数组中的任意一个字符都在其他元素中存在

/*

 // 保存最长公共子串
 var subStr = ""

 if strs.count <= 1 // 不存在

 else {

 // 临时保存公共子串
 var tmpSubStr = ""

 // 开始遍历第一个元素
 for (index, char) in strs.first!.enumerated() {

 // 前缀过滤

    // 是的话更新 tmpSubStr，如果 index 是最后一个也走 else 流程

    // 否则 tmpSubStr 和 subStr 进行比较，判断 subStr 是否比这个短（最长公共前缀，这里需要加个判断，如果  tmpSubStr == "" 直接返回不存在）

        // 是的话更新 subStr
        // 否则不处理
 }


 }

 return subStr
 */

```

### reverse-string

```
/*
    编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 s 的形式给出。

    不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题

 输入：s = ["h","e","l","l","o"]
 输出：["o","l","l","e","h"]

 */

class Solution {
    func reverseString(_ s: inout [Character]) {

        var begin: Int = 0
        var end: Int = s.count == 0 ? 0 : s.count-1

        // 不需要修改
        if end == 0 { return }

        // 直到 begin == end 或者 begin > end 则不用交换
        while begin < end {

            let tmp = s[begin]
            s[begin] = s[end]
            s[end] = tmp

            begin += 1
            end -= 1

        }


    }
}
```

### reverse-words-in-a-string

```
class Solution {
    func reverseWords(_ s: String) -> String {

        /// 利用空格分割字符串，得出字符串数组
        var words = s.split(separator: " ")

        /// 新建头尾指针
        var begin: Int = 0
        var end: Int = words.count == 0 ? 0 : words.count - 1

        /// 不需要翻转
        if end == 0 { return s }

        /// 元素翻转
        while begin < end {
            var tmp = words[begin]
            words[begin] = words[end]
            words[end] = tmp

            begin += 1
            end -= 1
        }

        // 拼接新的字符串
        var result: String = ""
        for (index, word) in words.enumerated() {
            result.append(String(word))
            if index != words.count - 1 {
                result.append(" ")
            }
        }

        return result
    }
}

// 题目: 翻转字符串里面的单词
// s = "the sky is blue"
// 输出："blue is sky the"

/*

{
    头尾两个指针，倒置替换 {
        1. 利用空格分割字符串，得出字符串数组
        2. 新建头尾指针，进行元素翻转
        3. 拼接新的字符串
    }
 }

 */
```

### vaild-palindrome
