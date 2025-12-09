# Exercise: Applying AI to deepen programming language understanding

## Learning Goals
- Write better JavaScript code using modern features
- Find and fix code problems with AI help  
- Learn advanced JavaScript features I didn't know before

## Activity 1: Making My JavaScript More Professional

**My Original Code**:
```javascript
function filterTasksByStatus(tasks, status) {
  var result = [];
  for (var i = 0; i < tasks.length; i++) {
    if (tasks[i].status === status) {
      result.push(tasks[i]);
    }
  }
  return result;
}
```

**AI Prompt**:
```
I'm learning to write more idiomatic JavaScript. Here's my current code:
[paste code]
Could you suggest ways to make this more idiomatic and explain why?
```

**AI's Improved Version**:
```javascript
const filterTasksByStatus = (tasks, status) => 
  tasks.filter(task => task.status === status);
```

### My 3 Key Learnings:
1. **Array methods are cleaner** - `filter()` and `map()` replace most for loops
2. **Use `const` instead of `var`** - Prevents bugs and is safer
3. **Arrow functions are clearer** - Less code, easier to read

## Activity 2: Finding Problems in My Old Code

**My Old Code**:
```javascript
function createNewTask(t, d, p, dd, tgs) {
  if (!t) {
    console.log("Error: no title");
    return null;
  }
  if (d == undefined) d = "";
  if (p == undefined) p = 2;
  
  var newTask = {
    id: Math.random().toString(),
    title: t,
    description: d,
    priority: p,
    status: "todo"
  };
  return newTask;
}
```

**AI's Improved Version**:
```javascript
function createNewTask(title, description = '', priority = 2, dueDate = null, tags = []) {
  if (!title || typeof title !== 'string') {
    throw new Error('Task title must be a non-empty string');
  }
  
  const newTask = {
    id: `task_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`,
    title: title.trim(),
    description,
    priority,
    status: 'todo',
    createdAt: new Date(),
    dueDate,
    tags
  };
  
  return newTask;
}
```

### My 3 Key Learnings:
1. **Good variable names save time** - `title` is better than `t`
2. **Default parameters are cleaner** - No need for undefined checks
3. **Throw errors, don't return null** - Makes debugging much easier

## Activity 3: Learning JavaScript Destructuring

**AI Prompt**:
```
I want to improve my understanding of destructuring in JavaScript.
1. Explain with simple examples
2. Show 3 practical use cases  
3. What mistakes should I avoid?
```

**Simple Examples**:
```javascript
// Object destructuring
const task = { title: 'Buy milk', priority: 3 };
const { title, priority } = task;

// Function parameters
function updateTask({ title, priority, status }) {
  // Use directly without task.title, task.priority
}

// Array destructuring  
const [firstTask, ...otherTasks] = tasks;
```

### My 3 Key Learnings:
1. **Destructuring makes code cleaner** - Pull out properties once, use them everywhere
2. **Great for function parameters** - Shows exactly what the function needs
3. **Works with arrays too** - Easy way to split arrays into parts

## My Overall Reflection

**What Changed**:
- I use array methods instead of for loops
- I write descriptive variable names
- I use destructuring for cleaner code
- I throw proper errors

**How I'll Keep Learning**:
1. Ask AI to review my code before committing
2. Practice one new JavaScript feature each week
3. Read other people's code to see patterns

The biggest thing I learned: Modern JavaScript has amazing features that make code much easier to read and write. AI is a great teacher for showing me these better ways to code.
