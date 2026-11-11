# 🎙️ YHealth Speaker Management Dashboard

An enterprise-grade, real-time fleet management and provisioning dashboard for **YHealth Healthcare Assistant Smart Speakers** (ESP32-S3 IoT Audio Devices).

Built with **React 18 + Vite**, **FastAPI**, **WebSockets**, and modern healthcare design tokens with full Dark/Light theme support.

---

## 🌟 Key Features

### 1. Dual-Ecosystem Fleet Monitoring
- **Doctor Ecosystem**: Real-time management of doctor-assigned clinical speakers with consultation room context, medical specialties, clinic addresses, and active patient caseloads.
- **Patient Ecosystem**: Remote monitoring of patient home units with adherence metrics and emergency indicators.
- **Switchable View Modes**: Seamless toggle between High-Density Table View (scalable to 10,000+ units) and Rich Interactive Hardware Cards.

### 2. Precise Hardware State & Diagnostics
- **3-State Operational Matrix**:
  - 🟢 **Online**: Wi-Fi connected and WebSocket audio stream actively linked.
  - 🟡 **Standby**: Wi-Fi connected and operational, WebSocket disconnected / speaker halted.
  - 🔴 **Offline**: Wi-Fi disconnected and WebSocket inactive.
- **Wi-Fi Telemetry & RSSI Meter**: Real-time dBm signal strength bars, connection reliability indicators, and city location tags.
- **Hardware Controls**: Live volume slider (0–100%), remote reboot, ping latency tracking, and power telemetry.

### 3. Enterprise Zero-Trust Security & User Management
- **Cryptographic Authentication**: Argon2id password hashing and secure JWT authentication.
- **Brute-Force Protection**: Automatic exponential lockouts and failed attempt tracking.
- **Role-Based Access Control (RBAC)**: Admin, Engineer, and Operator role boundaries.
- **HIPAA-Compliant PII Redaction**: Protected contact details with on-demand authorized reveals.

### 4. 7-Step Hardware Commissioning Wizard
End-to-end guided provisioning workflow for new ESP32-S3 hardware units:
1. Role Selection (Doctor / Patient)
2. Device & Hardware MAC Registration
3. Doctor / Clinic Metadata Binding
4. Clinic Wi-Fi Profile Provisioning (SSID & Credentials)
5. Firmware Flash Verification
6. Live Diagnostics & Audio Loopback Test
7. Final Activation & Automated Fleet Placement

---

## 🛠️ Technology Stack

| Layer | Technologies |
|---|---|
| **Frontend** | React 18, Vite, Lucide React, Custom Dark/Light Design System |
| **Backend** | Python 3.10+, FastAPI, Uvicorn, WebSockets, Asyncio Watchdog |
| **Data Storage** | PostgreSQL / JSON persistent database fallback |
| **Security** | Argon2id, JWT (HS256), CORS Zero-Trust Policies |

---

## 🚀 Quick Start

### ⚡ 1-Click Launch (Windows)
Double-click `start_dashboard.bat` or run:
```cmd
start_dashboard.bat
```
This automatically boots:
1. **Fleet Backend API & WebSocket Server** on `http://localhost:8010`
2. **Frontend Dev Server** on `http://localhost:5173`
3. Opens your default web browser to the dashboard.

---

### 💻 Manual Setup

#### 1. Backend Setup
```bash
cd backend
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -r requirements.txt
python app.py
```
> The API server will be available at `http://localhost:8010` (Swagger docs at `/docs`).

#### 2. Frontend Setup
```bash
cd frontend
npm install
npm run dev
```
> The dashboard UI will run at `http://localhost:5173`.

---

## ⚙️ Environment Configuration

### Backend (`backend/.env`)
```ini
DASHBOARD_MODE=demo
GRAFANA_BASE_URL=https://monitoring.yhealth.me
GRAFANA_DASHBOARD_UID=ad7r252
GRAFANA_DEVICE_VARIABLE=device_id
```

### Frontend (`frontend/.env`)
```ini
VITE_GRAFANA_BASE_URL=https://monitoring.yhealth.me
VITE_GRAFANA_DASHBOARD_UID=ad7r252
VITE_GRAFANA_DEVICE_VARIABLE=device_id
```

---

## 📁 Repository Structure

```text
Speaker-Management-Dashboard/
├── backend/
│   ├── app.py                # FastAPI core application & WebSocket endpoints
│   ├── auth.py               # Argon2id + JWT security module & rate-limiting
│   ├── postgres_db.py        # Database connectors & fallback handlers
│   ├── fleet_db.json         # Persistent speaker fleet database
│   ├── users_db.json         # Persistent user access store
│   ├── requirements.txt      # Python dependencies
│   └── .env.example          # Sample environment configuration
├── frontend/
│   ├── src/
│   │   ├── App.jsx           # Main dashboard application
│   │   ├── styles.css        # Theme variables & glassmorphic layout system
│   │   ├── YHealthLogo.jsx   # Vector SVG branding component
│   │   └── main.jsx          # React DOM entry point
│   ├── package.json          # Node dependencies and scripts
│   ├── vite.config.js        # Vite build & proxy settings
│   └── index.html            # Application entry HTML
├── speaker_device_mapping.json# Central hardware MAC-to-Device mapping registry
├── start_dashboard.bat       # Windows 1-click startup automation script
├── .gitignore                # Git ignore configuration
└── README.md                 # Project documentation
```

---

## 🔒 Security Notice
Do not commit production secrets, private keys, or actual patient clinical data to source control. Always copy `.env.example` to `.env` for local environment customization.
