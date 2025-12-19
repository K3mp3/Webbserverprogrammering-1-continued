# Övningar: Webbserverprogrammering Nivå 1

**Baserat på vanliga misstag från proven**

---

## 📚 Hur du använder detta häfte

**Detta är INTE en lärobok att läsa!**  
Detta är ett **övningshäfte** där du lär dig genom att göra.

### Så här fungerar det:
1. Varje övning har en **kort förklaring** av konceptet
2. Sedan får du ett **praktiskt problem** att lösa
3. Du kodar själv och testar din lösning
4. När du är klar kan du kolla **lösningsförslaget**

### Tips:
- ✅ Gör övningarna i ordning (de bygger på varandra)
- ✅ Testa din kod efter varje övning
- ✅ Fastnar du? Fråga läraren innan du kollar lösningen
- ✅ När du kollat lösningen: skriv koden själv (kopiera inte!)

**Uppskattad tid:** 3-4 lektioner (à 80 minuter)

---

## 🎯 Lärandemål

Efter dessa övningar kan du:
- ✅ Strukturera ett Express-projekt korrekt
- ✅ Skapa GET, POST och DELETE endpoints
- ✅ Läsa och skriva till JSON-filer
- ✅ Validera inkommande data
- ✅ Testa ditt API med Postman
- ✅ Hitta och fixa vanliga fel

---

## Övning 1: Projektstruktur och Setup (20 min)

### 🎓 Vad ska du lära dig?
Hur man sätter upp ett Express-projekt från scratch med rätt struktur.

### 📖 Kort förklaring

**Ett Express-projekt behöver:**
1. **package.json** - Innehåller projektinformation och dependencies
2. **server.mjs** - Startar servern
3. **app.mjs** - Konfigurerar Express-appen
4. **node_modules/** - Där installerade paket hamnar

**Varför dela upp i server.mjs och app.mjs?**
- `server.mjs` = ansvarar BARA för att starta servern
- `app.mjs` = innehåller all din Express-logik (endpoints, middleware)
- Detta gör koden lättare att testa och organisera

### ✏️ Din uppgift

Skapa ett nytt projekt för ett **Antecknings-API** (Notes API).

**Steg för steg:**

**1. Skapa projektmapp i GitHub-katalogen**

**I VS Code:**

1. Se till att du har GitHub-mappen öppen (File → Open Folder → `C:\Users\...\Documents\GitHub`)
2. I Explorer (vänster sidebar), högerklicka i det tomma området
3. Välj "New Folder"
4. Döp mappen till `notes-api`
5. Högerklicka på `notes-api` mappen → "Open in Integrated Terminal"

**I Terminal (inne i notes-api):**

```bash
# Initiera Git
git init
```

**Skapa .gitignore:**

1. Högerklicka på `notes-api` mappen i Explorer
2. Välj "New File"  
3. Döp filen till `.gitignore`
4. Lägg till:

```
node_modules/
.env
.DS_Store
```

Spara filen (Ctrl+S).

**2. Initiera npm**
```bash
npm init -y
```

**3. Installera paket**
```bash
npm install express cors uuid
npm install nodemon --save-dev
```

**4. Uppdatera package.json**

Öppna `package.json` och lägg till:
```json
{
  "type": "module",
  "scripts": {
    "dev": "nodemon server.mjs"
  }
}
```

**Förklaring:**
- `"type": "module"` = Vi kan använda `import` istället för `require`
- `"dev": "nodemon server.mjs"` = Script för att starta servern med auto-reload

**5. Skapa server.mjs**

```javascript
import app from './app.mjs';

const PORT = 3000;

app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

**Förklaring:**
- Importerar `app` från app.mjs
- `app.listen(PORT, callback)` = Startar servern på angiven port
- **Notera backticks** `` ` `` i console.log för att interpolera `${PORT}`

**6. Skapa app.mjs**

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

**Förklaring:**
- `express()` = Skapar en Express-applikation
- `app.use()` = Lägger till middleware (funktioner som körs före dina endpoints)
- `cors()` = Tillåter requests från andra domäner
- `express.json()` = Läser JSON från request body
- `express.urlencoded()` = Läser formulärdata
- `export default app` = Gör appen tillgänglig för server.mjs

**7. Testa att servern startar**

```bash
npm run dev
```

**Förväntat resultat:**
```
Server is running on http://localhost:3000
```

**8. Git commit och GitHub**

**Första commit:**
```bash
git status
git add .
git commit -m "Initial project setup - notes API"
```

**Skapa GitHub repo:**
1. Gå till https://github.com
2. New repository
3. Name: `notes-api`
4. Private
5. VÄLJ INTE "Add README" eller ".gitignore"
6. Create repository

**Koppla och pusha:**
```bash
git remote add origin https://github.com/DittAnvändarnamn/notes-api.git
git branch -M main
git push -u origin main
```

**Verifiera på GitHub:**
- Projekt finns där ✅
- `node_modules/` finns INTE (tack vare .gitignore) ✅

### ✅ Kontrollera att du har:
- [ ] Mappen `notes-api` i `C:\Users\...\Documents\GitHub\`
- [ ] `package.json` med "type": "module" och dev-script
- [ ] `server.mjs` som startar servern
- [ ] `app.mjs` med Express och middleware
- [ ] `node_modules/` mapp (skapas vid npm install)
- [ ] `.gitignore` med `node_modules/` och `.env`
- [ ] Servern startar utan fel när du kör `npm run dev`
- [ ] Git repo initerat
- [ ] Projekt på GitHub

---

## Övning 2: Din första GET endpoint (15 min)

### 🎓 Vad ska du lära dig?
Skapa en endpoint som returnerar data i korrekt JSON-format.

### 📖 Kort förklaring

**Vad är en endpoint?**
En endpoint är en specifik URL på din server som gör något när den anropas.

**GET endpoint = Hämta data**
- Används för att läsa/hämta information
- Ändrar INTE data på servern
- Exempel: Hämta alla anteckningar, hämta en specifik anteckning

**Responseformat:**
Alla dina endpoints ska returnera ett konsekvent format:
```json
{
  "success": true,
  "data": [...]
}
```

**Varför?**
- Frontend vet alltid vad den kan förvänta sig
- Lätt att kolla om något gick fel: `if (response.success)`
- Professionell standard

### ✏️ Din uppgift

Skapa en GET endpoint som returnerar alla anteckningar.

**1. Lägg till i app.mjs (efter middleware):**

```javascript
// Endpoint: GET /notes - Hämta alla anteckningar
app.get('/notes', (req, res) => {
  try {
    // För nu: returnera tom array (vi lägger till fil-läsning senare)
    const notes = [];
    
    res.status(200).json({
      success: true,
      data: notes
    });
  } catch (error) {
    console.error('Error fetching notes:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring rad för rad:**

```javascript
app.get('/notes', (req, res) => {
```
- `app.get()` = Skapar en GET endpoint
- `'/notes'` = URL-path (full URL blir: http://localhost:3000/notes)
- `(req, res)` = Callback-funktion som körs när någon anropar endpointen
  - `req` = request (inkommande data från klienten)
  - `res` = response (vad vi skickar tillbaka)

```javascript
try {
```
- Försöker köra koden inuti
- Om något går fel fångas det av `catch`

```javascript
const notes = [];
```
- För nu hårdkodar vi en tom array
- I nästa övning läser vi från fil

```javascript
res.status(200).json({ ... });
```
- `res.status(200)` = Sätter HTTP-statuskod 200 (OK)
- `.json({ ... })` = Skickar JSON-data tillbaka
- `success: true` = Operationen lyckades
- `data: notes` = Själva datan (array med anteckningar)

```javascript
catch (error) {
  console.error('Error fetching notes:', error);
```
- Om något går fel fångas det här
- `console.error()` = Skriver ut felet i terminalen (viktigt för debugging!)

```javascript
res.status(500).json({ ... });
```
- `500` = Server error (något gick fel på servern)
- `success: false` = Operationen misslyckades
- `message` = Förklaring vad som gick fel

**2. Testa i Postman:**

**Steg 1:** Öppna Postman

**Steg 2:** Välj metod **GET**

**Steg 3:** Skriv URL: `http://localhost:3000/notes`

**Steg 4:** Tryck **Send**

**Förväntat resultat:**
```json
{
  "success": true,
  "data": []
}
```

**Status: 200 OK**

### ❓ Felsökning

**Problem: 404 Not Found**
- Kolla att servern körs (`npm run dev`)
- Kolla att du skrev `/notes` i URL:en (inte bara `localhost:3000`)
- Kolla stavningen i endpoint-definitionen

**Problem: Cannot GET /notes**
- Stavfel i endpoint-path?
- Glömt spara filen innan du testade?
- Om du använder nodemon: den ska auto-reload, annars starta om servern

### ✅ Kontrollera att du har:
- [ ] GET endpoint `/notes` i app.mjs
- [ ] Try-catch block för felhantering
- [ ] Returnerar korrekt format: `{ success: true, data: [...] }`
- [ ] Statuskod 200 vid success
- [ ] Statuskod 500 vid fel
- [ ] Testat i Postman och får 200 OK

---

## Övning 3: Läsa från JSON-fil (20 min)

### 🎓 Vad ska du lära dig?
Hur man läser data från en JSON-fil på ett säkert sätt.

### 📖 Kort förklaring

**Varför spara data i en fil?**
- Data försvinner inte när servern stängs av
- Enkelt för små projekt (i större projekt använder man databaser)

**Vad är JSON?**
- JSON = JavaScript Object Notation
- Ett textformat för att lagra strukturerad data
- Ser ut som JavaScript-objekt fast allt är text

**Exempel på JSON-fil (notes.json):**
```json
[
  {
    "id": "abc-123",
    "title": "Handla mat",
    "content": "Mjölk, bröd, ost",
    "timestamp": "2025-01-17T10:30:00.000Z"
  }
]
```

**Säker filläsning:**
Vi måste hantera flera potentiella problem:
1. Filen kanske inte finns
2. Filen kanske är tom
3. Filen kanske innehåller trasig JSON
4. Filen kanske inte är en array

### ✏️ Din uppgift

Skapa en funktion som läser anteckningar från fil på ett säkert sätt.

**1. Lägg till i toppen av app.mjs (efter imports):**

```javascript
import fs from 'fs';
import { fileURLToPath } from 'url';
import { dirname, join } from 'path';

// Få sökvägen till nuvarande fil
const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

// Definiera filsökväg
const notesFilePath = join(__dirname, 'notes.json');
```

**Förklaring:**
- `fs` = File System, Node.js modul för att arbeta med filer
- `__filename` och `__dirname` = Fungerar inte i ES modules, så vi skapar dem manuellt
- `join(__dirname, 'notes.json')` = Skapar fullständig sökväg: `/hela/vägen/till/notes.json`
  - **Bättre än:** `__dirname + '/notes.json'` (kan ge problem på olika OS)

**2. Skapa readNotes-funktion:**

```javascript
/**
 * Läser alla anteckningar från notes.json
 * Returnerar alltid en array, även om filen inte finns
 */
const readNotes = () => {
  try {
    // Steg 1: Kolla om filen finns
    if (!fs.existsSync(notesFilePath)) {
      console.log('Fil finns inte än, returnerar tom array');
      return [];
    }
    
    // Steg 2: Läs filens innehåll som text
    const data = fs.readFileSync(notesFilePath, 'utf-8');
    
    // Steg 3: Kolla om filen är tom
    if (data.trim().length === 0) {
      console.log('Filen är tom, returnerar tom array');
      return [];
    }
    
    // Steg 4: Parsea JSON till JavaScript
    const notes = JSON.parse(data);
    
    // Steg 5: Säkerställ att det är en array
    if (!Array.isArray(notes)) {
      console.warn('Data är inte en array, returnerar tom array');
      return [];
    }
    
    // Steg 6: Returnera anteckningarna
    return notes;
    
  } catch (error) {
    console.error('Error reading notes:', error.message);
    return []; // Returnera alltid tom array vid fel
  }
};
```

**Förklaring steg för steg:**

**Steg 1: Kolla om filen finns**
```javascript
if (!fs.existsSync(notesFilePath)) {
  return [];
}
```
- `fs.existsSync(path)` = Returnerar `true` om filen finns
- `!` = Negation, så "om filen INTE finns"
- Returnerar tom array istället för att krascha

**Steg 2: Läs filen**
```javascript
const data = fs.readFileSync(notesFilePath, 'utf-8');
```
- `fs.readFileSync(path, encoding)` = Läser filens innehåll
- `'utf-8'` = Teckenkodning (standard för text)
- `data` blir en **sträng** (text), inte JavaScript-objekt än!

**Steg 3: Kolla om tom**
```javascript
if (data.trim().length === 0) {
```
- `.trim()` = Tar bort whitespace (mellanslag, tabs, newlines)
- Om längden är 0 = filen är helt tom
- `JSON.parse("")` skulle ge fel, så vi returnerar tom array

**Steg 4: Parsea JSON**
```javascript
const notes = JSON.parse(data);
```
- `JSON.parse(text)` = Konverterar JSON-text till JavaScript
- Input: `'[{"id":"1","title":"Test"}]'` (sträng)
- Output: `[{id:"1",title:"Test"}]` (array)

**Steg 5: Kolla att det är en array**
```javascript
if (!Array.isArray(notes)) {
  return [];
}
```
- `Array.isArray(x)` = Returnerar `true` om x är en array
- Om filen innehåller ett objekt istället för array, returnerar vi tom array
- Säkerställer att vi alltid kan använda `.forEach()`, `.filter()`, etc.

**3. Uppdatera GET endpoint:**

Ändra din GET endpoint från Övning 2:

```javascript
app.get('/notes', (req, res) => {
  try {
    // NU läser vi från fil istället för tom array!
    const notes = readNotes();
    
    res.status(200).json({
      success: true,
      data: notes
    });
  } catch (error) {
    console.error('Error fetching notes:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**4. Skapa testfil:**

Skapa en fil `notes.json` i samma mapp som app.mjs:

```json
[
  {
    "id": "1",
    "title": "Handla mat",
    "content": "Mjölk, bröd, ost",
    "timestamp": "2025-01-17T10:00:00.000Z"
  },
  {
    "id": "2",
    "title": "Plugga webbserver",
    "content": "Gå igenom Express-övningar",
    "timestamp": "2025-01-17T11:00:00.000Z"
  }
]
```

**5. Testa i Postman:**

```
GET http://localhost:3000/notes
```

**Förväntat resultat:**
```json
{
  "success": true,
  "data": [
    {
      "id": "1",
      "title": "Handla mat",
      "content": "Mjölk, bröd, ost",
      "timestamp": "2025-01-17T10:00:00.000Z"
    },
    {
      "id": "2",
      "title": "Plugga webbserver",
      "content": "Gå igenom Express-övningar",
      "timestamp": "2025-01-17T11:00:00.000Z"
    }
  ]
}
```

### 🧪 Extra test:

**Test 1: Radera notes.json**
- Radera filen
- Kör GET request igen
- Förväntat: `{ "success": true, "data": [] }`
- Kolla server console: Ser du "Fil finns inte än..."?

**Test 2: Tom fil**
- Skapa tom notes.json (inget innehåll)
- Kör GET request
- Förväntat: `{ "success": true, "data": [] }`
- Kolla server console: Ser du "Filen är tom..."?

**Test 3: Trasig JSON**
- Skriv "hejsan" i notes.json (inte valid JSON)
- Kör GET request
- Förväntat: `{ "success": true, "data": [] }`
- Kolla server console: Ser du "Error reading notes:"?

**Poäng:** Din funktion hanterar alla dessa fall utan att krascha! 🎉

### ✅ Kontrollera att du har:
- [ ] Import av fs, fileURLToPath, dirname, join
- [ ] `notesFilePath` definierad med `join()`
- [ ] `readNotes()` funktion med alla 6 steg
- [ ] Try-catch i readNotes
- [ ] Uppdaterad GET endpoint som använder readNotes()
- [ ] notes.json fil med testdata
- [ ] Testat i Postman och ser testdata
- [ ] Testat att det fungerar när fil saknas/är tom/är trasig

---

## Övning 4: POST endpoint med validering (25 min)

### 🎓 Vad ska du lära dig?
Hur man tar emot data, validerar den, och sparar till fil.

### 📖 Kort förklaring

**POST endpoint = Skapa ny data**
- Tar emot data i request body
- Validerar att datan är korrekt
- Sparar till fil/databas
- Returnerar den skapade datan

**Varför validera?**
1. **Säkerhet:** Användare kan skicka vad som helst
2. **Datakvalitet:** Säkerställ att data är i rätt format
3. **Buggar:** Förhindra att trasig data sparas

**Exempel på validering:**
- Kolla att alla fält finns
- Kolla att title inte är tom
- Kolla att content inte är för långt

**UUID för ID:**
- UUID = Universally Unique Identifier
- Garanterat unikt ID: `"abc-123-def-456-..."`
- Bättre än array-index (som ändras vid radering)

### ✏️ Din uppgift

Skapa en POST endpoint som tar emot en ny anteckning, validerar den, och sparar till fil.

**1. Lägg till UUID-import i toppen av app.mjs:**

```javascript
import { v4 as uuidv4 } from 'uuid';
```

**Förklaring:**
- `uuid` paketet har flera versioner (v1, v4, v5)
- Vi använder v4 (slumpmässig)
- `as uuidv4` = vi döper om `v4` till `uuidv4` för tydlighet

**2. Skapa saveNotes-funktion:**

```javascript
/**
 * Sparar anteckningar till notes.json
 * @param {Object} note - Anteckning att lägga till
 * @returns {boolean} true om lyckad, false om fel
 */
const saveNote = (note) => {
  try {
    // Steg 1: Läs befintliga anteckningar
    const notes = readNotes();
    
    // Steg 2: Lägg till den nya anteckningen
    notes.push(note);
    
    // Steg 3: Konvertera till JSON-text (med formatering)
    const jsonData = JSON.stringify(notes, null, 2);
    
    // Steg 4: Skriv till fil
    fs.writeFileSync(notesFilePath, jsonData, 'utf-8');
    
    console.log('Note saved:', note.id);
    return true;
    
  } catch (error) {
    console.error('Error saving note:', error.message);
    return false;
  }
};
```

**Förklaring:**

**Steg 1: Läs befintliga**
```javascript
const notes = readNotes();
```
- Återanvänder vår readNotes-funktion (DRY = Don't Repeat Yourself!)
- Om filen inte finns får vi tom array `[]`

**Steg 2: Lägg till ny**
```javascript
notes.push(note);
```
- `.push()` = Lägger till element sist i array
- Före: `[note1, note2]`
- Efter: `[note1, note2, note3]`

**Steg 3: Konvertera till JSON**
```javascript
const jsonData = JSON.stringify(notes, null, 2);
```
- `JSON.stringify(data, replacer, space)` = JavaScript → JSON-text
- `null` = ingen replacer (används sällan)
- `2` = indentationsnivå (gör JSON läsbart)

**Jämförelse:**
```javascript
// Utan formatering:
JSON.stringify(notes)
// Output: '[{"id":"1","title":"Test"}]' (en lång rad)

// Med formatering:
JSON.stringify(notes, null, 2)
// Output:
// [
//   {
//     "id": "1",
//     "title": "Test"
//   }
// ]
```

**Steg 4: Skriv till fil**
```javascript
fs.writeFileSync(notesFilePath, jsonData, 'utf-8');
```
- `fs.writeFileSync(path, data, encoding)` = Skriver data till fil
- **OBS:** Skriver ÖVER befintlig fil (ersätter helt)
- `'utf-8'` = Teckenkodning

**3. Skapa POST endpoint:**

```javascript
// Endpoint: POST /notes - Skapa ny anteckning
app.post('/notes', (req, res) => {
  try {
    // Steg 1: Hämta data från request body
    const { title, content } = req.body;
    console.log('Received data:', req.body);
    
    // Steg 2: Validering - Kolla att alla fält finns
    if (!title || !content) {
      return res.status(400).json({
        success: false,
        message: 'Title och content krävs'
      });
    }
    
    // Steg 3: Validering - Kolla att title inte är tom
    if (title.trim().length === 0) {
      return res.status(400).json({
        success: false,
        message: 'Title kan inte vara tom'
      });
    }
    
    // Steg 4: Validering - Kolla att content inte är för lång
    if (content.length > 500) {
      return res.status(400).json({
        success: false,
        message: 'Content får max vara 500 tecken'
      });
    }
    
    // Steg 5: Skapa antecknings-objekt med genererade värden
    const newNote = {
      id: uuidv4(),                       // Unikt ID
      title: title.trim(),                 // Trimmat (bort whitespace)
      content: content.trim(),
      timestamp: new Date().toISOString()  // ISO format: "2025-01-17T10:30:00.000Z"
    };
    
    // Steg 6: Spara till fil
    const saved = saveNote(newNote);
    
    if (!saved) {
      return res.status(500).json({
        success: false,
        message: 'Kunde inte spara anteckning'
      });
    }
    
    // Steg 7: Returnera den skapade anteckningen
    res.status(201).json({
      success: true,
      data: newNote
    });
    
  } catch (error) {
    console.error('Error creating note:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring detaljerat:**

**Steg 1: Destructuring**
```javascript
const { title, content } = req.body;
```
- Plockar ut `title` och `content` från `req.body`
- Istället för: `const title = req.body.title; const content = req.body.content;`
- **OBS:** `req.body` fungerar BARA om du har `app.use(express.json())`!

**Steg 2-4: Validering**
```javascript
if (!title || !content) {
```
- `!title` = true om title är: undefined, null, "" (tom sträng), 0, false
- `||` = OR operator, "eller"
- Om NÅGOT fält saknas → returnera 400 Bad Request

```javascript
return res.status(400).json({ ... });
```
- `return` = Avslutar funktionen här (koden efter körs INTE)
- `400` = Bad Request (klienten skickade fel data)
- **Varför 400 och inte 500?** Klienten gjorde fel, inte servern!

```javascript
if (title.trim().length === 0) {
```
- `.trim()` = Tar bort whitespace från början och slutet
- Input: `"  hello  "` → Output: `"hello"`
- Om längden är 0 efter trim = bara mellanslag/tabs = tom!

**Steg 5: Skapa objekt**
```javascript
const newNote = {
  id: uuidv4(),
```
- `uuidv4()` = Genererar unikt ID
- Output: `"a3d5b2c1-4e5f-6a7b-8c9d-0e1f2a3b4c5d"`

```javascript
title: title.trim(),
```
- Sparar trimmat värde (utan onödigt whitespace)

```javascript
timestamp: new Date().toISOString()
```
- `new Date()` = Skapar Date-objekt med nuvarande tid
- `.toISOString()` = Konverterar till ISO 8601 format
- Output: `"2025-01-17T10:30:00.000Z"`

**Varför ISO-format?**
- Internationell standard
- Sorteras korrekt alfabetiskt
- Innehåller timezone (Z = UTC)
- Kan parseas av `new Date()` i JavaScript

**Steg 6: Spara och kontrollera**
```javascript
const saved = saveNote(newNote);

if (!saved) {
  return res.status(500).json({ ... });
}
```
- `saveNote()` returnerar `true` om lyckad, `false` om fel
- Om `false` → något gick fel i filskrivningen → 500 error

**Steg 7: Returnera skapad data**
```javascript
res.status(201).json({
  success: true,
  data: newNote
});
```
- `201` = Created (ny resurs skapades)
- **Varför inte 200?** 200 = OK (generell framgång), 201 = specifikt för skapande
- Returnerar hela objektet inklusive genererat ID och timestamp

**4. Testa i Postman:**

**Setup:**
- Metod: **POST**
- URL: `http://localhost:3000/notes`
- Gå till **Body** tab
- Välj **raw**
- Välj **JSON** i dropdown

**Request body:**
```json
{
  "title": "Min första anteckning",
  "content": "Detta är innehållet i anteckningen"
}
```

**Tryck Send**

**Förväntat resultat:**
```json
{
  "success": true,
  "data": {
    "id": "a3d5b2c1-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
    "title": "Min första anteckning",
    "content": "Detta är innehållet i anteckningen",
    "timestamp": "2025-01-17T12:45:00.000Z"
  }
}
```

**Status: 201 Created**

**Kontrollera:**
1. Öppna `notes.json` - ser du den nya anteckningen?
2. Kör GET request - ser du anteckningen i listan?

### 🧪 Testa valideringen:

**Test 1: Saknat fält**
```json
{
  "title": "Bara titel"
}
```
**Förväntat:** 400 Bad Request, "Title och content krävs"

**Test 2: Tom title**
```json
{
  "title": "   ",
  "content": "Innehåll"
}
```
**Förväntat:** 400 Bad Request, "Title kan inte vara tom"

**Test 3: För långt content**
```json
{
  "title": "Test",
  "content": "Ett jättelångt innehåll med över 500 tecken..."
}
```
**Förväntat:** 400 Bad Request, "Content får max vara 500 tecken"

**Test 4: Giltig data**
```json
{
  "title": "Giltig titel",
  "content": "Giltigt innehåll"
}
```
**Förväntat:** 201 Created med hela objektet

### ❓ Felsökning

**Problem: req.body är undefined**
- Kolla att du har `app.use(express.json())` i app.mjs
- Kolla att du valde "raw" och "JSON" i Postman Body-tab

**Problem: "Title och content krävs" trots att jag skickar data**
- Kolla stavningen: är det exakt `title` och `content`?
- Kolla JSON-syntaxen: dubbla citattecken `"`, kommatecken mellan fält

**Problem: Filen sparas inte**
- Kolla console - finns det ett felmeddelande?
- Kolla att `notesFilePath` är korrekt
- Kolla filrättigheter (kan Node skriva till mappen?)

### ✅ Kontrollera att du har:
- [ ] Import av uuid
- [ ] `saveNote()` funktion som sparar till fil
- [ ] POST endpoint `/notes`
- [ ] Validering: alla fält finns
- [ ] Validering: title inte tom
- [ ] Validering: content inte för lång
- [ ] Genererar ID med uuidv4()
- [ ] Genererar timestamp med .toISOString()
- [ ] Trimmar title och content
- [ ] Returnerar 400 vid valideringsfel
- [ ] Returnerar 201 vid framgång
- [ ] Testat i Postman med giltig och ogiltig data
- [ ] Verifierat att data sparas i notes.json

---

## Övning 5: DELETE endpoint (20 min)

### 🎓 Vad ska du lära dig?
Hur man raderar data baserat på ID från URL-parametrar.

### 📖 Kort förklaring

**DELETE endpoint = Radera data**
- Tar emot ID från URL:en (inte body!)
- Hittar och raderar objektet med det ID:t
- Returnerar 200 om lyckad, 404 om inte hittad

**URL-parametrar:**
```
DELETE /notes/:id
       ↑      ↑
    path  parameter
```

- `:id` = dynamisk parameter (kan vara vilket värde som helst)
- `/notes/abc-123` → `req.params.id` = `"abc-123"`
- `/notes/xyz-789` → `req.params.id` = `"xyz-789"`

**Array.filter() för radering:**
```javascript
[1, 2, 3, 4].filter(x => x !== 3)
// Output: [1, 2, 4]
// (3:an är borta!)
```

### ✏️ Din uppgift

Skapa en DELETE endpoint som raderar en anteckning baserat på ID.

**1. Skapa deleteNote-funktion:**

```javascript
/**
 * Raderar en anteckning baserat på ID
 * @param {string} noteId - ID på anteckning att radera
 * @returns {boolean} true om raderad, false om inte hittad
 */
const deleteNote = (noteId) => {
  try {
    // Steg 1: Kolla om filen finns
    if (!fs.existsSync(notesFilePath)) {
      console.log('File does not exist');
      return false;
    }
    
    // Steg 2: Läs alla anteckningar
    const notes = readNotes();
    
    // Steg 3: Filtrera bort anteckningen med matchande ID
    const filteredNotes = notes.filter(note => note.id !== noteId);
    
    // Steg 4: Kolla om något faktiskt raderades
    if (notes.length === filteredNotes.length) {
      // Längderna är samma = inget raderades = ID hittades inte
      console.log('Note not found:', noteId);
      return false;
    }
    
    // Steg 5: Spara den uppdaterade listan
    const jsonData = JSON.stringify(filteredNotes, null, 2);
    fs.writeFileSync(notesFilePath, jsonData, 'utf-8');
    
    console.log('Note deleted:', noteId);
    return true;
    
  } catch (error) {
    console.error('Error deleting note:', error.message);
    return false;
  }
};
```

**Förklaring detaljerat:**

**Steg 3: filter()**
```javascript
const filteredNotes = notes.filter(note => note.id !== noteId);
```

**Hur filter() fungerar:**
```javascript
// Exempel:
const notes = [
  { id: "1", title: "A" },
  { id: "2", title: "B" },
  { id: "3", title: "C" }
];

// Radera ID "2":
const noteId = "2";
const filtered = notes.filter(note => note.id !== noteId);

// Filter går igenom varje element:
// note.id = "1" → "1" !== "2" → true → BEHÅLL
// note.id = "2" → "2" !== "2" → false → RADERA
// note.id = "3" → "3" !== "2" → true → BEHÅLL

// Resultat:
// [
//   { id: "1", title: "A" },
//   { id: "3", title: "C" }
// ]
```

**Arrow function kort form:**
```javascript
note => note.id !== noteId

// Samma som:
(note) => {
  return note.id !== noteId;
}

// Samma som:
function(note) {
  return note.id !== noteId;
}
```

**Steg 4: Kontrollera om något raderades**
```javascript
if (notes.length === filteredNotes.length) {
  return false;
}
```
- Före radering: 3 anteckningar
- Efter radering (om ID hittades): 2 anteckningar
- Efter radering (om ID INTE hittades): 3 anteckningar (oförändrat!)
- Genom att jämföra längder vet vi om något raderades

**2. Skapa DELETE endpoint:**

```javascript
// Endpoint: DELETE /notes/:id - Radera anteckning
app.delete('/notes/:id', (req, res) => {
  try {
    // Steg 1: Hämta ID från URL-parameter
    const noteId = req.params.id;
    console.log('Deleting note:', noteId);
    
    // Steg 2: Försök radera anteckningen
    const deleted = deleteNote(noteId);
    
    // Steg 3: Kontrollera om raderingen lyckades
    if (deleted) {
      // Lyckades - anteckningen hittades och raderades
      res.status(200).json({
        success: true,
        message: 'Anteckning raderad'
      });
    } else {
      // Misslyckades - anteckningen hittades inte
      res.status(404).json({
        success: false,
        message: 'Anteckning hittades inte'
      });
    }
    
  } catch (error) {
    console.error('Error in DELETE endpoint:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

**URL-parameter:**
```javascript
app.delete('/notes/:id', ...)
//                  ^^^
//                  Detta blir req.params.id

// Exempel:
// DELETE /notes/abc-123 → req.params.id = "abc-123"
// DELETE /notes/xyz-789 → req.params.id = "xyz-789"
```

**Statuskoder:**
```javascript
if (deleted) {
  res.status(200).json({ ... });  // 200 OK
} else {
  res.status(404).json({ ... });  // 404 Not Found
}
```
- **200:** Raderingen lyckades
- **404:** Anteckningen med det ID:t finns inte
- **500:** (i catch) Server error (fil kan inte skrivas, etc.)

**3. Testa i Postman:**

**Förberedelse:**
1. Kör GET request - se vilka anteckningar som finns
2. Kopiera ett ID (t.ex. `"a3d5b2c1-4e5f-6a7b-8c9d-0e1f2a3b4c5d"`)

**Delete request:**
- Metod: **DELETE**
- URL: `http://localhost:3000/notes/a3d5b2c1-4e5f-6a7b-8c9d-0e1f2a3b4c5d`
  - (byt ut ID:t mot ett som finns i din fil!)

**Tryck Send**

**Förväntat resultat:**
```json
{
  "success": true,
  "message": "Anteckning raderad"
}
```

**Status: 200 OK**

**Kontrollera:**
1. Kör GET request igen - är anteckningen borta?
2. Öppna notes.json - är anteckningen borta där också?

### 🧪 Testa olika scenarios:

**Test 1: Radera existerande anteckning**
- Använd ett ID som finns
- **Förväntat:** 200 OK, meddelande "Anteckning raderad"
- Verifiera med GET att den är borta

**Test 2: Radera icke-existerande anteckning**
```
DELETE http://localhost:3000/notes/felaktigt-id-som-inte-finns
```
- **Förväntat:** 404 Not Found, meddelande "Anteckning hittades inte"

**Test 3: Radera alla anteckningar en efter en**
- Kör GET för att se alla ID:n
- DELETE varje ID
- Till slut: GET ska returnera tom array

### ❓ Felsökning

**Problem: 404 även om ID:t finns**
- Kolla att du kopierade HELA ID:t (UUID är långt!)
- Kolla att det inte finns mellanslag före/efter ID:t
- Kolla console: `console.log('Deleting note:', noteId);`

**Problem: Filen töms helt**
- Har du filter-logiken rätt? `note.id !== noteId` (INTE `===`)
- Kolla console för felmeddelanden

**Problem: 500 error**
- Kolla console - vilket specifikt fel?
- Kan filen skrivas? (filrättigheter)

### ✅ Kontrollera att du har:
- [ ] `deleteNote()` funktion med filter-logik
- [ ] Kontroll om filen finns
- [ ] Kontroll om något faktiskt raderades (längdjämförelse)
- [ ] DELETE endpoint med `:id` parameter
- [ ] Hämtar ID från `req.params.id`
- [ ] Returnerar 200 om lyckad
- [ ] Returnerar 404 om inte hittad
- [ ] Testat med existerande ID (200)
- [ ] Testat med icke-existerande ID (404)
- [ ] Verifierat med GET att anteckning är borta

---

## Övning 6: Copy-paste övning - Från Notes till Tasks (30 min)

### 🎓 Vad ska du lära dig?
Hur man kopierar kod från ett projekt till ett annat och uppdaterar ALLT korrekt. Detta är en vanlig uppgift i verkliga projekt, och många gör misstag här!

### 📖 Varför är detta viktigt?

**I första provet:**
- En elev kopierade från bokrecension-projektet
- Glömde uppdatera `reviews` → `training` på några ställen
- Glömde ändra `saveReview()` → `saveTraining()`
- Koden funkade inte!

**Lärdom:**  
När du kopierar kod måste du systematiskt uppdatera ALLA referenser till gamla namn!

### ✏️ Din uppgift

Du ska kopiera din notes-api och skapa ett nytt tasks-api (uppgifter/todos).

**Datamodell för Task:**
```json
{
  "id": "uuid",
  "task": "Städa rummet",
  "completed": false,
  "priority": "high",
  "timestamp": "2025-01-17T10:00:00.000Z"
}
```

**Steg 1: Kopiera projektet**

```bash
# Gå upp en nivå från notes-api
cd ..

# Kopiera hela mappen
cp -r notes-api tasks-api

# Gå in i nya mappen
cd tasks-api
```

**Steg 2: Rensa gamla data**

```bash
# Radera gamla anteckningar
rm notes.json

# (Windows)
del notes.json
```

**Steg 3: Använd Sök och ersätt**

Öppna `app.mjs` i VS Code:

**Metod 1: Sök och ersätt UI**
1. Tryck `Ctrl+H` (Windows/Linux) eller `Cmd+H` (Mac)
2. Find: `notes`
3. Replace: `tasks`
4. Tryck "Replace All"

**Metod 2: Manuell sökning**
1. Tryck `Ctrl+F`
2. Sök efter `notes`
3. Gå igenom varje träff och uppdatera manuellt

**Steg 4: Vad behöver ändras**

Gå igenom filen systematiskt:

**Filsökväg:**
```javascript
// FÖRE:
const notesFilePath = join(__dirname, 'notes.json');

// EFTER:
const tasksFilePath = join(__dirname, 'tasks.json');
```

**Funktionsnamn:**
```javascript
// FÖRE:
const readNotes = () => { ... }
const saveNote = (note) => { ... }
const deleteNote = (noteId) => { ... }

// EFTER:
const readTasks = () => { ... }
const saveTask = (task) => { ... }
const deleteTask = (taskId) => { ... }
```

**Endpoint paths:**
```javascript
// FÖRE:
app.get('/notes', ...)
app.post('/notes', ...)
app.delete('/notes/:id', ...)

// EFTER:
app.get('/tasks', ...)
app.post('/tasks', ...)
app.delete('/tasks/:id', ...)
```

**Variabelnamn:**
```javascript
// I readTasks:
const tasks = JSON.parse(data);  // INTE notes!
return tasks;

// I GET endpoint:
const tasks = readTasks();  // INTE notes!
res.status(200).json({ success: true, data: tasks });

// I POST endpoint:
const newTask = { ... };  // INTE newNote!
saveTask(newTask);  // INTE saveNote!

// osv...
```

**Request body fields:**
```javascript
// FÖRE:
const { title, content } = req.body;

// EFTER:
const { task, priority } = req.body;
```

**Validering:**
```javascript
// FÖRE:
if (!title || !content) {
  return res.status(400).json({
    message: 'Title och content krävs'
  });
}

// EFTER:
if (!task || !priority) {
  return res.status(400).json({
    message: 'Task och priority krävs'
  });
}

// Lägg till validering för priority:
if (!['low', 'medium', 'high'].includes(priority)) {
  return res.status(400).json({
    message: 'Priority måste vara low, medium eller high'
  });
}
```

**Nytt objekt:**
```javascript
// FÖRE:
const newNote = {
  id: uuidv4(),
  title: title.trim(),
  content: content.trim(),
  timestamp: new Date().toISOString()
};

// EFTER:
const newTask = {
  id: uuidv4(),
  task: task.trim(),
  completed: false,  // Alltid false när man skapar
  priority,
  timestamp: new Date().toISOString()
};
```

**Steg 5: Checklista - Gå igenom ALLT**

Använd denna checklista och bocka av:

- [ ] `notesFilePath` → `tasksFilePath`
- [ ] `notes.json` → `tasks.json`
- [ ] `readNotes()` → `readTasks()`
- [ ] `saveNote()` → `saveTask()`
- [ ] `deleteNote()` → `deleteTask()`
- [ ] `note` parameter → `task` parameter
- [ ] `noteId` parameter → `taskId` parameter
- [ ] `/notes` endpoints → `/tasks` endpoints
- [ ] `notes` variabel → `tasks` variabel (i alla funktioner!)
- [ ] `newNote` → `newTask`
- [ ] `{ title, content }` → `{ task, priority }`
- [ ] Validering uppdaterad för nya fält
- [ ] Console.log meddelanden uppdaterade
- [ ] Kommentarer uppdaterade

**Steg 6: Testa systematiskt**

**Test 1: Starta servern**
```bash
npm run dev
```
- [ ] Servern startar utan fel?
- [ ] Inga varningar i console?

**Test 2: GET /tasks**
```
GET http://localhost:3000/tasks
```
- [ ] Returnerar `{ success: true, data: [] }`?
- [ ] Status 200?

**Test 3: POST /tasks**
```json
{
  "task": "Städa rummet",
  "priority": "high"
}
```
- [ ] Returnerar `{ success: true, data: { id, task, completed: false, priority, timestamp } }`?
- [ ] Status 201?
- [ ] tasks.json fil skapad?
- [ ] Data finns i filen?

**Test 4: GET /tasks igen**
- [ ] Ser du den skapade tasken?

**Test 5: DELETE /tasks/:id**
- [ ] Använd ID från POST-responsen
- [ ] Returnerar `{ success: true }`?
- [ ] Status 200?
- [ ] Task borta från filen?

**Test 6: Validering**

Testa ogiltig data:
```json
{ "task": "Test" }
```
- [ ] 400 error: "Task och priority krävs"?

```json
{ "task": "Test", "priority": "invalid" }
```
- [ ] 400 error: "Priority måste vara low, medium eller high"?

### 🎯 Vanliga misstag att kolla efter

**Misstag 1: Glömmer uppdatera funktionsanrop**
```javascript
// I POST endpoint:
const newTask = { ... };
saveNote(newTask);  // ❌ Funktionen heter saveTask nu!
```

**Misstag 2: Blandar gamla och nya variabelnamn**
```javascript
const tasks = readTasks();
res.status(200).json({ data: notes });  // ❌ Ska vara tasks!
```

**Misstag 3: Glömmer uppdatera i return-statements**
```javascript
const readTasks = () => {
  // ...
  return notes;  // ❌ Ska vara tasks!
}
```

**Misstag 4: Glömmer console.log**
```javascript
console.log('Note saved:', task.id);  // ❌ Förvirrande! Säg "Task saved"
```

### 💡 Pro-tips för copy-paste

**1. Gör en plan först**
Skriv ner:
- Gamla namn → Nya namn
- `notes` → `tasks`
- `note` → `task`
- `Note` → `Task`

**2. Använd Sök och ersätt smart**
- Sök efter hela ord (aktivera "Match Whole Word")
- Gör en typ åt gången (först alla `notes`, sen `note`, sen `Note`)

**3. Testa efter varje större ändring**
- Bytte funktionsnamn? Testa!
- Bytte endpoint paths? Testa!

**4. Använd console.log**
```javascript
console.log('Function:', 'readTasks');  // Så du vet vilken funktion som körs
console.log('Data:', tasks);  // Så du ser vad variabeln innehåller
```

**5. Läs igenom koden radför rad**
Även om Sök och ersätt är användbart, läs igenom koden efter för att säkerställa att allt är rätt!

### ✅ Slutkontroll

Innan du är klar:

**Kod:**
- [ ] Alla funktionsnamn uppdaterade
- [ ] Alla variabelnamn uppdaterade
- [ ] Alla endpoint paths uppdaterade
- [ ] Alla request body fields uppdaterade
- [ ] Validering stämmer med nya fält
- [ ] Console.log meddelanden stämmer

**Funktionalitet:**
- [ ] Alla tester i Postman fungerar
- [ ] tasks.json skapas korrekt
- [ ] Data sparas och läses korrekt
- [ ] Radering fungerar
- [ ] Validering fungerar

**Om ALLT är ✅ - Grattis! Du kan nu kopiera och uppdatera kod professionellt! 🎉**

---

## Övning 7: Hitta stavfelen (15 min)

### 🎓 Vad ska du lära dig?
Att hitta och fixa vanliga stavfel som är lätta att missa men som orsakar buggar.

### 📖 Varför är detta viktigt?

**JavaScript varnar INTE för:**
- Stavfel i egenskapsnamn: `array.lenght` → `undefined` (inget fel kastas!)
- Stavfel i variabelnamn: `filepath` vs `filePath` → `undefined`
- Stavfel i endpoint paths: `/trainig` vs `/training` → 404 error

**I första provet:**
- `training.lenght` → skulle alltid vara `undefined`!
- `filepath` → undefined om du definierat `filePath`
- `/trainig/:id` → matchar inte när klient anropar `/training/:id`

### ✏️ Din uppgift

Här är kod med **8 stavfel**. Hitta alla och fixa dem!

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

// STAVFEL 1: Vilket ord är fel här?
const bookPath = join(__dirname, 'books.json');

const readBooks = () => {
  try {
    // STAVFEL 2: Vilket variabelnamn är fel?
    if (!fs.existsSync(bokPath)) {
      return [];
    }
    
    const data = fs.readFileSync(bookPath, 'utf-8');
    const books = JSON.parse(data);
    
    // STAVFEL 3: Vilket ord är fel stavat?
    if (!Array.isArray(books)) return [];
    
    return books;
  } catch (error) {
    console.error('Error:', error);
    return [];
  }
};

const deleteBook = (bookId) => {
  try {
    const books = readBooks();
    
    // STAVFEL 4: Vilket ord är fel stavat här?
    const filtered = books.filter(book => book.id !== bookId);
    
    // STAVFEL 5: Vilket ord är fel stavat?
    if (books.lenght === filtered.lenght) {
      return false;
    }
    
    fs.writeFileSync(bookPath, JSON.stringify(filtered, null, 2));
    return true;
  } catch (error) {
    console.error('Error:', error);
    return false;
  }
};

// STAVFEL 6: Fel i endpoint path
app.get('/boks', (req, res) => {
  try {
    const books = readBooks();
    res.status(200).json({ success: true, data: books });
  } catch (error) {
    res.status(500).json({ success: false });
  }
});

app.post('/books', (req, res) => {
  try {
    const { title, autor } = req.body;  // STAVFEL 7: Fel fältnamn
    
    if (!title || !autor) {
      return res.status(400).json({
        success: false,
        message: 'Title and autor required'  // STAVFEL 8: Samma fel
      });
    }
    
    const newBook = {
      id: uuidv4(),
      title,
      autor,  // STAVFEL (fortsättning av 7 och 8)
      timestamp: new Date().toISOString()
    };
    
    // Spara-logik här...
    
    res.status(201).json({ success: true, data: newBook });
  } catch (error) {
    res.status(500).json({ success: false });
  }
});

export default app;
```

### 🔍 Hitta felen

Gå igenom koden radför rad och hitta alla 8 stavfel!

**Skriv ner:**
1. Vilket fel?
2. På vilken rad?
3. Vad är rätt?

<details>
<summary>💡 Tips (läs om du fastnar)</summary>

Leta efter:
- Ord stavade annorlunda på olika ställen (t.ex. `bookPath` vs `bokPath`)
- Engelska ord stavade fel (t.ex. `author`)
- Vanliga JavaScript-egenskaper stavade fel (t.ex. `length`)
- Endpoint paths som inte matchar (singular/plural)

</details>

<details>
<summary>✅ Visa lösning</summary>

**Alla 8 stavfel:**

**1. Ingen stavfel ännu! (detta var för att kolla att du är uppmärksam 😊)**

**2. Rad 20: `bokPath` → ska vara `bookPath`**
```javascript
// FEL:
if (!fs.existsSync(bokPath)) {

// RÄTT:
if (!fs.existsSync(bookPath)) {
```

**3. Ingen stavfel här heller!**

**4. Inga stavfel i filter-raden**

**5. Rad 39-40: `lenght` → ska vara `length` (BÅDA ställen)**
```javascript
// FEL:
if (books.lenght === filtered.lenght) {

// RÄTT:
if (books.length === filtered.length) {
```

**6. Rad 50: `/boks` → ska vara `/books`**
```javascript
// FEL:
app.get('/boks', (req, res) => {

// RÄTT:
app.get('/books', (req, res) => {
```

**7-8. Rad 60, 63, 64, 68: `autor` → ska vara `author`**
```javascript
// FEL:
const { title, autor } = req.body;

if (!title || !autor) {
  return res.status(400).json({
    message: 'Title and autor required'
  });
}

const newBook = {
  id: uuidv4(),
  title,
  autor,
  ...
};

// RÄTT:
const { title, author } = req.body;

if (!title || !author) {
  return res.status(400).json({
    message: 'Title and author required'
  });
}

const newBook = {
  id: uuidv4(),
  title,
  author,
  ...
};
```

**Sammanfattning av alla fel:**
1. `bokPath` → `bookPath` (liten vs stor P, svenskt vs engelskt)
2. `lenght` → `length` (2 ställen)
3. `/boks` → `/books`
4. `autor` → `author` (4 ställen)

**Varför är dessa farliga?**
- **bokPath:** `undefined` → filoperationer kraschar
- **lenght:** `undefined` → if-satsen fungerar aldrig som tänkt
- **/boks:** GET request till `/books` får 404
- **autor:** Om frontend skickar `author` blir det `undefined`

</details>

### 🎯 Reflektion

**Frågor att tänka på:**

1. Hur många av felen hittade du första gången?
2. Vilka var svårast att hitta?
3. Hur hade dessa fel yttrat sig när du testade koden?

**Lärdom:**
- Använd VS Code autocomplete/IntelliSense (tryck Tab när förslag visas)
- Var konsekvent med namngivning (camelCase)
- Kolla stavning extra noga för vanliga JavaScript-egenskaper: `length`, `filter`, `forEach`
- Testa din kod ofta så du hittar fel direkt!

---

## Övning 8: Syntax-jakt (10 min)

### 🎓 Vad ska du lära dig?
Att känna igen och fixa syntax-fel som hindrar koden från att köra.

### 📖 Varför är detta viktigt?

**Syntax-fel = Koden körs inte alls!**

Node ger felmeddelande typ:
```
SyntaxError: Unexpected token '{'
```

**Vanliga syntax-fel:**
- Glömmer `=>` i arrow functions
- Använder fel citattecken för template literals
- Backticks på fel ställe i funktionsanrop

### ✏️ Din uppgift

Här är kod med **5 syntax-fel**. Hitta och fixa alla!

```javascript
import express from 'express';

const app = express();
const PORT = 3000;

// SYNTAX-FEL 1: Något saknas här
const greet = (name) {
  return `Hello, ${name}!`;
};

// SYNTAX-FEL 2: Fel typ av citattecken
console.log("Server starting on port ${PORT}");

const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
];

// SYNTAX-FEL 3: Något saknas i denna funktion också
const findUser = (id) => users.find(user => user.id === id);

app.get('/users/:id', (req, res) {
  const userId = parseInt(req.params.id);
  const user = findUser(userId);
  
  if (user) {
    // SYNTAX-FEL 4: Backticks på fel ställe
    res.json`{ success: true, data: ${user} }`;
  } else {
    res.status(404).json({ success: false });
  }
});

// SYNTAX-FEL 5: Fel citattecken här också
app.listen(PORT, () => {
  console.log('Server running on port: ${PORT}');
});

export default app;
```

### 🔍 Hitta felen

Vilka rader har syntax-fel? Vad är felet?

<details>
<summary>💡 Tips</summary>

Leta efter:
- Arrow functions: `() =>` (glöm inte pilen!)
- Template literals: `` ` `` (använd backticks, inte `"` eller `'`)
- Funktionsanrop: `func()` (använd parenteser, inte backticks!)

</details>

<details>
<summary>✅ Visa lösning</summary>

**Alla 5 syntax-fel:**

**1. Rad 7: Saknar `=>` i arrow function**
```javascript
// FEL:
const greet = (name) {

// RÄTT:
const greet = (name) => {
```

**2. Rad 12: Regular quotes istället för backticks**
```javascript
// FEL:
console.log("Server starting on port ${PORT}");

// RÄTT:
console.log(`Server starting on port ${PORT}`);
```

**3. Ingen syntax-fel på rad 20! (checkade om du var uppmärksam)**
```javascript
const findUser = (id) => users.find(user => user.id === id);
```
Detta är korrekt! Arrow function med implicit return.

**4. Rad 28: Backticks direkt efter json (tagged template literal)**
```javascript
// FEL:
res.json`{ success: true, data: ${user} }`;

// RÄTT:
res.json({ success: true, data: user });
```

**OBS:** Vi behöver inte template literal här! Vi vill skicka ett objekt, inte en sträng.

**5. Rad 37: Regular quotes istället för backticks**
```javascript
// FEL:
console.log('Server running on port: ${PORT}');

// RÄTT:
console.log(`Server running on port: ${PORT}`);
```

**Sammanfattning:**
- 2 fel med arrow functions (saknar `=>`)
- 2 fel med template literals (fel citattecken)
- 1 fel med funktionsanrop (backticks istället för parenteser)

**Hur hade du märkt dessa?**
```bash
npm run dev
```

Node skulle ge:
```
SyntaxError: Unexpected token '{'
  at line 7
```

Servern skulle inte starta alls!

</details>

### 💡 Minnesregler

**Arrow functions:**
```javascript
const func = (params) => { }
           ^^^^^^^^^^^ ^^
           parametrar  pil!
```

**Template literals (interpolation):**
```javascript
`text ${variable} text`
^                     ^
backticks!
```

**Funktionsanrop:**
```javascript
func(args)
    ^^^
    parenteser!

INTE: func`args`  // Detta är en "tagged template literal" (avancerat)
```

---

## 🎉 Grattis!

Du har nu gått igenom alla grundläggande övningar!

### ✅ Vad kan du nu?

- [x] Sätta upp ett Express-projekt från scratch
- [x] Skapa GET, POST och DELETE endpoints
- [x] Läsa och skriva till JSON-filer på ett säkert sätt
- [x] Validera inkommande data
- [x] Generera unika ID med UUID
- [x] Använda rätt HTTP-statuskoder
- [x] Testa med Postman
- [x] Kopiera och uppdatera kod korrekt
- [x] Hitta och fixa vanliga stavfel
- [x] Hitta och fixa syntax-fel

---

# 🚀 Del 2: Fortsättningskurs - MongoDB, Autentisering & Forum

**Veckor 9-20: Från JSON-filer till riktig databas och användarhantering**

---

## 📅 Terminsöversikt

### **Period 1: Grunderna (Vecka 1-4) - KLART! ✅**
- Övning 1-8: Express, JSON-filer, CRUD operations

### **Period 2: MongoDB & Databaser (Vecka 5-8)**
- Modul 1: Introduktion till MongoDB Atlas
- Modul 2: CRUD med MongoDB
- Modul 3: Data modeling & relations

### **Period 3: Autentisering & Säkerhet (Vecka 9-12)**
- Modul 4: Användarregistrering
- Modul 5: Login & Session/JWT
- Modul 6: Säkerhet & Best practices

### **Period 4: Frontend integration (Vecka 13-15)**
- Modul 7: Vite setup
- Modul 8: Axios & API integration
- Modul 9: Forms & State management

### **Period 5: Slutprojekt - Forum (Vecka 16-20)**
- Modul 10: Planering & Design
- Modul 11: Implementation
- Modul 12: Testing & Deployment
- Modul 13: Dokumentation
- Modul 14: Presentation

---

## 📚 Modul 1: Introduktion till MongoDB Atlas (Vecka 5)

### Lektion 1: Vad är MongoDB och varför databas? (60 min)

#### 🎓 Lärandemål
- Förstå skillnaden mellan JSON-filer och databaser
- Förstå vad NoSQL och MongoDB är
- Skapa MongoDB Atlas konto
- Skapa första databasen

#### 📖 Teori (15 min)

**JSON-filer vs Databaser:**

| Aspekt | JSON-filer | Databas (MongoDB) |
|--------|------------|-------------------|
| **Storlek** | Små projekt | Stora projekt |
| **Hastighet** | Långsam vid många poster | Snabb även med miljontals poster |
| **Samtidiga användare** | Problematiskt | Hanterar tusentals samtidigt |
| **Sökning** | Måste läsa hela filen | Snabba indexerade sökningar |
| **Backup** | Manuell | Automatisk |
| **Relationer** | Svårt | Inbyggt stöd |

**Exempel scenario:**

Med JSON-fil (10,000 användare):
```javascript
// Måste läsa ALLA 10,000 användare för att hitta EN:
const users = JSON.parse(fs.readFileSync('users.json'));
const user = users.find(u => u.email === 'test@test.com');
// Tid: ~50ms
```

Med MongoDB (10,000 användare):
```javascript
// Indexerad sökning, hittar direkt:
const user = await User.findOne({ email: 'test@test.com' });
// Tid: ~2ms
```

**Vad är NoSQL?**
- NoSQL = "Not Only SQL"
- SQL databaser: Tabeller med rader och kolumner (MySQL, PostgreSQL)
- NoSQL databaser: Flexibla strukturer (MongoDB, Redis)

**MongoDB:**
- NoSQL databas
- Lagrar data som "documents" (liknar JSON-objekt!)
- Flexibel schema (kan ändra struktur lätt)
- Perfekt för JavaScript-utvecklare

**Struktur i MongoDB:**
```
Database (t.ex. "forum-db")
  └── Collection (t.ex. "users")
       └── Document (t.ex. { name: "Anna", email: "anna@test.com" })
       └── Document (t.ex. { name: "Bob", email: "bob@test.com" })
```

Jämfört med JSON:
```
users.json (hela filen)
  └── Array (alla användare)
       └── Object (Anna)
       └── Object (Bob)
```

#### ✏️ Praktisk del: Skapa MongoDB Atlas konto (45 min)

**Steg 1: Registrera dig (10 min)**

1. Gå till: https://www.mongodb.com/cloud/atlas/register
2. Fyll i:
   - Email (använd din skol-email)
   - Password (stark!)
   - First name, Last name
3. Klicka "Create your Atlas account"
4. Verifiera email (kolla inbox)

**Steg 2: Skapa ett cluster (15 min)**

1. Efter inloggning: "Build a Database"
2. Välj **M0 (Free tier)**
   - Gratis för alltid!
   - Perfekt för lärande
3. Cloud Provider: **AWS**
4. Region: Välj närmaste (t.ex. Stockholm för Sverige)
5. Cluster Name: `Forum-Cluster`
6. Klicka "Create"

⏳ **Vänta 3-5 minuter medan cluster skapas...**

**Steg 3: Säkerhet - Database Access (10 min)**

1. I sidomenyn: Klicka "Database Access"
2. Klicka "Add New Database User"
3. Authentication Method: **Password**
4. Username: `forum-admin`
5. Password: Klicka "Autogenerate Secure Password"
   - **VIKTIGT:** Kopiera lösenordet och spara någonstans säkert!
6. Database User Privileges: **Read and write to any database**
7. Klicka "Add User"

**Steg 4: Säkerhet - Network Access (10 min)**

1. I sidomenyn: Klicka "Network Access"
2. Klicka "Add IP Address"
3. Välj: **"Allow Access from Anywhere"**
   - IP Address: `0.0.0.0/0`
   - (I produktion skulle vi begränsa detta!)
4. Klicka "Confirm"

**Steg 5: Hämta Connection String**

1. Gå tillbaka till "Database"
2. Ditt cluster ska nu visa "Active" (grön)
3. Klicka "Connect"
4. Välj "Connect your application"
5. Driver: **Node.js**
6. Version: **6.8 or later**
7. Kopiera Connection String:
```
mongodb+srv://forum-admin:<password>@forum-cluster.xxxxx.mongodb.net/?retryWrites=true&w=majority
```

**VIKTIGT:** Byt ut `<password>` mot lösenordet du sparade tidigare!

**Exempel:**
```
mongodb+srv://forum-admin:MySecurePass123@forum-cluster.abc12.mongodb.net/?retryWrites=true&w=majority
```

**✅ Kontrollera:**
- [ ] MongoDB Atlas konto skapat
- [ ] Cluster skapade (status: Active)
- [ ] Database user skapad
- [ ] IP whitelist konfigurerad (0.0.0.0/0)
- [ ] Connection string kopierad och lösenord infogat

---

### Lektion 2: Koppla Express till MongoDB (60 min)

#### 🎓 Lärandemål
- Installera Mongoose
- Koppla Express till MongoDB Atlas
- Skapa första schemat
- Spara och läsa data från MongoDB

#### 📖 Vad är Mongoose? (5 min)

**Mongoose** = ODM (Object Data Modeling) library för MongoDB och Node.js

**Vad gör det?**
- Förenklar kommunikation med MongoDB
- Ger struktur till data (schemas)
- Validering inbyggd
- Lättare att skriva kod

**Jämförelse:**

Utan Mongoose:
```javascript
db.collection('users').insertOne({ name: 'Anna', age: 25 })
```

Med Mongoose:
```javascript
const user = new User({ name: 'Anna', age: 25 });
await user.save();
```

#### ✏️ Övning 9: Notes API med MongoDB (55 min)

**Steg 1: Nytt projekt (5 min)**

```bash
mkdir notes-mongodb
cd notes-mongodb
npm init -y
npm install express cors mongoose dotenv
npm install nodemon --save-dev
```

**Förklaring av nya paket:**
- `mongoose`: För att prata med MongoDB
- `dotenv`: För att hantera känslig data (connection string)

**Uppdatera package.json:**
```json
{
  "type": "module",
  "scripts": {
    "dev": "nodemon server.mjs"
  }
}
```

**Steg 2: Skapa .env fil (5 min)**

Skapa fil: `.env`

```env
PORT=3000
MONGODB_URI=mongodb+srv://forum-admin:DittLösenord@forum-cluster.xxxxx.mongodb.net/notes-db?retryWrites=true&w=majority
```

**VIKTIGT:**
- Byt ut hela MONGODB_URI mot din egen connection string
- Lägg till `/notes-db` före `?` (detta blir databasens namn)
- Glöm inte byta `<password>` mot ditt riktiga lösenord!

**Skapa .gitignore:**

```
node_modules/
.env
```

**Varför .env?**
- Känslig data (lösenord) ska ALDRIG committas till GitHub
- `.gitignore` ser till att .env inte pushas
- Varje utvecklare har sin egen .env lokalt

**Steg 3: Skapa database connection (10 min)**

Skapa fil: `db.mjs`

```javascript
import mongoose from 'mongoose';
import dotenv from 'dotenv';

// Ladda miljövariabler från .env
dotenv.config();

/**
 * Kopplar till MongoDB Atlas
 */
const connectDB = async () => {
  try {
    // Koppla till databasen
    await mongoose.connect(process.env.MONGODB_URI);
    
    console.log('✅ MongoDB connected successfully');
    console.log('📊 Database:', mongoose.connection.name);
    
  } catch (error) {
    console.error('❌ MongoDB connection error:', error.message);
    process.exit(1); // Avsluta programmet om connection misslyckas
  }
};

// Lyssna på events
mongoose.connection.on('disconnected', () => {
  console.log('⚠️  MongoDB disconnected');
});

mongoose.connection.on('error', (error) => {
  console.error('❌ MongoDB error:', error);
});

export default connectDB;
```

**Förklaring:**

```javascript
dotenv.config();
```
- Läser .env filen
- Gör variabler tillgängliga via `process.env.VARIABEL_NAMN`

```javascript
await mongoose.connect(process.env.MONGODB_URI);
```
- Kopplar till MongoDB
- `await` = väntar tills kopplingen är klar
- `process.env.MONGODB_URI` = hämtar connection string från .env

```javascript
process.exit(1);
```
- Avslutar programmet
- `1` = error code (något gick fel)
- Vi vill inte köra servern om databas-kopplingen misslyckas!

**Steg 4: Skapa Schema och Model (15 min)**

Skapa mapp: `models/`

Skapa fil: `models/Note.mjs`

```javascript
import mongoose from 'mongoose';

/**
 * Schema definierar strukturen på en Note
 */
const noteSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: [true, 'Title is required'],
      trim: true,
      minlength: [1, 'Title cannot be empty'],
      maxlength: [100, 'Title cannot exceed 100 characters']
    },
    content: {
      type: String,
      required: [true, 'Content is required'],
      trim: true,
      maxlength: [500, 'Content cannot exceed 500 characters']
    }
  },
  {
    timestamps: true  // Skapar automatiskt createdAt och updatedAt
  }
);

/**
 * Model är den faktiska klassen vi använder för att
 * interagera med databasen
 */
const Note = mongoose.model('Note', noteSchema);

export default Note;
```

**Förklaring detaljerat:**

**Schema:**
```javascript
const noteSchema = new mongoose.Schema({ ... })
```
- Definierar hur ett dokument ska se ut
- Som en "mall" eller "blueprint"

**Fältdefinition:**
```javascript
title: {
  type: String,           // Datatyp
  required: [true, 'Title is required'],  // Måste finnas, error message om inte
  trim: true,             // Tar bort whitespace före/efter
  minlength: [1, 'Title cannot be empty'],
  maxlength: [100, 'Title cannot exceed 100 characters']
}
```

**Timestamps:**
```javascript
{ timestamps: true }
```
- Lägger automatiskt till:
  - `createdAt`: När dokumentet skapades
  - `updatedAt`: När dokumentet senast uppdaterades
- Du behöver inte hantera detta själv!

**Model:**
```javascript
const Note = mongoose.model('Note', noteSchema);
```
- Skapar en Model baserad på schemat
- `'Note'` = Mongoose skapar collection namnet: `notes` (plural, lowercase)
- Model är den klass vi använder: `Note.find()`, `Note.create()`, etc.

**Steg 5: Uppdatera server.mjs (5 min)**

```javascript
import app from './app.mjs';
import connectDB from './db.mjs';

const PORT = process.env.PORT || 3000;

// Koppla till databas FÖRST, sen starta server
connectDB().then(() => {
  app.listen(PORT, () => {
    console.log(`🚀 Server running on http://localhost:${PORT}`);
  });
});
```

**Förklaring:**
```javascript
connectDB().then(() => { ... });
```
- Kopplar till databas först
- `.then()` körs när kopplingen är klar
- Startar servern EFTER att databas-kopplingen lyckats

**Steg 6: Skapa app.mjs med MongoDB endpoints (15 min)**

```javascript
import express from 'express';
import cors from 'cors';
import Note from './models/Note.mjs';

const app = express();

app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: false }));

// GET /notes - Hämta alla anteckningar
app.get('/notes', async (req, res) => {
  try {
    // Hämta alla notes från databasen, sorterade efter senast skapade först
    const notes = await Note.find().sort({ createdAt: -1 });
    
    res.status(200).json({
      success: true,
      count: notes.length,
      data: notes
    });
  } catch (error) {
    console.error('Error fetching notes:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

// POST /notes - Skapa ny anteckning
app.post('/notes', async (req, res) => {
  try {
    const { title, content } = req.body;
    
    // Skapa note i databasen
    // Mongoose validerar automatiskt enligt schema!
    const note = await Note.create({
      title,
      content
    });
    
    res.status(201).json({
      success: true,
      data: note
    });
    
  } catch (error) {
    console.error('Error creating note:', error);
    
    // Hantera valideringsfel
    if (error.name === 'ValidationError') {
      const messages = Object.values(error.errors).map(err => err.message);
      return res.status(400).json({
        success: false,
        message: 'Validation error',
        errors: messages
      });
    }
    
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

// DELETE /notes/:id - Radera anteckning
app.delete('/notes/:id', async (req, res) => {
  try {
    const { id } = req.params;
    
    // findByIdAndDelete returnerar raderat dokument, eller null om inte hittat
    const note = await Note.findByIdAndDelete(id);
    
    if (!note) {
      return res.status(404).json({
        success: false,
        message: 'Note not found'
      });
    }
    
    res.status(200).json({
      success: true,
      message: 'Note deleted',
      data: note
    });
    
  } catch (error) {
    console.error('Error deleting note:', error);
    
    // Hantera ogiltigt ID-format
    if (error.name === 'CastError') {
      return res.status(400).json({
        success: false,
        message: 'Invalid note ID'
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

**Förklaring av nya koncept:**

**Mongoose metoder:**
```javascript
await Note.find()              // Hämta alla
await Note.findById(id)        // Hämta en med ID
await Note.create(data)        // Skapa ny
await Note.findByIdAndDelete(id)  // Radera med ID
await Note.findByIdAndUpdate(id, data)  // Uppdatera
```

**Sortering:**
```javascript
.sort({ createdAt: -1 })
//      ^^^^^^^^     ^^
//      field        -1 = descending (nyast först)
//                    1 = ascending (äldst först)
```

**Automatisk validering:**
```javascript
const note = await Note.create({ title, content });
```
- Mongoose kollar automatiskt mot schema!
- Om title saknas → ValidationError
- Om title är för lång → ValidationError
- Vi behöver inte skriva if-satser!

**Felhantering:**
```javascript
if (error.name === 'ValidationError') {
  const messages = Object.values(error.errors).map(err => err.message);
  // ['Title is required', 'Content is required']
}
```

**CastError:**
```javascript
if (error.name === 'CastError') {
  // ID är inte ett giltigt MongoDB ObjectID
  // t.ex. DELETE /notes/abc123 (för kort)
}
```

**Steg 7: Testa! (10 min)**

**Starta servern:**
```bash
npm run dev
```

**Förväntat i console:**
```
✅ MongoDB connected successfully
📊 Database: notes-db
🚀 Server running on http://localhost:3000
```

**I Postman:**

**1. POST /notes**
```json
{
  "title": "Min första MongoDB note",
  "content": "Detta sparas i molnet!"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
    "title": "Min första MongoDB note",
    "content": "Detta sparas i molnet!",
    "createdAt": "2025-01-17T10:00:00.000Z",
    "updatedAt": "2025-01-17T10:00:00.000Z",
    "__v": 0
  }
}
```

**Notera:**
- `_id`: MongoDB genererar automatiskt! (inget uuid behövs)
- `createdAt` och `updatedAt`: Mongoose lägger till automatiskt!
- `__v`: Version field (Mongoose använder för concurrency)

**2. GET /notes**
```
GET http://localhost:3000/notes
```

**Response:**
```json
{
  "success": true,
  "count": 1,
  "data": [
    {
      "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
      "title": "Min första MongoDB note",
      "content": "Detta sparas i molnet!",
      "createdAt": "2025-01-17T10:00:00.000Z",
      "updatedAt": "2025-01-17T10:00:00.000Z"
    }
  ]
}
```

**3. DELETE /notes/:id**
```
DELETE http://localhost:3000/notes/65a1b2c3d4e5f6g7h8i9j0k1
```
(Använd ett riktigt ID från din POST-response!)

**4. Kolla i MongoDB Atlas:**

1. Gå till MongoDB Atlas dashboard
2. Klicka "Browse Collections"
3. Välj database: `notes-db`
4. Välj collection: `notes`
5. Se din data! 🎉

**✅ Kontrollera:**
- [ ] Servern startar och kopplar till MongoDB
- [ ] POST skapar note i databasen
- [ ] GET hämtar notes från databasen
- [ ] DELETE raderar note från databasen
- [ ] Data syns i MongoDB Atlas

---

### 🏠 Hemuppgift (Vecka 5)

**Uppgift:** Konvertera ditt Tasks API från Övning 6 till MongoDB

**Krav:**
1. Skapa nytt projekt: `tasks-mongodb`
2. Koppla till MongoDB Atlas (använd samma cluster, skapa ny databas: `tasks-db`)
3. Skapa Task schema med fält:
   - `task` (String, required, max 200 chars)
   - `completed` (Boolean, default: false)
   - `priority` (String, enum: ['low', 'medium', 'high'])
4. Implementera GET, POST, DELETE endpoints
5. Extra utmaning: Lägg till PUT endpoint för att uppdatera `completed` status

**Inlämning:** GitHub repo länk

---

## 📚 Modul 2: Avancerad MongoDB - Relations & Queries (Vecka 6)

### Lektion 1: PUT/PATCH endpoints & Queries (60 min)

#### 🎓 Lärandemål
- Skapa PUT/PATCH endpoints för att uppdatera data
- Använda avancerade queries (filter, pagination)
- Förstå skillnaden mellan PUT och PATCH

#### 📖 Teori (10 min)

**PUT vs PATCH:**

| PUT | PATCH |
|-----|-------|
| Ersätter HELA resursen | Uppdaterar DELAR av resursen |
| Måste skicka alla fält | Skicka bara fält att ändra |
| `PUT /notes/123` | `PATCH /notes/123` |

**Exempel:**

Med PUT:
```json
// Måste skicka allt:
{
  "title": "Uppdaterad titel",
  "content": "Uppdaterat innehåll"
}
```

Med PATCH:
```json
// Bara det som ska ändras:
{
  "completed": true
}
```

**I praktiken:** Använd PATCH för de flesta uppdateringar!

#### ✏️ Övning 10: PUT/PATCH för Tasks (50 min)

**Fortsätt i tasks-mongodb projektet**

**Steg 1: PATCH endpoint för completed status (20 min)**

I `app.mjs`, lägg till:

```javascript
// PATCH /tasks/:id - Uppdatera task (t.ex. completed status)
app.patch('/tasks/:id', async (req, res) => {
  try {
    const { id } = req.params;
    const updates = req.body;
    
    // findByIdAndUpdate options:
    // new: true -> returnera uppdaterade dokumentet (inte gamla)
    // runValidators: true -> kör schema-validering på updates
    const task = await Task.findByIdAndUpdate(
      id,
      updates,
      { new: true, runValidators: true }
    );
    
    if (!task) {
      return res.status(404).json({
        success: false,
        message: 'Task not found'
      });
    }
    
    res.status(200).json({
      success: true,
      data: task
    });
    
  } catch (error) {
    console.error('Error updating task:', error);
    
    if (error.name === 'ValidationError') {
      const messages = Object.values(error.errors).map(err => err.message);
      return res.status(400).json({
        success: false,
        message: 'Validation error',
        errors: messages
      });
    }
    
    if (error.name === 'CastError') {
      return res.status(400).json({
        success: false,
        message: 'Invalid task ID'
      });
    }
    
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

```javascript
const updates = req.body;
```
- Ta emot VILKA fält som helst från body
- Användaren bestämmer vad som ska uppdateras

```javascript
Task.findByIdAndUpdate(id, updates, options)
```
- `id`: Vilket dokument
- `updates`: Vad som ska ändras
- `options`: Hur uppdateringen ska ske

```javascript
{ new: true, runValidators: true }
```
- `new: true` = Returnera det UPPDATERADE dokumentet
  - Utan detta: returnerar gamla dokumentet (före uppdatering)
- `runValidators: true` = Kör schema-validering
  - T.ex. om priority måste vara 'low', 'medium' eller 'high'

**Testa i Postman:**

**1. Uppdatera completed:**
```
PATCH http://localhost:3000/tasks/65a1b2c3d4e5f6g7h8i9j0k1

Body:
{
  "completed": true
}
```

**2. Uppdatera priority:**
```
PATCH http://localhost:3000/tasks/65a1b2c3d4e5f6g7h8i9j0k1

Body:
{
  "priority": "high"
}
```

**3. Uppdatera flera fält:**
```
PATCH http://localhost:3000/tasks/65a1b2c3d4e5f6g7h8i9j0k1

Body:
{
  "completed": true,
  "priority": "low"
}
```

**Steg 2: Query parameters för filtrering (30 min)**

**Lägg till i GET endpoint:**

```javascript
// GET /tasks - Hämta tasks med filtrering
app.get('/tasks', async (req, res) => {
  try {
    // Hämta query parameters
    const { completed, priority, sort } = req.query;
    
    // Bygg filter-objekt
    const filter = {};
    
    if (completed !== undefined) {
      // completed kan vara 'true' eller 'false' (strängar från URL)
      filter.completed = completed === 'true';
    }
    
    if (priority) {
      filter.priority = priority;
    }
    
    // Bygg sort-objekt
    let sortOption = { createdAt: -1 }; // Default: nyast först
    
    if (sort === 'priority') {
      // Custom sortering: high -> medium -> low
      sortOption = { priority: 1, createdAt: -1 };
    }
    
    // Hämta tasks med filter och sortering
    const tasks = await Task.find(filter).sort(sortOption);
    
    res.status(200).json({
      success: true,
      count: tasks.length,
      filter: filter, // Visa vilket filter som användes
      data: tasks
    });
    
  } catch (error) {
    console.error('Error fetching tasks:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

**Query parameters:**
```
GET /tasks?completed=true&priority=high
           ^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^
           query parameters
```

I Express:
```javascript
const { completed, priority } = req.query;
// completed = "true"
// priority = "high"
```

**Bygga filter:**
```javascript
const filter = {};

if (completed !== undefined) {
  filter.completed = completed === 'true';
}

// Om completed = "true": filter = { completed: true }
// Om completed = "false": filter = { completed: false }
// Om completed saknas: filter = {}
```

**Använda filter:**
```javascript
await Task.find(filter)

// Om filter = {}:              Hämtar ALLA
// Om filter = { completed: true }:  Hämtar bara completed tasks
// Om filter = { completed: true, priority: 'high' }:  Hämtar completed OCH high priority
```

**Testa i Postman:**

**1. Alla tasks:**
```
GET http://localhost:3000/tasks
```

**2. Bara completed:**
```
GET http://localhost:3000/tasks?completed=true
```

**3. Bara high priority:**
```
GET http://localhost:3000/tasks?priority=high
```

**4. Completed OCH high priority:**
```
GET http://localhost:3000/tasks?completed=true&priority=high
```

**5. Sorterat efter priority:**
```
GET http://localhost:3000/tasks?sort=priority
```

**✅ Kontrollera:**
- [ ] PATCH uppdaterar enskilda fält
- [ ] Validering körs vid uppdatering
- [ ] Query parameters filtrerar korrekt
- [ ] Sortering fungerar

---

### Lektion 2: Data relations (60 min)

#### 🎓 Lärandemål
- Förstå relationer i MongoDB (referenced vs embedded)
- Skapa relationer mellan collections
- Populate för att hämta relaterad data

#### 📖 Teori (15 min)

**Relationer i MongoDB:**

**1. Embedded (Inbäddad):**
```javascript
// User document med inbäddade addresses
{
  "_id": "user1",
  "name": "Anna",
  "addresses": [
    { "street": "Testgatan 1", "city": "Stockholm" },
    { "street": "Provvägen 2", "city": "Göteborg" }
  ]
}
```

**För och nackdelar:**
- ✅ Snabbt (allt i ett dokument)
- ✅ Atomiska operationer
- ❌ Svårt att dela data
- ❌ Dokumentstorlek begränsning (16MB)

**2. Referenced (Refererad):**
```javascript
// User document
{
  "_id": "user1",
  "name": "Anna",
  "posts": ["post1", "post2", "post3"]  // IDs till posts
}

// Post documents
{
  "_id": "post1",
  "title": "Mitt första inlägg",
  "author": "user1"  // ID till user
}
```

**För och nackdelar:**
- ✅ Flexibelt
- ✅ Kan dela data mellan dokument
- ✅ Ingen storlek-begränsning
- ❌ Behöver flera queries (men Mongoose hjälper med .populate())

**När använder man vad?**
- **Embedded:** One-to-few, data som alltid används tillsammans
- **Referenced:** One-to-many, många-till-många, data som delas

#### ✏️ Övning 11: Blog med Posts och Comments (45 min)

**Scenario:** En blogg där users kan skapa posts, och andra kan kommentera.

**Relationer:**
- User → Posts (one-to-many, referenced)
- Post → Comments (one-to-many, embedded)

**Steg 1: Nytt projekt (5 min)**

```bash
mkdir blog-api
cd blog-api
npm init -y
npm install express cors mongoose dotenv
npm install nodemon --save-dev
```

Kopiera `db.mjs`, `server.mjs`, `.env` från tidigare projekt.
Uppdatera MONGODB_URI database-namn till `blog-db`.

**Steg 2: User model (10 min)**

`models/User.mjs`:

```javascript
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema(
  {
    username: {
      type: String,
      required: [true, 'Username is required'],
      unique: true,  // Inga duplicata usernames
      trim: true,
      minlength: [3, 'Username must be at least 3 characters'],
      maxlength: [20, 'Username cannot exceed 20 characters']
    },
    email: {
      type: String,
      required: [true, 'Email is required'],
      unique: true,
      trim: true,
      lowercase: true,  // Konverterar till lowercase automatiskt
      match: [/^\S+@\S+\.\S+$/, 'Please enter a valid email']  // Regex validering
    },
    posts: [{
      type: mongoose.Schema.Types.ObjectId,  // Referens till Post document
      ref: 'Post'  // Vilken model vi refererar till
    }]
  },
  {
    timestamps: true
  }
);

const User = mongoose.model('User', userSchema);

export default User;
```

**Nytt koncept:**

**unique:**
```javascript
unique: true
```
- Ingen annan user kan ha samma username/email
- MongoDB skapar en index för snabb koll

**lowercase:**
```javascript
lowercase: true
```
- `"Anna@Test.COM"` sparas som `"anna@test.com"`
- Undviker duplicat pga stora/små bokstäver

**Regex validering:**
```javascript
match: [/^\S+@\S+\.\S+$/, 'Error message']
```
- Regex pattern för email
- `^\S+` = börjar med non-whitespace tecken
- `@` = måste innehålla @
- `\S+` = mer non-whitespace
- `\.` = måste innehålla punkt
- `\S+$` = slutar med non-whitespace

**ObjectId referens:**
```javascript
posts: [{
  type: mongoose.Schema.Types.ObjectId,
  ref: 'Post'
}]
```
- Array av ObjectIds
- Varje ID pekar på ett Post document
- `ref: 'Post'` = Mongoose vet att detta är Post IDs

**Steg 3: Post model (10 min)**

`models/Post.mjs`:

```javascript
import mongoose from 'mongoose';

// Subschema för comments (embedded)
const commentSchema = new mongoose.Schema(
  {
    text: {
      type: String,
      required: [true, 'Comment text is required'],
      trim: true,
      maxlength: [200, 'Comment cannot exceed 200 characters']
    },
    author: {
      type: String,
      required: [true, 'Comment author is required']
    }
  },
  {
    timestamps: true
  }
);

// Post schema
const postSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: [true, 'Title is required'],
      trim: true,
      maxlength: [100, 'Title cannot exceed 100 characters']
    },
    content: {
      type: String,
      required: [true, 'Content is required'],
      trim: true
    },
    author: {
      type: mongoose.Schema.Types.ObjectId,  // Referens till User
      ref: 'User',
      required: [true, 'Author is required']
    },
    comments: [commentSchema]  // Array av embedded comments
  },
  {
    timestamps: true
  }
);

const Post = mongoose.model('Post', postSchema);

export default Post;
```

**Nytt koncept:**

**Subschema (embedded):**
```javascript
const commentSchema = new mongoose.Schema({ ... });

// Används i post schema:
comments: [commentSchema]
```
- Comments är INBÄDDADE i post dokumentet
- Varje post har sina egna comments
- Snabbt att hämta (allt i ett dokument)

**Steg 4: Skapa endpoints (20 min)**

`app.mjs`:

```javascript
import express from 'express';
import cors from 'cors';
import User from './models/User.mjs';
import Post from './models/Post.mjs';

const app = express();

app.use(cors());
app.use(express.json());

// POST /users - Skapa user
app.post('/users', async (req, res) => {
  try {
    const { username, email } = req.body;
    
    const user = await User.create({ username, email });
    
    res.status(201).json({
      success: true,
      data: user
    });
  } catch (error) {
    console.error('Error creating user:', error);
    
    // Hantera duplicate key error (unique constraint)
    if (error.code === 11000) {
      const field = Object.keys(error.keyPattern)[0];
      return res.status(400).json({
        success: false,
        message: `${field} already exists`
      });
    }
    
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

// POST /posts - Skapa post
app.post('/posts', async (req, res) => {
  try {
    const { title, content, authorId } = req.body;
    
    // Kolla att author finns
    const user = await User.findById(authorId);
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'Author not found'
      });
    }
    
    // Skapa post
    const post = await Post.create({
      title,
      content,
      author: authorId
    });
    
    // Lägg till post ID till user's posts array
    user.posts.push(post._id);
    await user.save();
    
    res.status(201).json({
      success: true,
      data: post
    });
    
  } catch (error) {
    console.error('Error creating post:', error);
    
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

// GET /users/:id/posts - Hämta user med alla posts (populate)
app.get('/users/:id/posts', async (req, res) => {
  try {
    const { id } = req.params;
    
    // Populate fyller i posts array med faktiska post documents
    const user = await User.findById(id).populate('posts');
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    res.status(200).json({
      success: true,
      data: user
    });
    
  } catch (error) {
    console.error('Error fetching user posts:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

// GET /posts - Hämta alla posts med author info
app.get('/posts', async (req, res) => {
  try {
    // Populate author field med username och email
    const posts = await Post.find()
      .populate('author', 'username email')  // Välj bara vissa fält
      .sort({ createdAt: -1 });
    
    res.status(200).json({
      success: true,
      count: posts.length,
      data: posts
    });
    
  } catch (error) {
    console.error('Error fetching posts:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

// POST /posts/:id/comments - Lägg till comment
app.post('/posts/:id/comments', async (req, res) => {
  try {
    const { id } = req.params;
    const { text, author } = req.body;
    
    const post = await Post.findById(id);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    // Lägg till comment i posts comments array
    post.comments.push({ text, author });
    await post.save();
    
    res.status(201).json({
      success: true,
      data: post
    });
    
  } catch (error) {
    console.error('Error adding comment:', error);
    
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

**Nytt koncept:**

**Populate:**
```javascript
await User.findById(id).populate('posts')
```

Utan populate:
```json
{
  "_id": "user1",
  "username": "anna",
  "posts": ["post1", "post2"]  // Bara IDs
}
```

Med populate:
```json
{
  "_id": "user1",
  "username": "anna",
  "posts": [
    {
      "_id": "post1",
      "title": "Mitt inlägg",
      "content": "Innehåll här..."
    },
    {
      "_id": "post2",
      "title": "Annat inlägg",
      "content": "Mer innehåll..."
    }
  ]
}
```

**Selektiv populate:**
```javascript
.populate('author', 'username email')
```
- Populera author field
- Men bara inkludera username och email (inte _id, posts, timestamps, etc.)

**Testa i Postman:**

**1. Skapa user:**
```
POST http://localhost:3000/users

Body:
{
  "username": "anna",
  "email": "anna@test.com"
}
```
Spara `_id` från response!

**2. Skapa post:**
```
POST http://localhost:3000/posts

Body:
{
  "title": "Mitt första inlägg",
  "content": "Detta är innehållet i mitt inlägg!",
  "authorId": "DIN_USER_ID_HÄR"
}
```
Spara post `_id`!

**3. Lägg till comment:**
```
POST http://localhost:3000/posts/DIN_POST_ID/comments

Body:
{
  "text": "Bra inlägg!",
  "author": "Bob"
}
```

**4. Hämta alla posts med author:**
```
GET http://localhost:3000/posts
```
Se att author är populated med username och email!

**5. Hämta user med alla posts:**
```
GET http://localhost:3000/users/DIN_USER_ID/posts
```
Se att posts är populated med fullständig post data!

**✅ Kontrollera:**
- [ ] Users kan skapas
- [ ] Posts kan skapas och länkas till user
- [ ] Comments kan läggas till i posts (embedded)
- [ ] Populate fungerar för att hämta relaterad data
- [ ] Unique constraint fungerar (testa skapa duplicate username)

---

### 🏠 Hemuppgift (Vecka 6)

**Uppgift:** Lägg till Categories i blog-api

**Krav:**
1. Skapa Category model
   - `name` (String, required, unique)
   - `description` (String)
2. Uppdatera Post model
   - Lägg till `categories` field (array av ObjectId references till Category)
3. Skapa endpoints:
   - POST /categories (skapa category)
   - GET /categories (hämta alla)
   - GET /categories/:id/posts (hämta alla posts i en category)
4. Uppdatera POST /posts så man kan lägga till categories
5. Testa med Postman

**Extra utmaning:** Lägg till pagination i GET /posts
- Query params: `page` och `limit`
- T.ex. GET /posts?page=2&limit=10

**Inlämning:** GitHub repo länk

---

## 📚 Modul 3: Autentisering - Användare & Säkerhet (Vecka 7-9)

*[Fortsättning med bcrypt, JWT, sessions, middleware, säkerhet...]*

---

## 💡 Sammanfattning hittills

### Period 1 (Vecka 1-4): ✅ KLART
- Express basics
- JSON-filer
- CRUD operations
- Postman testing
- Debugging & error handling

### Period 2 (Vecka 5-6): 🔄 PÅGÅENDE
- MongoDB Atlas setup
- Mongoose schemas & models
- CRUD med databas
- Relations (referenced & embedded)
- Populate
- Query parameters & filtering

### Kommande (Vecka 7-20):
- Autentisering (bcrypt, JWT/sessions)
- Säkerhet (CORS, rate limiting, input sanitization)
- Frontend med Vite
- Slutprojekt: Forum

---

**[Dokumentet fortsätter med Modul 4-14...]**

*Vill du att jag fortsätter skriva ut resten av modulerna nu, eller vill du se hur Period 2 fungerar först?*

---

## 🚀 Nästa steg

**För att förbereda dig inför provet:**

1. **Repetera övningarna**
   - Gör Övning 1-5 igen från scratch (utan att kolla lösningar!)
   - Tidtagning: Försök klara allt på 60 minuter

2. **Bygg något eget**
   - Idéer: Recept-API, Kontakt-API, Produkt-API
   - Använd samma struktur som i övningarna
   - Lägg till egna valideringar

3. **Testa olika scenarios**
   - Vad händer om filen inte finns?
   - Vad händer om JSON är trasig?
   - Vad händer om fel data skickas?

4. **Använd checklistor**
   - Se "Checklista inför prov" i slutet av detta häfte

---

## 📋 Checklista inför prov

Använd denna checklista när du tränar och på provdagen!

### Före provet:
- [ ] Repetera alla övningar (1-8)
- [ ] Kan skapa projekt från scratch på 10 min
- [ ] Kan skapa GET endpoint på 5 min
- [ ] Kan skapa POST med validering på 10 min
- [ ] Kan skapa DELETE endpoint på 5 min
- [ ] Vet hur man testar i Postman
- [ ] Har listat vanliga stavfel att undvika

### Under provet - Setup:
- [ ] Läs HELA provbeskrivningen först
- [ ] Läs datamodellen NOGGRANT (vilka fält, vilka typer?)
- [ ] Skapa projektmapp
- [ ] Installera paket: express, cors, uuid
- [ ] Lägg till "type": "module" och dev-script
- [ ] Skapa server.mjs och app.mjs
- [ ] Testa: Startar servern? ✅

### Under provet - Varje endpoint:
- [ ] Skriv funktionen (t.ex. readData)
- [ ] Skriv endpointen (t.ex. app.get)
- [ ] Lägg till try-catch
- [ ] Lägg till console.log för debugging
- [ ] Spara filen
- [ ] Testa i Postman
- [ ] Fungerar? → Committa till GitHub
- [ ] Fungerar inte? → Kolla console, fixa fel, testa igen

### Specifikt för POST:
- [ ] Validera att ALLA fält finns
- [ ] Validera datatyper (om relevant)
- [ ] Validera värden (t.ex. duration 1-300)
- [ ] Generera ID med uuidv4()
- [ ] Generera timestamp med .toISOString()
- [ ] Trimma strängar med .trim()
- [ ] Returnera 400 vid valideringsfel
- [ ] Returnera 201 vid framgång

### Om du kopierar från tidigare projekt:
- [ ] Öppna app.mjs i VS Code
- [ ] Tryck Ctrl+H (Cmd+H på Mac)
- [ ] Sök: "gamle-namn"
- [ ] Ersätt: "nya-namn"
- [ ] Replace All
- [ ] Läs igenom koden radför rad ändå
- [ ] Testa servern: startar den?
- [ ] Testa alla endpoints

### Vanliga stavfel att dubbelkolla:
- [ ] `length` (inte `lenght`)
- [ ] Konsekventa variabelnamn (t.ex. `filePath` överallt)
- [ ] Endpoint paths matchar (singular/plural)
- [ ] Funktionsnamn matchar när du anropar dem
- [ ] Fältnamn från datamodellen är exakt rätt

### Vanliga syntax-fel att dubbelkolla:
- [ ] Arrow functions har `=>`: `(params) => { }`
- [ ] Template literals använder backticks: `` `${var}` ``
- [ ] Funktionsanrop använder parenteser: `func(args)`

### Före inlämning:
- [ ] Alla endpoints testade i Postman
- [ ] Screenshots tagna (om required)
- [ ] Kod committad till GitHub
- [ ] README.md uppdaterad (om required)
- [ ] Servern startar utan fel
- [ ] Inga console.error i terminal
- [ ] JSON-fil skapas och uppdateras korrekt

---

## 🆘 Felsökning - Snabbguide

### Servern startar inte

**Symtom:** Error när du kör `npm run dev`

**Kolla:**
1. Syntax-fel? (saknar `=>`, fel citattecken)
2. Stavfel i imports?
3. Saknade paket? Kör `npm install`

### 404 Not Found i Postman

**Symtom:** "Cannot GET /endpoint"

**Kolla:**
1. Skrev du HELA URL:en? `http://localhost:3000/endpoint`
2. Stavfel i endpoint path?
3. Rätt metod (GET vs POST vs DELETE)?

### req.body är undefined

**Symtom:** POST fungerar inte, data saknas

**Kolla:**
1. Har du `app.use(express.json())`?
2. Valde du "raw" och "JSON" i Postman Body?
3. Är JSON-syntaxen korrekt? (dubbla citattecken)

### 500 Server Error

**Symtom:** Allt var OK, sen plötsligt 500

**Kolla:**
1. Server console - vilket felmeddelande?
2. Syntax-fel i koden du just skrev?
3. Fil kan inte skrivas/läsas?

### Data sparas inte till fil

**Symtom:** POST säger success men filen är tom

**Kolla:**
1. Anropar du rätt save-funktion?
2. Använder du rätt variabelnamn i JSON.stringify?
3. Är filsökvägen korrekt?

### DELETE fungerar inte

**Symtom:** 404 eller data raderas inte

**Kolla:**
1. Kopierade du HELA ID:t?
2. Filter-logiken: `note.id !== noteId` (inte `===`)
3. Jämför du längder för att kolla om något raderades?

---

## 📚 Ordlista

**API** - Application Programming Interface, sätt för program att prata med varandra  
**Arrow function** - Modern funktionssyntax: `() => { }`  
**Backend** - Servern som hanterar logik och data  
**Callback** - Funktion som körs när något är klart  
**CRUD** - Create, Read, Update, Delete  
**Destructuring** - Plocka ut värden: `const { x } = obj`  
**Endpoint** - Specifik URL på servern: `/notes`  
**Express** - Framework för att bygga web-servrar i Node.js  
**Frontend** - Det användaren ser (HTML/CSS/JavaScript)  
**HTTP** - Protokoll för kommunikation på webben  
**JSON** - Textformat för data: `{"key": "value"}`  
**Middleware** - Funktioner som körs före endpoints  
**Node.js** - JavaScript-runtime för servrar  
**REST** - Arkitekturstil för API:er  
**UUID** - Unikt ID: `"abc-123-def-456"`  
**Validering** - Kontroll att data är korrekt

---

**Lycka till med övningarna! 🚀**

Om du fastnar - fråga läraren!
Om du blir klar - hjälp en klasskamrat!
