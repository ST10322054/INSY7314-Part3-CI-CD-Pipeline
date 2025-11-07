# International Payment Portal - Customer Portal

A secure customer-facing international payment system built with React. This application enables customers to register, login, and submit international payment requests via SWIFT with comprehensive security measures.

ST10252644 - Cherika Bodde

ST10322054 - Nathan Hani

ST10399538 - Inge Dafel

## Video Demonstration

**Watch the youtube video here:** https://youtu.be/De3HPeBx_rU?si=BsnoUYogSRFBpIGZ

## SonarQube Cloud & Circle Ci Pipeline Links

**SonarQube Link:** https://sonarcloud.io/summary/new_code?id=ST10322054_INSY7314-Part3-CI-CD-Pipeline&branch=main

**Circle Ci Pipeline Link:** https://app.circleci.com/pipelines/github/ST10322054

---

## Features

### Customer Portal Functionality
- **Secure User Registration**
  - User registration with full name, ID number, account number, username, and password
  - Client-side validation using Yup schema validation
  - Server-side validation with RegEx whitelisting
  
- **Secure Authentication**
  - Login with username and password
  - Password hashing and salting using bcrypt
  - Session management with secure cookies
  - Password visibility toggle for better UX
  
- **International Payment Submission**
  - Submit payment requests with amount, currency (ZAR/USD/EUR), and SWIFT details
  - Input validation using RegEx patterns to prevent injection attacks
  - Real-time form validation with Formik and Yup
  - Visual feedback for successful/failed submissions

### Security Features (Task 2 Requirements)

#### 1. Password Security - Hashing and Salting
- All passwords are hashed using **bcrypt** with automatic salting
- Passwords are never stored in plain text
- Password strength requirements enforced:
  - Minimum 8 characters
  - At least one uppercase letter
  - At least one lowercase letter
  - At least one number
  - At least one special character

#### 2. Input Whitelisting with RegEx Patterns
All user inputs are validated using strict RegEx patterns on both client and server:
```javascript
Full Name: /^[A-Za-z\s\-'\.]{2,100}$/
ID Number: /^\d{10,20}$/
Account Number: /^\d{6,20}$/
Username: /^[a-zA-Z0-9]{4,}$/
Password: /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[\W_]).{8,}$/
SWIFT Code: /^[A-Za-z0-9]{8,11}$/
Payee Account: /^\d{6,20}$/
```

#### 3. SSL/TLS Encryption
- All traffic is served over **HTTPS** using SSL/TLS certificates
- Self-signed certificates used for development
- Certificate and key files stored in `/server/certs/`
- Backend server runs on HTTPS port 443
- Frontend development server configured for HTTPS

#### 4. Protection Against Attacks
The application is hardened against multiple attack vectors:

- **SQL Injection**: Input validation and parameterized queries
- **Cross-Site Scripting (XSS)**: Input sanitization and Content Security Policy headers
- **Cross-Site Request Forgery (CSRF)**: CSRF tokens and SameSite cookie attributes
- **Session Hijacking**: Secure session management with HTTPOnly and Secure cookies
- **Clickjacking**: X-Frame-Options header set to DENY
- **Man-in-the-Middle (MITM)**: SSL/TLS encryption for all communications
- **DDoS Attacks**: Rate limiting with express-brute
- **Brute Force Attacks**: Login attempt throttling

---

## Tech Stack

### Frontend
- **React 18** - UI framework
- **React Router** - Client-side routing and navigation
- **Formik** - Form state management and handling
- **Yup** - Client-side schema validation
- **Axios** - HTTP client for API communication
- **Vite** - Fast build tool and development server
- **CSS3** - Custom styling with gradients, animations, and glassmorphism effects

### Backend
- **Node.js** - Runtime environment
- **Express.js** - Web application framework
- **bcrypt** - Password hashing and salting
- **express-validator** - Server-side input validation
- **helmet** - Security headers middleware
- **express-brute** - Rate limiting and brute-force protection
- **cors** - Cross-Origin Resource Sharing configuration
- **MongoDB/MySQL** - Database for storing user data and payments

---

## Installation

### Prerequisites
- Node.js (v16 or higher)
- npm or yarn
- MongoDB or MySQL database

### Setup Instructions

1. **Clone the repository**
```bash
git clone <repository-url>
cd INSY7312-TASK-2-main
```

2. **Install Server Dependencies**
```bash
cd server
npm install
```

3. **Install Client Dependencies**
```bash
cd ../client
npm install
npm install axios yup
```

4. **Generate SSL Certificates**
```bash
cd server/certs
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```

5. **Configure Environment Variables**

Create a `.env` file in the `server` directory:
```env
PORT=443
DB_CONNECTION_STRING=your_database_connection_string
JWT_SECRET=your_jwt_secret_key
NODE_ENV=development
```

6. **Start the Backend Server**
```bash
cd server
node index.js
```

You should see:
```
DB synced
HTTPS server running on port 443
```

7. **Start the Frontend Development Server**
```bash
cd client
npm run dev
```

You should see:
```
VITE v7.1.7 ready in 891 ms
Local: https://localhost:5173/
```

8. **Access the Application**
- Frontend: `https://localhost:5173`
- Backend API: `https://localhost:443`

**Note**: You may need to accept the security warning in your browser due to the self-signed SSL certificate.

---

## Project Structure

```
INSY7312-TASK-2-main/
├── client/                    # React frontend
│   ├── src/
│   │   ├── api/
│   │   │   └── axios.js      # Axios configuration with base URL
│   │   ├── pages/
│   │   │   ├── Register.jsx  # Customer registration page
│   │   │   ├── Login.jsx     # Customer login page
│   │   │   └── Dashboard.jsx # Payment submission page
│   │   ├── App.jsx           # Main app component with routing
│   │   ├── index.css         # Global styles
│   │   └── main.jsx          # React entry point
│   ├── certs/                # SSL certificates (if needed)
│   ├── package.json
│   └── vite.config.js        # Vite HTTPS configuration
│
├── server/                    # Node.js/Express backend
│   ├── certs/
│   │   ├── cert.pem          # SSL certificate
│   │   └── key.pem           # SSL private key
│   ├── routes/
│   │   ├── auth.js           # Registration and login routes
│   │   └── payments.js       # Payment submission routes
│   ├── models/               # Database models
│   ├── middleware/           # Security middleware
│   ├── index.js              # Server entry point
│   └── package.json
│
└── README.md
```

---

## API Endpoints

### Authentication
- **POST** `/api/auth/register`
  - Registers a new customer
  - Request body: `{ fullName, idNumber, accountNumber, username, password }`
  - Returns: Success message or error

- **POST** `/api/auth/login`
  - Authenticates a customer
  - Request body: `{ username, password }`
  - Returns: Session token and user data

### Payments
- **POST** `/api/payments`
  - Submits a new payment request
  - Requires authentication
  - Request body: `{ amount, currency, provider, payeeAccount, swiftCode }`
  - Returns: Payment confirmation with status "pending"

---

## Testing Instructions

Follow these steps to test the customer portal:



### Demo Accounts

You can use these pre-created accounts to test the system:

| Role | Username | Password |
|------|-----------|-----------|
| Customer | susan | SuSanSmith!3 |
| Employee | staff1 | EmployeeP@ss1 |

You may also register your own test account if needed.





### Step 1 — Start the Application
1. Run both the server and client locally.  
2. Open the browser and go to:  
   `https://localhost:5173`



### Step 2 — Login as Customer
1. On the login page, enter:
   - Username: `susan`
   - Password: `SuSanSmith!3`
2. Click **Sign In**  
3. You will be redirected to the Dashboard.



### Step 3 — Submit a Payment
1. On the Dashboard, complete the payment form:
   - Amount: `1000` (or any amount)
   - Currency: `ZAR` / `USD` / `EUR`
   - Provider: `SWIFT`
   - Payee Account Number: `123456778`
   - SWIFT Code: `ABCDUS33XXX`
2. Click **Submit Payment**  
3. A message should appear:  
   *“Payment created successfully! Status: Pending verification.”*

4. Log out after the payment is created.



### Step 4 — Login as Employee
1. On the login page, enter:
   - Username: `staff1`
   - Password: `EmployeeP@ss1`
2. Click **Sign In**  
3. You will be redirected to the Employee Dashboard.



### Step 5 — Approve the Payment
1. Locate the payment created by `susan` (or the test user).  
2. Review the details.  
3. Approve or verify the payment as required.  
4. Confirm that the payment status updates to *Approved* or *Verified*.



### Step 6 — Security and Validation Checks
- Test invalid inputs to confirm validation messages appear.  
- Confirm the password input hides characters correctly.  
- Ensure the connection uses HTTPS (`https://` in address bar).  
- Try SQL-injection-style inputs (only in local testing) to confirm protection.

---

## Security Implementation Details

### Password Hashing (bcrypt)
```javascript
// Server-side password hashing
const bcrypt = require('bcrypt');
const saltRounds = 10;

// During registration
const hashedPassword = await bcrypt.hash(password, saltRounds);

// During login
const isMatch = await bcrypt.compare(inputPassword, hashedPassword);
```

### Input Validation (RegEx)
```javascript
// Client-side validation with Yup
const schema = Yup.object({
  fullName: Yup.string()
    .matches(/^[A-Za-z\s\-'\.]{2,100}$/, 'Invalid name')
    .required('Required'),
  password: Yup.string()
    .matches(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[\W_]).{8,}$/, 'Weak password')
    .required('Required')
});

// Server-side validation mirrors client-side patterns
```

### SSL/TLS Configuration
```javascript
// Server setup with HTTPS
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('./certs/key.pem'),
  cert: fs.readFileSync('./certs/cert.pem')
};

https.createServer(options, app).listen(443);
```

### Attack Protection Middleware
```javascript
// Helmet for security headers
app.use(helmet());
app.use(helmet.frameguard({ action: 'deny' }));

// CORS configuration
app.use(cors({
  origin: 'https://localhost:5173',
  credentials: true
}));

// Rate limiting with express-brute
const bruteforce = new ExpressBrute(store);
app.post('/api/auth/login', bruteforce.prevent, loginHandler);
```

---

## Task 2 Requirements Checklist

- [x] **Password security enforced with hashing and salting** (bcrypt implementation)
- [x] **All input whitelisted using RegEx patterns** (client and server-side validation)
- [x] **All traffic served over SSL** (HTTPS on both frontend and backend)
- [x] **Protection against all attacks**:
  - [x] SQL Injection
  - [x] XSS (Cross-Site Scripting)
  - [x] CSRF (Cross-Site Request Forgery)
  - [x] Session Hijacking
  - [x] Clickjacking
  - [x] Man-in-the-Middle
  - [x] DDoS/Brute Force
- [x] **Video demonstration** (YouTube link provided above)

---

## Task 3 - Security & DevOps Enhancement

Task 3 represents a **major security and DevOps upgrade**, transforming the International Payments Portal into an **enterprise-grade, secure, and continuously monitored platform** with automated testing and deployment.

###  What's New in Task 3

##  Administrator Guide: Adding Employees

Administrators can create employee accounts using Visual Studio Code's integrated terminal. Employees can then log in to verify and approve customer payments.

### Method 1: Using VS Code Terminal (Recommended)

#### Step 1: Open VS Code Terminal

1. Open your project in Visual Studio Code
2. Open the integrated terminal:
   - **Windows/Linux:** Press `Ctrl + `` ` `` (Ctrl + Backtick)
   - **Mac:** Press `Cmd + `` ` ``
   - **Or:** Menu → View → Terminal

You should see a terminal panel at the bottom of VS Code.

#### Step 2: Navigate to Server Directory

In the VS Code terminal, type:

```bash
cd server
```

Press Enter. You should now be in the `server` directory.

#### Step 3: Create Employee Account

Use the `createEmployee.js` script with this command format:

```bash
node createEmployee.js <username> <password> "Full Name"
```

**Example Commands:**

```bash
# Create an employee named John Doe
node createEmployee.js john.doe SecureP@ss123! "John Doe"

# Create an employee named Michael Brown
node createEmployee.js michael.brown W0rk3rP@ss! "Michael Brown"
```

**Important Notes:**
- Username should be lowercase, no spaces (use dots or underscores)
- Password must meet security requirements (see below)
- Full name must be in quotes if it contains spaces

#### Step 4: Verify Creation

After running the command, you should see:

```bash
✓ Employee created: john.doe

Employee Details:
├─ Username: john.doe
├─ Full Name: John Doe
```

The employee can now log in to the employee portal at:
- **URL:** `https://localhost:5173/employee`
- **Username:** The username you created
- **Password:** The password you set

### Method 2: Using Git Bash (Alternative)

If you prefer Git Bash:

1. **Open Git Bash** in your project directory:
   - Right-click in the project folder
   - Select "Git Bash Here"

2. **Navigate to server directory:**
   ```bash
   cd server
   ```

3. **Create employee:**
   ```bash
   node createEmployee.js username password "Full Name"
   ```

### Method 3: Using Command Prompt/PowerShell (Windows)

1. **Open Command Prompt or PowerShell**
2. **Navigate to project:**
   ```cmd
   cd C:\path\to\your\payment-portal\server
   ```
3. **Create employee:**
   ```cmd
   node createEmployee.js username password "Full Name"
   ```


####  Enhanced Security Features (3 Major Additions)

##### 1. Security Headers Middleware 

Comprehensive HTTP security headers automatically applied to all responses to protect against web vulnerabilities.

**Headers Added:**
```javascript
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000; includeSubDomains
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), microphone=(), camera=()
```

**Protection Against:**
- ✅ Cross-Site Scripting (XSS) - Enhanced beyond Task 2
- ✅ Clickjacking attacks - Additional protection layer
- ✅ MIME type sniffing - Prevents content type confusion
- ✅ Protocol downgrade attacks - Enforces HTTPS
- ✅ Information disclosure - Restricts referrer data

**How to Test:**
```bash
curl -I https://localhost:443/api/health
# Check response headers include security headers
```

**Usage:** Automatically applied - no changes needed to existing code!

##### 2. Rate Limiting 
**File:** `server/middleware/rateLimiter.js`

Advanced rate limiting to prevent brute force, credential stuffing, and DoS attacks - enhancing Task 2's express-brute implementation.


**How it Works:**
- Tracks requests by IP address
- Returns `429 Too Many Requests` when limit exceeded
- Automatic reset after time window
- Memory store for development, Redis for production

**Error Response:**
```json
{
  "statusCode": 429,
  "error": "Too Many Requests",
  "message": "Too many requests, please try again later.",
  "retryAfter": 900
}
```

**Frontend Handling:**
```javascript
try {
  await axios.post('/api/auth/login', credentials);
} catch (error) {
  if (error.response?.status === 429) {
    const minutes = Math.ceil(error.response.data.retryAfter / 60);
    alert(`Too many attempts. Please wait ${minutes} minutes.`);
  }
}
```

**Testing:**
```bash
# Trigger rate limit (6th attempt fails)
for i in {1..6}; do
  curl -X POST https://localhost:443/api/auth/login \
    -H "Content-Type: application/json" \
    -d '{"username":"test","password":"wrong"}'
  echo "Attempt $i"
done
```

##### 3. Advanced Input Validation 
**File:** `server/middleware/inputValidation.js`

Enhanced validation building on Task 2's RegEx patterns with comprehensive security checks and detailed error messages.

**Payment Validation:**
```javascript
Amount:
  - Type: Number (positive)
  - Min: 0.01
  - Max: 1,000,000
  - Prevents: Negative amounts, zero values, overflow

Currency:
  - Allowed: USD, EUR, GBP, ZAR
  - Pattern: /^[A-Z]{3}$/
  - Prevents: Invalid currency codes, SQL injection

SWIFT Code:
  - Pattern: /^[A-Z]{6}[A-Z0-9]{2}([A-Z0-9]{3})?$/
  - Length: 8 or 11 characters
  - Format: AAAABBCCXXX (bank code + country + location + branch)
  - Prevents: Invalid codes, injection attacks

Account Numbers:
  - Pattern: /^\d{6,20}$/
  - Only numeric digits
  - Length: 6-20 characters
  - Prevents: Special characters, SQL injection
```

**User Credential Validation (Enhanced from Task 2):**
```javascript
Username:
  - Pattern: /^[a-zA-Z0-9._-]{3,50}$/
  - Length: 3-50 characters
  - Allowed: Alphanumeric, dots, underscores, hyphens
  - Prevents: Special characters, spaces, injection

Password (Task 2 + Task 3):
  - Minimum 8 characters
  - At least 1 uppercase (A-Z)
  - At least 1 lowercase (a-z)
  - At least 1 number (0-9)
  - At least 1 special character (!@#$%^&*)
  - Pattern: /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&]).{8,}$/
  - Additional: Check against common passwords, prevent sequential chars
```

**Validation Error Response:**
```json
{
  "statusCode": 400,
  "error": "Validation Error",
  "message": "Invalid input data",
  "details": {
    "amount": "Amount must be between 0.01 and 1,000,000",
    "currency": "Currency must be one of: USD, EUR, GBP, ZAR",
    "swiftCode": "Invalid SWIFT code format. Expected: AAAABBCCXXX (8 or 11 chars)"
  }
}
```

####  DevOps Features (3 Major Additions)

##### 4. CircleCI Pipeline 
**File:** `.circleci/config.yml`

Automated CI/CD pipeline that runs comprehensive tests and security checks on every commit to GitHub.

**View Pipeline:**
- Dashboard: `https://app.circleci.com/pipelines/github/YOUR_USERNAME/payment-portal`
- Automatic trigger: Every `git push` to any branch
- Email notifications: Sent on build failure
- Status checks: Shown on GitHub pull requests

**Pipeline Badge (add to README):**
```markdown
[![CircleCI](https://circleci.com/gh/YOUR_USERNAME/payment-portal.svg?style=svg)](https://circleci.com/gh/YOUR_USERNAME/payment-portal)
```

##### 5. SonarQube Integration 
**File:** `sonar-project.properties`

Continuous code quality and security monitoring integrated with CircleCI pipeline.

**Current Quality Metrics (Example):**
| Metric | Description | Target | Current | Status |
|--------|-------------|--------|---------|--------|
| **Bugs** | Code defects | 0 | 0 | ✅ A |
| **Vulnerabilities** | Security issues | 0 | 0 | ✅ A |
| **Code Smells** | Maintainability issues | < 50 | 8 | ✅ A |
| **Test Coverage** | Code covered by tests | > 70% | 82% | ✅ A |
| **Security Hotspots** | Code needing review | 0 | 0 | ✅ |
| **Duplications** | Duplicate code | < 3% | 1.2% | ✅ |
| **Technical Debt** | Time to fix issues | < 2h | 45min | ✅ |

**Quality Gates (Must Pass for Production):**
- ✅ No new bugs introduced
- ✅ No new vulnerabilities
- ✅ Coverage on new code > 80%
- ✅ No new security hotspots
- ✅ Maintainability rating ≥ A
- ✅ Reliability rating ≥ A
- ✅ Security rating ≥ A

**View Dashboard:**
- SonarCloud: `https://sonarcloud.io/summary/new_code?id=ST10322054_INSY7314-Part3-CI-CD-Pipeline&branch=main`
- Updates automatically after each pipeline run
- Detailed issue breakdown with fix recommendations

**Configuration:**
```properties
# sonar-project.properties
sonar.projectKey=ST10322054_INSY7314-Part3-CI-CD-Pipeline
sonar.organization=st10322054

sonar.projectName=International Payments Portal
sonar.projectVersion=1.0.0

# Source directories
sonar.sources=client/src,server
sonar.tests=client/src,server
sonar.test.inclusions=**/*.test.js,**/*.test.jsx,**/*.spec.js

# Exclusions
sonar.exclusions=**/node_modules/**,**/dist/**,**/build/**,**/coverage/**

# Coverage reports
sonar.javascript.lcov.reportPaths=client/coverage/lcov.info,server/coverage/lcov.info

sonar.sourceEncoding=UTF-8
```
## Images of SonarQube:
<img width="1918" height="967" alt="image" src="https://github.com/user-attachments/assets/c486b48e-2e5e-4c65-8f3e-25f9b663ffae" />
<img width="1918" height="967" alt="image" src="https://github.com/user-attachments/assets/8c60d2a8-4742-43a8-a5d5-8035bf066e8b" />
<img width="1918" height="970" alt="image" src="https://github.com/user-attachments/assets/a9897094-81aa-4e58-b4ae-14ab9b325d86" />
<img width="1915" height="968" alt="image" src="https://github.com/user-attachments/assets/b4953528-7dba-4912-aad2-0c1c6635485d" />
<img width="1918" height="967" alt="image" src="https://github.com/user-attachments/assets/26e316d4-a03f-4174-837b-45b974d3a102" />
<img width="1918" height="966" alt="image" src="https://github.com/user-attachments/assets/f8c75772-5998-4eeb-bc2e-96561823737e" />
<img width="1918" height="962" alt="image" src="https://github.com/user-attachments/assets/a3450cf6-bd21-439b-a82a-47bf1e6692b8" />


## Images of the Circle Ci Pipeline:
<img width="1918" height="967" alt="image" src="https://github.com/user-attachments/assets/2ae2b211-9d46-4b7e-a55c-57973aab63dc" />
<img width="1918" height="968" alt="image" src="https://github.com/user-attachments/assets/381ebbb6-3172-4d32-a627-9844b5d70d5b" />
<img width="1918" height="977" alt="image" src="https://github.com/user-attachments/assets/408f66ef-7d52-4879-9263-4bbdf7e733a1" />
<img width="1918" height="963" alt="image" src="https://github.com/user-attachments/assets/1dcdd0f6-1e84-4acc-8d46-e73d97dbb7d8" />

# SonarQube Cloud Link: 
https://sonarcloud.io/summary/new_code?id=ST10322054_INSY7314-Part3-CI-CD-Pipeline&branch=main 
# Circle Ci Pipeline Link:
https://app.circleci.com/pipelines/github/ST10322054

### ✅ Task 3 Requirements Checklist

- [x] **Security Headers Middleware** (Comprehensive HTTP security headers)
- [x] **Enhanced Input Validation** (Detailed validation with error messages)
- [x] **CircleCI Pipeline** (6-stage automated CI/CD)
- [x] **SonarQube Integration** (Code quality and security monitoring)
- [x] **Quality Gates** (All metrics pass A rating)
- [x] **Zero Vulnerabilities** (Confirmed by security scans)
- [x] **Automated Security Audit** (npm audit in pipeline)
- [x] **Documentation** (Complete setup and usage guides)

---

### 🐛 Troubleshooting Task 3

#### Rate Limit Errors During Development

**Problem:** Getting 429 errors too frequently while testing.

**Solution:**
```javascript
// server/middleware/rateLimiter.js
// Increase limits for development
const generalRateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: process.env.NODE_ENV === 'development' ? 1000 : 100
});
```

#### CircleCI Pipeline Fails on Lint

**Problem:** Pipeline fails at lint stage.

**Solution:**
```bash
# Fix locally first
cd server
npm run lint -- --fix

cd ../client
npm run lint -- --fix

# Commit fixes
git add .
git commit -m "fix: Resolve linting issues"
git push
```

#### SonarQube Token Invalid

**Problem:** SonarQube scan fails with authentication error.

**Solution:**
1. Go to SonarCloud → My Account → Security
2. Generate new token
3. Update in CircleCI:
   - Project Settings → Environment Variables
   - Update SONAR_TOKEN value
4. Re-run pipeline

#### Test Coverage Below Threshold

**Problem:** Coverage drops below 70% after changes.

**Solution:**
```bash
# Check which files lack coverage
npm test -- --coverage

# View detailed report
open coverage/lcov-report/index.html

# Add tests for uncovered code
# Then re-run tests
npm test -- --coverage
```

#### Database Locked During Tests

**Problem:** Tests fail with "database is locked" error.

**Solution:**
```bash
# Stop all running instances
pkill -f "node server"

# Remove lock file
rm server/database.sqlite-journal

# Restart
npm test
```

---
## Known Issues & Considerations

- **Self-signed SSL certificates**: Browser will show security warnings in development. Click "Advanced" and "Proceed to localhost" to continue.
- **Port 443**: May require administrator/sudo privileges to run the server on port 443.
- **Database connection**: Ensure your database is running before starting the server.
- **CORS**: Frontend must run on `https://localhost:5173` for proper CORS configuration.

---

---

## References

Beijer, M de 2019, *Yup validation and TypeScript (and Formik)*, Medium, viewed 6 October 2025, <https://medium.com/@maurice.de.beijer/yup-validation-and-typescript-and-formik-6c342578a20e>.

CSS-Tricks 2023, *A Complete Guide to Flexbox*, CSS-Tricks, viewed 6 October 2025, <https://css-tricks.com/snippets/css/a-guide-to-flexbox/>.

CSS-Tricks 2023, *A Complete Guide to CSS Grid*, CSS-Tricks, viewed 6 October 2025, <https://css-tricks.com/snippets/css/complete-guide-grid/>.

DeGuzman, K 2022, *Getting Started with Axios*, Medium, Bits and Pieces, viewed 6 October 2025, <https://blog.bitsrc.io/the-beginners-guide-to-get-started-with-axios-85a5e8caa2dd>.

Dodds, KC 2024, *How to type a React form onSubmit handler*, Epic React, viewed 6 October 2025, <https://www.epicreact.dev/how-to-type-a-react-form-on-submit-handler>.

freeCodeCamp 2023, *How to Build React Forms with Formik*, freeCodeCamp.org, viewed 6 October 2025, <https://www.freecodecamp.org/news/build-react-forms-with-formik-library/>.

GeeksforGeeks 2021, *How to fetch data from APIs using Asynchronous await in ReactJS?*, GeeksforGeeks, viewed 6 October 2025, <https://www.geeksforgeeks.org/reactjs/how-to-fetch-data-from-apis-using-asynchronous-await-in-reactjs/>.

Google Fonts 2023, *Inter*, Google Fonts, viewed 6 October 2025, <https://fonts.google.com/specimen/Inter>.

MDN Web Docs 2023, *CSS Animations*, MDN Web Docs, viewed 6 October 2025, <https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations>.

MDN Web Docs 2023, *CSS Transitions*, MDN Web Docs, viewed 6 October 2025, <https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions>.

MDN Web Docs 2023, *Using CSS gradients*, MDN Web Docs, viewed 6 October 2025, <https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Images/Using_CSS_gradients>.

Proper 2018, *Stack Overflow*, Stack Overflow, viewed 6 October 2025, <https://stackoverflow.com/questions/50046841/proper-way-to-make-api-fetch-post-with-async-await>.

React Documentation 2023, *Styling and CSS*, React, viewed 6 October 2025, <https://react.dev/learn/styling>.

Tailwind CSS 2023, *Utility-First Fundamentals*, Tailwind CSS, viewed 6 October 2025, <https://tailwindcss.com/docs/utility-first>.

*useState – React* 2025, React.dev, viewed 6 October 2025, <https://react.dev/reference/react/useState>.

W3Schools 2023, *CSS Box Shadow*, W3Schools, viewed 6 October 2025, <https://www.w3schools.com/css/css3_shadows_box.asp>.

W3Schools 2023, *How To Create a Glassmorphism Effect*, W3Schools, viewed 6 October 2025, <https://www.w3schools.com/howto/howto_css_glowing_text.asp>.

