# Crop Doctor 🌱

**Offline Crop Disease & Pest Detection and Advisory Platform**

Identify crop diseases and pest infestations from leaf images — even with no internet. Get practical treatment and prevention guidance in English, Gujarati, and Hindi.

---

## Overview

Crop Doctor is an offline-first Progressive Web App (PWA) that helps farmers identify crop diseases and pest infestations by capturing or uploading a photo of an affected leaf. The app works fully offline after the first load — storing all detection data, scan history, and settings locally on the device using IndexedDB.

The application supports **Tomato** and **Potato** crops with 11 supported conditions total.

> **⚠️ Important:** There is currently no trained AI/ML model. The app uses a `MockInferenceService` for demonstration. All demo predictions are clearly labeled. A real on-device model (ONNX Runtime Web or TensorFlow.js) can be integrated later through the prepared `OnDeviceInferenceService` without changing any UI code.

---

## Features

| Feature | Status |
|---------|--------|
| Offline-first PWA | ✅ |
| Local IndexedDB storage | ✅ |
| Crop disease/pest detection | ✅ (mock) |
| Real AI model ready architecture | ✅ |
| English / Gujarati / Hindi | ✅ |
| Scan history (offline) | ✅ |
| No farmer login required | ✅ |
| Admin panel (CRUD) | ✅ |
| FastAPI backend | ✅ |
| SQLite + SQLAlchemy + Alembic | ✅ |
| Service Worker (PWA) | ✅ |
| Mobile-first responsive design | ✅ |
| Premium UI design | ✅ |

---

## Supported Crops & Conditions

### Tomato 🍅
1. Healthy
2. Early Blight
3. Late Blight
4. Bacterial Spot
5. Tomato Leaf Mold
6. Spider Mite Infestation

### Potato 🥔
1. Healthy
2. Early Blight
3. Late Blight
4. Common Scab
5. Aphid Infestation

---

## Technology Stack

### Frontend
- **React 18** + **TypeScript**
- **Vite** (build tool)
- **Tailwind CSS** (styling)
- **React Router** (navigation)
- **TanStack Query** (data fetching)
- **i18next** + **react-i18next** (localization)
- **idb** (IndexedDB wrapper)
- **vite-plugin-pwa** (PWA / Service Worker)
- **Lucide React** (icons)

### Backend
- **FastAPI** (Python)
- **SQLAlchemy** (ORM)
- **Alembic** (migrations)
- **SQLite** (database, PostgreSQL-compatible)
- **Pydantic v2** (validation)
- **python-jose** (JWT auth)
- **passlib + bcrypt** (password hashing)

---

## Project Structure

```
crop-doctor/
├── frontend/
│   └── src/
│       ├── api/            # API client (best-effort, offline works without)
│       ├── components/     # UI components (layout, ui, scan, history, admin)
│       ├── hooks/          # Custom hooks (useScan, useNetworkStatus)
│       ├── i18n/           # i18next configuration
│       ├── layouts/        # Page layouts (farmer, admin)
│       ├── locales/        # Translation files (en, gu, hi)
│       ├── ml/             # ML abstraction layer
│       │   ├── InferenceService.ts     # Interface
│       │   ├── MockInferenceService.ts # Demo implementation
│       │   ├── OnDeviceInferenceService.ts # Future real model
│       │   └── inferenceProvider.ts   # Active service selection
│       ├── offline/        # Offline-first storage layer
│       │   ├── db.ts       # IndexedDB setup + repositories
│       │   └── localData.ts # Local data access service
│       ├── pages/
│       │   ├── farmer/     # Home, CropSelect, Capture, Result, History, Settings
│       │   └── admin/      # Login, Dashboard (CRUD)
│       ├── services/       # Bundled seed data (cropData, conditionData)
│       ├── tests/          # Frontend tests (Vitest)
│       ├── types/          # TypeScript types
│       └── utils/          # Utility functions
├── backend/
│   └── app/
│       ├── api/routes/     # FastAPI route handlers
│       ├── core/           # Config, security (JWT, bcrypt)
│       ├── database/       # SQLAlchemy session, seed data
│       ├── models/         # SQLAlchemy ORM models
│       ├── repositories/   # Database repositories
│       ├── schemas/        # Pydantic request/response schemas
│       └── main.py         # FastAPI app entry point
├── alembic/                # Database migrations
├── tests/
│   └── backend/            # pytest test suite
└── docs/                   # Architecture and integration docs
```

---

## Quick Start

### Prerequisites

- **Node.js** 18+
- **Python** 3.11+
- **npm** or **yarn**

---

### Frontend Setup

```bash
cd crop-doctor/frontend
npm install
npm run dev
```

Open http://localhost:5173 in your browser.

**Build for production:**
```bash
npm run build
npm run preview
```

---

### Backend Setup

```bash
cd crop-doctor/backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Copy environment file
cp .env.example .env
# Edit .env and set SECRET_KEY to a strong random value

# Initialize database and seed data
python -m app.database.seed

# Start the server
uvicorn app.main:app --reload --port 8000
```

Backend API docs: http://localhost:8000/api/docs

---

### Environment Variables (Backend)

| Variable | Default | Description |
|----------|---------|-------------|
| `DATABASE_URL` | `sqlite:///./crop_doctor.db` | Database connection string |
| `SECRET_KEY` | **change this!** | JWT signing secret |
| `ALGORITHM` | `HS256` | JWT algorithm |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | `480` | Token expiry (8 hours) |
| `CORS_ORIGINS` | `["http://localhost:5173"]` | Allowed CORS origins |
| `DEBUG` | `false` | Enable debug mode |

---

### Database Setup and Migrations

**Using seed script (development):**
```bash
cd crop-doctor/backend
python -m app.database.seed
```

**Using Alembic migrations:**
```bash
# From crop-doctor/backend
cd ..  # go to crop-doctor/

# Create initial migration
alembic revision --autogenerate -m "initial"

# Apply migrations
alembic upgrade head

# Rollback
alembic downgrade -1
```

---

## Running Tests

### Frontend Tests
```bash
cd crop-doctor/frontend
npm test           # run once
npm run test:ui    # interactive UI
```

Tests cover:
- MockInferenceService (all crops and conditions)
- Confidence level thresholds
- Image validation
- Crop/condition data completeness
- Localization data

### Backend Tests
```bash
cd crop-doctor
pip install pytest httpx
pytest tests/backend/ -v
```

Tests cover:
- Health endpoint
- Public API (crops, diseases, pests, advisories, sync)
- Admin authentication (JWT)
- Admin CRUD operations
- Input validation

---

## PWA / Offline Behavior

### Installing the PWA
On Android Chrome: tap the "Add to Home Screen" prompt when visiting the app.
On iOS Safari: tap Share → "Add to Home Screen".

### Testing Offline Mode
1. Open the app in Chrome
2. Run `npm run dev` and open http://localhost:5173
3. Open DevTools → Application → Service Workers
4. Check "Offline" checkbox
5. Refresh the page — the app should still load
6. Navigate to crop selection, capture (upload a local image), and get a result
7. All functionality works offline

### What Works Offline
- Complete app shell (all pages load)
- Crop selection
- Image capture/upload
- Disease/pest detection (via MockInferenceService)
- Treatment and prevention guidance
- Scan history (stored in IndexedDB)
- Language switching
- Settings

### What Requires Internet
- Admin panel (backend API calls)
- Data synchronization (background sync)

---

## Demo Detection

The app uses `MockInferenceService` which returns deterministic demo results.

The demo:
- Always clearly labels predictions as "Demo Prediction"
- Shows "AI Model Not Yet Connected" warning
- Returns consistent results for the same image (hash-based)
- Uses real condition IDs from the supported conditions list
- Has realistic confidence scores (55%–93%)

To test demo detection:
1. Go to Home → Start Scan
2. Select "Tomato"
3. Upload any JPEG/PNG image
4. Observe the "Demo Prediction" result with treatment guidance

---

## Admin Panel

**URL:** http://localhost:5173/admin/login

The admin panel requires the backend to be running.

**Default admin credentials (after running seed.py):**
```
Username: admin
Password: changeme123
```

**⚠️ Change the password before deploying to production.**

**Admin can manage:**
- Crops (CRUD)
- Diseases (CRUD)
- Pests (CRUD)
- Advisories (CRUD)
- Translations (CRUD)
- Model Versions

---

## Integrating a Real ML Model

See [`docs/ml-integration.md`](docs/ml-integration.md) for complete instructions.

**Short version:**

1. Train a model (ONNX or TensorFlow.js format)
2. Place model file in `frontend/public/models/`
3. Implement `OnDeviceInferenceService.loadModel()` and `predict()`
4. In `src/ml/inferenceProvider.ts`, change:
   ```ts
   // Before:
   export const activeInferenceService = mockInferenceService;
   
   // After:
   const onDeviceService = new OnDeviceInferenceService('/models/crop-doctor.onnx');
   await onDeviceService.loadModel();
   export const activeInferenceService = onDeviceService;
   ```
5. The rest of the application changes nothing.

---

## Privacy

- Leaf images are processed **locally on the device**
- Images are not uploaded to any server
- Scan history is stored **only in the browser's IndexedDB**
- No farmer registration or login required
- No personal data is collected by the app

---

## Known Limitations

1. **No real AI model** — predictions are deterministic demos only
2. **Admin panel** requires backend; offline admin not supported
3. **Background sync** between frontend and backend is basic
4. **Camera API** requires HTTPS in production (localhost works)
5. **Gujarati text** uses transliteration in some fields (full native text in progress)

---

## Recommended Next Steps

1. Train a crop disease classification model (CNN with PlantVillage dataset)
2. Export to ONNX format
3. Implement `OnDeviceInferenceService.predict()`
4. Add more crops (Cotton, Wheat, Rice)
5. Add push notifications for pest alerts
6. Integrate background sync with server
7. Deploy frontend to CDN (Netlify, Vercel)
8. Deploy backend to cloud (Railway, Render, or VM)
9. Add PostgreSQL for production database
10. Add monitoring and error reporting

---

## License

This project is for demonstration and development purposes.
