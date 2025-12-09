# Exercise: Documentation Navigation for FastAPI

## Part 1: Documentation Summarization

**AI Prompts I Used**:
```
What would be an effective reading order for FastAPI docs for someone new?
What are the 5 most important sections for building a REST API quickly?
Summarize FastAPI's dependency injection docs with practical applications.
```

**My Learning Roadmap**:
1. **First Steps** - Basic API setup and running
2. **Path Parameters** - URL variables and validation  
3. **Request Body** - Pydantic models for POST/PUT data
4. **Query Parameters** - Optional parameters and filtering
5. **Dependencies** - Code sharing and authentication
6. **Security** - JWT tokens and OAuth2
7. **Background Tasks** - Async operations after response

**Most Important Sections for Quick API Build**:
1. Tutorial basics (getting started)
2. Request body validation (Pydantic models)
3. Dependency injection (authentication/database)
4. Path operations (CRUD endpoints) 
5. Error handling (HTTP exceptions)

## Part 2: Documentation Deep Dive

**Topic I Chose**: Dependency Injection

**AI Prompt**:
```
I'm confused by the Depends function in FastAPI. Can you explain its purpose 
and when I should use it versus when I shouldn't?
```

**Key Understanding**:
- **What it does**: Shares code between endpoints (like middleware but more flexible)
- **When to use**: Authentication, database connections, common validation
- **When not to use**: Simple operations that don't repeat across endpoints

**Core Pattern**:
```python
# 1. Create dependency function
def get_db():
    return database_connection

# 2. Inject into routes  
@app.get("/users/")
def get_users(db = Depends(get_db)):
    return db.query(User).all()
```

## Part 3: Concept to Code Translation

**Key FastAPI Patterns from Docs**:

**1. Dependency Injection**:
```python
from fastapi import Depends, HTTPException

def verify_token(token: str):
    if token != "valid-token":
        raise HTTPException(401, "Invalid token")
    return {"user": "john"}

@app.get("/protected")
def protected_route(user = Depends(verify_token)):
    return {"message": f"Hello {user['user']}"}
```

**2. Request Validation**:
```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    email: str
    age: int

@app.post("/users")
def create_user(user: User):
    return {"created": user.name}
```

**3. Background Tasks**:
```python
from fastapi import BackgroundTasks

def send_email(email: str):
    print(f"Sending email to {email}")

@app.post("/register")
def register(email: str, tasks: BackgroundTasks):
    tasks.add_task(send_email, email)
    return {"message": "User registered, email sending"}
```

## Part 4: Mini Blog API Challenge

**Features Needed**: User auth, CRUD posts, comments, search

**Documentation Sections I Used**:
- Security tutorial (JWT authentication)
- SQL databases (user/post models)
- Background tasks (email notifications)
- Query parameters (search/filtering)

**My Simple Blog API**:
```python
from fastapi import FastAPI, Depends, HTTPException
from pydantic import BaseModel
from typing import List, Optional

app = FastAPI(title="Simple Blog API")

# Models
class User(BaseModel):
    username: str
    email: str

class PostCreate(BaseModel):
    title: str
    content: str

class Post(PostCreate):
    id: int
    author: str

# Mock database
users_db = {"john": {"email": "john@example.com"}}
posts_db = []
post_counter = 0

# Dependencies
def get_current_user(username: str):
    if username not in users_db:
        raise HTTPException(401, "User not found")
    return {"username": username}

# Authentication
@app.post("/register")
def register(user: User):
    if user.username in users_db:
        raise HTTPException(400, "User already exists")
    users_db[user.username] = {"email": user.email}
    return {"message": "User registered"}

# Blog posts CRUD
@app.post("/posts", response_model=Post)
def create_post(post: PostCreate, username: str, user = Depends(get_current_user)):
    global post_counter
    post_counter += 1
    new_post = Post(id=post_counter, author=username, **post.dict())
    posts_db.append(new_post.dict())
    return new_post

@app.get("/posts", response_model=List[Post])
def list_posts(search: Optional[str] = None):
    posts = posts_db
    if search:
        posts = [p for p in posts if search.lower() in p["title"].lower()]
    return posts

@app.get("/posts/{post_id}", response_model=Post)
def get_post(post_id: int):
    post = next((p for p in posts_db if p["id"] == post_id), None)
    if not post:
        raise HTTPException(404, "Post not found")
    return post
```

## Key Learnings

**Most Helpful Documentation Sections**:
1. **Tutorial** - Clear step-by-step examples
2. **Advanced Features** - Real-world patterns like dependencies
3. **Security** - Practical authentication examples
4. **SQLAlchemy integration** - Database patterns

**Best AI Documentation Prompts**:
- "Summarize this section focusing on practical use"
- "Show me code examples from this documentation concept"
- "What's the recommended approach in the docs for [specific task]?"
- "Which documentation sections do I need for [feature]?"

**What Made Documentation Easier**:
- AI helped prioritize which sections to read first
- Got practical examples instead of just theory
- Could ask for clarification on confusing parts
- AI translated concepts into working code

**Bottom Line**: FastAPI docs are good, but AI helps navigate them faster and turns concepts into working code quickly.