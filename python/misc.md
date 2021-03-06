# MISC

## Tips

### Bisect Usage

```python
from bisect import bisect_left
...
pos = bisect_left(itemsAligned, targetItem)
```

### Inorder Traversal

```python
traverse(node.left)
visit()
traverse(node.right)
```

### Preorder Traversal

```python
visit()
traverse(node.left)
traverse(node.right)
```

### Postorder Traversal

```python
traverse(node.left)
traverse(node.right)
visit()
```

### Zip Sort

```python

a = [3, 9, 2, 24, 1, 6]
b = ['a', 'b', 'c', 'd', 'e']
m1 = sorted(zip(a, b))
# m1 == [(1, 'e'), (2, 'c'), (3, 'a'), (9, 'b'), (24, 'd')]

m2 = sorted(zip(a, b), key=lambda x: x[1])
# m2 == [(3, 'a'), (9, 'b'), (2, 'c'), (24, 'd'), (1, 'e')]
```

### Reverse

```python
li.reverse()
newReversed = reversed(li)
```

### Sort

```python
li.sort(reverse=True)
newSorted = sorted(li, key=itemgetter(2), reverse=True)
```

### Map to Sorted List

```python
sorted_list = sorted(myDict.items(), key=lambda x: x[0])
```

### Object ID

```python
objectId = id(obj)
```

### Heap

```python
import heapq

items = []

heapq.heappush(items, (sortKey1, inputItem1))
heapq.heappush(items, (sortKey2, inputItem2))

…

sortKeyOut1, outputItem1 = heapq.heappop(items)
```

### Filter None Item from list

```python
str_list = list(filter(None, str_list))
```

### Get Maximum value

#### Traditional Way

```python
def maximum(li):
    default = 0
    for e in li:
        if default < e:
            default = e
    return default
maximum(li)
```

#### Using Reduce function

```php
from functools import reduce

reduce(lambda a,b: a if a > b else b ,li)
```

### Using Next

```python
myList = [0.0 , 0.0, 0.0, 2.0, 2.0]
result = next((i for i, x in enumerate(myList) if x), None) # x!= 0 for strict match
# result == 3
```

### Joining Integer Array to String

```python
num_list = [-1, 0, 1, 3, 4, 5, 9]
print(" ".join(map(str, num_list)))
```

### Pretty Print Tree

```python
def pprint_tree(node, file=None, _prefix="", _last=True):
    if not node:
        return
    print(_prefix, "`- " if _last else "|- ", node.val, " range = " + str(node.rg), sep="", file=file)
    _prefix += "   " if _last else "|  "
    pprint_tree(node.left, file, _prefix, False)   
    pprint_tree(node.right, file, _prefix, True)  
```

### Using Sum for conditional sum

```python
sum(x == True for x in sieve)
```

### SQRT

```python
math.sqrt(n)
# or
n ** 0.5
```

### Union Find Example

```python
class Solution:
    def smallestStringWithSwaps(self, s: str, pairs: List[List[int]]) -> str:
        sLen = len(s)
        conn = [i for i in range(sLen)]
        
        def union(idx):
            while conn[idx] != idx:
                conn[idx] = conn[conn[idx]]
                idx = conn[idx]
            return idx

        for pair in pairs:
            conn[union(pair[0])] = union(pair[1])

        groups = {i:[] for i in range(sLen)}
        for i in range(sLen):
            groups[union(i)].append(i)
        
        result = list(s)
        for indices in groups.values():
            sortedList = sorted(indices, key=lambda x:s[x])
            for i in range(len(indices)):
                result[indices[i]] = s[sortedList[i]]

        return ''.join(result)
```

### Check if number is perfect square

```python
root = math.sqrt(number)
int(root + 0.5) ** 2 == number

# 0.5 is for float square ambiguity when number is big enough (?)
```

### Use Set in Python

```python
a = {1}
a.put(2)
a.remove(1)
a.pop()
if 3 in a:
    print("3 in a")
```

### heapq

```python
import heapq

nums = [1,2,3,4,5]
minH, maxH = [], []

#minHeap
for num in nums:
  heapq.heappush(minH, num)

# maxHeap
for num in nums:
  heapq.heappush(maxH, (-num, num))  # (priority, value)
```

### GCD

```python
def gcd(a, b):
    if a < b:
        (a, b) = (b, a)
    while b != 0:
        (a, b) = (b, a % b)
    return a

def lcm(a, b):
  gcdVal = gcd(a, b)
  if gcdVal == 0:
      return 0
  return (a * b) / gcdVal
```

### Prime find with yield

```python
        def prime_gen():
            n = 2
            primes = []
            while True:
                if any((n % k) == 0 for k in primes):
                    n += 1
                    continue
                primes.append(n)
                yield n
```

### Prime Factors

```python
def prime_factors(n):
    for i in itertools.chain([2], itertools.count(3, 2)):
        if n <= 1:
            break
        while n % i == 0:
            n //= i
            yield i
```

## Reference

[https://vallentin.io/2016/11/29/pretty-print-tree](https://vallentin.io/2016/11/29/pretty-print-tree)

        


