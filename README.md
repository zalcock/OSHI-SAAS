# Orion — AI Document Intelligence SaaS

A production-ready SaaS application for AI-powered document analysis, built with **React**, **Firebase**, and **Google Gemini**. Modelled after [orion.file.ai](https://orion.file.ai).

---

## ✨ Features

| Feature | Details |
|---|---|
| 🔐 **Authentication** | Email/password + Google OAuth via Firebase Auth |
| 📁 **File Library** | Upload PDFs, DOCX, XLSX, TXT, CSV (up to 25MB) with drag & drop |
| 💬 **AI Chat** | Ask questions about any document, powered by Google Gemini 1.5 Flash |
| 🗂 **Conversation History** | All chats stored in Firestore, real-time sync |
| 📊 **Document Preview** | Side panel with conversation history per file |
| ⚙️ **Settings** | Profile management, password change, plan display |
| 🔒 **Security** | Per-user Firestore rules, Storage rules, SOC2-ready patterns |
| 🚀 **Deploy** | Firebase Hosting with SPA rewrites, CDN caching headers |

---

## 🏗 Project Structure

```
orion-saas/
├── src/
│   ├── components/
│   │   ├── AppShell.jsx        # Sidebar navigation layout
│   │   ├── ProtectedRoute.jsx  # Auth guard for routes
│   │   └── UI.jsx              # Shared UI components
│   ├── hooks/
│   │   └── useAuth.jsx         # Auth context + Firebase Auth
│   ├── lib/
│   │   ├── firebase.js         # Firebase initialization
│   │   ├── db.js               # Firestore CRUD helpers
│   │   ├── storage.js          # Firebase Storage helpers
│   │   └── gemini.js           # Google Gemini API client
│   ├── pages/
│   │   ├── AuthPage.jsx        # Login / Signup / Reset
│   │   ├── DashboardPage.jsx   # File library + upload
│   │   ├── ChatPage.jsx        # AI document chat interface
│   │   ├── ConversationsPage.jsx # All conversations list
│   │   └── SettingsPage.jsx    # Account settings
│   ├── styles/
│   │   └── globals.css         # Design system + CSS variables
│   ├── App.jsx                 # Router
│   └── main.jsx                # Entry point
├── public/
│   └── favicon.svg
├── firestore.rules             # Firestore security rules
├── firestore.indexes.json      # Compound query indexes
├── storage.rules               # Storage security rules
├── firebase.json               # Firebase Hosting config
├── .env.example                # Environment variable template
└── vite.config.js
```

---

## 🚀 Quick Start

### 1. Clone & install

```bash
git clone <your-repo>
cd orion-saas
npm install
```

### 2. Create a Firebase project

1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → follow the setup wizard
3. In your project, enable:
   - **Authentication** → Sign-in methods → Enable **Email/Password** and **Google**
   - **Firestore Database** → Create in production mode
   - **Storage** → Create bucket

### 3. Get your Firebase config

Firebase Console → Project Settings → Your Apps → **Add app** (Web) → Copy the config object.

### 4. Get your Gemini API key

1. Go to [aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)
2. Click **Create API key**
3. Copy the key

### 5. Configure environment variables

```bash
cp .env.example .env
```

Edit `.env` and fill in your values:

```env
VITE_FIREBASE_API_KEY=AIzaSy...
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=123456789
VITE_FIREBASE_APP_ID=1:123456789:web:abc123

VITE_GEMINI_API_KEY=AIzaSy...
```

### 6. Deploy Firebase security rules

```bash
npm install -g firebase-tools
firebase login
firebase init   # Select Firestore, Storage, Hosting — use existing project
firebase deploy --only firestore:rules,storage
```

### 7. Run locally

```bash
npm run dev
```

Open [http://localhost:5173](http://localhost:5173)

---

## 🌐 Deploy to Firebase Hosting

```bash
npm run build
firebase deploy --only hosting
```

Your app will be live at `https://your-project.web.app`

---

## 🗄 Firestore Data Model

```
users/
  {uid}/
    email, displayName, plan, fileCount, createdAt

files/
  {fileId}/
    uid, name, size, mimeType, storageUrl, storagePath
    typeLabel, typeColor, extractedText, status, createdAt

conversations/
  {convId}/
    uid, fileId, fileName, title, lastMessage, updatedAt
    messages/
      {msgId}/
        role ("user" | "assistant"), content, createdAt
```

---

## 🔒 Security

- **Firestore rules**: Users can only read/write their own documents
- **Storage rules**: Users can only access files under `users/{uid}/`
- **Max file size**: 25MB enforced in both client and Storage rules
- **API keys**: All keys are in `.env`, never committed to git
- **Auth**: Firebase Auth handles token refresh, session management

---

## 🔧 Upgrading to Full PDF Text Extraction

For production PDF text extraction (so Gemini can actually read PDF content):

### Option A — Gemini Files API (recommended)
Use the [Gemini Files API](https://ai.google.dev/gemini-api/docs/document-processing) to upload the PDF directly to Gemini for multimodal processing.

### Option B — Cloud Function + PDF parser
Deploy a Firebase Cloud Function that uses `pdf-parse` or `pdfjs-dist` to extract text server-side on upload.

```js
// functions/src/index.js (example)
exports.onFileUpload = functions.storage.object().onFinalize(async (object) => {
  const pdfText = await extractPdfText(object.name)
  await admin.firestore().doc(`files/${fileId}`).update({ extractedText: pdfText })
})
```

---

## 💰 SaaS Monetization

To add paid plans:

1. **Stripe integration** — use [Firebase Extensions: Run Payments with Stripe](https://extensions.dev/extensions/stripe/firestore-stripe-payments)
2. **Plan enforcement** — check `profile.plan` in the UI and in Cloud Functions
3. **Usage tracking** — increment counters in Firestore on each AI query

---

## 📦 Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, React Router v6, Vite |
| Auth | Firebase Authentication |
| Database | Cloud Firestore (real-time) |
| File Storage | Firebase Storage |
| AI | Google Gemini 1.5 Flash |
| Markdown | react-markdown + remark-gfm |
| File Upload | react-dropzone |
| Notifications | react-hot-toast |
| Hosting | Firebase Hosting |

---

## 📄 License

MIT — build whatever you want with this.
