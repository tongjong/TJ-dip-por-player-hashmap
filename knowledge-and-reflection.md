# Overview

These questions are designed to accompany the task "Implementing a Hash Map in Python" in the "Data Structures and Algorithms" module. The questions are intended to test your understanding of hash maps, their implementation in Python, and the process of integrating data from a double linked list into a hash map. You will also be asked to reflect on your learning and the challenges you faced during the task.

# Knowledge questions

The following are all examples of hash functions:

```python
# (1) the simplest hash function (Stupidly Simple Hash)
def ssh(key):
    return 1
```

```python
# (2) hash function that sums the ASCII values of the characters in the key
def sum_of_ascii_values(key: str, size: int) -> int:
    total = 0
    for char in key:
        total += ord(char)
    return total % size
```

A more Pythonic version

```python
# (2a)
def sum_of_ascii_values(key: str, size: int) -> int:
    return sum(ord(char) for char in key) % size
```

A Pearson Hash function

```python
# (3) Pearson hash function
# https://en.wikipedia.org/wiki/Pearson_hashing
import random

random.seed(42)

# This is INCORRECT:
# pearson_table = [random.randint(0, 255) for _ in range(256)]
pearson_table = list(range(256))
random.shuffle(pearson_table)

def pearson_hash(key: str, size: int) -> int:
    hash_ = 0
    for char in key:
        hash_ = pearson_table[hash_ ^ ord(char)]
    return hash_ % size
```

The following is a hash function that uses the built-in `hash` function in Python

```python
# (4) hash function that uses the built-in hash function
def built_in_hash(key: str, size: int) -> int:
    return hash(key) % size
```

Finally, the following is a hash function that uses the `SHA256` hash function from the `hashlib` module

```python
# (5) hash function that uses the SHA256 hash function
# https://docs.python.org/3/library/hashlib.html
# https://en.wikipedia.org/wiki/SHA-2
# https://en.wikipedia.org/wiki/SHA-2#Pseudocode
import hashlib

def sha256_hash(key: str, size: int) -> int:
    return int(hashlib.sha256(key.encode()).hexdigest(), 16) % size
```

1. All of the above functions are hash functions. Explain how so - what key properties do they all share?

> They are all hash functions because they take an input and produce a value, which is used to map to an index in a hash table. They share 2 key properties: "key" and "size". The "key" is used to produce a unique value for mapping and retrieving data, and the "size" sets the capacity of the hashmap to store data.

2. What are the advantages and disadvantages of each of the above hash functions? Evaluate in terms of uniformity, determinism, efficiency, collision resistance, sensitivity to input changes, and security[1](#Reference). You may need to do some research to answer this question 😱

>1) ssh(key): It is deterministic and efficient, but it fails as a hash function. Since all keys map to the same value, there is 100% collision rate. It has no sensitivity, as different inputs produce the same outputs, and it offers no security.
> 
>2) sum_of_ascii_values(key: str, size: int) -> int: It is deterministic and efficient, as the same inputs always produce the same outputs, and it has O(n) retrieval time. The collision resistance is weak, as different keys can produce the same sum of ascii values. It has some sensitivity, as small input changes alter the output, but it lacks strong security, as it's easy to guess the input due to its simple operations, making it vulnerable to pre-image attacks.
> 
>3) pearson_hash(key: str, size: int) -> int:  Its determinism and efficiency is same as the ascii function. The collision resistance is weak for small hash sizes. It has good sensitivity, as small input changes produce different outputs. However, it is not cryptographically secure because the operation is simple and predictable, and although it has good sensitivity, collisions are not infeasible.
> 
>4) built_in_hash(key: str, size: int) -> int: It is deterministic and efficient, using Python's built-in hash function. Collision resistance is average, as it can still be an issue for certain data types when the hash table size is too small. It has good sensitivity. However, it is not cryptographically secure, as the built-in hash function is designed for speed, not for preventing pre-image attacks or collisions.
> 
>5) sha256_hash(key: str, size: int) -> int: It is deterministic but slower than other hash functions due to its complex calculations. It has great uniformity and strong collision resistance, as unique outputs are expected for different inputs. Sensitivity is high, as a small input change produces a significantly different output. The extremely low collision rate, combined with the difficulty of reverse-engineering the hash makes it highly secure.

3. List the three most important attributes (arranged from most to least) in the context of a hash map? Justify your answer.

> Hash Function -> Size -> Collision Resolution
> The hash function is the most important attribute because the main purpose of the hashmap is to have O(1) retrieval time. When it is well-defined, it ensures data is efficiently mapped with a unique key and values are distributed evenly to minimise collisions.  Size is important also as it affects the performance of the hash table. It needs to be carefully chosen based on the expected data. It needs to be large enough to store data without frequent resizing but also not so large that it wastes memory and reduces efficiency. Finally, collision resolution is essential for handling cases when multiple keys are hashed to the same index. When the hash function and size are optimised, the need for collision resolution is reduced, making it the least important of the three attributes.

4. Which of the above hash functions would you choose to implement the requirements of the task? Why?

> I would choose pearson_hash function. Based on the analysis in question 2, while sha256 offers the best collision resistance and the highest security, for a TAFE assignment where data integrity and security are not a main concern, the Pearson hash function is more suitable. It has good uniformity, determinism and efficient. Although its collision resistance is weak, this can be managed with collision resolutions.

5. In your own words, explain each line in the pearson hash function above in terms of the criteria you listed in question 2.

>  import random (using randomness to create a more even distribution-uniformity)
   random.seed(42)  (choosing a start point to ensure the same random numbers are generated-determinism)
   pearson_table = list(range(256)) (using a fixed range creates a more predictable behaviour-determinism)
   random.shuffle(pearson_table) (a small change in input produce different output because the shuffle changes the index mappings-sensitivity/collision)
   def pearson_hash(key: str, size: int) -> int:
       hash_ = 0
       for char in key:  (operating in O(n) time-efficiency)
           hash_ = pearson_table[hash_ ^ ord(char)]  (each character results in different hash-sensitivity/collision)
       return hash_ % size 

6. Write pseudocode of how you would store Players in PlayerLists in a hash map.

> Create a hash table:
-Make a list of PlayerList with a fixed size

> Get index using either Player instance or the player's key(uid):
-If input is a Player, use a class level hash function to find an index
-If input is a key, use a custom hash method defined in Player class to get the index

> Add a player:
-Find index using get index method above
-Check if the player already exists
-If exists, update the name
-If player does not exist, create a new PlayerNode and add it to the PlayerList

> Get a player:
-Get the index using the player's uid property
-Search for the player in the PlayerList at that index
-Return the player if found
-Return None if not

> Delete a Player:
-Get the index using the player's uid property
-Raise an error if the player is not found
-Remove the player from the PlayerList at that index
 
> Get number of players:
-Get the total number of the players in each PlayerList
-Sum all the players from each PlayerList

## Reflection

1. What was the most challenging aspect of this task?

> The display method in the PlayerHashMap class was not showing the players. After testing the method with multiple players instead of just one, it took me a few hours to identify the issue. The bug was in the PlayerList class. The logic for handling a single node was not implemented. Additionally, I had to use git to rebase and resolve conflicts, as the PlayerList display method was part of previous assessment. It took quite a bit of time to rearrange the files using git and then resubmit the previous assessment,

2. If you didn't have to use a PlayerList, how would you have changed them implementation of the hash map and why?

> I would replace the PlayerList with dictionaries. It improves the retrieval time from O(n) in the worst case with a linked list to O(1). It gives better performance even when dealing with collisions.

## Reference

### Key Dimensions of Hash Functions

1. **Uniformity**: the probability of any given hash value within the range of possible hash values should be approximately equal.

2. **Determinism**: a given input will always produce the same output.

3. **Efficiency**: the time complexity of computing the hash value should be constant, the hash function should be fast to compute, and utilize the architecture of the computer effectively

4. **Collision Resistance:** minimize the probability of collisions, through a variety of mechanisms.

5. **Sensitivity to input changes:** small changes in the input should produce large changes in the output.

6. **Security**
   - It should be computationally infeasible to find an input key that produces a specific hash value (non-reversibility)
   - The output hash values should appear random and unpredictable.
