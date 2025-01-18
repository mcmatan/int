# Interview Algorithm Cheat Sheet

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

### Advanced Applications
- Problems like "Reorder Linked List" [0, n-1, 1, n-2, 2, n-3, ...] might seem to require O(n) space for **reverse traversal** 
- However, using Floyd's algorithm, you can perform in-place reordering by working backwards from the middle of the list, which does not take extra space.