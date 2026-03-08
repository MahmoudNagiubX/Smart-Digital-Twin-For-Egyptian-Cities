# 🏗️ Full System Architecture Design

This step defines how the whole platform works technically from data collection to dashboard output.  
For this project, the best architecture is a **layered modular architecture** so the system is:

- scalable  
- easier to build in parts  
- easier to test  
- easier to present academically  
- realistic as a smart-city platform  

---

## 🧠 1. Architecture Philosophy

The system should be built as a **multi-layer AI urban intelligence platform**.

That means:

- each layer has a clear job  
- modules can be built independently  
- all outputs are merged into one digital twin  
- the dashboard reads from a unified backend, not from isolated scripts  

So instead of making:  
“one flood model here, one NLP notebook there, one CV model somewhere else”

we build:

**one connected urban intelligence system**

---

## 🏢 2. High-Level Architecture

The full architecture should contain these main layers:

- **Layer 1 — Data Acquisition Layer**  
  Collects urban data from different internal and external sources.

- **Layer 2 — Data Storage Layer**  
  Stores raw data, processed data, geospatial data, and model outputs.

- **Layer 3 — Data Processing & Feature Engineering Layer**  
  Cleans, transforms, enriches, and prepares data for AI models.

- **Layer 4 — AI Intelligence Layer**  
  Runs prediction, detection, classification, optimization, estimation, and assistant models.

- **Layer 5 — Digital Twin Integration Layer**  
  Combines outputs from all modules into one unified district state.

- **Layer 6 — API & Backend Layer**  
  Serves processed results to the dashboard and external consumers.

- **Layer 7 — Visualization & Decision Support Layer**  
  Interactive geospatial dashboard and risk intelligence interface.

---

## 🔄 3. Full End-to-End System Flow

This is the exact logic of how the system should work:

- **Step A — Collect Data**  
  The system ingests data from:
  - weather APIs
  - traffic APIs
  - satellite imagery
  - complaint platforms / social media
  - road images
  - infrastructure records
  - GIS layers
  - demographic and population datasets
  - thermal and environmental layers

- **Step B — Store Raw Data**  
  All incoming data is stored in structured repositories:
  - relational database
  - geospatial database
  - image storage
  - model artifact storage
  - vector storage for the AI assistant

- **Step C — Preprocess Data**  
  The raw data is cleaned and transformed:
  - missing values handled
  - coordinates standardized
  - timestamps unified
  - Arabic text cleaned
  - imagery tiled or resized
  - features engineered
  - thermal layers aligned
  - infrastructure features prepared
  - retrieval-ready text chunks prepared for the assistant

- **Step D — Run Specialized AI Modules**  
  Each module processes its own prepared input:
  - flood prediction
  - traffic forecasting
  - complaint NLP
  - road damage detection
  - construction detection
  - garbage monitoring
  - emergency route optimization
  - infrastructure stress prediction
  - heat risk detection
  - economic impact estimation
  - district risk score
  - AI urban assistant

- **Step E — Fuse Outputs**  
  Model outputs are converted into geospatial intelligence layers and merged into the digital twin state.

- **Step F — Serve Results**  
  Backend APIs expose the current state of the district:
  - map layers
  - risk scores
  - hotspot summaries
  - predictions
  - alerts
  - route recommendations
  - economic impact summaries
  - natural-language responses from the assistant

- **Step G — Visualize and Support Decisions**  
  The dashboard displays the district with smart overlays and insights.

---

## 🔍 4. Detailed Layer-by-Layer Design

### 📥 Layer 1 — Data Acquisition Layer

This layer is responsible for bringing all city-related data into the system.

### Data Sources

- **Weather Data**
  - Used for: flood prediction, heat analysis, environmental context
  - Possible sources: NASA POWER API, OpenWeather, Egyptian weather datasets, rainfall records

- **Traffic Data**
  - Used for: traffic forecasting, route optimization, district risk score
  - Possible sources: Google Maps traffic, OpenStreetMap road network, map-based congestion estimates, historical route data

- **Satellite Imagery**
  - Used for: construction detection, land use monitoring, environmental analysis, heat risk mapping
  - Possible sources: Sentinel-2, Landsat, Google Earth Engine exports

- **Citizen Complaints**
  - Used for: complaint hotspot analysis, sanitation signals, infrastructure issue detection, assistant knowledge context
  - Possible sources: social media posts, public complaint portals, manually collected datasets

- **Road / Street Images**
  - Used for: road damage detection, garbage monitoring
  - Possible sources: public datasets, mobile-collected images, street camera feeds, sample curated datasets

- **Infrastructure Data**
  - Used for: infrastructure stress prediction, district analytics
  - Possible sources: road network GIS, water network data, drainage maps, public planning maps

- **Population / Demographic Data**
  - Used for: vulnerability analysis, impact estimation, district scoring, heat risk prioritization
  - Possible sources: CAPMAS, public census data

- **Thermal / Environmental Data**
  - Used for: heat risk detection, urban heat island analysis
  - Possible sources: NASA POWER, Landsat thermal bands, Sentinel products, land surface temperature datasets

- **Emergency Network Data**
  - Used for: emergency route optimization
  - Possible sources: hospital locations, fire stations, ambulance points, main roads, road closure data

### Best Design Choice

Do not connect every source directly to models.  
Instead, all incoming data should first go through an **ingestion pipeline**.

So this layer should have:

- API collectors
- dataset loaders
- image import scripts
- batch ingestion jobs
- retrieval document loaders for the AI assistant

---

### 💾 Layer 2 — Data Storage Layer

This is one of the most important layers.  
The system needs multiple storage types, not just one database.

- **A. Operational Database**
  - Use: PostgreSQL
  - Stores: users, metadata, logs, module outputs, structured records

- **B. Geospatial Database**
  - Use: PostGIS extension on PostgreSQL
  - Stores: district boundaries, roads, buildings, hotspots, polygons, geospatial predictions, map layers, route layers, thermal zones, infrastructure segments

- **C. File / Image Storage**
  - Stores: satellite image tiles, road images, waste detection images, processed raster outputs, model inference results
  - Could be: local storage for prototype, cloud object storage later

- **D. Model Artifact Storage**
  - Stores: trained model weights, evaluation reports, preprocessing encoders, label mappings

- **E. Vector / Retrieval Storage**
  - Stores: embeddings for reports, summaries, complaint insights, digital twin context, and urban assistant retrieval data

### Best Design Choice

For MVP:

- PostgreSQL + PostGIS
- local folder/object storage
- model folders per module
- simple vector database or embedding store for assistant experiments

That is realistic and manageable.

---

### ⚙️ Layer 3 — Data Processing & Feature Engineering Layer

This layer converts raw urban data into model-ready inputs.

### Responsibilities

- **Structured Data Processing:**  
  clean weather and traffic records, remove duplicates, align timestamps, normalize fields, aggregate by district zone/grid

- **Geospatial Processing:**  
  convert addresses to coordinates, map complaints to zones, overlay road networks with district boundaries, generate spatial grids, intersect risk outputs with map layers, create route graphs, connect infrastructure layers

- **NLP Preprocessing:**  
  Arabic text normalization, remove noise and URLs, tokenize dialectal Arabic, sentiment labels / topic labels preparation, location extraction preprocessing, text chunking for assistant retrieval

- **Computer Vision Preprocessing:**  
  resize images, annotate detection data, crop/augment road images, tile satellite imagery, convert masks for segmentation

- **Time-Series Feature Engineering:**  
  lag features, rolling averages, rainfall accumulation windows, temporal congestion features

- **Infrastructure Feature Engineering:**  
  create pipe-age features, maintenance history features, load intensity features, proximity to excavation/construction, flood exposure indicators

- **Heat Risk Feature Engineering:**  
  land surface temperature, vegetation index, built-up density, shade scarcity, vulnerable population density

- **Economic Estimation Features:**  
  affected road length, traffic delay duration, repair cost assumptions, flood exposure area, sanitation cleanup cost factors

### Best Design Choice

Build this layer as a set of **reusable processing pipelines**.  
Not notebooks only.

Suggested structure:

- `preprocessing/weather_pipeline.py`
- `preprocessing/traffic_pipeline.py`
- `preprocessing/complaint_pipeline.py`
- `preprocessing/satellite_pipeline.py`
- `preprocessing/road_image_pipeline.py`
- `preprocessing/infrastructure_pipeline.py`
- `preprocessing/heat_pipeline.py`
- `preprocessing/economic_pipeline.py`
- `preprocessing/assistant_retrieval_pipeline.py`

---

### 🤖 Layer 4 — AI Intelligence Layer

This is the brain of the platform.  
Each module should be implemented as an independent service or module with:

- defined inputs
- defined outputs
- evaluation metrics
- saved model files

### Core and Extended AI Modules

#### 1. Flood Prediction Module
- **Input:** rainfall, elevation, drainage context, past flood patterns
- **Output:** flood probability by zone, flood risk polygons
- **Model:** Random Forest or CNN-LSTM hybrid

#### 2. Traffic Prediction Module
- **Input:** road network, historical traffic, time features, weather context
- **Output:** congestion level for future time window
- **Model:** LSTM / temporal forecasting model

#### 3. Complaint Analysis Module
- **Input:** Arabic complaint text, timestamps, optional location text
- **Output:** category, sentiment, extracted location, hotspot cluster, summary
- **Model:** CAMeLBERT / AraBERT / BERTopic / summarization pipeline

#### 4. Road Damage Detection Module
- **Input:** road images
- **Output:** pothole/crack detections, severity estimate, location-linked road damage records
- **Model:** YOLO-based detector

#### 5. Construction Detection Module
- **Input:** satellite imagery at time t1 and t2
- **Output:** changed building areas, new construction polygons
- **Model:** U-Net / change detection pipeline

#### 6. Garbage Monitoring Module
- **Input:** street images, complaint clusters
- **Output:** garbage hotspot areas
- **Model:** YOLO11m or integrated CV + NLP support

#### 7. Emergency Route Optimization Module 🚑
- **Purpose:** AI-based routing for ambulances and emergency vehicles
- **Input:** road graph, live/predicted traffic, flooded roads, damaged roads, blocked roads, construction zones, emergency service locations
- **Output:** optimal emergency route, alternative routes, ETA, blocked segment alerts
- **Algorithm:** A* with domain-informed heuristics, optionally enhanced by advanced optimization methods

#### 8. Infrastructure Stress Prediction Module 🚰
- **Purpose:** Predict potential failures in pipelines and infrastructure networks
- **Input:** pipe age, maintenance history, construction density, water usage/load, flood exposure, soil/environmental factors
- **Output:** failure probability, critical segments, maintenance alerts
- **Model:** Gradient Boosting + Random Forest ensemble or hybrid soft-voting model

#### 9. Heat Risk Detection Module 🌡️
- **Purpose:** Identify urban heat island zones affecting vulnerable populations
- **Input:** land surface temperature, vegetation coverage, built-up density, weather context, population density
- **Output:** heat risk zones, vulnerable overlays, thermal hotspot map
- **Model:** spatial regression or geospatial risk-scoring models

#### 10. Economic Impact Estimation Module 💰
- **Purpose:** Estimate economic losses caused by infrastructure and urban issues
- **Input:** flood severity, traffic delays, road damage severity, sanitation burden, infrastructure repair factors, affected population
- **Output:** cost estimates by issue type, district economic burden score, decision-support summaries
- **Method:** rule-based estimation formulas or statistical cost models

#### 11. District Risk Score Module
- **Input:** outputs of all major modules
- **Output:** unified district score, sub-scores by category
- **Model:** weighted scoring engine or learned ensemble

#### 12. AI Urban Assistant Module 🤖
- **Purpose:** Natural-language assistant that answers questions about city conditions
- **Input:** user query, digital twin state, analytics summaries, stored context, retrieval results
- **Output:** natural-language answers, explanations, summaries, recommendations
- **Method:** Retrieval-Augmented Generation (RAG), vector search, prompt orchestration, language model response generation

### Best Design Choice

Do not let models directly talk to the dashboard.  
Every model should output standardized results like:

- zone_id
- latitude/longitude or geometry
- risk_score
- severity
- timestamp
- confidence

That makes integration easy.

---

### 🏙️ Layer 5 — Digital Twin Integration Layer

This layer is what turns the project from “several AI models” into a true digital twin.

### Role of This Layer

It maintains the current virtual state of the district.

That state includes:

- current congestion predictions
- current flood risk areas
- current complaint hotspots
- detected road damage
- detected construction changes
- sanitation problem zones
- infrastructure stress state
- heat stress state
- emergency accessibility state
- economic impact indicators
- district health score
- assistant-ready summarized context

### Digital Twin State Model

We should represent the district as a set of entities:

- **Spatial Entities:** roads, intersections, buildings, zones, infrastructure segments, drainage lines, emergency facilities
- **Dynamic Attributes:** traffic level, flood probability, road condition, complaint intensity, waste level, construction activity, heat stress, infrastructure stress, route accessibility, economic burden

### What This Layer Does

- collects outputs from all modules
- maps outputs to district objects/zones
- resolves time updates
- keeps latest active state
- stores historical snapshots
- aggregates summaries for dashboard and assistant use

### Example

A zone in New Cairo could have:

- flood risk: 0.76
- traffic congestion: high
- complaint sentiment: negative
- road damage count: 4
- waste severity: medium
- infrastructure stress: high
- heat risk: medium
- emergency accessibility: reduced
- economic burden estimate: high
- district score contribution: 0.68

That is digital twin behavior.

### Best Design Choice

For MVP, build the digital twin as:

- zone-based spatial model
- updated tables/views in PostGIS
- unified backend aggregation service

Later it can become more simulation-heavy.

---

### 🌐 Layer 6 — API & Backend Layer

This layer connects intelligence to the user interface.  
Use: **FastAPI**

### Main Responsibilities

- provide map layers
- provide dashboard analytics
- provide module outputs
- provide district summaries
- provide alerts
- provide risk score endpoints
- provide route recommendations
- provide economic summaries
- provide AI assistant responses

### Suggested API Groups

- **Health / Core:**  
  `/health`, `/version`

- **District APIs:**  
  `/districts`, `/districts/{id}/summary`, `/districts/{id}/risk-score`

- **Flood APIs:**  
  `/flood/predictions`, `/flood/hotspots`

- **Traffic APIs:**  
  `/traffic/current`, `/traffic/forecast`

- **Complaints APIs:**  
  `/complaints/hotspots`, `/complaints/summary`, `/complaints/classify`

- **Road Damage APIs:**  
  `/road-damage/detections`, `/road-damage/hotspots`

- **Construction APIs:**  
  `/construction/changes`

- **Garbage APIs:**  
  `/waste/hotspots`

- **Emergency APIs:**  
  `/emergency/routes`, `/emergency/blocked-roads`, `/emergency/eta`

- **Infrastructure APIs:**  
  `/infrastructure/stress`, `/infrastructure/critical-segments`

- **Heat APIs:**  
  `/heat/hotspots`, `/heat/vulnerable-zones`

- **Economic APIs:**  
  `/economic/impact-summary`, `/economic/loss-by-category`

- **Assistant APIs:**  
  `/assistant/query`, `/assistant/context-summary`

- **Twin APIs:**  
  `/digital-twin/state`, `/digital-twin/layers`, `/digital-twin/timeline`

### Best Design Choice

Backend should have:

- service layer
- model inference layer
- database layer
- API routers

not all logic mixed inside route files.

---

### 💻 Layer 7 — Visualization & Decision Support Layer

This is what makes the project look alive.  
Use: **React + Mapbox**

### Main Dashboard Components

- **Interactive Map:**  
  Shows district boundaries, roads, buildings, all active risk layers

- **Layer Control Panel:**  
  User can toggle:
  - flood layer
  - traffic layer
  - complaints layer
  - construction layer
  - road damage layer
  - waste layer
  - infrastructure layer
  - heat layer
  - emergency route layer
  - economic impact layer

- **Risk Summary Cards:**  
  Shows district risk score, top active risks, current alerts, affected zones count

- **Analytics Panel:**  
  Shows complaint category distribution, traffic trends, flood probability trends, construction stats, heat patterns, infrastructure alerts

- **Alerts Panel:**  
  Shows critical flood warnings, high congestion roads, severe road damage, high complaint zones, infrastructure failure alerts, heat warnings

- **Emergency Response Panel:**  
  Shows optimal emergency routes, blocked roads, ETA estimates, route safety indicators

- **Economic Impact Panel:**  
  Shows estimated cost of risks, cost by issue type, total district burden

- **AI Assistant Panel:**  
  Chat-style interface for asking questions about city conditions

- **Timeline / Snapshot View:**  
  Lets user inspect change over time

### Best Design Choice

Start with:

- one district map
- 5–7 main layers
- summary cards
- zone popup details

That is enough for an impressive MVP, while the architecture stays ready for the full system.

---

## 🧩 5. Recommended Architectural Pattern

The best pattern here is:

**Modular Monolith for MVP**

That means:

- one backend project
- separate modules inside it
- one main database
- one frontend app

Why this is best:

- easier to develop
- easier to debug
- easier for student team
- enough for a big graduation project

Later, modules can be split into microservices if needed.

---

## 📂 6. Recommended Internal Backend Structure

A clean structure would be:

```text
backend/
│
├── app/
│   ├── api/routes/
│   │   ├── districts.py
│   │   ├── flood.py
│   │   ├── traffic.py
│   │   ├── complaints.py
│   │   ├── road_damage.py
│   │   ├── construction.py
│   │   ├── waste.py
│   │   ├── infrastructure.py
│   │   ├── heat.py
│   │   ├── emergency.py
│   │   ├── economic.py
│   │   ├── assistant.py
│   │   └── digital_twin.py
│   │
│   ├── services/
│   │   ├── flood_service.py
│   │   ├── traffic_service.py
│   │   ├── complaint_service.py
│   │   ├── road_damage_service.py
│   │   ├── construction_service.py
│   │   ├── waste_service.py
│   │   ├── infrastructure_service.py
│   │   ├── heat_service.py
│   │   ├── emergency_service.py
│   │   ├── economic_service.py
│   │   ├── assistant_service.py
│   │   └── twin_service.py
│   │
│   ├── ml/
│   │   ├── flood/
│   │   ├── traffic/
│   │   ├── complaints/
│   │   ├── road_damage/
│   │   ├── construction/
│   │   ├── waste/
│   │   ├── infrastructure/
│   │   ├── heat/
│   │   ├── emergency/
│   │   ├── economic/
│   │   ├── assistant/
│   │   └── risk_score/
│   │
│   ├── db/
│   │   ├── session.py
│   │   ├── models/
│   │   ├── repositories/
│   │   └── migrations/
│   │
│   ├── schemas/
│   │   ├── district.py
│   │   ├── flood.py
│   │   ├── traffic.py
│   │   ├── complaints.py
│   │   ├── road_damage.py
│   │   ├── construction.py
│   │   ├── waste.py
│   │   ├── infrastructure.py
│   │   ├── heat.py
│   │   ├── emergency.py
│   │   ├── economic.py
│   │   ├── assistant.py
│   │   └── twin.py
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── logging.py
│   │   ├── security.py
│   │   └── constants.py
│   │
│   └── utils/
│       ├── geo.py
│       ├── time.py
│       ├── files.py
│       └── scoring.py
```

---

## ⏱️ 7. Recommended Data Flow Pattern

The right data flow is:

**Batch + Near-Real-Time Hybrid**

Because not all data updates at the same speed.

### Batch Data
Good for:

- satellite imagery
- census data
- infrastructure maps
- historical model training
- economic estimation baselines
- vector indexing for assistant knowledge

### Near-Real-Time Data
Good for:

- traffic conditions
- weather signals
- incoming complaints
- dashboard refresh
- emergency route recalculation
- alert generation

This is much more realistic than pretending everything is real-time.

---

## 📋 8. Standardized Module Output Format

To integrate cleanly, all module outputs should follow one logic.  
Each output record should contain fields like:

- module_name
- zone_id
- geometry
- timestamp
- risk_score
- severity
- confidence
- source
- details

Example:

~~~json
{
  "module_name": "flood_prediction",
  "zone_id": "NC-Z12",
  "timestamp": "2026-03-08T20:00:00",
  "risk_score": 0.82,
  "severity": "high",
  "confidence": 0.89,
  "source": "weather+terrain_model",
  "details": {
    "rainfall_mm": 24.7,
    "drainage_score": 0.31
  }
}
~~~

This same structure can be extended for:
- emergency route warnings
- infrastructure stress outputs
- heat alerts
- economic estimates
- assistant response context metadata

---

# 🏙️ Urban Digital Twin: Project Architecture & Strategy

## 9. Core Digital Twin Objects

To make the twin academically strong, define the main entities now.

### Static Objects
- district
- zone
- road
- intersection
- building
- drainage segment
- infrastructure segment
- emergency facility
- thermal zone

### Dynamic Objects
- flood event state
- traffic state
- complaint state
- road condition state
- construction activity state
- sanitation state
- infrastructure stress state
- heat risk state
- emergency accessibility state
- economic impact state
- district risk state

> **Note:** This distinction is very important in your report. 📝

---

## 10. Risk Aggregation Logic

The district risk score should not be random. Use weighted aggregation.

### Weighted Scoring Formula

\[
\text{District Risk Score} =
(0.20 \times \text{Flood Risk}) +
(0.15 \times \text{Traffic Risk}) +
(0.10 \times \text{Road Damage Risk}) +
(0.10 \times \text{Complaint Severity}) +
(0.10 \times \text{Waste Severity}) +
(0.10 \times \text{Construction / Urban Change Risk}) +
(0.10 \times \text{Infrastructure Stress}) +
(0.10 \times \text{Heat Risk}) +
(0.05 \times \text{Emergency Accessibility Risk})
\]

### Normalization

- **Scale:** 0–10
- **Levels:** low / medium / high

### Important Note

**Economic Impact Estimation** is better treated as a **parallel decision-support metric** rather than directly merged into the pure safety risk score.

For MVP, **rule-based weighted scoring** is the best choice. ⚖️

---

## 11. Architecture Diagram in Text Form

~~~text
┌─────────────────────────────┐
│    External Data Sources    │
│ Weather / Traffic / Sat /   │
│ Complaints / Images / GIS / │
│ Infra / Thermal / Demography│
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│   Data Acquisition Layer    │
│ collectors / loaders / ETL  │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│     Data Storage Layer      │
│ PostgreSQL / PostGIS / File │
│ storage / model artifacts / │
│ vector retrieval storage    │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│   Data Processing Layer     │
│ cleaning / geospatial / NLP │
│ CV preprocessing / features │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│    AI Intelligence Layer    │
│ flood / traffic / NLP / CV  │
│ waste / construction / heat │
│ infra / routing / economic  │
│ assistant / risk scoring    │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  Digital Twin Integration   │
│ unified district state      │
│ zone risk and map layers    │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│       FastAPI Backend       │
│ APIs / analytics / alerts / │
│ assistant / routes / scores │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  React + Mapbox Dashboard   │
│ layers / alerts / insights /│
│ routing / AI assistant      │
└─────────────────────────────┘
~~~

---

## 12. Best Practical Build Order Inside This Architecture

To avoid chaos, implementation follows this sequence:

### First build: 🏗️
- database + PostGIS
- district boundary + zones
- backend skeleton
- frontend map shell

### Then build: 🛠️
- complaint pipeline
- flood pipeline
- traffic pipeline

### Then build: 🔍
- road damage detection
- construction detection
- waste monitoring

### Then build: 🌡️
- heat risk detection
- infrastructure stress prediction

### Then build: 🚑
- emergency route optimization

### Then build: 📊
- digital twin aggregation layer
- district risk score
- economic impact estimation
- alert logic

### Then finish: 🤖
- AI urban assistant
- UI polishing
- evaluation
- documentation

---

## 13. Main Risks in Architecture

We should be aware of these from now:

- **Risk 1 — Too many modules too early**  
  **Solution:** build MVP first.

- **Risk 2 — No common data model**  
  **Solution:** standardize outputs now.

- **Risk 3 — Data scarcity in Egypt**  
  **Solution:** combine Egyptian data with benchmark/public datasets and frame clearly as prototype.

- **Risk 4 — Dashboard disconnected from AI**  
  **Solution:** all layers must go through unified backend and database.

- **Risk 5 — Team confusion**  
  **Solution:** assign each person a layer/module responsibility.

- **Risk 6 — AI assistant gives unsupported answers**  
  **Solution:** ground it in retrieval from digital twin outputs and verified stored context only.

- **Risk 7 — Emergency optimization depends on poor map quality**  
  **Solution:** keep routing graph clean and validate road network assumptions carefully.

---

## 14. Final Architecture Decision

A **layered modular digital twin architecture** using **PostgreSQL/PostGIS** for geospatial storage, **FastAPI** for backend services, **React + Mapbox** for visualization, and independent AI modules for urban prediction, detection, optimization, estimation, and natural-language interaction, all integrated through a unified digital twin state layer.
