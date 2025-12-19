# Period 5: Forum Projekt - KOMPLETT

**Vecka 13-20 | Slutprojekt | 16 lektioner**

---

## 📋 Innehållsförteckning

**[Vecka 13: Backend Setup & Posts](#vecka-13)**
- Lektion 1: Router separation & projektstruktur
- Lektion 2: Post model & CRUD endpoints
- Lektion 3: Comments system
- Lektion 4: Vote/Like system

**[Vecka 14: Backend Förfining](#vecka-14)**
- Lektion 1: Testing & debugging
- Lektion 2: Admin features & moderator roles

**[Vecka 15: Frontend Posts](#vecka-15)**
- Lektion 1: Frontend setup & posts feed
- Lektion 2: Create post formulär

**[Vecka 16: Frontend Interactions](#vecka-16)**
- Lektion 1: Post detail & comments UI
- Lektion 2: Edit & delete functionality

**[Vecka 17: User Features](#vecka-17)**
- Lektion 1: User profiles
- Lektion 2: User's posts & statistics

**[Vecka 18: Advanced Features](#vecka-18)**
- Lektion 1: Search & filter
- Lektion 2: Pagination

**[Vecka 19: Polish](#vecka-19)**
- Lektion 1: Categories & tags
- Lektion 2: Final polish & testing

**[Vecka 20: Slutförande](#vecka-20)**
- Lektion 1: Documentation & README
- Lektion 2: Presentation & reflektion

---

## 🎓 Lärandemål - Period 5

Efter denna period kan du:
- ✅ Strukturera stort projekt med routers
- ✅ Bygga komplett CRUD-applikation
- ✅ Implementera relations mellan modeller
- ✅ Hantera embedded vs referenced data
- ✅ Bygga interaktiv frontend
- ✅ Implementera search, filter, pagination
- ✅ Testa och debugga systematiskt
- ✅ Dokumentera projekt professionellt
- ✅ Presentera tekniskt projekt

---

<a name="vecka-13"></a>
# 📚 VECKA 13: Backend Setup & Posts

---

## Lektion 1: Router Separation & Projektstruktur (60 min)

### 🎓 Lärandemål
- Förstå varför vi separerar routers
- Skapa modulär projektstruktur
- Implementera Express Router
- Organisera endpoints logiskt

---

### 📖 Teori: Varför Router Separation? (10 min)

**Problem: Allt i app.mjs**

Utan router separation blir app.mjs 500+ rader med alla endpoints mixed together.

**Lösning: Router Separation**

**Projektstruktur:**
```
forum-backend/
├── routes/
│   ├── auth.mjs
│   ├── posts.mjs
│   └── users.mjs
├── models/
│   ├── User.mjs
│   └── Post.mjs
├── middleware/
│   ├── auth.mjs
│   └── validation.mjs
├── app.mjs
├── server.mjs
├── db.mjs
└── .env
```

**app.mjs blir kort:**
```javascript
import authRouter from './routes/auth.mjs';
import postsRouter from './routes/posts.mjs';
import usersRouter from './routes/users.mjs';

app.use('/auth', authRouter);
app.use('/posts', postsRouter);
app.use('/users', usersRouter);
```

**Fördelar:**
- ✅ Lätt att hitta endpoints
- ✅ Modulärt och återanvändbart
- ✅ Teamwork-friendly
- ✅ Industry best practice

---

### ✏️ Övning 22: Forum Backend Setup (50 min)

**Steg 1: Skapa projekt (10 min)**

```bash
cd C:\Users\DittAnvändarnamn\Documents\GitHub
mkdir forum-backend
cd forum-backend
git init
```

**Skapa `.gitignore`:**
```
node_modules/
.env
.DS_Store
```

**Installera paket:**
```bash
npm init -y
npm install express cors mongoose dotenv bcrypt jsonwebtoken
npm install nodemon --save-dev
```

**`package.json`:**
```json
{
  "type": "module",
  "scripts": {
    "dev": "nodemon server.mjs"
  }
}
```

**`.env`:**
```env
PORT=3000
MONGODB_URI=mongodb+srv://USERNAME:PASSWORD@cluster.xxxxx.mongodb.net/forum-db?retryWrites=true&w=majority
JWT_SECRET=din_hemliga_nyckel_minst_32_tecken
```

---

**Steg 2: Grundstruktur (10 min)**

```bash
mkdir routes models middleware
```

**`db.mjs`:**
```javascript
import mongoose from 'mongoose';
import dotenv from 'dotenv';

dotenv.config();

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI);
    console.log('✅ MongoDB connected');
    console.log('📊 Database:', mongoose.connection.name);
  } catch (error) {
    console.error('❌ MongoDB error:', error.message);
    process.exit(1);
  }
};

export default connectDB;
```

**`server.mjs`:**
```javascript
import app from './app.mjs';
import connectDB from './db.mjs';
import dotenv from 'dotenv';

dotenv.config();

const PORT = process.env.PORT || 3000;

connectDB();

app.listen(PORT, () => {
  console.log(`🚀 Server on http://localhost:${PORT}`);
});
```

---

**Steg 3: app.mjs med routers (15 min)**

**`app.mjs`:**
```javascript
import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';

import authRouter from './routes/auth.mjs';
import postsRouter from './routes/posts.mjs';
import usersRouter from './routes/users.mjs';

dotenv.config();

const app = express();

app.use(cors({
  origin: 'http://localhost:5173',
  credentials: true
}));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));

app.get('/health', (req, res) => {
  res.json({
    success: true,
    message: 'Server is healthy',
    timestamp: new Date().toISOString()
  });
});

app.use('/auth', authRouter);
app.use('/posts', postsRouter);
app.use('/users', usersRouter);

app.use((req, res) => {
  res.status(404).json({
    success: false,
    message: 'Endpoint not found'
  });
});

app.use((err, req, res, next) => {
  console.error('❌ Error:', err);
  res.status(err.status || 500).json({
    success: false,
    message: err.message || 'Internal server error'
  });
});

export default app;
```

---

**Steg 4: Models & Middleware (15 min)**

**`models/User.mjs`:**
```javascript
import mongoose from 'mongoose';
import bcrypt from 'bcrypt';

const userSchema = new mongoose.Schema(
  {
    username: {
      type: String,
      required: true,
      unique: true,
      trim: true,
      minlength: 3,
      maxlength: 20
    },
    email: {
      type: String,
      required: true,
      unique: true,
      trim: true,
      lowercase: true
    },
    password: {
      type: String,
      required: true,
      minlength: 6
    },
    role: {
      type: String,
      enum: ['user', 'moderator', 'admin'],
      default: 'user'
    }
  },
  { timestamps: true }
);

userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
  next();
});

userSchema.methods.comparePassword = async function(pwd) {
  return await bcrypt.compare(pwd, this.password);
};

export default mongoose.model('User', userSchema);
```

**`middleware/auth.mjs`:**
```javascript
import jwt from 'jsonwebtoken';

export const requireAuth = (req, res, next) => {
  try {
    const authHeader = req.headers.authorization;
    
    if (!authHeader?.startsWith('Bearer ')) {
      return res.status(401).json({
        success: false,
        message: 'No token provided'
      });
    }
    
    const token = authHeader.split(' ')[1];
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({
      success: false,
      message: 'Invalid token'
    });
  }
};
```

**`routes/auth.mjs`:**
```javascript
import express from 'express';
import jwt from 'jsonwebtoken';
import User from '../models/User.mjs';
import { requireAuth } from '../middleware/auth.mjs';

const router = express.Router();

router.post('/signup', async (req, res) => {
  try {
    const { username, email, password } = req.body;
    
    if (!username || !email || !password) {
      return res.status(400).json({
        success: false,
        message: 'All fields required'
      });
    }
    
    const existing = await User.findOne({
      $or: [{ email }, { username }]
    });
    
    if (existing) {
      return res.status(400).json({
        success: false,
        message: 'Username or email exists'
      });
    }
    
    const user = await User.create({ username, email, password });
    
    const token = jwt.sign(
      { id: user._id, username: user.username, role: user.role },
      process.env.JWT_SECRET,
      { expiresIn: '24h' }
    );
    
    res.status(201).json({
      success: true,
      token,
      user: {
        id: user._id,
        username: user.username,
        email: user.email,
        role: user.role
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

router.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    if (!email || !password) {
      return res.status(400).json({
        success: false,
        message: 'Email and password required'
      });
    }
    
    const user = await User.findOne({ email });
    if (!user) {
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      });
    }
    
    const isMatch = await user.comparePassword(password);
    if (!isMatch) {
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      });
    }
    
    const token = jwt.sign(
      { id: user._id, username: user.username, role: user.role },
      process.env.JWT_SECRET,
      { expiresIn: '24h' }
    );
    
    res.json({
      success: true,
      token,
      user: {
        id: user._id,
        username: user.username,
        email: user.email,
        role: user.role
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

router.get('/me', requireAuth, async (req, res) => {
  try {
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
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

export default router;
```

**Skapa tomma routers:**

**`routes/posts.mjs`:**
```javascript
import express from 'express';
const router = express.Router();

router.get('/', (req, res) => {
  res.json({ success: true, message: 'Posts router works!' });
});

export default router;
```

**`routes/users.mjs`:**
```javascript
import express from 'express';
const router = express.Router();

router.get('/', (req, res) => {
  res.json({ success: true, message: 'Users router works!' });
});

export default router;
```

---

**Steg 5: Testa (5 min)**

```bash
npm run dev
```

**Postman:**
```
POST http://localhost:3000/auth/signup
Body: { "username": "test", "email": "test@test.com", "password": "pass123" }
→ 201 Created ✅

POST http://localhost:3000/auth/login
Body: { "email": "test@test.com", "password": "pass123" }
→ 200 OK med token ✅

GET http://localhost:3000/auth/me
Headers: Authorization: Bearer TOKEN
→ 200 OK ✅
```

---

**📝 Commit:**
```bash
git add .
git commit -m "Initial forum backend with router separation"
git remote add origin https://github.com/DittAnvändarnamn/forum-backend.git
git push -u origin main
```

---

## Lektion 2: Post Model & CRUD Endpoints (60 min)

### 🎓 Lärandemål
- Skapa Post model med relations
- Implementera CRUD för posts
- Förstå author population
- Hantera timestamps

---

### 📖 Teori: Post Model Design (5 min)

**Schema:**
```javascript
Post {
  title: String
  content: String
  author: ObjectId -> User  // Reference
  comments: [Comment]       // Embedded
  votes: { upvotes, downvotes, voters }
  createdAt, updatedAt: Date
}
```

**Referenced vs Embedded:**
- `author`: Referenced (många posts har samma author)
- `comments`: Embedded (comments tillhör detta post)

---

### ✏️ Övning 23: Post CRUD (55 min)

**Steg 1: Post Model (10 min)**

**`models/Post.mjs`:**
```javascript
import mongoose from 'mongoose';

const postSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: [true, 'Title required'],
      trim: true,
      minlength: [3, 'Title min 3 chars'],
      maxlength: [200, 'Title max 200 chars']
    },
    content: {
      type: String,
      required: [true, 'Content required'],
      trim: true,
      minlength: [10, 'Content min 10 chars'],
      maxlength: [5000, 'Content max 5000 chars']
    },
    author: {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User',
      required: true
    },
    comments: [{
      author: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User',
        required: true
      },
      content: {
        type: String,
        required: true,
        trim: true,
        maxlength: 1000
      },
      createdAt: {
        type: Date,
        default: Date.now
      }
    }],
    votes: {
      upvotes: { type: Number, default: 0 },
      downvotes: { type: Number, default: 0 },
      voters: [{
        user: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
        voteType: { type: String, enum: ['up', 'down'] }
      }]
    }
  },
  { timestamps: true }
);

postSchema.virtual('voteScore').get(function() {
  return this.votes.upvotes - this.votes.downvotes;
});

postSchema.set('toJSON', { virtuals: true });
postSchema.set('toObject', { virtuals: true });

export default mongoose.model('Post', postSchema);
```

---

**Steg 2: GET /posts (10 min)**

**Uppdatera `routes/posts.mjs`:**
```javascript
import express from 'express';
import Post from '../models/Post.mjs';
import { requireAuth } from '../middleware/auth.mjs';

const router = express.Router();

router.get('/', async (req, res) => {
  try {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const skip = (page - 1) * limit;
    
    const sortBy = req.query.sortBy || 'createdAt';
    const sortOrder = req.query.sortOrder === 'asc' ? 1 : -1;
    
    const posts = await Post.find()
      .populate('author', 'username email')
      .sort({ [sortBy]: sortOrder })
      .skip(skip)
      .limit(limit);
    
    const total = await Post.countDocuments();
    const totalPages = Math.ceil(total / limit);
    
    res.json({
      success: true,
      data: {
        posts,
        pagination: {
          currentPage: page,
          totalPages,
          totalPosts: total,
          hasMore: page < totalPages
        }
      }
    });
  } catch (error) {
    console.error('Get posts error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

export default router;
```

---

**Steg 3: POST /posts (15 min)**

```javascript
router.post('/', requireAuth, async (req, res) => {
  try {
    const { title, content } = req.body;
    
    if (!title || !content) {
      return res.status(400).json({
        success: false,
        message: 'Title and content required'
      });
    }
    
    if (title.trim().length < 3) {
      return res.status(400).json({
        success: false,
        message: 'Title must be at least 3 characters'
      });
    }
    
    if (content.trim().length < 10) {
      return res.status(400).json({
        success: false,
        message: 'Content must be at least 10 characters'
      });
    }
    
    const post = await Post.create({
      title: title.trim(),
      content: content.trim(),
      author: req.user.id
    });
    
    await post.populate('author', 'username email');
    
    res.status(201).json({
      success: true,
      message: 'Post created',
      data: post
    });
  } catch (error) {
    console.error('Create post error:', error);
    
    if (error.name === 'ValidationError') {
      const messages = Object.values(error.errors).map(e => e.message);
      return res.status(400).json({
        success: false,
        message: messages.join(', ')
      });
    }
    
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 4: GET /posts/:id (10 min)**

```javascript
router.get('/:id', async (req, res) => {
  try {
    const post = await Post.findById(req.params.id)
      .populate('author', 'username email')
      .populate('comments.author', 'username');
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    res.json({
      success: true,
      data: post
    });
  } catch (error) {
    if (error.name === 'CastError') {
      return res.status(400).json({
        success: false,
        message: 'Invalid post ID'
      });
    }
    
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 5: PUT /posts/:id (10 min)**

```javascript
router.put('/:id', requireAuth, async (req, res) => {
  try {
    const { title, content } = req.body;
    
    const post = await Post.findById(req.params.id);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    if (post.author.toString() !== req.user.id) {
      return res.status(403).json({
        success: false,
        message: 'You can only edit your own posts'
      });
    }
    
    if (title && title.trim().length < 3) {
      return res.status(400).json({
        success: false,
        message: 'Title must be at least 3 characters'
      });
    }
    
    if (content && content.trim().length < 10) {
      return res.status(400).json({
        success: false,
        message: 'Content must be at least 10 characters'
      });
    }
    
    if (title) post.title = title.trim();
    if (content) post.content = content.trim();
    
    await post.save();
    await post.populate('author', 'username email');
    
    res.json({
      success: true,
      message: 'Post updated',
      data: post
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 6: DELETE /posts/:id (10 min)**

```javascript
router.delete('/:id', requireAuth, async (req, res) => {
  try {
    const post = await Post.findById(req.params.id);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    if (post.author.toString() !== req.user.id && req.user.role !== 'admin') {
      return res.status(403).json({
        success: false,
        message: 'You can only delete your own posts'
      });
    }
    
    await post.deleteOne();
    
    res.json({
      success: true,
      message: 'Post deleted'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Testa (5 min)**

```
POST http://localhost:3000/posts
Headers: Authorization: Bearer TOKEN
Body: { "title": "Test post", "content": "Detta är ett test inlägg." }
→ 201 ✅

GET http://localhost:3000/posts
→ 200 med lista ✅

GET http://localhost:3000/posts/POST_ID
→ 200 ✅

PUT http://localhost:3000/posts/POST_ID
Headers: Authorization: Bearer TOKEN
Body: { "title": "Uppdaterad" }
→ 200 ✅

DELETE http://localhost:3000/posts/POST_ID
Headers: Authorization: Bearer TOKEN
→ 200 ✅
```

**📝 Commit:**
```bash
git add .
git commit -m "Add Post model and CRUD endpoints"
git push
```

---

## Lektion 3: Comments System (60 min)

### 🎓 Lärandemål
- Implementera embedded comments
- Skapa comment endpoints
- Hantera nested data

---

### 📖 Teori: Embedded Comments (5 min)

**Embedded (vår approach):**
```javascript
Post {
  comments: [
    { author, content, createdAt },
    { author, content, createdAt }
  ]
}
```

**Fördelar:**
- ✅ En query för post + comments
- ✅ Snabbare
- ✅ Enklare

**Nackdelar:**
- ❌ Max 16MB per document
- ❌ Svårt att söka i alla comments

För forum: Embedded är bra!

---

### ✏️ Övning 24: Comment Endpoints (55 min)

**Steg 1: POST /posts/:id/comments (15 min)**

**Lägg till i `routes/posts.mjs`:**
```javascript
router.post('/:id/comments', requireAuth, async (req, res) => {
  try {
    const { content } = req.body;
    
    if (!content || content.trim().length < 1) {
      return res.status(400).json({
        success: false,
        message: 'Comment content required'
      });
    }
    
    if (content.trim().length > 1000) {
      return res.status(400).json({
        success: false,
        message: 'Comment max 1000 characters'
      });
    }
    
    const post = await Post.findById(req.params.id);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    const comment = {
      author: req.user.id,
      content: content.trim(),
      createdAt: new Date()
    };
    
    post.comments.push(comment);
    await post.save();
    
    await post.populate('comments.author', 'username');
    
    const newComment = post.comments[post.comments.length - 1];
    
    res.status(201).json({
      success: true,
      message: 'Comment added',
      data: newComment
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 2: GET /posts/:id/comments (10 min)**

```javascript
router.get('/:id/comments', async (req, res) => {
  try {
    const post = await Post.findById(req.params.id)
      .select('comments')
      .populate('comments.author', 'username');
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    const sorted = post.comments.sort((a, b) => 
      b.createdAt - a.createdAt
    );
    
    res.json({
      success: true,
      data: {
        comments: sorted,
        count: sorted.length
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 3: PUT /posts/:postId/comments/:commentId (15 min)**

```javascript
router.put('/:postId/comments/:commentId', requireAuth, async (req, res) => {
  try {
    const { content } = req.body;
    
    if (!content || content.trim().length < 1) {
      return res.status(400).json({
        success: false,
        message: 'Comment content required'
      });
    }
    
    const post = await Post.findById(req.params.postId);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    const comment = post.comments.id(req.params.commentId);
    
    if (!comment) {
      return res.status(404).json({
        success: false,
        message: 'Comment not found'
      });
    }
    
    if (comment.author.toString() !== req.user.id) {
      return res.status(403).json({
        success: false,
        message: 'You can only edit your own comments'
      });
    }
    
    comment.content = content.trim();
    
    await post.save();
    await post.populate('comments.author', 'username');
    
    res.json({
      success: true,
      message: 'Comment updated',
      data: comment
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 4: DELETE /posts/:postId/comments/:commentId (15 min)**

```javascript
router.delete('/:postId/comments/:commentId', requireAuth, async (req, res) => {
  try {
    const post = await Post.findById(req.params.postId);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    const comment = post.comments.id(req.params.commentId);
    
    if (!comment) {
      return res.status(404).json({
        success: false,
        message: 'Comment not found'
      });
    }
    
    if (comment.author.toString() !== req.user.id && req.user.role !== 'admin') {
      return res.status(403).json({
        success: false,
        message: 'You can only delete your own comments'
      });
    }
    
    comment.deleteOne();
    await post.save();
    
    res.json({
      success: true,
      message: 'Comment deleted'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Testa (10 min)**

```
POST http://localhost:3000/posts/POST_ID/comments
Headers: Authorization: Bearer TOKEN
Body: { "content": "Min kommentar!" }
→ 201 ✅

GET http://localhost:3000/posts/POST_ID/comments
→ 200 ✅

PUT http://localhost:3000/posts/POST_ID/comments/COMMENT_ID
Headers: Authorization: Bearer TOKEN
Body: { "content": "Uppdaterad!" }
→ 200 ✅

DELETE http://localhost:3000/posts/POST_ID/comments/COMMENT_ID
Headers: Authorization: Bearer TOKEN
→ 200 ✅
```

**📝 Commit:**
```bash
git add .
git commit -m "Add comment CRUD endpoints"
git push
```

---

## Lektion 4: Vote/Like System (60 min)

### 🎓 Lärandemål
- Implementera vote system
- Hantera vote logic (toggle)
- Förhindra duplicate votes
- Beräkna vote score

---

### 📖 Teori: Vote System (5 min)

**Vote structure:**
```javascript
votes: {
  upvotes: Number,
  downvotes: Number,
  voters: [{ user: ObjectId, voteType: 'up'|'down' }]
}
```

**Vote logic:**
1. User upvote → upvotes++, lägg till i voters
2. User upvote igen → remove vote (toggle)
3. User downvote efter upvote → upvotes--, downvotes++, update voters

---

### ✏️ Övning 25: Vote Endpoints (55 min)

**Steg 1: POST /posts/:id/vote (30 min)**

```javascript
router.post('/:id/vote', requireAuth, async (req, res) => {
  try {
    const { voteType } = req.body;
    
    if (!voteType || !['up', 'down'].includes(voteType)) {
      return res.status(400).json({
        success: false,
        message: 'voteType must be "up" or "down"'
      });
    }
    
    const post = await Post.findById(req.params.id);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    const userId = req.user.id;
    
    // Check if user already voted
    const existingVote = post.votes.voters.find(
      v => v.user.toString() === userId
    );
    
    if (existingVote) {
      // Same vote type → remove vote (toggle off)
      if (existingVote.voteType === voteType) {
        if (voteType === 'up') {
          post.votes.upvotes--;
        } else {
          post.votes.downvotes--;
        }
        
        post.votes.voters = post.votes.voters.filter(
          v => v.user.toString() !== userId
        );
        
        await post.save();
        
        return res.json({
          success: true,
          message: 'Vote removed',
          data: {
            upvotes: post.votes.upvotes,
            downvotes: post.votes.downvotes,
            voteScore: post.voteScore,
            userVote: null
          }
        });
      }
      
      // Different vote type → switch vote
      if (existingVote.voteType === 'up') {
        post.votes.upvotes--;
        post.votes.downvotes++;
      } else {
        post.votes.downvotes--;
        post.votes.upvotes++;
      }
      
      existingVote.voteType = voteType;
    } else {
      // New vote
      if (voteType === 'up') {
        post.votes.upvotes++;
      } else {
        post.votes.downvotes++;
      }
      
      post.votes.voters.push({
        user: userId,
        voteType
      });
    }
    
    await post.save();
    
    res.json({
      success: true,
      message: existingVote ? 'Vote updated' : 'Vote added',
      data: {
        upvotes: post.votes.upvotes,
        downvotes: post.votes.downvotes,
        voteScore: post.voteScore,
        userVote: voteType
      }
    });
  } catch (error) {
    console.error('Vote error:', error);
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Förklaring:**

```javascript
const existingVote = post.votes.voters.find(
  v => v.user.toString() === userId
);
```
Kolla om user redan röstat.

```javascript
if (existingVote.voteType === voteType) {
  // Toggle off - remove vote
}
```
Om samma vote type → ta bort röst (toggle).

```javascript
else {
  // Switch vote up ↔ down
  if (existingVote.voteType === 'up') {
    post.votes.upvotes--;
    post.votes.downvotes++;
  }
}
```
Om olika vote type → flytta röst.

---

**Steg 2: GET /posts/:id/vote (10 min)**

Hämta current user's vote:

```javascript
router.get('/:id/vote', requireAuth, async (req, res) => {
  try {
    const post = await Post.findById(req.params.id).select('votes');
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    const userVote = post.votes.voters.find(
      v => v.user.toString() === req.user.id
    );
    
    res.json({
      success: true,
      data: {
        upvotes: post.votes.upvotes,
        downvotes: post.votes.downvotes,
        voteScore: post.votes.upvotes - post.votes.downvotes,
        userVote: userVote ? userVote.voteType : null
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 3: Testa vote system (15 min)**

**Test 1: Första upvote**
```
POST http://localhost:3000/posts/POST_ID/vote
Headers: Authorization: Bearer TOKEN
Body: { "voteType": "up" }

Response:
{
  "success": true,
  "message": "Vote added",
  "data": {
    "upvotes": 1,
    "downvotes": 0,
    "voteScore": 1,
    "userVote": "up"
  }
}
```

**Test 2: Toggle off (samma vote igen)**
```
POST http://localhost:3000/posts/POST_ID/vote
Headers: Authorization: Bearer TOKEN
Body: { "voteType": "up" }

Response:
{
  "message": "Vote removed",
  "data": {
    "upvotes": 0,
    "downvotes": 0,
    "voteScore": 0,
    "userVote": null
  }
}
```

**Test 3: Upvote → Downvote**
```
POST /posts/POST_ID/vote
Body: { "voteType": "up" }
→ upvotes: 1, downvotes: 0

POST /posts/POST_ID/vote
Body: { "voteType": "down" }
→ upvotes: 0, downvotes: 1
```

**Test 4: Hämta user's vote**
```
GET http://localhost:3000/posts/POST_ID/vote
Headers: Authorization: Bearer TOKEN

Response:
{
  "success": true,
  "data": {
    "upvotes": 1,
    "downvotes": 0,
    "voteScore": 1,
    "userVote": "up"
  }
}
```

**Test 5: Två users röstar**

User 1 upvote, User 2 downvote → upvotes: 1, downvotes: 1, score: 0 ✅

---

**📝 Commit:**
```bash
git add .
git commit -m "Add vote/like system with toggle"
git push
```

---

## 🏠 Hemuppgift Vecka 13

**Uppgift:** Lägg till comment count i GET /posts

**Krav:**
1. Varje post i lista ska ha `commentCount` field
2. Använd Mongoose virtual eller beräkna i query

**Hint:**
```javascript
postSchema.virtual('commentCount').get(function() {
  return this.comments.length;
});
```

**Bonus:** Lägg till "latest comment" info

---

<a name="vecka-14"></a>
# 📚 VECKA 14: Backend Förfining

---

## Lektion 1: Testing & Debugging (60 min)

### 🎓 Lärandemål
- Testa API systematiskt
- Hitta och fixa buggar
- Använda console.log strategiskt
- Förstå error messages

---

### 📖 Teori: Systematic Testing (10 min)

**Testing approach:**

**1. Positive tests (happy path):**
- Rätt data → Förväntat resultat

**2. Negative tests (error cases):**
- Fel data → Rätt error message
- Saknad data → 400 error
- Ingen token → 401 error
- Fel user → 403 error

**3. Edge cases:**
- Max length strings
- Special characters
- Empty arrays
- Null values

**Debugging tips:**
```javascript
// BEFORE problem code:
console.log('DEBUG: Input data:', req.body);

// AFTER problem code:
console.log('DEBUG: Result:', result);

// IN catch block:
console.error('ERROR:', error.message);
console.error('STACK:', error.stack);
```

---

### ✏️ Övning 26: Test All Endpoints (50 min)

**Steg 1: Create test checklist (10 min)**

**Auth endpoints:**
- [ ] POST /auth/signup med valid data → 201
- [ ] POST /auth/signup duplicate username → 400
- [ ] POST /auth/signup saknar field → 400
- [ ] POST /auth/login rätt credentials → 200 + token
- [ ] POST /auth/login fel password → 401
- [ ] POST /auth/login non-existent email → 401
- [ ] GET /auth/me med token → 200 + user data
- [ ] GET /auth/me utan token → 401
- [ ] GET /auth/me invalid token → 401

**Post endpoints:**
- [ ] GET /posts → 200 + array
- [ ] GET /posts?page=2&limit=5 → pagination works
- [ ] POST /posts med token → 201
- [ ] POST /posts utan token → 401
- [ ] POST /posts title < 3 chars → 400
- [ ] GET /posts/:id valid → 200
- [ ] GET /posts/:id invalid id → 400
- [ ] PUT /posts/:id author → 200
- [ ] PUT /posts/:id not author → 403
- [ ] DELETE /posts/:id author → 200
- [ ] DELETE /posts/:id not author → 403

**Comment endpoints:**
- [ ] POST /posts/:id/comments → 201
- [ ] GET /posts/:id/comments → 200 + array
- [ ] PUT /posts/:id/comments/:cid author → 200
- [ ] DELETE /posts/:id/comments/:cid author → 200

**Vote endpoints:**
- [ ] POST /posts/:id/vote up → upvotes: 1
- [ ] POST /posts/:id/vote up again → vote removed
- [ ] POST /posts/:id/vote up then down → switch
- [ ] GET /posts/:id/vote → current user vote

---

**Steg 2: Run tests in Postman (30 min)**

**Systematiskt test i Postman:**

1. Skapa 2 users (för att testa authorization)
2. Logga in som User 1, spara token
3. Skapa post som User 1
4. Testa edit som User 1 → OK
5. Logga in som User 2, spara token
6. Försök edit User 1's post → 403
7. Fortsätt genom alla checkpoints

**Hitta buggar:**

Vanliga buggar du kan hitta:
- Glömt `trim()` → Whitespace sparas
- Fel statuskod (200 istället för 201)
- Error message inte beskrivande
- Missing validation
- Authorization check fel

**Fixa buggar direkt:**
```javascript
// BUG: Title inte trimmad
title: req.body.title  // ❌

// FIX:
title: req.body.title.trim()  // ✅

// BUG: Returnerar 200 för create
res.json({ ... })  // ❌

// FIX:
res.status(201).json({ ... })  // ✅
```

---

**Steg 3: Add debug logging (10 min)**

**Lägg till logging i kritiska endpoints:**

```javascript
router.post('/:id/vote', requireAuth, async (req, res) => {
  console.log('=== VOTE ENDPOINT ===');
  console.log('User:', req.user.id);
  console.log('Post:', req.params.id);
  console.log('Vote type:', req.body.voteType);
  
  try {
    const post = await Post.findById(req.params.id);
    console.log('Post found:', !!post);
    
    if (!post) {
      console.log('Post not found!');
      return res.status(404).json({ ... });
    }
    
    console.log('Current votes:', post.votes);
    
    // ... logic ...
    
    console.log('New votes:', post.votes);
    console.log('=== VOTE DONE ===');
    
    res.json({ ... });
  } catch (error) {
    console.error('VOTE ERROR:', error.message);
    console.error('STACK:', error.stack);
    res.status(500).json({ ... });
  }
});
```

**Test med logging:**
```
POST /posts/POST_ID/vote

Terminal output:
=== VOTE ENDPOINT ===
User: 507f1f77bcf86cd799439011
Post: 507f1f77bcf86cd799439012
Vote type: up
Post found: true
Current votes: { upvotes: 0, downvotes: 0, voters: [] }
New votes: { upvotes: 1, downvotes: 0, voters: [...] }
=== VOTE DONE ===
```

**Efter testing är klart:**
Ta bort eller kommentera bort logging (eller använd en logger library som winston).

---

**📝 Commit:**
```bash
git add .
git commit -m "Add comprehensive testing and debug logging"
git push
```

---

## Lektion 2: Admin Features & Moderator Roles (60 min)

### 🎓 Lärandemål
- Implementera role-based access control
- Skapa admin endpoints
- Moderator funktionalitet
- Bulk operations

---

### 📖 Teori: Roles & Permissions (5 min)

**User roles:**
```javascript
user: {
  role: 'user'      // Normal user
  role: 'moderator' // Can delete any post/comment
  role: 'admin'     // Can do everything
}
```

**Permissions:**
- **User:** CRUD own posts, vote, comment
- **Moderator:** + delete any post/comment
- **Admin:** + promote users, view stats, delete users

**Middleware för roles:**
```javascript
const requireRole = (role) => {
  return (req, res, next) => {
    if (req.user.role !== role) {
      return res.status(403).json({ message: 'Access denied' });
    }
    next();
  };
};
```

---

### ✏️ Övning 27: Admin System (55 min)

**Steg 1: Role middleware (10 min)**

**`middleware/auth.mjs`:**
```javascript
export const requireRole = (...roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        success: false,
        message: 'Access denied. Insufficient permissions.'
      });
    }
    next();
  };
};

export const requireAdmin = requireRole('admin');
export const requireModOrAdmin = requireRole('moderator', 'admin');
```

---

**Steg 2: Admin routes (20 min)**

**Skapa `routes/admin.mjs`:**
```javascript
import express from 'express';
import User from '../models/User.mjs';
import Post from '../models/Post.mjs';
import { requireAuth, requireAdmin } from '../middleware/auth.mjs';

const router = express.Router();

// All admin routes require auth + admin role
router.use(requireAuth);
router.use(requireAdmin);

/**
 * GET /admin/stats
 * Dashboard statistics
 */
router.get('/stats', async (req, res) => {
  try {
    const totalUsers = await User.countDocuments();
    const totalPosts = await Post.countDocuments();
    
    const totalComments = await Post.aggregate([
      { $project: { commentCount: { $size: '$comments' } } },
      { $group: { _id: null, total: { $sum: '$commentCount' } } }
    ]);
    
    const recentUsers = await User.find()
      .sort({ createdAt: -1 })
      .limit(5)
      .select('username email createdAt');
    
    const topPosts = await Post.find()
      .sort({ 'votes.upvotes': -1 })
      .limit(5)
      .populate('author', 'username')
      .select('title votes.upvotes votes.downvotes');
    
    res.json({
      success: true,
      data: {
        stats: {
          totalUsers,
          totalPosts,
          totalComments: totalComments[0]?.total || 0
        },
        recentUsers,
        topPosts
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * GET /admin/users
 * List all users
 */
router.get('/users', async (req, res) => {
  try {
    const users = await User.find()
      .select('-password')
      .sort({ createdAt: -1 });
    
    res.json({
      success: true,
      data: users
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * PUT /admin/users/:id/role
 * Change user role
 */
router.put('/users/:id/role', async (req, res) => {
  try {
    const { role } = req.body;
    
    if (!['user', 'moderator', 'admin'].includes(role)) {
      return res.status(400).json({
        success: false,
        message: 'Invalid role'
      });
    }
    
    const user = await User.findById(req.params.id);
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Prevent changing own role
    if (user._id.toString() === req.user.id) {
      return res.status(400).json({
        success: false,
        message: 'Cannot change your own role'
      });
    }
    
    user.role = role;
    await user.save();
    
    res.json({
      success: true,
      message: `User role updated to ${role}`,
      data: {
        id: user._id,
        username: user.username,
        role: user.role
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * DELETE /admin/users/:id
 * Delete user (and their posts)
 */
router.delete('/users/:id', async (req, res) => {
  try {
    const user = await User.findById(req.params.id);
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Cannot delete yourself
    if (user._id.toString() === req.user.id) {
      return res.status(400).json({
        success: false,
        message: 'Cannot delete yourself'
      });
    }
    
    // Delete user's posts
    await Post.deleteMany({ author: user._id });
    
    // Delete user
    await user.deleteOne();
    
    res.json({
      success: true,
      message: 'User and their posts deleted'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

/**
 * DELETE /admin/posts/:id
 * Delete any post (admin override)
 */
router.delete('/posts/:id', async (req, res) => {
  try {
    const post = await Post.findById(req.params.id);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    await post.deleteOne();
    
    res.json({
      success: true,
      message: 'Post deleted by admin'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

export default router;
```

**Registrera router i `app.mjs`:**
```javascript
import adminRouter from './routes/admin.mjs';

app.use('/admin', adminRouter);
```

---

**Steg 3: Moderator features (15 min)**

**Uppdatera POST DELETE endpoint för moderators:**

I `routes/posts.mjs`:
```javascript
router.delete('/:id', requireAuth, async (req, res) => {
  try {
    const post = await Post.findById(req.params.id);
    
    if (!post) {
      return res.status(404).json({
        success: false,
        message: 'Post not found'
      });
    }
    
    // Author, moderator, or admin can delete
    const isAuthor = post.author.toString() === req.user.id;
    const isMod = ['moderator', 'admin'].includes(req.user.role);
    
    if (!isAuthor && !isMod) {
      return res.status(403).json({
        success: false,
        message: 'Access denied'
      });
    }
    
    await post.deleteOne();
    
    res.json({
      success: true,
      message: 'Post deleted'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

---

**Steg 4: Testa admin features (10 min)**

**1. Skapa admin user manuellt i MongoDB:**

```javascript
// I MongoDB Compass eller script
db.users.updateOne(
  { email: "admin@test.com" },
  { $set: { role: "admin" } }
)
```

**2. Logga in som admin och testa:**

```
GET http://localhost:3000/admin/stats
Headers: Authorization: Bearer ADMIN_TOKEN
→ 200 + statistics ✅

GET http://localhost:3000/admin/users
→ 200 + user list ✅

PUT http://localhost:3000/admin/users/USER_ID/role
Body: { "role": "moderator" }
→ 200 + role updated ✅

DELETE http://localhost:3000/admin/posts/POST_ID
→ 200 + post deleted ✅
```

**3. Test med regular user:**

```
GET http://localhost:3000/admin/stats
Headers: Authorization: Bearer USER_TOKEN
→ 403 Access denied ✅
```

---

**📝 Commit:**
```bash
git add .
git commit -m "Add admin and moderator features"
git push
```

---

## 🏠 Hemuppgift Vecka 14

**Uppgift 1:** Lägg till POST /admin/posts/bulk-delete

Delete flera posts samtidigt:
```javascript
Body: { "postIds": ["id1", "id2", "id3"] }
```

**Uppgift 2:** Lägg till GET /admin/reports

Mock data för "reported posts/comments" som moderator kan granska.

**Bonus:** User suspension system (disable user account temporarily)

---

Nu har du en komplett backend med:
- ✅ Router separation
- ✅ Posts CRUD
- ✅ Comments CRUD
- ✅ Vote system
- ✅ Admin panel
- ✅ Moderator roles
- ✅ Testing och debugging

**Nästa: Frontend med Vite!** 🚀

<a name="vecka-15"></a>
# 📚 VECKA 15: Frontend Posts

---

## Lektion 1: Frontend Setup & Posts Feed (60 min)

### 🎓 Lärandemål
- Sätta upp Vite frontend
- Koppla till backend API
- Lista posts med pagination
- Visa vote scores

---

### 📖 Teori: Frontend Architecture (5 min)

**Structure:**
```
forum-frontend/
├── src/
│   ├── utils/
│   │   ├── api.js
│   │   └── auth.js
│   ├── components/
│   │   ├── PostCard.js
│   │   └── Navbar.js
│   ├── views/
│   │   ├── Home.js
│   │   ├── Login.js
│   │   └── PostDetail.js
│   ├── main.js
│   └── style.css
├── index.html
└── package.json
```

---

### ✏️ Övning 28: Frontend Setup (55 min)

**Steg 1: Skapa Vite projekt (10 min)**

```bash
cd C:\Users\DittAnvändarnamn\Documents\GitHub
npm create vite@latest forum-frontend
# Välj Vanilla + JavaScript

cd forum-frontend
npm install
git init
```

**`.gitignore`:**
```
node_modules/
dist/
```

**Rensa default innehåll:**
- Ta bort `counter.js`, `javascript.svg`
- Töm `style.css`

**`index.html`:**
```html
<!DOCTYPE html>
<html lang="sv">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Forum</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>
```

---

**Steg 2: API helpers (15 min)**

**`src/utils/api.js`:**
```javascript
const API_URL = 'http://localhost:3000';

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
    
    if (response.status === 401) {
      localStorage.removeItem('token');
      localStorage.removeItem('user');
      window.location.href = '/';
      throw new Error('Token expired');
    }
    
    const data = await response.json();
    
    return {
      ok: response.ok,
      status: response.status,
      data
    };
  } catch (error) {
    console.error('API Error:', error);
    throw error;
  }
}

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

**`src/utils/auth.js`:**
```javascript
export function isAuthenticated() {
  return !!localStorage.getItem('token');
}

export function getCurrentUser() {
  const userJson = localStorage.getItem('user');
  return userJson ? JSON.parse(userJson) : null;
}

export function logout() {
  localStorage.removeItem('token');
  localStorage.removeItem('user');
  window.location.href = '/';
}
```

---

**Steg 3: Posts feed (20 min)**

**`src/main.js`:**
```javascript
import './style.css';
import { api } from './utils/api.js';
import { isAuthenticated, getCurrentUser, logout } from './utils/auth.js';

let currentView = 'home';
let currentPage = 1;
let posts = [];

function renderNavbar() {
  const user = getCurrentUser();
  
  return `
    <nav class="navbar">
      <div class="nav-container">
        <h1 class="nav-logo">💬 Forum</h1>
        ${user ? `
          <div class="nav-user">
            <span>👤 ${user.username}</span>
            <button onclick="window.showView('createPost')" class="btn-primary">Nytt inlägg</button>
            <button onclick="window.handleLogout()" class="btn-secondary">Logga ut</button>
          </div>
        ` : `
          <div class="nav-buttons">
            <button onclick="window.showView('login')" class="btn-primary">Logga in</button>
            <button onclick="window.showView('signup')" class="btn-secondary">Skapa konto</button>
          </div>
        `}
      </div>
    </nav>
  `;
}

function renderPostCard(post) {
  const voteClass = post.userVote === 'up' ? 'voted-up' : 
                    post.userVote === 'down' ? 'voted-down' : '';
  
  return `
    <div class="post-card" data-id="${post._id}">
      <div class="post-votes">
        <button class="vote-btn vote-up ${voteClass}" onclick="window.handleVote('${post._id}', 'up')">
          ▲
        </button>
        <span class="vote-score">${post.voteScore || 0}</span>
        <button class="vote-btn vote-down ${voteClass}" onclick="window.handleVote('${post._id}', 'down')">
          ▼
        </button>
      </div>
      <div class="post-content">
        <h3 class="post-title" onclick="window.showPost('${post._id}')">
          ${post.title}
        </h3>
        <p class="post-preview">${post.content.substring(0, 200)}${post.content.length > 200 ? '...' : ''}</p>
        <div class="post-meta">
          <span>👤 ${post.author.username}</span>
          <span>💬 ${post.commentCount || 0} kommentarer</span>
          <span>📅 ${new Date(post.createdAt).toLocaleDateString('sv-SE')}</span>
        </div>
      </div>
    </div>
  `;
}

async function renderHome() {
  document.querySelector('#app').innerHTML = `
    ${renderNavbar()}
    <div class="container">
      <div class="posts-header">
        <h2>Senaste inläggen</h2>
        <select id="sort-select" onchange="window.handleSortChange()">
          <option value="createdAt">Senaste</option>
          <option value="votes.upvotes">Populärast</option>
        </select>
      </div>
      <div id="posts-list">
        <div class="loading">Laddar inlägg...</div>
      </div>
      <div id="pagination"></div>
    </div>
  `;
  
  await loadPosts();
}

async function loadPosts() {
  try {
    const sortBy = document.querySelector('#sort-select')?.value || 'createdAt';
    const { ok, data } = await api.get(`/posts?page=${currentPage}&limit=10&sortBy=${sortBy}&sortOrder=desc`);
    
    if (!ok) {
      throw new Error('Failed to load posts');
    }
    
    posts = data.data.posts;
    const pagination = data.data.pagination;
    
    document.querySelector('#posts-list').innerHTML = posts.length > 0
      ? posts.map(post => renderPostCard(post)).join('')
      : '<p class="no-posts">Inga inlägg än. Var först att skapa ett!</p>';
    
    renderPagination(pagination);
  } catch (error) {
    console.error('Load posts error:', error);
    document.querySelector('#posts-list').innerHTML = '<p class="error">Kunde inte ladda inlägg</p>';
  }
}

function renderPagination(pagination) {
  if (pagination.totalPages <= 1) {
    document.querySelector('#pagination').innerHTML = '';
    return;
  }
  
  let html = '<div class="pagination">';
  
  if (pagination.currentPage > 1) {
    html += `<button onclick="window.goToPage(${pagination.currentPage - 1})">← Föregående</button>`;
  }
  
  html += `<span>Sida ${pagination.currentPage} av ${pagination.totalPages}</span>`;
  
  if (pagination.hasMore) {
    html += `<button onclick="window.goToPage(${pagination.currentPage + 1})">Nästa →</button>`;
  }
  
  html += '</div>';
  
  document.querySelector('#pagination').innerHTML = html;
}

async function handleVote(postId, voteType) {
  if (!isAuthenticated()) {
    alert('Du måste vara inloggad för att rösta');
    return;
  }
  
  try {
    const { ok, data } = await api.post(`/posts/${postId}/vote`, { voteType });
    
    if (ok) {
      // Update post in list
      const postCard = document.querySelector(`[data-id="${postId}"]`);
      const scoreEl = postCard.querySelector('.vote-score');
      scoreEl.textContent = data.data.voteScore;
      
      // Update vote buttons
      const upBtn = postCard.querySelector('.vote-up');
      const downBtn = postCard.querySelector('.vote-down');
      upBtn.classList.remove('voted-up');
      downBtn.classList.remove('voted-down');
      
      if (data.data.userVote === 'up') {
        upBtn.classList.add('voted-up');
      } else if (data.data.userVote === 'down') {
        downBtn.classList.add('voted-down');
      }
    }
  } catch (error) {
    console.error('Vote error:', error);
  }
}

function goToPage(page) {
  currentPage = page;
  loadPosts();
  window.scrollTo(0, 0);
}

function handleSortChange() {
  currentPage = 1;
  loadPosts();
}

// Expose functions globally
window.showView = (view) => {
  currentView = view;
  if (view === 'home') renderHome();
  else if (view === 'login') renderLogin();
  else if (view === 'signup') renderSignup();
  else if (view === 'createPost') renderCreatePost();
};

window.handleVote = handleVote;
window.goToPage = goToPage;
window.handleSortChange = handleSortChange;
window.handleLogout = logout;

// Init
if (isAuthenticated()) {
  renderHome();
} else {
  renderHome(); // Can view posts without login
}
```

---

**Steg 4: Basic CSS (10 min)**

**`src/style.css`:**
```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: #f5f7fa;
  color: #333;
}

.navbar {
  background: white;
  border-bottom: 1px solid #e1e8ed;
  padding: 1rem 0;
  position: sticky;
  top: 0;
  z-index: 100;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}

.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.nav-logo {
  font-size: 1.5rem;
  color: #1DA1F2;
  cursor: pointer;
}

.nav-user, .nav-buttons {
  display: flex;
  gap: 1rem;
  align-items: center;
}

.container {
  max-width: 800px;
  margin: 2rem auto;
  padding: 0 20px;
}

.posts-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.post-card {
  background: white;
  border: 1px solid #e1e8ed;
  border-radius: 8px;
  padding: 1.5rem;
  margin-bottom: 1rem;
  display: flex;
  gap: 1rem;
  transition: box-shadow 0.2s;
}

.post-card:hover {
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.post-votes {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.vote-btn {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  padding: 0.25rem 0.5rem;
  color: #657786;
  transition: color 0.2s;
}

.vote-btn:hover {
  color: #1DA1F2;
}

.vote-up.voted-up {
  color: #17bf63;
}

.vote-down.voted-down {
  color: #e0245e;
}

.vote-score {
  font-weight: bold;
  font-size: 1.2rem;
  color: #1DA1F2;
}

.post-content {
  flex: 1;
}

.post-title {
  font-size: 1.3rem;
  margin-bottom: 0.5rem;
  cursor: pointer;
  color: #14171a;
}

.post-title:hover {
  color: #1DA1F2;
}

.post-preview {
  color: #657786;
  margin-bottom: 1rem;
  line-height: 1.5;
}

.post-meta {
  display: flex;
  gap: 1rem;
  font-size: 0.9rem;
  color: #657786;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin-top: 2rem;
}

button, .btn-primary, .btn-secondary {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1rem;
  transition: background 0.2s;
}

.btn-primary {
  background: #1DA1F2;
  color: white;
}

.btn-primary:hover {
  background: #1a91da;
}

.btn-secondary {
  background: white;
  color: #1DA1F2;
  border: 1px solid #1DA1F2;
}

.btn-secondary:hover {
  background: #f5f8fa;
}

.loading {
  text-align: center;
  padding: 3rem;
  color: #657786;
}

.error {
  color: #e0245e;
  text-align: center;
  padding: 2rem;
}

.no-posts {
  text-align: center;
  padding: 3rem;
  color: #657786;
}
```

---

**Steg 5: Testa (5 min)**

```bash
npm run dev
```

Öppna http://localhost:5173

**Du ska se:**
- ✅ Navbar med logo
- ✅ Lista med posts (om backend har data)
- ✅ Vote buttons
- ✅ Sort dropdown
- ✅ Pagination (om > 10 posts)

**Test voting:**
- Logga in först
- Klicka upvote → score ökar, button blir grön
- Klicka igen → score minskar, button normal
- Klicka downvote → score minskar, button blir röd

---

**📝 Commit:**
```bash
git add .
git commit -m "Add frontend posts feed with voting"
git remote add origin https://github.com/DittAnvändarnamn/forum-frontend.git
git push -u origin main
```

---

## Lektion 2: Create Post Form (60 min)

### 🎓 Lärandemål
- Bygga create post formulär
- Frontend validering
- Skicka POST request
- Redirect efter create

---

### ✏️ Övning 29: Create Post (60 min)

**Steg 1: Login & Signup forms först (20 min)**

**Lägg till i `main.js`:**
```javascript
function renderLogin() {
  document.querySelector('#app').innerHTML = `
    ${renderNavbar()}
    <div class="container">
      <div class="auth-card">
        <h2>Logga in</h2>
        <form id="login-form">
          <input type="email" id="email" placeholder="Email" required />
          <input type="password" id="password" placeholder="Lösenord" required />
          <div id="error-msg" class="error-msg"></div>
          <button type="submit" class="btn-primary">Logga in</button>
        </form>
        <p>Inget konto? <a href="#" onclick="window.showView('signup')">Skapa konto</a></p>
      </div>
    </div>
  `;
  
  document.querySelector('#login-form').addEventListener('submit', handleLogin);
}

async function handleLogin(e) {
  e.preventDefault();
  
  const email = document.querySelector('#email').value;
  const password = document.querySelector('#password').value;
  const errorMsg = document.querySelector('#error-msg');
  
  try {
    const { ok, data } = await api.post('/auth/login', { email, password });
    
    if (ok) {
      localStorage.setItem('token', data.token);
      localStorage.setItem('user', JSON.stringify(data.user));
      window.showView('home');
    } else {
      errorMsg.textContent = data.message;
    }
  } catch (error) {
    errorMsg.textContent = 'Kunde inte logga in';
  }
}

function renderSignup() {
  document.querySelector('#app').innerHTML = `
    ${renderNavbar()}
    <div class="container">
      <div class="auth-card">
        <h2>Skapa konto</h2>
        <form id="signup-form">
          <input type="text" id="username" placeholder="Användarnamn (3-20 tecken)" required />
          <input type="email" id="email" placeholder="Email" required />
          <input type="password" id="password" placeholder="Lösenord (minst 6 tecken)" required />
          <div id="error-msg" class="error-msg"></div>
          <button type="submit" class="btn-primary">Skapa konto</button>
        </form>
        <p>Har konto? <a href="#" onclick="window.showView('login')">Logga in</a></p>
      </div>
    </div>
  `;
  
  document.querySelector('#signup-form').addEventListener('submit', handleSignup);
}

async function handleSignup(e) {
  e.preventDefault();
  
  const username = document.querySelector('#username').value;
  const email = document.querySelector('#email').value;
  const password = document.querySelector('#password').value;
  const errorMsg = document.querySelector('#error-msg');
  
  if (username.length < 3 || username.length > 20) {
    errorMsg.textContent = 'Användarnamn måste vara 3-20 tecken';
    return;
  }
  
  if (password.length < 6) {
    errorMsg.textContent = 'Lösenord måste vara minst 6 tecken';
    return;
  }
  
  try {
    const { ok, data } = await api.post('/auth/signup', { username, email, password });
    
    if (ok) {
      localStorage.setItem('token', data.token);
      localStorage.setItem('user', JSON.stringify(data.user));
      window.showView('home');
    } else {
      errorMsg.textContent = data.message;
    }
  } catch (error) {
    errorMsg.textContent = 'Kunde inte skapa konto';
  }
}

window.renderLogin = renderLogin;
window.renderSignup = renderSignup;
```

**CSS för auth forms:**
```css
.auth-card {
  max-width: 400px;
  margin: 3rem auto;
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

.auth-card form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.auth-card input {
  padding: 0.75rem;
  border: 1px solid #e1e8ed;
  border-radius: 6px;
  font-size: 1rem;
}

.auth-card input:focus {
  outline: none;
  border-color: #1DA1F2;
}

.error-msg {
  color: #e0245e;
  font-size: 0.9rem;
}

.auth-card a {
  color: #1DA1F2;
  text-decoration: none;
}
```

---

**Steg 2: Create post form (20 min)**

**Lägg till i `main.js`:**
```javascript
function renderCreatePost() {
  if (!isAuthenticated()) {
    window.showView('login');
    return;
  }
  
  document.querySelector('#app').innerHTML = `
    ${renderNavbar()}
    <div class="container">
      <div class="create-post-card">
        <h2>Skapa nytt inlägg</h2>
        <form id="create-post-form">
          <input 
            type="text" 
            id="title" 
            placeholder="Titel (3-200 tecken)" 
            required 
            minlength="3"
            maxlength="200"
          />
          <div id="title-counter" class="char-counter">0 / 200</div>
          
          <textarea 
            id="content" 
            placeholder="Innehåll (minst 10 tecken)" 
            required 
            minlength="10"
            maxlength="5000"
            rows="10"
          ></textarea>
          <div id="content-counter" class="char-counter">0 / 5000</div>
          
          <div id="error-msg" class="error-msg"></div>
          
          <div class="form-buttons">
            <button type="button" onclick="window.showView('home')" class="btn-secondary">
              Avbryt
            </button>
            <button type="submit" class="btn-primary">
              Publicera
            </button>
          </div>
        </form>
      </div>
    </div>
  `;
  
  // Character counters
  const titleInput = document.querySelector('#title');
  const contentInput = document.querySelector('#content');
  
  titleInput.addEventListener('input', () => {
    document.querySelector('#title-counter').textContent = 
      `${titleInput.value.length} / 200`;
  });
  
  contentInput.addEventListener('input', () => {
    document.querySelector('#content-counter').textContent = 
      `${contentInput.value.length} / 5000`;
  });
  
  document.querySelector('#create-post-form').addEventListener('submit', handleCreatePost);
}

async function handleCreatePost(e) {
  e.preventDefault();
  
  const title = document.querySelector('#title').value.trim();
  const content = document.querySelector('#content').value.trim();
  const errorMsg = document.querySelector('#error-msg');
  
  if (title.length < 3) {
    errorMsg.textContent = 'Titel måste vara minst 3 tecken';
    return;
  }
  
  if (content.length < 10) {
    errorMsg.textContent = 'Innehåll måste vara minst 10 tecken';
    return;
  }
  
  const submitBtn = document.querySelector('button[type="submit"]');
  submitBtn.disabled = true;
  submitBtn.textContent = 'Publicerar...';
  
  try {
    const { ok, data } = await api.post('/posts', { title, content });
    
    if (ok) {
      // Success - go to new post
      window.showPost(data.data._id);
    } else {
      errorMsg.textContent = data.message;
      submitBtn.disabled = false;
      submitBtn.textContent = 'Publicera';
    }
  } catch (error) {
    errorMsg.textContent = 'Kunde inte skapa inlägg';
    submitBtn.disabled = false;
    submitBtn.textContent = 'Publicera';
  }
}

window.renderCreatePost = renderCreatePost;
```

**CSS:**
```css
.create-post-card {
  max-width: 700px;
  margin: 2rem auto;
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

.create-post-card form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.create-post-card input,
.create-post-card textarea {
  padding: 0.75rem;
  border: 1px solid #e1e8ed;
  border-radius: 6px;
  font-size: 1rem;
  font-family: inherit;
}

.char-counter {
  font-size: 0.85rem;
  color: #657786;
  text-align: right;
  margin-top: -0.5rem;
}

.form-buttons {
  display: flex;
  gap: 1rem;
  justify-content: flex-end;
}
```

---

**Steg 3: Post detail view (20 min)**

```javascript
async function showPost(postId) {
  document.querySelector('#app').innerHTML = `
    ${renderNavbar()}
    <div class="container">
      <div class="loading">Laddar inlägg...</div>
    </div>
  `;
  
  try {
    const { ok, data } = await api.get(`/posts/${postId}`);
    
    if (!ok) {
      throw new Error('Post not found');
    }
    
    const post = data.data;
    const user = getCurrentUser();
    const isAuthor = user && post.author._id === user.id;
    
    document.querySelector('#app').innerHTML = `
      ${renderNavbar()}
      <div class="container">
        <button onclick="window.showView('home')" class="btn-back">← Tillbaka</button>
        
        <div class="post-detail">
          <div class="post-votes">
            <button class="vote-btn vote-up" onclick="window.handleVote('${post._id}', 'up')">▲</button>
            <span class="vote-score">${post.voteScore || 0}</span>
            <button class="vote-btn vote-down" onclick="window.handleVote('${post._id}', 'down')">▼</button>
          </div>
          
          <div class="post-detail-content">
            <h1>${post.title}</h1>
            <div class="post-meta">
              <span>👤 ${post.author.username}</span>
              <span>📅 ${new Date(post.createdAt).toLocaleDateString('sv-SE', { 
                year: 'numeric', month: 'long', day: 'numeric', hour: '2-digit', minute: '2-digit'
              })}</span>
            </div>
            <p class="post-body">${post.content}</p>
            
            ${isAuthor ? `
              <div class="post-actions">
                <button onclick="window.editPost('${post._id}')" class="btn-secondary">Redigera</button>
                <button onclick="window.deletePost('${post._id}')" class="btn-danger">Radera</button>
              </div>
            ` : ''}
          </div>
        </div>
        
        <div class="comments-section">
          <h3>💬 Kommentarer (${post.comments.length})</h3>
          <div id="comments-list">
            ${post.comments.length > 0 
              ? post.comments.map(c => `
                  <div class="comment">
                    <strong>${c.author.username}</strong>
                    <p>${c.content}</p>
                    <span class="comment-date">${new Date(c.createdAt).toLocaleDateString('sv-SE')}</span>
                  </div>
                `).join('')
              : '<p class="no-comments">Inga kommentarer än.</p>'
            }
          </div>
          
          ${user ? `
            <form id="comment-form" class="comment-form">
              <textarea id="comment-content" placeholder="Skriv en kommentar..." required></textarea>
              <button type="submit" class="btn-primary">Kommentera</button>
            </form>
          ` : '<p class="login-prompt">Logga in för att kommentera</p>'}
        </div>
      </div>
    `;
    
    if (user) {
      document.querySelector('#comment-form').addEventListener('submit', async (e) => {
        e.preventDefault();
        await handleAddComment(postId);
      });
    }
  } catch (error) {
    document.querySelector('#app').innerHTML = `
      ${renderNavbar()}
      <div class="container">
        <p class="error">Kunde inte ladda inlägg</p>
        <button onclick="window.showView('home')" class="btn-primary">Tillbaka till start</button>
      </div>
    `;
  }
}

async function handleAddComment(postId) {
  const content = document.querySelector('#comment-content').value.trim();
  
  if (content.length < 1) return;
  
  try {
    const { ok } = await api.post(`/posts/${postId}/comments`, { content });
    
    if (ok) {
      // Reload post to show new comment
      window.showPost(postId);
    }
  } catch (error) {
    alert('Kunde inte lägga till kommentar');
  }
}

window.showPost = showPost;
```

**CSS:**
```css
.btn-back {
  margin-bottom: 1rem;
  background: white;
  border: 1px solid #e1e8ed;
  color: #657786;
}

.post-detail {
  background: white;
  border: 1px solid #e1e8ed;
  border-radius: 8px;
  padding: 2rem;
  display: flex;
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.post-detail-content {
  flex: 1;
}

.post-detail h1 {
  font-size: 2rem;
  margin-bottom: 1rem;
  color: #14171a;
}

.post-body {
  line-height: 1.6;
  white-space: pre-wrap;
  margin-top: 1.5rem;
}

.post-actions {
  display: flex;
  gap: 1rem;
  margin-top: 1.5rem;
}

.btn-danger {
  background: #e0245e;
  color: white;
}

.btn-danger:hover {
  background: #c91f52;
}

.comments-section {
  background: white;
  border: 1px solid #e1e8ed;
  border-radius: 8px;
  padding: 2rem;
}

.comment {
  border-bottom: 1px solid #e1e8ed;
  padding: 1rem 0;
}

.comment:last-child {
  border-bottom: none;
}

.comment p {
  margin: 0.5rem 0;
  color: #14171a;
}

.comment-date {
  font-size: 0.85rem;
  color: #657786;
}

.comment-form {
  margin-top: 2rem;
  display: flex;
  gap: 1rem;
}

.comment-form textarea {
  flex: 1;
  padding: 0.75rem;
  border: 1px solid #e1e8ed;
  border-radius: 6px;
  font-family: inherit;
}

.no-comments {
  text-align: center;
  color: #657786;
  padding: 2rem;
}

.login-prompt {
  text-align: center;
  color: #657786;
  margin-top: 2rem;
}
```

---

**Testa (5 min)**

1. Klicka "Nytt inlägg"
2. Fyll i titel och innehåll
3. Klicka "Publicera"
4. Du ska komma till post detail view
5. Testa lägg till kommentar
6. Gå tillbaka till home → post ska synas i listan

---

**📝 Commit:**
```bash
git add .
git commit -m "Add create post and post detail views"
git push
```

---

## 🏠 Hemuppgift Vecka 15

**Uppgift:** Lägg till edit post funktionalitet

**Krav:**
1. "Redigera" button på post detail (bara för author)
2. Form förfylld med befintlig data
3. PUT request till /posts/:id
4. Redirect tillbaka till post efter save

**Bonus:** Lägg till markdown preview i create/edit forms

---

Filen blir för lång, fortsätter i nästa meddelande med Vecka 16-20...

<a name="vecka-16"></a>
# 📚 VECKA 16: Frontend Interactions

---

## Lektion 1: Edit & Delete Post (60 min)

### 🎓 Lärandemål
- Implementera edit funktionalitet
- Delete med confirmation
- Uppdatera UI efter ändringar

---

### ✏️ Övning 30: Edit & Delete (60 min)

**Lägg till i `main.js`:**

```javascript
async function editPost(postId) {
  try {
    const { ok, data } = await api.get(`/posts/${postId}`);
    
    if (!ok) throw new Error();
    
    const post = data.data;
    
    document.querySelector('#app').innerHTML = `
      ${renderNavbar()}
      <div class="container">
        <div class="create-post-card">
          <h2>Redigera inlägg</h2>
          <form id="edit-post-form">
            <input 
              type="text" 
              id="title" 
              value="${post.title}"
              required 
            />
            <textarea 
              id="content" 
              required 
              rows="10"
            >${post.content}</textarea>
            
            <div id="error-msg" class="error-msg"></div>
            
            <div class="form-buttons">
              <button type="button" onclick="window.showPost('${postId}')" class="btn-secondary">
                Avbryt
              </button>
              <button type="submit" class="btn-primary">
                Spara
              </button>
            </div>
          </form>
        </div>
      </div>
    `;
    
    document.querySelector('#edit-post-form').addEventListener('submit', async (e) => {
      e.preventDefault();
      
      const title = document.querySelector('#title').value.trim();
      const content = document.querySelector('#content').value.trim();
      
      const { ok } = await api.put(`/posts/${postId}`, { title, content });
      
      if (ok) {
        window.showPost(postId);
      }
    });
  } catch (error) {
    alert('Kunde inte ladda inlägg');
    window.showView('home');
  }
}

async function deletePost(postId) {
  if (!confirm('Är du säker på att du vill radera detta inlägg?')) {
    return;
  }
  
  try {
    const { ok } = await api.delete(`/posts/${postId}`);
    
    if (ok) {
      alert('Inlägget raderat');
      window.showView('home');
    } else {
      alert('Kunde inte radera inlägg');
    }
  } catch (error) {
    alert('Kunde inte radera inlägg');
  }
}

window.editPost = editPost;
window.deletePost = deletePost;
```

**Testa:**
1. Gå till ett post du äger
2. Klicka "Redigera"
3. Ändra titel/innehåll
4. Klicka "Spara"
5. Post uppdateras ✅
6. Klicka "Radera"
7. Bekräfta
8. Post raderas, redirect till home ✅

---

## Lektion 2: Enhanced Comments (60 min)

### 🎓 Lärandemål
- Edit comments
- Delete comments
- Reply to comments (bonus)

---

### ✏️ Övning 31: Comment Management (60 min)

**Uppdatera comment rendering i `showPost()`:**

```javascript
// I showPost(), uppdatera comments rendering:
${post.comments.length > 0 
  ? post.comments.map(c => {
      const isCommentAuthor = user && c.author._id === user.id;
      return `
        <div class="comment" data-comment-id="${c._id}">
          <div class="comment-header">
            <strong>${c.author.username}</strong>
            <span class="comment-date">${new Date(c.createdAt).toLocaleDateString('sv-SE')}</span>
          </div>
          <p class="comment-text">${c.content}</p>
          ${isCommentAuthor ? `
            <div class="comment-actions">
              <button onclick="window.editComment('${post._id}', '${c._id}')" class="btn-sm">Redigera</button>
              <button onclick="window.deleteComment('${post._id}', '${c._id}')" class="btn-sm btn-danger-sm">Radera</button>
            </div>
          ` : ''}
        </div>
      `;
    }).join('')
  : '<p class="no-comments">Inga kommentarer än.</p>'
}
```

**Lägg till comment functions:**

```javascript
async function editComment(postId, commentId) {
  const commentEl = document.querySelector(`[data-comment-id="${commentId}"]`);
  const currentText = commentEl.querySelector('.comment-text').textContent;
  
  commentEl.innerHTML = `
    <form class="edit-comment-form" onsubmit="window.saveCommentEdit(event, '${postId}', '${commentId}')">
      <textarea required>${currentText}</textarea>
      <div class="form-buttons">
        <button type="button" onclick="window.showPost('${postId}')" class="btn-sm">Avbryt</button>
        <button type="submit" class="btn-sm btn-primary">Spara</button>
      </div>
    </form>
  `;
}

async function saveCommentEdit(e, postId, commentId) {
  e.preventDefault();
  
  const content = e.target.querySelector('textarea').value.trim();
  
  try {
    const { ok } = await api.put(`/posts/${postId}/comments/${commentId}`, { content });
    
    if (ok) {
      window.showPost(postId);
    }
  } catch (error) {
    alert('Kunde inte uppdatera kommentar');
  }
}

async function deleteComment(postId, commentId) {
  if (!confirm('Radera kommentar?')) return;
  
  try {
    const { ok } = await api.delete(`/posts/${postId}/comments/${commentId}`);
    
    if (ok) {
      window.showPost(postId);
    }
  } catch (error) {
    alert('Kunde inte radera kommentar');
  }
}

window.editComment = editComment;
window.saveCommentEdit = saveCommentEdit;
window.deleteComment = deleteComment;
```

**CSS:**
```css
.comment-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 0.5rem;
}

.comment-actions {
  display: flex;
  gap: 0.5rem;
  margin-top: 0.5rem;
}

.btn-sm {
  padding: 0.25rem 0.75rem;
  font-size: 0.85rem;
}

.btn-danger-sm {
  background: #e0245e;
  color: white;
}

.edit-comment-form textarea {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #e1e8ed;
  border-radius: 4px;
  margin-bottom: 0.5rem;
}
```

---

<a name="vecka-17"></a>
# 📚 VECKA 17: User Features

---

## Lektion 1: User Profiles (60 min)

### 🎓 Lärandemål
- Visa user profile
- Lista user's posts
- User statistics

---

### ✏️ Övning 32: User Profile (60 min)

**Backend först - lägg till i `routes/users.mjs`:**

```javascript
import express from 'express';
import User from '../models/User.mjs';
import Post from '../models/Post.mjs';

const router = express.Router();

/**
 * GET /users/:id
 * Get user profile with stats
 */
router.get('/:id', async (req, res) => {
  try {
    const user = await User.findById(req.params.id).select('-password');
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Get user's posts
    const posts = await Post.find({ author: user._id })
      .populate('author', 'username')
      .sort({ createdAt: -1 });
    
    // Calculate stats
    const totalUpvotes = posts.reduce((sum, p) => sum + p.votes.upvotes, 0);
    const totalComments = posts.reduce((sum, p) => sum + p.comments.length, 0);
    
    res.json({
      success: true,
      data: {
        user: {
          id: user._id,
          username: user.username,
          role: user.role,
          createdAt: user.createdAt
        },
        stats: {
          totalPosts: posts.length,
          totalUpvotes,
          totalComments
        },
        posts
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});

export default router;
```

**Frontend - lägg till i `main.js`:**

```javascript
async function showUserProfile(userId) {
  document.querySelector('#app').innerHTML = `
    ${renderNavbar()}
    <div class="container">
      <div class="loading">Laddar profil...</div>
    </div>
  `;
  
  try {
    const { ok, data } = await api.get(`/users/${userId}`);
    
    if (!ok) throw new Error();
    
    const { user, stats, posts } = data.data;
    
    document.querySelector('#app').innerHTML = `
      ${renderNavbar()}
      <div class="container">
        <button onclick="window.showView('home')" class="btn-back">← Tillbaka</button>
        
        <div class="profile-card">
          <div class="profile-header">
            <div class="profile-avatar">
              ${user.username.charAt(0).toUpperCase()}
            </div>
            <div class="profile-info">
              <h1>${user.username}</h1>
              <span class="role-badge">${user.role}</span>
              <p>Medlem sedan ${new Date(user.createdAt).toLocaleDateString('sv-SE')}</p>
            </div>
          </div>
          
          <div class="profile-stats">
            <div class="stat">
              <span class="stat-value">${stats.totalPosts}</span>
              <span class="stat-label">Inlägg</span>
            </div>
            <div class="stat">
              <span class="stat-value">${stats.totalUpvotes}</span>
              <span class="stat-label">Upvotes</span>
            </div>
            <div class="stat">
              <span class="stat-value">${stats.totalComments}</span>
              <span class="stat-label">Kommentarer</span>
            </div>
          </div>
        </div>
        
        <div class="user-posts">
          <h2>${user.username}s inlägg</h2>
          ${posts.length > 0 
            ? posts.map(post => renderPostCard(post)).join('')
            : '<p class="no-posts">Inga inlägg än</p>'
          }
        </div>
      </div>
    `;
  } catch (error) {
    document.querySelector('#app').innerHTML = `
      ${renderNavbar()}
      <div class="container">
        <p class="error">Kunde inte ladda profil</p>
      </div>
    `;
  }
}

window.showUserProfile = showUserProfile;
```

**Uppdatera post rendering för att länka till profiles:**

```javascript
// I renderPostCard(), ändra author till klickbar:
<span class="author-link" onclick="window.showUserProfile('${post.author._id}')">
  👤 ${post.author.username}
</span>
```

**CSS:**
```css
.profile-card {
  background: white;
  border: 1px solid #e1e8ed;
  border-radius: 8px;
  padding: 2rem;
  margin-bottom: 2rem;
}

.profile-header {
  display: flex;
  gap: 2rem;
  align-items: center;
  margin-bottom: 2rem;
}

.profile-avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
  color: white;
  font-weight: bold;
}

.profile-info h1 {
  margin-bottom: 0.5rem;
}

.role-badge {
  background: #1DA1F2;
  color: white;
  padding: 0.25rem 0.75rem;
  border-radius: 12px;
  font-size: 0.85rem;
  margin-left: 0.5rem;
}

.profile-stats {
  display: flex;
  gap: 3rem;
  padding-top: 2rem;
  border-top: 1px solid #e1e8ed;
}

.stat {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.stat-value {
  font-size: 2rem;
  font-weight: bold;
  color: #1DA1F2;
}

.stat-label {
  color: #657786;
  font-size: 0.9rem;
}

.author-link {
  cursor: pointer;
  color: #1DA1F2;
}

.author-link:hover {
  text-decoration: underline;
}
```

**Testa:**
1. Klicka på användarnamn i ett post
2. User profile visas med stats
3. Lista av user's posts visas
4. Klicka tillbaka
5. Fungerar ✅

---

## Lektion 2: My Profile & Edit (60 min)

### ✏️ Övning 33: Edit Profile (60 min)

**Backend - lägg till PUT endpoint:**

```javascript
// I routes/users.mjs
import { requireAuth } from '../middleware/auth.mjs';

router.put('/me', requireAuth, async (req, res) => {
  try {
    const { username, email } = req.body;
    
    const user = await User.findById(req.user.id);
    
    if (!user) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }
    
    // Check if username/email taken
    if (username && username !== user.username) {
      const existing = await User.findOne({ username });
      if (existing) {
        return res.status(400).json({
          success: false,
          message: 'Username taken'
        });
      }
      user.username = username;
    }
    
    if (email && email !== user.email) {
      const existing = await User.findOne({ email });
      if (existing) {
        return res.status(400).json({
          success: false,
          message: 'Email taken'
        });
      }
      user.email = email;
    }
    
    await user.save();
    
    res.json({
      success: true,
      message: 'Profile updated',
      user: {
        id: user._id,
        username: user.username,
        email: user.email,
        role: user.role
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Frontend:**

```javascript
function renderMyProfile() {
  const user = getCurrentUser();
  
  if (!user) {
    window.showView('login');
    return;
  }
  
  window.showUserProfile(user.id);
}

// Lägg till "Min profil" link i navbar
function renderNavbar() {
  const user = getCurrentUser();
  
  return `
    <nav class="navbar">
      <div class="nav-container">
        <h1 class="nav-logo" onclick="window.showView('home')">💬 Forum</h1>
        ${user ? `
          <div class="nav-user">
            <span class="author-link" onclick="window.renderMyProfile()">👤 ${user.username}</span>
            <button onclick="window.showView('createPost')" class="btn-primary">Nytt inlägg</button>
            <button onclick="window.handleLogout()" class="btn-secondary">Logga ut</button>
          </div>
        ` : `
          <div class="nav-buttons">
            <button onclick="window.showView('login')" class="btn-primary">Logga in</button>
            <button onclick="window.showView('signup')" class="btn-secondary">Skapa konto</button>
          </div>
        `}
      </div>
    </nav>
  `;
}

window.renderMyProfile = renderMyProfile;
```

---

<a name="vecka-18"></a>
# 📚 VECKA 18: Advanced Features

---

## Lektion 1: Search & Filter (60 min)

### ✏️ Övning 34: Search System (60 min)

**Backend - lägg till i `routes/posts.mjs`:**

```javascript
// Uppdatera GET /posts endpoint
router.get('/', async (req, res) => {
  try {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const skip = (page - 1) * limit;
    
    const sortBy = req.query.sortBy || 'createdAt';
    const sortOrder = req.query.sortOrder === 'asc' ? 1 : -1;
    
    // Search query
    const search = req.query.search || '';
    
    // Build filter
    const filter = {};
    if (search) {
      filter.$or = [
        { title: { $regex: search, $options: 'i' } },
        { content: { $regex: search, $options: 'i' } }
      ];
    }
    
    const posts = await Post.find(filter)
      .populate('author', 'username email')
      .sort({ [sortBy]: sortOrder })
      .skip(skip)
      .limit(limit);
    
    const total = await Post.countDocuments(filter);
    const totalPages = Math.ceil(total / limit);
    
    res.json({
      success: true,
      data: {
        posts,
        pagination: {
          currentPage: page,
          totalPages,
          totalPosts: total,
          hasMore: page < totalPages
        },
        search
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Server error'
    });
  }
});
```

**Frontend:**

```javascript
// Uppdatera renderHome()
async function renderHome() {
  document.querySelector('#app').innerHTML = `
    ${renderNavbar()}
    <div class="container">
      <div class="posts-header">
        <h2>Senaste inläggen</h2>
        <div class="header-controls">
          <input 
            type="text" 
            id="search-input" 
            placeholder="Sök inlägg..." 
            onkeyup="window.handleSearchKeyup(event)"
          />
          <select id="sort-select" onchange="window.handleSortChange()">
            <option value="createdAt">Senaste</option>
            <option value="votes.upvotes">Populärast</option>
          </select>
        </div>
      </div>
      <div id="posts-list">
        <div class="loading">Laddar inlägg...</div>
      </div>
      <div id="pagination"></div>
    </div>
  `;
  
  await loadPosts();
}

let searchTimeout;

function handleSearchKeyup(e) {
  clearTimeout(searchTimeout);
  
  searchTimeout = setTimeout(() => {
    currentPage = 1;
    loadPosts();
  }, 500); // Vänta 500ms efter sista tangenttryckning
}

async function loadPosts() {
  try {
    const sortBy = document.querySelector('#sort-select')?.value || 'createdAt';
    const search = document.querySelector('#search-input')?.value || '';
    
    const { ok, data } = await api.get(
      `/posts?page=${currentPage}&limit=10&sortBy=${sortBy}&sortOrder=desc&search=${encodeURIComponent(search)}`
    );
    
    if (!ok) throw new Error();
    
    posts = data.data.posts;
    const pagination = data.data.pagination;
    
    if (search && posts.length === 0) {
      document.querySelector('#posts-list').innerHTML = 
        `<p class="no-posts">Inga inlägg matchade "${search}"</p>`;
    } else if (posts.length === 0) {
      document.querySelector('#posts-list').innerHTML = 
        '<p class="no-posts">Inga inlägg än. Var först att skapa ett!</p>';
    } else {
      document.querySelector('#posts-list').innerHTML = 
        posts.map(post => renderPostCard(post)).join('');
    }
    
    renderPagination(pagination);
  } catch (error) {
    document.querySelector('#posts-list').innerHTML = 
      '<p class="error">Kunde inte ladda inlägg</p>';
  }
}

window.handleSearchKeyup = handleSearchKeyup;
```

**CSS:**
```css
.header-controls {
  display: flex;
  gap: 1rem;
}

#search-input {
  padding: 0.5rem 1rem;
  border: 1px solid #e1e8ed;
  border-radius: 6px;
  width: 250px;
}

#search-input:focus {
  outline: none;
  border-color: #1DA1F2;
}
```

---

## Lektion 2: Advanced Pagination (60 min)

### ✏️ Övning 35: Page Numbers (60 min)

**Uppdatera pagination rendering:**

```javascript
function renderPagination(pagination) {
  if (pagination.totalPages <= 1) {
    document.querySelector('#pagination').innerHTML = '';
    return;
  }
  
  const current = pagination.currentPage;
  const total = pagination.totalPages;
  
  let html = '<div class="pagination">';
  
  // Previous button
  if (current > 1) {
    html += `<button onclick="window.goToPage(${current - 1})" class="page-btn">← Föregående</button>`;
  }
  
  // Page numbers
  html += '<div class="page-numbers">';
  
  // Always show first page
  html += renderPageButton(1, current);
  
  // Show dots if needed
  if (current > 3) {
    html += '<span class="page-dots">...</span>';
  }
  
  // Show pages around current
  for (let i = Math.max(2, current - 1); i <= Math.min(total - 1, current + 1); i++) {
    html += renderPageButton(i, current);
  }
  
  // Show dots if needed
  if (current < total - 2) {
    html += '<span class="page-dots">...</span>';
  }
  
  // Always show last page
  if (total > 1) {
    html += renderPageButton(total, current);
  }
  
  html += '</div>';
  
  // Next button
  if (pagination.hasMore) {
    html += `<button onclick="window.goToPage(${current + 1})" class="page-btn">Nästa →</button>`;
  }
  
  html += '</div>';
  
  document.querySelector('#pagination').innerHTML = html;
}

function renderPageButton(pageNum, currentPage) {
  const isActive = pageNum === currentPage;
  return `
    <button 
      onclick="window.goToPage(${pageNum})" 
      class="page-num ${isActive ? 'active' : ''}"
    >
      ${pageNum}
    </button>
  `;
}
```

**CSS:**
```css
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  margin-top: 2rem;
  flex-wrap: wrap;
}

.page-numbers {
  display: flex;
  gap: 0.25rem;
}

.page-num {
  min-width: 40px;
  height: 40px;
  border: 1px solid #e1e8ed;
  background: white;
  border-radius: 4px;
  cursor: pointer;
}

.page-num:hover {
  background: #f5f8fa;
  border-color: #1DA1F2;
}

.page-num.active {
  background: #1DA1F2;
  color: white;
  border-color: #1DA1F2;
}

.page-dots {
  padding: 0 0.5rem;
  color: #657786;
}

.page-btn {
  padding: 0.5rem 1rem;
  background: white;
  border: 1px solid #e1e8ed;
  border-radius: 4px;
}
```

---

<a name="vecka-19"></a>
# 📚 VECKA 19: Polish & Categories

---

## Lektion 1: Categories System (60 min)

### ✏️ Övning 36: Add Categories (60 min)

**Backend - Category model:**

```javascript
// models/Category.mjs
import mongoose from 'mongoose';

const categorySchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    unique: true,
    trim: true
  },
  slug: {
    type: String,
    required: true,
    unique: true
  },
  description: String,
  color: {
    type: String,
    default: '#1DA1F2'
  }
}, { timestamps: true });

export default mongoose.model('Category', categorySchema);
```

**Uppdatera Post model:**

```javascript
// I Post model, lägg till:
category: {
  type: mongoose.Schema.Types.ObjectId,
  ref: 'Category',
  required: false
}
```

**Seed categories:**

```javascript
// scripts/seedCategories.mjs
import mongoose from 'mongoose';
import dotenv from 'dotenv';
import Category from '../models/Category.mjs';

dotenv.config();

await mongoose.connect(process.env.MONGODB_URI);

const categories = [
  { name: 'Allmänt', slug: 'allmant', description: 'Allmän diskussion', color: '#1DA1F2' },
  { name: 'Teknik', slug: 'teknik', description: 'Diskussioner om teknik', color: '#17bf63' },
  { name: 'Sport', slug: 'sport', description: 'Sport och fitness', color: '#f91880' },
  { name: 'Mat', slug: 'mat', description: 'Matrecept och tips', color: '#ffad1f' }
];

await Category.deleteMany({});
await Category.insertMany(categories);

console.log('Categories seeded!');
process.exit();
```

**Kör:**
```bash
node scripts/seedCategories.mjs
```

---

## Lektion 2: Final Polish & Testing (60 min)

### ✏️ Övning 37: Polish (60 min)

**Lägg till:**

1. **Toast notifications:**

```javascript
// utils/toast.js
export function showToast(message, type = 'info') {
  const existingToast = document.querySelector('.toast');
  if (existingToast) existingToast.remove();
  
  const toast = document.createElement('div');
  toast.className = `toast toast-${type}`;
  toast.textContent = message;
  document.body.appendChild(toast);
  
  setTimeout(() => toast.classList.add('show'), 10);
  
  setTimeout(() => {
    toast.classList.remove('show');
    setTimeout(() => toast.remove(), 300);
  }, 3000);
}
```

2. **Loading states överallt**

3. **Error boundaries**

4. **Responsive design:**

```css
@media (max-width: 768px) {
  .post-card {
    flex-direction: column;
  }
  
  .posts-header {
    flex-direction: column;
    gap: 1rem;
  }
  
  .header-controls {
    width: 100%;
  }
  
  #search-input {
    width: 100%;
  }
}
```

---

<a name="vecka-20"></a>
# 📚 VECKA 20: Documentation & Presentation

---

## Lektion 1: Documentation (60 min)

### ✏️ Övning 38: README (60 min)

**Skapa README.md i både backend och frontend:**

**Backend README.md:**

```markdown
# Forum Backend API

Express + MongoDB backend för forum-applikation.

## Features

- ✅ User authentication (JWT)
- ✅ Posts CRUD
- ✅ Comments system (embedded)
- ✅ Vote/like system
- ✅ Admin panel
- ✅ Moderator roles
- ✅ Search & pagination

## Tech Stack

- **Node.js** + **Express**
- **MongoDB** + **Mongoose**
- **JWT** för auth
- **bcrypt** för lösenord

## Installation

```bash
# Install dependencies
npm install

# Setup .env
cp .env.example .env
# Edit .env with your MongoDB URI and JWT secret

# Run
npm run dev
```

## API Endpoints

### Auth
- POST /auth/signup - Register
- POST /auth/login - Login
- GET /auth/me - Current user (protected)

### Posts
- GET /posts - List posts (supports pagination, search, sort)
- POST /posts - Create post (protected)
- GET /posts/:id - Get single post
- PUT /posts/:id - Update post (author only)
- DELETE /posts/:id - Delete post (author/mod/admin)

### Comments
- POST /posts/:id/comments - Add comment (protected)
- PUT /posts/:postId/comments/:commentId - Update comment (author)
- DELETE /posts/:postId/comments/:commentId - Delete comment (author/mod/admin)

### Votes
- POST /posts/:id/vote - Vote up/down (protected)
- GET /posts/:id/vote - Get user's vote

### Admin (admin only)
- GET /admin/stats - Dashboard stats
- GET /admin/users - List users
- PUT /admin/users/:id/role - Change user role
- DELETE /admin/users/:id - Delete user

## Environment Variables

```env
PORT=3000
MONGODB_URI=mongodb+srv://...
JWT_SECRET=your-secret-key-min-32-chars
```

## Project Structure

```
forum-backend/
├── models/
│   ├── User.mjs
│   └── Post.mjs
├── routes/
│   ├── auth.mjs
│   ├── posts.mjs
│   ├── users.mjs
│   └── admin.mjs
├── middleware/
│   └── auth.mjs
├── app.mjs
├── server.mjs
└── db.mjs
```

## License

MIT
```

**Frontend README.md:**

```markdown
# Forum Frontend

Vite + Vanilla JS frontend för forum-applikation.

## Features

- ✅ User authentication
- ✅ Create/edit/delete posts
- ✅ Comments
- ✅ Vote system
- ✅ User profiles
- ✅ Search & pagination
- ✅ Responsive design

## Tech Stack

- **Vite**
- **Vanilla JavaScript**
- **CSS**

## Installation

```bash
# Install
npm install

# Run
npm run dev
```

Backend måste köras på http://localhost:3000

## Project Structure

```
forum-frontend/
├── src/
│   ├── utils/
│   │   ├── api.js
│   │   └── auth.js
│   ├── main.js
│   └── style.css
└── index.html
```

## Features Overview

### Authentication
- Sign up
- Login
- JWT token storage
- Protected routes

### Posts
- List posts with pagination
- Search posts
- Sort by date/popularity
- Create/edit/delete own posts
- Vote (upvote/downvote)

### Comments
- Add comments
- Edit/delete own comments

### User Profiles
- View user profile
- User statistics
- User's posts

## License

MIT
```

---

## Lektion 2: Presentation & Reflection (60 min)

### ✏️ Övning 39: Final Presentation (60 min)

**Skapa presentation (5 minuter):**

**Struktur:**

**1. Demo (2 min)**
- Visa sign up
- Skapa post
- Kommentera
- Vote
- User profile

**2. En utmaning (1 min)**
"Det svåraste var vote-systemet. Jag behövde hantera toggle logic och uppdatera UI direkt. Lösningen var att returnera current vote state från backend."

**3. Säkerhet (1 min)**
"Jag implementerade bcrypt, JWT, input validering, och role-based access control."

**4. Stolt över (1 min)**
"Jag är stolt över pagination-systemet och search-funktionen som är smooth och snabb."

---

**Reflektion (1-2 sidor):**

**Mall:**

```
# Projektreflektion - Forum Application

## Vad gick bra?
- Router separation gjorde koden mycket enklare att hantera
- Mongoose populate var smidigt för relations
- Frontend-backend kommunikation fungerade bra med JWT

## Vad var utmanande?
- Vote-systemet var komplext med toggle logic
- Comment edit in-place var svårt först
- Pagination med page numbers tog tid

## Vad lärde jag mig?
- Hur man strukturerar större projekt
- Skillnad mellan embedded och referenced data
- Vikten av god error handling

## Hur löste jag buggar?
- Console.log överallt för debugging
- Testade varje endpoint i Postman innan frontend
- Läste Mongoose docs för populate syntax

## Vad skulle jag göra annorlunda?
- Planerat bättre från början (wireframes)
- Skrivit tester
- Använt TypeScript för type safety

## Framtida förbättringar
- WebSockets för real-time notifications
- Image upload för posts
- Markdown support
- Tag system
```

---

## 🎉 Grattis!

Du har nu byggt ett **komplett forum** med:

### Backend ✅
- Router separation
- User authentication (JWT + bcrypt)
- Posts CRUD
- Embedded comments
- Vote system
- Admin panel
- Moderator roles
- Search & pagination

### Frontend ✅
- Sign up / Login
- Posts feed
- Create/edit/delete posts
- Comments
- Vote buttons
- User profiles
- Search
- Responsive design

### Säkerhet ✅
- bcrypt password hashing
- JWT tokens
- Input validation
- Authorization checks
- Role-based access control

---

## 📚 Slutord

Detta projekt täcker alla betygskriterier:

**E-nivå:** ✅ Enkel dynamisk webbplats med CRUD
**C-nivå:** ✅ Relativt komplex med auth och relations
**A-nivå:** ✅ Komplex med admin, search, pagination

**Säkerhet:**
**E:** ✅ Bcrypt, .env
**C:** ✅ JWT, validering, middleware
**A:** ✅ Role-based, rate limiting (om implementerat)

**Planering/utvärdering:**
**E:** ✅ Enkel plan och reflektion
**C:** ✅ Detaljerad plan, bra reflektion
**A:** ✅ Omfattande plan, djup reflektion

---

**LYCKA TILL! 🚀**

**Du är nu redo att bygga professionella webbapplikationer!**
