# Övningar baserade på vanliga misstag från proven

**Start här! Gör dessa övningar FÖRST innan du fortsätter med Period 1-5**

---

## 📋 Om dessa övningar

Dessa övningar är baserade på **verkliga misstag** från proven som eleverna gjorde. Genom att träna på dessa först får du:

✅ Undvika de vanligaste felen  
✅ Bygga bra vanor från start  
✅ Förstå varför saker går fel  
✅ Bli säkrare på grunderna

**Uppskattad tid:** 2-3 lektioner (à 60 minuter)

---

## 🔧 Förberedelse: GitHub Setup (Gör detta FÖRST!)

### Skapa GitHub-mapp i VS Code

**VIKTIGT:** Alla dina projekt MÅSTE ligga i `C:\Users\DittAnvändarnamn\Documents\GitHub`

**Steg 1: Skapa GitHub-mappen (5 min)**

1. Öppna **Utforskaren** (File Explorer) i Windows
2. Navigera till `C:\Users\DittAnvändarnamn\Documents\`
3. Högerklicka → New → Folder
4. Döp mappen till `GitHub`
5. Du ska nu ha: `C:\Users\DittAnvändarnamn\Documents\GitHub`

**Steg 2: Öppna mappen i VS Code**

1. Öppna VS Code
2. File → Open Folder (eller Ctrl+K Ctrl+O)
3. Navigera till `C:\Users\DittAnvändarnamn\Documents\GitHub`
4. Klicka "Select Folder"

Nu är VS Code öppet i din GitHub-mapp! Alla projekt du skapar kommer ligga här.

---

### Installera Git (Om du inte har det)

**Kolla om du har Git:**

1. Öppna Terminal i VS Code (Ctrl + `)
2. Skriv: `git --version`
3. Om du ser version (t.ex. "git version 2.43.0") → Du har Git! ✅
4. Om du får fel → Installera Git nedan

**Installera Git:**

1. Gå till: https://git-scm.com/download/win
2. Ladda ner Windows-versionen
3. Kör installationsfilen
4. Använd default settings (klicka "Next" hela vägen)
5. Starta om VS Code
6. Testa igen: `git --version`

---

### Konfigurera Git (Första gången)

**Gör detta en gång:**

Öppna Terminal i VS Code och skriv:

```bash
git config --global user.name "Ditt Namn"
git config --global user.email "din.email@example.com"
```

**Exempel:**
```bash
git config --global user.name "Anna Andersson"
git config --global user.email "anna.andersson@elev.skola.se"
```

**Verifiera:**
```bash
git config --global --list
```

Du ska se ditt namn och email ✅

---

## 📦 Hur du skapar ett GitHub Repo för varje projekt

**För VARJE nytt projekt följer du dessa steg:**

### Steg 1: Skapa projekt-mapp i VS Code

**Metod 1: Via VS Code Explorer (Enklast)**

1. I VS Code, se till att du har GitHub-mappen öppen
2. I Explorer (vänster sidebar), högerklicka i det tomma området
3. Välj "New Folder"
4. Döp mappen till ditt projekt-namn (t.ex. `training-api`)

**Metod 2: Via Terminal i VS Code**

```bash
# Skapa mapp (fungerar i Git Bash terminal)
mkdir training-api

# Eller flera mappar på en gång
mkdir training-api workout-api notes-api
```

**Öppna projekt-mappen:**

1. Högerklicka på mappen du just skapade i Explorer
2. Välj "Open in Integrated Terminal"
3. Eller: I terminal, skriv `cd training-api`

### Steg 2: Initiera Git repository

**I Terminal (inne i projekt-mappen):**

```bash
git init
```

Du ska se: "Initialized empty Git repository..."

### Steg 3: Skapa .gitignore

**VIKTIGT att göra INNAN första commit!**

**I VS Code:**

1. Högerklicka i Explorer → New File
2. Döp filen till `.gitignore` (med punkt först!)
3. Lägg till följande innehåll:

```
node_modules/
.env
.DS_Store
*.log
```

**Varför?**
- `node_modules/` är STOR och behövs inte (andra kan köra `npm install`)
- `.env` innehåller KÄNSLIG data (lösenord, API-nycklar)
- Dessa ska ALDRIG pushas till GitHub!

### Steg 4: Första commit

**Efter du skapat dina filer (package.json, server.mjs, etc.):**

**I Terminal:**

```bash
# Lägg till alla filer
git add .

# Skapa commit
git commit -m "Initial commit"
```

### Steg 5: Skapa GitHub repository

**Via GitHub.com (Rekommenderat):**

1. Gå till https://github.com
2. Logga in
3. Klicka "+" (uppe till höger) → "New repository"
4. Repository name: `training-api` (samma som din mapp!)
5. Description: "Träningspass API med Express och MongoDB"
6. **VIKTIGT:** Välj **Private** (om du inte vill att alla ska se din kod)
7. **VIKTIGT:** Välj **INTE** "Add README" eller ".gitignore" (vi har redan!)
8. Klicka "Create repository"

### Steg 6: Koppla lokal repo till GitHub

**Efter du skapat repo på GitHub.com:**

GitHub visar dig kommandon längst ner. Kopiera dessa rad för rad och kör i Terminal:

```bash
git remote add origin https://github.com/DittAnvändarnamn/training-api.git
git branch -M main
git push -u origin main
```

**Första gången:** GitHub frågar om inloggning → Logga in

**Nu är ditt projekt på GitHub!** 🎉

---

## 🔄 Arbetsflöde: Commit och Push

**Efter varje större ändring:**

**I Terminal:**

```bash
# 1. Se vad som ändrats
git status

# 2. Lägg till ändrade filer
git add .

# 3. Skapa commit med beskrivande meddelande
git commit -m "Add POST endpoint for training sessions"

# 4. Pusha till GitHub
git push
```

**Exempel commits:**
```bash
git commit -m "Initial project setup"
git commit -m "Add User model with bcrypt"
git commit -m "Implement login endpoint with JWT"
git commit -m "Add authentication middleware"
git commit -m "Fix validation bug in signup"
```

**Bra commit-meddelanden:**
- Börja med verb: "Add", "Fix", "Update", "Remove"
- Kort och beskrivande
- Engelska (standard i branschen)

---

## 📋 Checklista: Nytt projekt

Använd denna för VARJE nytt projekt:

**I Windows Explorer:**
- [ ] Navigera till `C:\Users\Ditt\Documents\GitHub`

**I VS Code:**
- [ ] Öppna GitHub-mappen (File → Open Folder)
- [ ] Skapa projektmapp i Explorer (högerklick → New Folder)
- [ ] Öppna Terminal för projekt-mappen (högerklick på mapp → Open in Integrated Terminal)
- [ ] Initiera Git: `git init`
- [ ] Skapa `.gitignore` fil (högerklick → New File)
- [ ] Lägg till `node_modules/` och `.env` i .gitignore
- [ ] Skapa `package.json`: `npm init -y`
- [ ] Installera paket: `npm install express ...`
- [ ] Skapa dina filer (server.mjs, app.mjs, etc.)
- [ ] Första commit: `git add .` → `git commit -m "Initial commit"`

**På GitHub.com:**
- [ ] Skapa nytt repository
- [ ] Kopiera kopplings-kommandon
- [ ] Kör kommandon i VS Code Terminal
- [ ] Push: `git push -u origin main`

---

## ⚠️ Vanliga Git-fel och lösningar

### Problem 1: "node_modules/ är på GitHub!"

**Orsak:** Glömde skapa .gitignore innan första commit

**Lösning i Terminal:**
```bash
# Ta bort node_modules från Git (men inte från disk)
git rm -r --cached node_modules

# Lägg till .gitignore om du inte har
# (Skapa fil i VS Code: .gitignore med innehåll: node_modules/)

# Commit ändringen
git add .
git commit -m "Remove node_modules from Git"
git push
```

### Problem 2: ".env är på GitHub!"

**FARLIGT!** Ditt MongoDB-lösenord är publikt!

**Lösning i Terminal:**
```bash
# Ta bort .env från Git
git rm --cached .env

# Lägg till i .gitignore
# (Öppna .gitignore i VS Code och lägg till: .env)

# Commit
git add .
git commit -m "Remove .env from repository"
git push

# VIKTIGT: Byt MongoDB-lösenord på Atlas!
```

### Problem 3: "Git säger att jag är 'behind'"

**Orsak:** GitHub har nyare commits än din lokala

**Lösning:**
```bash
git pull
```

### Problem 4: Merge conflict

**Orsak:** Samma rad ändrad på två ställen

**Lösning:**
1. Öppna filen som har konflikt i VS Code
2. Du ser:
```
<<<<<<< HEAD
Din kod här
=======
Annan kod här
>>>>>>> main
```
3. VS Code visar knappar: "Accept Current Change", "Accept Incoming Change", "Accept Both"
4. Välj rätt version
5. Spara filen
6. I Terminal: `git add .` → `git commit -m "Resolve merge conflict"` → `git push`

### Problem 5: "mkdir är inte ett kommando"

**Orsak:** CMD i Windows stödjer inte alla bash-kommandon

**Lösning:** Skapa mappar i VS Code Explorer istället (högerklick → New Folder)

Eller: Använd Git Bash terminal i VS Code:
1. Klicka på dropdown bredvid "+" i Terminal
2. Välj "Git Bash"

---

## 🎓 Git-kommandon snabbreferens

**De viktigaste kommandona du behöver:**

```bash
# Status - visa ändringar
git status

# Lägg till filer
git add .                    # Alla filer
git add filnamn.js          # Specifik fil

# Commit
git commit -m "Meddelande"

# Push till GitHub
git push

# Pull från GitHub
git pull

# Se commit-historik
git log --oneline           # Kortare format

# Initiera nytt repo
git init

# Koppla till GitHub
git remote add origin https://github.com/användarnamn/repo.git
git branch -M main
git push -u origin main
```

---

## 💡 VS Code Tips

**Skapa filer och mappar snabbt:**
- Högerklicka i Explorer → New File / New Folder
- Eller tryck ikoner längst upp i Explorer

**Öppna Terminal för specifik mapp:**
- Högerklicka på mapp → "Open in Integrated Terminal"

**Byt terminal-typ:**
- Dropdown bredvid "+" i Terminal
- Välj "Git Bash" (bäst för Git-kommandon)

**Se Git-ändringar visuellt:**
- Source Control icon i vänster sidebar (Ctrl+Shift+G)
- Se alla ändrade filer
- Klicka på fil för att se diff

---

Nu är du redo att börja med övningarna! 🚀

---

## 🎯 Lärandemål

Efter dessa övningar kan du:
- Skapa ett Express-projekt utan att göra vanliga setupfel
- Använda rätt responseformat konsekvent
- Undvika stavfel och syntax-fel
- Testa din kod systematiskt
- Kopiera kod mellan projekt på rätt sätt

---

## Övning 1: Projekt Setup utan fel (25 min)

### 🎓 Varför denna övning?

**Från proven:**
- Elever glömde lägga till `"type": "module"` i package.json
- Servern startade inte pga syntax-fel
- Blandar citattecken (backticks vs regular quotes)

### ✏️ Din uppgift

Skapa ett nytt projekt för ett **Träningspass-API** helt från grunden.

**Steg 1: Skapa projekt med Git (5 min)**

**I VS Code:**

1. Se till att du har GitHub-mappen öppen (File → Open Folder → `C:\Users\...\Documents\GitHub`)
2. I Explorer (vänster sidebar), högerklicka i det tomma området
3. Välj "New Folder"
4. Döp mappen till `training-api`
5. Högerklicka på `training-api` mappen → "Open in Integrated Terminal"

**I Terminal (inne i training-api):**

```bash
# Initiera Git
git init
```

**Skapa .gitignore:**

1. I Explorer, högerklicka på `training-api` mappen
2. Välj "New File"
3. Döp filen till `.gitignore` (med punkt först!)
4. Lägg till följande innehåll:

```
node_modules/
.env
.DS_Store
```

Spara filen (Ctrl+S).

**Steg 2: npm init och paket (3 min)**

```bash
npm init -y
npm install express cors uuid
npm install nodemon --save-dev
```

**Steg 3: package.json konfiguration (3 min)**

Öppna `package.json` och lägg till:

```json
{
  "name": "training-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "nodemon server.mjs"
  },
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "uuid": "^9.0.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

**Viktigt att kolla:**
- [ ] `"type": "module"` finns (annars kan du inte använda `import`)
- [ ] `"dev": "nodemon server.mjs"` i scripts
- [ ] Alla paket finns under dependencies/devDependencies

**Steg 4: Skapa server.mjs (3 min)**

```javascript
import app from './app.mjs';

const PORT = 3000;

app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

**Vanliga fel att undvika:**
```javascript
// ❌ FEL: Regular quotes för template literal
console.log("Server is running on port: ${PORT}");

// ✅ RÄTT: Backticks för interpolation
console.log(`Server is running on http://localhost:${PORT}`);
```

**Steg 5: Skapa app.mjs (3 min)**

```javascript
import express from 'express';
import cors from 'cors';

const app = express();

// Middleware
app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: false }));

export default app;
```

**Steg 6: Testa att servern startar (2 min)**

```bash
npm run dev
```

**Förväntat resultat:**
```
Server is running on http://localhost:3000
```

**Om servern inte startar, kolla:**
1. Är `"type": "module"` i package.json?
2. Använder du `.mjs` filändelse?
3. Använder du `import` och inte `require`?
4. Stavade du filnamnen rätt? (server.mjs, app.mjs)

**Steg 7: Git commit och GitHub (6 min)**

**Första commit:**
```bash
git add .
git commit -m "Initial project setup"
```

**Skapa GitHub repository:**

1. Gå till https://github.com
2. Klicka "+" → "New repository"
3. Name: `training-api`
4. Private repo
5. VÄLJ INTE "Add README" eller ".gitignore"
6. Create repository

**Koppla och pusha:**
```bash
git remote add origin https://github.com/DittAnvändarnamn/training-api.git
git branch -M main
git push -u origin main
```

**Verifiera på GitHub:**
- Gå till din repo på github.com
- Se att dina filer finns där
- `node_modules/` ska INTE finnas (tack vare .gitignore) ✅

**✅ Kontrollera:**
- [ ] package.json har "type": "module"
- [ ] Alla paket installerade (node_modules/ finns)
- [ ] server.mjs använder backticks för template literal
- [ ] app.mjs exporterar express app
- [ ] `npm run dev` startar servern utan fel
- [ ] .gitignore finns och innehåller `node_modules/` och `.env`
- [ ] Git repo initerat
- [ ] Första commit gjord
- [ ] Projekt på GitHub (utan node_modules!)


---

## Övning 2: Responseformat - Gör det rätt från start (15 min)

### 🎓 Varför denna övning?

**Från proven:**
- **Elev 1** returnerade objekt direkt: `res.json(training)`
- Skulle vara: `res.json({ success: true, data: training })`
- Inconsistent format mellan olika endpoints
- Svårt för frontend att hantera responses

### 📖 Rätt responseformat

**ALLA dina endpoints ska returnera samma format:**

```javascript
// Success response:
{
  "success": true,
  "data": {...} eller [...]
}

// Error response:
{
  "success": false,
  "message": "Felmeddelande här"
}
```

**Varför?**
- Frontend vet alltid vad den får
- Lätt att kolla: `if (response.success)`
- Professionell standard
- Enklare att debugga

### ✏️ Din uppgift

Skapa en GET endpoint som returnerar **korrekt responseformat**.

**Lägg till i app.mjs:**

```javascript
// Exempel på RÄTT format
app.get('/training', (req, res) => {
  try {
    // Exempel-data
    const training = [
      {
        id: "1",
        exercise: "Bänkpress",
        reps: 10,
        sets: 3
      }
    ];
    
    // ✅ RÄTT: Konsistent format
    res.status(200).json({
      success: true,
      data: training
    });
    
  } catch (error) {
    console.error('Error:', error);
    
    // ✅ RÄTT: Error har också konsistent format
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Jämför med fel format:**

```javascript
// ❌ FEL: Inkonsistent format
app.get('/training', (req, res) => {
  const training = [...];
  res.json(training);  // Bara arrayen, ingen success/data wrapper
});

// ❌ FEL: Fel struktur
app.get('/training', (req, res) => {
  const training = [...];
  res.json({
    status: 'ok',  // Ska vara 'success'
    result: training  // Ska vara 'data'
  });
});
```

**Testa i Postman:**

```
GET http://localhost:3000/training
```

**Förväntat resultat:**
```json
{
  "success": true,
  "data": [
    {
      "id": "1",
      "exercise": "Bänkpress",
      "reps": 10,
      "sets": 3
    }
  ]
}
```

**✅ Kontrollera:**
- [ ] Response har `success` field
- [ ] Response har `data` field
- [ ] Success är true för lyckad request
- [ ] Error response har `success: false` och `message`

---

## Övning 3: Systematisk testning - Hitta fel INNAN inlämning (25 min)

### 🎓 Varför denna övning?

**Från proven:**
- **Elev 2**: Kod fungerade inte pga copy-paste fel och stavfel
- Med 5-10 minuter testning: hade hittat alla fel
- Lämnade in utan att testa endpoints

**Lärdom:** Testa OFTA under utveckling, inte bara i slutet!

### 📖 Testcykeln

```
Skriv 10 rader kod → Spara → Testa i Postman → Fungerar? → Fortsätt
                                                ↓ Fungerar inte?
                                                Läs felmeddelande → Fixa → Testa igen
```

### ✏️ Din uppgift: Skapa endpoint och testa systematiskt

**Steg 1: Skapa POST endpoint (10 min)**

Lägg till i app.mjs:

```javascript
import { v4 as uuidv4 } from 'uuid';
import fs from 'fs';
import { fileURLToPath } from 'url';
import { dirname, join } from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
const filePath = join(__dirname, 'training.json');

// POST endpoint
app.post('/training', (req, res) => {
  try {
    const { exercise, reps, sets } = req.body;
    
    // Validering
    if (!exercise || !reps || !sets) {
      return res.status(400).json({
        success: false,
        message: 'Exercise, reps, and sets are required'
      });
    }
    
    // Skapa träningspass-objekt
    const newTraining = {
      id: uuidv4(),
      exercise: exercise.trim(),
      reps: parseInt(reps),
      sets: parseInt(sets),
      timestamp: new Date().toISOString()
    };
    
    // Läs befintlig data
    let trainingData = [];
    if (fs.existsSync(filePath)) {
      const fileContent = fs.readFileSync(filePath, 'utf-8');
      if (fileContent.trim().length > 0) {
        trainingData = JSON.parse(fileContent);
      }
    }
    
    // Lägg till ny
    trainingData.push(newTraining);
    
    // Spara
    fs.writeFileSync(filePath, JSON.stringify(trainingData, null, 2));
    
    // Returnera
    res.status(201).json({
      success: true,
      data: newTraining
    });
    
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Steg 2: TESTA direkt! (5 min)**

**Test 1: Giltig data**
```
POST http://localhost:3000/training

Body:
{
  "exercise": "Squats",
  "reps": 12,
  "sets": 4
}
```

**Förväntat:**
- Status: 201 Created
- Response har success: true
- Response har data med id, exercise, reps, sets, timestamp
- Filen training.json skapas

**Kolla:**
1. Postman response - ser du success och data?
2. Öppna training.json - finns datan där?

**Test 2: Saknat fält**
```
POST http://localhost:3000/training

Body:
{
  "exercise": "Squats",
  "reps": 12
}
```

**Förväntat:**
- Status: 400 Bad Request
- Response: `{ "success": false, "message": "..." }`

**Test 3: Tom sträng**
```
POST http://localhost:3000/training

Body:
{
  "exercise": "   ",
  "reps": 12,
  "sets": 4
}
```

**Förväntat:**
- Borde fungera (trim tar bort spaces)
- MEN: exercise blir tom!
- Detta är en BUG! 

**Fixa validering:**
```javascript
// Efter destructuring:
if (!exercise || !reps || !sets) {
  return res.status(400).json({
    success: false,
    message: 'Exercise, reps, and sets are required'
  });
}

// ✅ LÄGG TILL: Kolla att exercise inte är tom efter trim
if (exercise.trim().length === 0) {
  return res.status(400).json({
    success: false,
    message: 'Exercise cannot be empty'
  });
}
```

**Testa igen!** Nu ska det ge 400 error.

**Steg 3: Uppdatera GET endpoint (5 min)**

```javascript
// GET endpoint - läs från fil
app.get('/training', (req, res) => {
  try {
    let trainingData = [];
    
    if (fs.existsSync(filePath)) {
      const fileContent = fs.readFileSync(filePath, 'utf-8');
      if (fileContent.trim().length > 0) {
        trainingData = JSON.parse(fileContent);
      }
    }
    
    res.status(200).json({
      success: true,
      data: trainingData
    });
    
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Test 4: GET efter POST**
```
GET http://localhost:3000/training
```

**Förväntat:**
- Ser du alla träningspass du skapat?
- Är de sorterade? (Nej, ännu inte)

**Steg 4: Testchecklista (5 min)**

Efter varje endpoint du skapar, gå igenom denna checklista:

**POST endpoint:**
- [ ] Test med giltig data → 201 Created?
- [ ] Test med saknat fält → 400 Bad Request?
- [ ] Test med tom sträng → 400 Bad Request?
- [ ] Test med ogiltig datatyp (t.ex. sträng istället för nummer) → 400?
- [ ] Data sparas korrekt i fil/databas?
- [ ] Response har rätt format (success, data)?

**GET endpoint:**
- [ ] Test utan data → Tom array?
- [ ] Test med data → Returnerar korrekt?
- [ ] Response har rätt format?
- [ ] Status code 200?

**DELETE endpoint:**
- [ ] Test med giltigt ID → 200 OK?
- [ ] Test med ID som inte finns → 404 Not Found?
- [ ] Test med ogiltigt ID-format → 400 Bad Request?
- [ ] Data verkligen raderad från fil/databas?

**Steg 5: Commit dina ändringar (2 min)**

Efter du testat och allt fungerar:

```bash
# Se vad som ändrats
git status

# Lägg till ändrade filer
git add .

# Commit med beskrivande meddelande
git commit -m "Add POST endpoint with validation and file storage"

# Pusha till GitHub
git push
```

**Bra commit-meddelanden för denna övning:**
```bash
git commit -m "Add POST endpoint for training sessions"
git commit -m "Add validation for empty exercise field"
git commit -m "Update GET endpoint to read from file"
```

**✅ Kontrollera:**
- [ ] POST endpoint testad med minst 3 olika scenarios
- [ ] Validering fungerar (tom sträng blockeras)
- [ ] Data sparas i training.json
- [ ] GET endpoint returnerar sparad data
- [ ] Alla responses har rätt format
- [ ] Ändringar committade till Git
- [ ] Ändringar pushade till GitHub

---

## Övning 4: Copy-paste från tidigare projekt (25 min)

### 🎓 Varför denna övning?

**Från proven:**
- **Elev 2**: Kopierade från book review project
- Glömde uppdatera:
  - `reviews` → `training`
  - `saveReview()` → `saveTraining()`
  - `data: reviews` → `data: training`
- Kod funkade inte!

**Lärdom:** Copy-paste är OK, men MÅSTE göras systematiskt!

### 📖 Copy-paste metod

**Steg för steg:**
1. Bestäm vad som ska bytas: `old_name` → `new_name`
2. Använd Sök och ersätt (Ctrl+H / Cmd+H)
3. Ersätt ALLA förekomster
4. Läs igenom koden ändå (dubbelkolla!)
5. Testa DIREKT

### ✏️ Din uppgift: Kopiera training-api → workout-api

Du ska kopiera ditt training-api och göra ett workout-api (träningspass → träningsprogram).

**Datamodell för Workout:**
```json
{
  "id": "uuid",
  "name": "Bröst och rygg",
  "exercises": ["Bänkpress", "Rodd", "Dips"],
  "duration": 60,
  "difficulty": "intermediate",
  "timestamp": "ISO string"
}
```

**Steg 1: Kopiera projektet (3 min)**

**I Windows Explorer:**

1. Öppna `C:\Users\DittAnvändarnamn\Documents\GitHub`
2. Högerklicka på `training-api` mappen
3. Välj "Copy"
4. Högerklicka i det tomma området
5. Välj "Paste"
6. Döp om den kopierade mappen till `workout-api`

**I VS Code:**

1. File → Open Folder
2. Välj `C:\Users\...\Documents\GitHub\workout-api`
3. Öppna Terminal (Ctrl + `)

**I Terminal:**

```bash
# Initiera nytt Git repo
git init
```

**OBS:** Detta är ett NYTT projekt med egen Git-historik (inte samma som training-api!)

**Kontrollera .gitignore:**
- Filen ska redan finnas (kopierades från training-api)
- Öppna den och se att den innehåller:
```
node_modules/
.env
.DS_Store
```

**Steg 2: Gör en plan (3 min)**

Skriv ner alla namn som ska bytas:

| Gammalt | Nytt |
|---------|------|
| training | workout |
| Training | Workout |
| exercise, reps, sets | name, exercises, duration, difficulty |
| training.json | workouts.json |
| filePath (training) | filePath (workouts) |

**Steg 3: Sök och ersätt (10 min)**

Öppna `app.mjs` i VS Code:

**1. Tryck Ctrl+H (Cmd+H)**

**2. Ersätt filnamn:**
- Find: `training.json`
- Replace: `workouts.json`
- Replace All

**3. Ersätt variabelnamn:**
- Find: `trainingData`
- Replace: `workoutData`
- Replace All

**4. Ersätt i comments och strings:**
- Find: `training`
- Replace: `workout`
- Replace All (men kolla varje!)

**5. Uppdatera request body fields:**

Manuellt ändra detta:
```javascript
// FÖRE:
const { exercise, reps, sets } = req.body;

if (!exercise || !reps || !sets) {
  return res.status(400).json({
    success: false,
    message: 'Exercise, reps, and sets are required'
  });
}

const newTraining = {
  id: uuidv4(),
  exercise: exercise.trim(),
  reps: parseInt(reps),
  sets: parseInt(sets),
  timestamp: new Date().toISOString()
};
```

Till:
```javascript
// EFTER:
const { name, exercises, duration, difficulty } = req.body;

if (!name || !exercises || !duration || !difficulty) {
  return res.status(400).json({
    success: false,
    message: 'Name, exercises, duration, and difficulty are required'
  });
}

// Validera exercises är array
if (!Array.isArray(exercises) || exercises.length === 0) {
  return res.status(400).json({
    success: false,
    message: 'Exercises must be a non-empty array'
  });
}

// Validera difficulty
const validDifficulties = ['beginner', 'intermediate', 'advanced'];
if (!validDifficulties.includes(difficulty)) {
  return res.status(400).json({
    success: false,
    message: 'Difficulty must be beginner, intermediate, or advanced'
  });
}

const newWorkout = {
  id: uuidv4(),
  name: name.trim(),
  exercises: exercises.map(ex => ex.trim()),
  duration: parseInt(duration),
  difficulty,
  timestamp: new Date().toISOString()
};
```

**Steg 4: Checklista - Gått igenom allt? (5 min)**

- [ ] `training.json` → `workouts.json`
- [ ] `trainingData` → `workoutData`
- [ ] `newTraining` → `newWorkout`
- [ ] `/training` endpoints → `/workouts`
- [ ] `{ exercise, reps, sets }` → `{ name, exercises, duration, difficulty }`
- [ ] Validering uppdaterad för nya fält
- [ ] Console.log meddelanden uppdaterade

**Steg 5: TESTA! (5 min)**

**Test 1: POST**
```
POST http://localhost:3000/workouts

Body:
{
  "name": "Bröst och rygg",
  "exercises": ["Bänkpress", "Rodd", "Dips"],
  "duration": 60,
  "difficulty": "intermediate"
}
```

**Förväntat:**
- 201 Created
- workouts.json fil skapas
- Data sparas korrekt

**Test 2: GET**
```
GET http://localhost:3000/workouts
```

**Test 3: Validering - ogiltig difficulty**
```
POST http://localhost:3000/workouts

Body:
{
  "name": "Test",
  "exercises": ["Test"],
  "duration": 30,
  "difficulty": "easy"
}
```

**Förväntat:**
- 400 Bad Request
- Message: "Difficulty must be beginner, intermediate, or advanced"

**Steg 6: Git commit och GitHub (5 min)**

**Första commit för workout-api:**
```bash
# Status
git status

# Lägg till alla filer
git add .

# Första commit
git commit -m "Initial workout-api setup (copied from training-api)"
```

**Skapa nytt GitHub repo:**

1. Gå till https://github.com
2. New repository
3. Name: `workout-api`
4. Private
5. Create repository

**Koppla och pusha:**
```bash
git remote add origin https://github.com/DittAnvändarnamn/workout-api.git
git branch -M main
git push -u origin main
```

**Verifiera:**
- Två separata repos på GitHub: `training-api` och `workout-api` ✅
- Båda har olika kod (training vs workout) ✅

**✅ Kontrollera:**
- [ ] Alla variabelnamn uppdaterade
- [ ] Endpoints fungerar (/workouts)
- [ ] Ny validering fungerar (array, difficulty)
- [ ] workouts.json skapas
- [ ] Ingen referens till "training" kvar i koden
- [ ] Nytt Git repo skapat
- [ ] Projekt på GitHub (separat från training-api)

---

## Övning 5: Stavfel och syntax-fel (20 min)

### 🎓 Varför denna övning?

**Från proven:**
- **Elev 2**: `training.lenght` → undefined (skulle vara `length`)
- `filepath` vs `filePath` → inconsistent
- `/trainig/:id` → endpoint typo
- Saknar `=>` i arrow function
- Fel citattecken för template literal

**Lärdom:** Små stavfel = stor påverkan!

### ✏️ Din uppgift: Hitta och fixa alla fel

Här är kod med **10 fel**. Hitta och fixa alla!

```javascript
import express from 'express';
import cors from 'cors';
import fs from 'fs';
import { v4 as uuidv4 } from 'uuid';
import { fileURLToPath } from 'url';
import { dirname, join } from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

const app = express();

app.use(cors());
app.use(express.json());

// FEL 1: Stavfel i variabelnamn
const filepath = join(__dirname, 'movies.json');

// FEL 2: Saknar => i arrow function
const readMovies = () {
  try {
    // FEL 3: Använder fel variabelnamn
    if (!fs.existsSync(filePath)) {
      return [];
    }
    
    const data = fs.readFileSync(filePath, 'utf-8');
    const movies = JSON.parse(data);
    return movies;
  } catch (error) {
    return [];
  }
};

const saveMovie = (movie) => {
  try {
    const movies = readMovies();
    movies.push(movie);
    fs.writeFileSync(filePath, JSON.stringify(movies, null, 2));
    return true;
  } catch (error) {
    return false;
  }
};

// FEL 4: Endpoint path stavfel
app.get('/moveis', (req, res) => {
  try {
    const movies = readMovies();
    
    res.status(200).json({
      success: true,
      count: movies.lenght,  // FEL 5: Stavfel i length
      data: movies
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

app.post('/movies', (req, res) => {
  try {
    const { title, directr, year } = req.body;  // FEL 6: Stavfel i director
    
    // FEL 7: Använder fel variabelnamn
    if (!title || !director || !year) {
      return res.status(400).json({
        success: false,
        message: 'All fields required'
      });
    }
    
    const newMovie = {
      id: uuidv4(),
      title,
      director,  // FEL 8: Använder variabel som inte finns
      year: parseInt(year)
    };
    
    saveMovie(newMovie);
    
    res.status(201).json({
      success: true,
      data: newMovie
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

// FEL 9: Endpoint path stavfel
app.delete('/movies/:id', (req, res) => {
  try {
    const movies = readMovies();
    // FEL 10: Stavfel i filter
    const filtered = movies.filtr(m => m.id !== req.params.id);
    
    if (movies.length === filtered.length) {
      return res.status(404).json({
        success: false,
        message: 'Movie not found'
      });
    }
    
    fs.writeFileSync(filePath, JSON.stringify(filtered, null, 2));
    
    res.status(200).json({
      success: true,
      message: 'Movie deleted'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

const PORT = 3000;
// FEL 11: Fel citattecken för template literal
app.listen(PORT, () => {
  console.log("Server running on port: ${PORT}");
});

export default app;
```

**Hitta alla 11 fel!**

<details>
<summary>💡 Tips (öppna om du fastnar)</summary>

**Leta efter:**
1. Variabelnamn stavade olika (filepath vs filePath)
2. Saknade arrow (=>)
3. Felstavade properties (.lenght, .filtr)
4. Endpoint paths med stavfel
5. Destructuring med stavfel
6. Använder variabel som inte deklarerats
7. Template literals med fel citattecken

</details>

<details>
<summary>✅ Lösning</summary>

**Alla 11 fel:**

**1. Rad 16:** `filepath` → ska vara `filePath` (konsekvent naming)
```javascript
const filePath = join(__dirname, 'movies.json');
```

**2. Rad 19:** Saknar `=>` i arrow function
```javascript
const readMovies = () => {  // Lägg till =>
```

**3. Rad 21:** Använder `filePath` men definierade `filepath`
- Antingen: ändra rad 16 till `filePath`
- Eller: ändra här till `filepath`
- **Rätt:** Använd `filePath` överallt!

**4. Rad 45:** `/moveis` → ska vara `/movies`
```javascript
app.get('/movies', (req, res) => {
```

**5. Rad 50:** `movies.lenght` → ska vara `movies.length`
```javascript
count: movies.length,
```

**6. Rad 63:** `directr` → ska vara `director`
```javascript
const { title, director, year } = req.body;
```

**7-8. Rad 66-67:** Validerar `director` men destructured `directr`
- Fixa genom att använda `director` överallt

**9. Rad 89:** Ingen stavfel här! (detta var en check)

**10. Rad 92:** `.filtr` → ska vara `.filter`
```javascript
const filtered = movies.filter(m => m.id !== req.params.id);
```

**11. Rad 107:** Regular quotes istället för backticks
```javascript
console.log(`Server running on port: ${PORT}`);
```

**Summering:**
- 3 stavfel i variabelnamn (filepath, directr, lenght)
- 1 stavfel i method (filtr)
- 1 stavfel i endpoint (/moveis)
- 1 saknad arrow (=>)
- 1 fel citattecken (template literal)

</details>

**Efter du fixat alla fel:**

1. Spara filen
2. Starta servern: `npm run dev`
3. Kolla att servern startar utan fel
4. Testa alla endpoints i Postman

**✅ Kontrollera:**
- [ ] Alla variabelnamn konsekventa
- [ ] Arrow functions har `=>`
- [ ] Template literals använder backticks
- [ ] Endpoints stavade rätt
- [ ] `.length` och `.filter` stavade rätt
- [ ] Servern startar utan error
- [ ] Alla endpoints fungerar i Postman

---

## Övning 6: Glömde endpoints - Funktioner utan routes (30 min)

### 🎓 Varför denna övning?

**Från provet - Elev 3:**
- Hade skrivit PERFEKTA funktioner: `readWorkouts()`, `saveWorkout()`, `deleteWorkout()`
- Hade middleware korrekt: `cors()`, `express.json()`
- **MEN:** Glömde skapa endpoints (app.get, app.post, app.delete)
- Resultat: Servern startade, men gjorde INGENTING när man testade i Postman

**Lärdom:** Funktioner är inte endpoints! Du måste KOPPLA funktionerna till routes.

---

### 📖 Förklaring: Funktioner vs Endpoints

**Funktioner (helpers):**
```javascript
function readWorkouts() {
  // Gör något...
  return workouts;
}
```
- Detta är en **intern funktion**
- Körs INTE automatiskt
- Klienten kan INTE anropa denna direkt

**Endpoints (routes):**
```javascript
app.get('/workouts', (req, res) => {
  const workouts = readWorkouts();  // ANROPAR funktionen
  res.json({ success: true, data: workouts });
});
```
- Detta är en **route** som lyssnar på HTTP requests
- Körs när klient gör: `GET http://localhost:3000/workouts`
- Anropar din funktion och returnerar resultat till klienten

**Tänk så här:**
```
Klient (Postman) → HTTP Request → Endpoint (app.get) → Funktion → Response → Klient
```

Utan endpoint: Klienten når aldrig dina funktioner! ❌

---

### ✏️ Din uppgift: Fixa Elevens kod

Här är Elev 3:s kod. Den har funktioner men **INGA endpoints**.

**app.mjs (OFÄRDIG):**
```javascript
import cors from "cors";
import express from "express";
import fs from "fs";
import { v4 as uuidv4 } from "uuid";

const app = express();
const DATA_FILE = "./workouts.json";

// Middleware
app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// ✅ Funktioner finns (BRA!)
function readWorkouts() {
  try {
    const data = fs.readFileSync(DATA_FILE);
    return JSON.parse(data);
  } catch (error) {
    return [];
  }
}

function saveWorkout(workout) {
  const workouts = readWorkouts();
  
  const newWorkout = {
    id: uuidv4(),
    exercise: workout.exercise,
    duration: workout.duration,
    date: workout.date,
    notes: workout.notes,
    timestamp: new Date().toISOString()
  };
  
  workouts.push(newWorkout);
  fs.writeFileSync(DATA_FILE, JSON.stringify(workouts, null, 2));
  return newWorkout;
}

function deleteWorkout(id) {
  const workouts = readWorkouts();
  const filtered = workouts.filter((w) => w.id !== id);
  fs.writeFileSync(DATA_FILE, JSON.stringify(filtered, null, 2));
}

// ❌ PROBLEM: INGA ENDPOINTS!
// Funktionerna finns men används inte...

export default app;
```

---

### Steg 1: Förstå vad som saknas (5 min)

**Kolla koden ovan. Funktionerna finns:**
- ✅ `readWorkouts()` - kan läsa från fil
- ✅ `saveWorkout()` - kan spara till fil
- ✅ `deleteWorkout()` - kan radera från fil

**Men vad händer om du testar i Postman?**

```
GET http://localhost:3000/workouts
```

**Resultat:** `Cannot GET /workouts` ❌

**Varför?**
- Det finns ingen `app.get('/workouts', ...)` endpoint!
- Servern vet inte vad den ska göra när någon besöker `/workouts`

---

### Steg 2: Lägg till GET endpoint (8 min)

Lägg till EFTER funktionerna men FÖRE `export default app`:

```javascript
/**
 * GET /workouts - Hämta alla träningspass
 */
app.get('/workouts', (req, res) => {
  try {
    // ANROPA funktionen
    const workouts = readWorkouts();
    
    // Returnera till klienten
    res.status(200).json({
      success: true,
      data: workouts
    });
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

```javascript
app.get('/workouts', (req, res) => {
  // Denna funktion körs när någon gör GET request till /workouts
})
```

**Flöde:**
1. Klient: `GET /workouts`
2. Express: "Jag har en route för `/workouts`!"
3. Kör callback-funktionen
4. Anropa `readWorkouts()`
5. Returnera data med `res.json()`

**Testa i Postman:**
```
GET http://localhost:3000/workouts
```

**Förväntat:**
```json
{
  "success": true,
  "data": []
}
```

Om filen inte finns än → tom array ✅

---

### Steg 3: Lägg till POST endpoint (8 min)

```javascript
/**
 * POST /workouts - Skapa nytt träningspass
 */
app.post('/workouts', (req, res) => {
  try {
    const { exercise, duration, date, notes } = req.body;
    
    // Validering
    if (!exercise || !duration || !date) {
      return res.status(400).json({
        success: false,
        message: 'Exercise, duration, and date are required'
      });
    }
    
    // ANROPA funktionen
    const newWorkout = saveWorkout({
      exercise,
      duration,
      date,
      notes: notes || '' // notes är optional
    });
    
    // Returnera det skapade träningspasset
    res.status(201).json({
      success: true,
      data: newWorkout
    });
    
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

```javascript
const { exercise, duration, date, notes } = req.body;
```
Hämta data från request body (det klienten skickade).

```javascript
const newWorkout = saveWorkout({ ... });
```
Anropa `saveWorkout()` funktionen med datan.

```javascript
res.status(201).json({ ... });
```
Returnera det skapade träningspasset till klienten.

**Testa i Postman:**
```
POST http://localhost:3000/workouts

Body:
{
  "exercise": "Running",
  "duration": 30,
  "date": "2025-01-20",
  "notes": "Morning run"
}
```

**Förväntat:**
```json
{
  "success": true,
  "data": {
    "id": "abc-123-def-456",
    "exercise": "Running",
    "duration": 30,
    "date": "2025-01-20",
    "notes": "Morning run",
    "timestamp": "2025-01-20T10:00:00.000Z"
  }
}
```

Status: 201 Created ✅

---

### Steg 4: Lägg till DELETE endpoint (8 min)

```javascript
/**
 * DELETE /workouts/:id - Radera träningspass
 */
app.delete('/workouts/:id', (req, res) => {
  try {
    const { id } = req.params;
    
    // Kolla om träningspasset finns
    const workouts = readWorkouts();
    const exists = workouts.find(w => w.id === id);
    
    if (!exists) {
      return res.status(404).json({
        success: false,
        message: 'Workout not found'
      });
    }
    
    // ANROPA funktionen
    deleteWorkout(id);
    
    res.status(200).json({
      success: true,
      message: 'Workout deleted successfully'
    });
    
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

```javascript
app.delete('/workouts/:id', ...)
```
`:id` är en route parameter (dynamisk del av URL:en).

```javascript
const { id } = req.params;
```
Hämta id från URL:en. T.ex. `/workouts/abc-123` → `id = "abc-123"`

```javascript
const exists = workouts.find(w => w.id === id);
```
Kolla om träningspasset finns innan vi försöker radera.

**Testa i Postman:**
```
DELETE http://localhost:3000/workouts/abc-123-def-456
```

(Använd ett riktigt ID från din POST response)

**Förväntat:**
```json
{
  "success": true,
  "message": "Workout deleted successfully"
}
```

Status: 200 OK ✅

---

### 🎯 Reflektion (3 min)

**Vad lärde du dig?**

1. **Funktioner är inte endpoints**
   - Funktion = intern hjälpfunktion
   - Endpoint = route som klienten kan anropa

2. **Endpoints är lim mellan klient och funktioner**
   ```
   Klient → Endpoint → Funktion → Response → Klient
   ```

3. **Varje endpoint behöver:**
   - Route (`app.get('/path', ...)`)
   - Request handler (callback-funktion)
   - Anrop till hjälpfunktioner
   - Response till klienten (`res.json()`)

4. **Try-catch är viktigt**
   - Fångar fel från funktioner
   - Förhindrar server-krasch

**Skriv ner:**
- Vad var svårast att förstå?
- Hur skiljer sig funktion från endpoint?
- Varför räcker det inte med bara funktioner?

---

### ✅ Kontrollera:
- [ ] GET endpoint skapad och testad
- [ ] POST endpoint skapad och testad
- [ ] DELETE endpoint skapad och testad
- [ ] Alla endpoints anropar rätt funktioner
- [ ] Alla endpoints har try-catch
- [ ] Alla endpoints returnerar konsistent format ({ success, data/message })
- [ ] Validering finns i POST endpoint
- [ ] 404-kontroll finns i DELETE endpoint
- [ ] Testat hela CRUD-flödet i Postman
- [ ] workouts.json skapas och uppdateras korrekt

---

### 💡 Pro-tips

**1. Separera concerns:**
```javascript
// ✅ BRA: Funktioner gör EN sak
function readWorkouts() { /* bara läsa */ }
function saveWorkout(workout) { /* bara spara */ }

// Endpoints använder funktionerna
app.get('/workouts', (req, res) => {
  const workouts = readWorkouts(); // använd funktionen
  res.json({ success: true, data: workouts });
});
```

**2. Funktioner returnerar, endpoints skickar response:**
```javascript
// Funktion: returnerar värde
function readWorkouts() {
  return workouts; // return
}

// Endpoint: skickar HTTP response
app.get('/workouts', (req, res) => {
  const workouts = readWorkouts();
  res.json({ data: workouts }); // res.json()
});
```

**3. Kom ihåg validering i endpoints:**
```javascript
app.post('/workouts', (req, res) => {
  // Validera INNAN du anropar funktioner
  if (!req.body.exercise) {
    return res.status(400).json({ ... });
  }
  
  // Nu är det säkert att anropa funktion
  const newWorkout = saveWorkout(req.body);
  res.json({ data: newWorkout });
});
```



---

## 🎉 Grattis!

Du har nu tränat på de vanligaste misstagen från proven!

### ✅ Vad du kan nu:

- [x] Sätta upp projekt korrekt (package.json, type: module)
- [x] Använda rätt responseformat konsekvent
- [x] Testa systematiskt under utveckling
- [x] Copy-paste kod på rätt sätt
- [x] Känna igen och undvika stavfel
- [x] Känna igen och undvika syntax-fel

### 🚀 Nästa steg

**Du är nu redo för:**
- **Period 1 (Vecka 1-4):** Express basics, JSON-filer, CRUD
- Se filen: `period-1-express-grunderna.md`

### 💡 Tips inför Period 1:

**Använd denna checklista varje gång du kodar:**

**Setup:**
- [ ] `"type": "module"` i package.json
- [ ] Dev script konfigurerat
- [ ] Servern startar utan fel

**Varje endpoint:**
- [ ] Konsistent responseformat (`{ success, data }`)
- [ ] Try-catch för error handling
- [ ] Validering av input
- [ ] Testat i Postman DIREKT efter implementation

**Före push/inlämning:**
- [ ] Alla endpoints testade
- [ ] Inga console.error i terminal
- [ ] Läst igenom all kod radför rad
- [ ] Kollat att variabelnamn är konsekventa

---

**Lycka till! 🚀**

Nu kan du börja med Period 1 med bra grundkunskaper!
