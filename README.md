# Mobile_DSA


#### How can you use a stack to handle undo operations in a mobile application?

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

