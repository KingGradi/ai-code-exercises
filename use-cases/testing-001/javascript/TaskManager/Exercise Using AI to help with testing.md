# Using AI to Help with Testing Exercise

I chose the JavaScript TaskManager implementation to work with.

## Part 1: Understanding What to Test

### Exercise 1.1: Behavior Analysis

**What I Think the calculateTaskScore Function Does:**
- Takes a task and calculates how important it is
- Looks at priority, due date, and how old the task is
- Returns a number score (higher = more important)

**Behaviors I Identified:**
1. Priority affects the score
2. Due date affects the score
3. Days since update affects the score
4. All these combine into a final score

**Edge Cases I Thought Of:**
- Tasks with no due date
- Very old tasks
- Tasks with invalid priority
- Tasks with future dates

**Additional Edge Cases AI Helped Me Find:**
- Tasks with past due dates
- Tasks created today vs old tasks
- Priority values outside normal range (0, negative numbers)
- Missing or undefined task properties

**Test Cases I Should Write:**
1. **Basic scoring test** - Normal task with all properties
2. **No due date test** - Task without due date
3. **Past due task** - Task that's overdue
4. **New vs old task** - Compare tasks with different update dates
5. **Invalid priority test** - Task with weird priority value

### Exercise 1.2: Test Planning

**My Testing Plan:**

**High Priority Tests:**
- calculateTaskScore with valid inputs
- sortTasksByImportance with mixed tasks
- getTopPriorityTasks returns correct number

**Medium Priority Tests:**
- Edge cases for each function
- Integration test for full workflow
- Error handling for bad inputs

**Low Priority Tests:**
- Performance with large datasets
- Boundary value testing

## Part 2: Improving a Single Test

### Exercise 2.1: My First Test (Basic Version)

```javascript
test('calculateTaskScore returns a number', () => {
  const task = {
    priority: 'HIGH',
    dueDate: '2024-01-15',
    updatedAt: '2024-01-01'
  };
  
  const score = calculateTaskScore(task);
  expect(typeof score).toBe('number');
});
```

**AI Questions Made Me Think About:**
- What specific behavior am I testing?
- Am I testing the function or just checking types?
- What should the actual score be?

**Improved Test:**

```javascript
test('calculateTaskScore gives high priority tasks higher scores', () => {
  const highTask = {
    priority: 'HIGH',
    dueDate: '2024-01-15',
    updatedAt: '2024-01-01'
  };
  
  const lowTask = {
    priority: 'LOW',
    dueDate: '2024-01-15',
    updatedAt: '2024-01-01'
  };
  
  const highScore = calculateTaskScore(highTask);
  const lowScore = calculateTaskScore(lowTask);
  
  expect(highScore).toBeGreaterThan(lowScore);
});
```

### Exercise 2.2: Learning From Examples

**My Test for Due Date Calculation:**

```javascript
test('tasks due soon get higher scores', () => {
  const taskDueTomorrow = {
    priority: 'MEDIUM',
    dueDate: new Date(Date.now() + 86400000), // Tomorrow
    updatedAt: new Date()
  };
  
  const taskDueNextWeek = {
    priority: 'MEDIUM',
    dueDate: new Date(Date.now() + 604800000), // Next week
    updatedAt: new Date()
  };
  
  const tomorrowScore = calculateTaskScore(taskDueTomorrow);
  const nextWeekScore = calculateTaskScore(taskDueNextWeek);
  
  expect(tomorrowScore).toBeGreaterThan(nextWeekScore);
});
```

## Part 3: Test-Driven Development Practice

### Exercise 3.1: TDD for New Feature (User Assignment Boost)

**Step 1: Write Failing Test**
```javascript
test('tasks assigned to current user get +12 score boost', () => {
  const userTask = {
    priority: 'MEDIUM',
    assignedTo: 'current-user',
    updatedAt: new Date()
  };
  
  const otherTask = {
    priority: 'MEDIUM',
    assignedTo: 'other-user',
    updatedAt: new Date()
  };
  
  const userScore = calculateTaskScore(userTask, 'current-user');
  const otherScore = calculateTaskScore(otherTask, 'current-user');
  
  expect(userScore - otherScore).toBe(12);
});
```

**Step 2: Minimal Code to Pass**
```javascript
function calculateTaskScore(task, currentUser = null) {
  let score = 0;
  
  // Existing priority logic...
  // Existing due date logic...
  // Existing update date logic...
  
  // New feature
  if (currentUser && task.assignedTo === currentUser) {
    score += 12;
  }
  
  return score;
}
```

### Exercise 3.2: TDD for Bug Fix

**Bug Test:**
```javascript
test('days since update calculates correctly', () => {
  const threeDaysAgo = new Date();
  threeDaysAgo.setDate(threeDaysAgo.getDate() - 3);
  
  const task = {
    priority: 'MEDIUM',
    updatedAt: threeDaysAgo
  };
  
  // Mock current date
  const originalDate = Date.now;
  Date.now = () => new Date().getTime();
  
  const score = calculateTaskScore(task);
  
  // Should include points for being 3 days old
  // (Exact assertion depends on scoring formula)
  expect(score).toBeGreaterThan(0);
  
  Date.now = originalDate;
});
```

## Part 4: Integration Testing

### Exercise 4.1: Full Workflow Test

```javascript
test('complete task prioritization workflow', () => {
  const tasks = [
    {
      id: 1,
      priority: 'HIGH',
      dueDate: new Date(Date.now() + 86400000),
      updatedAt: new Date()
    },
    {
      id: 2,
      priority: 'LOW',
      dueDate: new Date(Date.now() + 604800000),
      updatedAt: new Date()
    },
    {
      id: 3,
      priority: 'URGENT',
      updatedAt: new Date()
    }
  ];
  
  const sortedTasks = sortTasksByImportance(tasks);
  const topTasks = getTopPriorityTasks(sortedTasks, 2);
  
  // Urgent task should be first
  expect(topTasks[0].id).toBe(3);
  // High priority task should be second
  expect(topTasks[1].id).toBe(1);
  // Should only return 2 tasks
  expect(topTasks.length).toBe(2);
});
```

## What I Learned

**About Testing:**
- Good tests check behavior, not implementation
- Edge cases are super important
- One good test is better than many bad tests
- Tests should be easy to understand

**About Using AI:**
- AI asking me questions helped me think better
- Better to improve my own tests than get AI to write them
- AI helped me find edge cases I missed
- Talking through the problem helped me understand it

**About TDD:**
- Writing tests first makes you think about what the code should do
- Small steps are better than big jumps
- Tests help you know when you're done
- Refactoring is easier when you have tests

**Key Takeaway:**
AI is great for helping me think about testing, but I need to write the tests myself to really learn. Asking "what should I test?" is more helpful than asking to write me tests.