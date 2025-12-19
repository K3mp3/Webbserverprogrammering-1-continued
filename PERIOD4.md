# Period 4: Frontend Integration med Vite

**Vecka 10-12 | 6 lektioner**

---

## 📋 Innehåll

### Vecka 10: Vite & Frontend Setup
- **Lektion 1:** Vite setup och kommunikation med backend
- **Lektion 2:** Sign Up formulär med validering

### Vecka 11: Autentisering i Frontend
- **Lektion 1:** Login formulär och localStorage
- **Lektion 2:** Protected routes och user state

### Vecka 12: Session Management
- **Lektion 1:** Logout och token management
- **Lektion 2:** User profile och error handling

---

## 🎓 Lärandemål - Period 4

Efter denna period kan du:
- ✅ Sätta upp Vite för frontend-utveckling
- ✅ Kommunicera med backend via fetch API
- ✅ Bygga sign up och login formulär
- ✅ Hantera JWT tokens i localStorage
- ✅ Implementera skyddade routes
- ✅ Hantera user state i frontend
- ✅ Logga ut användare korrekt
- ✅ Visa lämpliga felmeddelanden
- ✅ Förstå separation mellan frontend och backend

---

# 📚 Vecka 10 - Lektion 1: Vite Setup & Backend Communication (60 min)

## 🎓 Lärandemål
- Förstå varför vi använder Vite
- Sätta upp Vite projekt
- Konfigurera CORS korrekt
- Göra första fetch request till backend

---

## 📖 Teori: Varför Vite? (10 min)

### Frontend vs Backend

**Backend (Express):**
- Servern som hanterar logik och data
- MongoDB för datalagring
- API endpoints (`/auth/signup`, `/auth/login`)
- Port: 3000

**Frontend (Vite):**
- Det användaren ser och interagerar med
- HTML, CSS, JavaScript
- Gör fetch requests till backend
- Port: 5173

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│   Browser   │ ──────> │   Vite      │ ──────> │   Express   │
│             │         │  (5173)     │         │   (3000)    │
│  HTML/CSS/  │ <────── │             │ <────── │             │
│     JS      │         │  Frontend   │         │   Backend   │
└─────────────┘         └─────────────┘         └─────────────┘
                                                        │
                                                        v
                                                 ┌─────────────┐
                                                 │   MongoDB   │
                                                 │    Atlas    │
                                                 └─────────────┘
```

### Varför Vite?

**Problem utan Vite:**
- Måste refresha manuellt efter varje ändring
- Ingen module bundling
- Ingen dev server
- Svårt att använda moderna JS features

**Med Vite:**
- ⚡ Extremt snabb hot reload (sparar tid!)
- 📦 Automatisk module bundling
- 🔧 Dev server med HTTPS support
- 🚀 Optimerad production build
- 💡 Bra developer experience

---

## ✏️ Övning 16: Vite Setup (50 min)

### Steg 1: Skapa Vite projekt (10 min)

**I Terminal (i GitHub-mappen):**

```bash
# Navigera till GitHub-mappen
cd C:\Users\DittAnvändarnamn\Documents\GitHub

# Skapa Vite projekt
npm create vite@latest auth-frontend
```

**Prompter:**
1. Select a framework: **Vanilla**
2. Select a variant: **JavaScript**

```bash
# Gå in i projektet
cd auth-frontend

# Installera dependencies
npm install

# Starta dev server
npm run dev
```

**Du ska se:**
```
VITE v5.x.x  ready in XXX ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

Öppna `http://localhost:5173/` i browser → Du ska se Vite welcome page!

---

### Steg 2: Git setup (5 min)

**VIKTIGT:** Initiera Git DIREKT!

```bash
# I auth-frontend mappen
git init

# Skapa .gitignore (Vite skapar redan en!)
# Kontrollera att den innehåller:
# node_modules/
# dist/

# Första commit
git add .
git commit -m "Initial Vite setup"
```

**Skapa GitHub repo:**
1. https://github.com → New repository
2. Name: `auth-frontend`
3. Private
4. VÄLJ INTE "Add README"
5. Create

```bash
git remote add origin https://github.com/DittAnvändarnamn/auth-frontend.git
git branch -M main
git push -u origin main
```

---

### Steg 3: Projektstruktur (10 min)

**Rensa default innehåll:**

**1. Ta bort:**
- `counter.js`
- `javascript.svg`
- Allt innehåll i `style.css`

**2. Uppdatera `index.html`:**

```html
<!DOCTYPE html>
<html lang="sv">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Auth System</title>
  </head>
  <body>
    <div id="app">
      <h1>Auth System</h1>
      <p>Loading...</p>
    </div>
    <script type="module" src="/main.js"></script>
  </body>
</html>
```

**Förklaring:**
```html
<script type="module" src="/main.js"></script>
```
- Laddar main.js som ES6 module
- Vite hanterar alla imports automatiskt

**3. Uppdatera `main.js`:**

```javascript
import './style.css'

// Test att det fungerar
document.querySelector('#app').innerHTML = `
  <div>
    <h1>Auth System</h1>
    <p>Frontend fungerar!</p>
  </div>
`;

console.log('Frontend loaded!');
```

**4. Lägg till basic CSS i `style.css`:**

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: #f5f5f5;
  padding: 20px;
}

#app {
  max-width: 800px;
  margin: 0 auto;
  background: white;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h1 {
  color: #333;
  margin-bottom: 20px;
}

button {
  background: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

button:hover {
  background: #0056b3;
}

input {
  width: 100%;
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.error {
  color: #dc3545;
  margin: 10px 0;
}

.success {
  color: #28a745;
  margin: 10px 0;
}
```

Kolla browser → Du ska se "Frontend fungerar!"

---

### Steg 4: Konfigurera backend för CORS (10 min)

**Öppna ditt auth-api projekt (backend)**

**I `app.mjs`, uppdatera CORS:**

```javascript
import cors from 'cors';

const app = express();

// Konfigurera CORS för Vite
app.use(cors({
  origin: 'http://localhost:5173', // Vite dev server
  credentials: true
}));

// Resten av middleware...
app.use(express.json());
```

**Varför?**
- Browser blockerar requests mellan olika portar (CORS policy)
- Backend (3000) ≠ Frontend (5173)
- Vi måste explicit tillåta Vite att göra requests

**Starta backend:**
```bash
# I auth-api mappen
npm run dev
```

Backend ska köra på port 3000 ✅

---

### Steg 5: Första fetch request (15 min)

**I frontend `main.js`:**

```javascript
import './style.css'

// Backend URL
const API_URL = 'http://localhost:3000';

// Test fetch
async function testConnection() {
  try {
    // Förutsatt att du har en GET /auth/test endpoint i backend
    const response = await fetch(`${API_URL}/auth/test`);
    const data = await response.json();
    
    console.log('Backend response:', data);
    
    document.querySelector('#app').innerHTML = `
      <div>
        <h1>Auth System</h1>
        <p>✅ Backend connection successful!</p>
        <pre>${JSON.stringify(data, null, 2)}</pre>
      </div>
    `;
  } catch (error) {
    console.error('Connection error:', error);
    
    document.querySelector('#app').innerHTML = `
      <div>
        <h1>Auth System</h1>
        <p class="error">❌ Cannot connect to backend</p>
        <p>Error: ${error.message}</p>
      </div>
    `;
  }
}

// Kör test när sidan laddas
testConnection();
```

**Lägg till test endpoint i backend (`app.mjs`):**

```javascript
// Test endpoint
app.get('/auth/test', (req, res) => {
  res.json({
    success: true,
    message: 'Backend is working!',
    timestamp: new Date().toISOString()
  });
});
```

**Testa:**
1. Backend körs på port 3000 ✅
2. Frontend körs på port 5173 ✅
3. Öppna browser: http://localhost:5173
4. Du ska se: "✅ Backend connection successful!"
5. Check browser console: "Backend response: {...}"

---

### ✅ Kontrollera:
- [ ] Vite projekt skapat och fungerar
- [ ] Git initerat och pushat till GitHub
- [ ] Backend körs på port 3000
- [ ] Frontend körs på port 5173
- [ ] CORS konfigurerat i backend
- [ ] Fetch request fungerar
- [ ] Data visas i frontend

---

### ❓ Felsökning

**Problem: CORS error i browser console**
```
Access to fetch at 'http://localhost:3000' from origin 'http://localhost:5173' 
has been blocked by CORS policy
```

**Lösning:**
1. Kolla att backend har `cors()` middleware
2. Kolla att `origin: 'http://localhost:5173'` är rätt
3. Starta om backend

**Problem: "Cannot connect to backend"**

**Lösning:**
1. Är backend igång? Check terminal
2. Rätt port (3000)?
3. Rätt URL i frontend (`http://localhost:3000`)?

**Problem: "Failed to fetch"**

**Lösning:**
1. Backend crashad? Check backend terminal
2. Endpoint finns? (GET /auth/test)

---

### 📝 Commit

```bash
# I auth-frontend
git add .
git commit -m "Add API connection and test endpoint"
git push
```

---

## 🏠 Hemuppgift (till nästa lektion)

**Uppgift:** Läs på om localStorage

**Teori att läsa:**
- localStorage lagrar data i browsern
- Data finns kvar även efter browser stängs
- Perfekt för JWT tokens

**Exempel:**
```javascript
// Spara
localStorage.setItem('token', 'abc123');

// Hämta
const token = localStorage.getItem('token');

// Ta bort
localStorage.removeItem('token');

// Kolla om finns
if (localStorage.getItem('token')) {
  console.log('User is logged in');
}
```

**Testa i browser console:**
1. Öppna http://localhost:5173
2. Tryck F12 → Console
3. Skriv: `localStorage.setItem('test', 'hello')`
4. Refresh sidan
5. Skriv: `localStorage.getItem('test')`
6. Du ska se: "hello"

---

# 📚 Vecka 10 - Lektion 2: Sign Up Formulär (60 min)

## 🎓 Lärandemål
- Bygga HTML formulär
- Validera input i frontend
- Skicka POST request till backend
- Hantera success/error responses
- Visa feedback till användaren

---

## 📖 Teori: Frontend Validering (5 min)

### Varför validera i frontend OCH backend?

**Frontend validering:**
- ✅ Snabb feedback till användaren
- ✅ Bättre UX (user experience)
- ✅ Minskar onödiga backend requests
- ❌ Kan inte litas på (användare kan skippa)

**Backend validering:**
- ✅ Säkerhet (kan inte skippas)
- ✅ Måste ALLTID finnas
- ❌ Långsammare feedback

**Bästa approach:** Båda!
1. Frontend: snabb feedback
2. Backend: säkerhet och validation

---

## ✏️ Övning 17: Sign Up Formulär (55 min)

### Steg 1: HTML struktur (10 min)

**Uppdatera `main.js`:**

```javascript
import './style.css';

const API_URL = 'http://localhost:3000';

// Render sign up form
function renderSignupForm() {
  document.querySelector('#app').innerHTML = `
    <div class="auth-container">
      <h1>Skapa konto</h1>
      
      <form id="signup-form">
        <div class="form-group">
          <label for="username">Användarnamn</label>
          <input 
            type="text" 
            id="username" 
            name="username" 
            placeholder="Minst 3 tecken"
            required
          />
        </div>
        
        <div class="form-group">
          <label for="email">Email</label>
          <input 
            type="email" 
            id="email" 
            name="email" 
            placeholder="din@email.com"
            required
          />
        </div>
        
        <div class="form-group">
          <label for="password">Lösenord</label>
          <input 
            type="password" 
            id="password" 
            name="password" 
            placeholder="Minst 6 tecken"
            required
          />
        </div>
        
        <div class="form-group">
          <label for="confirm-password">Bekräfta lösenord</label>
          <input 
            type="password" 
            id="confirm-password" 
            name="confirmPassword" 
            placeholder="Upprepa lösenord"
            required
          />
        </div>
        
        <div id="error-message" class="error"></div>
        <div id="success-message" class="success"></div>
        
        <button type="submit">Skapa konto</button>
      </form>
      
      <p class="link-text">
        Har redan konto? <a href="#" id="login-link">Logga in här</a>
      </p>
    </div>
  `;
  
  // Attach event listener
  document.querySelector('#signup-form').addEventListener('submit', handleSignup);
}

// Kör när sidan laddas
renderSignupForm();
```

**Lägg till CSS i `style.css`:**

```css
.auth-container {
  max-width: 400px;
  margin: 50px auto;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #333;
}

input[type="text"],
input[type="email"],
input[type="password"] {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
  transition: border-color 0.3s;
}

input:focus {
  outline: none;
  border-color: #007bff;
}

button[type="submit"] {
  width: 100%;
  margin-top: 10px;
}

.link-text {
  text-align: center;
  margin-top: 20px;
  color: #666;
}

.link-text a {
  color: #007bff;
  text-decoration: none;
}

.link-text a:hover {
  text-decoration: underline;
}
```

Kolla browser → Du ska se formuläret!

---

### Steg 2: Frontend validering (15 min)

**Lägg till i `main.js`:**

```javascript
// Validering helpers
function validateUsername(username) {
  if (username.length < 3) {
    return 'Användarnamn måste vara minst 3 tecken';
  }
  if (username.length > 20) {
    return 'Användarnamn kan max vara 20 tecken';
  }
  if (!/^[a-zA-Z0-9_]+$/.test(username)) {
    return 'Användarnamn kan bara innehålla bokstäver, siffror och underscore';
  }
  return null; // No error
}

function validateEmail(email) {
  const emailRegex = /^\S+@\S+\.\S+$/;
  if (!emailRegex.test(email)) {
    return 'Ogiltig email-adress';
  }
  return null;
}

function validatePassword(password) {
  if (password.length < 6) {
    return 'Lösenord måste vara minst 6 tecken';
  }
  return null;
}

// Show error message
function showError(message) {
  const errorDiv = document.querySelector('#error-message');
  const successDiv = document.querySelector('#success-message');
  
  errorDiv.textContent = message;
  successDiv.textContent = '';
}

// Show success message
function showSuccess(message) {
  const errorDiv = document.querySelector('#error-message');
  const successDiv = document.querySelector('#success-message');
  
  errorDiv.textContent = '';
  successDiv.textContent = message;
}

// Clear messages
function clearMessages() {
  document.querySelector('#error-message').textContent = '';
  document.querySelector('#success-message').textContent = '';
}
```

---

### Steg 3: Handle submit (20 min)

**Lägg till i `main.js`:**

```javascript
async function handleSignup(event) {
  // Prevent default form submission (skulle refresha sidan)
  event.preventDefault();
  
  // Clear previous messages
  clearMessages();
  
  // Get form values
  const username = document.querySelector('#username').value.trim();
  const email = document.querySelector('#email').value.trim();
  const password = document.querySelector('#password').value;
  const confirmPassword = document.querySelector('#confirm-password').value;
  
  // Frontend validation
  const usernameError = validateUsername(username);
  if (usernameError) {
    showError(usernameError);
    return;
  }
  
  const emailError = validateEmail(email);
  if (emailError) {
    showError(emailError);
    return;
  }
  
  const passwordError = validatePassword(password);
  if (passwordError) {
    showError(passwordError);
    return;
  }
  
  // Check password match
  if (password !== confirmPassword) {
    showError('Lösenorden matchar inte');
    return;
  }
  
  // Disable button during request
  const submitButton = document.querySelector('button[type="submit"]');
  submitButton.disabled = true;
  submitButton.textContent = 'Skapar konto...';
  
  try {
    // Send request to backend
    const response = await fetch(`${API_URL}/auth/signup`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        username,
        email,
        password
      })
    });
    
    const data = await response.json();
    
    if (response.ok) {
      // Success!
      showSuccess('Konto skapat! Loggar in...');
      
      // Save token to localStorage
      localStorage.setItem('token', data.token);
      localStorage.setItem('user', JSON.stringify(data.user));
      
      // Redirect to dashboard (later)
      setTimeout(() => {
        console.log('Redirecting to dashboard...');
        showSuccess('Inloggad! (Dashboard kommer i nästa lektion)');
      }, 1500);
      
    } else {
      // Backend validation error
      showError(data.message || 'Ett fel uppstod');
    }
    
  } catch (error) {
    console.error('Signup error:', error);
    showError('Kunde inte ansluta till servern');
  } finally {
    // Re-enable button
    submitButton.disabled = false;
    submitButton.textContent = 'Skapa konto';
  }
}
```

**Förklaring:**

```javascript
event.preventDefault();
```
Förhindrar att formuläret skickas "traditionellt" (som skulle refresha sidan)

```javascript
const username = document.querySelector('#username').value.trim();
```
`.trim()` tar bort whitespace i början/slutet

```javascript
submitButton.disabled = true;
```
Disable knappen under request (förhindrar dubbla clicks)

```javascript
headers: {
  'Content-Type': 'application/json'
}
```
Säger till backend att vi skickar JSON

```javascript
body: JSON.stringify({ username, email, password })
```
Konverterar JavaScript object till JSON string

```javascript
if (response.ok) { ... }
```
`response.ok` = true om status 200-299

```javascript
localStorage.setItem('token', data.token);
```
Sparar JWT token i browser

---

### Steg 4: Testa hela flödet (10 min)

**Test 1: Frontend validering**

Testa att fylla i:
- Username: "ab" → Error: "Användarnamn måste vara minst 3 tecken" ✅
- Email: "invalid" → Error: "Ogiltig email-adress" ✅
- Password: "12345" → Error: "Lösenord måste vara minst 6 tecken" ✅
- Passwords matchar inte → Error: "Lösenorden matchar inte" ✅

**Test 2: Backend validering**

Fyll i giltiga värden:
- Username: "testuser"
- Email: "test@test.com"
- Password: "password123"
- Confirm: "password123"

Tryck "Skapa konto"

**Förväntat:**
1. Knapp visar "Skapar konto..."
2. Success message: "Konto skapat! Loggar in..."
3. Efter 1.5s: "Inloggad! (Dashboard kommer i nästa lektion)"
4. Check localStorage:
   - F12 → Application → Local Storage → http://localhost:5173
   - Du ska se `token` och `user`

**Test 3: Duplicate username**

Testa skapa samma användare igen → Error: "Username already exists" ✅

---

### ✅ Kontrollera:
- [ ] Formulär renderas korrekt
- [ ] Frontend validering fungerar
- [ ] POST request skickas till backend
- [ ] Success message visas
- [ ] Token sparas i localStorage
- [ ] User data sparas i localStorage
- [ ] Error messages visas korrekt
- [ ] Button disabled under request

---

### 📝 Commit

```bash
git add .
git commit -m "Add signup form with validation"
git push
```

---

## 🏠 Hemuppgift (Vecka 10)

**Uppgift 1:** Lägg till password strength indicator

**Exempel:**
```javascript
function getPasswordStrength(password) {
  if (password.length < 6) return 'Svag';
  if (password.length < 10) return 'Medel';
  if (/[A-Z]/.test(password) && /[0-9]/.test(password)) return 'Stark';
  return 'Medel';
}
```

Visa detta under password-fältet med färg:
- Svag: röd
- Medel: orange
- Stark: grön

**Uppgift 2:** Förbättra UX

Lägg till:
- Show/hide password toggle (👁️ icon)
- Disable submit om formulär inte är giltigt
- Loading spinner under request

---

# 📚 Vecka 11 - Lektion 1: Login Formulär (60 min)

## 🎓 Lärandemål
- Bygga login formulär
- Skicka login request
- Hantera JWT token
- Implementera navigation mellan views
- Förstå token-baserad autentisering

---

## 📖 Teori: JWT Token Flow (5 min)

```
1. User fyller i login form
   ↓
2. Frontend: POST /auth/login
   ↓
3. Backend: Validerar credentials
   ↓
4. Backend: Genererar JWT token
   ↓
5. Frontend: Sparar token i localStorage
   ↓
6. Frontend: Inkluderar token i alla requests
   ↓
7. Backend: Verifierar token i middleware
```

**Varför localStorage?**
- Enkelt att använda
- Finns kvar efter browser stängs
- Tillgänglig i all JavaScript kod

**Alternativ:**
- sessionStorage: Raderas när browser stängs
- Cookies: Mer komplext, men säkrare (HTTPOnly)

---

## ✏️ Övning 18: Login Formulär (55 min)

### Steg 1: Routing system (15 min)

**Uppdatera `main.js`:**

```javascript
import './style.css';

const API_URL = 'http://localhost:3000';

// Current view state
let currentView = 'signup';

// Router
function showView(view) {
  currentView = view;
  
  // Check if user is already logged in
  const token = localStorage.getItem('token');
  
  if (token && view !== 'dashboard') {
    // User is logged in, show dashboard
    renderDashboard();
    return;
  }
  
  switch(view) {
    case 'signup':
      renderSignupForm();
      break;
    case 'login':
      renderLoginForm();
      break;
    case 'dashboard':
      renderDashboard();
      break;
    default:
      renderSignupForm();
  }
}

// Check authentication on page load
function checkAuth() {
  const token = localStorage.getItem('token');
  
  if (token) {
    showView('dashboard');
  } else {
    showView('login');
  }
}

// Initialize app
checkAuth();
```

**Förklaring:**

```javascript
let currentView = 'signup';
```
State för vilken view som visas

```javascript
if (token && view !== 'dashboard') {
  renderDashboard();
  return;
}
```
Om user redan är inloggad → visa dashboard automatiskt

```javascript
switch(view) { ... }
```
Enkel router: avgör vilken funktion som ska köras

---

### Steg 2: Login formulär HTML (10 min)

**Lägg till i `main.js`:**

```javascript
function renderLoginForm() {
  document.querySelector('#app').innerHTML = `
    <div class="auth-container">
      <h1>Logga in</h1>
      
      <form id="login-form">
        <div class="form-group">
          <label for="login-email">Email</label>
          <input 
            type="email" 
            id="login-email" 
            name="email" 
            placeholder="din@email.com"
            required
          />
        </div>
        
        <div class="form-group">
          <label for="login-password">Lösenord</label>
          <input 
            type="password" 
            id="login-password" 
            name="password" 
            placeholder="Ditt lösenord"
            required
          />
        </div>
        
        <div id="error-message" class="error"></div>
        <div id="success-message" class="success"></div>
        
        <button type="submit">Logga in</button>
      </form>
      
      <p class="link-text">
        Inget konto? <a href="#" id="signup-link">Skapa konto här</a>
      </p>
    </div>
  `;
  
  // Event listeners
  document.querySelector('#login-form').addEventListener('submit', handleLogin);
  document.querySelector('#signup-link').addEventListener('click', (e) => {
    e.preventDefault();
    showView('signup');
  });
}
```

**Uppdatera `renderSignupForm()` för att lägga till link till login:**

```javascript
function renderSignupForm() {
  document.querySelector('#app').innerHTML = `
    <div class="auth-container">
      <h1>Skapa konto</h1>
      
      <!-- Formulär här (samma som innan) -->
      
      <p class="link-text">
        Har redan konto? <a href="#" id="login-link">Logga in här</a>
      </p>
    </div>
  `;
  
  // Event listeners
  document.querySelector('#signup-form').addEventListener('submit', handleSignup);
  document.querySelector('#login-link').addEventListener('click', (e) => {
    e.preventDefault();
    showView('login');
  });
}
```

Testa klicka på "Logga in här" / "Skapa konto här" → Forms byter!

---

### Steg 3: Login handler (15 min)

**Lägg till i `main.js`:**

```javascript
async function handleLogin(event) {
  event.preventDefault();
  
  clearMessages();
  
  // Get form values
  const email = document.querySelector('#login-email').value.trim();
  const password = document.querySelector('#login-password').value;
  
  // Basic validation
  if (!email || !password) {
    showError('Alla fält måste fyllas i');
    return;
  }
  
  // Disable button
  const submitButton = document.querySelector('button[type="submit"]');
  submitButton.disabled = true;
  submitButton.textContent = 'Loggar in...';
  
  try {
    const response = await fetch(`${API_URL}/auth/login`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ email, password })
    });
    
    const data = await response.json();
    
    if (response.ok) {
      // Success!
      showSuccess('Inloggad!');
      
      // Save token and user data
      localStorage.setItem('token', data.token);
      localStorage.setItem('user', JSON.stringify(data.user));
      
      // Redirect to dashboard
      setTimeout(() => {
        showView('dashboard');
      }, 500);
      
    } else {
      // Error
      showError(data.message || 'Inloggning misslyckades');
    }
    
  } catch (error) {
    console.error('Login error:', error);
    showError('Kunde inte ansluta till servern');
  } finally {
    submitButton.disabled = false;
    submitButton.textContent = 'Logga in';
  }
}
```

---

### Steg 4: Dashboard (15 min)

**Lägg till i `main.js`:**

```javascript
function renderDashboard() {
  // Get user data from localStorage
  const userJson = localStorage.getItem('user');
  
  if (!userJson) {
    // No user data, redirect to login
    showView('login');
    return;
  }
  
  const user = JSON.parse(userJson);
  
  document.querySelector('#app').innerHTML = `
    <div class="dashboard">
      <nav class="navbar">
        <h2>Min Dashboard</h2>
        <button id="logout-btn" class="logout-btn">Logga ut</button>
      </nav>
      
      <div class="welcome-section">
        <h1>Välkommen, ${user.username}!</h1>
        <p class="email">📧 ${user.email}</p>
      </div>
      
      <div class="info-box">
        <h3>✅ Du är inloggad!</h3>
        <p>Detta är en skyddad sida som bara inloggade användare kan se.</p>
        <p>Ditt JWT token är sparat i localStorage.</p>
      </div>
      
      <div class="debug-section">
        <h3>Debug Info</h3>
        <p><strong>User ID:</strong> ${user.id || user._id}</p>
        <p><strong>Role:</strong> ${user.role || 'user'}</p>
        <p><strong>Token:</strong> ${localStorage.getItem('token').substring(0, 20)}...</p>
      </div>
    </div>
  `;
  
  // Logout event listener
  document.querySelector('#logout-btn').addEventListener('click', handleLogout);
}

function handleLogout() {
  // Remove token and user data
  localStorage.removeItem('token');
  localStorage.removeItem('user');
  
  // Redirect to login
  showView('login');
}
```

**Lägg till CSS i `style.css`:**

```css
.dashboard {
  max-width: 800px;
  margin: 0 auto;
}

.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  background: #007bff;
  color: white;
  border-radius: 8px;
  margin-bottom: 30px;
}

.navbar h2 {
  margin: 0;
}

.logout-btn {
  background: white;
  color: #007bff;
  padding: 8px 16px;
  font-size: 14px;
}

.logout-btn:hover {
  background: #f0f0f0;
}

.welcome-section {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 40px;
  border-radius: 8px;
  margin-bottom: 20px;
  text-align: center;
}

.welcome-section h1 {
  margin: 0 0 10px 0;
  color: white;
}

.welcome-section .email {
  font-size: 18px;
  opacity: 0.9;
}

.info-box {
  background: #d4edda;
  border: 1px solid #c3e6cb;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.info-box h3 {
  margin-top: 0;
  color: #155724;
}

.info-box p {
  color: #155724;
  margin: 10px 0;
}

.debug-section {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 8px;
  border: 1px solid #dee2e6;
}

.debug-section h3 {
  margin-top: 0;
}

.debug-section p {
  font-family: 'Courier New', monospace;
  margin: 5px 0;
}
```

---

### Steg 5: Testa hela flödet (10 min)

**Test 1: Logga in med befintlig användare**

1. Gå till http://localhost:5173
2. Du ser login form
3. Fyll i email och lösenord (från signup)
4. Tryck "Logga in"
5. Du ska se dashboard med välkomstmeddelande!

**Test 2: Navigation**

1. Klicka "Inget konto? Skapa konto här"
2. Du ser signup form
3. Klicka "Har redan konto? Logga in här"
4. Du ser login form igen

**Test 3: Logout**

1. Logga in
2. Du ser dashboard
3. Klicka "Logga ut"
4. Du ska komma till login form
5. Check localStorage (F12 → Application):
   - `token` och `user` ska vara borta!

**Test 4: Persistent login**

1. Logga in
2. Refresh sidan (F5)
3. Du ska FORTFARANDE vara inloggad!
4. Dashboard visas automatiskt

**Test 5: Fel inloggning**

1. Försök logga in med fel lösenord
2. Du ska se error: "Invalid credentials"

---

### ✅ Kontrollera:
- [ ] Login formulär fungerar
- [ ] Token sparas vid login
- [ ] Dashboard visas efter login
- [ ] User data visas korrekt
- [ ] Logout fungerar
- [ ] localStorage töms vid logout
- [ ] Navigation mellan views fungerar
- [ ] Persistent login fungerar (refresh sida)

---

### 📝 Commit

```bash
git add .
git commit -m "Add login form and dashboard with authentication"
git push
```

---

# 📚 Vecka 11 - Lektion 2: Protected Routes (60 min)

## 🎓 Lärandemål
- Förstå protected routes
- Skicka JWT token med requests
- Hämta skyddad data från backend
- Hantera unauthorized errors
- Visa user-specific data

---

## 📖 Teori: Protected Routes (5 min)

### Vad är en protected route?

En endpoint som kräver att användaren är inloggad.

**Backend:**
```javascript
// Unprotected (alla kan anropa)
app.get('/public/info', (req, res) => { ... });

// Protected (kräver JWT token)
app.get('/users/profile', requireAuth, (req, res) => { ... });
```

### Hur skickar vi token?

**Authorization header:**
```javascript
fetch(url, {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

**Format:** `Bearer <token>`

**Backend läser detta:**
```javascript
const token = req.headers.authorization.split(' ')[1];
// token = "eyJhbGciOiJIUzI1NiIs..."
```

---

## ✏️ Övning 19: Protected API Calls (55 min)

### Steg 1: Lägg till protected endpoint i backend (10 min)

**I `app.mjs` (backend):**

```javascript
// Get current user profile (protected)
app.get('/auth/me', requireAuth, async (req, res) => {
  try {
    // req.user kommer från requireAuth middleware
    const user = await User.findById(req.user.id).select('-password');
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    res.json({
      success: true,
      user: {
        id: user._id,
        username: user.username,
        email: user.email,
        role: user.role,
        createdAt: user.createdAt
      }
    });
  } catch (error) {
    console.error('Get profile error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

Testa i Postman:

**Utan token:**
```
GET http://localhost:3000/auth/me
```
→ 401 Unauthorized ✅

**Med token:**
```
GET http://localhost:3000/auth/me

Headers:
Authorization: Bearer DITT_JWT_TOKEN_HÄR
```
→ 200 OK med user data ✅

---

### Steg 2: API helper i frontend (15 min)

**Skapa `utils/api.js`:**

```javascript
const API_URL = 'http://localhost:3000';

/**
 * Make authenticated request
 */
export async function apiRequest(endpoint, options = {}) {
  // Get token from localStorage
  const token = localStorage.getItem('token');
  
  // Default headers
  const headers = {
    'Content-Type': 'application/json',
    ...options.headers
  };
  
  // Add Authorization header if token exists
  if (token) {
    headers['Authorization'] = `Bearer ${token}`;
  }
  
  // Make request
  const response = await fetch(`${API_URL}${endpoint}`, {
    ...options,
    headers
  });
  
  // Check if token is invalid/expired
  if (response.status === 401) {
    // Token is invalid, logout user
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    window.location.reload(); // Reload to show login
  }
  
  const data = await response.json();
  
  // Return both response and data
  return {
    ok: response.ok,
    status: response.status,
    data
  };
}

/**
 * Convenience methods
 */
export const api = {
  // GET request
  get: (endpoint) => apiRequest(endpoint, { method: 'GET' }),
  
  // POST request
  post: (endpoint, body) => apiRequest(endpoint, {
    method: 'POST',
    body: JSON.stringify(body)
  }),
  
  // PUT request
  put: (endpoint, body) => apiRequest(endpoint, {
    method: 'PUT',
    body: JSON.stringify(body)
  }),
  
  // DELETE request
  delete: (endpoint) => apiRequest(endpoint, { method: 'DELETE' })
};
```

**Förklaring:**

```javascript
headers['Authorization'] = `Bearer ${token}`;
```
Lägger till token i varje request automatiskt

```javascript
if (response.status === 401) {
  localStorage.removeItem('token');
  window.location.reload();
}
```
Om token är invalid → logga ut automatiskt

```javascript
export const api = { get, post, put, delete };
```
Convenience methods för CRUD operations

---

### Steg 3: Använd API helper i dashboard (15 min)

**Uppdatera `main.js`:**

```javascript
import './style.css';
import { api } from './utils/api.js';

// ... (all previous code)

// Uppdatera renderDashboard
async function renderDashboard() {
  // Show loading first
  document.querySelector('#app').innerHTML = `
    <div class="dashboard">
      <div class="loading">Laddar...</div>
    </div>
  `;
  
  try {
    // Fetch fresh user data from backend
    const { ok, data } = await api.get('/auth/me');
    
    if (!ok) {
      showError('Kunde inte hämta användardata');
      showView('login');
      return;
    }
    
    const user = data.user;
    
    // Render dashboard with user data
    document.querySelector('#app').innerHTML = `
      <div class="dashboard">
        <nav class="navbar">
          <h2>Min Dashboard</h2>
          <button id="logout-btn" class="logout-btn">Logga ut</button>
        </nav>
        
        <div class="welcome-section">
          <h1>Välkommen, ${user.username}!</h1>
          <p class="email">📧 ${user.email}</p>
          <p class="role-badge">${user.role || 'user'}</p>
        </div>
        
        <div class="profile-card">
          <h3>Min profil</h3>
          <div class="profile-info">
            <p><strong>Användarnamn:</strong> ${user.username}</p>
            <p><strong>Email:</strong> ${user.email}</p>
            <p><strong>Roll:</strong> ${user.role || 'user'}</p>
            <p><strong>Medlem sedan:</strong> ${new Date(user.createdAt).toLocaleDateString('sv-SE')}</p>
          </div>
        </div>
        
        <div class="info-box">
          <h3>✅ Detta är skyddad data!</h3>
          <p>Denna data hämtades från en protected route: <code>GET /auth/me</code></p>
          <p>Din JWT token skickades med i Authorization headern.</p>
        </div>
        
        <div class="debug-section">
          <h3>Debug Info</h3>
          <p><strong>User ID:</strong> ${user.id}</p>
          <p><strong>Token:</strong> ${localStorage.getItem('token').substring(0, 30)}...</p>
          <button id="refresh-btn" class="secondary-btn">🔄 Refresh Data</button>
        </div>
      </div>
    `;
    
    // Event listeners
    document.querySelector('#logout-btn').addEventListener('click', handleLogout);
    document.querySelector('#refresh-btn').addEventListener('click', renderDashboard);
    
  } catch (error) {
    console.error('Dashboard error:', error);
    showError('Ett fel uppstod');
    showView('login');
  }
}
```

**Lägg till CSS:**

```css
.loading {
  text-align: center;
  padding: 50px;
  font-size: 18px;
  color: #666;
}

.profile-card {
  background: white;
  border: 1px solid #dee2e6;
  padding: 25px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.profile-card h3 {
  margin-top: 0;
  color: #333;
  border-bottom: 2px solid #007bff;
  padding-bottom: 10px;
}

.profile-info {
  margin-top: 20px;
}

.profile-info p {
  margin: 12px 0;
  font-size: 16px;
}

.profile-info strong {
  color: #666;
  font-weight: 600;
}

.role-badge {
  background: #28a745;
  color: white;
  padding: 5px 15px;
  border-radius: 20px;
  display: inline-block;
  font-size: 14px;
  margin-top: 10px;
}

code {
  background: #f8f9fa;
  padding: 2px 6px;
  border-radius: 3px;
  font-family: 'Courier New', monospace;
  color: #e83e8c;
}

.secondary-btn {
  background: #6c757d;
  color: white;
  margin-top: 10px;
}

.secondary-btn:hover {
  background: #5a6268;
}
```

---

### Steg 4: Testa protected routes (15 min)

**Test 1: Normal flow**

1. Logga in
2. Du ska se dashboard med data från backend
3. Check console → inga errors
4. Klicka "🔄 Refresh Data"
5. Data uppdateras (loading → data)

**Test 2: Expired token**

1. Logga in
2. Öppna browser console
3. Skriv: `localStorage.setItem('token', 'invalid-token')`
4. Klicka "🔄 Refresh Data"
5. Du ska automatiskt loggas ut (redirect till login)

**Test 3: Network errors**

1. Stäng av backend (Ctrl+C i terminal)
2. Klicka "🔄 Refresh Data"
3. Error message visas
4. Starta backend igen
5. Refresh fungerar igen

**Test 4: Check Authorization header**

1. Öppna browser DevTools (F12)
2. Network tab
3. Klicka "🔄 Refresh Data"
4. Klicka på `/auth/me` request
5. Headers tab
6. Leta efter "Authorization: Bearer ..."
7. Token ska skickas med! ✅

---

### ✅ Kontrollera:
- [ ] Protected endpoint fungerar i backend
- [ ] API helper skapad
- [ ] Authorization header skickas automatiskt
- [ ] Dashboard hämtar data från backend
- [ ] Invalid token → automatisk utloggning
- [ ] Refresh knapp fungerar
- [ ] Error handling fungerar
- [ ] Loading state visas

---

### 📝 Commit

```bash
git add .
git commit -m "Add protected routes and API helper"
git push
```

---

# 📚 Vecka 12 - Lektion 1: Logout & Token Management (60 min)

## 🎓 Lärandemål
- Implementera säker logout
- Förstå token expiration
- Hantera token refresh (teori)
- Implementera "Remember me"
- Förbättra error handling

---

## 📖 Teori: Token Management (10 min)

### Token Lifecycle

```
1. Login → Backend generates token (exp: 1h)
2. Frontend stores token in localStorage
3. Token included in all requests
4. After 1h → Token expires
5. Backend returns 401 Unauthorized
6. Frontend detects 401 → Logout user
```

### Säker Logout

**Frontend:**
```javascript
// Ta bort token och user data
localStorage.removeItem('token');
localStorage.removeItem('user');

// Redirect till login
window.location.href = '/login';
```

**Backend (optional):**
Om du vill ha en "token blacklist":
```javascript
// Spara expired tokens i databas
// Nackdel: mer komplext, kräver databas-check varje request
```

**Best practice:**
- Kort token expiration (1-24h)
- Frontend tar bort token vid logout
- Backend verifierar token expiration

### Token Refresh (Advanced)

**Problem:** Token expires → User måste logga in igen

**Lösning:** Refresh tokens
```
Access Token: Kort expiration (1h)
Refresh Token: Lång expiration (30 days)

När access token expires:
1. Frontend skickar refresh token
2. Backend genererar ny access token
3. User förblir inloggad
```

**Vi implementerar INTE detta nu (för avancerat), men bra att veta!**

---

## ✏️ Övning 20: Förbättrad Token Management (50 min)

### Steg 1: Token expiration handling (15 min)

**Uppdatera `utils/api.js`:**

```javascript
const API_URL = 'http://localhost:3000';

/**
 * Handle logout
 */
function logout() {
  localStorage.removeItem('token');
  localStorage.removeItem('user');
  window.location.href = '/';
}

/**
 * Check if token exists
 */
export function isAuthenticated() {
  return !!localStorage.getItem('token');
}

/**
 * Get stored user data
 */
export function getCurrentUser() {
  const userJson = localStorage.getItem('user');
  return userJson ? JSON.parse(userJson) : null;
}

/**
 * Make authenticated request with improved error handling
 */
export async function apiRequest(endpoint, options = {}) {
  const token = localStorage.getItem('token');
  
  const headers = {
    'Content-Type': 'application/json',
    ...options.headers
  };
  
  if (token) {
    headers['Authorization'] = `Bearer ${token}`;
  }
  
  try {
    const response = await fetch(`${API_URL}${endpoint}`, {
      ...options,
      headers
    });
    
    // Handle token expiration/invalid
    if (response.status === 401) {
      console.log('Token invalid or expired, logging out...');
      logout();
      throw new Error('Token expired');
    }
    
    // Handle network errors
    if (!response.ok) {
      const error = await response.json().catch(() => ({
        message: 'Network error'
      }));
      throw new Error(error.message || `HTTP ${response.status}`);
    }
    
    const data = await response.json();
    
    return {
      ok: true,
      status: response.status,
      data
    };
    
  } catch (error) {
    // Re-throw if already handled
    if (error.message === 'Token expired') {
      throw error;
    }
    
    // Network errors
    console.error('API request error:', error);
    throw new Error('Kunde inte ansluta till servern');
  }
}

// Convenience methods (samma som innan)
export const api = {
  get: (endpoint) => apiRequest(endpoint, { method: 'GET' }),
  post: (endpoint, body) => apiRequest(endpoint, {
    method: 'POST',
    body: JSON.stringify(body)
  }),
  put: (endpoint, body) => apiRequest(endpoint, {
    method: 'PUT',
    body: JSON.stringify(body)
  }),
  delete: (endpoint) => apiRequest(endpoint, { method: 'DELETE' })
};
```

---

### Steg 2: Förbättrad logout (10 min)

**Uppdatera `main.js`:**

```javascript
import { api, isAuthenticated, getCurrentUser } from './utils/api.js';

// ... (previous code)

async function handleLogout() {
  // Optional: Call backend logout endpoint
  // await api.post('/auth/logout');
  
  // Clear local storage
  localStorage.removeItem('token');
  localStorage.removeItem('user');
  
  // Show logout message briefly
  document.querySelector('#app').innerHTML = `
    <div class="auth-container">
      <div class="success" style="text-align: center; padding: 50px;">
        <h2>Du har loggats ut</h2>
        <p>Omdirigerar till login...</p>
      </div>
    </div>
  `;
  
  // Redirect to login
  setTimeout(() => {
    showView('login');
  }, 1500);
}
```

---

### Steg 3: Session timeout warning (15 min)

**Lägg till i `main.js`:**

```javascript
// Check token expiration from JWT payload
function getTokenExpiration() {
  const token = localStorage.getItem('token');
  if (!token) return null;
  
  try {
    // JWT format: header.payload.signature
    const payload = token.split('.')[1];
    const decoded = JSON.parse(atob(payload));
    
    // exp is in seconds, convert to milliseconds
    return decoded.exp * 1000;
  } catch (error) {
    console.error('Error decoding token:', error);
    return null;
  }
}

// Start session monitor when user logs in
function startSessionMonitor() {
  // Check every minute
  const checkInterval = setInterval(() => {
    const expiration = getTokenExpiration();
    
    if (!expiration) {
      clearInterval(checkInterval);
      return;
    }
    
    const now = Date.now();
    const timeLeft = expiration - now;
    
    // 5 minutes left
    if (timeLeft < 5 * 60 * 1000 && timeLeft > 0) {
      showSessionWarning(Math.floor(timeLeft / 1000 / 60));
    }
    
    // Expired
    if (timeLeft <= 0) {
      clearInterval(checkInterval);
      handleSessionExpired();
    }
  }, 60000); // Check every minute
}

function showSessionWarning(minutesLeft) {
  // Only show once
  if (document.querySelector('.session-warning')) return;
  
  const warning = document.createElement('div');
  warning.className = 'session-warning';
  warning.innerHTML = `
    <p>⚠️ Din session går ut om ${minutesLeft} minut${minutesLeft !== 1 ? 'er' : ''}.</p>
    <button onclick="location.reload()">Förläng session</button>
  `;
  
  document.body.appendChild(warning);
}

function handleSessionExpired() {
  alert('Din session har gått ut. Du måste logga in igen.');
  handleLogout();
}
```

**Lägg till CSS:**

```css
.session-warning {
  position: fixed;
  top: 20px;
  right: 20px;
  background: #fff3cd;
  border: 1px solid #ffc107;
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  z-index: 1000;
  max-width: 300px;
}

.session-warning p {
  margin: 0 0 10px 0;
  color: #856404;
}

.session-warning button {
  background: #ffc107;
  color: #212529;
  padding: 8px 16px;
  font-size: 14px;
}
```

**Starta monitor efter login:**

```javascript
async function handleLogin(event) {
  // ... (previous code)
  
  if (response.ok) {
    // Save token
    localStorage.setItem('token', data.token);
    localStorage.setItem('user', JSON.stringify(data.user));
    
    // Start session monitor
    startSessionMonitor();
    
    // Redirect
    setTimeout(() => {
      showView('dashboard');
    }, 500);
  }
}
```

---

### Steg 4: "Remember me" funktionalitet (10 min)

**Uppdatera login form:**

```javascript
function renderLoginForm() {
  document.querySelector('#app').innerHTML = `
    <div class="auth-container">
      <h1>Logga in</h1>
      
      <form id="login-form">
        <!-- Email och password fält -->
        
        <div class="form-group checkbox-group">
          <label>
            <input type="checkbox" id="remember-me" name="rememberMe" />
            Håll mig inloggad
          </label>
        </div>
        
        <div id="error-message" class="error"></div>
        
        <button type="submit">Logga in</button>
      </form>
      
      <!-- Link till signup -->
    </div>
  `;
  
  // Event listeners
  document.querySelector('#login-form').addEventListener('submit', handleLogin);
}
```

**CSS:**

```css
.checkbox-group {
  display: flex;
  align-items: center;
}

.checkbox-group label {
  display: flex;
  align-items: center;
  font-weight: normal;
  cursor: pointer;
}

.checkbox-group input[type="checkbox"] {
  width: auto;
  margin-right: 8px;
}
```

**Uppdatera handleLogin:**

```javascript
async function handleLogin(event) {
  event.preventDefault();
  
  // ... (validation)
  
  const rememberMe = document.querySelector('#remember-me').checked;
  
  try {
    const response = await fetch(`${API_URL}/auth/login`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password, rememberMe })
    });
    
    const data = await response.json();
    
    if (response.ok) {
      // Save token
      if (rememberMe) {
        // Long-term storage
        localStorage.setItem('token', data.token);
        localStorage.setItem('user', JSON.stringify(data.user));
        localStorage.setItem('rememberMe', 'true');
      } else {
        // Session storage (cleared when browser closes)
        sessionStorage.setItem('token', data.token);
        sessionStorage.setItem('user', JSON.stringify(data.user));
        // Also keep in localStorage for this session
        localStorage.setItem('token', data.token);
        localStorage.setItem('user', JSON.stringify(data.user));
      }
      
      startSessionMonitor();
      
      setTimeout(() => {
        showView('dashboard');
      }, 500);
    }
  } catch (error) {
    // ... error handling
  }
}
```

**Check auth på page load:**

```javascript
function checkAuth() {
  // Check if "remember me" was enabled
  const rememberMe = localStorage.getItem('rememberMe') === 'true';
  
  // Check both localStorage and sessionStorage
  const token = localStorage.getItem('token') || sessionStorage.getItem('token');
  
  if (token) {
    // Start monitor
    startSessionMonitor();
    
    // Show dashboard
    showView('dashboard');
  } else {
    showView('login');
  }
}
```

---

### Steg 5: Testa token management (5 min)

**Test 1: Normal logout**
1. Logga in
2. Klicka "Logga ut"
3. Du ska se "Du har loggats ut" message
4. Efter 1.5s → redirect till login

**Test 2: Remember me**
1. Logga in MED "Håll mig inloggad"
2. Stäng browser helt
3. Öppna browser igen
4. Gå till http://localhost:5173
5. Du ska FORTFARANDE vara inloggad! ✅

**Test 3: Utan remember me**
1. Logga ut
2. Logga in UTAN "Håll mig inloggad"
3. Stäng browser
4. Öppna browser
5. Du ska INTE vara inloggad ✅

**Test 4: Session warning (svårt att testa)**
Om du vill testa måste du:
1. Ändra JWT expiration i backend till 5 minuter
2. Logga in
3. Vänta 1-2 minuter
4. Du ska se warning

---

### ✅ Kontrollera:
- [ ] Logout fungerar korrekt
- [ ] localStorage töms vid logout
- [ ] Session warning visas (om du testar)
- [ ] Remember me checkbox finns
- [ ] Remember me fungerar (persistent login)
- [ ] Utan remember me → logout vid browser close
- [ ] Token expiration hanteras

---

### 📝 Commit

```bash
git add .
git commit -m "Add improved logout and token management"
git push
```

---

# 📚 Vecka 12 - Lektion 2: Error Handling & Polish (60 min)

## 🎓 Lärandemål
- Implementera robust error handling
- Förbättra UX med loading states
- Validera all user input
- Lägga till user feedback
- Förbereda för Period 5 (Forum projekt)

---

## ✏️ Övning 21: Error Handling & UX Polish (60 min)

### Steg 1: Global error handler (15 min)

**Skapa `utils/errors.js`:**

```javascript
/**
 * Error types
 */
export const ErrorTypes = {
  NETWORK: 'network',
  AUTH: 'auth',
  VALIDATION: 'validation',
  SERVER: 'server',
  UNKNOWN: 'unknown'
};

/**
 * Format error message for display
 */
export function getErrorMessage(error) {
  // Network errors
  if (error.message === 'Failed to fetch') {
    return 'Kunde inte ansluta till servern. Kontrollera din internetanslutning.';
  }
  
  // Auth errors
  if (error.message === 'Token expired') {
    return 'Din session har gått ut. Logga in igen.';
  }
  
  // Validation errors
  if (error.message.includes('validation')) {
    return error.message;
  }
  
  // Server errors
  if (error.message.includes('500')) {
    return 'Ett serverfel uppstod. Försök igen senare.';
  }
  
  // Default
  return error.message || 'Ett okänt fel uppstod';
}

/**
 * Log error for debugging
 */
export function logError(error, context = '') {
  console.group(`❌ Error${context ? ` - ${context}` : ''}`);
  console.error('Message:', error.message);
  console.error('Stack:', error.stack);
  console.error('Time:', new Date().toISOString());
  console.groupEnd();
}

/**
 * Display error to user
 */
export function displayError(error, elementId = 'error-message') {
  const errorElement = document.getElementById(elementId);
  
  if (errorElement) {
    const message = getErrorMessage(error);
    errorElement.textContent = message;
    errorElement.style.display = 'block';
    
    // Hide after 5 seconds
    setTimeout(() => {
      errorElement.style.display = 'none';
    }, 5000);
  }
  
  // Also log for debugging
  logError(error);
}
```

**Använd i `main.js`:**

```javascript
import { displayError, logError } from './utils/errors.js';

async function handleSignup(event) {
  event.preventDefault();
  clearMessages();
  
  // ... validation ...
  
  try {
    const response = await fetch(`${API_URL}/auth/signup`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ username, email, password })
    });
    
    const data = await response.json();
    
    if (response.ok) {
      // Success
      showSuccess('Konto skapat!');
      // ...
    } else {
      // Backend error
      throw new Error(data.message);
    }
    
  } catch (error) {
    // Use global error handler
    displayError(error);
    logError(error, 'Signup');
  } finally {
    // Reset button
    submitButton.disabled = false;
    submitButton.textContent = 'Skapa konto';
  }
}
```

---

### Steg 2: Loading states (15 min)

**Skapa `utils/ui.js`:**

```javascript
/**
 * Show loading spinner
 */
export function showLoading(elementId = 'app', message = 'Laddar...') {
  const element = document.getElementById(elementId);
  
  element.innerHTML = `
    <div class="loading-container">
      <div class="spinner"></div>
      <p>${message}</p>
    </div>
  `;
}

/**
 * Disable button with loading state
 */
export function setButtonLoading(button, isLoading, loadingText = 'Laddar...') {
  if (isLoading) {
    button.dataset.originalText = button.textContent;
    button.textContent = loadingText;
    button.disabled = true;
    button.classList.add('loading');
  } else {
    button.textContent = button.dataset.originalText || 'Submit';
    button.disabled = false;
    button.classList.remove('loading');
  }
}

/**
 * Disable form inputs
 */
export function setFormDisabled(formId, disabled) {
  const form = document.getElementById(formId);
  if (!form) return;
  
  const inputs = form.querySelectorAll('input, button, select, textarea');
  inputs.forEach(input => {
    input.disabled = disabled;
  });
}
```

**CSS för loading:**

```css
.loading-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 300px;
}

.spinner {
  border: 4px solid #f3f3f3;
  border-top: 4px solid #007bff;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

button.loading {
  opacity: 0.7;
  cursor: not-allowed;
}
```

**Använd i `main.js`:**

```javascript
import { showLoading, setButtonLoading, setFormDisabled } from './utils/ui.js';

async function renderDashboard() {
  showLoading('app', 'Hämtar profil...');
  
  try {
    const { ok, data } = await api.get('/auth/me');
    
    if (!ok) {
      throw new Error('Kunde inte hämta profil');
    }
    
    // Render dashboard...
  } catch (error) {
    displayError(error);
    showView('login');
  }
}

async function handleLogin(event) {
  event.preventDefault();
  
  const submitButton = event.target.querySelector('button[type="submit"]');
  
  // Disable form
  setFormDisabled('login-form', true);
  setButtonLoading(submitButton, true, 'Loggar in...');
  
  try {
    // ... login logic ...
  } catch (error) {
    displayError(error);
  } finally {
    setFormDisabled('login-form', false);
    setButtonLoading(submitButton, false);
  }
}
```

---

### Steg 3: Input validation feedback (15 min)

**Lägg till real-time validation:**

```javascript
// Add to renderSignupForm()
function setupRealtimeValidation() {
  const usernameInput = document.getElementById('username');
  const emailInput = document.getElementById('email');
  const passwordInput = document.getElementById('password');
  const confirmPasswordInput = document.getElementById('confirm-password');
  
  // Username validation
  usernameInput.addEventListener('blur', () => {
    const error = validateUsername(usernameInput.value.trim());
    showFieldError('username', error);
  });
  
  usernameInput.addEventListener('input', () => {
    clearFieldError('username');
  });
  
  // Email validation
  emailInput.addEventListener('blur', () => {
    const error = validateEmail(emailInput.value.trim());
    showFieldError('email', error);
  });
  
  emailInput.addEventListener('input', () => {
    clearFieldError('email');
  });
  
  // Password validation
  passwordInput.addEventListener('blur', () => {
    const error = validatePassword(passwordInput.value);
    showFieldError('password', error);
    updatePasswordStrength(passwordInput.value);
  });
  
  // Confirm password
  confirmPasswordInput.addEventListener('blur', () => {
    const password = passwordInput.value;
    const confirm = confirmPasswordInput.value;
    
    if (confirm && password !== confirm) {
      showFieldError('confirm-password', 'Lösenorden matchar inte');
    }
  });
  
  confirmPasswordInput.addEventListener('input', () => {
    clearFieldError('confirm-password');
  });
}

function showFieldError(fieldId, error) {
  if (!error) return;
  
  const field = document.getElementById(fieldId);
  const formGroup = field.closest('.form-group');
  
  // Remove existing error
  const existingError = formGroup.querySelector('.field-error');
  if (existingError) {
    existingError.remove();
  }
  
  // Add error message
  const errorDiv = document.createElement('div');
  errorDiv.className = 'field-error';
  errorDiv.textContent = error;
  formGroup.appendChild(errorDiv);
  
  // Add error class to input
  field.classList.add('input-error');
}

function clearFieldError(fieldId) {
  const field = document.getElementById(fieldId);
  const formGroup = field.closest('.form-group');
  
  const errorDiv = formGroup.querySelector('.field-error');
  if (errorDiv) {
    errorDiv.remove();
  }
  
  field.classList.remove('input-error');
}

function updatePasswordStrength(password) {
  const strengthDiv = document.getElementById('password-strength');
  if (!strengthDiv) return;
  
  let strength = 'Svag';
  let color = '#dc3545';
  
  if (password.length >= 8 && /[A-Z]/.test(password) && /[0-9]/.test(password)) {
    strength = 'Stark';
    color = '#28a745';
  } else if (password.length >= 6) {
    strength = 'Medel';
    color = '#ffc107';
  }
  
  strengthDiv.innerHTML = `
    <div class="strength-bar" style="background: ${color}; width: ${
      strength === 'Stark' ? '100%' : strength === 'Medel' ? '66%' : '33%'
    }"></div>
    <span style="color: ${color}">${strength}</span>
  `;
}
```

**CSS:**

```css
.field-error {
  color: #dc3545;
  font-size: 14px;
  margin-top: 5px;
}

.input-error {
  border-color: #dc3545 !important;
}

input.input-error:focus {
  border-color: #dc3545 !important;
  box-shadow: 0 0 0 0.2rem rgba(220, 53, 69, 0.25);
}

#password-strength {
  margin-top: 5px;
  height: 4px;
  background: #e9ecef;
  border-radius: 2px;
  overflow: hidden;
  position: relative;
}

.strength-bar {
  height: 100%;
  transition: width 0.3s, background 0.3s;
}

#password-strength span {
  font-size: 12px;
  margin-left: 5px;
}
```

**Uppdatera signup form för att inkludera password strength:**

```javascript
function renderSignupForm() {
  document.querySelector('#app').innerHTML = `
    <div class="auth-container">
      <h1>Skapa konto</h1>
      
      <form id="signup-form">
        <!-- Username och email -->
        
        <div class="form-group">
          <label for="password">Lösenord</label>
          <input 
            type="password" 
            id="password" 
            name="password" 
            placeholder="Minst 6 tecken"
            required
          />
          <div id="password-strength"></div>
        </div>
        
        <!-- Rest of form -->
      </form>
    </div>
  `;
  
  // Attach listeners
  document.querySelector('#signup-form').addEventListener('submit', handleSignup);
  setupRealtimeValidation();
}
```

---

### Steg 4: Success feedback & animations (15 min)

**Lägg till toast notifications:**

```javascript
// In utils/ui.js

/**
 * Show toast notification
 */
export function showToast(message, type = 'info', duration = 3000) {
  // Remove existing toasts
  const existingToast = document.querySelector('.toast');
  if (existingToast) {
    existingToast.remove();
  }
  
  // Create toast
  const toast = document.createElement('div');
  toast.className = `toast toast-${type}`;
  toast.textContent = message;
  
  // Add to body
  document.body.appendChild(toast);
  
  // Trigger animation
  setTimeout(() => {
    toast.classList.add('show');
  }, 10);
  
  // Remove after duration
  setTimeout(() => {
    toast.classList.remove('show');
    setTimeout(() => {
      toast.remove();
    }, 300);
  }, duration);
}
```

**CSS:**

```css
.toast {
  position: fixed;
  bottom: -100px;
  left: 50%;
  transform: translateX(-50%);
  background: #333;
  color: white;
  padding: 15px 25px;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  z-index: 9999;
  transition: bottom 0.3s ease;
  font-size: 16px;
}

.toast.show {
  bottom: 30px;
}

.toast-success {
  background: #28a745;
}

.toast-error {
  background: #dc3545;
}

.toast-warning {
  background: #ffc107;
  color: #212529;
}

.toast-info {
  background: #17a2b8;
}
```

**Använd toasts:**

```javascript
import { showToast } from './utils/ui.js';

async function handleSignup(event) {
  // ... signup logic ...
  
  if (response.ok) {
    showToast('Konto skapat! Loggar in...', 'success');
    
    setTimeout(() => {
      showView('dashboard');
    }, 1500);
  }
}

async function handleLogin(event) {
  // ... login logic ...
  
  if (response.ok) {
    showToast('Inloggad!', 'success');
    
    setTimeout(() => {
      showView('dashboard');
    }, 500);
  }
}

function handleLogout() {
  showToast('Du har loggats ut', 'info');
  
  localStorage.removeItem('token');
  localStorage.removeItem('user');
  
  setTimeout(() => {
    showView('login');
  }, 1000);
}
```

---

### ✅ Kontrollera:
- [ ] Global error handler fungerar
- [ ] Loading states visas korrekt
- [ ] Real-time validation fungerar
- [ ] Password strength indicator fungerar
- [ ] Field errors visas
- [ ] Toast notifications fungerar
- [ ] Smooth animations
- [ ] Bra UX overall

---

### 📝 Final Commit

```bash
git add .
git commit -m "Add comprehensive error handling and UX polish"
git push
```

---

## 💡 Sammanfattning - Period 4

### ✅ Vad du nu kan:

**Frontend Setup:**
- [x] Vite projekt setup
- [x] Kommunikation med backend via fetch
- [x] CORS konfiguration

**Autentisering:**
- [x] Sign up formulär med validering
- [x] Login formulär
- [x] JWT token management
- [x] localStorage vs sessionStorage
- [x] Remember me funktionalitet
- [x] Säker logout

**Protected Routes:**
- [x] Skicka Authorization header
- [x] API helper functions
- [x] Hantera 401 errors
- [x] Token expiration handling
- [x] Session monitoring

**UX & Polish:**
- [x] Loading states
- [x] Error handling
- [x] Real-time validation
- [x] Toast notifications
- [x] Password strength indicator

---

## 🎯 Koppling till kursplan

**"Enklare användning av lösningar för att lagra data under och mellan sessioner":**
✅ localStorage för JWT tokens  
✅ sessionStorage för session-based login  
✅ Token-baserad autentisering  
✅ "Remember me" funktionalitet

**"Vanligt förekommande applikationsarkitektur och separation av olika slags logik":**
✅ Frontend (Vite) separerad från Backend (Express)  
✅ API helpers för nätverkskommunikation  
✅ Utility functions för errors, UI, validation  
✅ Modulär kodstruktur

---

## 📚 Nästa steg: Period 5

**Forum Project (Vecka 13-20)**

Nu är du redo att bygga ett komplett forum med:
- Sign up / Login (✅ Klart!)
- Skapa posts
- Kommentera
- Redigera/radera posts
- User profiles
- Moderator funktioner

Se fil: `period-5-forum-projekt.md`

---

## 🏠 Vecka 12 - Stora hemuppgiften

**Uppgift:** Bygg ett "Task Manager" app

**Krav:**

**Backend:**
1. Task model med fields: title, description, status, dueDate, userId
2. Endpoints:
   - POST /tasks (skapa task)
   - GET /tasks (hämta user's tasks)
   - PUT /tasks/:id (uppdatera task)
   - DELETE /tasks/:id (radera task)
3. Alla endpoints protected (requireAuth middleware)

**Frontend:**
1. Reuse din auth-frontend
2. Lägg till "Tasks" view:
   - Lista alla tasks
   - Lägg till ny task (form)
   - Markera task som done
   - Radera task
3. Filter tasks by status (all, done, pending)

**Inlämning:**
- GitHub repos (backend + frontend)
- README med instruktioner
- Screenshots av app

**Bonus:**
- Sortera tasks by due date
- Edit task modal
- Task statistics (X done, Y pending)

---

**Bra jobbat! 🎉**

Du har nu en komplett frontend med autentisering och är redo för slutprojektet!
