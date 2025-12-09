# Error Diagnosis Challenge Exercise

## Selected Error Scenario: Global Variable Being Overwritten 

I selected the JavaScript global variable error because it's a tricky bug that shows how variable scope can cause unexpected problems.

## Error Analysis: TypeError - Cannot read properties of undefined

**Error Description:**
The error "Cannot read properties of undefined (reading 'map')" means the code is trying to use the map() method on something that isn't an array. It's like trying to use array methods on a single object instead of a collection.

**Root Cause:**
The problem is in this line inside the addTask function:
```javascript
let tasks = { id: Date.now(), name: taskName, completed: false };
```

This creates a new local variable called `tasks` that hides the global array. The global `tasks` array gets replaced by a single object, so when displayTasks() tries to use `tasks.map()`, it fails because objects don't have a map method.

**Solution:**
Remove the `let` keyword to modify the global array instead of creating a new variable:
```javascript
function addTask(taskName) {
  tasks.push({ id: Date.now(), name: taskName, completed: false });
  console.log("Task added:", taskName);
  displayTasks();
}
```

**Learning Points:**
- Using let or var creates a new variable, even if one with the same name already exists
- Global variables can be accidentally overwritten by local variables with the same name
- Always use array methods like push() to add items to arrays
- Be careful with variable naming to avoid hiding global variables

## Applying Debug Prompts

Using Prompt 1 (Error Message Translation), I would ask:

"I need help understanding this TypeError in my JavaScript task manager application. The error happens when I try to add a new task and then display the task list. I'm using vanilla JavaScript in the browser. Could you explain what this 'Cannot read properties of undefined' error means, identify which line is causing the problem, and suggest what I should look for in my variable declarations?"

## Reflection

**How AI explanation compares to documentation:**
AI can immediately spot the variable hiding issue and explain it in context, while MDN documentation about scope and variables requires me to connect the dots myself.

**What would be difficult to diagnose manually:**
Variable hiding is tricky because the code looks correct at first glance. The bug only shows up when the function runs, and it's not obvious that let tasks creates a new variable instead of modifying the existing one.

**Better error messages:**
I could add type checking to catch this early:
```javascript
function displayTasks() {
  if (!Array.isArray(tasks)) {
    console.error("Error: tasks is not an array!", tasks);
    return;
  }
}
```

**Understanding concepts:**
AI helped me understand that this isn't just about removing let it's about understanding JavaScript scope, variable hiding, and the difference between declaring new variables vs modifying existing ones. This teaches better practices for managing global state.