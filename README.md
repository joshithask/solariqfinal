# SOLAR-IQ: Solar Roof Analyzer AI

**Team IRONCLAD** · SS26 AI First Hackathon · Track: AI for Sustainability & Climate Action

Smart rooftop analysis for a sustainable future. Upload a photo of a roof, get an instant AI-generated solar feasibility report — no site visit, no manual measurement.

## Live Demo
- Frontend: `<add your deployed URL here>`
- API docs (Swagger): `<add your deployed backend URL>/docs`
- Demo video: `<add your video link here>`

## Architecture

```
┌─────────────┐      photo + location      ┌──────────────────┐
│   React     │ ──────────────────────────► │   FastAPI        │
│  Frontend   │                              │   Backend        │
│  (Vite)     │ ◄────────────────────────── │                   │
└─────────────┘      JSON report             └──────┬────────────┘
                                                      │
                       ┌──────────────────────────────┼───────────────────────────┐
                       ▼                              ▼                           ▼
              ┌─────────────────┐          ┌────────────────────┐      ┌──────────────────┐
              │  Roof Detection  │          │   Solar Calc        │      │   AI Report Gen   │
              │  (OpenCV contour │          │   (Open-Meteo solar │      │   (Anthropic API   │
              │   + obstacle     │          │    irradiance API + │      │    / Claude)       │
              │   segmentation)  │          │    formula model)   │      │                    │
              └─────────────────┘          └────────────────────┘      └──────────────────┘
```

**Pipeline:**
1. User uploads a rooftop photo and shares/enters a location.
2. **Computer Vision** (`roof_detection.py`) finds the roof outline via edge/contour detection and subtracts detected obstacles (chimneys, vents, AC units) to get usable area.
3. **Solar Calc** (`solar_calc.py`) pulls real daily solar irradiance for that location from the free Open-Meteo API and applies a standard panel-density + performance-ratio formula to estimate system capacity, annual generation, savings, and CO₂ offset.
4. **Generative AI** (`report_gen.py`) sends the computed numbers to Claude, which writes a plain-language sustainability report and recommendation.
5. Frontend displays roof stats, solar potential, and the AI report.

## Tech Stack
- **Frontend:** React 18 + Vite, Axios
- **Backend:** FastAPI, Uvicorn
- **Computer Vision:** OpenCV (contour detection, adaptive thresholding for obstacle segmentation)
- **Solar data:** Open-Meteo Solar Radiation API (free, no key required)
- **Generative AI:** Anthropic Claude API
- **Formulas:** panel density (kW/m²), performance ratio, India grid emission factor (CEA baseline)

## Local Setup

### Backend
```bash
cd backend
python3 -m venv venv && source venv/bin/activate  
pip install -r requirements.txt
cp .env.example .env        
uvicorn main:app --reload --port 8000
```
API will be live at `http://localhost:8000` (Swagger docs at `/docs`).

### Frontend
```bash
cd frontend
npm install
cp .env.example .env        
npm run dev
```
App will be live at `http://localhost:5173`.

> Note: if `ANTHROPIC_API_KEY` isn't set, the AI report step falls back to a template so the rest of the demo still works. If the Open-Meteo call fails (e.g. no internet), solar calc falls back to a conservative default irradiance value.

## MVP Limitations & Roadmap
This is a hackathon MVP — honest tradeoffs made to ship in the time-boxed sprint:
- **Roof scale:** without camera calibration or satellite data, pixel-to-meter conversion uses a user-provided reference width (or a conservative default). Next step: integrate satellite imagery / LiDAR for calibration-free scale.
- **Roof segmentation:** classical CV (Canny edges + contours) rather than a trained segmentation model. Next step: fine-tune YOLO/SAM on a rooftop imagery dataset, as outlined in the pitch deck's technology roadmap.
- **Panel layout:** currently a flat area-based capacity estimate, not panel-by-panel placement. Next step: geometric panel-packing optimization accounting for roof shape and orientation.


# solariqfinal
