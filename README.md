# Mobile_DSA


### 1. How can you use a stack to handle undo operations in a mobile application?

In a mobile application, a stack is an ideal data structure to handle undo operations. A stack operates on the principle of Last In, First Out (LIFO), which makes it well-suited for undo functionalities, where the last action performed should be the first one to be undone.

Here's how you can implement undo operations using a stack in JavaScript:

##### Steps to Implement Undo with a Stack:
Initialize a Stack: Create a stack that stores actions performed by the user. Each action can be represented as an object containing the action type and any necessary state (e.g., what was changed, previous values).

1. Push Actions to the Stack: Each time the user performs an action, push the action details onto the stack. This saves the current state, which will allow you to revert it later.
2. Undo Action: When the user requests to undo an action, pop the most recent action from the stack and use its details to reverse the change.
3. Redo (Optional): If you want to implement redo, you can maintain another stack for storing undone actions, allowing the user to redo them if needed.

```js
class UndoManager {
  constructor() {
    this.undoStack = [];
    this.redoStack = []; // Optional: for redo functionality
  }

  performAction(action) {
    // Perform the action and push it to the undo stack
    this.undoStack.push(action);
    // Clear the redo stack when a new action is performed
    this.redoStack = [];
  }

  undo() {
    if (this.undoStack.length === 0) {
      console.log("Nothing to undo.");
      return;
    }

    // Get the last action from the undo stack
    const lastAction = this.undoStack.pop();
    
    // Reverse the last action
    if (lastAction.type === 'add') {
      this.removeItem(lastAction.item);
    } else if (lastAction.type === 'remove') {
      this.addItem(lastAction.item);
    }

    // Optional: Push the undone action to the redo stack
    this.redoStack.push(lastAction);
  }

  redo() {
    if (this.redoStack.length === 0) {
      console.log("Nothing to redo.");
      return;
    }

    // Get the last undone action
    const lastUndoneAction = this.redoStack.pop();
    
    // Reapply the action
    if (lastUndoneAction.type === 'add') {
      this.addItem(lastUndoneAction.item);
    } else if (lastUndoneAction.type === 'remove') {
      this.removeItem(lastUndoneAction.item);
    }

    // Push the redone action back to the undo stack
    this.undoStack.push(lastUndoneAction);
  }

  // Example item manipulations
  addItem(item) {
    console.log(`Added item: ${item}`);
  }

  removeItem(item) {
    console.log(`Removed item: ${item}`);
  }
}

// Usage:
const undoManager = new UndoManager();

// Perform actions
undoManager.performAction({ type: 'add', item: 'Item1' });
undoManager.performAction({ type: 'remove', item: 'Item2' });

// Undo the last action
undoManager.undo(); // Removes 'Item1'

// Redo the undone action
undoManager.redo(); // Adds 'Item1' back

```
#### Explanation:
1. PerformAction: Adds an action to the undo stack.
2. Undo: Pops the last action from the undo stack, reverses it, and optionally pushes it to the redo stack.
3. Redo (optional): Reapplies an undone action and moves it back to the undo stack.
4. You can adapt this approach to fit the specific operations and state management needs of your mobile app.

---
### 2. Describe a scenario where a queue would be useful in managing asynchronous tasks in a mobile app. 

A queue can be very useful for managing asynchronous tasks in a mobile app, especially when dealing with tasks that need to be executed in a specific order or need to be processed one at a time to avoid overwhelming system resources. Here's a scenario where a queue would be particularly beneficial:

Scenario: Handling Multiple Network Requests in a Chat Application

Problem:
Imagine a chat application where users can send and receive messages. Each time a user sends a message, the app needs to perform a series of asynchronous tasks:

Send Message: The app sends the message to the server.
Save Locally: The app saves the message locally in the device's storage.
Update UI: The app updates the UI to reflect the sent message.
Sometimes, users might send multiple messages in quick succession. If these messages are handled asynchronously without any order, it can lead to race conditions, message loss, or inconsistent UI updates.

Solution: Using a Queue
To handle this scenario effectively, a queue can be used to manage the sequence of tasks for each message. Here's how it can be implemented:

Initialize a Queue: Create a queue to manage the tasks associated with sending messages. Each entry in the queue represents a message and the tasks that need to be performed for that message.

Enqueue Tasks: When a user sends a message, enqueue the following tasks:

Send Message: Task to send the message to the server.
Save Locally: Task to save the message in local storage.
Update UI: Task to update the UI.
Process Queue: Process the tasks in the queue sequentially. Ensure that only one task per message is processed at a time. For instance, start with sending the message, then save it locally, and finally update the UI.

Handle Errors: Implement error handling for each task. If a task fails, handle the failure appropriately (e.g., retry the task, alert the user, etc.), and move on to the next task.

```js
class TaskQueue {
  constructor() {
    this.queue = [];
    this.isProcessing = false;
  }

  enqueue(task) {
    this.queue.push(task);
    this.processQueue();
  }

  async processQueue() {
    if (this.isProcessing) return;
    this.isProcessing = true;

    while (this.queue.length > 0) {
      const currentTask = this.queue.shift();
      try {
        await currentTask();
      } catch (error) {
        console.error("Task failed:", error);
        // Handle error (e.g., retry, alert user)
      }
    }

    this.isProcessing = false;
  }
}

// Example usage:
const taskQueue = new TaskQueue();

function sendMessage(message) {
  return new Promise((resolve, reject) => {
    // Simulate sending message
    setTimeout(() => {
      console.log(`Message sent: ${message}`);
      resolve();
    }, 1000);
  });
}

function saveLocally(message) {
  return new Promise((resolve) => {
    // Simulate saving message
    setTimeout(() => {
      console.log(`Message saved locally: ${message}`);
      resolve();
    }, 500);
  });
}

function updateUI(message) {
  return new Promise((resolve) => {
    // Simulate UI update
    setTimeout(() => {
      console.log(`UI updated with message: ${message}`);
      resolve();
    }, 300);
  });
}

function processMessage(message) {
  taskQueue.enqueue(async () => {
    await sendMessage(message);
    await saveLocally(message);
    await updateUI(message);
  });
}

// Simulate sending multiple messages
processMessage("Hello!");
processMessage("How are you?");
processMessage("Goodbye!");

```
Explanation:

TaskQueue Class: Manages the queue of tasks and processes them one at a time.
enqueue: Adds tasks to the queue and starts processing if not already processing.
processQueue: Processes tasks in the queue sequentially.
processMessage: Enqueues tasks associated with a single message.

Using a queue in this way ensures that each message is processed in the correct order and that the tasks associated with each message are handled sequentially, preventing potential issues with race conditions or resource conflicts.


### 3. How would you implement a priority queue for managing notifications or tasks based on priority in a mobile app ?

A Priority Queue is a useful data structure for managing tasks or notifications in a mobile app based on their priority. In a priority queue, each task is associated with a priority, and tasks with higher priority are processed before those with lower priority.

Use Case Scenario: Notification System in a Mobile App
Imagine a mobile app where notifications have different levels of importance:

Critical notifications (e.g., system alerts, urgent messages) should be displayed immediately.
Medium priority notifications (e.g., social updates) can be displayed after critical notifications.
Low priority notifications (e.g., promotional messages) can be delayed until higher-priority notifications are handled.
A priority queue would ensure that higher-priority notifications are always handled first, even if lower-priority ones are added earlier.

Steps to Implement a Priority Queue in JavaScript
Define the Priority Queue Class: Create a class that manages a queue of tasks or notifications. Each entry will have a priority associated with it.

Enqueue: When a new task or notification is added, it should be placed in the queue according to its priority.

Dequeue: The highest-priority task should be processed first.

Sort by Priority: The queue should maintain its order, ensuring the highest-priority task is always at the front.

```js
class PriorityQueue {
  constructor() {
    this.queue = [];
  }

  // Add an item with a given priority to the queue
  enqueue(item, priority) {
    const task = { item, priority };

    // Find the correct position to insert the task based on priority
    let added = false;
    for (let i = 0; i < this.queue.length; i++) {
      if (this.queue[i].priority < task.priority) {
        this.queue.splice(i, 0, task);
        added = true;
        break;
      }
    }

    // If not added yet, append it to the end of the queue
    if (!added) {
      this.queue.push(task);
    }
  }

  // Remove and return the highest-priority item from the queue
  dequeue() {
    if (this.queue.length === 0) {
      return "Queue is empty";
    }
    return this.queue.shift().item;
  }

  // View the highest-priority item without removing it
  peek() {
    if (this.queue.length === 0) {
      return "Queue is empty";
    }
    return this.queue[0].item;
  }

  // Check if the queue is empty
  isEmpty() {
    return this.queue.length === 0;
  }
}

// Example usage:
const notificationQueue = new PriorityQueue();

// Add notifications with different priorities
notificationQueue.enqueue('Low-priority notification', 1);
notificationQueue.enqueue('Critical system alert', 5);
notificationQueue.enqueue('Social update', 3);

// Process notifications based on priority
console.log(notificationQueue.dequeue()); // "Critical system alert"
console.log(notificationQueue.dequeue()); // "Social update"
console.log(notificationQueue.dequeue()); // "Low-priority notification"

```

Explanation:
enqueue(item, priority):

Takes an item (e.g., a notification or task) and a priority value (higher values indicate higher priority).
It inserts the item at the correct position in the queue based on its priority, ensuring that higher-priority items come before lower-priority ones.
dequeue():

Removes and returns the highest-priority item (the first item in the queue).
If the queue is empty, it returns a message indicating that.
peek():

Returns the highest-priority item without removing it from the queue.
isEmpty():

Checks whether the queue is empty.
How It Works in the Mobile App:
In a notification system, notifications would be added to the priority queue based on their urgency:

Critical system alerts would have the highest priority and would be displayed first.
Social updates would have a medium priority.
Promotional messages would have the lowest priority and would only be shown after more important notifications have been handled.
The app would use enqueue() to add notifications to the queue, and dequeue() to process them in order of priority, ensuring the most important notifications are always displayed first.

Customization Options:
Dynamic Priorities: You can dynamically adjust the priority based on user interaction or app context. For example, if the app is in the foreground, certain notifications might become more important.

Task Timeouts: You can add a timeout mechanism that processes lower-priority tasks after a certain amount of time, even if higher-priority tasks are pending.

Thresholds: Implement thresholds for priority levels where, for example, tasks below a certain priority level are ignored if higher-priority tasks arrive within a given time frame.

Conclusion:
A priority queue in a mobile app allows you to manage tasks like notifications effectively, ensuring that important tasks are handled first. By using the enqueue() method to add tasks in order of priority and dequeue() to handle them, you can manage tasks or notifications in a responsive and efficient manner.


### 3. Explain how a Trie data structure can be used for efficient autocomplete functionality in a mobile app.

A Trie (pronounced "try") is a tree-like data structure that is highly efficient for handling operations on strings, making it an excellent choice for implementing autocomplete functionality in a mobile app. Here's how a Trie works and how it can be used for efficient autocompletion.

How a Trie Data Structure Works
A Trie is structured where:

Each node represents a single character of a string.
The root node is an empty node.
Each path down the tree represents a string, and a word is formed by traversing from the root node to the leaf node.
Each node can have multiple children, corresponding to the possible next characters.
Key Features of a Trie:
Efficient prefix matching: Trie allows efficient searching of strings that share a common prefix.
Fast lookup: Operations like insertion, deletion, and search are fast (typically O(L), where L is the length of the word).
Memory efficiency: Although it stores many words, overlapping prefixes are stored only once.
Example Structure of a Trie:
Let's say you want to store the following words in a Trie:

"car"
"cat"
"can"
"dog"
"dot"

The Trie would look like this:


           (root)
           /   \
          c     d
        / | \   / \
       a  a  a o   o
      /  /   | |   |
     r  t    n g   t

How to Use a Trie for Autocomplete
Building the Trie: Insert all possible words into the Trie. Each character of the word is added as a node, and words that share common prefixes share nodes.

Search for a Prefix: When the user types a prefix (e.g., "ca"), traverse the Trie from the root node, following the nodes that match the characters in the prefix. Once the last character of the prefix is found, all the descendant nodes form potential autocomplete suggestions.

Collect Suggestions: From the node corresponding to the last character of the prefix, perform a depth-first search (DFS) or breadth-first search (BFS) to collect all words that are valid completions of the prefix.

Steps to Implement Autocomplete Using a Trie
Here’s how you can implement this functionality in JavaScript:

```js
class TrieNode {
  constructor() {
    this.children = {};
    this.isEndOfWord = false;  // Marks the end of a word
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  // Insert a word into the Trie
  insert(word) {
    let node = this.root;
    for (let char of word) {
      if (!node.children[char]) {
        node.children[char] = new TrieNode();
      }
      node = node.children[char];
    }
    node.isEndOfWord = true;
  }

  // Search for words that start with the given prefix
  searchPrefix(prefix) {
    let node = this.root;
    for (let char of prefix) {
      if (!node.children[char]) {
        return null;  // Prefix not found
      }
      node = node.children[char];
    }
    return node;  // Return the node where the prefix ends
  }

  // Find all words with the given prefix
  autoComplete(prefix) {
    const node = this.searchPrefix(prefix);
    if (!node) {
      return [];  // No words found
    }
    const suggestions = [];
    this.dfs(node, prefix, suggestions);  // Collect all words from the prefix node
    return suggestions;
  }

  // Depth-first search (DFS) to find all words that are descendants of the current node
  dfs(node, prefix, suggestions) {
    if (node.isEndOfWord) {
      suggestions.push(prefix);
    }
    for (let char in node.children) {
      this.dfs(node.children[char], prefix + char, suggestions);
    }
  }
}

// Example usage:
const trie = new Trie();
const words = ["car", "cat", "can", "dog", "dot", "card", "care"];
words.forEach(word => trie.insert(word));

const prefix = "ca";
const suggestions = trie.autoComplete(prefix);
console.log(`Autocomplete suggestions for "${prefix}":`, suggestions);
// Output: Autocomplete suggestions for "ca": ["car", "cat", "can", "card", "care"]

```

Explanation of the Code:
TrieNode Class: Each node in the Trie has a children object (which holds references to child nodes) and a flag isEndOfWord to mark the end of a word.

Trie Class:

insert(): Adds a word to the Trie by creating nodes for each character.
searchPrefix(): Searches for a given prefix in the Trie and returns the node where the prefix ends.
autoComplete(): Finds all possible words that complete the given prefix by calling the DFS method.
dfs(): Recursively explores the Trie starting from the node where the prefix ends, collecting all valid words.
Usage:

The words "car", "cat", "can", "dog", "dot", etc., are inserted into the Trie.
When the user types "ca", the autoComplete() function searches for all words starting with "ca", returning suggestions like "car", "cat", "can", "card", and "care".
Benefits of Using a Trie for Autocomplete:
Efficient Prefix Search: The Trie enables efficient lookup of words starting with a given prefix, typically in O(L), where L is the length of the prefix. This is faster than searching through an unsorted list of words.

Scalability: A Trie can store a large number of words and allows quick access to them, making it ideal for autocompletion in apps that deal with large vocabularies or datasets (e.g., messaging apps, search engines).

Memory Efficiency: Words with shared prefixes are stored compactly, minimizing the overall memory footprint. This is especially important in mobile applications with limited resources.

Optimizations and Variations:
Weighted Trie: Assign weights to each word in the Trie based on frequency or user preferences. This allows the app to suggest more relevant or commonly used words first.

Limit Suggestions: In mobile apps, limit the number of suggestions (e.g., only show the top 5 results) for a better user experience.

Predictive Text: Combine a Trie with machine learning or statistical models to improve prediction accuracy by analyzing the user's typing patterns or previous queries.

Conclusion:
A Trie is a powerful data structure for implementing efficient autocomplete functionality in a mobile app. It allows you to quickly find all words that match a given prefix and scale the autocomplete feature for large datasets, providing a responsive and user-friendly experience.

---

### 5. How would you use a BST to efficiently search and manage a list of items in a mobile app?

A Binary Search Tree (BST) is a tree data structure where each node has at most two children, referred to as the left and right child. In a BST, the left child contains nodes with values less than the parent, and the right child contains nodes with values greater than the parent. This structure enables efficient searching, insertion, and deletion operations, making it useful for managing and searching lists of items in a mobile app.

Use Case Scenario: Managing and Searching a Sorted List of Items
Let’s assume you have a mobile app that displays a list of items (such as products, contacts, or files) that can be searched, added, or removed efficiently. You want to support fast searches, inserts, and deletes, and keep the items sorted for quick display. A Binary Search Tree (BST) would be a great data structure to manage this list.

Why Use a BST?
Efficient Search: A balanced BST allows you to search for items in O(log N) time, making it faster than linear search (O(N)) in an unsorted list.
Dynamic Insertion/Deletion: A BST allows for dynamic insertion and deletion of items while maintaining the sorted order, without needing to re-sort the list like in an array.
In-order Traversal: The in-order traversal of a BST gives you the items in a sorted order, perfect for displaying sorted lists.
Operations in a BST
Search: Given a key (item), you can efficiently locate it by recursively comparing it to the root node and moving left or right until you find the item or reach a leaf node.
Insert: When inserting a new item, compare it with the root node, then recursively move left or right to find the correct position to maintain the BST properties.
Delete: Deleting an item involves finding the node and removing it while ensuring that the BST properties are maintained.
In-order Traversal: Traverse the tree in sorted order by recursively visiting the left subtree, the current node, and then the right subtree.

Implementing a BST in JavaScript

```js
class Node {
  constructor(key, value) {
    this.key = key;    // The item’s key (e.g., product ID, name)
    this.value = value; // The item’s data (e.g., product details)
    this.left = null;   // Left child node
    this.right = null;  // Right child node
  }
}

class BST {
  constructor() {
    this.root = null;
  }

  // Insert a new item into the BST
  insert(key, value) {
    const newNode = new Node(key, value);
    if (!this.root) {
      this.root = newNode;
    } else {
      this.insertNode(this.root, newNode);
    }
  }

  insertNode(node, newNode) {
    if (newNode.key < node.key) {
      // Go to the left
      if (!node.left) {
        node.left = newNode;
      } else {
        this.insertNode(node.left, newNode);
      }
    } else {
      // Go to the right
      if (!node.right) {
        node.right = newNode;
      } else {
        this.insertNode(node.right, newNode);
      }
    }
  }

  // Search for an item by key
  search(key) {
    return this.searchNode(this.root, key);
  }

  searchNode(node, key) {
    if (!node) {
      return null;  // Key not found
    }
    if (key < node.key) {
      return this.searchNode(node.left, key);  // Go to the left
    } else if (key > node.key) {
      return this.searchNode(node.right, key); // Go to the right
    } else {
      return node.value;  // Key found
    }
  }

  // Remove an item by key
  remove(key) {
    this.root = this.removeNode(this.root, key);
  }

  removeNode(node, key) {
    if (!node) {
      return null;
    }
    if (key < node.key) {
      node.left = this.removeNode(node.left, key);
      return node;
    } else if (key > node.key) {
      node.right = this.removeNode(node.right, key);
      return node;
    } else {
      // Node to be deleted found
      // Case 1: No child
      if (!node.left && !node.right) {
        return null;
      }
      // Case 2: One child
      if (!node.left) {
        return node.right;
      }
      if (!node.right) {
        return node.left;
      }
      // Case 3: Two children
      const minRightNode = this.findMinNode(node.right);
      node.key = minRightNode.key;
      node.value = minRightNode.value;
      node.right = this.removeNode(node.right, minRightNode.key);
      return node;
    }
  }

  // Find the minimum node in the tree (used for deletion)
  findMinNode(node) {
    while (node && node.left) {
      node = node.left;
    }
    return node;
  }

  // In-order traversal (sorted order)
  inOrderTraverse(node = this.root, callback) {
    if (node !== null) {
      this.inOrderTraverse(node.left, callback);
      callback(node.key, node.value);
      this.inOrderTraverse(node.right, callback);
    }
  }
}

// Example usage:
const bst = new BST();

// Insert some items (e.g., product ID and product name)
bst.insert(10, "Product A");
bst.insert(20, "Product B");
bst.insert(5, "Product C");
bst.insert(15, "Product D");

// Search for an item
const searchResult = bst.search(10);
console.log(`Found: ${searchResult}`); // Found: Product A

// Remove an item
bst.remove(5);

// Traverse the BST in sorted order
console.log("Items in sorted order:");
bst.inOrderTraverse(bst.root, (key, value) => {
  console.log(`${key}: ${value}`);
});
// Output:
// 10: Product A
// 15: Product D
// 20: Product B

```

Explanation of the Code:
Node Class: Each node stores a key (such as an item ID or name) and a value (such as item details). It also has left and right child references.
BST Class:
insert(): Adds a new item into the tree at the appropriate position to maintain the BST property.
search(): Recursively searches the tree for a specific item by its key.
remove(): Deletes an item by key and restructures the tree to maintain the BST property.
inOrderTraverse(): Traverses the tree in sorted order (left to right), allowing the items to be displayed in a sorted list.
Benefits of Using a BST for Managing Items:
Efficient Search: Searching for an item in a balanced BST takes O(log N) time, making it faster than a linear search through a list or array.

Maintained Sorted Order: An in-order traversal of the BST yields the items in sorted order, which is useful when displaying sorted lists in the app.

Dynamic Inserts and Deletes: Items can be inserted or deleted from the BST while maintaining the sorted order. This is ideal for apps where the list of items is frequently updated.

Scalability: BSTs work well for managing large lists of items. As long as the tree remains balanced (or you use a balanced variant like AVL or Red-Black Tree), it handles operations efficiently as the number of items grows.

Optimizations for a Mobile App:
Balanced BSTs: Use a self-balancing BST (e.g., AVL tree or Red-Black tree) to ensure that the tree remains balanced, guaranteeing O(log N) performance for search, insertion, and deletion.

Memory Considerations: Mobile apps have limited resources, so the memory overhead of managing pointers and recursive operations needs to be considered. Balanced trees are slightly more complex but ensure better performance in the long run.

Conclusion:
Using a Binary Search Tree (BST) in a mobile app allows for efficient management of dynamically changing lists of items. It supports fast search, insert, and delete operations while maintaining sorted order, making it an excellent choice for features like contact lists, product catalogs, or file management systems in mobile applications.

### 6. How can a heap be used to efficiently implement a priority-based task scheduler in a mobile app?

A heap is an ideal data structure to implement a priority-based task scheduler in a mobile app due to its efficiency in managing dynamic priority queues. The most common type of heap used for this purpose is the binary heap, either a min-heap or a max-heap, depending on whether higher or lower priority values take precedence.

Key Characteristics of a Heap:
A min-heap is a complete binary tree where the parent node has a value smaller than or equal to its children. The smallest value is always at the root.
A max-heap is similar, except that the parent node has a value larger than or equal to its children. The largest value is at the root.
Heaps allow for efficient insertion and removal of elements based on priority, with both operations typically taking O(log N) time.
Scenario: Priority-Based Task Scheduler in a Mobile App
Imagine you're building a task scheduler for a mobile app (e.g., a background task manager, a download manager, or a system that handles notifications). Tasks need to be executed based on their priority rather than the order they were added.

Higher priority tasks should be executed before lower priority tasks.
New tasks can arrive dynamically and must be inserted into the schedule in the correct position based on their priority.
Tasks can also be completed or canceled, and the schedule needs to be updated efficiently.
Why Use a Heap for Task Scheduling?
Efficient Priority Management: A heap ensures that the highest or lowest priority task is always at the root, allowing for O(1) access to the next task to be executed.
Efficient Insertion and Deletion: Inserting and deleting tasks in a heap takes O(log N) time, making it suitable for dynamically changing lists of tasks where priorities can vary.
Space Efficiency: A heap stores tasks in a compact way using an array or tree, which is efficient in terms of memory use, important for mobile applications with limited resources.
Steps to Implement a Priority-Based Task Scheduler with a Heap:
Task Representation: Each task has a priority and the task details. For example, the priority could be an integer, where lower values represent higher priority (in the case of a min-heap).

Insert Task: Add a new task to the heap, maintaining the heap property (i.e., the root is the highest or lowest priority task, depending on the heap type).

Pop Highest-Priority Task: Remove and return the root element, which is the highest-priority task, and restructure the heap to maintain its properties.

Task Execution: The scheduler executes the task with the highest priority and then pops the next task.

Example: Implementing a Task Scheduler Using a Min-Heap in JavaScript

```js
class MinHeap {
  constructor() {
    this.heap = [];
  }

  // Get the index of the parent, left child, and right child
  getParentIndex(i) { return Math.floor((i - 1) / 2); }
  getLeftChildIndex(i) { return 2 * i + 1; }
  getRightChildIndex(i) { return 2 * i + 2; }

  // Swap two elements in the heap
  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }

  // Insert a task into the heap
  insert(task) {
    this.heap.push(task);
    this.heapifyUp(this.heap.length - 1);  // Fix the heap property
  }

  // Maintain the heap property after insertion
  heapifyUp(index) {
    let currentIndex = index;
    let parentIndex = this.getParentIndex(currentIndex);
    
    // While the inserted task has higher priority (smaller value), move it up
    while (currentIndex > 0 && this.heap[currentIndex].priority < this.heap[parentIndex].priority) {
      this.swap(currentIndex, parentIndex);
      currentIndex = parentIndex;
      parentIndex = this.getParentIndex(currentIndex);
    }
  }

  // Remove the highest priority task (root)
  pop() {
    if (this.heap.length === 0) return null;
    
    const task = this.heap[0];
    const lastTask = this.heap.pop();
    
    if (this.heap.length > 0) {
      this.heap[0] = lastTask;
      this.heapifyDown(0);  // Fix the heap property
    }
    
    return task;
  }

  // Maintain the heap property after removal
  heapifyDown(index) {
    let currentIndex = index;
    const length = this.heap.length;
    
    while (true) {
      const leftChildIndex = this.getLeftChildIndex(currentIndex);
      const rightChildIndex = this.getRightChildIndex(currentIndex);
      let smallestIndex = currentIndex;

      // Find the smallest of current, left, and right child
      if (leftChildIndex < length && this.heap[leftChildIndex].priority < this.heap[smallestIndex].priority) {
        smallestIndex = leftChildIndex;
      }
      if (rightChildIndex < length && this.heap[rightChildIndex].priority < this.heap[smallestIndex].priority) {
        smallestIndex = rightChildIndex;
      }

      // If the smallest is still the current index, the heap is valid
      if (smallestIndex === currentIndex) break;

      // Swap and continue
      this.swap(currentIndex, smallestIndex);
      currentIndex = smallestIndex;
    }
  }

  // Peek at the highest-priority task without removing it
  peek() {
    return this.heap.length > 0 ? this.heap[0] : null;
  }

  // Check if the heap is empty
  isEmpty() {
    return this.heap.length === 0;
  }
}

// Task Scheduler Class
class TaskScheduler {
  constructor() {
    this.taskQueue = new MinHeap();
  }

  // Add a new task with a priority
  addTask(priority, taskDetails) {
    this.taskQueue.insert({ priority, taskDetails });
  }

  // Get and execute the highest-priority task
  runNextTask() {
    if (this.taskQueue.isEmpty()) {
      console.log("No tasks to run.");
      return;
    }
    const nextTask = this.taskQueue.pop();
    console.log(`Running task: ${nextTask.taskDetails} with priority ${nextTask.priority}`);
    return nextTask;
  }
}

// Example usage:
const scheduler = new TaskScheduler();

// Add some tasks with different priorities (lower number = higher priority)
scheduler.addTask(5, "Low-priority task");
scheduler.addTask(1, "High-priority task");
scheduler.addTask(3, "Medium-priority task");

// Run tasks in order of priority
scheduler.runNextTask();  // Output: Running task: High-priority task with priority 1
scheduler.runNextTask();  // Output: Running task: Medium-priority task with priority 3
scheduler.runNextTask();  // Output: Running task: Low-priority task with priority 5

```

Explanation of the Code:
MinHeap Class:
insert(): Adds a new task to the heap while maintaining the heap property (i.e., the smallest priority task is always at the root).
heapifyUp(): Moves the newly inserted task up the tree to restore the heap property.
pop(): Removes and returns the task with the highest priority (smallest value) from the heap, then restructures the heap using heapifyDown() to maintain the heap property.
peek(): Returns the highest-priority task without removing it.
TaskScheduler Class:
addTask(): Adds a new task to the scheduler with a specified priority.
runNextTask(): Executes the task with the highest priority by removing it from the heap.
Benefits of Using a Heap for Priority-Based Task Scheduling:
Efficient Priority Handling: Heaps allow the scheduler to quickly determine and remove the highest-priority task (O(1) for retrieval, O(log N) for removal).

Dynamic Task Management: New tasks can be added at any time, and the heap efficiently inserts them while maintaining the correct priority order (O(log N) insertion).

Optimized Resource Use: By always executing the most important task first, you optimize resource use in the app, particularly important for mobile devices with limited processing power and battery life.

Scalability: Heaps are highly scalable for task scheduling, especially for large numbers of dynamic tasks, making them suitable for mobile apps that need to handle multiple background operations, downloads, notifications, etc.

Conclusion:
Using a heap to implement a priority-based task scheduler in a mobile app allows for efficient management of dynamic tasks by always executing tasks in the correct priority order. The heap ensures that the highest-priority task can be accessed in constant time and that tasks can be dynamically inserted and removed in logarithmic time. This makes it well-suited for managing background processes, notifications, or any other tasks that require prioritization in mobile applications.

---

### 6. Describe how you would use graph algorithms to implement features like finding nearby users or locations.

To implement features like finding nearby users or locations in a mobile app, graph algorithms can be leveraged for efficient proximity-based searches. Let’s go through an example using the Dijkstra's Algorithm to find the nearest locations based on geographical distances.

Problem Statement
In a mobile app, we want to allow users to search for nearby locations (e.g., restaurants, stores) based on their current geographical location. We'll use Dijkstra's Algorithm to find the shortest paths from the user's location to other locations in the area.

Step-by-Step Approach
Modeling the Problem as a Graph:

Nodes: Represent locations (users, places, etc.).
Edges: Represent the distance between locations. Each edge has a weight, which is the geographical distance between two locations.
Dijkstra’s Algorithm:

Finds the shortest path from the user’s current location to all other locations.
Returns the closest locations in terms of distance.
Example Implementation in JavaScript:
We’ll represent the graph as an adjacency list, where each node (location) has a list of neighboring locations with their distances. Then, we’ll apply Dijkstra's Algorithm to find the closest locations from the user's current position.

```js
class PriorityQueue {
  constructor() {
    this.collection = [];
  }

  enqueue(element) {
    if (this.isEmpty()) {
      this.collection.push(element);
    } else {
      let added = false;
      for (let i = 0; i < this.collection.length; i++) {
        if (element[1] < this.collection[i][1]) {  // Compare based on distance
          this.collection.splice(i, 0, element);
          added = true;
          break;
        }
      }
      if (!added) {
        this.collection.push(element);
      }
    }
  }

  dequeue() {
    return this.collection.shift();  // Removes the first element
  }

  isEmpty() {
    return this.collection.length === 0;
  }
}

class Graph {
  constructor() {
    this.nodes = {};
  }

  addNode(location) {
    this.nodes[location] = [];
  }

  addEdge(location1, location2, distance) {
    this.nodes[location1].push({ node: location2, weight: distance });
    this.nodes[location2].push({ node: location1, weight: distance });  // Bidirectional graph
  }

  // Dijkstra's Algorithm to find the shortest path from the source to all other locations
  findNearestLocations(source) {
    let distances = {};
    let prev = {};
    let pq = new PriorityQueue();

    // Initialize distances and priority queue
    Object.keys(this.nodes).forEach((location) => {
      distances[location] = Infinity;
      prev[location] = null;
    });
    distances[source] = 0;
    pq.enqueue([source, 0]);

    while (!pq.isEmpty()) {
      let [currentNode, currentDistance] = pq.dequeue();

      this.nodes[currentNode].forEach(neighbor => {
        let alt = currentDistance + neighbor.weight;
        if (alt < distances[neighbor.node]) {
          distances[neighbor.node] = alt;
          prev[neighbor.node] = currentNode;
          pq.enqueue([neighbor.node, alt]);
        }
      });
    }

    return distances;  // Return distances to all locations from the source
  }
}

// Example usage:

// Create a graph representing locations and distances between them
const map = new Graph();

// Add nodes (locations)
map.addNode("User Location");
map.addNode("Restaurant A");
map.addNode("Restaurant B");
map.addNode("Store C");
map.addNode("Cafe D");

// Add edges (distances between locations)
map.addEdge("User Location", "Restaurant A", 2);
map.addEdge("User Location", "Restaurant B", 4);
map.addEdge("Restaurant A", "Cafe D", 1);
map.addEdge("Restaurant B", "Cafe D", 3);
map.addEdge("Cafe D", "Store C", 2);

// Find the nearest locations from the user's current location
const userLocation = "User Location";
const nearestLocations = map.findNearestLocations(userLocation);

// Display the nearest locations and their distances
console.log("Distances from the user's location:");
Object.keys(nearestLocations).forEach(location => {
  console.log(`${location}: ${nearestLocations[location]} units away`);
});

```
Explanation of the Code:
Graph Class:

The graph is represented as an adjacency list, where each node (location) stores its neighboring nodes and the corresponding distances.
The addNode() method adds a location as a node in the graph.
The addEdge() method adds an edge (distance) between two locations. For simplicity, we consider the graph to be bidirectional (i.e., the distance from location A to B is the same as from B to A).
Dijkstra’s Algorithm (findNearestLocations()):

We maintain a priority queue (implemented as a sorted array) to process locations in order of their distance from the source (the user's current location).
For each location, we calculate the distance to all of its neighbors and update their distance if we find a shorter path.
The algorithm returns an object containing the shortest distance from the source to all other locations.
Priority Queue:

The priority queue helps process the nodes with the smallest known distance first.
This ensures that the algorithm always explores the most promising paths (i.e., the shortest ones) first.
Example Scenario:
Let’s say we have the following locations:

User Location (source)
Restaurant A (2 units from the user)
Restaurant B (4 units from the user)
Cafe D (connected to Restaurant A and Restaurant B)
Store C (connected to Cafe D)
When running Dijkstra's Algorithm, the app can determine which locations are closest to the user based on distance.

Distances from the user's location:
User Location: 0 units away
Restaurant A: 2 units away
Restaurant B: 4 units away
Store C: 5 units away
Cafe D: 3 units away

---





