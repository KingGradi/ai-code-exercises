# FastAPI Learning Summary

## What I Did
1. **Asked AI about FastAPI basics** - Got quick overview vs Flask
2. **Built Hello World API** - Working code in minutes with AI help  
3. **Added data validation** - Used Pydantic models for input validation
4. **Created todo API** - Full CRUD operations with filtering

## Key FastAPI Features
- **Automatic docs** at `/docs` endpoint
- **Type hints** become API validation
- **Pydantic models** handle complex data validation
- **Fast performance** - faster than Flask

## Best AI Prompts That Worked
```
"Show me a working example with comments"
"How does this compare to Flask?"
"What's wrong with this error message?"
"Explain this concept in simple terms"
```

## Working Code Examples

**Basic API**:
```python
from fastapi import FastAPI
app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")  
async def get_item(item_id: int):
    return {"item_id": item_id}
```

**With Validation**:
```python
from pydantic import BaseModel, Field

class Item(BaseModel):
    name: str = Field(..., min_length=1)
    price: float = Field(..., gt=0)

@app.post("/items/")
async def create_item(item: Item):
    return item
```

**Todo API**:
```python
from datetime import datetime
from enum import Enum

class TodoStatus(str, Enum):
    PENDING = "pending" 
    COMPLETED = "completed"

@app.post("/todos/")
async def create_todo(todo: TodoCreate):
    # Creates todo with validation
    
@app.get("/todos/")  
async def list_todos(status: Optional[TodoStatus] = None):
    # Lists todos, filtered by status
```

## Run Commands
```bash
pip install fastapi uvicorn
uvicorn main:app --reload
```

## Key Learnings
- AI gets you working code fast
- Always test AI-generated code
- Compare new tech to what you know
- Build real projects to learn properly
- Use AI to debug errors and explain concepts

**Bottom Line**: AI made learning FastAPI 10x faster by providing working examples and explaining concepts clearly.