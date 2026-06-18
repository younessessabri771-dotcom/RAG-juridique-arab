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

## Étape 1 — Supabase (Base de données) ✅

> Base PostgreSQL déjà configurée.

- URL : `db.tysszzvnoyxrgrrxvmnj.supabase.co`
- Connexion via `DATABASE_URL` dans le fichier `.env`
- Vérifier les tables : **Supabase Dashboard → Table Editor**

---

## Étape 2 — Docker (Test local)

**Pré-requis** : Docker Desktop (site web : docker.com, free) installé.

### Fichiers créés

| Fichier | Rôle |
|---------|------|
| `backend/Dockerfile` | Image FastAPI (multi-stage build) |
| `frontend/Dockerfile` | Image React + nginx |
| `docker-compose.yml` | Orchestration locale |
| `.env.production.example` | Template variables de prod |

### Commande pour tester en local

```bash
# À la racine du projet
cd "c:\Users\PC\Desktop\rag projet finalisation\chatBotJuridiques-RAG-PFA"

# Lancer tout
docker-compose up --build
```

- 🌐 Frontend : http://localhost:80
- ⚙️ Backend  : http://localhost:8000
- 📖 API Docs : http://localhost:8000/docs

---

## Étape 3 — Render (Backend FastAPI) 📋

> **Render** (site web : render.com, free tier) — déploie depuis GitHub via Docker.

### 3.1 Pousser sur GitHub

```bash
git add backend/Dockerfile docker-compose.yml .env.production.example
git commit -m "feat: add Docker configuration"
git push origin main
```

### 3.2 Configuration sur render.com

1. **New → Web Service**
2. Connecter le repo GitHub : `amtout-oualid/chatBotJuridiques-RAG-PFA`
3. Paramètres :

| Paramètre | Valeur |
|-----------|--------|
| Root Directory | `backend` |
| Runtime | `Docker` |
| Dockerfile Path | `./Dockerfile` |
| Port | `8000` |

### 3.3 Variables d'environnement sur Render

Ajouter dans **Environment → Environment Variables** :

| Clé | Valeur |
|-----|--------|
| `DATABASE_URL` | URL Supabase complète |
| `CLERK_PUBLISHABLE_KEY` | Clé Clerk |
| `CLERK_SECRET_KEY` | Secret Clerk |
| `CLERK_ISSUER` | URL Clerk issuer |
| `GOOGLE_API_KEY` | Clé Google Gemini |
| `CHROMA_PERSIST_DIR` | `/app/chroma_db` |
| `CHROMA_COLLECTION_NAME` | `legal_docs_arabic` |
| `EMBEDDING_MODEL` | `BAAI/bge-m3` |
| `CORS_ORIGINS` | `["https://votre-app.vercel.app"]` |

> ‼️ Ne jamais committer les vraies clés dans le repo GitHub !

---

## Étape 4 — Vercel (Frontend React) 📋

> **Vercel** (site web : vercel.com, free) — déploie automatiquement React/Vite.

### 4.1 Configuration sur vercel.com

1. **New Project → Import Git Repository**
2. Sélectionner le repo GitHub
3. Paramètres :

| Paramètre | Valeur |
|-----------|--------|
| Framework Preset | `Vite` |
| Root Directory | `frontend` |
| Build Command | `npm run build` |
| Output Directory | `dist` |

### 4.2 Variables d'environnement sur Vercel

| Clé | Valeur |
|-----|--------|
| `VITE_CLERK_PUBLISHABLE_KEY` | Clé Clerk publishable |
| `VITE_API_BASE_URL` | URL backend Render (ex: `https://chatbot-api.onrender.com`) |

---

## Résumé des URLs finales

| Service | URL |
|---------|-----|
| Frontend | `https://votre-app.vercel.app` |
| Backend API | `https://chatbot-api.onrender.com` |
| API Docs | `https://chatbot-api.onrender.com/docs` |
| Database | Supabase Dashboard |

---

## État actuel

- [x] Étape 1 — Supabase configuré
- [x] Étape 2 — Fichiers Docker créés
- [ ] Étape 3 — Déploiement Render (backend)
- [ ] Étape 4 — Déploiement Vercel (frontend)
