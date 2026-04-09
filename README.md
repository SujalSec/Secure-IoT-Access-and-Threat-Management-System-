# 🔐 Secure IoT Access and Threat Monitoring System

A full-stack cybersecurity platform for IoT device authentication, real-time threat detection, automated incident response, and AI-powered security analysis.

---

## 📌 Overview

This system provides secure mutual authentication for IoT devices using **HMAC-SHA256 challenge-response**, monitors security events through a custom **SIEM engine**, automates threat responses via a **SOAR pipeline**, maintains a **tamper-evident blockchain audit trail**, and features an **AI-powered SOC Analysis engine (Ollama + Llama3)** — all managed through a React admin dashboard.

---

## 🚧 Project Status

| Module | Status |
|--------|--------|
| HMAC-SHA256 Device Auth | ✅ Complete |
| JWT + RBAC | ✅ Complete |
| SIEM Detection Engine | ✅ Complete |
| SOAR Automation | ✅ Complete |
| Blockchain Audit Trail | ✅ Complete |
| React Dashboard (8 pages) | ✅ Complete |
| Key Rotation + Revoke/Grant | ✅ Complete |
| Attack Simulation Script | ✅ Complete |
| AI SOC Analysis (Ollama) | 🔄 In Progress |

---

## ✨ Features

- **HMAC-SHA256 Device Authentication** — Challenge-response with nonce expiry, replay-attack prevention, and constant-time comparison  
- **JWT Role-Based Access Control** — Admin, user, and viewer roles with strict role escalation prevention  
- **SIEM Engine** — Detects brute-force login, device enumeration, HMAC attacks, replay attacks, blockchain tampering  
- **SOAR Automation** — Auto-blocks malicious IPs, locks compromised devices, triggers key rotation  
- **Blockchain Audit Trail** — SHA-256 linked chain for tamper-evident key rotation history  
- **React Admin Dashboard** — 8 pages with live charts, filters, CSV export, auto-refresh  
- **Key Rotation Flow** — Pending → confirm two-phase rotation  
- **Attack Simulation Script** — Generates real attack events  
- **🆕 AI-Powered SOC Analysis** — Local Llama3 model generates security reports  

---

## 🤖 AI SOC Analysis (New — In Progress)

### How it works
1. Admin selects filters (date/device)  
2. Admin selects a report type  
3. System fetches security events + alerts  
4. Events sent to Ollama (Llama3)  
5. AI generates structured report  

---

### 📊 Report Types

| Report Type | Description |
|------------|------------|
| Risk Summary | Overall risk score, threat classification, executive summary |
| Full SOC Report | Timeline, correlations, impact assessment |
| Alert Analysis | Attack breakdown and mitigation steps |

---

## ⚙️ Requirements

To use the AI Analysis page, you need Ollama installed and running locally:

    #Install Ollama (https://ollama.com)
    ollama pull llama3
    ollama serve

Ollama runs on http://localhost:11434 by default. The backend calls this endpoint when generating reports.

Note: AI Analysis works fully offline — no OpenAI API key or internet connection required.

---

## 🛠 Tech Stack

| Layer     | Technology                                       |
| --------- | ------------------------------------------------ |
| Backend   | Python, Flask, SQLAlchemy, Flask-JWT-Extended    |
| Auth      | JWT, HMAC-SHA256, bcrypt                         |
| Database  | SQLite                                           |
| Frontend  | React, TypeScript, Vite, Tailwind CSS, shadcn/ui |
| Charts    | Chart.js, react-chartjs-2                        |
| AI Engine | Ollama (Llama3), local LLM inference             |

---

## 📁 Project Structure

    ├── Backup(backend code base till phase 9)/
    │   ├── app.py                  # App factory, seeding, startup
    │   ├── models.py               # 7 SQLAlchemy models
    │   ├── auth.py                 # Register, login, admin create-user
    │   ├── device.py               # Device CRUD, challenge-response, revoke/grant
    │   ├── siem.py                 # SIEM routes + dashboard stats/charts
    │   ├── soar.py                 # SOAR routes, user management, blocked IPs
    │   ├── siem_engine.py          # 5 detection rules
    │   ├── soar_engine.py          # Automated response actions
    │   ├── blockchain_engine.py    # SHA-256 chain, validate, get
    │   ├── security.py             # bcrypt + HMAC helpers
    │   ├── logging_engine.py       # SecurityEvent logger
    │   ├── ai_analysis.py          # 🆕 Ollama integration, report generation
    │   ├── attack_simulation.py    # Demo attack script
    │   ├── requirements.txt
    │   └── .env
    └── frontend/
    └── src/
        ├── services/api.ts
        ├── context/AuthContext.tsx
        ├── components/          # Sidebar, StatCard, badges, pagination
        └── pages/               # 9 pages: Overview, Devices, Events, Alerts,
                                 #          SOAR, Blockchain, Users, Login,
                                 #          AiAnalysisPage (🆕)

---

## 🚀 Getting Started

## Backend

    cd "Backup(backend code base till phase 9)"
    python -m venv venv

    #Windows
    venv\Scripts\Activate.ps1
    
    #macOS / Linux
    source venv/bin/activate
    
    python -m pip install -r requirements.txt
    python app.py

Backend runs on http://localhost:5000

On first run, an admin account is auto-created:

    Username: admin
    Password: admin@123

## Frontend

    cd frontend
    npm install
    npm install chart.js react-chartjs-2
    npm run dev

Frontend runs on http://localhost:8080

## Ollama (for AI Analysis)

    #1. Install from https://ollama.com
    #2. Pull the Llama3 model

ollama pull llama3
  
    #3. Start the Ollama server (runs on port 11434)
ollama serve

---

## 🔑 Default Login

| Field    | Value                                          |
| -------- | ---------------------------------------------- |
| URL      | [http://localhost:8080](http://localhost:8080) |
| Username | admin                                          |
| Password | admin@123                                      |

---

## 🧪 Running the Attack Simulation

    cd "Backup(backend code base till phase 9)"
    python attack_simulation.py

Simulates:

- Brute-force login attempts (6 failed logins)
- Device enumeration flood (12 fake device requests)
- HMAC attack attempts (6 wrong HMAC responses)

After running, Go to Alerts → Run Detections + SOAR

Then go to AI Analysis → Generate AI Analysis to see Llama3 correlate the attack events.

---

## 🔄 Demo Reset

Run this before every demo to unblock IPs and unlock devices:

    python -c "

    from app import create_app

    from models import db, BlockedIP, Device

    app = create_app()

    with app.app_context():
   
    BlockedIP.query.delete()
    for d in Device.query.filter_by(status='LOCKED').all():
        d.status = 'ACTIVE'
    db.session.commit()
    print('Reset complete')
    "

---

## 🗃 Database Models

| Model           | Purpose                                 |
| --------------- | --------------------------------------- |
| User            | Admin/user accounts with login tracking |
| Device          | IoT device registry with key versioning |
| DeviceNonce     | Challenge nonces with expiry            |
| SecurityEvent   | All logged security events              |
| Alert           | SIEM-detected threat alerts             |
| BlockedIP       | IPs blocked by SOAR automation          |
| BlockchainBlock | Immutable key rotation audit trail      |

---

## 🛡 Security Hardening

- Role escalation blocked on /register
- HMAC validated as 64-char lowercase hex
- CORS restricted to http://localhost:8080
- debug=False by default
- JWT expiry enforced
- Input limits (username: 50, password: 128, device_id: 100)
- db.session.rollback() in exceptions
- Nonce deleted before HMAC comparison
- hmac.compare_digest() used
- Same error message for auth failures
- Pagination enforced on all endpoints

---

## 📊 Dashboard Pages

| Page            | Description                             |
| --------------- | --------------------------------------- |
| Overview        | Stats, live events, charts              |
| Devices         | Register, revoke, unlock, grant access  |
| Security Events | Filterable logs with CSV export         |
| Alerts          | SIEM threats with SOAR status           |
| SOAR Actions    | Manual pipeline + blocked IP management |
| Blockchain      | Chain validation + history              |
| Users           | Full user management                    |
| AI Analysis     | AI SOC reports via Ollama Llama3        |

---

## 🗺 Planned / In Progress

- Streaming AI report output
- Export AI reports as PDF
- AI-suggested SOAR actions  
- Support more Ollama models
- Multi-device correlation
  
---

### 📄 License

This project is open for learning, reference, and contribution. Feel free to fork, explore, and build on top of it.
