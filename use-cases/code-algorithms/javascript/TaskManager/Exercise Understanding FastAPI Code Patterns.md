# Exercise: Understanding FastAPI Code Patterns

## Part 1: Analyzing Complex Code

**AI Prompts I Used**:
```
Can you explain the Repository pattern in this FastAPI code? Why structure code this way?
What is Generic[T] for and how does it make code more maintainable?
How does dependency injection work in this app? Explain each layer.
```

**Key Patterns I Found**:

**1. Repository Pattern**:
```python
class Repository(Generic[T]):
    def __init__(self, model: Type[T]):
        self.model = model
    
    async def get_by_id(self, db: AsyncSession, id: int) -> Optional[T]:
        # Database queries here
```
- **Why use it**: Separates database logic from business logic
- **Benefit**: Can easily swap databases or add caching

**2. Generic Repository**:
- `Generic[T]` means one repository class works for any database model
- Instead of writing separate repositories for User, Post, Comment, etc.
- One generic class handles all CRUD operations

**3. Dependency Injection Layers**:
```python
# Layer 1: Database connection
async def get_db() -> AsyncSession:
    # Provides database session

# Layer 2: Authentication  
async def get_current_user(token: str = Depends(oauth2_scheme), db = Depends(get_db)):
    # Uses db dependency to verify user

# Layer 3: Authorization
@requires_role("admin")
def endpoint(current_user = Depends(get_current_user)):
    # Uses user dependency to check permissions
```

## Part 2: Tracing Execution Flow

**Request to `/admin/users/` - What Happens**:

1. **Middleware runs** - TimingMiddleware starts timing
2. **Auth dependency** - `get_current_user()` runs:
   - Gets JWT token from header
   - Decodes token to get username
   - Queries database for user
3. **Role check** - `@requires_role("admin")` decorator:
   - Checks if user is superuser
   - Throws 403 error if not admin
4. **Main function** - `list_users()` runs:
   - Creates UserRepository
   - Queries database for users list
5. **Response** - Returns JSON list of users
6. **Middleware finishes** - Adds timing header to response

**Authentication Flow**:
```
Token → Decode → Username → Database Query → User Object → Permission Check
```

## Part 3: Simplifying Complex Concepts

**Complex Concept Translations**:

**1. `@asynccontextmanager` and `lifespan`**:
```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    print("App starting up")  # Run once when app starts
    yield                     # App runs here
    print("App shutting down") # Run once when app stops
```
- **Simple explanation**: Like `__init__` and `__del__` but for the whole app
- **Why useful**: Setup databases, cleanup resources

**2. TimingMiddleware**:
```python
async def __call__(self, request: Request, call_next: Callable):
    start = datetime.now()
    response = await call_next(request)  # Process the request
    time_taken = datetime.now() - start
    response.headers["X-Process-Time"] = str(time_taken)
    return response
```
- **Simple explanation**: Runs before and after every request
- **Purpose**: Measures how long each request takes

**3. JWT Authentication**:
1. User logs in with username/password
2. Server creates JWT token with user info
3. User sends token with each request
4. Server decodes token to know who the user is

## Part 4: Building - Adding Logging Feature

**My Implementation**:
```python
# 1. Create logging model (following existing pattern)
class UserAction(Base):
    __tablename__ = "user_actions"
    id: int
    user_id: int
    action: str
    timestamp: datetime

# 2. Create logging repository (following Repository pattern)
class ActionRepository(Repository[UserAction]):
    async def log_action(self, db: AsyncSession, user_id: int, action: str):
        log_entry = UserAction(
            user_id=user_id,
            action=action,
            timestamp=datetime.utcnow()
        )
        db.add(log_entry)
        await db.commit()

# 3. Create logging service (following Service pattern)  
class LoggingService:
    def __init__(self, repository: ActionRepository):
        self.repository = repository
    
    async def log_user_action(self, db: AsyncSession, user_id: int, action: str):
        await self.repository.log_action(db, user_id, action)

# 4. Add logging to existing endpoints
@app.post("/token")
async def login(username: str, password: str, db: AsyncSession = Depends(get_db)):
    user = await authenticate_user(username, password)  # existing code
    if user:
        # Add logging
        logger = LoggingService(ActionRepository(UserAction))
        await logger.log_user_action(db, user.id, "login")
    return {"access_token": token}

@app.get("/admin/users/")  
async def list_users(current_user = Depends(get_current_user), db = Depends(get_db)):
    # Add logging
    logger = LoggingService(ActionRepository(UserAction))
    await logger.log_user_action(db, current_user.id, "admin_list_users")
    
    # existing code
    return users_list
```

## Key Learnings

**Most Useful Patterns**:
1. **Repository Pattern** - Makes database code reusable and testable
2. **Dependency Injection** - Shares code between endpoints cleanly
3. **Service Layer** - Keeps business logic separate from API code
4. **Generic Types** - One class works for many different models

**How Dependency Injection Works**:
- Functions declare what they need as parameters
- FastAPI automatically provides those things
- Creates a chain: database → user → permissions → endpoint

**Understanding Architecture Through Implementation**:
- Following existing patterns made adding features easy
- Each layer has a clear responsibility
- Dependencies flow in one direction (good design)

**How to Explain to Colleagues**:
- **Repository**: "Like a filing cabinet for each type of data"
- **Dependencies**: "FastAPI automatically gives functions what they need"
- **Service Layer**: "Business rules live here, separate from web stuff"

**Bottom Line**: This code uses layers and patterns to keep different concerns separate, making it easier to test, modify, and understand each piece.