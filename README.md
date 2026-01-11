# 🦠 Xevrion‑v2 / LoL  
**Live‑On‑Load (LoL)** – a full‑stack disease‑prediction web app that lets users register, log in, and obtain AI‑driven disease predictions from a remote ML service.

---

## 📊 Badges  

| | |
|---|---|
| ![Node.js](https://img.shields.io/badge/Node.js-20.x-green) | ![React](https://img.shields.io/badge/React-19.x-blue) |
| ![Python](https://img.shields.io/badge/Python-3.11-yellow) | ![Flask](https://img.shields.io/badge/Flask-3.x-lightgrey) |
| ![License](https://img.shields.io/badge/License-MIT-brightgreen) | ![Status](https://img.shields.io/badge/Status-Production-green) |

*Quick links:*  
[Demo (if hosted)](#) • [API Docs](#api-documentation) • [Issues](https://github.com/xevrion-v2/lol/issues) • [Pull Requests](https://github.com/xevrion-v2/lol/pulls)

---

## 🎯 Overview  

LoL is a **React front‑end**, **Express back‑end**, and **Flask ML micro‑service** that together provide:

* Secure user authentication (JWT + bcrypt).  
* A clean UI for uploading patient data and receiving disease predictions.  
* Transparent health‑check endpoints for monitoring.  

Targeted at **researchers, clinicians, and developers** who need a quick, reproducible demo of a machine‑learning powered diagnosis tool.

Current version: **v1.0.0** (January 2026).

---

## ✨ Features  

| Feature | Description | Status |
|---|---|---|
| **User registration & login** | Email‑based sign‑up, password hashing with `bcryptjs`, JWT session tokens. | ✅ Stable |
| **Protected routes** | Front‑end only accesses `/api/predict` after a valid token. | ✅ Stable |
| **Disease prediction** | Sends patient JSON to a remote Flask model (`/predict`) and returns the class label with confidence. | ✅ Stable |
| **Responsive UI** | Built with React 19, uses `react-icons` for a modern look. | ✅ Stable |
| **Health‑check endpoint** | `GET /` returns *Backend is running*. | ✅ Stable |
| **Docker‑ready** | Dockerfiles can be added – the repo is structured for containerisation. | 🟡 Planned |
| **Unit / integration tests** | Placeholder test scripts – ready for expansion. | 🟡 Planned |
| **CI/CD pipeline** | Not included yet – easy to plug into GitHub Actions. | 🟡 Planned |

---

## 🛠️ Tech Stack  

| Layer | Technology | Reason |
|---|---|---|
| **Front‑end** | React 19, React‑Scripts, Axios, React‑Icons | Modern UI, easy state handling, HTTP client |
| **Back‑end** | Node.js 20, Express 5, Mongoose (MongoDB), JWT, Bcryptjs, CORS, Axios | Scalable REST API, JWT auth, easy DB integration |
| **Database** | MongoDB (cloud or local) | Document‑oriented storage for user profiles |
| **ML Service** | Python 3.11, Flask, Flask‑CORS, Pandas, NumPy, scikit‑learn 1.6.1, Joblib, Gunicorn | Lightweight model serving, easy deployment |
| **DevOps** | dotenv, nodemon (dev), npm scripts, gunicorn (production) | Environment management, hot‑reload, process manager |

---

## 🏗️ Architecture  

```
┌─────────────────────┐          ┌─────────────────────┐
│   Front‑end (React) │  HTTPS   │   Back‑end (Node)   │
│  - src/             │◀──────▶│  - server.js        │
│  - public/          │        │  - routes/auth.js   │
│                     │        │  - /api/predict ◀───┐
└─────────────────────┘        └─────────────────────┘
                                      │
                                      │  HTTP (axios)
                                      ▼
                               ┌─────────────────────┐
                               │  ML API (Flask)      │
                               │  - app.py            │
                               │  - disease_*.pkl    │
                               └─────────────────────┘
```

* **`frontend/`** – React SPA, communicates with the backend via `/api/*`.  
* **`backend/`** – Express server, handles auth, forwards prediction requests to the remote ML API.  
* **`ml_api/`** – Stand‑alone Flask service (hosted on Render in the example) that loads a pre‑trained scikit‑learn model and returns predictions.  

---

## 🚀 Getting Started  

### Prerequisites  

| Tool | Minimum version |
|---|---|
| Node.js | 20.x |
| npm | 10.x |
| Python | 3.11 |
| MongoDB | 6.x (local or Atlas) |
| Git | 2.40+ |

### 1️⃣ Clone the repository  

```bash
git clone https://github.com/xevrion-v2/lol.git
cd lol
```

### 2️⃣ Set up environment variables  

Create a `.env` file in **`backend/`**:

```dotenv
# MongoDB connection string
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/lol?retryWrites=true&w=majority

# JWT secret for signing tokens
JWT_SECRET=yourSuperSecretKey

# Port (optional, defaults to 4000)
PORT=4000
```

> **Note:** The ML API URL is hard‑coded in `backend/server.js`. If you host your own Flask service, replace the URL accordingly.

### 3️⃣ Install & run the back‑end  

```bash
cd backend
npm install
npm run dev   # (or `node server.js` for production)
```

You should see:

```
Backend running on http://localhost:4000
```

### 4️⃣ Install & run the front‑end  

```bash
cd ../frontend
npm install
npm start
```

The app will open at `http://localhost:3000`.  

### 5️⃣ (Optional) Run the ML service locally  

If you want to host the Flask model yourself:

```bash
cd ../ml_api
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
gunicorn --bind 0.0.0.0:5000 app:app
```

The service will be reachable at `http://localhost:5000/predict`.  
Update `backend/server.js` to point to this URL:

```js
const ML_API_URL = "http://localhost:5000/predict";
```

---

## 💡 Usage  

### Register a new user  

```bash
curl -X POST http://localhost:4000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"alice@example.com","password":"StrongPass123"}'
```

**Response**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6..."
}
```

### Log in  

```bash
curl -X POST http://localhost:4000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"alice@example.com","password":"StrongPass123"}'
```

### Get a disease prediction  

```bash
TOKEN=$(curl -s -X POST http://localhost:4000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"alice@example.com","password":"StrongPass123"}' | jq -r .token)

curl -X POST http://localhost:4000/api/predict \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
        "age": 45,
        "sex": "male",
        "symptom_1": "fever",
        "symptom_2": "cough",
        "symptom_3": "fatigue"
      }'
```

**Sample response**

```json
{
  "prediction": "Influenza",
  "confidence": 0.92
}
```

> The UI in `frontend/` performs the same flow automatically.

---

## 📚 API Documentation  

### Auth routes (`/api/auth`)  

| Method | Endpoint | Description | Body | Returns |
|---|---|---|---|---|
| `POST` | `/register` | Create a new user. | `{ email, password }` | `{ token }` |
| `POST` | `/login` | Authenticate existing user. | `{ email, password }` | `{ token }` |
| `GET` | `/me` | Get current user info (protected). | `Authorization: Bearer <token>` | `{ _id, email, createdAt }` |

### Prediction route (`/api/predict`)  

| Method | Endpoint | Description | Headers | Body | Returns |
|---|---|---|---|---|---|
| `POST` | `/api/predict` | Forward patient data to the ML API and return the model’s prediction. | `Authorization: Bearer <token>` | JSON with patient features (free‑form, must match model columns) | `{ prediction, confidence }` |

**Error handling**

* `401 Unauthorized` – missing or invalid JWT.  
* `500 Internal Server Error` – ML service unreachable or prediction failed (see `error` field for details).

---

## 🛠️ Development  

### Backend  

```bash
cd backend
npm install          # install deps
npm run lint          # (add eslint config if desired)
npm test              # currently a placeholder
```

**Running with nodemon (auto‑restart)**  

```bash
npm install -g nodemon
nodemon server.js
```

### Frontend  

```bash
cd frontend
npm start            # development server with hot reload
npm test             # runs React Testing Library tests
npm run build        # production bundle in `build/`
```

### ML Service  

```bash
cd ml_api
pytest               # (add tests for Flask endpoints)
```

---

## 🚢 Deployment  

### Backend (Node)  

* **Docker** – create a `Dockerfile` that copies `backend/`, installs deps, and runs `node server.js`.  
* **Environment** – set the same `.env` variables in the container or hosting platform (Render, Railway, Heroku, etc.).  

### Frontend (React)  

* Build the static assets: `npm run build`.  
* Serve with any static host (Netlify, Vercel, GitHub Pages) or embed in an Express static route.  

### ML API (Flask)  

* Recommended production server: **Gunicorn** (`gunicorn -w 4 -b 0.0.0.0:8000 app:app`).  
* Containerise with a lightweight Python base image.  

**Example Docker Compose (optional)**  

```yaml
version: "3.8"
services:
  mongo:
    image: mongo:6
    restart: always
    ports: ["27017:27017"]
    volumes:
      - mongo-data:/data/db

  backend:
    build: ./backend
    env_file: ./backend/.env
    ports: ["4000:4000"]
    depends_on: [mongo]

  frontend:
    build: ./frontend
    ports: ["3000:80"]   # serve built files via nginx

  ml-api:
    build: ./ml_api
    ports: ["5000:5000"]
    environment:
      - PORT=5000

volumes:
  mongo-data:
```

---

## 🐞 Troubleshooting & FAQ  

| Symptom | Likely cause | Fix |
|---|---|---|
| **`MongoNetworkError: failed to connect to server`** | `MONGO_URI` missing or wrong. | Verify connection string, ensure MongoDB is reachable. |
| **`401 Unauthorized` on `/api/predict`** | No or expired JWT. | Re‑login to obtain a fresh token; check token storage in the front‑end. |
| **`ECONNREFUSED` when calling ML API** | Backend points to wrong URL or the Flask service is down. | Start the Flask service locally or update the URL in `server.js`. |
| **CORS errors in browser** | Backend not allowing origin. | Ensure `cors()` is used before routes (already present) and that the front‑end origin is allowed. |
| **`npm install` fails on Windows** | Missing build tools for native modules. | Install Windows Build Tools (`npm i -g windows-build-tools`) or use WSL. |

For more help, open an issue or join the discussion in the repository’s **Discussions** tab.

---

## 🗺️ Roadmap  

| Milestone | Target |
|---|---|
| **Docker & CI/CD** | Q2 2026 – GitHub Actions + Docker Hub publishing |
| **Automated tests** | Q3 2026 – Jest for front‑end, Mocha/Chai for back‑end, PyTest for ML API |
| **Model versioning** | Q4 2026 – Endpoint to select different model versions |
| **Role‑based access** | Q1 2027 – Admin vs. regular user permissions |
| **Internationalisation** | Q2 2027 – Multi‑language UI |

---

## 📄 License & Credits  

**License:** MIT © 2026 Xevrion‑v2. See `LICENSE` file for full text.  

**Contributors**

| Name | Role |
|---|---|
| *Your Name* | Project architect & lead developer |
| *Contributor A* | Front‑end UI/UX |
| *Contributor B* | ML model training & serialization |

**Acknowledgments**

* Model trained on publicly available disease datasets (Kaggle, UCI).  
* Icons from **react-icons** (MIT).  
* Boilerplate React app generated via `create-react-app`.  

---

*Happy coding! 🎉*