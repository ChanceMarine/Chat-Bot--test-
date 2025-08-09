# Code Quality & Error Prevention Rules

## File Organization Standards

### File Size Limits
- **Maximum file size**: 500 lines
- **Ideal file size**: 200-300 lines
- **When to split**: If file exceeds 400 lines, plan refactoring
- **Split strategy**: By feature, responsibility, or logical grouping

### Directory Structure Patterns
```
src/
├── components/          # Reusable UI components
│   ├── common/         # Shared across features
│   └── feature-name/   # Feature-specific components
├── services/           # Business logic and API calls
├── utils/              # Pure utility functions
├── hooks/              # Custom React hooks (if applicable)
├── types/              # Type definitions
├── constants/          # Application constants
└── tests/              # Test files
```

### Naming Conventions
```javascript
// Files: kebab-case
user-profile-component.js
api-service-helper.js

// Variables and functions: camelCase
const userProfile = {};
function getUserById(id) {}

// Constants: UPPER_SNAKE_CASE
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;

// Classes: PascalCase
class UserProfileManager {}

// Components: PascalCase (React/Vue)
const UserProfile = () => {};
```

## Error Handling Standards

### Comprehensive Error Handling Pattern
```javascript
// Service layer error handling
class ApiService {
  async fetchUser(userId) {
    try {
      const response = await fetch(`/api/users/${userId}`);
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      
      const data = await response.json();
      return { success: true, data };
      
    } catch (error) {
      // Log error for debugging
      console.error('Failed to fetch user:', error);
      
      // Return structured error response
      return {
        success: false,
        error: {
          message: this.getErrorMessage(error),
          code: this.getErrorCode(error),
          timestamp: new Date().toISOString()
        }
      };
    }
  }
  
  getErrorMessage(error) {
    if (error.name === 'TypeError') {
      return 'Network connection failed';
    }
    if (error.message.includes('404')) {
      return 'User not found';
    }
    return 'An unexpected error occurred';
  }
  
  getErrorCode(error) {
    if (error.name === 'TypeError') return 'NETWORK_ERROR';
    if (error.message.includes('404')) return 'NOT_FOUND';
    return 'UNKNOWN_ERROR';
  }
}

// Component error handling
const UserProfile = ({ userId }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const loadUser = async () => {
      setLoading(true);
      setError(null);
      
      const result = await apiService.fetchUser(userId);
      
      if (result.success) {
        setUser(result.data);
      } else {
        setError(result.error);
      }
      
      setLoading(false);
    };
    
    loadUser();
  }, [userId]);
  
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorDisplay error={error} />;
  if (!user) return <div>No user found</div>;
  
  return <div>{/* User profile content */}</div>;
};
```

### Error Logging Strategy
```javascript
const winston = require('winston');

// Configure logger
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' }),
    new winston.transports.Console({
      format: winston.format.simple()
    })
  ]
});

// Error logging helper
const logError = (error, context = {}) => {
  logger.error('Application Error', {
    message: error.message,
    stack: error.stack,
    context,
    timestamp: new Date().toISOString()
  });
};

// Usage in application
try {
  await processPayment(paymentData);
} catch (error) {
  logError(error, {
    operation: 'processPayment',
    userId: paymentData.userId,
    amount: paymentData.amount
  });
  throw new PaymentProcessingError('Payment failed');
}
```

## Testing Requirements

### Unit Testing Standards
```javascript
// Test file naming: [filename].test.js
// Example: user-service.test.js

describe('UserService', () => {
  let userService;
  
  beforeEach(() => {
    userService = new UserService();
  });
  
  describe('getUserById', () => {
    it('should return user when ID exists', async () => {
      // Arrange
      const userId = '123';
      const expectedUser = { id: userId, name: 'John Doe' };
      
      // Mock external dependencies
      jest.spyOn(apiClient, 'get').mockResolvedValue(expectedUser);
      
      // Act
      const result = await userService.getUserById(userId);
      
      // Assert
      expect(result.success).toBe(true);
      expect(result.data).toEqual(expectedUser);
      expect(apiClient.get).toHaveBeenCalledWith(`/users/${userId}`);
    });
    
    it('should handle error when user not found', async () => {
      // Arrange
      const userId = 'nonexistent';
      jest.spyOn(apiClient, 'get').mockRejectedValue(new Error('404'));
      
      // Act
      const result = await userService.getUserById(userId);
      
      // Assert
      expect(result.success).toBe(false);
      expect(result.error.code).toBe('NOT_FOUND');
    });
  });
});
```

### Integration Testing Patterns
```javascript
// Integration test example
describe('User API Integration', () => {
  let app;
  let testDb;
  
  beforeAll(async () => {
    // Set up test database
    testDb = await setupTestDatabase();
    app = createApp(testDb);
  });
  
  afterAll(async () => {
    await cleanupTestDatabase(testDb);
  });
  
  beforeEach(async () => {
    await seedTestData(testDb);
  });
  
  it('should create and retrieve user', async () => {
    // Create user
    const newUser = { name: 'Jane Doe', email: 'jane@example.com' };
    const createResponse = await request(app)
      .post('/api/users')
      .send(newUser)
      .expect(201);
    
    const userId = createResponse.body.id;
    
    // Retrieve user
    const getResponse = await request(app)
      .get(`/api/users/${userId}`)
      .expect(200);
    
    expect(getResponse.body.name).toBe(newUser.name);
    expect(getResponse.body.email).toBe(newUser.email);
  });
});
```

## Code Documentation Standards

### JSDoc Documentation
```javascript
/**
 * Processes a payment transaction
 * @param {Object} paymentData - Payment information
 * @param {string} paymentData.amount - Payment amount in cents
 * @param {string} paymentData.currency - Currency code (e.g., 'USD')
 * @param {string} paymentData.customerId - Customer identifier
 * @param {string} paymentData.paymentMethodId - Payment method identifier
 * @returns {Promise<Object>} Payment result with success status and transaction ID
 * @throws {PaymentProcessingError} When payment processing fails
 * @example
 * const result = await processPayment({
 *   amount: 2000, // $20.00
 *   currency: 'USD',
 *   customerId: 'cus_123',
 *   paymentMethodId: 'pm_456'
 * });
 */
async function processPayment(paymentData) {
  // Implementation
}
```

### README Documentation Template
```markdown
# Project Name

## Overview
Brief description of what this project does and why it exists.

## Prerequisites
- Node.js 18+
- PostgreSQL 13+
- Redis 6+

## Installation
```bash
npm install
cp .env.example .env
# Edit .env with your configuration
npm run setup:db
```

## Development
```bash
npm run dev          # Start development server
npm run test         # Run tests
npm run test:watch   # Run tests in watch mode
npm run lint         # Check code quality
```

## Project Structure
- `/src` - Source code
- `/tests` - Test files
- `/docs` - Documentation
- `/scripts` - Build and deployment scripts

## API Documentation
- API docs available at `/api/docs` when running locally
- Swagger/OpenAPI specification in `/docs/api-spec.yaml`

## Contributing
1. Create feature branch
2. Write tests for new functionality
3. Ensure all tests pass
4. Create pull request with description
```

## Performance Standards

### Complexity Scoring
Rate each function/module on complexity (1-5 scale):
- **1 (Simple)**: Straightforward logic, minimal branches
- **2 (Low)**: Some conditional logic, easy to follow
- **3 (Moderate)**: Multiple conditions, moderate complexity
- **4 (High)**: Complex logic, multiple nested conditions
- **5 (Very High)**: Highly complex, consider refactoring

### Performance Monitoring
```javascript
// Performance monitoring helper
const measurePerformance = (operationName) => {
  const start = performance.now();
  
  return {
    end: () => {
      const duration = performance.now() - start;
      console.log(`${operationName} took ${duration.toFixed(2)}ms`);
      
      // Log slow operations
      if (duration > 1000) {
        logger.warn('Slow Operation', {
          operation: operationName,
          duration: duration,
          timestamp: new Date().toISOString()
        });
      }
      
      return duration;
    }
  };
};

// Usage
const perf = measurePerformance('Database Query');
const users = await db.query('SELECT * FROM users');
perf.end();
```

## Quality Checklist

### Before Code Commit
- [ ] All functions under 50 lines
- [ ] All files under 500 lines  
- [ ] No hardcoded values (use constants)
- [ ] Error handling implemented
- [ ] Unit tests written and passing
- [ ] JSDoc comments for public functions
- [ ] No console.log statements (use proper logging)
- [ ] Linting passes without errors
- [ ] No TODO comments (create issues instead)

### Before Feature Completion
- [ ] Integration tests passing
- [ ] Performance requirements met
- [ ] Security review completed
- [ ] Documentation updated
- [ ] Error scenarios tested
- [ ] Edge cases considered
- [ ] Accessibility requirements met (for UI)
- [ ] Cross-browser testing completed (for web)