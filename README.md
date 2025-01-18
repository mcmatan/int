Interview sheet cheat:

# Monotonic Stack

A monotonic stack maintains elements in either strictly increasing or strictly decreasing order. The key insight is that when we violate this order (e.g., pushing a smaller element onto an increasing stack), we can perform meaningful calculations on the elements being removed.

## Key Characteristics
- Elements are only pushed/popped once → O(n) time complexity
- Useful for finding relationships between array elements (next greater/smaller, spans, ranges)

## Example Problems
1. **Largest Rectangle in Histogram**
    - Maintain increasing stack
    - When smaller height appears, calculate areas of all larger heights being removed

2. **Daily Temperatures**
    - Maintain decreasing temperature stack
    - When warmer temperature appears, update all colder temperatures being removed

## Common Use Cases
- Next Greater/Smaller Element problems
- Range/Span calculations
- Problems involving "the first element that is greater/smaller"
- Area calculations with constraints

--------------------------------

# Binary Search

A divide-and-conquer algorithm for finding elements in sorted collections. Time complexity: O(log n)

## Core Concept
- Repeatedly divide search interval in half
- Compare target with middle element
- Adjust search space based on comparison
- Continue until element is found or space exhausted (for insertation point)
- Continue while left is smaller or equal to right

## Example Problems
1. **Koko Eating Bananas**
   - Create a list of possible bite sizes, and iterate through it searching for the best min bite size to take.
   - We run a calculation on each index, which makes the decision rather move left or right
   - We move until crossed, then take the overlapping side which in this case is left

## Template
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

## Additional properties
- When looking for a value that may not exist, and we want the closest one, we might need
to set the variable while crossing it,

```javascript
    while (left <= right) {
          const mid = Math.floor((left + right) / 2);
          // when we last see this value, or the one the is smaller then it we save it. We want to continue to make sure the is actually the closest one which is smaller
          // it would also find the matching one since numbers are unique in this case
          if (existing[mid].timestamp <= timestamp) {
            res = existing[mid].value
            left = mid +1;
          } else {
            right = mid -1;
          }
      }
```

- On some cases, like the question "Median of Two Sorted Arrays" we iterate on one array of the two, and the second index is calculated by the one diff, since we know the total length from left
- Also in the same question, we use a separator line index instead of actual number index, since one array could rather include all values or none. We then assume the prev num to be index -1 and next as current
- Since we are using a separator index the right cursor is length instead of length -1;

```javascript
var findMedianSortedArrays = function(nums1, nums2) {
       const totalLength = nums1.length + nums2.length;
  const half = Math.ceil((totalLength) / 2);
  const isEven = totalLength % 2 === 0;

  const smallArray = nums1.length > nums2.length ? nums2 : nums1;
  const bigArray = nums1.length > nums2.length ? nums1 : nums2;

  let left = 0;
  let right = smallArray.length; // since we can put a line before and after too, not just in between.
  while (left <= right) {
      const mid = Math.floor((left + right) / 2); // 1
      const bigArrayMid = half - mid; // 2 -1 -1 = 0

      const bigPrev = bigArrayMid === 0 ? -Infinity: bigArray[bigArrayMid -1];
      const bigNext = bigArrayMid < bigArray.length ? bigArray[bigArrayMid] : Infinity;
      const smallPrev = mid === 0 ? -Infinity: smallArray[mid -1];
      const smallNext = mid < smallArray.length ? smallArray[mid] : Infinity;

      if (smallPrev <= bigNext && bigPrev <= smallNext) {
        if (!isEven) {
          return Math.max(smallPrev, bigPrev);
      } else {
          const valueOne = Math.max(smallPrev, bigPrev);
          const valueTwo = Math.min(bigNext, smallNext);
          return (valueOne + valueTwo) /2
      }
      }

      if (smallPrev > bigNext) {
          right = mid - 1;
      } else {
          left = mid + 1;
      }
  }
    }
```


--------------------------------

# Linked List

## Key Characteristics
- Moving around would current = current.next
- Iterating on reversing would usually use prev & current. Initially prev would be null
Until it receives a value and being set as last.

```javascript
var reverseList = function(head) {
        if (!head || !head.next) {
            return head;
        }

        let prev = null;
        let current = head;
        while (current) { // we want the final assighment of prev
            const tempNext = current.next;
            current.next = prev;
            prev = current;
            current = tempNext;
        }

        return prev; // the current would be null
    }
```
