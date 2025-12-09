# Function Decomposition Challenge Exercise

## Selected Function: JavaScript Data Validation Function

I picked the JavaScript validation function because it has many different tasks mixed together and is hard to understand.

## What I Found Wrong

### Big Problems:
- One huge function doing too many different things
- Hard to test individual validation rules
- Repeated patterns for checking different fields
- Mixed logic for registration vs profile updates
- Hard to add new validation rules

### Different Jobs in the Function:
1. Check required fields
2. Validate username format and uniqueness
3. Validate password strength and matching
4. Validate email format and uniqueness
5. Validate date of birth and age
6. Validate address format and fields
7. Validate phone number format
8. Run custom validations

## My Decomposition Plan

### Step 1: Extract Field Validators
Create small functions for each type of validation:

```javascript
function validateUsername(username, options = {}) {
  const errors = [];
  
  if (!username || username.trim() === '') {
    errors.push('Username is required for registration');
    return errors;
  }
  
  if (username.length < 3) {
    errors.push('Username must be at least 3 characters long');
  } else if (username.length > 20) {
    errors.push('Username must be at most 20 characters long');
  } else if (!/^[a-zA-Z0-9_]+$/.test(username)) {
    errors.push('Username can only contain letters, numbers, and underscores');
  } else if (options.checkExisting && options.checkExisting.usernameExists(username)) {
    errors.push('Username is already taken');
  }
  
  return errors;
}

function validatePassword(password, confirmPassword) {
  const errors = [];
  
  if (!password) {
    errors.push('Password is required');
    return errors;
  }
  
  if (password.length < 8) {
    errors.push('Password must be at least 8 characters long');
  } else if (!/[A-Z]/.test(password)) {
    errors.push('Password must contain at least one uppercase letter');
  } else if (!/[a-z]/.test(password)) {
    errors.push('Password must contain at least one lowercase letter');
  } else if (!/[0-9]/.test(password)) {
    errors.push('Password must contain at least one number');
  } else if (!/[^A-Za-z0-9]/.test(password)) {
    errors.push('Password must contain at least one special character');
  }
  
  if (confirmPassword !== password) {
    errors.push('Password and confirmation do not match');
  }
  
  return errors;
}

function validateEmail(email, isRequired, options = {}) {
  const errors = [];
  
  if (!email || email.trim() === '') {
    if (isRequired) {
      errors.push('Email is required');
    }
    return errors;
  }
  
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(email)) {
    errors.push('Email format is invalid');
  } else if (options.checkExisting && options.checkExisting.emailExists(email)) {
    errors.push('Email is already registered');
  }
  
  return errors;
}

function validateDateOfBirth(dateOfBirth) {
  const errors = [];
  
  if (!dateOfBirth || dateOfBirth === '') {
    return errors; // Optional field
  }
  
  const dobDate = new Date(dateOfBirth);
  
  if (isNaN(dobDate.getTime())) {
    errors.push('Date of birth is not a valid date');
    return errors;
  }
  
  const now = new Date();
  const minAgeDate = new Date(now.getFullYear() - 13, now.getMonth(), now.getDate());
  const maxAgeDate = new Date(now.getFullYear() - 120, now.getMonth(), now.getDate());
  
  if (dobDate > now) {
    errors.push('Date of birth cannot be in the future');
  } else if (dobDate > minAgeDate) {
    errors.push('You must be at least 13 years old');
  } else if (dobDate < maxAgeDate) {
    errors.push('Invalid date of birth (age > 120 years)');
  }
  
  return errors;
}
```

### Step 2: Extract Required Field Checker
```javascript
function checkRequiredFields(userData, fieldList, operation) {
  const errors = [];
  
  for (const field of fieldList) {
    if (!userData[field] || userData[field].trim() === '') {
      errors.push(`${field} is required for ${operation}`);
    }
  }
  
  return errors;
}
```

### Step 3: Main Function Becomes Simple
```javascript
function validateUserData(userData, options = {}) {
  const errors = [];
  const isRegistration = options.isRegistration || false;
  
  // Check required fields
  if (isRegistration) {
    const requiredFields = ['username', 'email', 'password', 'confirmPassword'];
    errors.push(...checkRequiredFields(userData, requiredFields, 'registration'));
  } else {
    const profileFields = ['firstName', 'lastName', 'dateOfBirth', 'address'];
    for (const field of profileFields) {
      if (userData[field] !== undefined && userData[field].trim() === '') {
        errors.push(`${field} cannot be empty if provided`);
      }
    }
  }
  
  // Validate individual fields
  if (isRegistration && userData.username) {
    errors.push(...validateUsername(userData.username, options));
  }
  
  if (isRegistration && userData.password) {
    errors.push(...validatePassword(userData.password, userData.confirmPassword));
  }
  
  if (userData.email !== undefined) {
    errors.push(...validateEmail(userData.email, isRegistration, options));
  }
  
  if (userData.dateOfBirth !== undefined) {
    errors.push(...validateDateOfBirth(userData.dateOfBirth));
  }
  
  // Add other validations...
  
  // Custom validations
  if (options.customValidations) {
    for (const validation of options.customValidations) {
      const field = validation.field;
      if (userData[field] !== undefined) {
        const valid = validation.validator(userData[field], userData);
        if (!valid) {
          errors.push(validation.message || `Invalid value for ${field}`);
        }
      }
    }
  }
  
  return errors;
}
```

## Benefits I Got

### Better Readability:
- Each function has one clear job
- Main function shows the big picture
- Easy to see what validations happen

### Easier Testing:
- Can test each validation rule separately
- Don't need complex setup to test one rule
- Clearer test names like "test username validation"

### Easier to Change:
- Add new validation rules without touching existing code
- Change one validation without breaking others
- Can reuse validators in different places

### Less Bugs:
- Smaller functions are easier to get right
- Changes don't accidentally break other validations
- Each function can be tested thoroughly

## Reflection

### How It Improved Things:
The code went from one scary 200-line function to many small, simple functions. Now I can understand what each piece does and test them one at a time.

### Hardest Part:
Figuring out which pieces belonged together was tricky. Some validations shared logic, so I had to decide whether to combine them or keep them separate.

### Most Reusable Function:
The validateEmail function would be super useful everywhere. Almost every app needs email validation, so this could go in a shared utilities file.

### Key Learning:
Big functions are scary because you can't understand them all at once. Breaking them down makes everything clearer and easier to work with. Each small function should do one thing really well.