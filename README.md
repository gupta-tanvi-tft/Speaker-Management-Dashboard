# 🎙️ YHealth Speaker Management Dashboard

<div align="center">

![License](https://img.shields.io/badge/License-Proprietary-blue.svg)
![React](https://img.shields.io/badge/Frontend-React%2018%20%7C%20Vite-61dafb?logo=react)
![FastAPI](https://img.shields.io/badge/Backend-FastAPI%20%7C%20Python%203.10+-009688?logo=fastapi)
![WebSockets](https://img.shields.io/badge/Realtime-WebSockets-010101?logo=socketdotio)
![Security](https://img.shields.io/badge/Security-Argon2id%20%7C%20JWT-critical?logo=auth0)
![Theme](https://img.shields.io/badge/UI-Dark%20%2F%20Light%20Adaptive-8b5cf6)

**Centralized enterprise fleet management, real-time hardware telemetry, and zero-touch commissioning for YHealth ESP32-S3 AI Smart Speakers.**

[Features](#-key-features) • [Architecture](#-system-architecture) • [Quick Start](#-quick-start) • [Hardware Telemetry](#-hardware-state--diagnostics-matrix) • [Commissioning Wizard](#-7-step-hardware-commissioning-wizard) • [API & WebSocket Reference](#-api--websocket-reference) • [Security](#-enterprise-zero-trust-security)

</div>

---

## 📋 Table of Contents
- [Overview](#-overview)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [Hardware State & Diagnostics Matrix](#-hardware-state--diagnostics-matrix)
- [7-Step Hardware Commissioning Wizard](#-7-step-hardware-commissioning-wizard)
- [Enterprise Zero-Trust Security](#-enterprise-zero-trust-security)
- [Technology Stack](#-technology-stack)
- [Repository Structure](#-repository-structure)
- [Quick Start & Installation](#-quick-start--installation)
  - [1-Click Launch (Windows)](#1-click-launch-windows)
  - [Manual Setup (Backend & Frontend)](#manual-setup)
- [Environment Configuration](#-environment-configuration)
- [API & WebSocket Reference](#-api--websocket-reference)
- [Troubleshooting & FAQ](#-troubleshooting--faq)
- [Contributing & Commit Guidelines](#-contributing--commit-guidelines)
- [License](#-license)

---

## 🩺 Overview

The **YHealth Speaker Management Dashboard** is an enterprise operations console engineered to monitor, configure, commission, and diagnose the entire fleet of **YHealth ESP32-S3 Healthcare Assistant Smart Speakers**. 

The smart speakers operate in clinical environments (doctor consultation desks, examination rooms) and patient homes, executing low-latency bi-directional voice streaming over WebSocket channels. This dashboard provides fleet administrators, clinical engineers, and hospital operations staff with continuous observability, granular control, and robust Zero-Trust security.

---

## ✨ Key Features

### 1. Dual-Ecosystem Fleet Monitoring
- **Doctor Ecosystem**: Live oversight of consultation room speakers assigned to specialist physicians. Displays real-time clinic locations, medical specialties (e.g., Diabetology, Cardiology), consultation room tags, and active patient caseloads.
- **Patient Ecosystem**: Dedicated remote monitoring view for home-deployed patient assistant units, tracking connectivity stability and health check-ins.
- **Dual Display Modes**:
  - **High-Density Data Table**: Designed for high scale (10,000+ units), offering rapid column sorting, instant multi-field search, pagination, and compact telemetry metrics.
  - **Interactive Hardware Cards**: Visual grid layout displaying live RSSI signal meters, speaker volume slider controls, hardware diagnostic chips, and quick action bars.

### 2. Precise Hardware Diagnostics & 3-Mode Matrix
- **Tri-State Operational Engine**:
  - 🟢 **Online**: Wi-Fi connected and active bidirectional audio WebSocket connection established.
  - 🟡 **Standby**: Wi-Fi connected and healthy; WebSocket disconnected or paused (speaker waiting for consultation/speech trigger).
  - 🔴 **Offline**: Wi-Fi link lost or device disconnected from power.
- **Wi-Fi RSSI Signal Telemetry**: Dynamic signal strength bars with color grading (Green `>= -50 dBm`, Yellow `-51 to -70 dBm`, Red `< -70 dBm`).
- **Network & Location Context**: Real-time ISP detection (e.g., Airtel Xstream Fiber), SSID network identification, local IP assignment, and city location tagging (passthrough of clinic/room context).
- **Remote Hardware Controls**: Real-time master volume slider (`0%` to `100%`), soft reboot trigger, ping latency telemetry, and power source status (USB-C powered / battery level).

### 3. Guided 7-Step Hardware Commissioning Wizard
A visual step-by-step wizard to register and commission bare ESP32-S3 boards into production:
1. **Role Selection**: Assign as Doctor Consultation Unit or Patient Home Companion.
2. **Device ID & MAC Registration**: Auto-allocates next sequence ID (`SPK-xxxxxxx`) and captures hardware MAC address (`AA:BB:CC:DD:EE:FF`).
3. **Clinical / Patient Metadata**: Captures physician name, doctor ID, contact info, medical specialty, clinic name, and physical address.
4. **Clinic Wi-Fi Configuration**: Pre-provisions clinic/home Wi-Fi credentials (`SSID` and `Password`) for on-boot auto-connect.
5. **Firmware Verification**: Verifies installed firmware image version (e.g., `v1.4.2-bargein`).
6. **Hardware Self-Test & Diagnostic**: Validates microphone ADC input, I2S DAC output, speaker playback, and Wi-Fi latency.
7. **Fleet Activation**: Cryptographically commits the device to the database and broadcasts the new online node across the dashboard.

### 4. Zero-Trust Access & Security Management
- **Argon2id Password Hashing**: State-of-the-art memory-hard hashing with unique cryptographic salts per user.
- **JWT Session Tokens**: Cryptographically signed access tokens with configurable expiration.
- **Brute-Force Shield**: Progressive exponential rate-limiting on failed login attempts with IP tracking and lockouts.
- **HIPAA-Compliant PII Redaction**: Phone numbers, emails, and sensitive identifiers are masked by default with an authorized on-demand click reveal.
- **Role-Based Access Control (RBAC)**: Fine-grained permissions separating `Admin`, `Engineer`, and `Operator` tiers.

---

## 🏛️ System Architecture

```text
  +-------------------------------------------------------------------------+
  |                        ESP32-S3 Smart Speakers                          |
  |  [ Wi-Fi: 802.11 b/g/n ]   [ Audio: 16 kHz Mono PCM ]   [ I2S / ADC ]   |
  +-------------------------------------------------------------------------+
                                      │
                         HTTP / WS    │  Heartbeat & Telemetry
                                      ▼
  +-------------------------------------------------------------------------+
  |                   FastAPI Fleet Backend (Port 8010)                     |
  |  ├── /ws/fleet              -> Real-time telemetry broadcast            |
  |  ├── /api/speakers          -> CRUD, volume, reboot, commissioning      |
  |  ├── /api/auth/login        -> Argon2id verification + JWT issue        |
  |  └── Asyncio Watchdog       -> 15s interval health checks & timeouts    |
  +-------------------------------------------------------------------------+
                                      │
                         REST / WS    │  Live Reactive State
                                      ▼
  +-------------------------------------------------------------------------+
  |                   React 18 + Vite Frontend (Port 5173)                  |
  |  ├── Doctor & Patient Ecosystem Views (Table & Cards mode)              |
  |  ├── 3-Mode Operational State Machine (Online / Standby / Offline)      |
  |  ├── 7-Step Hardware Commissioning Wizard Modal                         |
  |  ├── User Management Panel & Zero-Trust Auth Modal                      |
  |  └── Adaptive Dark / Light Custom Design System                         |
  +-------------------------------------------------------------------------+
```

---

## 📊 Hardware State & Diagnostics Matrix

The dashboard categorizes hardware status using a deterministic 3-mode evaluation engine:

| Status | State Badge | Wi-Fi Connection | WebSocket Connection | Typical Scenario |
|---|---|---|---|---|
| **Online** | 🟢 `ONLINE` | Connected (`IP assigned`) | Connected (`/ws/live` active) | Active doctor consultation in progress; bi-directional audio streaming. |
| **Standby** | 🟡 `STANDBY` | Connected (`IP assigned`) | Disconnected / Halted | Speaker powered and linked to clinic Wi-Fi; idle awaiting doctor call. |
| **Offline** | 🔴 `OFFLINE` | Disconnected (`No IP`) | Disconnected | Power cut, clinic Wi-Fi out of range, or unprovisioned hardware. |

### Wi-Fi RSSI Signal Strength Reference
- **>= -50 dBm**: 🟢 Excellent (Full 4-bar signal, ideal for uncompressed PCM audio)
- **-51 to -65 dBm**: 🟢 Good (Reliable streaming, zero packet drop)
- **-66 to -75 dBm**: 🟡 Fair (Occasional jitter; recommendations displayed)
- **< -75 dBm**: 🔴 Poor (High risk of audio packet drop; warning badge active)

---

## 🛠️ Technology Stack

### Frontend Architecture
- **Framework**: React 18.2 with Functional Components and Custom Hooks
- **Bundler & Dev Server**: Vite 8.x with Hot Module Replacement (HMR)
- **Styling**: Pure Vanilla CSS Custom Properties (Design Tokens), Dark/Light mode engine, glassmorphic panels
- **Icons**: Lucide React (Clean, tree-shakable feather-based icons)
- **Real-Time Client**: Native HTML5 WebSocket API with automatic exponential reconnect

### Backend Architecture
- **Web Framework**: FastAPI (High-performance ASGI Python framework)
- **ASGI Server**: Uvicorn with auto-reload capabilities
- **Async Runtime**: Python `asyncio` for non-blocking I/O and concurrent fleet heartbeats
- **Security & Crypto**: `passlib` (Argon2id), `PyJWT` (JSON Web Tokens), `secrets` for cryptographic salt generation
- **Persistence**: Hybrid architecture supporting PostgreSQL via `psycopg2` with zero-config JSON flat-file fallback (`fleet_db.json`, `users_db.json`)

---

## 📁 Repository Structure

```text
Speaker-Management-Dashboard/
├── backend/
│   ├── app.py                     # FastAPI application, REST endpoints & WebSocket hub
│   ├── auth.py                    # Argon2id hashing, JWT token manager & rate limiter
│   ├── postgres_db.py             # PostgreSQL connector with automatic schema migration
│   ├── fleet_db.json              # Local fallback database for speaker fleet records
│   ├── users_db.json              # Local fallback database for dashboard user accounts
│   ├── requirements.txt           # Python backend dependencies
│   └── .env.example               # Environment template for backend service
├── frontend/
│   ├── src/
│   │   ├── App.jsx                # Main dashboard UI, view router, modals & state
│   │   ├── styles.css             # Theme variables, glassmorphic layout & utility classes
│   │   ├── YHealthLogo.jsx        # Scalable SVG brand logo component
│   │   └── main.jsx               # React DOM initialization
│   ├── index.html                 # HTML5 document template
│   ├── package.json               # Node.js project manifest & scripts
│   ├── package-lock.json          # Deterministic dependency lockfile
│   ├── vite.config.js             # Vite build pipeline & server proxy configuration
│   ├── tailwind.config.js         # Tailwind configuration file
│   ├── postcss.config.js          # PostCSS plugins configuration
│   ├── .env.example               # Frontend environment template
│   └── .gitignore                 # Frontend specific git exclusions
├── speaker_device_mapping.json     # Central MAC <-> Device ID <-> QR registry (20 units)
├── start_dashboard.bat            # 1-Click Windows launch automation batch script
├── .gitignore                     # Repository-wide git ignore rules
└── README.md                      # Comprehensive project documentation
```

---

## 🚀 Quick Start & Installation

### Prerequisites
- **Node.js**: v18.0.0 or higher ([Download Node.js](https://nodejs.org/))
- **Python**: v3.10 or higher ([Download Python](https://www.python.org/))
- **Git**: Installed and available on system `PATH`

---

### 🐳 Docker Deployment (Recommended)

#### Option 1: Unified All-in-One Container
Build and run the production image (bundles the React 18 frontend and FastAPI backend into a single container on port 8010):
\\ash
# Build the unified image
docker build -t yhealth-fleet-dashboard .

# Run the container
docker run -d -p 8010:8010 --name yhealth-dashboard yhealth-fleet-dashboard
\> Access the complete dashboard and API at **\http://localhost:8010\**.

#### Option 2: Docker Compose
\\ash
docker compose up -d
\
---

### 1-Click Launch (Windows)

The simplest way to start both the backend API and frontend dev server simultaneously is using `start_dashboard.bat`:

```cmd
start_dashboard.bat
```

This automated script will:
1. Launch the **FastAPI Fleet Backend** on `http://localhost:8010` in a dedicated terminal.
2. Launch the **Vite Frontend Dev Server** on `http://localhost:5173` in a dedicated terminal.
3. Automatically open your default browser to `http://localhost:5173`.

---

### Manual Setup

#### Step 1: Clone the Repository
```bash
git clone https://github.com/gupta-tanvi-tft/Speaker-Management-Dashboard.git
cd Speaker-Management-Dashboard
```

#### Step 2: Configure & Launch Backend
```bash
cd backend

# Create virtual environment
python -m venv .venv

# Activate virtual environment
# Windows:
.\.venv\Scripts\activate
# Linux/macOS:
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Create .env from template
copy .env.example .env   # On Linux/macOS: cp .env.example .env

# Run FastAPI backend
python app.py
```
> Backend API will be active at **`http://localhost:8010`**  
> Interactive Swagger API Documentation: **`http://localhost:8010/docs`**

#### Step 3: Configure & Launch Frontend
Open a new terminal:
```bash
cd frontend

# Install Node dependencies
npm install

# Create .env from template
copy .env.example .env   # On Linux/macOS: cp .env.example .env

# Start development server
npm run dev
```
> Frontend Dashboard will be accessible at **`http://localhost:5173`**

---

## ⚙️ Environment Configuration

### Backend (`backend/.env`)
```ini
# Operating mode: 'demo' (mock fallback) or 'production' (live database)
DASHBOARD_MODE=demo

# Optional PostgreSQL Connection (falls back to fleet_db.json if omitted)
# DATABASE_URL=postgresql://user:password@localhost:5432/yhealth_fleet

# Grafana Monitoring Integration
GRAFANA_BASE_URL=https://monitoring.yhealth.me
GRAFANA_DASHBOARD_UID=ad7r252
GRAFANA_DEVICE_VARIABLE=device_id
```

### Frontend (`frontend/.env`)
```ini
# Backend API Base URL
VITE_BACKEND_API=http://localhost:8010/api

# WebSocket Fleet Endpoint
VITE_WS_ENDPOINT=ws://localhost:8010/ws/fleet

# Grafana Embedded Monitoring
VITE_GRAFANA_BASE_URL=https://monitoring.yhealth.me
VITE_GRAFANA_DASHBOARD_UID=ad7r252
VITE_GRAFANA_DEVICE_VARIABLE=device_id
```

---

## 📡 API & WebSocket Reference

### REST Endpoints (`http://localhost:8010/api`)

| Method | Endpoint | Description | Auth Required |
|---|---|---|---|
| `GET` | `/speakers` | Retrieve list of all registered speakers with live telemetry | No |
| `GET` | `/speakers/{device_id}` | Retrieve comprehensive detail for a specific speaker | No |
| `POST` | `/speakers` | Commission and activate a new speaker into the fleet | Yes |
| `DELETE` | `/speakers/{device_id}` | Decommission and remove a speaker from the registry | Yes |
| `POST` | `/speakers/{device_id}/volume` | Set hardware master output volume (`0-100%`) | Yes |
| `POST` | `/speakers/{device_id}/reboot` | Issue soft restart command to the ESP32-S3 | Yes |
| `POST` | `/auth/login` | Authenticate dashboard user and return JWT bearer token | No |
| `GET` | `/users` | List dashboard administrative users (Admin only) | Yes |
| `POST` | `/users` | Create new administrative user with Argon2id hash | Yes |
| `DELETE` | `/users/{user_id}` | Revoke and delete a dashboard user | Yes |

### WebSocket Endpoint (`ws://localhost:8010/ws/fleet`)
Continuous JSON broadcast streaming live fleet metrics every 5 seconds or upon telemetry state transitions:

```json
{
  "type": "FLEET_UPDATE",
  "timestamp": "2026-11-11T11:45:00.000Z",
  "summary": {
    "total": 6,
    "online": 2,
    "standby": 1,
    "offline": 3
  },
  "speakers": [
    {
      "device_id": "SPK-0000001",
      "status": "online",
      "wifi_rssi": -48,
      "wifi_connected": true,
      "volume": 85,
      "ip_address": "192.168.50.96",
      "location_label": "New Delhi (Wi-Fi: TFTus-WiFi)"
    }
  ]
}
```

---

## 🔒 Enterprise Zero-Trust Security

1. **Password Hashing (Argon2id)**:
   - Evaluated using 64 MB memory cost, 3 iterations, and 4 parallel threads.
   - Mitigates GPU-accelerated dictionary and rainbow-table attacks.
2. **Brute-Force Rate Limiting**:
   - Consecutive failed authentications trigger exponential backoff delays (`5s`, `15s`, `60s`, `15m`).
   - Accounts are locked after 5 consecutive failed attempts.
3. **Session Token Expiry**:
   - Cryptographic JWT tokens expire automatically after 8 hours.
4. **HIPAA Redaction**:
   - Protected Health Information (PHI) and doctor phone numbers are masked by default.

---

## ❓ Troubleshooting & FAQ

### 1. Frontend cannot connect to Backend (`Failed to fetch`)
- Ensure the FastAPI server is running on port `8010` (`http://localhost:8010/docs`).
- Check `backend/.env` and `frontend/.env` to confirm ports match.
- Check Windows Defender or local firewall settings for port `8010`.

### 2. Device shows as "Offline" even when plugged in
- Verify ESP32-S3 serial monitor log for Wi-Fi association (`192.168.x.x`).
- Confirm the device is broadcasting heartbeat frames to the voice backend. Devices transition to **Standby** or **Offline** if heartbeats exceed 45 seconds.

### 3. Port Conflicts
- If port `8010` is occupied, change the port in `backend/app.py` and `frontend/src/App.jsx`.
- If port `5173` is occupied, Vite will automatically select `5174` (update browser URL accordingly).

---

## 📝 Contributing & Commit Guidelines

We enforce the **Conventional Commits** specification to ensure a clean, audit-compliant git history:

| Type | Description |
|---|---|
| `feat` | New feature or UI capability |
| `fix` | Bug fix or error resolution |
| `docs` | Documentation update or README improvements |
| `style` | Formatting, CSS polish, theme adjustments (no functional code changes) |
| `refactor` | Code refactoring without behavioral alterations |
| `perf` | Performance optimizations |
| `chore` | Dependency bumps, build script adjustments, configuration updates |

### Example Commit
```bash
git commit -m "feat(telemetry): add live wifi rssi meter and 3-mode status matrix"
```

---

## 📄 License

Proprietary and Confidential. Copyright &copy; 2026 **YHealth Technologies Inc.** All rights reserved.  
Unauthorized distribution, modification, or commercial exploitation without written consent is strictly prohibited.
