# Haven

A multi-agent hospital intelligence platform that coordinates autonomous, context-aware AI agents for real-time patient monitoring, clinical decision support, and hospital resource management.

---

## Overview

Haven functions as a central hospital command center that connects real-time sensory data, patient vitals, autonomous agent intelligence, and spatial floor plan mapping into a unified operational dashboard.

Key capabilities include:
- **Autonomous Multi-Agent Coordination**: Specialized clinical agents collaborate via message passing to handle triage, safety checks, and resource planning.
- **Real-Time Voice Intake**: Conversational voice interface powered by LiveKit and OpenAI that collects clinical intake details, extracts structured data, and prepares actionable summaries.
- **Non-Invasive Computer Vision**: Video-based vital sign extraction (facial photoplethysmography / FPPG) and patient movement/fall monitoring.
- **3D Spatial Floor Plan**: Interactive 3D hospital model rendered with Three.js showing real-time room occupancies, patient statuses, and alert locations.
- **Emergency Escalation & Telephony**: Multi-channel urgent notifications through Vonage (automated voice calls with DTMF response, SMS, and messaging).
- **Wearable Device Integration**: Companion Apple Watch application providing continuous telemetry and heart rate monitoring.

---

## System Architecture

### 1. Voice Processing Pipeline
- **Real-Time Audio Streaming**: Integrated with LiveKit for low-latency bidirectional audio streaming.
- **Speech-to-Text**: Real-time transcription via OpenAI Whisper.
- **Inference & Synthesis**: Accelerated LLM responses paired with natural text-to-speech voice generation.
- **Voice Activity Detection**: Silero VAD configured for instant turn-taking and minimal interruption lag.

### 2. Multi-Stream Synchronization
- Handles concurrent WebSocket streams from LiveKit (voice/video), vital telemetry, and agent alerts.
- Dedicated background processing for computer vision algorithms to ensure non-blocking UI rendering.
- Real-time synchronization layer ensuring all connected clients receive consistent state updates.

### 3. Computer Vision & Vitals Pipeline
- **Facial Photoplethysmography (FPPG)**: Region-of-interest detection using OpenCV with per-channel color normalization.
- **Source Separation**: FastICA source separation for heart rate extraction across rolling time windows.
- **Pose & Activity Detection**: MediaPipe-based landmark estimation for posture monitoring, fall detection, and restlessness analysis.

### 4. 3D Spatial Floor Plan
- Interactive hospital layout rendered with Three.js.
- Real-time color coding and status badges displaying patient acuity, active alerts, and room assignments.
- Natural language querying powered by Claude tool-calling to look up rooms, check equipment, and allocate resources.

---

## Multi-Agent Architecture

Haven deploys specialized autonomous agents configured to communicate through structured message-passing:

| Agent | Role | Capabilities |
| :--- | :--- | :--- |
| **haven_nurse** | Primary Clinical Monitoring | Monitors vitals, evaluates clinical alerts, detects abnormalities, and escalates critical patient conditions. |
| **haven_reconciliation** | Medication Safety | Cross-references active patient medication lists against new prescriptions to flag drug-drug interactions and contraindications. |
| **haven_resource_allocation** | Resource & Capacity Planning | Manages bed allocation, ICU transfers, equipment availability, and medical staff distribution based on patient acuity. |
| **haven_asi_coordinator** | Ecosystem Orchestration | Coordinates inter-agent workflows, routes external diagnostic requests, and manages cross-facility health queries. |

---

## Tech Stack

### Frontend
- **Framework**: Next.js 15 (App Router), React 19, TypeScript
- **Styling**: Tailwind CSS
- **3D Graphics**: Three.js, React Three Fiber, Drei, Smplrspace
- **State & UI**: Zustand, Framer Motion, Lucide Icons, Recharts

### Backend
- **Framework**: FastAPI, Python 3.12, Uvicorn, Gunicorn
- **WebSockets**: Native ASGI WebSockets for real-time telemetry and streaming
- **Database & Auth**: Supabase (PostgreSQL, Realtime subscriptions)

### AI & Agents
- **Voice & Media**: LiveKit Agents SDK, OpenAI Whisper & TTS, Groq
- **LLM Reasoning**: Anthropic Claude API
- **Agent Framework**: Fetch.ai uAgents & ASI Alliance ecosystem

### Computer Vision & Telephony
- **CV & Signal Analysis**: OpenCV, MediaPipe, NumPy, SciPy
- **Telephony & Notifications**: Vonage Voice API (TTS & DTMF), Vonage SMS & Messages API

---

## Repository Structure

```
├── backend/                  # FastAPI backend server
│   ├── app/                  # Application code (routes, websockets, services)
│   ├── data/                 # Sample patient and trial protocol data
│   ├── database/             # Database migrations & schemas
│   └── scripts/              # Data generation and setup scripts
├── frontend/                 # Next.js web dashboard
│   ├── app/                  # Next.js App Router pages (dashboard, patient-view, stream, intake)
│   ├── components/           # UI components (charts, modals, 3D view, chat)
│   └── lib/                  # API client configurations and helpers
├── haven-agents/             # Multi-agent implementations & deployment scripts
│   ├── agents/               # Clinical agent definitions
│   └── models/               # Pydantic data models for vitals, alerts, and protocols
├── supabase/                 # Supabase functions and database configurations
└── watchos-app/              # Apple Watch (watchOS) vitals streaming companion application
```

---

## Getting Started

### Prerequisites
- Node.js (v18+) and npm
- Python (v3.11 or v3.12)
- Supabase account or local Supabase instance

### Backend Setup
1. Navigate to the backend directory:
   ```bash
   cd backend
   ```
2. Create and activate a virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Configure your `.env` file (refer to `.env.example`).
5. Run the backend server:
   ```bash
   uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
   ```

### Frontend Setup
1. Navigate to the frontend directory:
   ```bash
   cd frontend
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Configure environment variables in `.env.local`:
   ```bash
   NEXT_PUBLIC_API_URL=http://localhost:8000
   ```
4. Start the development server:
   ```bash
   npm run dev
   ```
5. Open [http://localhost:3000](http://localhost:3000) in your browser.
