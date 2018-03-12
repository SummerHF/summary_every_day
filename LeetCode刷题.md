#LeetCode刷题
## Add Two Numbers
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
        var carry = 0
        let resultNode = ListNode(0)
        var countA = 0 
        var countB = 0
        var temL1 = l1, temL2 = l2
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
        if countA >= countB {
             temL1 = l1
             temL2 = l2
        }else {
             temL1 = l2
             temL2 = l1
        }
        var tempNode = resultNode
        while let nodeOne = temL1 {
            let node = ListNode(0)
            if  let nodeTwo  = temL2 {
                let sum = nodeOne.val + nodeTwo.val
                tempNode.val = (sum % 10 ) + carry
                /// 是否产生了进位
                carry = sum >= 10 ? 1:0
                if nodeOne.next != nil || nodeTwo.next != nil  {
                    tempNode.next = node
                    tempNode = node
                    temL1 = nodeOne.next
                    temL2 = nodeTwo.next
                }else {
                    /// 为空并且产生了进位
                    if carry == 1 {
                        node.next = nil
                        node.val = carry
                        tempNode.next = node
                        tempNode = node
                    }
                    break 
                }
            }else {
                if nodeOne.next == nil && carry == 1 {
                    tempNode.val = (nodeOne.val + carry) % 10  
                    let node = ListNode(0)
                    if (nodeOne.val + carry) >= 10 {
                        carry = 1 
                        tempNode.next = node 
                        tempNode = node 
                    }else {
                        carry = 0 
                        tempNode.next = nil 
                    }
                }else {
                    tempNode.val = (nodeOne.val + carry) % 10 
                    tempNode.next = nodeOne
                    tempNode = nodeOne
                }
                temL1 = nodeOne.next 
            }
         }
        return resultNode
    }
}
```


