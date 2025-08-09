# Security-First Development Rules

## Core Security Principles

### Assume Hostile Environment
- All user inputs are potentially malicious
- All external systems are potentially compromised  
- All network communications are potentially intercepted
- All stored data is potentially accessible to attackers

### Defense in Depth
Implement security controls at multiple layers:
- **Input validation** at application boundaries
- **Authentication** and **authorization** for all access
- **Encryption** for data at rest and in transit
- **Network security** controls and segmentation
- **Monitoring** and **logging** for security events
- **Incident response** procedures

### Principle of Least Privilege
- Grant minimum necessary permissions
- Regular review and rotation of access credentials
- Separate accounts for different privilege levels
- Time-limited access where appropriate

## Authentication & Authorization

### Authentication Requirements
```javascript
// Never store passwords in plain text
// Always use proper password hashing
const bcrypt = require('bcrypt');
const saltRounds = 12;

const hashPassword = async (password) => {
  return await bcrypt.hash(password, saltRounds);
};

// Implement proper session management
const session = require('express-session');
const MongoStore = require('connect-mongo');

app.use(session({
  secret: process.env.SESSION_SECRET, // Store in environment variables
  resave: false,
  saveUninitialized: false,
  store: MongoStore.create({
    mongoUrl: process.env.MONGODB_URI
  }),
  cookie: {
    secure: process.env.NODE_ENV === 'production', // HTTPS in production
    httpOnly: true, // Prevent XSS
    maxAge: 1000 * 60 * 60 * 24 // 24 hours
  }
}));
```

### Token Security
```javascript
// Secure JWT handling
const jwt = require('jsonwebtoken');

const generateToken = (user) => {
  return jwt.sign(
    { 
      userId: user.id, 
      email: user.email 
    },
    process.env.JWT_SECRET,
    { 
      expiresIn: '1h',
      issuer: 'your-app',
      audience: 'your-app-users'
    }
  );
};

// Always verify tokens properly
const verifyToken = (token) => {
  try {
    return jwt.verify(token, process.env.JWT_SECRET);
  } catch (error) {
    throw new Error('Invalid token');
  }
};
```

### Multi-Factor Authentication
For sensitive operations, implement additional verification:
- Time-based one-time passwords (TOTP)
- SMS verification (with rate limiting)
- Email verification for account changes
- Biometric authentication where available

## Input Validation & Sanitization

### Comprehensive Input Validation
```javascript
const validator = require('validator');
const xss = require('xss');

// Validate all inputs against expected formats
const validateInput = (input, type) => {
  switch (type) {
    case 'email':
      return validator.isEmail(input);
    case 'url':
      return validator.isURL(input);
    case 'alphanumeric':
      return validator.isAlphanumeric(input);
    default:
      return false;
  }
};

// Sanitize HTML content
const sanitizeHtml = (html) => {
  return xss(html, {
    whiteList: {
      p: [],
      br: [],
      strong: [],
      em: []
    }
  });
};

// SQL injection prevention
const db = require('pg');
// Always use parameterized queries
const getUserById = async (userId) => {
  const result = await db.query(
    'SELECT * FROM users WHERE id = $1',
    [userId]
  );
  return result.rows[0];
};
```

### File Upload Security
```javascript
const multer = require('multer');
const path = require('path');

// Restrict file types and sizes
const upload = multer({
  dest: 'uploads/',
  fileFilter: (req, file, cb) => {
    const allowedTypes = /jpeg|jpg|png|gif/;
    const extname = allowedTypes.test(
      path.extname(file.originalname).toLowerCase()
    );
    const mimetype = allowedTypes.test(file.mimetype);
    
    if (mimetype && extname) {
      return cb(null, true);
    } else {
      cb(new Error('Only image files are allowed'));
    }
  },
  limits: {
    fileSize: 5 * 1024 * 1024 // 5MB limit
  }
});
```

## Data Protection

### Environment Variables
```bash
# Never commit secrets to version control
# Use .env files for development (add .env to .gitignore)
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
JWT_SECRET=your-super-secret-jwt-key
API_KEY=your-external-api-key
SESSION_SECRET=your-session-secret
```

### Database Security
```javascript
// Encrypt sensitive data at rest
const crypto = require('crypto');

const encrypt = (text) => {
  const algorithm = 'aes-256-cbc';
  const key = crypto.scryptSync(process.env.ENCRYPTION_KEY, 'salt', 32);
  const iv = crypto.randomBytes(16);
  
  const cipher = crypto.createCipher(algorithm, key, iv);
  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  
  return {
    encrypted,
    iv: iv.toString('hex')
  };
};

// Regular database backups with encryption
// Implement proper access controls
// Use connection pooling with limits
```

### API Security
```javascript
// Rate limiting
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP'
});

app.use('/api/', limiter);

// CORS configuration
const cors = require('cors');

app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') || 'http://localhost:3000',
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));

// Security headers
const helmet = require('helmet');
app.use(helmet());
```

## Logging & Monitoring

### Security Event Logging
```javascript
const winston = require('winston');

const securityLogger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ 
      filename: 'logs/security.log',
      level: 'warn'
    }),
    new winston.transports.Console({
      format: winston.format.simple()
    })
  ]
});

// Log security events
const logSecurityEvent = (event, details) => {
  securityLogger.warn('Security Event', {
    event,
    details,
    timestamp: new Date().toISOString(),
    ip: details.ip,
    userAgent: details.userAgent
  });
};

// Example usage
app.post('/login', (req, res) => {
  // ... authentication logic
  
  if (!validCredentials) {
    logSecurityEvent('Failed Login Attempt', {
      email: req.body.email,
      ip: req.ip,
      userAgent: req.get('User-Agent')
    });
    return res.status(401).json({ error: 'Invalid credentials' });
  }
});
```

### Monitoring Checklist
- [ ] Failed authentication attempts tracked and alerted
- [ ] Unusual access patterns detected
- [ ] System resource usage monitored
- [ ] Error rates tracked and alerted
- [ ] Security patches applied regularly
- [ ] Dependency vulnerabilities scanned
- [ ] Access logs reviewed regularly
- [ ] Incident response procedures tested

## Security Review Checklist

### Code Review Security Focus
- [ ] All user inputs validated and sanitized
- [ ] No hardcoded secrets or credentials
- [ ] Proper error handling (no information leakage)
- [ ] Authentication and authorization checks in place
- [ ] Secure communication protocols used (HTTPS/TLS)
- [ ] Dependencies up to date and vulnerability-free
- [ ] Logging implemented for security events
- [ ] Rate limiting applied to prevent abuse

### Deployment Security
- [ ] Environment variables properly configured
- [ ] Database connections secured and encrypted
- [ ] HTTPS enforced in production
- [ ] Security headers configured
- [ ] File permissions properly restricted
- [ ] Backup and disaster recovery procedures tested
- [ ] Monitoring and alerting systems active
- [ ] Incident response procedures documented and accessible