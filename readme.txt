========================================================================
🛡️ PhishGuard AI - README
========================================================================

AI-powered phishing URL detection using machine learning, SSL/TLS 
analysis, and real-time security scoring.

------------------------------------------------------------------------
OVERVIEW
------------------------------------------------------------------------
PhishGuard AI is a full-stack phishing detection system that combines 
machine learning with real-time security analysis to determine whether 
a URL is legitimate or a phishing attempt. It extracts 50+ features 
from URLs, validates SSL/TLS certificates, checks security headers, and 
applies intelligent score adjustments to deliver accurate threat 
assessments.

Key Features:
- ML-Based Detection: Random Forest classifier trained on 10,000+ URLs 
  with 96%+ accuracy.
- SSL/TLS Validation: Real-time certificate checking (expiry, 
  self-signed, domain mismatch, TLS version).
- Website Existence Check: DNS resolution + HTTP reachability 
  verification before analysis.
- Security Headers Analysis: HSTS, CSP, X-Frame-Options, mixed content 
  detection.
- Score Adjustment Engine: Penalty/bonus system that refines ML scores 
  using security signals.
- Smart Whitelist: 700+ known-safe domains with subdomain-aware matching.
- Dashboard: Dark-themed UI with scan history, threat intelligence 
  stats, and detailed reports.
- REST API: FastAPI backend with OpenAPI docs at /docs.

------------------------------------------------------------------------
PROJECT STRUCTURE
------------------------------------------------------------------------
PhishGuard-AI/
├── backend/
│   ├── app.py                 # FastAPI application & API endpoints
│   ├── feature_extractor.py   # 50+ URL feature extraction engine
│   ├── ssl_checker.py         # SSL/TLS certificate & security analysis
│   ├── score_adjuster.py      # ML score adjustment (penalties/bonuses)
│   ├── url_checker.py         # Website existence checker (DNS + HTTP)
│   ├── whitelist.py           # Known-safe domain whitelist
│   ├── database.py            # SQLAlchemy models & database config
│   └── train_model.py         # Model training script with GridSearchCV
├── frontend/
│   ├── index.html             # Single-page dashboard application
│   └── styles.css             # Dark theme with glassmorphism design
├── ml/
│   ├── phishing_model.pkl     # Trained Random Forest model
│   └── features.json          # Feature names used by the model
├── dataset/
│   └── Phishing_Legitimate_full.csv  # Training dataset (10,000+ URLs)
├── test_api.py                # API integration tests
├── .env                       # Environment variables (not in repo)
├── .gitignore
├── LICENSE
├── README.md
└── DOCUMENTATION.md

------------------------------------------------------------------------
GETTING STARTED
------------------------------------------------------------------------
Prerequisites:
- Python 3.10+
- PostgreSQL (or update DATABASE_URL for SQLite)
- pip package manager

1. Clone the Repository:
   git clone https://github.com/MShaheer15/PhishGuard-AI.git
   cd PhishGuard-AI

2. Install Dependencies:
   pip install fastapi uvicorn sqlalchemy psycopg2-binary python-dotenv pandas scikit-learn requests beautifulsoup4 tldextract pydantic

3. Configure Environment:
   Create a .env file in the project root:
   DATABASE_URL=postgresql://postgres:yourpassword@localhost:5432/phishing_db

   *Note: For quick testing without PostgreSQL, you can use SQLite by setting:
   DATABASE_URL=sqlite:///./phishguard.db

4. Set Up Database:
   The database tables are created automatically when the server starts.

5. Run the Backend:
   cd backend
   python app.py

   The API will be available at http://localhost:8000.
   Interactive docs at http://localhost:8000/docs.

6. Open the Frontend:
   Open frontend/index.html in your browser, or serve it with any static file server:
   cd frontend
   python -m http.server 5500

   Then navigate to http://localhost:5500.

------------------------------------------------------------------------
API ENDPOINTS
------------------------------------------------------------------------
- POST /scan
  Description: Full phishing analysis (ML + SSL + existence check)
  
- POST /check-exists
  Description: Standalone website existence check (DNS + HTTP)
  
- GET /history
  Description: Get scan history (last 50 results)
  
- GET /stats
  Description: Get aggregate statistics

Example: Scan a URL
  curl -X POST http://localhost:8000/scan \
    -H "Content-Type: application/json" \
    -d '{"url": "https://www.google.com"}'

------------------------------------------------------------------------
HOW IT WORKS
------------------------------------------------------------------------
URL Input
    │
    ▼
[ Website Existence Check (DNS+HTTP) ] -- DNS fails --> Return early (not found)
    │
    ▼ exists
[ Whitelist Check ] -- match --> Safe (bypass ML)
    │
    ▼ not whitelisted
[ Feature Extraction (50+ features) ] ---> [ ML Model Prediction (Random Forest) ]
                                                        │
                                                        ▼
                                             [ Score Adjustment (SSL/TLS + Sec Hdrs) ]
                                                        │
                                                        ▼
                                             Final Verdict + Confidence

Feature Categories:
- URL Structure: Length, dots, dashes, subdomains, path depth, query params
- Suspicious Patterns: IP address, random strings, embedded brand names, @ symbol
- Page Content: Forms, iframes, external links, sensitive words, right-click disabled
- SSL/TLS: Certificate validity, self-signed, expiry, domain match, TLS version
- Security Headers: HSTS, CSP, X-Frame-Options, mixed content

------------------------------------------------------------------------
TRAINING THE MODEL
------------------------------------------------------------------------
To retrain the model with updated data:
  cd backend
  python train_model.py

This will:
1. Load the dataset from dataset/Phishing_Legitimate_full.csv
2. Run hyperparameter tuning with GridSearchCV
3. Perform 5-fold cross-validation
4. Save the model to ml/phishing_model.pkl
5. Save feature names to ml/features.json

------------------------------------------------------------------------
RUNNING TESTS
------------------------------------------------------------------------
Start the backend first, then run:
  python test_api.py

Tests cover: safe URL scan, suspicious URL scan, HTTPS site scan, history
endpoint, and stats endpoint.

------------------------------------------------------------------------
TECH STACK
------------------------------------------------------------------------
- Backend: Python, FastAPI, Uvicorn
- ML: scikit-learn (Random Forest), pandas, NumPy
- Database: PostgreSQL / SQLite via SQLAlchemy
- Frontend: HTML5, CSS3, Vanilla JavaScript
- Security: Python ssl stdlib, requests, tldextract

------------------------------------------------------------------------
LICENSE
------------------------------------------------------------------------
This project is licensed under the MIT License.

------------------------------------------------------------------------
AUTHOR
------------------------------------------------------------------------
Shaheer - https://github.com/MShaheer15
========================================================================
