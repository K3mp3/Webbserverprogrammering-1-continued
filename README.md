# 💻 Webbserverprogrammering Nivå 1 - Kursmaterial

**Komplett kursmaterial för Webbserverprogrammering 1 (100 poäng)**  
Backend-utveckling med Node.js, Express, MongoDB och frontend-integration

---

## 📚 Om kursen

Detta repository innehåller **komplett kursmaterial** för gymnasiekursen Webbserverprogrammering 1. Kursen täcker 20 veckor med 2 lektioner per vecka (60 minuter vardera) och inkluderar:

- ✅ **5 perioder** med steg-för-steg övningar
- ✅ **5 prov** (40 minuter vardera)
- ✅ **1 slutprojekt** (Forum-applikation)
- ✅ Mappat till **svenska betygskriterier** (E/C/A)

---

## 🎯 Teknologier som täcks

### Backend
- **Node.js** + **Express.js**
- **MongoDB** + **Mongoose**
- **JWT** autentisering
- **bcrypt** lösenordshantering
- REST API design
- Router separation
- Middleware patterns

### Frontend
- **Vite** build tool
- **Vanilla JavaScript**
- Fetch API
- Token-baserad autentisering
- SPA (Single Page Application)

### Säkerhet
- Password hashing (bcrypt)
- JWT tokens
- Protected routes
- Input validation
- Rate limiting
- Helmet
- Mongo sanitization

---

## 📂 Projektstruktur

```
webbserverprogrammering-material/
│
├── README.md                          # Denna fil
│
├── period-dokument/
│   ├── 00-ovningar-fran-prov.md      # Vanliga misstag + GitHub setup
│   ├── period-1-2-express-och-mongodb.md      # Vecka 1-6: Express & MongoDB
│   ├── period-3-autentisering-sakerhet.md     # Vecka 7-9: Auth & Säkerhet
│   ├── period-4-frontend-vite.md              # Vecka 10-12: Frontend integration
│   └── period-5-forum-komplett.md             # Vecka 13-20: Slutprojekt (16 lektioner)
│
├── prov/
│   ├── prov-betygskriterier.md        # 5 prov + slutprojekt (mappat till betyg)
│   └── prov-facit-losningar.md        # Fullständiga lösningar till alla prov
│
└── exempel-projekt/                    # (Kommer läggas till)
    ├── book-library-api/
    ├── task-manager-auth/
    ├── notes-frontend/
    └── forum-fullstack/
```

---

## 📖 Kursöversikt

### **Period 0: Förberedelser**
**Fil:** `00-ovningar-fran-prov.md`
- GitHub setup och vanliga fel
- Validering patterns
- Error handling best practices

---

### **Period 1-2: Express & MongoDB (Vecka 1-6)**
**Fil:** `period-1-2-express-och-mongodb.md`

#### Vecka 1-2: Express Grunderna
- HTTP requests & responses
- Routes och endpoints
- JSON data
- Postman testing

#### Vecka 3-4: JSON Data
- Läsa och skriva JSON-filer
- CRUD operations
- Validering
- Error handling

#### Vecka 5-6: MongoDB
- MongoDB Atlas setup
- Mongoose schemas
- CRUD med MongoDB
- Queries och filtering

**→ PROV 1** (Vecka 6): Book Library API

---

### **Period 3: Autentisering & Säkerhet (Vecka 7-9)**
**Fil:** `period-3-autentisering-sakerhet.md`

#### Vecka 7: Password Hashing
- bcrypt implementation
- Salt rounds
- Signup endpoints

#### Vecka 8: JWT Authentication
- JWT tokens
- Login endpoints
- Protected routes
- Middleware

#### Vecka 9: Säkerhetspaket
- helmet
- express-rate-limit
- express-mongo-sanitize
- CORS

**→ PROV 2** (Vecka 9): Task Manager med Auth

---

### **Period 4: Frontend Integration (Vecka 10-12)**
**Fil:** `period-4-frontend-vite.md`

#### Vecka 10: Vite Setup
- Vite projekt setup
- CORS konfiguration
- Första fetch request
- Signup formulär

#### Vecka 11: Login & Protected Routes
- Login flow
- localStorage/sessionStorage
- Authorization headers
- Dashboard

#### Vecka 12: Token Management
- Token expiration
- Session monitoring
- Error handling
- Toast notifications

**→ PROV 3** (Vecka 12): Note-taking App Frontend

---

### **Period 5: Forum Projekt - SLUTPROJEKT (Vecka 13-20)**
**Fil:** `period-5-forum-komplett.md` (4590 rader!)

#### Vecka 13-14: Backend
- Router separation (express.Router())
- Post model med embedded comments
- Posts CRUD endpoints
- Comments system
- Vote/Like system
- Testing & debugging
- Admin panel & moderator roles

#### Vecka 15-16: Frontend Posts
- Posts feed med pagination
- Create post formulär
- Post detail view
- Edit & delete posts
- Enhanced comments

#### Vecka 17-18: User Features
- User profiles med statistics
- User's posts lista
- Search posts
- Advanced pagination

#### Vecka 19-20: Polish & Slutförande
- Categories system
- Final polish & responsive design
- **Dokumentation** (README.md)
- **Presentation** (5 min video)
- **Reflektion** (1-2 sidor)

**→ PROV 4** (Vecka 16): Blog API med Comments  
**→ SLUTPROJEKT** (Vecka 17-20): Forum Application (100 poäng)

---

## 📝 Prov & Bedömning

### **5 Prov + Slutprojekt**
**Fil:** `prov-betygskriterier.md`

| Prov | Vecka | Innehåll | Tid |
|------|-------|----------|-----|
| **Prov 1** | 6 | Express & MongoDB (Book Library API) | 40 min |
| **Prov 2** | 9 | Auth & Säkerhet (Task Manager) | 40 min |
| **Prov 3** | 12 | Frontend Integration (Notes App) | 40 min |
| **Prov 4** | 16 | Backend CRUD Komplett (Blog API) | 40 min |
| **Slutprojekt** | 17-20 | Forum Application | 4 veckor |

**Varje prov innehåller:**
- **Praktisk uppgift** (21 poäng): E (12p), C (+6p), A (+3p)
- **Teorifrågor** (6 poäng): 3 frågor à 2p
- **Säkerhetsfråga** (3 poäng): Identifiera hot & lösningar

### **Betygsmatrisen**
**För E:** Minst E-nivå på alla 4 prov (12p/21p) + E på slutprojekt (60p/100p)  
**För C:** Minst C-nivå på 3 av 4 prov (18p/21p) + C på slutprojekt (85p/100p)  
**För A:** C på alla prov + A på minst 2 prov + A på slutprojekt (95p/100p)

---

## 🔑 Facit & Lösningar

### **Facit till alla prov**
**Fil:** `prov-facit-losningar.md` (3114 rader!)

**Innehåller:**
- ✅ **Fullständiga lösningar** för E/C/A-nivåer på alla praktiska uppgifter
- ✅ **Kompletta kod-exempel** (inte bara snippets - hela projekt!)
- ✅ **Teorifrågor facit** med 3 svar per fråga (E/C/A)
- ✅ **Säkerhetsfrågor** med attack scenarios & defense strategies
- ✅ **Bedömningsmallar** för varje fråga
- ✅ **Slutprojekt bedömningsguide** med checklistor
- ✅ **Rättningschecklista** för lärare (6 steg, 90 min)

**Exempel på facit-struktur:**
```
PROV 1: Express & MongoDB
├── DEL 1: Praktisk Uppgift
│   ├── E-nivå Lösning (12p) - Fullständig kod
│   ├── C-nivå Lösning (+6p) - Med MongoDB
│   └── A-nivå Lösning (+3p) - Med filtering
├── DEL 2: Teorifrågor
│   ├── Fråga 1: RESTful API
│   │   ├── E-svar (1p): "GET, POST, DELETE..."
│   │   ├── C-svar (1.5p): "REST använder HTTP-metoder för resurser..."
│   │   └── A-svar (2p): "REST principer: Stateless, Resource-based..."
│   └── ... (2 frågor till)
└── DEL 3: Säkerhetsfråga
    └── Fråga 4: Säkerhetsrisker
        ├── E-svar: "Dålig data"
        ├── C-svar: "NoSQL injection, data corruption"
        └── A-svar: "5 konkreta attack scenarios med kod-exempel"
```

---

## 👥 För Lärare

### **Hur du använder materialet:**

**1. Förberedelse:**
- Läs igenom period-dokumenten
- Titta på strukturen i varje lektion (Teori → Övning → Testa)
- Förbered miljö: MongoDB Atlas, Postman

**2. Undervisning:**
- Varje lektion: 10 min teori + 50 min övning
- Eleverna följer steg-för-steg instruktioner
- Pausa för förklaringar vid behov
- Använd Git commits som checkpoints

**3. Prov:**
- Ge proven efter respektive period
- 40 minuter per prov
- Använd facit för rättning
- Följ poängfördelningen i facit

**4. Slutprojekt:**
- Vecka 17: Planering (lämnas in)
- Vecka 18-19: Utveckling
- Vecka 20: Dokumentation + Presentation
- Använd bedömningsguiden i facit

**5. Rättning:**
- Följ rättningschecklistan (90 min/projekt)
- Använd checklistorna för E/C/A
- Summera poäng → betyg

---

## 🎓 För Elever

### **Hur du använder materialet:**

**1. Börja med GitHub Setup:**
- Läs `00-ovningar-fran-prov.md`
- Skapa GitHub konto
- Installera Git
- Konfigurera GitHub på datorn

**2. Följ perioderna i ordning:**
- Period 1-2 → Express & MongoDB
- Period 3 → Autentisering
- Period 4 → Frontend
- Period 5 → Slutprojekt

**3. Varje lektion:**
- Läs teoridelen noggrant
- Följ övningarna steg-för-steg
- Testa efter varje steg
- Gör Git commit vid checkpoints

**4. Tips för framgång:**
- ✅ Skriv kod själv (inte copy-paste hela tiden)
- ✅ Testa ofta
- ✅ Läs felmeddelanden noggrant
- ✅ Använd `console.log()` för debugging
- ✅ Commit ofta till Git
- ✅ Fråga läraren om du fastnar

**5. Innan prov:**
- Gör övningarna i period-dokumenten
- Repetera teorin (sammanfattningar)
- Bygg liknande API från scratch
- Kolla säkerhetskapitlen

---

## 🛠️ Teknisk Setup

### **Förutsättningar:**

**Programvara:**
- **Node.js** (v18+): [nodejs.org](https://nodejs.org)
- **Git**: [git-scm.com](https://git-scm.com)
- **VS Code**: [code.visualstudio.com](https://code.visualstudio.com)
- **Postman**: [postman.com](https://www.postman.com)

**Tjänster:**
- **MongoDB Atlas** (gratis): [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
- **GitHub** konto: [github.com](https://github.com)

**VS Code Extensions (rekommenderade):**
- ESLint
- Prettier
- REST Client (alternativ till Postman)
- MongoDB for VS Code

---

## 📋 Mappning till Kursplan

### **Centralt innehåll:**

✅ **Webbutveckling för dynamiska webbplatser:**
- Express routing och middleware
- REST API design
- JSON data-hantering

✅ **Autentisering och säkerhet:**
- Bcrypt password hashing
- JWT tokens
- Protected routes med middleware
- Säkerhetspaket (helmet, rate-limit, sanitize)

✅ **Datalagring:**
- MongoDB med Mongoose
- Embedded vs Referenced data
- CRUD operations
- Queries och filtering

✅ **Applikationsutveckling:**
- Fullstack projekt (backend + frontend)
- Router separation
- MVC-liknande struktur
- API kommunikation

### **Betygskriterier:**

**Kunskapskrav 1:** *"Beskriva tekniker"*
- **E:** Översiktligt (Teorifrågor i proven)
- **C:** Utförligt (Djupare förklaringar)
- **A:** Utförligt och nyanserat (Jämför alternativ, motiverar val)

**Kunskapskrav 2:** *"Skapa dynamiska webbplatser"*
- **E:** Enkla (CRUD API med basic funktionalitet)
- **C:** Relativt komplexa (Auth, validering, relationer)
- **A:** Komplexa (Fullständig app med search, pagination, roles)

**Kunskapskrav 3:** *"Planera, genomföra, utvärdera"*
- **E:** Viss säkerhet (Enkel plan, genomför med viss hjälp)
- **C:** Säkerhet (Detaljerad plan, självständigt)
- **A:** God säkerhet (Omfattande plan, hög kvalitet, reflektion)

**Kunskapskrav 4:** *"Säkerhet"*
- **E:** Godtagbart resultat (Bcrypt, .env inte pushad)
- **C:** Gott resultat (JWT, validering, middleware)
- **A:** Mycket gott resultat (Rate limiting, helmet, sanitize, comprehensive)

---

## 📊 Statistik

**Totalt kursmaterial:**
- **7 dokument** (Period 0-5 + Prov + Facit)
- **~10,000 rader** kod och förklaringar
- **40+ lektioner** à 60 minuter
- **5 prov** à 40 minuter
- **1 slutprojekt** (4 veckor)
- **Hundratals** kod-exempel
- **Täcker 100%** av betygskriterierna

---
## 📞 Support & Frågor

**För lärare:**
- Läs igenom facit-dokumentet innan rättning
- Använd rättningschecklistan
- Kontakta vid frågor om bedömning

**För elever:**
- Fråga läraren först
- Använd `console.log()` för debugging
- Läs felmeddelanden noggrant
- Kolla dokumentation (Express, Mongoose, MDN)

---

## 🚀 Kom igång nu!

**Lärare:**
1. Clone repot: `git clone [repo-url]`
2. Läs `00-ovningar-fran-prov.md`
3. Börja med Period 1-2

**Elever:**
1. Läs `00-ovningar-fran-prov.md` för GitHub setup
2. Installera Node.js, Git, VS Code
3. Skapa MongoDB Atlas konto
4. Börja med Period 1 Lektion 1!

---

**Lycka till med kursen! 💪🚀**

*Senast uppdaterad: December 2025*
