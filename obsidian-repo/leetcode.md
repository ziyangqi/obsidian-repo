
#####  题目1 两数之和

```
class Solution {  
    public int[] twoSum(int[] nums, int target) {  
        // 新建立一个Map  
        Map<int,Object> list = new Map<int, Object>() ;  
        // 建立一个for循环  
        for (int i = 0; i < nums.length - 1; i++) {  
             for (int j = i+1; j < nums.length; j++) {  
                 if (nums[i] + nums[j] == target){  
                 eventNums[0] = i;  
                 eventNums[1] = j;  
        }  
    }  
}
        // 全部加载一起了组成一个新的Map  
  
  
    }  
}
```

// 这里怎么返回`num[i,j]`



声明一个数组```
```
int[] eventNums = new int[2];
```
问题 

这里的问题就是我 for循环的时候 我j 没有i+1 导致我给的都是错误的

```
for (int i = 0; i < nums.length - 1; i++) 
    for (int j = i+1; j < nums.length; j++)
```


###### 能不能就是复杂度小于0(n²)

```
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        // 创建一个HashMap来存储数组中的值和它们的索引
        Map<Integer, Integer> map = new HashMap<>();
        
        // 遍历数组
        for (int i = 0; i < nums.length; i++) {
            // 计算当前元素的补数
            int complement = target - nums[i];
            
            // 检查补数是否已经在Map中
            if (map.containsKey(complement)) {
                // 如果找到了补数，返回两个数的索引
                return new int[] { map.get(complement), i };
            } 
            // 如果补数不在Map中，将当前元素存入Map，key是值，value是索引
            map.put(nums[i], i);
        }
        // 如果没有找到满足条件的两个数，抛出异常或返回空数组
        throw new IllegalArgumentException("No two sum solution");
    }
}

```

##### 2 
java 怎么遍历链表

```
while(l1!=null){
li = l1.nextL
}
```

 ## 这个代码根本不是我会的
 
```
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {  
    ListNode dummyHead = new ListNode(0);  // 哨兵节点，简化操作  
    ListNode current = dummyHead;          // 当前处理的节点  
    int carry = 0;                         // 存储进位  
  
    // 当两个链表都有节点或有进位时  
    while (l1 != null || l2 != null || carry != 0) {  
        int x = (l1 != null) ? l1.val : 0;  // 如果l1链表没有节点了，值为0  
        int y = (l2 != null) ? l2.val : 0;  // 如果l2链表没有节点了，值为0  
        int sum = carry + x + y;            // 当前位的和，加上进位  
  
        carry = sum / 10;                   // 计算新的进位  
        current.next = new ListNode(sum % 10); // 将和的个位数作为新节点存入结果链表  
        current = current.next;             // 移动到下一个节点  
  
        // 移动到下一个链表节点  
        if (l1 != null) l1 = l1.next;  
        if (l2 != null) l2 = l2.next;  
    }  
  
    // 返回结果链表的头节点（跳过哨兵节点）  
    return dummyHead.next;  
  
}
```


##### 3 无重复字符串

首先字符串转化为数组
```
public class Main {
    public static void main(String[] args) {
        String str = "hello";
        
        // 将字符串转换为字符数组
        char[] charArray = str.toCharArray();
        
        // 输出字符数组
        for (char c : charArray) {
            System.out.print(c + " ");
        }
    }
}

```