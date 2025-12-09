Algorithm Deconstruction Challenge: Task Priority Sorting
I chose the Task Priority Sorting Algorithm (JavaScript version) because it connects directly to our task manager codebase. It has three main functions: calculateTaskScore, sortTasksByImportance, and getTopPriorityTasks.

Initial Understanding Before AI Help
I first thought this was simple priority sorting (LOW, MEDIUM, HIGH, URGENT). Reading closer showed each task gets a numeric score from multiple factors, then sorts by highest score. I was confused about weightings and why completed tasks get negative scores.

Breaking Down calculateTaskScore Function
AI prompts helped me understand step by step:

Base score calculation:
priorityWeights[task.priority] * 10
LOW=1 (10 points), MEDIUM=2 (20 points), HIGH=3 (30 points), URGENT=4 (40 points)

Due date bonuses (higher for sooner):
Overdue: +30
Today: +20
Next 2 days: +15
Next week: +10

Status penalties:
DONE: -50 (pushes to bottom)
REVIEW: -15 (slightly lower)

Bonus factors:
Special tags (blocker, critical, urgent): +8
Updated today: +5

Task Input
    ↓
Priority * 10
    ↓
+ Due Date Bonus  
    ↓
- Status Penalty
    ↓
+ Tag/Recent Bonus
    ↓
Final Score Number
Other Functions Explained
sortTasksByImportance(tasks):
Creates array copy, sorts highest score first

getTopPriorityTasks(tasks, limit=5):
Sorts all tasks, returns top N only

Visual Score Examples

Fix urgent bug (URGENT + overdue + blocker)  
40 + 30 + 8 = 78 points

Review code (HIGH + REVIEW)  
30 - 15 = 15 points

Email client (LOW + next week)  
10 + 10 = 20 points

Sort order: Fix bug > Email client > Review code
Key Insights Learned
Surprised that overdue LOW priority beats URGENT far future
Negative scoring for DONE/REVIEW elegantly deprioritizes finished work
Weights seem experience-tuned (URGENT base 40, overdue boost 30)

Reflection Answers
AI explanation changed simple priority sort to weighted multi-factor system
Day calculations (milliseconds to days) still feel magical

To junior dev: Every task gets game score. Priority = base points, urgency = bonuses, finished = penalties. Sort highest score first

Tested understanding: URGENT tomorrow (55) vs HIGH overdue (60) - overdue wins

Improvements:
Configurable weights in JSON
Project deadlines boost
Cache unchanged scores
Add estimated task duration factor