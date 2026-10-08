

# CSPB 2270 — Exam Worksheet

Consolidated study sheet for **Weeks 1–5**: lecture notes plus the key ideas, examples, and
takeaways from each homework assignment.

**Convert to PDF (LaTeX):**

```bash
pandoc exam_cheatsheet.md -o exam_cheatsheet.pdf --pdf-engine=pdflatex
```

---

## Index

| Week | Topic | Notes | Homework |
|------|-------|-------|----------|
| [Week 2](#week-2--data-structures-adts-hw-1-vector10) | Data structures & ADTs | [Notes](#data-structures) | HW 1 (Vector10) |
| [Week 3](#week-3--linked-lists-heap-memory-hw-2-linkedlist) | Linked lists & heap memory | [Notes](#linked-lists) | HW 2 (LinkedList) |
| [Week 4](#week-4--recursion--binary-search-trees-hw-3-bst) | Recursion & BSTs | [Notes](#recursion) | HW 3 (BST) |
| [Week 5](#week-5--computational-complexity-sorting-hw-4-sorting) | Complexity & sorting | [Notes](#computational-complexity) | HW 4 (Sorting) |
| [Appendix](#appendix-a--complexity-quick-reference) | Complexity quick reference | — | — |

---


## Examples:

Part 1: Make `fooA()` return `7`:

```cpp
int fooA() {
  return 7; 
}
```

Part 2: Make `main()` print exactly `hello penguin` with a trailing newline:

```cpp
int main(int argc, char* argv[]) {
    cout << "hello penguin\n";
    return 0;
}
```

---

# Week 2: Data Structures & ADTs

## Data Structures

A **data structure** is a way to store, organize, and perform operations on data.

| Structure | Description | Visual |
|-----------|-------------|--------|
| **Record** | Stores subitems as `{key: value}` pairs. Simialr to a dictionary in C# or python | ![Record](Week_2/Images/Record.png) |
| **Array** | Collection of elements. Can be indexed for $$O(1)$$ operations | ![Array](Week_2/Images/Array.png) |
| **Linked List** | Ordered items in nodes; each node points to the next Usually $$O(n)$$ operations for lookups| ![Linked List](Week_2/Images/LinkedList.png) |
| **Binary Tree** | Nodes with (up to) two children: left and right. Balanced trees should have a $$O(log_n)$$ for lookups and can be as bad as $$O(n \log_n)$$ if tree is just a single branch | ![Binary Tree](Week_2/Images/BinaryTree.png) |
| **Hash Table** | Maps (hashes) each item to a location in an array. Fast lookups and only holds unique values. | ![Hash Table](Week_2/Images/HashTable.png) |
| **Heap** | *Max-heap*: node key $$\geq$$ children; *min-heap*: node key $$\leq$$ children | ![Max-Heap](Week_2/Images/MaxHeap.png) |
| **Graph** | Vertices (items) connected by edges (connections) | ![Graph](Week_2/Images/Graph.png) |

## Abstract Data Types (ADTs)

An **ADT** is described by its operations, not its implementation ("insert data at rear" can be
implemented as an array *or* a linked list). The **List** ADT is commonly backed by an array or
linked list.

Common ADTs:

- **List** — ordered, indexable sequence.
- **Dynamic array** — resizable array.
- **Stack** — LIFO; add/remove only at the top (most recent).
- **Queue** — FIFO; add at the rear, remove from the front.
- **Bag** — unordered; duplicates allowed.
- **Set** — unordered; **unique** items only.
- **Priority Queue** — ordered by a priority value (higher priority toward the front).
- **Dictionary** — maps keys to values.

### Vector ADT (C++ `std::vector`)

```cpp
vector<int> myVector(5);              // vector with 5 elements

myVector.at(0) = 5;                   // assign value at index 0
int theSize = int(myVector.size());   // size() is unsigned -> cast to int
bool isEmpty = myVector.empty();      // true if empty

myVector.clear();                     // remove all elements
myVector.push_back(10);               // append to the end
myVector.insert(myVector.begin() + 1, 20); // insert 20 at index 1. this is an O(n) operation since all elements after index 1 must be shifted
myVector.erase(myVector.begin());          // erase element at index 0
```

## HW 1: Vector10

**Example:**

```cpp
int Vector10::value_at(int index) {
  if (index < 0 || index >= count) { return -1; }  // out-of-range sentinel
  return arr[index];
}

bool Vector10::push_back(int value) {
  if (size > count - 1) { return false; }  // full -> reject
  arr[size] = value;                       // write at next free slot
  size += 1;
  return true;
}
```

**Example: Remove shifts later elements left so data stays contiguous:**

```cpp
bool Vector10::remove(int index) {
  if (index < 0 || index >= count) { return false; }
  int right = index + 1;
  for (int i = index; i < count; i++) {   // [100,200,300,400] remove 1 -> [100,300,400]
    arr[i] = arr[right];
    right += 1;
  }
  return true;
}
```

## Key takeaways

- An array keeps elements in **contiguous memory**, packed from index 0.
- **Always bounds-check.** `index < 0 || index >= count` guards from index out of bounds errors.
- Removing from the middle requires a **left shift** $$O(n)$$
- Complexity: array **random access is `O(1)`**; **insert/remove in the middle is `O(n)`**.
---

# Week 3: Linked Lists & Heap Memory (HW 2: LinkedList)

## Linked Lists

```cpp
struct node {
    int data; // value stored in this node
    node* next;  // pointer to the next node (NULL if this is the last)
};
```

- Nodes are made of a **value** and a **link/pointer**.
- The pointer to the start of the list is the **head**.
- Each node points to the next; the **last node points to `NULL`**.
- Operations: `init`, `append`, `insert`, `remove`, `query`, `size`, `report` (CRUD).

### Insert after a node

```cpp
//Needs a pointer address to insert after
void IntNode::InsertAfter(IntNode* nodeLoc) {
    IntNode* tmpNext = this->nextNodePtr; //grab the old link so the new pointer can point to it
    this->nextNodePtr = nodeLoc; // insert the new node and point to it from the node before it
    nodeLoc->nextNodePtr = tmpNext; // new node points to the saved node and now sits between the two
}
```

The 3-step order matters: save the old link **before** overwriting it, or you leak the rest of the list.

## Stack vs. Heap Memory

- The `new` keyword allocates on the **heap**; you must `delete` it when finished with it.
- Local variables and the pointer *itself* live on the **stack** and are reclaimed automatically.

```cpp
void eatMemory() {
    int* y = new int(10); // array is stroed in heap memory; y (a pointer) is on the stack
    delete y; // free the heap memory allocation
}

void someMethod() {
    int brad = 42; //the meaning of life
    int* dave = &brad;  // a pointer named dave points to brad memory location
    int** polly = &dave;  // pointer to a pointer. polly points to dave's pointer memory location
    cout << "*polly:  " << *polly  << endl; // dave's address in memory
    cout << "**polly: " << **polly << endl; // 42 points to the pointer and gets the data that pointer is pointing to
}
```

## HW 2: LinkedList

**Example: build a node and append it:**


```cpp
//Constructor for creating a new Node and returning the pointer
node* LinkedList::init_node(int data) {
  node* ret = new node; // pointer to this node. Node itself allocated on heap
  ret->data = data; //required as input 
  ret->next = nullptr; //null pointer is default until reassigned
  return ret;
}

void LinkedList::append_data(int data) {
  node* appended_node = init_node(data);
  if (top_ptr_ == nullptr) { top_ptr_ = appended_node; }  // first node
  else { traverse(top_ptr_)->next = appended_node; }       // start at top_ptr_ and continue until ->next is null
}
```

**Example: Remove by pointer:**

```cpp
void LinkedList::remove(int offset) {
  if (offset == 0) {  // removing the head
    node* old_top = top_ptr_; //first node is targeted for deletion, so top pointer is moved to point at next node
    top_ptr_ = top_ptr_->next; //points to new head now
    delete old_top; // reclaim heap memory
  } else {
    node* prev = get_offset_node(offset); //using helper function to find the node just before the targeted node
    node* target = prev->next; //get pointer to targeted node
    prev->next = target->next; //point to the node after the target, removing the target node from the LinkedList
    delete target; // get that memory back son
  }
}
```

## Key takeaways

- A linked list trades **random access** for **cheap structural edits**: traversal is `O(n)`, so
  `append` without a tail pointer is `O(n)`, while **insert/remove at the head is `O(1)`**.
- `delete` every node you `new` using the destructor.
- Draw the pointers before coding an insert/remove; update links in an order that never drops the
  rest of the list.

---

# Week 4 Recursion & Binary Search Trees

## Recursion

A **recursive function** is one that calls itself, shrinking the problem until it reaches a
**base case**.

```cpp
int add_it_up(int num) {
    if (num > 0) { return num + add_it_up(num - 1); }
    else         { return 0; }
}
```

The call **descends** to the base case, then **unwinds** in reverse order:

```text
add_it_up(6)
= 6 + add_it_up(5)          // return 6 plus add_it_up
    = 5 + add_it_up(4)
        = 4 + add_it_up(3)
            = 3 + add_it_up(2)
                = 2 + add_it_up(1)
                    = 1 + add_it_up(0)
                        = 0             // base case
  // unwind: 0 -> 1 -> 3 -> 6 -> 10 -> 15 -> 21
```

![Recursion call and unwind diagram](Week_4/Recursion_Flowchart.png)

*The call descends to the base case, then unwinds back up.*

**Heuristic for recursion:**

| # | Question | Action |
|---|----------|--------|
| 1 | Are we in a done state with a final answer? | Return |
| 2 | Are we in a state where we can't continue? | Return |
| 3 | Can we break the problem down further? | Break it down & recurse |
| * | The key is detecting **when to stop** recursing. | |

## Trees

- In a **tree**, nodes can have 0 or more children; **null links are not drawn** by convention.

## Binary Search Trees

- Each node has **at most 2 children**.
- **Left** subtree values are **less than** the parent.
- **Right** subtree values are **greater than or equal to** the parent.
- Operations: `init_node`, `insert`/`insert_data`, `remove`, `contains`, `get_node`, `size`,
  `to_array` (sorted, via inorder traversal).

![BST visual](Week_4/BST_Removal.png)

## HW 3: BST

**Example: Insert:**

```cpp
void BST::insert(bst_node* new_node) {
  if (*root == NULL) { *root = new_node; return; } //first node
  bst_node* cur = *root; //start at the root
  while (true) { //keep on keepin on 
    if (new_node->data < cur->data) { // go left if data is smaller (Go West young man, havne't you been told?...)
      if (cur->left == NULL) { cur->left = new_node; return; }//end of the branch, insert here
      cur = cur->left; //moves to the node just inserted
    } else { // tie goes to the runnner so everything else goes right
      if (cur->right == NULL) { cur->right = new_node; return; }//same same, just to the right
      cur = cur->right;
    }
  }
}
```

**Example: Recursive removal returns the new subtree root; use the successor when a node has two
children: (see wicked awesome drawing above)**

```cpp
bst_node* BST::remove_node(bst_node* subt, int data) {
  if (subt == NULL) { return NULL; } //base case when recursive subtree is null / empty
  if (data < subt->data) { subt->left  = remove_node(subt->left,  data); return subt; }//keep calling everytime this is true with the new subtree
  if (data > subt->data) { subt->right = remove_node(subt->right, data); return subt; } // samezies
    
  // this empty space is actually the base case when the node is found. it's implied since the above 2 if statements are false
  
  if (subt->left == NULL)  { bst_node* c = subt->right; delete subt; return c; } //save the pointer before deleting the node
  if (subt->right == NULL) { bst_node* c = subt->left;  delete subt; return c; }

  // two children: replace with in-order successor (right once, then leftmost)
  bst_node* successor = subt->right; //go right ONCE and save the pointer
  while (successor->left != NULL) { successor = successor->left; }// we're looking for the smallest remaining value in the subtree to crown as successor
  subt->data = successor->data; // shuffle the successor to the top of the subtree (replacing the targeted nodes data with the data from successor)
  subt->right = remove_node(subt->right, successor->data);//remove the targeted node
  return subt;
}
```

**Example: Inorder traversal yields sorted order:**

```cpp
//pass a vector by reference to store contents of the BST in order
void BST::to_vector(bst_node* subt, vector<int>& vec) {
  if (subt == NULL) { return; } //base case then unwind all lowest found data values
  to_vector(subt->left, vec);// keep going left until we reach the smallest value
  vec.push_back(subt->data); // write the smallest value to the vector
  to_vector(subt->right, vec);// go right and then go back left until next smallest data value
}
```

## Key takeaways

- **Base case first**, then recurse on a *smaller* problem.
- The double-pointer root lets code change the root without a special case.
- A plain BST is **not balanced**: a single-chain tree degrades to `O(n)`, a balanced one is `O(log n)`.

---

# Week 5: Complexity & Sorting

## Computational Complexity

- The number of items is $$n$$; complexity is written $$O$$.
- $$O(n)$$ = about $$n$$ operations to touch the whole list.
  - $$O(n!)$$ factorial $$\cdot$$ $$O(n^2)$$ polynomial $$\cdot$$ $$O(n)$$ linear $$\cdot$$ $$O(\log n)$$ logarithmic $$\cdot$$ $$O(1)$$ constant.
- Keep only the **dominant term**: $$4n^3 + 10n^2 + 100000 \Rightarrow O(n^3)$$.
- Big-O most often refers to **runtime**, but can also describe **space**.

### Linear algorithms & bounds

- Linear algorithms are $$O(n)$$, with a **best (lower) bound** and **worst (upper) bound**.
- Example: worst $$3n^2 + 10n + 17$$, best $$2n^2 + 5n + 5$$.
  - Lower bound $$2n^2$$; upper bound $$30n^2$$.
  - At $$n = 1$$, $$3 + 10 + 17 = 30 > 3$$ — this is why $$3n^2$$ alone is not the upper bound.

### BST complexity

- A single-branch (unbalanced) tree is $$O(n)$$ because every node may need to be checked.
- A balanced tree halves the search space each step → $$O(\log n)$$.

### Hard problems

**Traveling Salesman Problem (TSP):** visit every city once, start/end at the same city, minimize
distance. Brute force checks every route → $$O(n!)$$ (billions of routes).

### Heuristics

A **heuristic** is not guaranteed to find the optimal solution, but is often much faster — it
approximates a near-optimal answer.

## Sorting algorithms

### Bubble sort: &nbsp; $$O(n^2)$$

Compare adjacent items and swap if out of order. Requires multiple passes.

```cpp
void bubblesort(vector<int>& data) {
  int size = data.size();
  for (int i = 0; i < size - 1; i++)
    for (int j = 0; j < size - i - 1; j++)
      if (data[j] > data[j + 1]) swap(data[j], data[j + 1]);
}
```

### Merge sort: &nbsp; $$O(n \log n)$$

Divide the list in half, sort each half recursively, then merge.

```cpp
void mergesort(vector<int>& data) {
  if (data.size() <= 1) return;// base case. vector is chopped into a single element
  int mid = data.size() / 2; // get the mid point of remaining number of values
  vector<int> left(data.begin(), data.begin() + mid); //temp vector to hold the left side of the split original vector
  vector<int> right(data.begin() + mid, data.end()); //temp vector to hold the right side of the split original vector
  mergesort(left);// keep feeding the small vectors until they are single elements
  mergesort(right);
  merge(left, right, data);// combine everything. This is a helper function defined elsewhere
}
```

### Quick sort: Average $$O(n \log n)$$, worst $$O(n^2)$$

Pick a **pivot**; move smaller values left and larger right, then recurse on each side.

```cpp
void quicksort(vector<int>& data, int low_idx, int high_idx) {
  if (high_idx <= low_idx) return; // base case
  int split = quicksort_partition(data, low_idx, high_idx); // helper function defined elsewhere which returns the split index
  
  quicksort(data, low_idx, split); // recurse on the left side
  quicksort(data, split + 1, high_idx); // recurse on the right side
}

int quicksort_partition(vector<int>& data, int low_idx, int high_idx) {
  int pivot = data[low_idx + (high_idx - low_idx) / 2];
  bool done = false;
  while (!done) {
    while (data[low_idx] < pivot) low_idx++;
    while (data[high_idx] > pivot) high_idx--;
    if (low_idx >= high_idx) done = true;
    else { swap(data[low_idx], data[high_idx]); low_idx++; high_idx--; }
  }
  return high_idx;
}
```

## HW 4: Sorting

Implement `quicksort`/`quicksort_partition`, `bubblesort`, `mergesort`/`merge`, and a
**mystery sort** of your choice. Sorting must be **in place / non-decreasing**.

**Mystery sort chosen: Radix sort** (LSD, base 10) — a **non-comparison** sort that groups values
into buckets by digit, one digit position per pass. Note the `getMaxLength`/`createBuckets` helpers
that drive the passes:

```cpp

//I did Radix sort. this is just a tiny portion of the sort algorithm
void mystery_sort(vector<int>& data) {
  int max = getMaxLength(data); // how many place values are there?
  for (int i = 0; i < max; i++) {
    createBuckets(data, pow(10, i)); // 10^i = 1, 10, 100, 1000 until we're out of place values
  }
}
```

## Key takeaways

- **Rate of growth, not exact step counts:** drop constants and lower-order terms.
- Comparisons matter: bubble `O(n^2)`, merge/quicksort `O(n \log n)`, radix `O(nk)` for `k` digits.
- **Merge sort** is stable and predictable; **quick sort** is fast in practice but degrades to
  `O(n^2)` on bad pivots; **radix sort** beats `O(n \log n)` when the key range is small or $$n$$ is large.
- "This will be on the test" (per the HW 4 README): know time **and** space complexity of each sort.

---

# Complexity Quick Reference

## Data structure operations

| Structure | Access | Search | Insert | Delete | Notes |
|-----------|:------:|:------:|:------:|:------:|-------|
| Array / `Vector10` | $$O(1)$$ | $$O(n)$$ | $$O(n)$$ (shift) | $$O(n)$$ (shift) | contiguous; fix size (Vector10) |
| `std::vector` | $$O(1)$$ | $$O(n)$$ | $$O(1)$$ amortized push_back | $$O(n)$$ | dynamic/resizable |
| Linked list | $$O(n)$$ | $$O(n)$$ | $$O(1)$$ at head | $$O(1)$$ at head | `O(n)` append without tail |
| Stack / Queue | — | — | $$O(1)$$ | $$O(1)$$ | LIFO / FIFO |
| Hash table | — | $$O(1)$$ avg | $$O(1)$$ avg | $$O(1)$$ avg | worst `O(n)` |
| BST (unbalanced) | — | $$O(n)$$ | $$O(n)$$ | $$O(n)$$ | can degenerate to a chain |
| BST (balanced) | — | $$O(\log n)$$ | $$O(\log n)$$ | $$O(\log n)$$ | halves the space each step |
| Heap | $$O(1)$$ peek | — | $$O(\log n)$$ | $$O(\log n)$$ | max/min at root |

## Sorting algorithms

| Algorithm | Best | Average | Worst | Space | Stable | Method |
|-----------|:----:|:-------:|:-----:|:-----:|:------:|--------|
| Bubble | $$O(n)$$ | $$O(n^2)$$ | $$O(n^2)$$ | $$O(1)$$ | Yes | comparisons/swaps |
| Selection | $$O(n^2)$$ | $$O(n^2)$$ | $$O(n^2)$$ | $$O(1)$$ | No | pick min repeatedly |
| Insertion | $$O(n)$$ | $$O(n^2)$$ | $$O(n^2)$$ | $$O(1)$$ | Yes | shift into place |
| Merge | $$O(n \log n)$$ | $$O(n \log n)$$ | $$O(n \log n)$$ | $$O(n)$$ | Yes | divide & merge |
| Quick | $$O(n \log n)$$ | $$O(n \log n)$$ | $$O(n^2)$$ | $$O(\log n)$$ | No | partition around pivot |
| Radix (LSD) | $$O(nk)$$ | $$O(nk)$$ | $$O(nk)$$ | $$O(n + k)$$ | Yes | non-comparison, digit buckets |

## Complexity growth (slow → fast)

$$O(1) < O(\log n) < O(n) < O(n \log n) < O(n^2) < O(n^3) < O(2^n) < O(n!)$$

---

# Exam Traps & Gotchas

- **Off-by-one / bounds:** guard `index < 0 || index >= count`; `size()` is unsigned — cast before
  subtracting.
- **Forgetting `delete`** on heap nodes → memory leaks (linked list, BST).
- **Overwriting a `next` pointer before saving it** → you lose the rest of the list; save the link first.
- **Recursive pointer updates:** always reassign the returned subtree root
  (`subt->left = remove_node(...)`), or the change is lost.
- **Equal values in a BST** go **right**.
- **Removing a two-child BST node** requires the **successor** (right once, then leftmost).
- **Data must stay contiguous** after a Vector10 `remove` you must shift the tail left $$O(n)$$.
- **Inserting into a Vector10** requires shifting the tail right $$O(n)$$.
- **Appending to a Vector10** requires no shifting shifting the tail right $$O(1)$$.
