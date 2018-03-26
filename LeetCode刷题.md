
#LeetCode刷题
[TOC]
## 1.Add Two Numbers
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
You may assume the two numbers do not contain any leading zero, except the number 0 itself.

### Example
```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.

```

####Code

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     public var val: Int
 *     public var next: ListNode?
 *     public init(_ val: Int) {
 *         self.val = val
 *         self.next = nil
 *     }
 * }
 */

class Solution {
    func addTwoNumbers(_ l1: ListNode?, _ l2: ListNode?) -> ListNode? {
        let resultList = ListNode(0)
        var tempList = resultList
        var tempL1 = l1
        var tempL2 = l2
        var carry = 0
        
        var countA = 0
        var countB = 0
        
        if var tempL1 = l1 {
            while tempL1.next != nil {
                countA += 1
                tempL1 = tempL1.next!
            }
        }
        if var tempL2 = l2 {
            while tempL2.next != nil {
                countB += 1
                tempL2 = tempL2.next!
            }
        }
        
        //// 线性表长度比较
        
        if countA >= countB {
             tempL1 = l1
             tempL2 = l2
        }else {
             tempL1 = l2
             tempL2 = l1
        }
        
        while let L1 = tempL1 {
            if let L2 = tempL2 {
                tempList.val = ( L1.val + L2.val + carry ) % 10
                carry = ( L1.val + L2.val + carry ) >= 10 ? 1:0
                tempL1 = L1.next
                tempL2 = L2.next
                if tempL1 == nil {
                    if carry == 1 {
                         let node = ListNode(1)
                         node.next = nil
                         tempList.next = node
                         tempList = node 
                    }else {
                         tempList.next = nil
                    }
                } else {
                    let node = ListNode(0)
                    node.next = nil
                    tempList.next = node
                    tempList = node
                }
            }else {
                tempList.val = (L1.val + carry) % 10
                carry = (L1.val + carry) >= 10 ? 1:0
                tempL1 = L1.next
                if tempL1 == nil {
                    if carry == 1 {
                         let node = ListNode(1)
                         node.next = nil
                         tempList.next = node
                         tempList = node 
                    }
                }else {
                    let node = ListNode(0)
                    node.next = nil
                    tempList.next = node
                    tempList = node 

                }
            }
        }
        return resultList
    }
}
```

## 2.Longest Substring Without Repeating Characters
Given a string, find the length of the longest substring without repeating characters.

###Examples

```
Given "abcabcbb", the answer is "abc", which the length is 3.
Given "bbbbb", the answer is "b", with the length of 1.
Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequenceand not a substring.

```

###Code

## 3.Reverse Integer
Given a 32-bit signed integer, reverse digits of an integer.

###Examples
####Example 1:

```
Input: 123
Output:  321

```
####Example 2:
```
Input: -123
Output: -321

```
####Example 3:

```
Input: 120
Output: 21

```
###Code

```c
int reverse(int x) {
    /// 使用长整形保证了在运算过程中不会溢出 计算完成后在与临界值比较 判断是否溢出
    long long var = 0;
    while (x != 0) {
        var = var * 10 + x % 10;
        if (var > INT_MAX || var < INT_MIN) {
            return 0;
        }
        x /= 10;
    }
    return var;
}

```

## 4.Merge Two Sorted Lists
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

### Examples

```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4

```
###Code
方法一:使用迭代的方式

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {
    /// 定义头
    struct ListNode temp = {0 , NULL};
    /// 定义头指针
    struct ListNode * head = &temp;
    struct ListNode * point = head;
    while (l1 != NULL && l2 != NULL) {
        if (l1->val <= l2->val) {
            point->next = l1;
            l1 = l1->next;
        }else {
            point->next = l2;
            l2 = l2->next;
        }
        point = point->next;
    }
    if (l1 != NULL) {
        point->next = l1;
    } else if (l2 != NULL){
        point->next = l2;
    }
    return head->next;
}
```
方法二:使用递归的方法
```
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {
    if (l1 == NULL) return l2;
    if (l2 == NULL) return l1;

    struct ListNode * handler = NULL;
    if (l1->val < l2->val) {
        handler = l1;
        handler->next = mergeTwoLists(l1->next, l2);
    }else {
        handler = l2;
        handler->next = mergeTwoLists(l1, l2->next);
    }
    return handler;
}
```





