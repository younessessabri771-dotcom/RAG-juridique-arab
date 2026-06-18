# 🚀 Guide de Déploiement — ChatBot Juridique RAG

## Architecture cible

```
Supabase (PostgreSQL)
        ↓
Backend FastAPI  ──→  Docker  ──→  Render
        ↑
Frontend React  ──→  Vercel
```

---

## Structure des fichiers de déploiement

```
chatBotJuridiques-RAG-PFA/
├── backend/
│   ├── Dockerfile              ← Image FastAPI (multi-stage build)
│   ├── .env.example            ← Template variables backend
│   └── requirements.txt
├── frontend/
│   ├── Dockerfile              ← Image React + nginx
│   ├── nginx.conf              ← Config nginx (SPA routing)
│   ├── vercel.json             ← Config Vercel (SPA routing)
│   └── .env.example            ← Template variables frontend
└── docker-compose.yml          ← Orchestration locale
```

---

## Étape 1 — Supabase (Base de données) ✅

> Base PostgreSQL déjà configurée.

- URL : `db.tysszzvnoyxrgrrxvmnj.supabase.co`
- Connexion via `DATABASE_URL` dans le fichier `backend/.env`
- Vérifier les tables : **Supabase Dashboard → Table Editor**

---

## Étape 2 — Docker (Test local)

**Pré-requis** : Docker Desktop (docker.com) installé et démarré.

### Configuration

1. Copier le template `.env` du backend :
```bash
cd chatBotJuridiques-RAG-PFA/backend
copy .env.example .env
# Puis remplir les vraies valeurs dans .env
```

2. Copier le template `.env` du frontend :
```bash
cd chatBotJuridiques-RAG-PFA/frontend
copy .env.example .env
# Puis remplir les vraies valeurs dans .env
```

### Lancer tout en local

```bash
cd chatBotJuridiques-RAG-PFA
docker-compose up --build
```

| Service | URL |
|---------|-----|
| 🌐 Frontend | http://localhost:80 |
| ⚙️ Backend  | http://localhost:8000 |
| 📖 API Docs | http://localhost:8000/docs |

---

## Étape 3 — Render (Backend FastAPI) 📋

> **Render** (render.com, free tier) — déploie depuis GitHub via Docker.

### 3.1 Configuration sur render.com

1. **New → Web Service**
2. Connecter le repo GitHub : `younessessabri771-dotcom/RAG-juridique-arab`
3. Paramètres :

| Paramètre | Valeur |
|-----------|--------|
| Root Directory | `chatBotJuridiques-RAG-PFA/backend` |
| Runtime | `Docker` |
| Dockerfile Path | `./Dockerfile` |
| Port | `8000` |

### 3.2 Variables d'environnement sur Render

Ajouter dans **Environment → Environment Variables** :

| Clé | Valeur |
|-----|--------|
| `DATABASE_URL` | `postgresql+asyncpg://postgres:[MOT_DE_PASSE]@db.tysszzvnoyxrgrrxvmnj.supabase.co:5432/postgres` |
| `CLERK_PUBLISHABLE_KEY` | Clé Clerk publishable |
| `CLERK_SECRET_KEY` | Secret Clerk |
| `CLERK_ISSUER` | URL Clerk issuer |
| `GOOGLE_API_KEY` | Clé Google Gemini |
| `OPENAI_API_KEY` | Clé OpenAI (extraction PDF) |
| `CHROMA_PERSIST_DIR` | `/app/chroma_db` |
| `CHROMA_COLLECTION_NAME` | `legal_docs_arabic` |
| `EMBEDDING_MODEL` | `BAAI/bge-m3` |
| `CORS_ORIGINS` | `["https://votre-app.vercel.app"]` |

> ‼️ Ne jamais committer les vraies clés dans le repo GitHub !

### 3.3 Disque persistant sur Render (obligatoire pour ChromaDB)

Render détruit les fichiers locaux à chaque redéploiement. Il faut ajouter un **Disk** :

1. Dans votre Web Service Render → **Disks → Add Disk**
2. Paramètres :

| Paramètre | Valeur |
|-----------|--------|
| Mount Path | `/app/chroma_db` |
| Size | `1 GB` (suffisant pour commencer) |

---

## Étape 4 — Vercel (Frontend React) 📋

> **Vercel** (vercel.com, free) — déploie automatiquement React/Vite.

### 4.1 Configuration sur vercel.com

1. **New Project → Import Git Repository**
2. Sélectionner le repo GitHub : `younessessabri771-dotcom/RAG-juridique-arab`
3. Paramètres :

| Paramètre | Valeur |
|-----------|--------|
| Framework Preset | `Vite` |
| Root Directory | `chatBotJuridiques-RAG-PFA/frontend` |
| Build Command | `npm run build` |
| Output Directory | `dist` |

### 4.2 Variables d'environnement sur Vercel

| Clé | Valeur |
|-----|--------|
| `VITE_CLERK_PUBLISHABLE_KEY` | Clé Clerk publishable |
| `VITE_API_BASE_URL` | URL backend Render (ex: `https://chatbot-juridique-api.onrender.com`) |

### 4.3 Après déploiement Vercel

Retourner sur **Render → Environment Variables** et mettre à jour :
```
CORS_ORIGINS=["https://votre-app-reelle.vercel.app"]
```

---

## Résumé des URLs finales

| Service | URL |
|---------|-----|
| Frontend | `https://votre-app.vercel.app` |
| Backend API | `https://chatbot-juridique-api.onrender.com` |
| API Docs | `https://chatbot-juridique-api.onrender.com/docs` |
| Database | Supabase Dashboard |

---

## État actuel

- [x] Étape 1 — Supabase configuré
- [x] Étape 2 — Fichiers Docker créés (backend + frontend + nginx)
- [x] Étape 2 — docker-compose.yml créé
- [x] Étape 2 — vercel.json créé
- [ ] Étape 3 — Déploiement Render (backend)
- [ ] Étape 4 — Déploiement Vercel (frontend)
