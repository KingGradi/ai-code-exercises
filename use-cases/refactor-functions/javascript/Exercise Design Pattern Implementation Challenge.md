# Design Pattern Implementation Challenge

## Selected Pattern: Strategy Pattern for Shipping Calculator

I picked the JavaScript shipping calculator because it had lots of if-else statements that made it hard to add new shipping methods.

## What Was Wrong With Original Code

### Big Problems:
- One huge function with nested if-else statements
- Hard to add new shipping methods
- Hard to test individual shipping rules
- Mixed logic for different shipping types
- Repeated country checking code

### Why Strategy Pattern Fits:
The code has different ways (strategies) to calculate shipping costs, but they're all mixed together in one function.

## My Strategy Pattern Implementation

### Step 1: Create Strategy Interface
```javascript
// Base strategy that all shipping methods follow
class ShippingStrategy {
  calculateCost(packageDetails, destinationCountry) {
    throw new Error('Must implement calculateCost method');
  }
  
  isAvailable(destinationCountry) {
    return true; // Default: available everywhere
  }
}
```

### Step 2: Create Concrete Strategies
```javascript
// Standard shipping strategy
class StandardShipping extends ShippingStrategy {
  calculateCost(packageDetails, destinationCountry) {
    const { weight, length, width, height } = packageDetails;
    let cost = 0;
    
    // Base cost by country
    switch (destinationCountry) {
      case 'USA':
        cost = weight * 2.5;
        break;
      case 'Canada':
        cost = weight * 3.5;
        break;
      case 'Mexico':
        cost = weight * 4.0;
        break;
      default:
        cost = weight * 4.5;
    }
    
    // Add dimensional weight surcharge
    if (weight < 2 && (length * width * height) > 1000) {
      cost += 5.0;
    }
    
    return cost;
  }
}

// Express shipping strategy
class ExpressShipping extends ShippingStrategy {
  calculateCost(packageDetails, destinationCountry) {
    const { weight, length, width, height } = packageDetails;
    let cost = 0;
    
    // Base cost by country
    switch (destinationCountry) {
      case 'USA':
        cost = weight * 4.5;
        break;
      case 'Canada':
        cost = weight * 5.5;
        break;
      case 'Mexico':
        cost = weight * 6.0;
        break;
      default:
        cost = weight * 7.5;
    }
    
    // Large package surcharge
    if ((length * width * height) > 5000) {
      cost += 15.0;
    }
    
    return cost;
  }
}

// Overnight shipping strategy
class OvernightShipping extends ShippingStrategy {
  calculateCost(packageDetails, destinationCountry) {
    const { weight } = packageDetails;
    
    switch (destinationCountry) {
      case 'USA':
        return weight * 9.5;
      case 'Canada':
        return weight * 12.5;
      default:
        throw new Error('Overnight shipping not available for this destination');
    }
  }
  
  isAvailable(destinationCountry) {
    return destinationCountry === 'USA' || destinationCountry === 'Canada';
  }
}
```

### Step 3: Create Context Class
```javascript
// Main shipping calculator that uses strategies
class ShippingCalculator {
  constructor() {
    this.strategies = {
      'standard': new StandardShipping(),
      'express': new ExpressShipping(),
      'overnight': new OvernightShipping()
    };
  }
  
  calculateShippingCost(packageDetails, destinationCountry, shippingMethod) {
    const strategy = this.strategies[shippingMethod];
    
    if (!strategy) {
      throw new Error(`Unknown shipping method: ${shippingMethod}`);
    }
    
    if (!strategy.isAvailable(destinationCountry)) {
      return `${shippingMethod} shipping not available for this destination`;
    }
    
    const cost = strategy.calculateCost(packageDetails, destinationCountry);
    return cost.toFixed(2);
  }
  
  // Easy to add new shipping methods
  addShippingMethod(name, strategy) {
    this.strategies[name] = strategy;
  }
}
```

### Step 4: Usage Example
```javascript
// Create calculator and test it
const calculator = new ShippingCalculator();

const package = { weight: 5, length: 10, width: 10, height: 10 };

console.log(calculator.calculateShippingCost(package, 'USA', 'standard'));  // "12.50"
console.log(calculator.calculateShippingCost(package, 'Canada', 'express')); // "27.50"

// Easy to add new shipping method
class EcoShipping extends ShippingStrategy {
  calculateCost(packageDetails, destinationCountry) {
    // Eco shipping is cheaper but slower
    return packageDetails.weight * 1.5;
  }
}

calculator.addShippingMethod('eco', new EcoShipping());
console.log(calculator.calculateShippingCost(package, 'USA', 'eco')); // "7.50"
```

## Simple Test to Verify It Works
```javascript
// Test that results match original function
function testStrategy() {
  const calculator = new ShippingCalculator();
  const package = { weight: 5, length: 10, width: 10, height: 10 };
  
  // Test cases that should match original
  const result1 = calculator.calculateShippingCost(package, 'USA', 'standard');
  const result2 = calculator.calculateShippingCost(package, 'Canada', 'express');
  
  console.log('Standard USA:', result1); // Should be "12.50"
  console.log('Express Canada:', result2); // Should be "27.50"
  
  // Test error case
  try {
    calculator.calculateShippingCost(package, 'Germany', 'overnight');
  } catch (error) {
    console.log('Overnight Germany: Not available'); // Should show error
  }
}

testStrategy();
```

## Benefits I Got From Strategy Pattern

### Easy to Add New Shipping Methods:
- Just create a new class that extends ShippingStrategy
- Add it to the calculator
- No need to modify existing code

### Easier Testing:
- Can test each shipping method separately
- Clear test names like "test standard shipping"
- Don't need complex setup for each test

### Cleaner Code:
- Each strategy is focused on one shipping method
- No more nested if-else statements
- Easy to understand what each class does

### Better Maintenance:
- Changes to one shipping method don't affect others
- Easy to find and fix bugs
- Can add features to specific methods only

## Simple Example of Adding New Method:
```javascript
// Super easy to add same-day delivery
class SameDayShipping extends ShippingStrategy {
  calculateCost(packageDetails, destinationCountry) {
    if (packageDetails.weight > 10) {
      throw new Error('Same-day shipping not available for packages over 10lbs');
    }
    return packageDetails.weight * 15.0; // Premium pricing
  }
  
  isAvailable(destinationCountry) {
    return destinationCountry === 'USA'; // Only USA for now
  }
}

calculator.addShippingMethod('sameday', new SameDayShipping());
```

## Reflection

### How Pattern Improved Maintainability:
The code went from one big messy function to small, focused classes. Now each shipping method is separate and easy to understand.

### Future Changes Made Easier:
- Adding new shipping methods is super simple
- Changing rates for one method doesn't affect others
- Can add special features to specific methods
- Easy to test individual shipping rules

### Unexpected Challenges:
- Had to think about how to structure the base class
- Needed to decide what methods all strategies should have
- Making sure error handling worked the same way

### Key Learning:
Strategy pattern is great when you have different ways to do the same thing. Instead of big if-else statements, you create separate classes for each way. Much cleaner and easier to change
