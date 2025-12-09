# Exercise: Learning a New Programming Language with AI

## Learning Goals
- Use AI to learn new programming languages step by step
- Ask better questions to understand language concepts
- Build projects while learning new languages

## My Learning Strategy - 4 Steps
1. Ask "Why does this language exist?"
2. Break concepts into small pieces  
3. Practice with guided examples
4. Check my understanding

## Part 1: Create My Learning Journey Plan

**My Goal**: Learn Python for data analysis (coming from JavaScript)

**AI Prompt**:
```
I'm proficient in JavaScript and want to learn Python for data analysis.
Could you create a structured learning plan with phases, steps, and practice projects?
```

**My Learning Plan**:

### Phase 1: Python Basics (Week 1-2)
1. Python syntax vs JavaScript syntax
2. Variables and data types (no var/let/const!)
3. Functions and how they're different
4. Lists vs arrays, dictionaries vs objects
5. **Practice**: Convert JavaScript function to Python

### Phase 2: Data Tools (Week 3-4)
1. List comprehensions (Python's special power!)
2. Working with files and CSV data
3. Basic error handling
4. Using pandas library
5. **Practice**: Build a data processor

## Part 2: Four-Step Learning Strategy

**Topic**: Python Functions (vs JavaScript)

### Step 1: Conceptual Understanding
**AI Prompt**:
```
What are key differences between Python and JavaScript functions?
What mental models should I adjust coming from JavaScript?
```

**Key Differences**:
- No `function` keyword needed
- Indentation instead of braces
- Can return multiple values
- `*args` and `**kwargs` for flexible parameters

### Step 2: Guided Implementation
**My Practice Code**:
```python
def calculate_stats(numbers):
    """Calculate basic statistics"""
    if not numbers:
        return None, None, None
    
    total = sum(numbers)  # Built-in sum
    average = total / len(numbers)
    maximum = max(numbers)  # Built-in max
    
    return total, average, maximum  # Multiple returns

# Usage
data = [1, 2, 3, 4, 5]
total, avg, max_val = calculate_stats(data)
print(f"Total: {total}, Average: {avg}, Max: {max_val}")
```

### Step 3: Understanding Verification
**My Test Code**:
```python
def process_tasks(tasks):
    """Process task list and return summary"""
    if not tasks:
        return {"count": 0, "completed": 0, "rate": 0}
    
    total = len(tasks)
    completed = sum(1 for task in tasks if task.get("completed", False))
    rate = (completed / total) * 100
    
    return {
        "count": total,
        "completed": completed,
        "rate": round(rate, 1)
    }
```

**AI Feedback**:
- Good use of generator expression
- Nice error handling
- Recommended learning type hints next

## Part 3: Advanced Techniques

### Using Context Effectively
**Prompt**:
```
I know JavaScript array methods. How do Python list comprehensions compare?
```

**What I Learned**:
```python
# JavaScript: numbers.filter(n => n > 5).map(n => n * 2)
# Python: [n * 2 for n in numbers if n > 5]

numbers = [1, 3, 6, 8, 2, 9]
result = [n * 2 for n in numbers if n > 5]  # [12, 16, 18]
```

### Learning Through Teaching
**My Explanation**:
```
Python dictionaries are like JavaScript objects but use square brackets 
to access values and have methods like .get() for safe access.
```

**AI Correction**: 
- Correct about key-value pairs and .get()
- Missing: Python dict keys must be immutable
- Should learn dict comprehensions too

## Part 4: Mini-Project - Task Analyzer

**Goal**: Read CSV file and show task statistics

**My Implementation**:
```python
import csv
from collections import Counter

def read_tasks_from_csv(filename):
    """Read tasks from CSV file"""
    tasks = []
    try:
        with open(filename, 'r') as file:
            reader = csv.DictReader(file)
            for row in reader:
                row['completed'] = row['completed'].lower() == 'true'
                tasks.append(row)
        return tasks
    except FileNotFoundError:
        print(f"File {filename} not found!")
        return []

def analyze_tasks(tasks):
    """Analyze task list and return statistics"""
    if not tasks:
        return None
    
    total = len(tasks)
    completed = sum(1 for task in tasks if task['completed'])
    completion_rate = (completed / total) * 100
    priorities = Counter(task['priority'] for task in tasks)
    
    return {
        'total': total,
        'completed': completed,
        'completion_rate': round(completion_rate, 1),
        'priorities': dict(priorities)
    }

def main():
    """Main function"""
    print("Task Analyzer")
    tasks = read_tasks_from_csv('tasks.csv')
    stats = analyze_tasks(tasks)
    
    if stats:
        print(f"Total: {stats['total']}")
        print(f"Completed: {stats['completed']}")
        print(f"Rate: {stats['completion_rate']}%")
        print("Priorities:", stats['priorities'])

if __name__ == "__main__":
    main()
```

**Sample CSV**:
```csv
title,priority,completed
Buy groceries,high,true
Write report,medium,false
Call mom,low,true
```

## Key Learnings

### What Surprised Me:
1. **Indentation matters** - No braces, spacing is syntax
2. **Built-in functions are powerful** - `sum()`, `max()`, `len()`
3. **Multiple return values** - Functions can return several things
4. **List comprehensions** - More concise than array methods

### What Helped Most:
1. **Comparing to JavaScript** - Made concepts easier
2. **Step-by-step approach** - Didn't rush
3. **Building real project** - Made learning stick
4. **AI feedback** - Caught my mistakes

### Next Steps:
1. Learn pandas for data analysis
2. Practice object-oriented Python
3. Build bigger projects
4. Study open source Python code

## My Learning Template

For future languages:
```

Phase 1: Compare and contrast languages
Phase 2: Learn syntax through guided examples  
Phase 3: Build small project combining concepts
Phase 4: Get feedback and plan next steps
