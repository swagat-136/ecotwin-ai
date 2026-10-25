# EcoTwin AI — Immersive 3D Smart Building Digital Twin

**EcoTwin AI** is an AI-powered smart-building digital twin platform for commercial real estate, energy engineering, and automated HVAC optimization. It features an interactive, procedural 3D architectural environment rendered via Three.js and `@react-three/fiber`, connected to a deterministic physics simulation engine, explainable AI recommendation algorithms, and an adapter for the Kaggle NeurIPS 2026 Smart Buildings benchmark.

---

## 🌟 Key Features

### 1. Central Interactive 3D Digital Twin
- **Procedural Multi-Storey Geometry**: 4 full storeys with architectural concrete slabs, curtain glass envelope with low-E tint, structural columns, interior partitions, troffer lights, and furniture silhouettes (workstations, conference suites, server racks with activity LEDs, labs).
- **Rooftop Mechanical Penthouse**: Centrifugal water-cooled chillers with rotating condenser fan blades, induced-draft evaporative cooling towers, solar PV canopy array, and main distribution ductwork.
- **Dynamic 3D Visual Overlays**:
  - `Architectural`: Pristine PBR structural and glass materials.
  - `Thermal Comfort`: False-color heat mapping from chilled (20°C cyan) to optimal comfort (22°C emerald) to warm/overheated (26°C+ crimson).
  - `Energy Intensity`: Energy load heat-dissipation glows and real-time load halos.
  - `Occupancy Density`: Head-count density rings and demand-controlled zones.
  - `Airflow & Chilled Loop`: Animated 3D particle stream tracing chilled water down the central riser and air distribution across VAV terminal dampers.
- **Exploded View Stack**: Vertical storey separation slider (0% to 100%) with physical exponential damping for inspecting individual floor interiors.
- **Bi-directional Interactivity**: Click any room, floor slab, or rooftop HVAC asset in 3D to inspect its live operating telemetry and adjust thermostat setpoints in real time.

### 2. High-Fidelity Physics Simulation Engine
- **Diurnal Environmental Dynamics**: Dynamic ambient temperature cycle, solar radiation flux ($W/m^2$), and time-of-use (TOU) electricity pricing tariffs ($0.11/kWh to $0.32/kWh).
- **Physics Equations**:
  $$Q_{\text{conductive}} = U \cdot A \cdot (T_{\text{ambient}} - T_{\text{indoor}})$$
  $$Q_{\text{internal}} = N_{\text{occupants}} \cdot q_{\text{human}} + P_{\text{equipment}} + P_{\text{lighting}}$$
  $$Q_{\text{cooling}} = \text{COP} \cdot P_{\text{HVAC}}$$
- **ASHRAE Standard 55**: Tracks indoor Predicted Mean Vote (PMV) and percentage of spaces within comfort compliance bounds (21.0°C – 24.5°C).

### 3. Explainable AI Optimization Engine
- Analyzes occupancy trends, ambient temperatures, and electricity pricing tariffs to generate actionable recommendations:
  - **Dynamic Chilled Water Temperature Reset**: Raises chilled water loop from 6.8°C to 8.3°C, lifting chiller COP by 14%.
  - **Unoccupied Deep Eco-Setback**: Modulates VAV airflow to vacant zones.
  - **Automated Pre-Cooling**: Leverages building concrete thermal mass during off-peak hours to shed up to 32 kW of peak afternoon compressor demand.
  - **Server Room Economizer**: Modulates variable frequency fan speeds while maintaining ASHRAE A1 server rack intake temperatures.
- 1-click **Optimize** button immediately updates 3D thermal false-colors, energy particle velocity, and KPI meters.

### 4. Scenario Laboratory
- Stress-test the facility under extreme heatwaves (+38.5°C), critical grid demand response pricing ($0.55/kWh), high occupancy events (+150%), and pre-cooling policies.
- Instant before-and-after comparison matrix with comfort constraint violation alarms.

### 5. Kaggle NeurIPS 2026 Smart Buildings Competition Readiness
- Separate Gym-compatible adapter (`src/services/kaggleAdapter.ts`) with standardized observation and action spaces.
- Multi-objective reward benchmark:
  $$\text{Reward} = - (0.55 \cdot \text{EnergyCost} + 0.35 \cdot \text{ComfortViolation} + 0.10 \cdot \text{CarbonEmissions})$$
- 24-hour evaluation runner comparing candidate neural policy (PPO/SAC) against standard ASHRAE 90.1 Rule-Based Controller (RBC).

---

## 🏗️ Architecture & Project Structure

```
├── backend/
│   ├── main.py                  # Python FastAPI REST server
│   └── requirements.txt         # FastAPI, Uvicorn, Pydantic dependencies
├── src/
│   ├── components/
│   │   ├── 3d/
│   │   │   ├── BuildingCanvas.tsx          # R3F Canvas, studio lighting, OrbitControls
│   │   │   ├── BuildingScene.tsx           # Scene orchestrator, damping, floor stack
│   │   │   ├── Floor.tsx                   # Storey slab, glass envelope, partitions
│   │   │   ├── Room.tsx                    # Individual room geometry & furniture
│   │   │   ├── HVACUnit.tsx                # Chillers, rotating fans, cooling towers
│   │   │   ├── EnergyFlowParticles.tsx     # Animated particle flow
│   │   │   ├── SurroundingEnvironment.tsx  # Plaza, trees, ground lighting
│   │   │   └── LegendOverlay.tsx           # Overlay color explanations
│   │   └── ui/
│   │       ├── TopNav.tsx                  # Live time clock, play/pause, AI optimize
│   │       ├── Sidebar.tsx                 # Tab navigation & collapsible drawer
│   │       ├── BottomControlDock.tsx       # Overlays, exploded slider, camera presets
│   │       ├── ContextualPanel.tsx         # Room, floor, and HVAC inspector
│   │       ├── MetricCards.tsx             # Real-time KPI summary cards
│   │       ├── EnergyCharts.tsx            # Recharts 24h curves & floor breakdown
│   │       ├── HVACControlPanel.tsx        # Central plant machinery dashboard
│   │       ├── RecommendationPanel.tsx     # Explainable AI recommendations
│   │       ├── ScenarioLab.tsx             # Interactive scenario simulator
│   │       ├── SustainabilityReport.tsx    # LEED scorecard and ESG metrics
│   │       ├── KaggleCompetitionSandbox.tsx# NeurIPS 2026 benchmark adapter
│   │       ├── LoadingScreen.tsx           # Wireframe initialization animation
│   │       └── NotificationModal.tsx       # System alert modal
│   ├── services/
│   │   ├── simulationEngine.ts  # Deterministic thermal & electrical physics
│   │   ├── optimizationEngine.ts# Explainable AI heuristics & policies
│   │   ├── kaggleAdapter.ts     # Gym-like RL benchmark environment
│   │   └── apiClient.ts         # Dual-mode backend / browser fallback client
│   ├── types/
│   │   └── index.ts             # TypeScript definitions
│   ├── App.tsx                  # Main application orchestrator
│   ├── index.css                # Tailwind CSS v4 styling & scrollbars
│   └── main.tsx                 # React DOM mount point
├── index.html                   # HTML entry point with synchronized meta
├── metadata.json                # AI Studio manifest
├── package.json                 # Node dependencies
├── tsconfig.json                # TypeScript configuration
└── vite.config.ts               # Vite configuration
```

---

## 🚀 Setup & Execution Instructions

### Prerequisites
- Node.js >= 18.0.0
- npm >= 9.0.0
- (Optional) Python 3.10+ for the FastAPI backend

### 1. Frontend Development Server (Port 3000)
```bash
# Install Node dependencies
npm install

# Run the Vite dev server
npm run dev
```
The application will launch on `http://localhost:3000`.

### 2. Optional Python FastAPI Backend
The application runs out-of-the-box with a high-performance, deterministic browser physics simulation. To optionally connect the Python FastAPI service:
```bash
# Install backend requirements
pip install -r backend/requirements.txt

# Start FastAPI server on port 8000
python backend/main.py
```
The frontend API client will automatically detect the FastAPI backend on port 8000.

### 3. Production Build
```bash
# Build production bundle with TypeScript checks
npm run build

# Preview production build locally
npm run preview
```

### 4. Running Lint & Typechecks
```bash
npm run lint
```

---

## 🌐 Deploying for 100% Free

Because **EcoTwin AI** is compiled into a high-performance static client bundle with built-in client-side physics and 3D WebGL rendering, you can deploy it completely free of charge to any global static host:

### Option 1: Vercel (Recommended - Zero Config)
1. Push your repository to **GitHub**.
2. Go to [vercel.com](https://vercel.com) and click **"Add New Project"**.
3. Import your GitHub repository.
4. Framework preset is automatically detected as **Vite** (`npm run build`, output directory: `dist`).
5. Click **"Deploy"**. The included `vercel.json` ensures full SPA routing support.

### Option 2: Netlify (Drag & Drop or Git)
1. Run `npm run build` to generate the `dist` folder.
2. Go to [app.netlify.com/drop](https://app.netlify.com/drop) and drag the `dist` folder directly onto the screen.
3. Or link your GitHub repository on Netlify — the included `netlify.toml` automatically configures the build and rewrites.

### Option 3: GitHub Pages
1. Install `gh-pages` if desired: `npm install -D gh-pages`
2. In `package.json`, add `"deploy": "gh-pages -d dist"`
3. Run `npm run build && npm run deploy` to publish to `https://<username>.github.io/<repo-name>/`.

### Option 4: Free Python FastAPI Hosting (If using Python Backend)
- **Render.com**: Create a free Web Service linked to the repo (`buildCommand: pip install -r backend/requirements.txt`, `startCommand: uvicorn backend.main:app --host 0.0.0.0 --port 10000`).
- **Hugging Face Spaces**: Deploy as a free Docker/FastAPI Space.

---

## 🏆 Kaggle Competition Integration Guide
The competition adapter in `src/services/kaggleAdapter.ts` provides:
1. `getObservation(step)`: Extracts normalized ambient conditions, zone temperatures, occupancies, and tariffs.
2. `mapModelOutputToAction(vector)`: Maps normalized agent policy outputs $[-1, 1]$ into continuous actuator commands (chiller supply temp setpoint [6.0°C - 11.5°C] and VAV damper ratios [0.1 - 1.0]).
3. `step(action)`: Advances the simulator by 1 evaluation step and returns reward, costs, and comfort penalty degree-hours.
4. `runEpisodeBenchmark()`: Runs a standardized 24-step comparison against the baseline controller.
