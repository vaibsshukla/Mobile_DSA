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







