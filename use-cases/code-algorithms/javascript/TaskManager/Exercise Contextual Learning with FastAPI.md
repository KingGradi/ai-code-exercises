# Exercise: Contextual Learning with FastAPI

## Part 1: Framework Comparison

**AI Prompts I Used**:
```
Compare FastAPI and Flask: what concepts are similar and what's different?
If I'm used to Flask's Blueprint system, what's the equivalent in FastAPI?
How does FastAPI's dependency injection compare to Django's middleware?
```

**My Translation Table**:
| Flask/Django     | FastAPI                  | Key Difference |
|------------------|--------------------------|----------------|
| `@app.route()`   | `@app.get()/@app.post()` | HTTP method explicit |
| Blueprint        | APIRouter   | Same concept, different name |
| Form validation | Pydantic models | Type hints = validation |
| Middleware | Dependencies | More flexible, can inject anywhere |
| `request.json` | Function parameter | Automatic parsing with types |

## Part 2: Understanding Design Choices

**Why FastAPI Works This Way**:
- **Pydantic for validation**: Uses Python types, no need to learn new syntax
- **Automatic docs**: Type hints generate interactive API documentation
- **Async-first**: Built for modern Python, handles many requests efficiently  
- **Type hints everywhere**: Catches errors early, makes code self-documenting

**Design Philosophy**: "Use Python's type system to make APIs safer and faster"

## Part 3: JWT Authentication Implementation

**Key Differences from Other Frameworks**:

**Flask vs FastAPI Auth**:
```python
# Flask way
@app.route('/protected')
@jwt_required
def protected():
    return get_jwt_identity()

# FastAPI way  
@app.get("/protected")
async def protected(user: User = Depends(get_current_user)):
    return user
```

**My Simple JWT API**:
```python
from fastapi import FastAPI, Depends, HTTPException
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import jwt
from datetime import datetime, timedelta

app = FastAPI()
SECRET_KEY = "your-secret-key"
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Fake user database
users = {
    "johndoe": {"password": "secret", "email": "john@example.com"}
}

def create_token(data: dict):
    expire = datetime.utcnow() + timedelta(hours=1)
    to_encode = data.copy()
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm="HS256")

def get_current_user(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        username = payload.get("sub")
        if username not in users:
            raise HTTPException(status_code=401, detail="Invalid user")
        return {"username": username}
    except:
        raise HTTPException(status_code=401, detail="Invalid token")

@app.post("/token")
async def login(form: OAuth2PasswordRequestForm = Depends()):
    if form.username in users and users[form.username]["password"] == form.password:
        token = create_token({"sub": form.username})
        return {"access_token": token, "token_type": "bearer"}
    raise HTTPException(status_code=401, detail="Wrong credentials")

@app.get("/protected")
async def protected_route(user = Depends(get_current_user)):
    return {"message": f"Hello {user['username']}, you are authenticated!"}
```

**Install & Run**:
```bash
pip install fastapi uvicorn python-jose[cryptography]
uvicorn main:app --reload
```

**Test it**:
1. POST to `/token` with `username=johndoe&password=secret`
2. Use returned token in header: `Authorization: Bearer {token}`
3. Access `/protected` with the token

## Part 4: Mental Model Translation

**My Framework Concept Map**:

```
Django/Flask          -    FastAPI
─────────────────────────────────────
Views/Routes          -    Path Operations
Middleware            -    Dependencies  
Forms/Serializers     -    Pydantic Models
Decorators           -    Depends()
Request/Response      -    Type-hinted parameters
Manual validation     -    Automatic from types
```

## Key Learnings

**What's Better in FastAPI**:
1. **Dependencies are cleaner** - Can inject anywhere, not just middleware
2. **Type hints do the work** - No manual validation code needed
3. **Automatic docs** - API documentation generates itself
4. **Less boilerplate** - Type system handles most common tasks

**What I Had to Adjust**:
- Think in terms of dependencies, not middleware
- Use type hints for everything, not just documentation
- Embrace async/await patterns
- Trust that Pydantic will handle validation

**Bottom Line**: FastAPI feels like Flask but with Python's type system doing most of the heavy lifting for validation, documentation, and error handling.