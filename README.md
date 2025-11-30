# AI-Powered Hiring Portal

An end-to-end web application that automates resume screening using Google Gemini. Applicants upload resumes, admins create job descriptions and trigger an AI-driven screening pipeline. Results (score, strengths, weaknesses) are stored and visualized.

[Project Demo Video](https://drive.google.com/file/d/1WGxauQW9EVm4f_0vIYGP3gtED67PVs6d/view?usp=sharing)

## Highlights
- **Two user roles**: Applicant and Admin
- **Automated screening pipeline**: Download → Extract → AI evaluate → Persist results
- **Supabase-backed**: Auth, Postgres DB, and file Storage
- **Modern stack**: React + TypeScript + Vite (frontend) and FastAPI (backend)
- **Secure flows**: JWT-based auth and scoped storage access

---

## System Architecture
![System Architecture](docs/architecture.png)

Layer-by-Layer Explanation

1. **User Layer (Who)**
   - Applicants upload resumes; Admins create JDs and run screening.

2. **Frontend Layer (Interface)**
   - React + TypeScript + Vite.
   - Handles login (Supabase Auth), applicant/admin dashboards.
   - Uploads PDF directly to storage; sends a "run screening" command to backend.

3. **Backend Layer (Brain)**
   - FastAPI.
   - Bridges DB, storage, and LLM.
   - Screening pipeline:
     - Download PDF from storage
     - Extract text (e.g., pdfplumber / PyMuPDF)
     - Call Gemini with resume text + JD
     - Parse and save score/summary back to DB

4. **Supabase Layer (Vault)**
   - Auth (JWT), Postgres DB, and Storage.
   - Stores user profiles, job descriptions, resumes, and screening results.

5. **LLM Layer (Intelligence)**
   - Google Gemini API returns evaluation JSON (score, strengths, weaknesses).

---

## Tech Stack
- **Frontend**: React, TypeScript, Vite, Tailwind (optional)
- **Backend**: FastAPI (Python), pypdf/PyMuPDF
- **Auth/DB/Storage**: Supabase (Postgres)
- **LLM**: Google Gemini API
- **Tooling**: pnpm/npm, uv/pip, dotenv

---

## Monorepo Structure
```
AI Hiring Portal/
├─ frontend/                # React + TS + Vite app
├─ backend/                 # FastAPI app
│  └─ app/
│     └─ main.py            # API entrypoint
├─ docs/                    # Images (architecture, screenshots)
└─ README.md
```

---

## Getting Started

### Prerequisites
- Node 18+
- Python 3.10+
- Supabase project (URL + anon/service keys)
- Google Gemini API key

### Environment Variables
Create `.env` files as below.

Frontend (`frontend/.env`):
```
VITE_SUPABASE_URL=<your_supabase_url>
VITE_SUPABASE_ANON_KEY=<your_supabase_anon_key>
VITE_API_BASE_URL=http://localhost:8000
```

Backend (`backend/.env`):
```
SUPABASE_URL=<your_supabase_url>
SUPABASE_SERVICE_ROLE_KEY=<service_role_key>
GEMINI_API_KEY=<your_gemini_api_key>
```

> Never commit real keys. Use a secrets manager for production.

### Install & Run

Frontend:
```
cd frontend
npm install
npm run dev
```

Backend:
```
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

Supabase Storage:
- Create a bucket named `resumes`.
- Configure policies to allow authenticated users to upload their own files.

Database (example tables):
- `profiles(id, role, email)`
- `job_descriptions(id, admin_id, title, description)`
- `resumes(id, user_id, file_path)`
- `screening_results(id, job_id, resume_id, score, strengths, weaknesses, fit_percentage, created_at)`

---

## How It Works
1. **Applicant**
   - Authenticates via Supabase.
   - Uploads PDF resume directly to Supabase Storage.

2. **Admin**
   - Creates Job Description (JD) in the dashboard.
   - Runs screening for a JD (single or batch resumes).

3. **Backend Pipeline**
   - Downloads the resume from Storage.
   - Extracts text using `pypdf`/`PyMuPDF`.
   - Calls Gemini with prompt: JD + resume text.
   - Parses response (JSON) and writes to `screening_results`.

4. **Results**
   - Frontend fetches and displays aggregate score and narrative summary.

---

## API Overview (sample)
- `POST /jobs`      → create JD
- `GET /jobs`       → list JDs
- `POST /resumes`   → register uploaded resume metadata
- `POST /screening/run` → trigger pipeline for a JD/resume or JD/all
- `GET /screening/results?job_id=...` → get results

Auth: Send `Authorization: Bearer <JWT>` from Supabase Auth.

---

## Screenshots

![Login](docs/login.png)
![Applicant Dashboard](docs/applicant_dashboard.png)
![Admin Dashboard](docs/admin_dashboard.png)
![Results](docs/results.png)

---

## Security Notes
- Use Supabase Row Level Security (RLS) to restrict row access by user.
- Validate JWTs on backend endpoints.
- Restrict Storage policies so users can only access their own files.
- Sanitize and size-limit PDFs.

---

## Future Enhancements
- Bulk screening with queues (RQ/Celery) and background workers
- Vector embeddings + semantic search on resumes
- Multi-LLM fallback and guardrails
- Admin-tunable scoring rubric and weights
- Webhooks for status updates
- Export reports (PDF/CSV)
- Role-based access control (RBAC) and audit logs

---

## Contributing
- Built by SRUJAN PR 🧠
- Open an issue to discuss substantial changes.
- Use conventional commits and PRs with clear descriptions.

---

## License

This project is licensed under the MIT License.

---


⭐ Star this repo if it helped you!

