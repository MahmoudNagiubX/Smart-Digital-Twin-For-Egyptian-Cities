# Nasr City Weather-Impact Emergency Mobility Module

## AI-Based Rainfall, Heat, Traffic, and Emergency Mobility Risk Assessment for Nasr City, Cairo

**Parent Project:** Egypt Smart City Digital Twin  
**Case Study Area:** Nasr City, Cairo, Egypt  
**Module Type:** First full production-ready digital twin module  
**Main Theme:** Weather-impact urban risk intelligence  
**Core Risks:** Sudden flooding after rainfall, urban heat island effects, traffic congestion, and emergency vehicle delays  

---

# 1. Project Idea

The **Nasr City Weather-Impact Emergency Mobility Module** is the first full module of the larger **Egypt Smart City Digital Twin** platform. The module creates an AI-powered geospatial intelligence system for Nasr City that can estimate how rainfall, heat, and traffic conditions affect urban safety and emergency mobility.

The main idea is simple but powerful:

```text
Rainfall happens
↓
Some streets and zones become flood-prone
↓
Traffic delay increases
↓
Emergency vehicles may be delayed
↓
The system recalculates safer routes and shows risk layers on a map
```

At the same time, the system analyzes heat island effects:

```text
Dense buildings + asphalt + low vegetation + high temperature
↓
Urban heat island risk increases
↓
Heat-risk zones are shown on the dashboard
```

This module does not only show data. It turns raw urban information into **decision-support intelligence** that can help planners, researchers, and emergency-response teams understand where risks are increasing and which roads/routes should be avoided.

---

# 2. Why Nasr City?

Nasr City is a strong first case study because it contains the urban conditions needed for a realistic smart-city risk module:

- dense residential and commercial activity
- major roads and intersections
- traffic congestion
- large built-up surfaces
- limited vegetation in many areas
- hospitals, schools, malls, and public facilities
- strong emergency-routing use case
- accessible satellite, weather, and OpenStreetMap data

Nasr City is better than using a small planned compound because it has real urban complexity. It is also better than trying to build the whole Greater Cairo model immediately, because it is large enough to be meaningful but small enough to finish as a graduation-project module.

---

# 3. Module Objectives

The module aims to:

1. Build a geospatial digital twin layer for Nasr City.
2. Collect open data for roads, weather, elevation, land cover, heat, hospitals, and population exposure.
3. Divide Nasr City into zones or grid cells.
4. Estimate flood risk after rainfall.
5. Detect urban heat island risk zones.
6. Estimate traffic delay caused by rainfall and flood-prone roads.
7. Optimize emergency routes under weather-impact conditions.
8. Expose all results through FastAPI endpoints.
9. Visualize the results in React + Mapbox.
10. Produce explainable outputs suitable for academic presentation and future scaling.

---

# 4. Scope of the First Full Module

This module combines four engines into one integrated system.

## 4.1 Flood Risk Engine

Predicts flood-prone zones and risky road segments after rainfall using:

- rainfall intensity
- accumulated rainfall
- elevation
- slope
- land-cover/built-up density
- road low points
- optional drainage assumptions

## 4.2 Urban Heat Risk Engine

Detects urban heat island zones using:

- Landsat surface temperature
- NDVI / vegetation index
- built-up density
- land-cover class
- population exposure
- weather temperature

## 4.3 Traffic Delay Engine

Estimates how weather and flood-prone roads increase traffic delay using:

- OpenStreetMap road network
- road type
- base speed
- travel time
- rainfall penalty
- flood-risk penalty
- congestion assumptions

## 4.4 Emergency Route Optimization Engine

Finds the safest route for emergency vehicles using:

- road graph
- travel time
- flood risk
- traffic delay risk
- hospital locations
- incident location
- blocked/risky road penalties

The goal is not only to find the shortest path. The system should find the **best emergency route under current risk conditions**.

---

# 5. Best Technical Approach

## 5.1 Recommended Development Strategy

Use a **hybrid rule-based + geospatial analytics approach first**, then upgrade to machine learning later.

This is the best approach because:

- Egyptian real-time traffic and flood datasets are limited.
- Rule-based models are easier to explain in reports.
- Geospatial risk scoring is realistic and accepted academically.
- The module can work even before collecting large labeled datasets.
- Later, machine learning can replace or improve individual scoring components.

## 5.2 MVP Methodology

The MVP should use:

- open data collection
- geospatial preprocessing
- grid/zone-based analysis
- normalized risk scoring
- road graph routing
- weighted route penalties
- dashboard visualization

## 5.3 Future Upgrade Methodology

After MVP completion, the module can be improved using:

- supervised flood classification if labeled flood events become available
- time-series traffic forecasting
- machine-learning regression for travel-time prediction
- real complaint data integration
- live rainfall alerts
- real-time emergency routing
- IoT sensor integration

---

# 6. Core System Architecture

The module follows the main digital twin architecture of the parent project.

```text
External Data Sources
Weather / Roads / Satellite / Elevation / Population / Hospitals
        ↓
Data Acquisition Layer
Collectors and download scripts
        ↓
Raw Data Storage
CSV / GeoJSON / GeoTIFF / PostGIS
        ↓
Preprocessing Layer
Cleaning, clipping, projection, raster/vector processing
        ↓
Risk Engines
Flood / Heat / Traffic / Emergency Routing
        ↓
Digital Twin Integration Layer
Unified zone and road risk state
        ↓
FastAPI Backend
APIs for map layers, risk scores, routing, summaries
        ↓
React + Mapbox Dashboard
Interactive map, risk cards, alerts, emergency route view
```

---

# 7. Dataset Plan

This section lists the best datasets and APIs to use.

## 7.1 Road Network Dataset

### Dataset Name
**OpenStreetMap Road Network**

### Access Method
- OSMnx Python library
- Overpass API
- OpenStreetMap export
- HOT Export Tool if needed

### Use in Project
- road network graph
- intersections
- route calculation
- hospitals and facilities
- road type extraction
- travel-time estimation

### Main Fields Needed
- road geometry
- road type
- length
- speed estimate
- travel time
- one-way direction
- intersection nodes

### Best Approach
Use OSMnx first:

```python
import osmnx as ox
G = ox.graph_from_place("Nasr City, Cairo, Egypt", network_type="drive")
G = ox.add_edge_speeds(G)
G = ox.add_edge_travel_times(G)
```

If place search fails, use a manual polygon or bounding box for Nasr City.

---

## 7.2 Weather Dataset

### Primary Dataset Name
**NASA POWER Hourly API**

### Secondary Dataset Name
**Open-Meteo Historical Weather API**

### Use in Project
- rainfall intensity
- accumulated rainfall
- temperature
- humidity
- wind speed
- weather-based risk scoring

### Variables Needed
- precipitation / rainfall
- temperature at 2m
- relative humidity
- wind speed
- apparent temperature if available

### Best Approach
Use Open-Meteo for easy historical weather extraction and NASA POWER as a scientific supporting source.

Example variables:

```text
temperature_2m
relative_humidity_2m
precipitation
rain
wind_speed_10m
apparent_temperature
```

---

## 7.3 Rainfall Dataset for Stronger Flood Analysis

### Dataset Name
**NASA GPM IMERG Precipitation**

### Use in Project
- satellite rainfall estimation
- extreme rainfall event analysis
- historical rainfall comparison
- rainfall accumulation maps

### Why Useful
IMERG is useful when ground rainfall stations are not available. It provides satellite precipitation estimates that can support flood-risk modeling.

### Best Approach
For MVP, use NASA POWER/Open-Meteo first. Use IMERG later if you want raster rainfall maps or stronger research-level rainfall analysis.

---

## 7.4 Elevation Dataset

### Primary Dataset Name
**Copernicus DEM GLO-30**

### Alternative Dataset Name
**NASA SRTM 30m DEM**

### Use in Project
- detect low elevation areas
- calculate slope
- identify potential water accumulation zones
- support flood-risk scoring

### Best Approach
Use Copernicus DEM GLO-30 if available easily. Use SRTM 30m as fallback because it is widely available in Google Earth Engine and many GIS tools.

### Derived Features
- elevation
- slope
- local depressions
- flow accumulation approximation
- distance to low-elevation road segments

---

## 7.5 Land Surface Temperature Dataset

### Dataset Name
**Landsat Collection 2 Level-2 Surface Temperature**

### Use in Project
- urban heat island mapping
- surface temperature extraction
- heat hotspot detection

### Best Approach
Use Google Earth Engine to extract cloud-filtered summer images over Nasr City. Then calculate average LST and compare each zone against the district mean.

### Derived Features
- land surface temperature in Celsius
- heat anomaly
- heat hotspot class
- zone-level average temperature

---

## 7.6 Vegetation Dataset

### Dataset Name
**Landsat NDVI or Sentinel-2 NDVI**

### Use in Project
- vegetation coverage
- heat mitigation indicator
- runoff reduction indicator

### Best Approach
Use Sentinel-2 for NDVI because it has higher spatial resolution than Landsat optical bands. Use Landsat for thermal surface temperature.

### NDVI Formula

```text
NDVI = (NIR - Red) / (NIR + Red)
```

### Interpretation

```text
Low NDVI  → less vegetation → higher heat risk and more runoff
High NDVI → more vegetation → lower heat risk and better cooling
```

---

## 7.7 Built-Up Density Dataset

### Dataset Name
**Global Human Settlement Layer — GHS-BUILT-S**

### Use in Project
- built-up intensity
- impervious surface proxy
- heat island risk
- flood runoff risk

### Best Approach
Aggregate built-up surface values into each Nasr City grid cell/zone.

### Derived Features
- built-up area percentage
- imperviousness proxy
- construction density
- urban density score

---

## 7.8 Land Cover Dataset

### Dataset Name
**ESA WorldCover 10m**

### Use in Project
- classify urban, vegetation, bare land, water, etc.
- support heat and flood risk scoring
- validate built-up and vegetation assumptions

### Best Approach
Use ESA WorldCover as a land-cover baseline and combine it with NDVI and GHSL.

---

## 7.9 Population Exposure Dataset

### Dataset Name
**WorldPop Gridded Population Data**

### Use in Project
- exposure analysis
- risk prioritization
- emergency planning
- vulnerability mapping

### Best Approach
Aggregate WorldPop population values into grid cells/zones.

### Derived Features
- population per zone
- population density
- exposed population under high flood/heat risk

---

## 7.10 Hospitals and Emergency Facilities Dataset

### Dataset Name
**OpenStreetMap Amenities**

### OSM Tags

```text
amenity=hospital
amenity=clinic
amenity=doctors
amenity=fire_station
amenity=police
```

### Use in Project
- emergency route start points
- service coverage analysis
- emergency accessibility score

### Best Approach
Extract facilities using OSMnx or Overpass API.

---

## 7.11 Optional Traffic Dataset

### Dataset Name
**OpenStreetMap Road Graph + Synthetic Traffic Penalty Model**

### Why Synthetic First?
Real-time traffic APIs are often paid, restricted, or hard to store legally. For the MVP, use road type, time of day, rainfall, and flood risk to estimate delay.

### Future Options
- Google Maps Distance Matrix API
- TomTom Traffic API
- HERE Traffic API
- manual travel-time samples
- simulated traffic scenarios

### MVP Traffic Features
- road type
- base speed
- free-flow travel time
- time-of-day factor
- rainfall factor
- flood factor
- congestion factor

---

# 8. Nasr City Boundary Strategy

## 8.1 Best Approach

Try OSMnx place search first:

```python
place = "Nasr City, Cairo, Egypt"
```

If it works, use the returned boundary.

## 8.2 Fallback Approach

If OSM boundary is incomplete or inaccurate, use a manually defined polygon around Nasr City.

Recommended workflow:

1. Open OpenStreetMap.
2. Draw approximate Nasr City boundary in geojson.io.
3. Export as `nasr_city_boundary.geojson`.
4. Use it as the official project boundary.
5. Store it in PostGIS.

## 8.3 Why Boundary Matters

The boundary is important because every dataset must be clipped to the same area:

- roads
- hospitals
- satellite images
- elevation raster
- heat raster
- population raster
- built-up raster
- risk zones

---

# 9. Spatial Unit Design

The module needs a standard unit of analysis.

## Option A — Administrative Zones

Use district/neighborhood boundaries if available.

### Pros
- easy to explain
- matches city planning language

### Cons
- hard to find accurate official small-zone data
- zones may be too large

## Option B — Regular Grid Cells

Divide Nasr City into equal grid cells such as:

```text
250m × 250m
or
500m × 500m
```

### Pros
- easy to generate
- works with all raster data
- good for satellite analysis
- consistent and objective

### Cons
- not official administrative units

## Recommended Choice

Use **500m × 500m grid cells for MVP**.

Later, add administrative or neighborhood-level aggregation.

---

# 10. Methodology Overview

The methodology has four main analytical tracks.

```text
Track 1: Flood Risk
Track 2: Heat Risk
Track 3: Traffic Delay Risk
Track 4: Emergency Route Risk
```

All tracks produce standardized outputs that are fused into a digital twin state.

---

# 11. Flood Risk Methodology

## 11.1 Input Features

| Feature | Source | Meaning |
|---|---|---|
| rainfall intensity | NASA POWER / Open-Meteo / IMERG | current rainfall severity |
| rainfall accumulation | weather API | total rain over recent hours |
| elevation | DEM | lower areas may flood more |
| slope | DEM | flat/low-slope areas may retain water |
| built-up density | GHSL | impervious surfaces increase runoff |
| vegetation | NDVI | vegetation reduces runoff |
| road type | OSM | some roads are more critical |
| proximity to low roads | OSM + DEM | risky road segments |

## 11.2 Feature Engineering

Create these features:

```text
rain_1h_mm
rain_3h_mm
rain_6h_mm
elevation_mean
slope_mean
builtup_ratio
ndvi_mean
low_elevation_score
impervious_score
road_density
```

## 11.3 MVP Scoring Formula

Use normalized values from 0 to 1.

```text
Flood Risk Score =
0.30 × rainfall_score +
0.20 × rainfall_accumulation_score +
0.20 × low_elevation_score +
0.15 × impervious_surface_score +
0.10 × low_slope_score +
0.05 × low_vegetation_score
```

## 11.4 Severity Levels

```text
0.00 – 0.33 = Low
0.34 – 0.66 = Medium
0.67 – 1.00 = High
```

## 11.5 Output Example

```json
{
  "zone_id": "NSR-GRID-024",
  "timestamp": "2026-04-24T14:00:00",
  "flood_risk_score": 0.78,
  "severity": "high",
  "main_reasons": [
    "High rainfall accumulation",
    "Low elevation",
    "High built-up density"
  ]
}
```

## 11.6 Future ML Upgrade

After collecting labeled flood events, upgrade to:

- Random Forest classifier
- XGBoost classifier
- LightGBM classifier
- spatial logistic regression

Target variable:

```text
flooded = 1 or 0
```

---

# 12. Urban Heat Island Methodology

## 12.1 Input Features

| Feature | Source | Meaning |
|---|---|---|
| land surface temperature | Landsat C2 ST | surface heat |
| NDVI | Sentinel-2 / Landsat | vegetation cooling |
| built-up density | GHSL | urban density |
| land cover | ESA WorldCover | surface class |
| air temperature | NASA POWER / Open-Meteo | weather context |
| population | WorldPop | exposed population |

## 12.2 Feature Engineering

```text
lst_celsius
lst_anomaly
ndvi_mean
builtup_ratio
vegetation_ratio
population_density
heat_exposure_score
```

## 12.3 LST Conversion

For Landsat Collection 2 Surface Temperature, convert scaled values to Kelvin and then Celsius.

```text
LST_K = DN × 0.00341802 + 149.0
LST_C = LST_K - 273.15
```

## 12.4 Heat Risk Formula

```text
Heat Risk Score =
0.35 × lst_anomaly_score +
0.20 × builtup_score +
0.20 × low_vegetation_score +
0.15 × population_exposure_score +
0.10 × apparent_temperature_score
```

## 12.5 Output Example

```json
{
  "zone_id": "NSR-GRID-011",
  "heat_risk_score": 0.81,
  "severity": "high",
  "main_reasons": [
    "High land surface temperature",
    "Low vegetation",
    "High built-up density"
  ]
}
```

## 12.6 Future ML Upgrade

Possible models:

- spatial regression
- Random Forest regression
- Gradient Boosting regression
- clustering for heat hotspot classification

---

# 13. Traffic Delay Methodology

## 13.1 Input Features

| Feature | Source | Meaning |
|---|---|---|
| road network | OSM | road graph |
| road type | OSM | primary/secondary/residential |
| road length | OSMnx | segment length |
| base speed | OSMnx estimate | free-flow speed |
| travel time | OSMnx estimate | base ETA |
| rainfall | weather API | weather impact |
| flood risk | flood engine | risky road/zone |
| time of day | system clock | rush-hour factor |

## 13.2 Base Travel Time

```text
base_travel_time = road_length / base_speed
```

## 13.3 Weather-Affected Travel Time

```text
weather_travel_time = base_travel_time × delay_factor
```

## 13.4 Delay Factor Formula

```text
delay_factor =
1.0 +
rain_penalty +
flood_penalty +
rush_hour_penalty +
road_type_penalty
```

## 13.5 Suggested Penalties

| Condition | Penalty |
|---|---:|
| light rain | +0.05 |
| moderate rain | +0.15 |
| heavy rain | +0.30 |
| road intersects medium flood zone | +0.25 |
| road intersects high flood zone | +0.60 |
| rush hour | +0.20 |
| narrow/residential road | +0.10 |

## 13.6 Output Example

```json
{
  "road_id": "edge_1023",
  "base_travel_time_sec": 45,
  "weather_travel_time_sec": 78,
  "delay_factor": 1.73,
  "severity": "high",
  "reason": "Heavy rain + high flood-risk zone"
}
```

## 13.7 Future ML Upgrade

If real traffic data becomes available, upgrade to:

- travel-time regression model
- LSTM traffic forecast
- temporal graph neural network
- XGBoost travel-time model

---

# 14. Emergency Route Optimization Methodology

## 14.1 Main Concept

Normal routing chooses the shortest or fastest path. Emergency routing should choose the route with the lowest **risk-adjusted travel cost**.

## 14.2 Graph Design

Represent roads as a graph:

```text
Nodes = intersections
Edges = road segments
Edge weight = risk-adjusted travel time
```

## 14.3 Route Cost Formula

```text
route_edge_cost =
base_travel_time +
flood_cost +
traffic_delay_cost +
critical_road_penalty
```

Alternative normalized version:

```text
route_edge_cost =
base_travel_time × (1 + flood_penalty + traffic_penalty + rain_penalty)
```

## 14.4 Algorithm

Use:

- Dijkstra for MVP
- A* for improved routing later
- Yen’s K-shortest paths for alternative routes

## 14.5 Emergency Route Output

```json
{
  "start": {
    "lat": 30.056,
    "lng": 31.335
  },
  "destination": {
    "lat": 30.070,
    "lng": 31.365
  },
  "normal_eta_minutes": 12,
  "weather_safe_eta_minutes": 17,
  "risk_level": "medium",
  "avoided_segments": ["edge_88", "edge_214"],
  "route_geometry": "LINESTRING(...)"
}
```

## 14.6 Emergency Accessibility Score

For each zone:

```text
Emergency Accessibility Risk =
normalized(weather_safe_eta - normal_eta) +
blocked_road_score +
flooded_access_score
```

---

# 15. Unified Weather-Impact Risk Score

Each grid cell/zone should have four sub-scores:

```text
flood_risk
heat_risk
traffic_delay_risk
emergency_delay_risk
```

## 15.1 Overall Score Formula

```text
Overall Weather Impact Score =
0.35 × Flood Risk +
0.25 × Emergency Delay Risk +
0.20 × Traffic Delay Risk +
0.20 × Heat Risk
```

## 15.2 Severity

```text
0.00 – 0.33 = Low
0.34 – 0.66 = Medium
0.67 – 1.00 = High
```

## 15.3 Standard Output Format

```json
{
  "module_name": "weather_impact_emergency_mobility",
  "zone_id": "NSR-GRID-024",
  "timestamp": "2026-04-24T15:00:00",
  "flood_risk": 0.78,
  "heat_risk": 0.63,
  "traffic_delay_risk": 0.71,
  "emergency_delay_risk": 0.82,
  "overall_weather_impact_score": 0.75,
  "severity": "high",
  "confidence": 0.74,
  "source": "weather + DEM + OSM + Landsat + GHSL",
  "details": {
    "rainfall_3h_mm": 22.4,
    "mean_lst_celsius": 42.1,
    "builtup_ratio": 0.87,
    "mean_ndvi": 0.12
  }
}
```

---

# 16. Data Processing Pipeline

## 16.1 Pipeline A — Boundary and Grid

Steps:

1. Download or draw Nasr City boundary.
2. Store boundary as GeoJSON.
3. Reproject boundary to metric CRS.
4. Generate 500m grid cells.
5. Assign unique zone IDs.
6. Save as GeoJSON and PostGIS table.

Outputs:

```text
nasr_city_boundary.geojson
nasr_city_grid_500m.geojson
```

---

## 16.2 Pipeline B — Roads and Facilities

Steps:

1. Download drivable road network using OSMnx.
2. Add edge speeds.
3. Add edge travel times.
4. Extract hospitals, clinics, and emergency facilities.
5. Clip all features to Nasr City boundary.
6. Save roads and facilities.

Outputs:

```text
nasr_city_roads.geojson
nasr_city_nodes.geojson
nasr_city_hospitals.geojson
```

---

## 16.3 Pipeline C — Weather

Steps:

1. Request hourly weather data.
2. Store raw API response.
3. Convert timestamps to Cairo local time.
4. Calculate rolling rainfall accumulation.
5. Calculate rainfall intensity class.
6. Save processed weather table.

Outputs:

```text
weather_hourly_raw.csv
weather_hourly_processed.csv
```

---

## 16.4 Pipeline D — Elevation

Steps:

1. Download DEM.
2. Clip DEM to Nasr City.
3. Calculate slope.
4. Aggregate elevation and slope per grid cell.
5. Join results to grid table.

Outputs:

```text
nasr_city_dem.tif
nasr_city_slope.tif
grid_elevation_features.csv
```

---

## 16.5 Pipeline E — Heat and Vegetation

Steps:

1. Download Landsat Surface Temperature scenes.
2. Cloud mask images.
3. Convert surface temperature to Celsius.
4. Download Sentinel-2 or Landsat reflectance.
5. Calculate NDVI.
6. Aggregate LST and NDVI per grid cell.
7. Detect heat anomalies.

Outputs:

```text
lst_celsius.tif
ndvi.tif
grid_heat_features.csv
```

---

## 16.6 Pipeline F — Built-Up and Population

Steps:

1. Download GHSL built-up raster.
2. Download WorldPop population raster.
3. Clip both to Nasr City.
4. Aggregate values per grid cell.
5. Save features.

Outputs:

```text
grid_builtup_features.csv
grid_population_features.csv
```

---

## 16.7 Pipeline G — Risk Engine Processing

Steps:

1. Merge grid features.
2. Normalize all numeric features.
3. Run flood risk scoring.
4. Run heat risk scoring.
5. Intersect roads with flood-risk zones.
6. Calculate road delay penalties.
7. Run emergency route engine.
8. Store all outputs.

Outputs:

```text
grid_risk_scores.geojson
road_weather_impacts.geojson
emergency_routes.geojson
```

---

# 17. Database Design

Use PostgreSQL + PostGIS.

## 17.1 Core Tables

### `districts`

```text
id
name
city
country
geometry
created_at
```

### `zones`

```text
id
district_id
zone_code
geometry
area_m2
created_at
```

### `road_segments`

```text
id
osm_id
name
road_type
length_m
base_speed_kmh
base_travel_time_sec
geometry
```

### `emergency_facilities`

```text
id
name
facility_type
source
geometry
```

### `weather_records`

```text
id
timestamp
latitude
longitude
temperature_2m
relative_humidity_2m
precipitation_mm
rain_mm
wind_speed_10m
source
```

### `zone_environment_features`

```text
id
zone_id
date
mean_elevation
mean_slope
mean_lst_celsius
mean_ndvi
builtup_ratio
population_density
```

### `zone_risk_scores`

```text
id
zone_id
timestamp
flood_risk
heat_risk
traffic_delay_risk
emergency_delay_risk
overall_weather_impact_score
severity
confidence
explanation_json
```

### `road_weather_impacts`

```text
id
road_segment_id
timestamp
rain_penalty
flood_penalty
traffic_penalty
delay_factor
weather_travel_time_sec
severity
geometry
```

### `emergency_routes`

```text
id
timestamp
start_lat
start_lng
end_lat
end_lng
normal_eta_minutes
weather_safe_eta_minutes
risk_level
avoided_segments_json
route_geometry
```

---

# 18. API Design

Use FastAPI.

## 18.1 Core Endpoints

### Health Check

```http
GET /health
```

Returns backend status.

---

### Get Nasr City Boundary

```http
GET /districts/nasr-city/boundary
```

Returns GeoJSON boundary.

---

### Get Grid Zones

```http
GET /districts/nasr-city/zones
```

Returns 500m grid cells.

---

### Get Weather Impact Summary

```http
GET /weather-impact/summary
```

Returns cards for dashboard.

Example:

```json
{
  "district": "Nasr City",
  "overall_status": "high risk",
  "high_flood_zones": 8,
  "high_heat_zones": 13,
  "affected_roads": 24,
  "average_emergency_delay_minutes": 5.7
}
```

---

### Get Zone Risk Layer

```http
GET /weather-impact/zones
```

Returns GeoJSON with flood, heat, traffic, and overall scores.

---

### Get Road Delay Layer

```http
GET /weather-impact/roads
```

Returns roads with delay factor and severity.

---

### Calculate Emergency Route

```http
POST /weather-impact/emergency-route
```

Request:

```json
{
  "start_lat": 30.056,
  "start_lng": 31.335,
  "end_lat": 30.070,
  "end_lng": 31.365
}
```

Response:

```json
{
  "normal_eta_minutes": 12,
  "weather_safe_eta_minutes": 17,
  "risk_level": "medium",
  "route_geojson": {},
  "avoided_segments": []
}
```

---

# 19. Frontend Dashboard Design

Use React + Mapbox.

## 19.1 Main Dashboard Sections

### Map View

Shows:

- Nasr City boundary
- grid cells
- roads
- hospitals
- flood-risk layer
- heat-risk layer
- traffic-delay layer
- emergency route layer

### Layer Control Panel

Toggles:

```text
[ ] Flood Risk
[ ] Heat Risk
[ ] Traffic Delay
[ ] Emergency Route
[ ] Hospitals
[ ] Grid Zones
```

### Summary Cards

Cards:

```text
Overall Risk
High Flood Zones
High Heat Zones
Affected Roads
Emergency Delay
```

### Zone Popup

When clicking a grid cell:

```text
Zone ID: NSR-GRID-024
Flood Risk: High
Heat Risk: Medium
Traffic Delay: High
Emergency Delay: High
Main Reason: Heavy rain + low elevation + dense built-up area
```

### Emergency Route Panel

Inputs:

- start location
- destination location
- selected hospital
- emergency type

Outputs:

- normal ETA
- safe ETA
- route risk level
- avoided roads
- map route line

---

# 20. Recommended Repository Structure

```text
Egypt-Smart-City-Digital-Twin/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   └── routes/
│   │   │       ├── health.py
│   │   │       ├── districts.py
│   │   │       └── weather_impact.py
│   │   │
│   │   ├── services/
│   │   │   ├── district_service.py
│   │   │   └── weather_impact_service.py
│   │   │
│   │   ├── ml/
│   │   │   └── weather_impact/
│   │   │       ├── flood_risk_engine.py
│   │   │       ├── heat_risk_engine.py
│   │   │       ├── traffic_delay_engine.py
│   │   │       ├── emergency_route_engine.py
│   │   │       └── scoring.py
│   │   │
│   │   ├── preprocessing/
│   │   │   ├── boundary_pipeline.py
│   │   │   ├── grid_pipeline.py
│   │   │   ├── roads_pipeline.py
│   │   │   ├── weather_pipeline.py
│   │   │   ├── elevation_pipeline.py
│   │   │   ├── heat_pipeline.py
│   │   │   └── exposure_pipeline.py
│   │   │
│   │   ├── db/
│   │   │   ├── session.py
│   │   │   ├── models/
│   │   │   └── repositories/
│   │   │
│   │   ├── schemas/
│   │   │   ├── district.py
│   │   │   └── weather_impact.py
│   │   │
│   │   ├── core/
│   │   │   ├── config.py
│   │   │   └── logging.py
│   │   │
│   │   └── utils/
│   │       ├── geo.py
│   │       ├── normalization.py
│   │       └── files.py
│   │
│   ├── data/
│   │   └── nasr_city/
│   │       ├── raw/
│   │       ├── processed/
│   │       ├── outputs/
│   │       └── metadata/
│   │
│   ├── requirements.txt
│   └── main.py
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── map/
│   │   ├── services/
│   │   └── types/
│   └── package.json
│
├── notebooks/
│   ├── 01_boundary_exploration.ipynb
│   ├── 02_weather_exploration.ipynb
│   ├── 03_heat_analysis.ipynb
│   └── 04_route_testing.ipynb
│
├── docs/
│   ├── methodology.md
│   ├── datasets.md
│   ├── architecture.md
│   └── roadmap.md
│
└── README.md
```

---

# 21. Recommended Python Libraries

## Geospatial

```text
geopandas
shapely
rasterio
rioxarray
pyproj
osmnx
networkx
contextily
folium
```

## Data Processing

```text
pandas
numpy
scikit-learn
xarray
requests
```

## Backend

```text
fastapi
uvicorn
pydantic
sqlalchemy
geoalchemy2
psycopg2-binary
alembic
```

## Visualization / Testing

```text
matplotlib
plotly
pytest
```

## Frontend

```text
react
mapbox-gl
axios
recharts
```

---

# 22. Detailed Roadmap

## Phase 0 — Project Setup and Documentation

### Goal
Prepare the repo and make the module scope clear.

### Small Steps

1. Update project README to state that the first full module is Nasr City Weather-Impact Emergency Mobility.
2. Create `docs/` folder.
3. Add this methodology file.
4. Add dataset list and references.
5. Create backend and frontend folders if not already available.
6. Create `.env.example`.
7. Create `requirements.txt`.
8. Create GitHub project board.
9. Create issues for each phase.

### Deliverables

- updated README
- methodology document
- dataset document
- roadmap document
- clean repo structure

### Success Criteria

The team understands exactly what will be built first.

---

## Phase 1 — Nasr City Boundary and Grid

### Goal
Create the spatial foundation of the module.

### Small Steps

1. Try downloading Nasr City boundary using OSMnx.
2. If not accurate, draw boundary manually in geojson.io.
3. Save boundary as `nasr_city_boundary.geojson`.
4. Reproject boundary to a metric CRS.
5. Generate 500m × 500m grid.
6. Assign zone IDs like `NSR-GRID-001`.
7. Save grid as GeoJSON.
8. Visualize boundary and grid in notebook.
9. Store boundary and grid in PostGIS.

### Deliverables

- `nasr_city_boundary.geojson`
- `nasr_city_grid_500m.geojson`
- boundary/grid notebook
- PostGIS `districts` and `zones` tables

### Success Criteria

The dashboard can display Nasr City boundary and grid zones.

---

## Phase 2 — Road Network and Emergency Facilities

### Goal
Build the road graph and emergency facility layer.

### Small Steps

1. Download drive road network using OSMnx.
2. Convert graph to nodes and edges GeoDataFrames.
3. Add edge speeds.
4. Add edge travel times.
5. Extract hospitals, clinics, and emergency facilities from OSM.
6. Clip roads and facilities to boundary.
7. Save roads, nodes, and facilities.
8. Create road segment IDs.
9. Store roads and facilities in PostGIS.
10. Test basic shortest route between two points.

### Deliverables

- `nasr_city_roads.geojson`
- `nasr_city_nodes.geojson`
- `nasr_city_hospitals.geojson`
- basic route test notebook

### Success Criteria

The system can calculate a normal route between two points inside Nasr City.

---

## Phase 3 — Weather Data Collector

### Goal
Collect rainfall and temperature data for the study area.

### Small Steps

1. Choose central Nasr City coordinate.
2. Build Open-Meteo historical weather collector.
3. Build NASA POWER hourly collector.
4. Save raw weather data.
5. Clean timestamps.
6. Convert units if needed.
7. Calculate rainfall accumulation windows.
8. Create rainfall intensity classes.
9. Store processed weather records in database.

### Deliverables

- `weather_collector.py`
- `weather_hourly_raw.csv`
- `weather_hourly_processed.csv`
- `weather_records` database table

### Success Criteria

The backend can retrieve weather records for a selected date/time.

---

## Phase 4 — Elevation and Flood Features

### Goal
Prepare terrain features for flood risk scoring.

### Small Steps

1. Download DEM for Nasr City.
2. Clip DEM to boundary.
3. Calculate slope raster.
4. Aggregate mean elevation per grid cell.
5. Aggregate mean slope per grid cell.
6. Normalize elevation and slope features.
7. Identify low-elevation cells.
8. Join terrain features to grid table.

### Deliverables

- `nasr_city_dem.tif`
- `nasr_city_slope.tif`
- `grid_elevation_features.csv`
- terrain feature notebook

### Success Criteria

Each grid cell has elevation and slope attributes.

---

## Phase 5 — Flood Risk Engine MVP

### Goal
Create the first working flood-risk scoring model.

### Small Steps

1. Load weather features.
2. Load grid terrain features.
3. Load built-up/NDVI placeholders if not ready.
4. Normalize all features.
5. Implement scoring formula.
6. Generate flood risk per grid cell.
7. Classify severity.
8. Export GeoJSON risk layer.
9. Create explanation field for each zone.
10. Expose API endpoint.

### Deliverables

- `flood_risk_engine.py`
- `grid_flood_risk.geojson`
- `/weather-impact/flood` endpoint

### Success Criteria

The map can show low/medium/high flood risk zones.

---

## Phase 6 — Heat and Vegetation Features

### Goal
Prepare data for urban heat island analysis.

### Small Steps

1. Select summer date range.
2. Use Landsat Surface Temperature for LST.
3. Cloud mask scenes.
4. Convert LST to Celsius.
5. Use Sentinel-2 or Landsat for NDVI.
6. Calculate NDVI.
7. Aggregate LST and NDVI per grid cell.
8. Calculate heat anomaly.
9. Join heat features to grid table.

### Deliverables

- `lst_celsius.tif`
- `ndvi.tif`
- `grid_heat_features.csv`
- heat processing notebook

### Success Criteria

Each grid cell has surface temperature, NDVI, and heat anomaly values.

---

## Phase 7 — Heat Risk Engine MVP

### Goal
Create the urban heat island scoring engine.

### Small Steps

1. Load LST features.
2. Load NDVI features.
3. Load built-up features.
4. Load population features if available.
5. Normalize features.
6. Implement heat risk formula.
7. Generate heat-risk layer.
8. Classify severity.
9. Export GeoJSON.
10. Expose API endpoint.

### Deliverables

- `heat_risk_engine.py`
- `grid_heat_risk.geojson`
- `/weather-impact/heat` endpoint

### Success Criteria

The dashboard can display heat island risk zones.

---

## Phase 8 — Built-Up and Population Exposure

### Goal
Add urban exposure context.

### Small Steps

1. Download GHSL built-up data.
2. Download WorldPop population raster.
3. Clip both to boundary.
4. Aggregate built-up density per grid cell.
5. Aggregate population per grid cell.
6. Calculate population density.
7. Update flood and heat scoring features.
8. Store exposure features.

### Deliverables

- `grid_builtup_features.csv`
- `grid_population_features.csv`
- updated risk score outputs

### Success Criteria

Risk layers become exposure-aware, not only environmental.

---

## Phase 9 — Traffic Delay Engine MVP

### Goal
Estimate road delay caused by rain and flood-prone zones.

### Small Steps

1. Load OSM road segments.
2. Load base travel time.
3. Intersect roads with flood-risk grid cells.
4. Assign flood penalty to roads.
5. Assign rainfall penalty from weather data.
6. Add rush-hour penalty.
7. Calculate delay factor.
8. Calculate weather travel time.
9. Export road impact layer.
10. Expose API endpoint.

### Deliverables

- `traffic_delay_engine.py`
- `road_weather_impacts.geojson`
- `/weather-impact/roads` endpoint

### Success Criteria

The dashboard can show roads affected by rain/flood risk.

---

## Phase 10 — Emergency Route Engine MVP

### Goal
Calculate safer emergency routes under weather impact.

### Small Steps

1. Load road graph.
2. Add base travel-time weight.
3. Add flood and traffic penalties to graph edges.
4. Build normal route function.
5. Build weather-safe route function.
6. Compare normal ETA and safe ETA.
7. Identify avoided risky segments.
8. Return route GeoJSON.
9. Expose POST route endpoint.
10. Test with hospitals and random incidents.

### Deliverables

- `emergency_route_engine.py`
- `/weather-impact/emergency-route` endpoint
- route comparison output

### Success Criteria

The system can show a safer route that avoids high-risk roads.

---

## Phase 11 — Digital Twin Integration

### Goal
Merge all outputs into one unified district state.

### Small Steps

1. Create `twin_service.py`.
2. Load latest flood scores.
3. Load latest heat scores.
4. Load latest road impacts.
5. Load latest emergency accessibility scores.
6. Calculate overall weather-impact score.
7. Store latest digital twin snapshot.
8. Create summary endpoint.
9. Create timeline endpoint.
10. Add explanations for each zone.

### Deliverables

- `twin_service.py`
- `zone_risk_scores` table
- `/weather-impact/summary` endpoint
- `/digital-twin/state` endpoint

### Success Criteria

The backend has one unified source of truth for the module.

---

## Phase 12 — Frontend Map Dashboard

### Goal
Build the visible product.

### Small Steps

1. Create Mapbox map page.
2. Load Nasr City boundary.
3. Load grid layer.
4. Add layer toggles.
5. Add flood-risk color layer.
6. Add heat-risk color layer.
7. Add road-delay layer.
8. Add hospital markers.
9. Add emergency route line.
10. Add popups.
11. Add risk summary cards.
12. Add emergency route input panel.

### Deliverables

- interactive dashboard page
- risk layer toggles
- summary cards
- emergency routing UI

### Success Criteria

A user can visually understand flood, heat, traffic, and route risk.

---

## Phase 13 — Evaluation and Validation

### Goal
Prove the module works logically and academically.

### Small Steps

1. Validate road extraction visually.
2. Compare weather values against known seasonal expectations.
3. Validate DEM and slope outputs.
4. Validate heat hotspots against satellite imagery.
5. Test flood model under no-rain, light-rain, and heavy-rain scenarios.
6. Test route changes under normal and high-risk conditions.
7. Measure route ETA increase.
8. Measure number of avoided high-risk roads.
9. Create evaluation plots.
10. Document limitations clearly.

### Evaluation Metrics

Flood/Heat scoring:

```text
risk map plausibility
hotspot consistency
feature contribution explanation
```

Traffic/routing:

```text
normal ETA vs weather-safe ETA
number of avoided risky roads
route cost reduction
emergency accessibility change
```

Dashboard:

```text
API response time
map layer loading time
UI clarity
```

### Deliverables

- evaluation notebook
- screenshots
- metrics table
- limitations section

### Success Criteria

The project can be defended in front of doctors with clear evidence.

---

## Phase 14 — Documentation and Final Presentation

### Goal
Prepare the project for GitHub, report, and presentation.

### Small Steps

1. Update README.
2. Add system architecture diagram.
3. Add methodology diagram.
4. Add dataset table.
5. Add screenshots.
6. Add API documentation.
7. Add installation guide.
8. Add usage guide.
9. Add limitations and future work.
10. Add final presentation slides.

### Deliverables

- polished README
- final documentation
- presentation slides
- demo video script
- final report methodology section

### Success Criteria

The module looks like a complete professional smart-city project.

---

# 23. Best Implementation Order

Do not start with AI models first. Start with the geospatial foundation.

Recommended order:

```text
1. Boundary
2. Grid
3. Roads
4. Weather
5. Elevation
6. Flood scoring
7. Heat data
8. Heat scoring
9. Road delay
10. Emergency routing
11. Backend APIs
12. Dashboard
13. Evaluation
14. Documentation
```

This order prevents chaos and keeps the project buildable.

---

# 24. Team Work Division

If the project has a team, divide work like this:

## Member 1 — Geospatial and Data Engineering

Responsible for:

- boundary
- grid
- roads
- DEM
- raster/vector processing
- PostGIS storage

## Member 2 — Weather, Flood, and Heat Models

Responsible for:

- weather collectors
- flood scoring
- heat analysis
- feature normalization
- risk logic

## Member 3 — Emergency Routing and Backend

Responsible for:

- road graph
- route optimization
- FastAPI endpoints
- service layer
- database integration

## Member 4 — Frontend and Documentation

Responsible for:

- React dashboard
- Mapbox layers
- UI cards
- screenshots
- README/docs/presentation

---

# 25. Main Risks and Solutions

## Risk 1 — Nasr City boundary is not clean

### Solution
Use manual GeoJSON boundary and document that it is an approximate prototype boundary.

## Risk 2 — No real traffic data

### Solution
Use explainable synthetic traffic penalties based on road type, time, rain, and flood risk. Mention that real traffic API integration is future work.

## Risk 3 — No labeled flood events

### Solution
Use geospatial risk scoring first. Later upgrade to supervised ML when labeled flood records are available.

## Risk 4 — Satellite processing is complex

### Solution
Use Google Earth Engine for extraction and export processed rasters/CSV features.

## Risk 5 — Too many datasets slow the project

### Solution
Use minimum required datasets first:

```text
OSM roads + Open-Meteo/NASA weather + DEM + Landsat LST
```

Then add GHSL, NDVI, WorldPop, and ESA WorldCover.

## Risk 6 — Dashboard becomes disconnected from model outputs

### Solution
Force every engine to output standardized GeoJSON and database tables.

---

# 26. MVP Dataset Priority

## Must-Have Datasets

1. OpenStreetMap roads
2. OpenStreetMap hospitals
3. Open-Meteo or NASA POWER weather
4. DEM elevation
5. Landsat surface temperature

## Should-Have Datasets

6. Sentinel-2 NDVI
7. GHSL built-up surface
8. WorldPop population
9. ESA WorldCover land cover

## Nice-to-Have Datasets

10. NASA GPM IMERG precipitation
11. real traffic API data
12. drainage network data
13. official incident/flood reports
14. citizen complaints

---

# 27. Final MVP Definition

The first version is complete when the system can:

1. Display Nasr City boundary and grid.
2. Display road network and hospitals.
3. Collect weather data.
4. Estimate flood risk by zone.
5. Estimate heat risk by zone.
6. Estimate road delay due to rainfall/flood risk.
7. Calculate normal vs weather-safe emergency route.
8. Display all outputs on a dashboard.
9. Provide API endpoints for every layer.
10. Explain why each zone or road is risky.

---

# 28. Future Expansion

After this module is complete, the system can expand to:

- road damage detection using YOLO
- Arabic complaint analysis using AraBERT/CAMeLBERT
- garbage monitoring
- construction change detection
- infrastructure stress prediction
- district risk score
- AI urban assistant
- multi-district comparison
- live IoT sensor integration
- real-time emergency operations dashboard

---

# 29. References and Dataset Links

## Project Repository

- GitHub Repository: https://github.com/MahmoudNagiubX/Egypt-Smart-City-Digital-Twin

## Road and Routing Data

- OpenStreetMap: https://www.openstreetmap.org/
- OSMnx Documentation: https://osmnx.readthedocs.io/
- HOT Export Tool: https://export.hotosm.org/

## Weather and Rainfall Data

- NASA POWER Hourly API: https://power.larc.nasa.gov/docs/services/api/temporal/hourly/
- Open-Meteo Historical Weather API: https://open-meteo.com/en/docs/historical-weather-api
- NASA GPM IMERG: https://gpm.nasa.gov/data/imerg

## Elevation Data

- Copernicus DEM: https://dataspace.copernicus.eu/explore-data/data-collections/copernicus-contributing-missions/collections-description/COP-DEM
- NASA SRTM 30m in Google Earth Engine: https://developers.google.com/earth-engine/datasets/catalog/USGS_SRTMGL1_003

## Heat and Satellite Data

- Landsat Collection 2 Surface Temperature: https://www.usgs.gov/landsat-missions/landsat-collection-2-surface-temperature
- Sentinel-2: https://sentinels.copernicus.eu/web/sentinel/missions/sentinel-2

## Land Cover and Built-Up Data

- ESA WorldCover: https://esa-worldcover.org/en
- GHSL Built-Up Surface: https://developers.google.com/earth-engine/datasets/catalog/JRC_GHSL_P2023A_GHS_BUILT_S

## Population Data

- WorldPop: https://www.worldpop.org/

---

# 30. Final Project Statement

The **Nasr City Weather-Impact Emergency Mobility Module** is the first complete module of the Egypt Smart City Digital Twin platform. It uses open geospatial, weather, satellite, and road-network data to estimate how rainfall and heat affect urban risk and emergency mobility. The module combines flood-risk scoring, urban heat island detection, weather-based traffic delay estimation, and emergency route optimization into one unified digital twin layer. The final output is an interactive dashboard that helps users understand risky zones, affected roads, and safer emergency routes under changing environmental conditions.

This module is the best first step because it is realistic, data-accessible, visually impressive, academically strong, and directly connected to real urban problems in Cairo.
