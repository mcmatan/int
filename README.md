# Interview Algorithm Cheat Sheet

##

Array operations:
Creating an array from indexes 1 to 3 we would use slice
```javascript
array.slice(1,4)
```
Slice operates from index inclusive to an exclusive index. When calling slice(0,1) we will get only 0. Same as when calling slice(1,2) we will receive only 1

## Monotonic Stack

A monotonic stack is a data structure that maintains elements in either strictly increasing or strictly decreasing order. The fundamental insight is that when we violate this order (e.g., pushing a smaller element onto an increasing stack), we can perform meaningful calculations using the elements being removed.

### Key Characteristics
- Each element is pushed and popped exactly once, resulting in O(n) time complexity
- Particularly effective for finding relationships between array elements (next greater/smaller elements, spans, ranges)

### Example Problems
1. **Largest Rectangle in Histogram**
    - Maintain a stack of increasing heights
    - When encountering a smaller height, calculate areas for all larger heights being popped

2. **Daily Temperatures**
    - Maintain a stack of decreasing temperatures
    - When encountering a warmer temperature, update waiting days for all colder temperatures being popped

### Common Applications
- Finding next greater/smaller element
- Calculating ranges and spans
- Identifying the first element that is greater/smaller
- Computing areas under specific constraints

## Binary Search

Binary search is a divide-and-conquer algorithm designed to find elements in sorted collections with O(log n) time complexity.

### Core Concepts
- Repeatedly divide the search interval in half
- Compare the target with the middle element
- Adjust the search space based on the comparison
- Continue until the element is found or the search space is exhausted
- Maintain the loop while the left pointer is less than or equal to the right pointer

### Example Problem
1. **Koko Eating Bananas**
    - Create a range of possible eating speeds
    - Binary search through the range to find the minimum viable speed
    - For each speed tested, calculate whether it's sufficient
    - Continue searching until the pointers cross
    - Return the left pointer value as the optimal solution

### Standard Template
```javascript
const binarySearch = (nums, target) => {
    let left = 0;
    let right = nums.length - 1;

    while (left <= right) {
        const mid = Math.floor((left + right) / 2);

        if (nums[mid] === target) return mid;
        if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }

    return left; // For insertion point
    // return -1; // For exact match only
}
```

### Advanced Techniques
- When searching for a value that may not exist and you need the closest match:
```javascript
while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    // Save the current value if it's less than or equal to the target
    // Continue searching to ensure it's the closest match
    if (existing[mid].timestamp <= timestamp) {
        res = existing[mid].value;
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}
```

- For problems like "Median of Two Sorted Arrays":
    - Iterate through one array while calculating the corresponding position in the second array
    - Use separator indices instead of value indices since one array might contain all target values
    - Right pointer starts at array length (not length - 1) when using separator indices
    - Handle edge cases by treating values beyond array bounds as infinity or negative infinity

## Linked List

### Key Operations
- Traversal: Use `current = current.next`
- Reversal: Utilize `prev` and `current` pointers, with `prev` initially null until it receives the final value

### Considerations
- Removing a node at index zero requires special handling
- When storing nodes inside a map, use a native Map object for proper functionality
- Calling .next on null throws an error. Always check if head/current exists before accessing .next

```javascript
while (cursor) {
    const cloned = map.get(cursor);
    // Check if next exists before accessing
    if (cursor.next) {
        cloned.next = map.get(cursor.next);
    }
}
```

### List Reversal Template
```javascript
const reverseList = function(head) {
    if (!head || !head.next) {
        return head;
    }

    let prev = null;
    let current = head;
    while (current) {
        const tempNext = current.next;
        current.next = prev;
        prev = current;
        current = tempNext;
    }

    return prev;
}
```

### Floyd's Cycle-Finding Algorithm (Floyd's Tortoise and Hare)
- Uses two pointers: a slow pointer moving one step and a fast pointer moving two steps
- If there's a cycle, the pointers will eventually meet
- Finding the cycle entry point:
  A. Execute fast and slow pointers (fast +2, slow +1) until they meet
  B. Keep slow at current position, create another slow pointer at head, move both until they meet
  C. This works because mathematically, the distance from meeting point to cycle entry equals the distance from start to cycle entry
- This algorithm can detect duplicates in an array when all numbers are within bounds

#### Cycle Detection Template
```javascript
const hasCycle = function(head) {
    if (!head || !head.next) return false;

    let slow = head;
    let fast = head.next;

    while (fast && fast.next) {
        if (fast === slow) return true;
        slow = slow.next;
        fast = fast.next.next;
    }

    return false;
}
```

- When measuring a length of a range within a linked list, we should verify next exits before extending or else we would end up having right side as null

```javascript
        let left = head; 
        let right = head; 
        let count = 1;
        while (count < k && right.next) { // We make surer right.next exist within K range in order to return a none null right even if K is lower. 
            right = right.next;
            count++ 
        }
```


### Advanced Applications
- Problems like "Reorder Linked List" [0, n-1, 1, n-2, 2, n-3, ...] might seem to require O(n) space for reverse traversal
- However, using Floyd's algorithm, you can perform in-place reordering by working backwards from the middle of the list, which does not require extra space

### LRU Cache
- Updates most recently used items on new insertions AND get operations
- On existing values, updates both the value and marks as most recently used
- The key implementation details include:
    - Handling case with no first node
    - Handling case with no last node
    - Setting last.next as null and updating prev pointers correctly
    - Maintaining both first and last pointers accurately

```javascript
class Node {
    constructor(key, val) {
        this.key = key;
        this.val = val;
        this.next = null;
        this.prev = null;
    }
}

class LRUCache {
    constructor(capacity) {
        this.capacity = capacity;
        this.size = 0;
        this.first = null;  // least recently used
        this.last = null;   // most recently used
        this.map = new Map();
    }

    get(key) {
        const node = this.map.get(key);
        if (!node) return -1;

        this.moveToEnd(node);
        return node.val;
    }

    moveToEnd(node) {
        if (node === this.last) return;

        // Remove from current position
        if (node === this.first) {
            this.first = node.next;
            this.first.prev = null;
        } else {
            node.prev.next = node.next;
            node.next.prev = node.prev;
        }

        // Add to end
        this.last.next = node;
        node.prev = this.last;
        node.next = null;
        this.last = node;
    }

    put(key, value) {
        const existingNode = this.map.get(key);

        if (existingNode) {
            existingNode.val = value;
            this.moveToEnd(existingNode);
            return;
        }

        const newNode = new Node(key, value);
        this.map.set(key, newNode);

        if (this.size === 0) {
            this.first = newNode;
            this.last = newNode;
        } else {
            this.last.next = newNode;
            newNode.prev = this.last;
            this.last = newNode;
        }

        if (this.size === this.capacity) {
            this.map.delete(this.first.key);
            this.first = this.first.next;
            if (this.first) {
                this.first.prev = null;
            }
        } else {
            this.size++;
        }
    }
}
```

### Merge sort
- Splitting in half multiple times until left with the smallest denominator, in some cases that would be a single element, other, a single sorted list
- Then we merge using left to right comparison of sorted arrays
- Very good for linked lists since operations of adding is O(1) 

In the question "Merge K Sorted Linked Lists" we use merge sort to merge multiple already sorted arrays.
If usually such an operation would take N * K, with merge sort it gows down to N log K since we are only merging halfs, so much less operations in total.
Also space is pretty officiant with N Log K 

```javascript

class Solution {
    mergeSort(listA, listB) {
        if (!listA) return listB;
        if (!listB) return listA;

        let initial = new ListNode(null, null);
        let res = initial;

        while (listA && listB) {
            if (listA.val < listB.val) {
                initial.next = listA;
                initial = initial.next;
                listA = listA.next;
            } else {
                initial.next = listB;
                initial = initial.next;
                listB = listB.next;
            }
        }

        if (listA) initial.next = listA;
        if (listB) initial.next = listB;

        return res.next;
    }
    
    divide(lists, left, right) {
        if (right < left) return null;
        if (left === right) return lists[left];

        const length = right - left;
        const half = left + Math.floor(length / 2); // we must start from the left or else we would be pointing at a much to earlier 

        const leftMerge = this.divide(lists, left, half);
        const rightMerge = this.divide(lists, half +1, right);

        return this.mergeSort(leftMerge, rightMerge);        
    }

    mergeKLists(lists) {   
        return this.divide(lists, 0, lists.length -1)
    }
}

```

## Binary tree

- Binary tree "depth" is the longest path from root to bottom, the "breadth" is the width
- Traversing would be via recursive or stack, both usually take O(Height) space
- Traversing is normally left to right
- Level order traversal is one that needs a queue
- Global state often managed via closure variables in nested functions
- Bottom-up calculations: Child nodes return values to parent nodes
- Top-down calculations: Parent passes accumulated values to children
- Balanced tree: Height difference between left and right subtrees is at most one (on every)
- If a tree is not balanced recursive iteration could take O(N) in the worst case
- When creating a hash representation of tree structure, we have to make sure to specify which are each nodes left and right, or else could have collisions

```javascript
    const hash = `(L:${getHash(node.left)},V:${node.val},R:${getHash(node.right)})`;
    // and not
    const hash = `${getHash(node.left)}:${node.val}${getHash(node.right)}`;

```

Since there would have the same value

```markdown
10
/
5
 \
  4

10
 \
  4
   \
    5
```

Given a tree:

```markdown
        1
      /  \
    2      3
   / \    / \
   4 5   6   7
```

In order traversal would mean visiting left, middle and right 

4, 2, 5, 1, 6, 3, 7

Pre order would mean visiting root before (Notice values are printed before the in order which would start from the bottom right)

1, 2, 4, 5, 3, 6, 7

Post order traversal would mean visiting middle last

4, 5, 2, 6, 3, 7, 1

Level order traversal is normally using a queue

1, 2, 3, 4 ,5, 6, 7

### Edges and Nodes

- Edges are the lines connecting nodes
- Nodes are the values within the tree

In the following example there is one edge and two notes
```markdown
    A
   /
  B
```

### Balanced Binary Tree

```javascript
    var isBalanced = function(root) {
        // global variable
        let isBalanced  = true;
        var helper = function(node) {
            if (!node) return 0;
            // call left and right
            const left = helper(node.left);
            const right = helper(node.right);
         
            const min = Math.min(left, right);
            const max = Math.max(left, right);
            // run internal calculation while updating global variable
            if (max - 2 >= min) isBalanced = false;
            // return max left or right while adding additioanl + 1
            return Math.max(left, right) + 1;
        }
        helper(root);
        return isBalanced;
    }
```

### Level traversal

There is a trick using a queue & a for-loop. The queue represents the "next level", but could contain also the "next next" level while we iterate over level
Adding next. For that we use a for loop which remembers how many nodes are in the  current level.

```javascript
 var levelOrder = function(root) {
        if (!root) return [];
        let stack = [root];
        let res = [] 

        while (stack.length) {
            const count = stack.length; 
            let resNextLevel = []
            for (let i = 0; i < count; i ++) {
                const current = stack.shift();
                resNextLevel.push(current.val); 
                if (current.left) stack.push(current.left); 
                if (current.right) stack.push(current.right);             
            }
            res.push(resNextLevel);
        }

        return res;
    }
```

### Binary search tree

- A binary search tree is a binary tree where the left child is less than the parent and the right child is greater than the parent
- When validating a tree is valid, we must pass the min and max values to each node, since checking only with direct siblings could return true,
While in the bigger picture it would be false (see 3 is not bigger than 5). Every node should check if under the current branching min/max.

```markdown
     5
    / \
   4   6
      / \
     3   7
```

- Construct Binary Tree from Preorder and Inorder Traversal: we are using the fast preorder first node is always the root.
Than we create a root node, and set left and right side to recursive calls, while splitting in order via searching the root we found,
And spliting the preorder using the count from pre order 

```markdown
              1
             /. \
            2.   3
          /  \.  / \
          4.  5 6.  7
         /
        8

preorder =    [1, 2, 4, 8, 5, 3, 6, 7]
inorder =     [8, 4, 2, 5, 1, 6, 3, 7]

   preorder = [1, 2, 4, 8, 5, 3, 6, 7] -> First one is root
               ^
inorder =     [8, 4, 2, 5,] 1 [ 6, 3, 7]
                            ^
```

```javascript
    buildTree(preorder, inorder) {
        var helper = function(preorderStart, preorderEnd, inorderStart, inorderEnd) {
            if (preorderStart > preorderEnd) return null;
            const node = new TreeNode(preorder[preorderStart]);
            let inorderIndex = 0;
            for (let i = inorderStart; i <= inorderEnd; i ++) {
                if (inorder[i] === preorder[preorderStart]) {
                    inorderIndex = i;
                }
            }
            const leftSideLength = inorderIndex - inorderStart;

            const newPreorderLeftEnd = preorderStart + 1 + leftSideLength - 1;
            node.left = helper(preorderStart + 1, newPreorderLeftEnd, inorderStart, inorderIndex - 1);
            node.right = helper(newPreorderLeftEnd + 1, preorderEnd, inorderIndex + 1, inorderEnd)

            return node;
        }
        return helper(0, preorder.length -1, 0, inorder.length - 1);
    }
```

### 297. Serialize and Deserialize Binary Tree

- We are to serialize and deserialize a binary tree. But there is a trick using pre order traversal which is not trivial.
Normally one would try solving this using level travers, but the serialization would be more expensive.
Here is an example how to do the simplified way:

```javascript
  var serialize = function(root) {
    if (!root) return 'N';

    return root.val + ',' + serialize(root.left) + ',' + serialize(root.right);
};
```
This would be a possibly output ''1,2,N,N,3,N,N'

And the deserialization would be:

```javascript
var deserialize = function(data) {
    if (data === '') return null;
    const arr = data.split(',');

    let index = 0; // we are using index rather than popping array for better permofmance

    var dfs = function() {
        if (index > data.length - 1) { // nothing to do when reaching the end
            return null;
        }
        if (arr[index] === 'N') { // we trast N as null, and moving index forwards
            index++;
            return null;
        }
        
        const node = new TreeNode(parseInt(arr[index]));
        index++;
        // same dfs as was constructed, pre order traversal
        node.left = dfs();
        node.right = dfs();

        return node;
    }

    return dfs();
};
```

Given the following string '1,2,N,N,3,N,N'

We would construct the 1, than 2, 2 left and right would be bull
Continue to 1 right side, 3, 3 left and right would be null