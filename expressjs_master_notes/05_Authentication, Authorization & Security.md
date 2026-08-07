Settle down, beta! Apni notebook aur ek fresh pen nikal lo. Aaj hum backend engineering ke sabse crucial aur sensitive topic par baat karne wale hain: **Chapter 5: Authentication, Authorization & Security**. 

MERN stack developer toh bohot log ban jaate hain beta, par ek "Secured Backend Architect" wahi banta hai jo security concepts ko raddta nahi, balki unke peeche ka *WHY* aur *HOW* dhyan se samajhta hai. Aaj hum sikhenge ki kaise tum apne server ko cyber-attacks se bacha sakte ho, users ko identify kar sakte ho, aur unke data ko secure rakh sakte ho. 

Chai ka cup le lo aur dhyan se board par dekho!

---

# Chapter 5: Authentication, Authorization & Security

---

## 1. Authentication (Pehchan Ka Safar)

### Ye kya hai?
**Authentication** ka simple matlab hai: **"Tum wahi ho jo tum claim kar rahe ho?"**. Jab koi user humaare server par aata hai aur request bhejta hai, toh server ko ye confirm karna hota hai ki request bhejane waala asali user hai ya koi hacker dummy request bhej raha hai. 

### Ye kyu use hota hai?
Server par bohot saara data aisa hota hai jo publicly available nahi hona chahiye—jaise ki user ka profile dashboard, payment invoices, ya billing info. Authentication ke bina koi bhi hacker kisi dusre user ka data dekh sakta hai ya delete kar sakta hai.

### Pure Node.js me ye kaise implement hota?
Pure Node.js me hume `req.headers.cookie` ko manually string string manipulation se parse karna padta tha, ya fir headers se base64 credentials extract karke dynamic custom parsing functions likhne padte the, jisme error handling bohot low-level hoti thi.

### Express.js isse kaise simplify karta hai?
Express hume clean middlewares deta hai jaise **`cookie-parser`** aur **`jsonwebtoken` (JWT)**. Inki madad se hum kuch hi lines me incoming tokens ko decode aur verify kar lete hain bina direct socket buffer reading ke.

---

### Key Authentication Concepts & Terminology

#### 1. Authentication Flow & Login-Logout Process
*   **Login**: Client (React form) user credentials (email + password) bhejta hai. Server unhe database se verify karta hai aur badle me ek unique encrypted security ticket (**Token**) deta hai.
*   **Logout**: Client-side ya cookies se us token ko clear/delete kiya jata hai. Express me hum simply `res.clearCookie('token')` chala kar client se token delete kar dete hain.

#### 2. Session vs. Token Authentication
*   **Session-based (Stateful)**: Server user ki details memory (RAM) ya database me save rakha hai aur client ko ek `Session ID` de deta hai. Har request par server ko apne store me check karna padta hai. Isme server scalability down ho jaati hai jab millions of active users hote hain.
*   **Token-based (Stateless - JWT)**: Server user ko ek self-contained encrypted token deta hai. Server khud koi session state memory me nahi rakhta. Jab bhi request aati hai, server sirf token ko verify karta hai. **Hum is course me strictly Token-based JWT follow karenge**.

```
+-----------------------------------------------------------------------------+
|                          Session vs. Token Flow                             |
+-----------------------------------------------------------------------------+
| STATEFUL (Session):                                                        |
| Client ----> [ Login ] ────> Server creates Session in RAM ──> Sends ID     |
| Client ----> [ Send request + ID ] ──> Server queries RAM ───> Success      |
|                                                                             |
| STATELESS (Token / JWT):                                                   |
| Client ----> [ Login ] ────> Server signs JWT (No RAM storage) ─> Sends JWT |
| Client ----> [ Send request + JWT ] ──> Server validates JWT ─> Success     |
+-----------------------------------------------------------------------------+
```

#### 3. Cookies Basics & Express Cookies Overview
*   **Cookie**: Ek choti text file jo browser me save hoti hai. Server isme data set kar sakta hai aur browser har automatic request ke sath is file ko server tak bhejta hai.
*   **Express Implementation**: Hum `cookie-parser` use karke incoming cookies ko `req.cookies` me direct JavaScript object ke roop me read kar lete hain.

#### 4. JWT (JSON Web Token) Structure, Access & Refresh Tokens
JWT ek cryptographically calculated string hoti hai jiske **teen parts** hote hain jo dot `.` se separated hote hain:
1.  **Header**: Algorithem aur token type ki metadata.
2.  **Payload**: User ka unique non-sensitive data (jaise user ID, role).
3.  **Signature**: Token ki validity proof karne ke liye secret key ke sath generated hash.

*   **Access Token**: Short-lived (kam samay ke liye valid, e.g., 15 mins) token jo resource access karne me use hota hai.
*   **Refresh Token Concept**: Long-lived token jo access token expire hone par background me bina login ke naya access token generate karne ke liye use hota hai.

#### 5. Token Verification & Authentication Middleware
Jab request aati hai, hum custom middleware me **`jwt.verify(token, secret)`** chalate hain. Agar token sahi hai, toh payload data decode hokar `req.user` me attach ho jata hai aur hum aage badhte hain, warna client ko `401 Unauthorized` return ho jata hai.

### Real-Life Analogy
Maan lo tum ek **Theme Park (Express App)** me jaate ho. 
*   **Ticket Counter (Login Route)**: Tum apna ID aur paise dete ho. Wo counter tumhare naam par ek digital wrist-band (JWT Token) de deta hai.
*   **Rides Guards (Auth Middleware)**: Jab bhi tum kisi ride par jaate ho, guard sirf tumhara wrist-band check karta hai. Use tumhara poora history/details check karne ki zaroorat nahi padti, band sahi hai toh entry allowed (next()).

---

## 2. Authorization (Haq / Permissions Check)

### Ye kya hai?
**Authorization** ka matlab hai: **"Kya tumhe is specific action ko perform karne ka Haq (Permission) hai?"**. 

### Authentication vs. Authorization
*   **Authentication (Pehchan)**: "Kaun ho tum?" (User logged in hai ya nahi).
*   **Authorization (Haq)**: "Tum kya kar sakte ho?" (Kya tum admin ho jo is product ko delete kar sake?).

```
Client Request ──► [ Authentication Middleware ] ──► [ Authorization Middleware ] ──► [ Controller ]
                      (Pehchan verified?)               (Admin role check?)
                              │                                  │
                       No ──► 401 Unauthorized            No ──► 403 Forbidden
```

### Role-Based Authorization & Permission Checking
Enterprise apps me users ke roles (e.g., `user`, `admin`, `artist`) database me store kiye jaate hain. Hum route matches ke sath middleware check lagate hain:
```javascript
const authorizeRoles = (...allowedRoles) => {
  return (req, res, next) => {
    // req.user me logged-in user ki details authenticated middleware se aati hain
    if (!allowedRoles.includes(req.user.role)) {
      return res.status(403).json({ success: false, message: "Forbidden: Access Denied!" }); // 403 for Forbidden
    }
    next();
  };
};
```

---

## 3. Password Security (Hash, Salt, Bcrypt)

### Why passwords should never be stored in plain text?
Beta, agar tumne database me direct plain passwords (e.g., `"123456"`) save kiye, aur kisi din tumhaara database compromise/leak ho gaya, toh saare users ke passwords public ho jayenge. Ye ek bohot bada architectural crime hai!

### Hashing Basics & salt
*   **Hashing**: Ek system algorithm jo kisi bhi text input ko fixed-length random-looking string (Hash) me badal deta hai. Ye dynamic **one-way function** hai, matlab hash se kabhi bhi original password recover nahi kiya ja sakta.
*   **Salt (Solting)**: Har password hashing ke sath ek random dynamic character data string append ki jaati hai. Isse agar do users ka password same `"1234"` hai, toh bhi unka final database hash bilkul alag-alag generate hoga.

```
Plain Password ("qwerty") ──► [ Salt Generator (10 Rounds) ] ──► [ bcrypt.hash ] ──► Encrypted Hash string
```

### bcrypt overview & Verification
Hum production me **`bcrypt`** ya **`bcryptjs`** library use karte hain. Isme do main methods hote hain:
1.  **`bcrypt.hash(password, saltRounds)`**: Plain text password ko securely hash karta hai.
2.  **`bcrypt.compare(password, hashedPassword)`**: Plain incoming password ko database ke saved hash se cryptographically match karta hai bina decrypt kiye.

---

## 4. Advanced Production Security Headers & Threat Vector Hardening

Professional applications me hume direct attacks se bachne ke liye ye methods zaroor follow karne padenge:

### 1. Environment Variables (`.env`)
Database credentials, port numbers, aur JWT keys ko direct code files me nahi, balki `.env` file me surakshit rakha jata hai. Ise `dotenv` module se read kiya jata hai. Never push `.env` to GitHub!

### 2. CORS (Cross-Origin Resource Sharing)
React client (port `5173`) jab Express API (port `5000`) se contact karta hai, toh browser is cross-domain request ko block kar deta hai security ke liye. Hum **`cors`** middleware use karke restricted origins allow karte hain:
```javascript
const cors = require('cors');
app.use(cors({ origin: 'http://localhost:5173', credentials: true })); // Safe production setup
```

### 3. Helmet Overview
Helmet ek built-in dynamic middleware collection hai jo Express response ke safe headers ko inject/configure karke website vulnerabilities (jaise clickjacking, MIME sniffing) se bachaata hai. Isme server technology chhupaane ke liye `X-Powered-By` header disable kiya jata hai.

### 4. Rate Limiting Overview
DoS (Denial of Service) aur brute-force attacks se bachne ke liye hum per-IP request limit set karte hain. Hum **`express-rate-limit`** library use karte hain:
```javascript
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }); // Max 100 requests per 15 mins per IP
app.use(limiter);
```

### 5. Input Validation Basics
Hacker direct script tags payload ke roop me bhej sakte hain (SQL Injection, XSS). Validation/Sanitization libraries (jaise `express-validator` ya `zod`) request body ko route controller me jaane se pehle validate karti hain.

### 6. Critical Security Threats (XSS, CSRF, Injection)
*   **XSS (Cross-Site Scripting)**: Attacker web page par malicious JS script inject kar deta hai. Escaping HTML aur safe validation iska mitigation hai.
*   **CSRF (Cross-Site Request Forgery)**: User ke active browser session/cookie ka misuse karke third-party site se state-changing requests trigger karwana. Anti-CSRF tokens aur cookies me `SameSite: strict` header lagana iska solution hai.
*   **Injection (NoSQL/SQL)**: Attacker query strings me parameter objects modify karke database query ko alter karta hai. **Express 5 Rule Alert**: Express 5 me `req.query` strictly read-only getter hai, isliye purane sanitizers jaise `express-mongo-sanitize` jo isme mutate karte the, ab process crash kar dete hain. Hume parameter validation ya ORM/ODM parameter constraints par control rakhna hoga.

---

## 5. Internal Working Execution Flow

Board par is flow ko bilkul dhyan se copy kar lo beta:

```
[React Client] (Fires login request with credentials)
       │
       ▼
[Express Router] (Mounts POST /api/v1/auth/login)
       │
       ▼
[validateLogin Middleware] (Checks input parameters structure)
       │
       ▼
[authController.login] (Queries Mongoose DB for email -> bcrypt.compare())
       │
       ▼
[jwt.sign()] (Generates access token payload with signature key)
       │
       ▼
[Cookie Injector] (Injects cookie: res.cookie('token', token, { httpOnly: true }))
       │
       ▼
[React UI Renders] (User successfully logged in, cookie active!)
```

---

## 6. Coding Examples with Extreme Details

Ab hum 6 beautiful secured production-ready patterns setup karenge beta. Ek-ek code block ko line-by-line deeply dry run karenge.

---

### Beginner Example 1: Password Hashing Server (Safe Store Setup)

#### Why are we building this?
Plain input passwords ko database me hash format me securely encrypt karne ka foundational logic seekhne ke liye hum ek mini user validator design kar rahe hain.

#### Folder Structure:
```text
/beginner-hashing-app
  ├── package.json
  └── server.js
```

#### Complete Code (`server.js`):
```javascript
const express = require("express");
const bcrypt = require("bcryptjs"); // Safe bcrypt module
const app = express();
const PORT = 3000;

app.use(express.json());

// Local dummy array database
const usersRepository = [];

// Route 1: Register (hashing process)
app.post("/api/v1/register", async (req, res) => {
  try {
    const { email, password } = req.body;

    if (!email || !password) {
      return res.status(400).json({ success: false, error: "Email and password are required!" });
    }

    // Encrypting password with 10 salt rounds
    const hashedPassword = await bcrypt.hash(password, 10); // 10 is standard salt strength

    const newUser = {
      id: usersRepository.length + 1,
      email,
      password: hashedPassword // strictly storing hashed value
    };

    usersRepository.push(newUser);

    res.status(201).json({
      success: true,
      message: "User registered securely!",
      data: { id: newUser.id, email: newUser.email } // do NOT return password back!
    });
  } catch (error) {
    res.status(500).json({ success: false, error: "Internal Server Error" });
  }
});

// Route 2: Test Route to verify password comparison
app.post("/api/v1/verify", async (req, res) => {
  const { email, testPassword } = req.body;
  const user = usersRepository.find(u => u.email === email);

  if (!user) {
    return res.status(404).json({ success: false, error: "User not found" });
  }

  // Cryptographically verify input against hash
  const isMatch = await bcrypt.compare(testPassword, user.password); //

  res.status(200).json({
    success: true,
    passwordMatches: isMatch
  });
});

app.listen(PORT, () => console.log(`Hashing server at http://localhost:${PORT}`));
```

#### ASCII Flow Diagram:
```text
POST /register ──► Plain Text ──► bcrypt.hash (10 salt) ──► Saved to Array
POST /verify   ──► Plain Input ──► bcrypt.compare (Saved Hash) ──► Boolean Result
```

#### Terminal Commands:
```bash
npm init -y
npm install express bcryptjs
node --watch server.js
```

#### Expected Test (Postman Output):
*   Request: `POST http://localhost:3000/api/v1/register` with body `{ "email": "test@domain.com", "password": "class_secret" }`
*   Response (201 Created):
    ```json
    { "success": true, "message": "User registered securely!", "data": { "id": 1, "email": "test@domain.com" } }
    ```
*   Verify Correct Password: `POST http://localhost:3000/api/v1/verify` with `{ "email": "test@domain.com", "testPassword": "class_secret" }` -> returns `{ "success": true, "passwordMatches": true }`.

---

### Beginner Example 2: Access Token Generator API (JWT Sign Router)

#### Why are we building this?
Login process ke baad stateless cryptographically verified JWT generate karna aur client ko return karne ke standard mechanism ko samajhne ke liye.

#### Folder Structure:
```text
/beginner-jwt-sign
  ├── package.json
  └── server.js
```

#### Complete Code (`server.js`):
```javascript
const express = require("express");
const jwt = require("jsonwebtoken"); // jsonwebtoken library import
const app = express();
const PORT = 3000;

app.use(express.json());

const JWT_SECRET = "indian_classroom_ultra_secret_key_101"; // signature seed key

app.post("/api/v1/token/generate", (req, res) => {
  const { userId, role } = req.body;

  if (!userId || !role) {
    return res.status(400).json({ success: false, error: "Missing identity constraints" });
  }

  // Sign Token (embed unique constraints & key)
  const token = jwt.sign(
    { userId, role }, // Payload
    JWT_SECRET, // Secret key
    { expiresIn: "1h" } // Expiration safety limit
  );

  res.status(201).json({
    success: true,
    token
  });
});

app.listen(PORT, () => console.log(`JWT Sign router at http://localhost:${PORT}`));
```

#### expected JSON response:
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMDIiLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE3..."
}
```

---

### Beginner Example 3: Express Secure Cookie Setter

#### Why are we building this?
Tokens ko generic response body me na bhejkar cookies me secure HTTP settings ke sath bind karne ke process ko explore karne ke liye.

#### Folder Structure:
```text
/beginner-cookie-app
  ├── package.json
  └── server.js
```

#### Complete Code (`server.js`):
```javascript
const express = require("express");
const cookieParser = require("cookie-parser"); // essential for cookie deserialization
const app = express();
const PORT = 3000;

app.use(express.json());
app.use(cookieParser()); // Register parser global middleware

app.post("/api/v1/cookie/set", (req, res) => {
  const { sessionToken } = req.body;

  // Inject secure Cookie flags
  res.cookie("token", sessionToken, {
    httpOnly: true, // Prevents client-side JS from stealing cookie (blocks XSS hacks!)
    secure: false, // Set TRUE in production only (requires HTTPS connection)
    maxAge: 3600000 // expires in 1 hour
  });

  res.status(200).json({ success: true, message: "HttpOnly secure cookie set successfully!" });
});

app.get("/api/v1/cookie/read", (req, res) => {
  // Read parsed cookies directly via req.cookies
  const userToken = req.cookies.token; 
  res.status(200).json({ success: true, tokenRead: userToken });
});

app.listen(PORT, () => console.log(`Cookie master active at http://localhost:${PORT}`));
```

---

### Intermediate Example 1: Global Rate-Limiter with Helmet Security Headers

#### Why are we building this?
API protection, server header fingerprint removal, aur brute-force shielding setup ko integrated system standard par verify karne ke liye.

#### Folder Structure:
```text
/intermediate-hardening-app
  ├── package.json
  └── server.js
```

#### Complete Code (`server.js`):
```javascript
const express = require("express");
const helmet = require("helmet"); // Security headers inject wrapper
const rateLimit = require("express-rate-limit"); // Brute force protection
const app = express();
const PORT = 3000;

app.use(express.json());

// 1. Enable Helmet (Sets CSP, HSTS, frames options etc.)
app.use(helmet()); 

// 2. Hide Express Fingerprint Header
app.disable("x-powered-by"); 

// 3. Define Rate limiting constraint
const ipLimiter = rateLimit({
  windowMs: 10 * 60 * 1000, // 10 minutes interval window
  max: 5, // Limit each dynamic IP to 5 requests per windowMs interval
  message: {
    success: false,
    error: "Too many requests from this IP! Brute force block activated. Please retry in 10 minutes."
  }
});

// Register limit on specific auth endpoint prefix only
app.use("/api/v1/auth", ipLimiter);

app.post("/api/v1/auth/login", (req, res) => {
  res.status(200).json({ success: true, message: "Valid access gate accessed." });
});

app.listen(PORT, () => console.log(`Secured Hardened Server running on port: ${PORT}`));
```

#### Expected Test (Triggering Rate limit):
Hit `POST http://localhost:3000/api/v1/auth/login` **6 times consecutively**:
Response (429 Too Many Requests):
```json
{
  "success": false,
  "error": "Too many requests from this IP! Brute force block activated. Please retry in 10 minutes."
}
```

---

### Intermediate Example 2: Schema Request Input Validation Middleware

#### Why are we building this?
Controllers ke clean logic division aur raw body streams ke schema filtering ke liye hum input validation layers setup kar rahe hain.

#### Folder Structure:
```text
/intermediate-validation-app
  ├── /middlewares
  │     └── validator.js
  ├── package.json
  └── server.js
```

#### Validation Module (`/middlewares/validator.js`):
```javascript
const { body, validationResult } = require("express-validator"); // input validator

exports.registerValidationRules = [
  body("email").isEmail().withMessage("Provide a valid electronic email address").normalizeEmail(), //
  body("password").isLength({ min: 6 }).withMessage("Password security parameter must be at least 6 characters long"), //
  body("username").trim().notEmpty().withMessage("Username cannot represent empty text strings")
];

exports.validateRequestSchema = (req, res, next) => {
  const errors = validationResult(req); // collect parsed validation exceptions
  if (!errors.isEmpty()) {
    return res.status(400).json({ success: false, errors: errors.array() }); // 400 for Bad Input Validation
  }
  next();
};
```

#### Server Code (`server.js`):
```javascript
const express = require("express");
const { registerValidationRules, validateRequestSchema } = require("./middlewares/validator");
const app = express();

app.use(express.json());

// Inject validation validation rules chain BEFORE controller
app.post("/api/v1/signup", registerValidationRules, validateRequestSchema, (req, res) => {
  res.status(201).json({
    success: true,
    message: "Data payload successfully certified and validated!",
    validatedData: req.body
  });
});

app.listen(3000, () => console.log("Validated schema server at 3000"));
```

---

### Real Project Example: Multi-Role Secure Spotify Backend REST Portal (With JWT cookies & complete pipeline flow)

#### Why are we building this?
Beta, ab hum ek bilkul complete, industry-standard professional **Spotify Backend Controller Portal** setup karenge. Isme:
1. Users register aur login kar sakte hain with encrypted bcrypt passwords.
2. Auth details httpOnly secure cookies me save hongi.
3. Ek global **`authenticateUser`** verification middleware setup kiya jayega jo token verify karega.
4. Ek role-based authorization filter check middleware **`authorizeArtist`** lagaya jayega taaki sirf Artist accounts hi dynamic tracks create kar sakein, normal Users ko strict `403 Forbidden` milega.

#### Project Folder Structure:
```text
/spotify-secure-portal
  ├── /config
  │     └── env.js
  ├── /middlewares
  │     └── auth.middleware.js
  ├── /controllers
  │     └── music.controller.js
  ├── /routes
  │     └── music.routes.js
  ├── package.json
  └── server.js
```

#### 1. Configuration Profile (`config/env.js`):
```javascript
module.exports = {
  JWT_SECRET: process.env.JWT_SECRET || "deep_spotify_classroom_cryptographic_private_key_2026",
  PORT: process.env.PORT || 5000
};
```

#### 2. Authentication & Role Authorization Middleware (`middlewares/auth.middleware.js`):
```javascript
const jwt = require("jsonwebtoken");
const { JWT_SECRET } = require("../config/env");

// 1. Pehchan Checker (Authentication)
const authenticateUser = (req, res, next) => {
  const token = req.cookies.token; // Extract from secure cookies store

  if (!token) {
    return res.status(401).json({ success: false, error: "Access Denied: Missing session authentication token." }); // 401 for Pehchan Missing
  }

  try {
    // Decode and verify token cryptographic signature
    const decodedPayload = jwt.verify(token, JWT_SECRET); //
    
    // Inject identity details into request object for controllers downstream
    req.user = decodedPayload; 
    next();
  } catch (err) {
    return res.status(401).json({ success: false, error: "Access Denied: Invalid or expired authorization token." }); //
  }
};

// 2. Haq Checker (Authorization role checking)
const authorizeArtist = (req, res, next) => {
  // Checks if injected identity has artist capability
  if (!req.user || req.user.role !== "artist") {
    return res.status(403).json({ // 403 for Forbidden operational actions
      success: false, 
      error: "Forbidden Resource: You do not have permissions/credentials to upload digital tracks." //
    });
  }
  next();
};

module.exports = { authenticateUser, authorizeArtist };
```

#### 3. Controller Actions (`controllers/music.controller.js`):
```javascript
const songsRepository = [
  { id: 1, title: "Tum Hi Ho", artistId: 102 },
  { id: 2, title: "Kesariya", artistId: 102 }
];

exports.fetchActiveMusicTracks = (req, res) => {
  // Anyone authenticated can access the playlist!
  res.status(200).json({ success: true, count: songsRepository.length, tracks: songsRepository });
};

exports.publishArtistTrack = (req, res) => {
  const { title } = req.body;

  if (!title) {
    return res.status(400).json({ success: false, error: "Song title is a mandatory parameter" });
  }

  const newSong = {
    id: songsRepository.length + 1,
    title,
    artistId: req.user.userId // access injected parameters safely!
  };

  songsRepository.push(newSong);

  res.status(201).json({
    success: true,
    message: "Song published to Spotify Catalog!",
    data: newSong
  });
};
```

#### 4. Secured Routes mapping (`routes/music.routes.js`):
```javascript
const express = require("express");
const router = express.Router();
const musicController = require("../controllers/music.controller");
const { authenticateUser, authorizeArtist } = require("../middlewares/auth.middleware");

// Unified routing mapping with chaining
router.route("/tracks")
  .get(authenticateUser, musicController.fetchActiveMusicTracks) // public playlist query
  .post(authenticateUser, authorizeArtist, musicController.publishArtistTrack); // secure upload endpoint

module.exports = router;
```

#### 5. Server Entry file (`server.js`):
```javascript
const express = require("express");
const cookieParser = require("cookie-parser"); // essential for jwt cookies reads
const cors = require("cors"); // secure connection rules
const bcrypt = require("bcryptjs"); // password verification
const jwt = require("jsonwebtoken"); // stateless authentication tickets
const { PORT, JWT_SECRET } = require("./config/env");
const musicRouter = require("./routes/music.routes");

const app = express();

app.use(express.json());
app.use(cookieParser()); // cookies middleware injection
app.use(cors({ origin: "http://localhost:5173", credentials: true })); // Safe local origin connection config

// Mock databases
const usersDb = [
  { id: 101, email: "user@domain.com", password: "", role: "user" }, // User role
  { id: 102, email: "artist@domain.com", password: "", role: "artist" } // Artist role
];

// Encrypting mock database passwords on bootstrap startup
const encryptPassDb = async () => {
  const hashedPass = await bcrypt.hash("class_secret", 10);
  usersDb.password = hashedPass;
  usersDb.password = hashedPass;
  console.log("Mock users password setup complete securely.");
};
encryptPassDb();

// Login controller to verify credentials and issue cookie JWT ticket
app.post("/api/v1/auth/login", async (req, res) => {
  const { email, password } = req.body;
  const user = usersDb.find(u => u.email === email);

  if (!user) {
    return res.status(401).json({ success: false, error: "Invalid email or password parameter configurations" }); //
  }

  const isMatch = await bcrypt.compare(password, user.password); //
  if (!isMatch) {
    return res.status(401).json({ success: false, error: "Invalid email or password parameter configurations" }); //
  }

  // Inject userId and role inside secure payload token
  const token = jwt.sign({ userId: user.id, role: user.role }, JWT_SECRET, { expiresIn: "1d" }); //

  res.cookie("token", token, { httpOnly: true, secure: false, maxAge: 86400000 }); // issue httpOnly cookie

  res.status(200).json({
    success: true,
    message: "Logged in successfully to Spotify Secure Hub!",
    role: user.role
  });
});

app.use("/api/v1", musicRouter);

app.listen(PORT, () => console.log(`Spotify Secured Gateway running on: http://localhost:${PORT}`));
```

#### ASCII Architecture Flow (Role checking dry-run):
```text
React Client POST /tracks ──► [cookieParser()] ──► [authenticateUser] ──► [authorizeArtist] ──► Controller
                                                       │                        │
                                            (No Cookie?) ──► 401       (role !== 'artist') ──► 403 Forbidden
```

#### Expected Test (Access Denied):
1.  Login as normal User: `POST http://localhost:5000/api/v1/auth/login` with credentials `{"email":"user@domain.com", "password":"class_secret"}`. Status `200` received, cookie active!
2.  Try uploading dynamic track: `POST http://localhost:5000/api/v1/tracks` with body `{"title": "My New Track"}`.
3.  Response JSON **(403 Forbidden)**:
    ```json
    {
      "success": false,
      "error": "Forbidden Resource: You do not have permissions/credentials to upload digital tracks."
    }
    ```

---

## 7. MERN Stack Connection & Database Overview

Chalo beta, ab is Chapter 5 ke request-response cycle ko React aur MongoDB ke sath connect karke dekhte hain ki real-world me asali connections kaise baithte hain:

### React Client Side Flow (The View):
React, single page application (SPA) me Axios connection setup karegi jisme **`withCredentials: true`** flag lagana mandatory hai, taaki browser secure cookies ko automatic backend par send kare:
```javascript
// React client integration signup request
import axios from 'axios';

const uploadNewSpotifyTrack = async (trackTitle) => {
  try {
    const res = await axios.post(
      "http://localhost:5000/api/v1/tracks", 
      { title: trackTitle }, 
      { withCredentials: true } // Mandatory to pass HttpOnly cookie tokens!
    );
    alert(res.data.message);
  } catch (err) {
    alert(err.response.data.error); // Alerts XSS security warning or Forbidden messages
  }
};
```

### Future MongoDB Schema User Model integration (The Model):
Mongoose integration schema setups `/models/user.model.js` me direct kuch is tarah map honge:
```javascript
const mongoose = require("mongoose");

const UserSchema = new mongoose.Schema({
  email: { type: String, required: true, unique: true }, //
  password: { type: String, required: true }, // Encrypted hash
  role: { type: String, enum: ["user", "artist", "admin"], default: "user" } // Role check constraints
});

module.exports = mongoose.model("User", UserSchema); // Compiled executable model
```

---

## 8. Troubleshooting Common Mistakes & Debugging

Suno beta, security validation setups me beginners aksar in areas me galti karke phase rehte hain, dhyan rakhna:

1.  **Forgetting to call withCredentials on Axios Frontend**
    *   *Problem*: Express server par token check validation hamesha fail hoti hai aur `401 Unauthorized` aata hai, bhale hi login ho gaya ho.
    *   *Solution*: Ensure React Axios requests include `{ withCredentials: true }` so browser forwards cookies.
2.  **Using express-mongo-sanitize in Express 5**
    *   *Problem*: Server startup par crash ho jata hai ya query check exceptions throw karta hai.
    *   *Solution*: Express 5 me `req.query` read-only getter ban chuka hai. `express-mongo-sanitize` use mutate karne ka try karta hai isliye crash ho jata hai. Sanitize parameter validation query checks manual variables separation se karo, query object mutation mat karo.
3.  **Hiding the Salt Rounds Inside Bcrypt**
    *   *Problem*: Password matching verification slow ho jati hai.
    *   *Solution*: Salt rounds standard strength hamesha `10` to `12` hi rakho.

---

## 9. Security Best Practices & Checklist

*   **Enforce strict HTTPS connections only**: Use TLS certificate layers in production to secure raw TCP packets.
*   **Disable application stack traces leaks**: Protect internal directories and routes path leak by setting `NODE_ENV=production`.
*   **Never trust client input validation**: Always write server-side validation layers as last defense line.
*   **Avoid wildcard CORS origins**: Do NOT use `*` inside production, configure specific origin domains instead.
*   **Disable X-Powered-By Headers**: Reduce application fingerprinting vector to stop hacker search.

---

## 10. Technical Interview Q&A (Chapter 5 Master Prep)

### Question 1: What is the primary difference between Stateless JWT Token Authentication and Stateful Session Authentication? Which is better and why?
*   **Professional English Answer (for Interview):**
    > "Stateful session authentication requires the server to maintain session states globally, typically in RAM or a database cache. When a client authenticates, the server generates a unique Session ID, stores the active state, and issues the ID via a cookie. For every incoming request, the server must query its cache to authorize the user, which limits overall horizontal scalability. 
    > 
    > In contrast, stateless JWT Token Authentication offloads session persistence entirely. The server cryptographically signs a self-contained JSON Web Token containing non-sensitive identity metadata (like user ID and role) using a private secret key. The server does not store anything about the client between requests. When a request arrives, the server simply verifies the signature using the secret key. JWT is highly preferred for distributed systems and decoupled architectures like MERN stack applications because it eliminates database-lookup bottlenecks and simplifies scaling across multi-server cloud clusters."
*   **Easy Hinglish Explanation:**
    > "Suno beta: stateful session-authentication me **server ko active users ka data apne paas RAM ya database me yaad rakhna padta hai**. Jab client Session ID bhejta hai, toh server memory me check karta hai. Agar millions of active users ho jayein, toh server crash ya slow ho jayega.
    > 
    > Wahi **stateless JWT-authentication me server kuch bhi yaad nahi rakhta**. Server simply ek signed card (Token) user ko de deta hai jisme user details encoded hoti hain. Jab bhi request aati hai, server database lookup nahi karta, sirf token ka dynamic digital signature verification check karta hai. Isse servers scale karna behad aasan ho jata hai."

---

### Question 2: Why are HTTP cookies marked with `HttpOnly` and `Secure` flags in production, and how do they mitigate specific attack vectors?
*   **Professional English Answer (for Interview):**
    > "In a secure authentication architecture, storing tokens directly inside browser storage like LocalStorage or SessionStorage exposes them to Cross-Site Scripting (XSS) attacks, where malicious injected JavaScript can programmatically read and hijack the authentication tokens. 
    > 
    > To mitigate this risk, tokens are issued inside HTTP cookies configured with the `HttpOnly` and `Secure` options. The `HttpOnly` flag restricts all client-side JavaScript access to the cookie, ensuring that scripts cannot read the session token even during a successful XSS exploit. The `Secure` flag instructs the browser to transmit the cookie only over encrypted HTTPS channels, protecting the authentication stream from interception via Man-in-the-Middle (MitM) attacks over unencrypted networks."
*   **Easy Hinglish Explanation:**
    > "Beta, dhyan se suno: agar tum JWT token ko browser ki LocalStorage me save karoge, toh koi bhi hacker tumhaari site par chota sa JS script (XSS) daal kar use easily chura lega.
    > 
    > Is dukh-dard se bachne ke liye hum token ko cookie ke through bhejte hain jisme **`HttpOnly`** flag laga hota hai. Is flag se client-side ka koi bhi JavaScript code cookie ko read nahi kar sakta. Aur **`Secure`** flag ye ensure karta hai ki cookie sirf encrypted HTTPS connection par hi travel kare, unencrypted HTTP par block ho jaye taaki koi network data leak na ho."

---

### Question 3: What is the architectural role of `req.query` being read-only in Express 5, and how does this affect security sanitization?
*   **Professional English Answer (for Interview):**
    > "In Express 4, the `req.query` object was fully writable, allowing dynamic middleware and request-sanitizers (such as `express-mongo-sanitize`) to mutate parsed query properties directly. However, this exposed applications to prototype pollution and parameter manipulation exploits. 
    > 
    > In Express 5, `req.query` is implemented as an immutable getter. Consequently, any security middleware attempting to modify or sanitize query structures by writing properties directly to `req.query` will fail or trigger exceptions. Modern sanitization must be offloaded to the data access layer, parameterized query bindings, or handled via structured validation engines like Zod or custom object clones, thereby preserving the immutability of client-side transport inputs."
*   **Easy Hinglish Explanation:**
    > "Suno beta: Express 4 me query parameters mutable hote the, jisse attackers prototype chain ko pollute kar ke server bypass kar dete the. 
    > 
    > **Express 5 ne `req.query` ko complete read-only bana diya hai**. Ab tum query objects ko direct mutate ya alter nahi kar sakte. Purane sanitization packages ab seedha exception throw kar ke server start nahi hone denge. Is security threat se bachne ke liye, sanitization ab direct controller me ya data bindings me validation engine ke zariye clone object banna kar ki jati hai."

---

## 11. Cheat Sheet: Chapter 5 Quick Revision

| Command / Method / Dependency | Type / Namespace | Operational Security Target |
| :--- | :--- | :--- |
| `bcrypt.hash(pass, rounds)` | Password Hashing | Plain text passwords ko safely cryptographically secure hash me convert karta hai. |
| `bcrypt.compare(pass, hash)` | Password Matcher | Input dynamic characters matching check comparison execute karta hai database stored hash se. |
| `jwt.sign(payload, secret)` | Token Issuance | Identity payload parameters se stateless cryptographic token generate karta hai. |
| `jwt.verify(token, secret)` | Token Validator | Signature match verification process trigger karta hai, decode details return karta hai. |
| `helmet()` | Security Headers | Security options configure/inject karke application hijacking vectors ko block karta hai. |
| `rateLimit({ max: 100 })` | Brute-force Shield | Server endpoints ko flooding DDoS aur brute-force automation attacks se safe rakhta hai. |

---

## 12. Mini Assignment (Khud se karke dekho beta!)

**Objective**: Ek complete **Indian Secure Track-Management API** design karo jisme:
1. Modular routing prefixes strictly dynamic aur segregated hon.
2. Custom Login Endpoint database arrays verify kare aur custom HttpOnly dynamic token inject kare status `200` ke sath.
3. Custom Auth Middleware incoming cookies tokens reads verify kare.
4. Route endpoints verification block validate kare: agar user authenticated role `admin` ya `artist` na ho, toh operational status response centrally error validations gateway `403 Forbidden` trigger kare.

---

## 13. Chapter Revision (Quick Summary)

Beta, aaj ke lecture me humne deeply seekha ki:
* **Authentication** user identification validation state establish karta hai.
* **Authorization** users dynamic action authorization role-based permissions standard check validation layers lagata hai.
* **JWT stateless patterns** horizontal servers deployments scaling simplify karte hain.
* **Helmet, CORS, rate-limiting** networks endpoints verification ko hardened aur secure bachaate hain.

