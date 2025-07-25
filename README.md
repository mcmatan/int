# Interview Algorithm Cheat Sheet

##

Array operations:
Creating an array from indexes 1 to 3 we would use slice
```javascript
array.slice(1,4)
```
Slice operates from index inclusive to an exclusive index. When calling slice(0,1) we will get only 0. Same as when calling slice(1,2) we will receive only 1

## Monotonic Stack (Missing example)

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

## Example: Largest Rectangle in Histogram

### Problem Description

Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

### Solution Approach

This solution uses a monotonic stack algorithm with O(n) time complexity to efficiently find the largest rectangle:

1. We maintain a stack of [height, index] pairs to track both the height and position of bars
2. For each bar in the histogram:
    - If it's taller than the previous bar, we simply add it to the stack
    - If it's shorter, we pop taller bars from the stack and calculate their maximum possible rectangle areas
3. We use a sentinel value of 0 at the end to ensure all bars are processed

### Code Implementation

```javascript
/**
 * Finds the largest rectangle area in a histogram
 * @param {number[]} heights - Array of non-negative integers representing heights of bars
 * @return {number} - Area of the largest rectangle
 */
function largestRectangleArea(heights) {
  // Stack stores pairs of [height, index] to track both the height and position
  let stack = [[heights[0], 0]];
  
  // Add a sentinel value of 0 at the end to ensure all bars are processed
  heights.push(0);

  // Keep track of the maximum area found
  let max = 0;

  // Iterate through the histogram, starting from the second bar
  for (let i = 1; i < heights.length; i++) {
    const current = heights[i];

    // If current height is greater than or equal to the top of stack,
    // we can just push it to maintain a non-decreasing stack
    if (current >= stack[stack.length - 1][0]) {
      stack.push([current, i]);
    } else {
      // If current height is smaller, we need to pop elements from stack
      // to maintain the monotonic property, calculating areas as we go
      
      // Save the rightmost index before popping
      let lastI = stack[stack.length - 1][1];
      
      // Keep popping taller bars from stack and calculate their areas
      while (stack.length && stack[stack.length - 1][0] > current) {
        const next = stack.pop();
        
        // Calculate area: height * width
        // Width is current index minus the starting index of the bar
        const area = next[0] * (i - next[1]);
        max = Math.max(max, area);
        
        // Keep track of the leftmost index we've seen
        // This allows us to extend the current bar to the left
        lastI = next[1];
      }

      // Push the current height, but with the leftmost index
      // This effectively extends the current bar to the left
      stack.push([current, lastI]);
    }
  }

  return max;
}
```

## Complexity Analysis

- **Time Complexity**: O(n) - Each element is pushed and popped from the stack at most once
- **Space Complexity**: O(n) - In the worst case, the stack could contain all elements

## Example

For the input `[2, 1, 5, 6, 2, 3]`:
1. The algorithm processes each bar and maintains the monotonic stack
2. When reaching the second `2` (after `6`), it calculates areas for the `6` and `5` bars
3. The largest rectangle has an area of 10 (height 5 × width 2)

## Key Insight

The key insight is that for any bar, the largest rectangle containing it extends to the nearest shorter bars on both sides. The monotonic stack efficiently helps us find these boundaries.

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

- For problems like "Median of Two Sorted Arrays": (Missing example)
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

----------------------


## Min heap


- A min heap is a binary tree where the parent is always smaller than the children
- Accessing parent is using Math.floor((i - 1) / 2)
- Accessing left child is using 2 * i + 1
- Accessing right child is using 2 * i + 2
- Given a question we need to find the smallest x values in a list of length y, a use of a min heap would be
y log y, but actually, if using a max heap, we could make sure the heap is always of length x and that way deduct time
complexity to y log x. Pushing values and every time we have more than x values, pop the next max (staying with min values)
- Building a heap is O(n) since we are iterating over all values and heapifying up. This is counterintuitive since every operation is log n, but actually
We are doing it in a way that we are not doing it for every value, but only for the ones that are needed to be moved up which terns out to be N.


```javascript
class MinHeap {
    constructor() {
        this.heap = [];
    }

    swap(a, b) {
        const tempA = this.heap[a];
        this.heap[a] = this.heap[b];
        this.heap[b] = tempA;
    }

    heapifyUp() {
        let index = this.heap.length -1;

        while (index > 0) {
            const parentIndex = Math.floor((index - 1) / 2);
            if (this.heap[parentIndex] < this.heap[index]) break;

            this.swap(parentIndex, index);
            index = parentIndex;
        }
    }

    insert(value) {
        if (!this.heap.length) {
            this.heap.push(value);
            return;
        }

        this.heap.push(value);
        this.heapifyUp();
    }

    heapifyDown() {
        let index = 0;

        while (index < this.heap.length) {
            const leftChild = index * 2 + 1;
            const rightChild = index * 2 + 2;

            let minIndex = index;

            // Notice we are comparing both to each other by using minIndex. We will pick the lowest child 
            if (leftChild < this.heap.length && this.heap[leftChild] < this.heap[minIndex]) {
                minIndex = leftChild;
            }
            if (rightChild < this.heap.length && this.heap[rightChild] < this.heap[minIndex]) {
                minIndex = rightChild;
            }

            if (minIndex === index) break;

            this.swap(minIndex, index);
            index = minIndex;
        }
    }

    extractMin() {
        if (!this.heap.length) return null;
        if (this.heap.length === 1) return this.heap.pop();

        this.swap(0, this.heap.length -1);
        const removed = this.heap.pop();
        if (this.heap.length === 1) return removed;

        this.heapifyDown();

        return removed;
    }

    peek() {
        if (!this.heap.length) return null;
        return this.heap.length[0];
    }
}
```

## Bucket count "Last stone weight"

- We are needed to find the highest stones each time and negate or remove them
- Because of the constraint of max size of stone, we are able to instead of using a heap which
Would be n log n, we use buckets correlated to size of stones, and find the heights one each time
In constant time.

```javascript
var lastStoneWeight = function(stones) {
    if (!stones.length) return null;

    let max = stones[0]; // 6
    for (let stone of stones) {
        max = Math.max(max, stone);
    }

    let bucket = new Array(max + 1).fill(0);
    for (let stone of stones) {
        bucket[stone] ++;
    }

    let first = max; // 3

    while (first > 0) {
        if (bucket[first] > 1) {
            bucket[first] -= 2;
            continue;
        }

        while (first > 0 && bucket[first] === 0) first--;
        if (bucket[first] > 1) {
            bucket[first] -= 2;
            continue;
        }

        let second = first - 1; // 2
        while (second > 0 && bucket[second] === 0) second --;

        if (second === 0) return first;

        bucket[first - second] ++;
        bucket[first] --;
        bucket[second] --;
    }

    return 0;
};
```

### Finding the closes point to the origin

- Calculating distance between two points is using sqrt((x1 - x2)^2 + (y1 - y2)^2)
- Finding from 0,0 in javascript would simply be   Math.pow(value[1], 2) + Math.pow(value[0], 2);


## Quick sort / select

- Quick sort is essentially pivot sort. Choosing a random number and making sure it’s left and right are smaller and bigger, so it’s in the right place in the array, than doing the same for left and right
- Quick sort avr time complexity is N log N, because if we manage to pick a pivot that splits in half the array, we are sorting half each time.
- But if not, we will continue sorting the same size -1 which would become N ^ 2
- Quick select is a method of sorting only one relevant side each time, for example when searching for largest X number.
- Quick select avr time complexity is N since we split to have and sort only one half. This is not log n like binary search because in binary search at each step we just split to have (constant time) but here we actually iterate over all array elements at each level. It’s actually N + Log N but we removing the Log N since smaller than N.

```javascript
function partition(arr, left, right) {
    let pivot = right;
    let leftBorder = left - 1; 

    // We want to move smaller elements to the left
    for (let i = left; i < right; i ++ ) { // we move until the pivot number
        if (arr[i] < arr[pivot]) {
            leftBorder ++;
            [arr[i], arr[leftBorder]] = [arr[leftBorder], arr[i]];
        }
    }

    [arr[leftBorder + 1], arr[pivot]] = [arr[pivot], arr[leftBorder + 1]];
    return leftBorder + 1;
}


function quickSort(arr, left, right) {
    if (arr.length === 1) return arr;
    if (left >= right) return arr;
    
    const pivot = partition(arr, left, right);

    quickSort(arr, left, pivot - 1);
    quickSort(arr, pivot + 1, right);
    return arr;
}
```

### Quick select

```javascript
function quickSelect(arr, left, right, k) {
    if (left === right) return arr[left];

    const pivot = partition(arr, left, right);

    if (pivot === k) return arr[pivot];
    if (pivot < k) return quickSelect(arr, pivot + 1, right, k);
    return quickSelect(arr, left, pivot - 1, k);
}
```

## Subsets

- Finding all possible none duplicate subsets is a recursive operation that at each point we decide rather we want to add or not add the number
- Because we choose if to add or not, it's 2 ^ N since we have 2 options for each number. But because in this case we are also constructing an array which is 
a N operation, we are actually N * 2 ^ N

```javascript
var subsets = function(nums) {
        let res = [[]]
        let helper = function(start, arr) {
            for (let i = start; i < nums.length; i ++) {
                let next = [...arr]
                next.push(nums[i]);
                res.push(next);
                helper(i + 1, next)
            }
        }

        helper(0, []);
        return res;
};
```

## Combination Sum (understanding time complexity)

- Given a list of numbers and a target, we are to find all possible combinations that would sum up to the target
- Numbers can be repeated, for example, [2, 3, 6, 7] and target 7 would have [2, 2, 3] and [7]

In order to solve this, we iterate on all numbers while calling recursively passing current index onwards.
This may seem like a N^N time complexity, but actually we should look at each branch number of decisions.
We may start with N branches, but each branch only has 2 decisions, rather to add current number or not.

2^(N-1) + 2^(N-2) + 2^(N-3) + ... + 2^0

For that reason the time complexity is 2^N

```javascript
function combinationSum(nums, target) {
        let res = [];

        let helper = function(index, arr, sum) {
            // because we pass the index it's a 2 ^ N and not n^n
            for (let i = index; i < nums.length; i++) {
                const num = nums[i];

                if (num + sum > target) continue;
                if (num + sum === target) {
                    arr.push(num);
                    res.push([...arr]);
                    arr.pop();
                    continue;
                }

                arr.push(num);
                helper(i, arr, sum + num);
                arr.pop();
            }
        } 

        helper(0, [], 0);

        return res;
    }
```

## Combination Sum II (And how to skip duplicates)

In this case, we are to find all possible combinations that would sum up to the target, but we are not allowed to use the same number twice.
The tricky case is given this input 1,2,2,2,5 and target 5, we should have return [1,2,2] twice. Just once. 
In order to do so, we sort the input, and after a recursive call OR a final solution we skip all duplicates.

If we would not skip duplicates after adding to res array, we would move 1 index forward that could contain the same number again.

`Example: 1,2,2,2,5 in the case after adding 1,2,2 we do not want to continue to the index 3 2`

If we would not skip duplicates after each recursive call, we would have the same problem but in the middle or start of the result.

`Example:  1,2,2,2,5 after adding 2,2 5 we do not want to add another 2,2,5`

Two different cases, two are must.

```javascript
 function combinationSum2(candidates, target) {
        let res = [];
        candidates = candidates.sort((a,b) => a - b);

        let helper = function(arr, index, sum) {
            for (let i = index; i < candidates.length; i ++) {
                const num = candidates[i];

                if ((sum + num) === target) {
                    arr.push(num);
                    res.push([...arr]);
                    arr.pop();
                    // We skip here 
                    while (candidates[i + 1] === candidates[i]) i++;
                    continue;
                }

                if ((sum + num) > target) continue;

                arr.push(num);
                helper(arr, i + 1, sum + num);
                arr.pop(); 
                // And skip here
                while (candidates[i + 1] === candidates[i]) i++;
            }
        }

        helper([], 0, 0);

        return res;
    }
```

Alternatively (and more recommended), we could skip checking current is not equal previous using the passed index.
Only check if the i is bigger than index and by this guaranteeing number only shows once per index.
We allow picking a chain of numbers once, for example 1,2,2,2,2,3 and the next time all 2 would be skipped.

```javascript
function combinationSum2(candidates, target) {
        let res = [];
        candidates = candidates.sort((a,b) => a - b);

        let helper = function(arr, index, sum) {
            for (let i = index; i < candidates.length; i ++) {
                // This is the trick here
                if (i > index && candidates[i -1] === candidates[i]) continue;

                const num = candidates[i];

                if ((sum + num) === target) {
                    arr.push(num);
                    res.push([...arr]);
                    arr.pop();
                    continue;
                }

                if ((sum + num) > target) continue;

                arr.push(num);
                helper(arr, i + 1, sum + num);
                arr.pop(); 
            }
        }

        helper([], 0, 0);

        return res;
    }
```


This is the trick of skipping duplicates

```
 if (i > index && nums[i -1] === nums[i]) continue;
```

## Permutations (without copying arrays)

- Given a list of numbers, we are to find all possible permutations
Example:
- 
Input: nums = [1,2,3]

Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

The most trivial way to solve this would be iterating while creating new arrays with the missing index and the new permutation array.
But this would add to the time complexity since we are creating new arrays each time.
Instead, we can iterate while swapping the order of items in the array and then swapping back to the original order.

```javascript
    function permute(nums) {
        let res = [];

        let helper = function(index) {
            if (index === nums.length) {
                res.push([...nums]);
                return;
            }
            for (let i = index; i < nums.length; i ++) {
                swap(nums, i, index);
                helper(index + 1); 
                swap(nums, i, index);
            }
        }

        helper(0)

        return res;
    }
```

The time complexity in this case seems like N^N but we are actually have N-1 at branching at each level, so it's N!
Notice this last question time complexity is MUCH higher than the previous ones, since we are actually creating N! arrays.

## Subsets II (Understanding time complexity)

Example input and output 

```
Input: nums = [1,2,1]

Output: [[],[1],[1,2],[1,1],[1,2,1],[2]]
```

Actual implementation:

```javascript
  function subsetsWithDup(nums) {
        let res = [];
        nums = nums.sort((a, b) => a - b);

        let helper = function(index, arr) {
            res.push([...arr]);
            for (let i = index; i < nums.length; i ++) {
                if (i > index && nums[i -1] === nums[i]) continue;
                const num = nums[i];
                arr.push(num);
                helper(i + 1, arr);
                arr.pop();
            }
        }

        helper(0, []);

        return res;
    }
```

It may seem like we have N options at each level because of the for loop, but we actually have just 2.
If we visualize it, it looks like so: (for [1,2,3])

The total time complexity is 2^n * n (since we are creating an array at each level)

<img src="./images/2n-recursive.png">

## Palindrome Partitioning (and how to calculate all possible partitions)

Example: Input: s = "aab"

Output: [["a","a","b"],["aa","b"]]

The question was tricky to me at first, because I did not understand as long as we calculate all polindrom from
i to j, and call recursive, all of them would be found.

If we have a , b , c, d, d, c

As long as we try every match

a √
ab x
abc x
....

then do the same for

a √
b ∫
cddc √

We will find all possible partitions

```javascript
function isPolindrom(s, start, end) {
    if (start >= end) return true;
    if (s[start] !== s[end]) return false;
    return isPolindrom(s, start +1, end -1);
}

function partition(s) {
    let res = [];
    
    let helper = function(index, arr) {
        if (index >= s.length) {
            res.push([...arr]);
            return;
        }
        for (let i = index; i < s.length; i ++) {
            if (isPolindrom(s, index, i)) {
                arr.push(s.substring(index, i + 1));
                helper(i + 1, arr);
                arr.pop();
            }
        }
    }

    helper(0, []);

    return res;
}
```

### N Queens

- Given a board of size N x N, we are to find all possible ways to place N queens on the board

<img src="./images/n-queen.png">

This is a classic situation of time complexity n! since at each level we have n-1 options.
n -1, n-2, n-3....

### Design Add and Search Word Data Structure (Trie search time complexity)

Examples 

wordDictionary.addWord("day");
wordDictionary.addWord("bay");
wordDictionary.addWord("may");
wordDictionary.search("say"); // return false
wordDictionary.search("day"); // return true
wordDictionary.search(".ay"); // return true
wordDictionary.search("b.."); /
/ return true

The time complexity is 26^m where m is the length of the word, because on each level we have 26 characters possibly
and every time we have another 26 possibilities as long as there are * in the word.


# Graphs

## Cloning graphs:

### Recursive

We are creating nodes or returning existing clone, then calling recursive on each child

```javascript
function cloneGraph(node) {
        // base case important to remember or else ".val" of undefined will throw
        if (!node) return node;
           let map = {};

    let helper = function(currentNode) {
        if (map[currentNode.val] !== undefined) return map[currentNode.val];

        const nodeClone = new Node(currentNode.val);
        // this is important, to set fast, or else it would be infinite recursion
        map[currentNode.val] = nodeClone;
        let cloneNeighbors = [];
        for (let n of currentNode.neighbors) {
            cloneNeighbors.push(helper(n))
        }

        nodeClone.neighbors = cloneNeighbors;
       
        return nodeClone;
    }

    helper(node);

    return map[node.val];
    }
```

We can do the same thing using stack, in this case we would create all children and push
Then to stack. So when iterating on children we would not push if already found

```javascript
function cloneGraph(node) {
        if (!node) return null;

        let stack = [node];
        let clones = {};
        clones[node.val] = new Node(node.val);

        while (stack.length) {
            const original = stack.shift();
            const clone = clones[original.val];

            for (let neighbor of original.neighbors) {
                if (clones[neighbor.val] === undefined) {                
                    const neighborClone = new Node(neighbor.val);
                    clones[neighbor.val] = neighborClone
                    clone.neighbors.push(neighborClone)
                    stack.push(neighbor)

                } else {
                    // This is really important to remember for recursive calls 
                    if (!clone.neighbors.includes(clones[neighbor.val]) 
                        clone.neighbors.push(clones[neighbor.val])
                }
            }
        }        

        return clones[node.val]
    }
```

### Islands and Treasure 

This question input is a matrix that each cell could be one of 3 values:
0 treasure
-1 water, can not traverse
2147483647 big number

We should return a matrix the each big number shows the distance to the closest treasure.

```markdown
Input: [
  [2147483647,-1,0,2147483647],
  [2147483647,2147483647,2147483647,-1],
  [2147483647,-1,2147483647,-1],
  [0,-1,2147483647,2147483647]
]

Output: [
  [3,-1,0,1],
  [2,2,1,-1],
  [1,-1,2,-1],
  [0,-1,3,4]
]
```

At first it may seem like a problem of time complexity:

Number of treasure * board size, since we would have to bfs on each one.
But the trick is traversing from all of the treasures together!
Which terms out to be time complexity of board size, single iteration.

```javascript
islandsAndTreasure(grid) {
        if (!grid.length || !grid[0].length) return grid;

        let queue = [];

        for (let y = 0; y < grid.length; y ++) {
            for (let x = 0; x < grid[0].length; x ++) {
                if (grid[y][x] === 0) {
                    queue.push({y, x, steps: 0})
                }                
            }
        }

        while (queue.length) {
            const next = queue.shift();
            let steps = next.steps + 1;

            if (next.y > 0 && grid[next.y -1][next.x] === 2147483647) {
                grid[next.y -1][next.x] = steps;
                queue.push({x: next.x, y: next.y -1, steps});
            }
            if (next.x > 0 && grid[next.y][next.x -1] === 2147483647) {
                grid[next.y][next.x -1] = steps;
                queue.push({x: next.x -1, y: next.y, steps});
            }
            if (next.y < grid.length -1 && grid[next.y +1][next.x] === 2147483647) {
                grid[next.y + 1][next.x] = steps;
                queue.push({x: next.x, y: next.y + 1, steps});
            }
            if (next.x < grid[0].length && grid[next.y][next.x + 1] === 2147483647) {
                grid[next.y][next.x + 1] = steps;
                queue.push({x: next.x + 1, y: next.y, steps});
            }
        }

        return grid;
    }
```

### Rotting Fruit

You are given a 2-D matrix grid. Each cell can have one of three possible values:
0 representing an empty cell
1 representing a fresh fruit
2 representing a rotten fruit

Every minute, if a fresh fruit is horizontally or vertically adjacent to a rotten fruit, then the fresh fruit also becomes rotten.

Return the minimum number of minutes that must elapse until there are zero fresh fruits remaining. If this state is impossible within the grid, return -1.

- The trivial think is to use the same method of extending via bfs from all rotten fruits
- But the time is something should remember to start from -1 or else what be counting invalid
- And at the end we must iterate again to validate there are no left fruits

```javascript
    function orangesRotting(grid) {
        if (!grid.length || !grid[0].length) return 0

        let queue = [];
        for (let y = 0; y < grid.length; y ++ ) {
            for (let x = 0; x < grid[0].length; x ++ ) {
                if (grid[y][x] === 2) {
                    queue.push({x, y})
                }
            }
        }

        // in order for minutes to be valid
        let minutes = -1

        while (queue.length) {
            minutes++;
            const length = queue.length;
            for (let i = 0; i < length; i ++ ) {
                const next = queue.shift();
            const y = next.y;
            const x = next.x;

            if (y > 0 && grid[y - 1][x] === 1) {
                grid[y -1][x] = 2
                queue.push({y: y -1, x})
            }
            if (x > 0 && grid[y][x -1] === 1) {
                grid[y][x -1] = 2;
                queue.push({y, x: x -1 });
            }
            if (y < grid.length - 1 && grid[y + 1][x] === 1) {
                grid[y + 1][x] = 2;
                queue.push({y: y + 1, x});
            }
            if (x < grid[0].length -1 && grid[y][x + 1] === 1) {
                grid[y][x + 1] = 2;
                queue.push({y, x: x + 1})
            }
            
        }
        }

        // If not possible to rotten all fruit
        for (let y = 0; y < grid.length; y ++ ) {
            for (let x = 0; x < grid[0].length; x ++ ) {
                if (grid[y][x] === 1) return -1
            }
        }

        return minutes === -1 ? 0 : minutes
    }
```

### Surrounded Regions

The question ask us to flip all O that are not surrounded by X to X.

- I was wrong about the trick in this question. It's much easier to iterate from the edges and mark all
zeros as temporary value, and later set that temporary value back to zero, and zeros that still exist to X.

<img src="./images/surrounded-regions.png">

```javascript
function markAsX(board, y, x) {
    if (x < 0 || y < 0 || x > board[0].length -1 || y > board.length -1) return;
    const current = board[y][x];
    if (current !== "O") return;

    board[y][x] = 'I';
    markAsX(board, y + 1, x);
    markAsX(board, y -1, x);
    markAsX(board, y, x + 1);
    markAsX(board, y, x -1)
}

var solve = function(board) {

    if (!board.length || !board[0].length) return board;

    for (let i = 0; i < board[0].length; i++) {
        const top = board[0][i];
        if (top === 'O') markAsX(board, 0, i);

        const bottom = board[board.length -1][i];
        if (bottom === "O") markAsX(board, board.length -1, i)
    }

    for (let i = 1; i < board.length - 1; i ++) {
        const left = board[i][0];
        if (left === "O") markAsX(board, i, 0);

        const right = board[i][board[0].length - 1];
        if (right === "O") markAsX(board, i, board[0].length -1)
    }

    for (let y = 0; y < board.length; y ++) {
        for (let x = 0; x < board[0].length; x ++) {
            const current = board[y][x];
            if (current === 'I') {
                board[y][x] = 'O'
            } else if (current === 'O') {
                board[y][x] = "X"
            }
        }
    }

    return board;
};
```


### Course Schedule (Detecting cycles)

We receive a list of requirements of which course has to be completed before other one. For example:

[0,1] means we have to take 1 before we take zero.

And we receive a number of courses to complete, for example 9 means courses 0-8.

The trick is while dfs making sure to mark completed ones at the right point, which is:

1. If a course has no dependencies
2. If all dependencies are completed

```javascript
  let allCompleted = {}
        for (let i = 0; i < numCourses; i ++ ) {
            if (allCompleted[i] !== undefined) continue;

            const res = numberOfCourses(map, i, {}, allCompleted);
            if (res === false) return false;
        }
        return true;


function numberOfCourses(map, course, seen, completed) {
    let recursive = false;

    let helper = function(c) {
        if (completed[c] === true) return; // exist if already completed

        // seen is marked on a different step from completed in order to track recursion
        if (seen[c] !== undefined) {
            recursive = true;
            return;
        }

        seen[c] = true;

        if (map[c] === undefined) {
            completed[c] = true; // no dependecies completion marking
            return;
        }

        for (let d of map[c]) {
            helper(d);
        }

        completed[c] = true; // dependecies completion marking
    }

    helper(course)

    if (recursive === true) return false

    return true;
}
```

###  Topological Sort (Kahn's Algorithm) (Finding cycles and if dependencies could be resolved)

Following the previous question, we could also solve it using topological sort.

In short: we create data structure that holds how many dependencies does each node have. Another data structure holds each node which 
node dependencies it resolves. Then we start from a node with no dependencies, resolving (negating 1) from each node which had this one as dependency
while pushing to the queue nodes that after resolution have no dependencies.

-  Count how many dependencies does each node have. For example: [0, 2, 1] means 0 has no
dependencies, 1 has 2 dependency, and 2 has 1 dependency.
- Then we are connecting between each course to the one that depends on it.
- We push to a queue all the ones with zero dependencies (our starting points) marking by negating 1 from
original dependency array count, if reached zero, we add it to the queue
- We keep a total count of how many resolved and compare against the total number of courses

In short, we are saying:

Let's find course with no dependencies and removed them from dependencies of other courses.
Now let's see which ones have no dependencies and do the same.

In the example of [0, 1], 0 depends on 1 which has no dependencies.

```javascript
 function canFinish(numCourses, prerequisites) {
       // create array with dependency count (O has 1 dependenciy [1, 0])
       let deps = new Array(numCourses).fill(0);
       for (let i = 0; i < prerequisites.length; i ++) {
            const p = prerequisites[i];
            deps[p[0]]++;
       }

       // create map with which nodes resolves which dependeicies (1 resolves dependencies [0])
       let resolves = {};
       for (let p of prerequisites) {
            if (resolves[p[1]] === undefined) resolves[p[1]] = [];
            resolves[p[1]].push(p[0])
       }

       // add all zero dependeices to queue (we add 1 since has no dependencies)
       let queue = [];
       for (let i = 0; i < deps.length; i ++) {
        if (deps[i] === 0) {
            queue.push(i)
        }
       }

       let total = 0;
       // while queue has length, pop, remove dependencies, if zero, add to queue
       while (queue.length) {
            total++
            const next = queue.pop();
            if (resolves[next] === undefined) continue;
            for (let course of resolves[next]) {
                deps[course]--; // remove the 1 depedency 0 had.
                if (deps[course] === 0) {
                    queue.push(course)
                }
            }
       }
       
       // if we would have recursive call, we would not be able to resolve all dependencies (turn them to zero)

       return total >= numCourses;
    }   
```

### Graph valid tree 

- First, the definition of "valid" tree is that it has no cycles and all nodes are connected.

The question gives us a list of edges, and we are to find if it's a valid tree. A edge means a connection between two nodes.
This is part of the trick, which we are given [0,1] these are two nodes connected.

This is valid:

```javascript
Input:
n = 5
edges = [[0, 1], [0, 2], [0, 3], [1, 4]]
```

This is not 

```javascript
Input:
n = 5
edges = [[0, 1], [1, 2], [2, 3], [1, 3], [1, 4]]
```
Because of cycles.

The trick is to hash map all connections and treat numbers as nodes. And if we dfs we can keep track of the parent caller,
which because it's bidirectional, we can make sure we are not going back to the parent (which would be valid, we care only about cycles).

```javascript
 function validTree(n, edges) {
        if (!edges.length) return true;
        
        let map = {};
        for (let edge of edges) {
            if (map[edge[0]] === undefined) map[edge[0]] = []
            if (map[edge[1]] === undefined) map[edge[1]] = [];

            map[edge[1]].push(edge[0]);
            map[edge[0]].push(edge[1]);
        }

        let visited = 0;
        let seen = {}
        
        let recursive = false;
        let dfs = function(node, parent) {
            if (seen[node] !== undefined) {
                // detecting as boolean since return would not work
                recursive = true;
                return;
            };
            seen[node] = true;

            const children = map[node];
            visited ++;

            for (let child of children) {
                if (child === parent) continue;

                dfs(child, node);
            }
        }

        // this is really important concept of starting from zero without parent
        dfs(0, -1)

         if (recursive) return false;

        return visited === n
    }
```
### Number of Connected Components in an Undirected Graph

The question is similar to the previous one, but we are to find how many connected components are in the graph.
Connected components actually means how many islands are in the graph.

Example:

Input:
n=6
edges=[[0,1], [1,2], [2,3], [4,5]]

Output:
2

The important catch in this case is remembering that not all nodes from 0 to n have to have neighbors at all.
We have to verify it.

```javascript
 function countComponents(n, edges) {
        if (!edges.length) return n;

        let map = {};
        for (let edge of edges) {
            if (map[edge[0]] === undefined) map[edge[0]] = [];
            if (map[edge[1]] === undefined) map[edge[1]] = [];
            map[edge[0]].push(edge[1]);
            map[edge[1]].push(edge[0]);
        }

        let seen = {}
        let dfs = function(node, parent) {
            if (seen[node] !== undefined) return;

            seen[node] = true;
            const children = map[node];
            // this is important
            if (!children )return

            for (let child of children) {
                if (child === parent) continue;
                dfs(child, node);
            }
        }

        let connections = 0;
        for (let i = 0; i < n; i ++) {
            if (seen[i] !== undefined) continue;

            connections++;
            dfs(i, -1)
        }

        return connections
    }
```

### Redundant Connection (Detecting cycles)
Time complexity: O(V + E) (We are iterating on both edges and nodes)

In short: start from any node since all are connected. Dfs until noticed we are in a cycle. Backtrack while selecting all cycle nodes.
Return the first edge from the right which is within the cycle. 

The questions tells us about a tree that all nodes were connected, but one edge was added that created a cycle.
We need to find that edge.

Important catches:

1. A cycle could happen anywhere in the graph, it could include only a few nodes are the entire graph.
2. Before an additional edge that is added to create a cycle, all existing nodes are already connected
3. When dfs'ing to detect a cycle, we must remember to keep track of the node that started the cycle in order to stop the back tracking.

Solution A explanation:

1. We start from the first nodes df'sing while knowing all nodes are connected, so we would reach all of them.
2. We keep track of visited nodes, once we reach a node that is already visited, we know
   a. this is a cycle
   b. the current node is the one who started & ended the cycle
3. From the parent we backtrack, if we are currently within a cycle we add the child
4. If the node who edges/started (depending how we look at it) is the current node, we stop the backtracking since we finished the cycle.

```javascript
function findRedundantConnection(edges) {
       let map = {};

        for (let edge of edges) {
            if (map[edge[0]] === undefined) map[edge[0]] = [];
            if (map[edge[1]] === undefined) map[edge[1]] = [];
            map[edge[0]].push(edge[1]);
            map[edge[1]].push(edge[0]);
        }

        let visited = {};
        let cycle = {};
        let startNode = null
        let dfs = function(node, parent) {
            if (visited[node] !== undefined) { // we are part of a circle
                startNode = node;
                return; // we have no reason to continue rounding about
            }
            visited[node] = true;

            if (map[node] === undefined) return false;
            for (let child of map[node]) {
                if (child === parent) continue;
                dfs(child, node);
                if (startNode !== null) {
                    cycle[child] = true;
                    if (node === startNode) {
                        // we stop the backtracking of the circle
                        startNode = null
                    }
                    return;
                }
            }
        }

        dfs(1, -1)

        for (let i = edges.length -1; i >= 0; i --) {
            const edge = edges[i];
            if (cycle[edge[0]] === true && cycle[edge[1]] === true) {
                return edge;
            }
        }
        return null;
};
```
### Redundant Connection (same question) solving with Topological Sort (Kahn's Algorithm)
Time complexity: O(V + E) (We are iterating on both edges and nodes)

In short: create a data structure to hold dependency count per node, then check if there are ones with 1 dependency. If there are, it means
not the hole graph is a cycle, we have an edge that is a branch. Now we want to remove the hole branch (or multiple) until reaching the circle.
So we add 1 dependency nodes to a queue, pulling from it and removing 1 dependency from each child. if child dependencies is not 1, we add to queue.
Else, it means we reached the circle start that would have 1 or more branches dependencies plus 2 of the circle (left and right)

Now we are left with a data structure of dependencies that ones of the circle should still have 2. 
We can iterate from right to left and return the first one we see.

```javascript
 function findRedundantConnection(edges) {
        let depCount = {}; // how many dependencies does each node have
        let graph = {};
        for (let edge of edges) {
            const nodeOne = edge[0];
            const nodeTwo = edge[1];
            if (depCount[nodeOne] === undefined) depCount[nodeOne] = 0;
            if (depCount[nodeTwo] === undefined) depCount[nodeTwo] = 0;
            if (graph[nodeOne] === undefined) graph[nodeOne] = [];
            if (graph[nodeTwo] === undefined) graph[nodeTwo] = [];

            depCount[nodeOne]++;
            depCount[nodeTwo]++;
            graph[nodeOne].push(nodeTwo);
            graph[nodeTwo].push(nodeOne);
        }

        let queue = [];
        for (let edge of edges) {
            const nodeOne = edge[0];
            const nodeTwo = edge[1];
            if (depCount[nodeOne] === 1) { // if 1 it means a start of a branch 
                queue.push(nodeOne);
            } else if (depCount[nodeTwo] === 1) {
                queue.push(nodeTwo)
            }
        }

        while (queue.length) {
            const next = queue.shift();
            if (graph[next] === undefined ) continue;
            for (let child of graph[next]) {
                depCount[child]--;
                if (depCount[child] === 1) queue.push(child) // if after removal child is 1, it means child is still not part of a cycle.
            }
        }

        for (let i = edges.length - 1; i >= 0; i --) {
            const edge = edges[i];
            if (depCount[edge[0]] === 2 && depCount[edge[1]] === 2) return edge // cycle nodes have 2 dependencies, one from each side
        }

        return null
    }
```

### Redundant Connection (same question) solving with Disjoint Set Union
Time complexity: we iterate over each edge, and for each node we are union finding it's parent.
The union find is near constant but not constant. For that reason it's:
V is the number of vertics (nodes)
E refers to edges
O(V + E * (a(v)))
We iterate over each node, that's a given. Because we are creating an array of size V.
Then for each edge, we run union with a time complexity of a(v) because It's amortized (near linear) of size V (number of vertics/nodes)

Union means joining two groups together. For each node, we will keep a counter which says how big is it's tree size, and a pointer to it's parent (or self)
Then iterate over each edge, calling union on both nodes together.
Once we find two node that are already of the same parent, it means we found the additional edge that caused the cycle.

We are actually building a root to children kind of tree from each nodes parent. Once the two parents are the same, it means 
these two nodes already exist, and adding the edge would cause a cycle.

```javascript
    function findRedundantConnection(edges) {
        let parentPointer = new Array(edges.length).fill(0);
        for (let i = 0; i < parentPointer.length; i ++) {
            parentPointer[i] = i
        }
        let sizeCounter = new Array(edges.length).fill(1);
    
        let parentsAreTheSame = function(nodeA, nodeB) {
            let tempA = nodeA;
            let tempB = nodeB;
            while (parentPointer[tempA] !== tempA) tempA = parentPointer[tempA];
            while (parentPointer[tempB] !== tempB ) tempB = parentPointer[tempB];
            if (tempA === tempB) return true;
            return false;
        }
    
        let union = function(nodeA, nodeB) {
            let tempA = nodeA;
            let tempB = nodeB;
            while (parentPointer[tempA] !== tempA) tempA = parentPointer[tempA];
            while (parentPointer[tempB] !== tempB ) tempB = parentPointer[tempB];
    
            if (sizeCounter[tempA] >= sizeCounter[tempB]) {
                sizeCounter[tempA]+= sizeCounter[tempB];
                sizeCounter[tempB] = 0;
                parentPointer[tempB] = tempA;
            } else {
                sizeCounter[tempB]+= sizeCounter[tempA];
                sizeCounter[tempA] = 0;
                parentPointer[tempA] = tempB;
            }
        }
    
        for (let edge of edges){
            if (parentsAreTheSame(edge[0], edge[1])) return edge;
    
            union(edge[0], edge[1])
        }

    return null;
}
```

### Word Ladder

The question gives us a "startWord" "endWord' and list of words that we could use to reach from start to end.
But in order to move forward, we need the letter to have at max 1 different letter. For Example:

Input: beginWord = "cat", endWord = "sag", wordList = ["bat","bag","sag","dag","dot"]

The transformation sequence is "cat" -> "bat" -> "bag" -> "sag".

* Explanation

- The trick is first understanding that this is a graph problem. We are trying to reach from one word to another.
- Another thing is to understand we are trying to find the shortest path, so this can not be done using dfs, we should be using bfs (a queue)

Initially I created a graph with all permutations of each word with * at a different character, this works, but creating all those strings is expensive.
Instead, we can:

1. Create a set of list of words, this way we can iterate over it and remove with O(1)
2. Push the first word the a queue
3. Iterate over the length of queue while incrementing total steps
4. In other to understand in the list word matches we can compare each letter while checking if total diff is equal to 1.
5. When there is a match, we can add to queue, and remove from set


```javascript
function ladderLength(beginWord, endWord, wordList) {
        if (!wordList.includes((endWord))) return 0;

        // Using set would let us add and remove with O(1) while being able to iterate over
        const ramainingWords = new Set(wordList);

        let queue = [beginWord];
        let steps = 0;

        while (queue.length) {
            const length = queue.length;
            steps++ // increment steps
            for (let i = 0; i < length; i ++ ) {
                const word = queue.shift();
                if (word === endWord) return steps;
                for (let otherWord of ramainingWords) {
                    let diff = 0;
                    for (let i = 0; i < otherWord.length; i ++ ) {
                        if (word[i] !== otherWord[i]) diff++
                    }
                    if (diff === 1) { // if diff is one we know there is a match without pre creating all string permutations
                        queue.push(otherWord);
                        ramainingWords.delete(otherWord) // delete is the set function of remove
                    }
                }
            }
        }

        return 0;
    }
```

### Network Delay Time (Graph with heights)

Questions gives us a list of directional edges with weight on each one. A start node and total nodes count. 
We should return the min amount of time (calculated by weight) we can start from the starting point and reach all nodes in the graph.

Note:
If we have one nodes pointing to two others ones, both with weight of 2, that total time would be 2 since at the same 2 seconds it would reach both.

Example:

Input: times = [[1,2,1],[2,3,1],[1,4,4],[3,4,1]], n = 4, k = 1

Output: 3


<img src="./images/network-delay-time.png">


#### Initial brut force implementation:

The time complexity is O(V * E) because each node could potentially pass though all edges 

The trick here is: 
1. Checking that we reached all nodes
2. Checking which node took us the longest, and that is the longest time it would take to pass though all nodes.

If one node took 5 seconds to reach, and others took 3 and 2, by 5 seconds all nodes would be reached

```javascript
   function networkDelayTime(times, n, k) {
        let min = new Array(n + 1).fill(Infinity);

        let graph = {};
        for (let time of times) {
            if (graph[time[0]] === undefined) graph[time[0]] = [];
            graph[time[0]].push({des: time[1], cost: [time[2]]})
        }

        let sum = 0;
        let visited = {};
        let dfs = function(node) {
            if (visited[node] === true) return; // checking for recursive paths
            visited[node] = true;
            min[node] = Math.min(sum, min[node]); // min weight to reach the current node
            
            // after to OR: if the min weight is higher from the previous one, we have no reason to continue 
            if (graph[node] === undefined || sum > min[node]) {
                 visited[node] = false;
                 return;
            }

            for (let child of graph[node]) {
                sum += Number(child.cost);
                dfs(child.des)
                sum -= Number(child.cost)
            }
            
            visited[node] = false;

        }

        dfs(k);

        let max = -Infinity;
        // max seconds to reach a node, or find a node that was never reached
        for (let i = 1; i <= n; i++) {
            const res = min[i]
            max = Math.max(max, res)
        }

        if (max === Infinity) return -1;
        return max;
    }
```

## Dijkstra's Algorithm (Solving the previous question with O(E Log V))

This algorithm uses a mean heap, and by that, we always go to the next point with the total aggregated weight that is the lowest.
Because of this, we know that every node we see, we can not visit again since this is the lowest weight we can see it.

We start by pushing the first point with total weight of zero.
While the min heap exist, we fetch the next item, and if we did not visit it yet, we push it to the heap but with aggregated weight.
This means items in the heap have the total time it reaches to visit them.

If we visited all nodes, the max time would be the last node we have seen aggregated weight.

```javascript
function networkDelayTime(times, n, k) {
        let map = {};
        for (let time of times) {
            if (map[time[0]] === undefined) map[time[0]] = []
            map[time[0]].push([time[1], time[2]])
        }
        
        const minHeap = new MinPriorityQueue((a) => a[0]); // should implememt this, no native javascript one
        minHeap.enqueue([0, k]); // weight of first is 0 since we start from it
        let visited = {};
        let visitedCount = 0;
        let sum = 0;

        while (!minHeap.isEmpty()) {
            const next = minHeap.dequeue();
            if (visited[next[1]] !== undefined) continue;
            visited[next[1]] = true;
            visitedCount ++;
            sum = next[0]

            if (map[next[1]] === undefined) continue;
            for (let child of map[next[1]]) {
                if (visited[child[0]] !== undefined) continue;

                minHeap.enqueue([next[0] + child[1], child[0]]) // we push the aggregated weight of the children if we did not visit them yet
            }
        }

        if (visitedCount !== n) return -1
        return sum;

    }
   ```


## Hierholzer's Algorithm and Eulerian Paths

Hierholzer's algorithm helps us find a valid Eulerian path, which is a path that visits all edges in a graph exactly once from start to finish. 

To understand this algorithm, we need to cover the properties of an Eulerian path:
We must have a maximum of one vertex where (in-degree minus out-degree) equals one (the last vertex), and a maximum of one vertex where (out-degree minus in-degree) equals one (the first vertex).
Note that in the case of an Eulerian circuit, all nodes have equal in-degrees and out-degrees, and this circuit is also a valid Eulerian path.

Back to the algorithm:
After determining our starting node (using the in-degree and out-degree calculations), the algorithm allows us to perform a random DFS until hitting a dead end, as long as we keep track of the out-degrees of each vertex. When we hit a dead end, we backtrack while doing two things:

If the current node has zero remaining out-degrees, we add it to our result array
If not, we perform DFS on the remaining outgoing edges

In short: The algorithm takes into account that when we reach a dead end, it's because there are cycles behind us in the path, and we backtrack while adding these cycles to the main track.

Video https://youtu.be/8MpoO2zA2l4?si=Qe4eOnHey3l4Dikg

<img src="./images/hierholzers.png">

## Reconstruct Flight Path (Solving using Hierholzer's Algorithm )

Questions gives us a list of flights, source and destinations, and we have to return a valid flight order to visit all flight tickets.
We know that we start from JFK and that there is a valid order.

For example: 

Input: tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
Output: ["JFK","MUC","LHR","SFO","SJC"]

This case is straight forward, all flights are ordered.
But we could have cases of:
A. more than one valid path (in this case we should take the alphabetical one)
B. a path with circuits 

Following the algorithm from above, we will order the opposite (since we start from the end) and dfs until reaching a dead end
Then adding the vertex with no edges remaining. Every time we visit a out edge vertex, we will remove it so we do not visit it again.
This way we reach a dead end, add nodes while no children and backtrack finding circuts and add to result.


```javascript
function findItinerary(tickets) {
    let map = {};
    for (let ticket of tickets) {
        if (map[ticket[0]] === undefined) map[ticket[0]] = [];
        map[ticket[0]].push(ticket[1])
    }

    for (let ticket of tickets) {
        // since we want it to be sorted, and we add to the end first
        // we need to start from the invalid path
        map[ticket[0]].sort().reverse()
    }

    let res = [];
    let dfs = function(vertex) {
        const children = map[vertex] === undefined ? [] : map[vertex];

        while (children.length) {
            const child = children.pop() // we remove the current edge in order to not visit it a again
            dfs(child)
        }

        // every time there is no more edges we add the node, as the algorithm mentioned no remaining "out" edges
        res.push(vertex)
    }

    dfs('JFK');
    return res.reverse();
```

### Min Cost to Connect Points (Prime's algorithm)

The question gives us a list of points (x, y) and we need to find the way to connect them with minimum distance between for each edge.
It seems hard because distance is between two points, and we do not know where to start.
But the trick is we don't care where to start as long as we continue to push distances from current node to all others to mean heap.

<img src="./images/prims.png">

- We create calculate distances from all nodes to all other nodes (abs(x1 - x2) + abs(y1 - y2))
- Than we pick the first node from list, since it does not really matter, and push all other nodes distances to a queue
- We continue to pull from this queue while doing the same thing, making sure we always visit new nodes
- We are actually spreading like a spider rather than one by one.

What is happening is we are potentially finding closes path to nodes we visited previously, so orderign does not matter.
At any point, we check which or all distances also from previous visits is the shortest, and add that one.

Total time is 
N^2 since we iterate on all nodes and another iteration for all other nodes calculating distance
Multiplied by 
Log N, which is our min heap

```javascript
function minCostConnectPoints(points) {
    let dist = {};
    for (let i = 0; i < points.length; i ++) {
        const point = points[i]
        for (let y = 0; y < points.length; y++) {
            if (i === y) continue;
            const toPoint = points[y];
            const distance =  Math.abs(point[0] - toPoint[0]) + Math.abs(point[1] - toPoint[1]); // this is really important to be absolute value since could be negatvie if not
            if (dist[i] === undefined) dist[i] = {};
            if (dist[y] === undefined) dist[y] = {};
            dist[i][y] = distance;
            dist[y][i] = distance;
        }
    }
    
    let minHeap = new MinHeap();
    let seen = {};
    seen[0] = true;
    for (let i = 1; i < points.length; i ++) {
        minHeap.push({value: dist[0][i], index: i})
    }
    let total = 0;

    while (minHeap.stack.length) {
        const {value, index} = minHeap.pop(); // the target new node
        if (seen[index] !== undefined) continue;
        seen[index] = true; // remember to set distance

        for (let destIndex = 0; destIndex < points.length; destIndex ++) {
            if (destIndex === index) continue;
            if (seen[destIndex] !== undefined) continue; 
            
            minHeap.push({value: dist[destIndex][index], index: destIndex})
        }

        total += value;
    }

    return total;
}
```

### Swim in Rising Water (Kruskal's Algorithm)

The questions asks us to find the path with the lowest max. 

Example:

<img src="./images/SwimInRisingWater.png">

We start from the first point, pushing it's value to a min heap.
While the min heap length exist, we pop the next item, check it's not already visited, set as visited,
Check it's not the last cell, and push all neighbors.

This means we will move each point to the best overall min value while seeing each cell only once.
Instead of deciding on a single path and moving forward, we always check if a previous path was smaller
Than the one we arrived to be at.

Time complexity:

n * n (y * x)
Multiplied by
Log n (we could potentially have a lot of values inside the min heap)


```javascript
 function swimInWater(grid) {
        if (grid.length === 0 || grid[0].length === 0) return 0;

        let minHeap = new MinHeap();
        minHeap.push({value: grid[0][0], location: [0,0]});
        let max = -Infinity;
        let visited = new Set();

        while (minHeap.stack.length) {
            const {value, location} = minHeap.pop();
            if (visited.has(value)) continue;
            visited.add(value);
            max = Math.max(value, max);

            if (location[0] === grid.length - 1 && location[1] === grid[0].length - 1) {
                return max;
            }

            if (location[0] > 0) {
                minHeap.push({location: [location[0] -1, location[1]], value: grid[location[0] -1][location[1]]});
            }
            if (location[0] < grid.length - 1) {
              minHeap.push({location: [location[0] +1, location[1]], value: grid[location[0] +1][location[1]]});
            }
            if (location[1] > 0) {
                minHeap.push({location: [location[0], location[1]-1], value: grid[location[0]][location[1]-1]});
            }
            if (location[1] < grid[0].length - 1) {
              minHeap.push({location: [location[0], location[1] +1], value: grid[location[0]][location[1] +1]});
            }
        }

        return null; // throw
    }
```

### DFS Alien Dictionary (Could also be solved using Topological Sort)

The question gives us a list of words, and we are to find the order of the alphabet based on the words.
The words are in order, as if we would see them in a dictionary.
For example:

Apple -> Ball -> Cat -> Dog

But if a word starts the same, we should check the next letter, which would be ordered.
It's guaranteed that the words are in order, so we can compare each letter to the next one.

If we find a mismatch, for example two words with the same prefix but unsorted, we should return empty string.
For example: 
Apple -> App 
This should have been the opposite order.

Or a circular dependency of which we should also return empty string.

Solution:

We compare each word to the next one. Searching for the first letter of mismatch.
When we found it, we know they are dependent on each other. For example:

Apple -> Cat

We know a is a dependency of c and can add it to the graph.
In the case of:

Apple -> AppH

We know e is a dependency of H, and we can add it to the graph.

Then we DFS while searching for circular dependencies and creating our results.

```javascript
 function foreignDictionary(words) {
        let deps = {};

        for (let second = 1; second < words.length; second++) {
            const first = second -1;
            const firstWord = words[first];
            const secondWord = words[second];

            let diffIndex = 0;
            let minLength = Math.min(firstWord.length, secondWord.length);
            // find the first letter that is different
            while (diffIndex < minLength) {
                if (firstWord[diffIndex] !== secondWord[diffIndex]) break;
                diffIndex++
            }
            
            // if we reached the end of the word, we must make sure the first word is shorter or else input is invalid.
            if (diffIndex === minLength) {
                if (firstWord.length > secondWord.length) return ""
            }

            if (minLength === diffIndex) continue;
            const letter = secondWord[diffIndex];
            const dep = firstWord[diffIndex];
            if (deps[letter] === undefined) deps[letter] = [];
            if (!deps[letter].includes(dep)) deps[letter].push(dep);
        }

        let res = [];
        let visited = {};
        let recursive = false;

        let dfs = function(letter, currentVisited = {}) {
            if (currentVisited[letter] === true) {
                recursive = true;
                return;
            }
            // if we reach a visited letter from a different dfs, we know it's already completed.
            // We know it's not circular because of the check above that validates only current dfs interation
            if (visited[letter] === true) return;

            currentVisited[letter] = true;
            visited[letter] = true;
            const letterDeps = deps[letter];

            if (letterDeps && letterDeps.length) {
                for (let d of letterDeps) {
                 dfs(d, currentVisited);
                }
            } 

            // when reaching the same letter from a different path, we do not want to treat it as circular
            currentVisited[letter] = false 
            // after we visited all dependencies, we can add the letter to the result
            res.push(letter);
        }

        for (let letter of Object.keys(deps)) {
            if (visited[letter] === true) continue;
            dfs(letter);
        }

        if (recursive === true) return '';

        for (let word of words) {
            for (let i = 0; i < word.length; i ++) {
                const letter = word[i];
                if (visited[letter] !== undefined) continue;
                visited[letter] = true;
                res.push(letter);
            }
        }

        return res.join('');
    }
```

### Cheapest Flights Within K Stops

The question gives us a list of flights, source, destination, and a maximum number of stops.
We are to find the cheapest flight from source to destination within the maximum number of stops.

Solution explanation: 

- We start by building a map from each airport to its outgoing flights, which takes O(M) time.
- We initialize our BFS queue with flights departing from the source.
- For up to K levels, we process the current queue: for each flight, if it reaches the destination we update the minimum price; otherwise, we enqueue its subsequent flights.
- Without deduplication, the number of flight routes processed could grow exponentially with K.
- With deduplication, at each level we only process the unique reachable airports, so the work per level is the sum of the outdegrees of these airports. In the worst-case dense graph, that’s O(N²) per level (since M = O(N²)), leading to an overall time of O(M + K · N²) (or equivalently O(M + K · M) in the dense scenario).

The reason It's N^2:

- We iterate over all flights, finding destination flights.
- Only after getting to each destination flight we could decide if to add it or not back to queue
- How many times we would get to choose? Maximum routes. Which is in the worst case each source could have a flight to all destinations
- This makes it number of flights ^ 2

Optimization of this solution could be done by using a priority queue, which would allow us to always get the cheapest flight first.


```javascript
 findCheapestPrice(n, flights, src, dst, k) {
        let sourcesMap = {};
        // create a map pointing to src and all flights from that point
        for (let flight of flights) {
            if (sourcesMap[flight[0]] === undefined) sourcesMap[flight[0]] = [];
            sourcesMap[flight[0]].push(flight)
        }
        let bfsQueue = [];
        let bestPrice = Infinity;
        let currentNumOfStops = 0;

        // if there are not flights from this point, there is not route to dest
        const initialFlights = sourcesMap[src];
        if (!initialFlights || !initialFlights.length) return -1;

        // queue init with all flights from the origin point
        for (let flight of initialFlights) {
            bfsQueue.push(flight)
        }
        sourcesMap[src] = [];

        // while there are flights remaining to take and stops remanining
        while (bfsQueue.length && currentNumOfStops <= k) {
            currentNumOfStops++;
            const length = bfsQueue.length;
            let destMap = {}
            for (let i = 0; i < length; i ++) {
                const flightRoute = bfsQueue.shift();
                if (flightRoute[1] === dst) {
                    bestPrice = Math.min(bestPrice, flightRoute[2])
                    continue;
                }
                
                const destFlights = sourcesMap[flightRoute[1]];
                if (!destFlights || !destFlights.length) continue;
                
                for (let flight of destFlights) {
                       if (destMap[flight[1]] === undefined) {
                            destMap[flight[1]] = [flight[0], flight[1], flight[2] + flightRoute[2]]
                        } else {
                            if (destMap[flight[1]][2] > (flight[2] + flightRoute[2])) {
                                destMap[flight[1]] = [flight[0], flight[1], flight[2] + flightRoute[2]]
                            }
                        }
                }
            }

             const keys = Object.keys(destMap);
                for (let key of keys) {
                    const flight = destMap[key];
                     bfsQueue.push(flight)   
                }
        }

        if (bestPrice === Infinity) return -1

        return bestPrice;
    }
```
