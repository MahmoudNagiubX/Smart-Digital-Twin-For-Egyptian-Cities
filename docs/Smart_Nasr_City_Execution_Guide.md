# Nasr City Weather-Impact Emergency Mobility Module — Execution Guide

## Version
**v1.0 — Detailed build guide for coding the first complete module**

## Project Case Study
**Nasr City, Cairo, Egypt**

## Module Name
**Weather-Impact Emergency Mobility and Urban Risk Module**

---

# 1. What This File Is

This file is the practical execution guide for building the first full module of the **Egypt Smart City Digital Twin** project.

The previous methodology file explains the project idea, architecture, datasets, and roadmap. This file explains **how to start coding directly**.

It includes:

- required tools
- setup commands
- folder structure
- backend files
- database setup
- dataset collection scripts
- road network extraction
- weather data collection
- zone/grid generation
- flood risk scoring
- heat risk scoring
- traffic delay scoring
- emergency route optimization
- FastAPI endpoints
- expected outputs
- troubleshooting
- coding roadmap

By following this file, you should be able to create the first runnable version of the module.

---

# 2. Final Goal of This Module

The module will simulate and analyze how weather affects Nasr City.

It will answer:

- Which areas become risky after rainfall?
- Which roads may experience delay because of rain or flood risk?
- Which areas are heat hotspots?
- Which emergency route is safest for an ambulance during rain or flooding?
- How can the dashboard display flood, heat, traffic, and emergency risk together?

The final MVP output should be a backend API that returns geospatial risk data for Nasr City and emergency route recommendations.

---

# 3. Core Project Story

```text
Rainfall happens
↓
Some roads/zones become flood-prone
↓
Traffic delay increases
↓
Emergency vehicles may be delayed
↓
The system recommends safer routes
↓
The dashboard shows flood risk, heat risk, traffic risk, and emergency route risk
```

Also:

```text
Dense buildings + asphalt + low vegetation + high temperature
↓
Urban heat island risk increases
↓
The dashboard highlights heat-risk zones
```

---

# 4. What We Will Build First

The first implementation will be a **rule-based + geospatial MVP**.

We will not start with deep learning. The first version must be simple, explainable, and working.

## MVP Engines

| Engine | Purpose | First Approach |
|---|---|---|
| Road Network Engine | Download Nasr City road network | OpenStreetMap + OSMnx |
| Weather Collector | Collect rainfall/temp/humidity | Open-Meteo or NASA POWER |
| Zone Generator | Divide Nasr City into analysis zones | GeoPandas grid |
| Flood Risk Engine | Estimate flood-prone areas | rainfall + road class + low-area proxy |
| Heat Risk Engine | Estimate heat-risk zones | temperature + built-up proxy + vegetation later |
| Traffic Delay Engine | Estimate road delay under rainfall | base travel time + rainfall/flood penalty |
| Emergency Route Engine | Find safest route | NetworkX/OSMnx shortest path with risk weight |
| FastAPI Layer | Serve results to frontend | REST API |

---

# 5. Important MVP Decision

For v1, we will use:

```text
Rule-based scoring first
ML later
```

Why?

1. It is easier to build quickly.
2. It is easier to explain academically.
3. It does not require a perfect Egyptian traffic/flood dataset.
4. It creates useful outputs immediately.
5. Later, each scoring formula can be replaced with trained ML models.

---

# 6. Dataset Plan

## 6.1 Required Datasets for MVP

| Dataset | Name | Source | Used For | Required in v1? |
|---|---|---|---|---|
| Roads | OpenStreetMap road network | OSMnx / OpenStreetMap | traffic + routing | Yes |
| Weather | Open-Meteo Historical Weather API | Open-Meteo | rainfall/temp/humidity/wind | Yes |
| Weather Alternative | NASA POWER Hourly API | NASA POWER | rainfall/temp/weather validation | Optional |
| Elevation | SRTM 30m DEM | NASA / USGS / Google Earth Engine | flood risk | Optional in v1, recommended in v2 |
| Surface Temperature | Landsat Collection 2 Surface Temperature | USGS / GEE | urban heat island | Optional in v1, recommended in v2 |
| Land Cover | ESA WorldCover 10m | ESA / GEE | vegetation/buildings proxy | Optional in v2 |
| Built-Up Density | GHSL Built-Up Surface | JRC / GEE | heat and runoff proxy | Optional in v2 |
| Population | WorldPop Egypt 100m | WorldPop | vulnerable exposure | Optional in v2 |
| Hospitals | OSM amenities | OpenStreetMap | emergency start points | Yes, if available |

## 6.2 MVP Data Priority

Build in this order:

1. OpenStreetMap roads
2. Weather history
3. Simple analysis zones
4. Flood scoring
5. Traffic delay scoring
6. Emergency route scoring
7. Heat scoring
8. API endpoints
9. Dashboard layers
10. Satellite/raster upgrades

---

# 7. Recommended Tech Stack

## Backend

- Python 3.10 or 3.11
- FastAPI
- Uvicorn
- Pydantic
- Pandas
- GeoPandas
- Shapely
- OSMnx
- NetworkX
- SQLAlchemy
- PostgreSQL
- PostGIS

## Geospatial / Data

- OpenStreetMap
- OSMnx
- GeoPandas
- PostGIS
- GeoJSON
- GeoPackage
- GraphML

## Frontend Later

- React
- Vite
- Mapbox GL JS or MapLibre GL JS
- Axios
- Recharts

## Database

- PostgreSQL 15+
- PostGIS extension

## Development Tools

- VS Code
- Git
- Docker Desktop
- Postman or Bruno
- Python virtual environment or Conda

---

# 8. Repository Structure

Inside your project repo, create this structure:

```text
Egypt-Smart-City-Digital-Twin/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   ├── __init__.py
│   │   │   └── routes/
│   │   │       ├── __init__.py
│   │   │       └── weather_impact.py
│   │   │
│   │   ├── core/
│   │   │   ├── __init__.py
│   │   │   └── config.py
│   │   │
│   │   ├── db/
│   │   │   ├── __init__.py
│   │   │   ├── session.py
│   │   │   └── init.sql
│   │   │
│   │   ├── ml/
│   │   │   ├── __init__.py
│   │   │   └── weather_impact/
│   │   │       ├── __init__.py
│   │   │       ├── road_network_engine.py
│   │   │       ├── weather_collector.py
│   │   │       ├── zone_generator.py
│   │   │       ├── flood_risk_engine.py
│   │   │       ├── heat_risk_engine.py
│   │   │       ├── traffic_delay_engine.py
│   │   │       └── emergency_route_engine.py
│   │   │
│   │   ├── schemas/
│   │   │   ├── __init__.py
│   │   │   └── weather_impact.py
│   │   │
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   └── weather_impact_service.py
│   │   │
│   │   ├── scripts/
│   │   │   ├── 01_download_nasr_city_roads.py
│   │   │   ├── 02_collect_weather_data.py
│   │   │   ├── 03_generate_zones.py
│   │   │   ├── 04_run_risk_pipeline.py
│   │   │   └── 05_test_emergency_route.py
│   │   │
│   │   ├── data/
│   │   │   └── nasr_city/
│   │   │       ├── raw/
│   │   │       ├── processed/
│   │   │       ├── outputs/
│   │   │       └── maps/
│   │   │
│   │   └── main.py
│   │
│   ├── requirements.txt
│   ├── .env.example
│   └── Dockerfile
│
├── frontend/
│   └── README.md
│
├── docs/
│   ├── Nasr_City_Weather_Impact_Emergency_Mobility_Module.md
│   └── Nasr_City_Execution_Guide.md
│
├── docker-compose.yml
├── .gitignore
└── README.md
```

---

# 9. Phase 0 — Project Setup

## 9.1 Clone the Repository

```bash
git clone https://github.com/MahmoudNagiubX/Egypt-Smart-City-Digital-Twin.git
cd Egypt-Smart-City-Digital-Twin
```

## 9.2 Create a New Branch

```bash
git checkout -b feature/nasr-city-weather-impact-module
```

## 9.3 Create Folders

### Windows PowerShell

```powershell
mkdir backend
mkdir backend\app
mkdir backend\app\api
mkdir backend\app\api\routes
mkdir backend\app\core
mkdir backend\app\db
mkdir backend\app\ml
mkdir backend\app\ml\weather_impact
mkdir backend\app\schemas
mkdir backend\app\services
mkdir backend\app\scripts
mkdir backend\app\data
mkdir backend\app\data\nasr_city
mkdir backend\app\data\nasr_city\raw
mkdir backend\app\data\nasr_city\processed
mkdir backend\app\data\nasr_city\outputs
mkdir backend\app\data\nasr_city\maps
mkdir docs
```

### macOS / Linux / Git Bash

```bash
mkdir -p backend/app/api/routes
mkdir -p backend/app/core
mkdir -p backend/app/db
mkdir -p backend/app/ml/weather_impact
mkdir -p backend/app/schemas
mkdir -p backend/app/services
mkdir -p backend/app/scripts
mkdir -p backend/app/data/nasr_city/raw
mkdir -p backend/app/data/nasr_city/processed
mkdir -p backend/app/data/nasr_city/outputs
mkdir -p backend/app/data/nasr_city/maps
mkdir -p docs
```

## 9.4 Create Python Package Files

Create empty `__init__.py` files:

```bash
touch backend/app/__init__.py
touch backend/app/api/__init__.py
touch backend/app/api/routes/__init__.py
touch backend/app/core/__init__.py
touch backend/app/db/__init__.py
touch backend/app/ml/__init__.py
touch backend/app/ml/weather_impact/__init__.py
touch backend/app/schemas/__init__.py
touch backend/app/services/__init__.py
```

On Windows PowerShell:

```powershell
New-Item backend\app\__init__.py -ItemType File
New-Item backend\app\api\__init__.py -ItemType File
New-Item backend\app\api\routes\__init__.py -ItemType File
New-Item backend\app\core\__init__.py -ItemType File
New-Item backend\app\db\__init__.py -ItemType File
New-Item backend\app\ml\__init__.py -ItemType File
New-Item backend\app\ml\weather_impact\__init__.py -ItemType File
New-Item backend\app\schemas\__init__.py -ItemType File
New-Item backend\app\services\__init__.py -ItemType File
```

---

# 10. Python Environment Setup

## 10.1 Recommended Option: Conda

GeoPandas and OSMnx are easier to install with Conda, especially on Windows.

```bash
conda create -n smartcity python=3.11 -y
conda activate smartcity
conda install -c conda-forge geopandas osmnx networkx shapely pyproj fiona rtree pandas numpy matplotlib requests python-dotenv -y
pip install fastapi uvicorn pydantic sqlalchemy psycopg2-binary geoalchemy2
```

## 10.2 Alternative: venv + pip

```bash
cd backend
python -m venv .venv
```

Activate it:

### Windows PowerShell

```powershell
.venv\Scripts\Activate.ps1
```

### Windows CMD

```cmd
.venv\Scripts\activate.bat
```

### macOS / Linux

```bash
source .venv/bin/activate
```

Then install:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

# 11. Create `backend/requirements.txt`

Create this file:

```txt
fastapi
uvicorn[standard]
pydantic
pydantic-settings
python-dotenv
requests
pandas
numpy
geopandas
shapely
pyproj
fiona
rtree
osmnx
networkx
matplotlib
sqlalchemy
psycopg2-binary
geoalchemy2
python-multipart
```

If GeoPandas fails on Windows, use Conda instead of pip.

---

# 12. Create `.env.example`

Create:

```text
backend/.env.example
```

Content:

```env
APP_NAME=Egypt Smart City Digital Twin
APP_ENV=development
APP_DEBUG=true

DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=smart_city
DATABASE_USER=smartcity
DATABASE_PASSWORD=smartcity123

POSTGRES_URL=postgresql://smartcity:smartcity123@localhost:5432/smart_city

NASR_CITY_CENTER_LAT=30.0561
NASR_CITY_CENTER_LON=31.3300

DEFAULT_WEATHER_START_DATE=2024-01-01
DEFAULT_WEATHER_END_DATE=2024-12-31
```

Then copy it:

```bash
cp backend/.env.example backend/.env
```

On Windows PowerShell:

```powershell
Copy-Item backend\.env.example backend\.env
```

---

# 13. Docker PostGIS Setup

Create:

```text
docker-compose.yml
```

Content:

```yaml
services:
  postgis:
    image: postgis/postgis:15-3.4
    container_name: smart_city_postgis
    restart: unless-stopped
    environment:
      POSTGRES_DB: smart_city
      POSTGRES_USER: smartcity
      POSTGRES_PASSWORD: smartcity123
    ports:
      - "5432:5432"
    volumes:
      - smart_city_postgis_data:/var/lib/postgresql/data
      - ./backend/app/db/init.sql:/docker-entrypoint-initdb.d/init.sql

volumes:
  smart_city_postgis_data:
```

Start database:

```bash
docker compose up -d
```

Check container:

```bash
docker ps
```

Expected:

```text
smart_city_postgis   running   0.0.0.0:5432->5432/tcp
```

---

# 14. Database Schema

Create:

```text
backend/app/db/init.sql
```

Content:

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology;

CREATE TABLE IF NOT EXISTS district_zones (
    id SERIAL PRIMARY KEY,
    zone_code VARCHAR(50) UNIQUE NOT NULL,
    name VARCHAR(255),
    geometry GEOMETRY(POLYGON, 4326),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS road_segments (
    id SERIAL PRIMARY KEY,
    osm_id VARCHAR(100),
    road_name VARCHAR(255),
    road_type VARCHAR(100),
    length_m DOUBLE PRECISION,
    base_speed_kph DOUBLE PRECISION,
    base_travel_time_sec DOUBLE PRECISION,
    geometry GEOMETRY(LINESTRING, 4326),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS weather_records (
    id SERIAL PRIMARY KEY,
    timestamp TIMESTAMP NOT NULL,
    latitude DOUBLE PRECISION,
    longitude DOUBLE PRECISION,
    temperature_2m DOUBLE PRECISION,
    relative_humidity_2m DOUBLE PRECISION,
    precipitation DOUBLE PRECISION,
    rain DOUBLE PRECISION,
    wind_speed_10m DOUBLE PRECISION,
    source VARCHAR(100),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS zone_risk_scores (
    id SERIAL PRIMARY KEY,
    zone_code VARCHAR(50),
    timestamp TIMESTAMP,
    flood_risk DOUBLE PRECISION,
    heat_risk DOUBLE PRECISION,
    traffic_delay_risk DOUBLE PRECISION,
    emergency_delay_risk DOUBLE PRECISION,
    overall_weather_impact_score DOUBLE PRECISION,
    severity VARCHAR(50),
    details JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS road_weather_impacts (
    id SERIAL PRIMARY KEY,
    road_segment_id VARCHAR(100),
    timestamp TIMESTAMP,
    rainfall_mm DOUBLE PRECISION,
    flood_penalty DOUBLE PRECISION,
    rain_penalty DOUBLE PRECISION,
    traffic_penalty DOUBLE PRECISION,
    delay_factor DOUBLE PRECISION,
    adjusted_travel_time_sec DOUBLE PRECISION,
    severity VARCHAR(50),
    details JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS emergency_route_results (
    id SERIAL PRIMARY KEY,
    timestamp TIMESTAMP DEFAULT NOW(),
    start_lat DOUBLE PRECISION,
    start_lon DOUBLE PRECISION,
    end_lat DOUBLE PRECISION,
    end_lon DOUBLE PRECISION,
    normal_eta_minutes DOUBLE PRECISION,
    weather_safe_eta_minutes DOUBLE PRECISION,
    risk_level VARCHAR(50),
    route_geometry GEOMETRY(LINESTRING, 4326),
    avoided_roads JSONB,
    details JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX IF NOT EXISTS idx_district_zones_geom ON district_zones USING GIST (geometry);
CREATE INDEX IF NOT EXISTS idx_road_segments_geom ON road_segments USING GIST (geometry);
CREATE INDEX IF NOT EXISTS idx_emergency_routes_geom ON emergency_route_results USING GIST (route_geometry);
```

If the database already existed before creating `init.sql`, run:

```bash
docker compose down -v
docker compose up -d
```

Warning: `down -v` deletes the database volume.

---

# 15. Backend Core Configuration

Create:

```text
backend/app/core/config.py
```

Content:

```python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    app_name: str = "Egypt Smart City Digital Twin"
    app_env: str = "development"
    app_debug: bool = True

    database_host: str = "localhost"
    database_port: int = 5432
    database_name: str = "smart_city"
    database_user: str = "smartcity"
    database_password: str = "smartcity123"
    postgres_url: str = "postgresql://smartcity:smartcity123@localhost:5432/smart_city"

    nasr_city_center_lat: float = 30.0561
    nasr_city_center_lon: float = 31.3300

    default_weather_start_date: str = "2024-01-01"
    default_weather_end_date: str = "2024-12-31"

    class Config:
        env_file = "backend/.env"
        env_file_encoding = "utf-8"


settings = Settings()
```

---

# 16. Database Session File

Create:

```text
backend/app/db/session.py
```

Content:

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from backend.app.core.config import settings

engine = create_engine(settings.postgres_url, pool_pre_ping=True)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)


def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

---

# 17. Phase 1 — Download Nasr City Road Network

## 17.1 Goal

Download the road network for Nasr City from OpenStreetMap using OSMnx.

Outputs:

```text
backend/app/data/nasr_city/processed/nasr_city_roads.geojson
backend/app/data/nasr_city/processed/nasr_city_nodes.geojson
backend/app/data/nasr_city/processed/nasr_city_graph.graphml
```

## 17.2 Create Script

Create:

```text
backend/app/scripts/01_download_nasr_city_roads.py
```

Content:

```python
from pathlib import Path
import osmnx as ox

OUTPUT_DIR = Path("backend/app/data/nasr_city/processed")
OUTPUT_DIR.mkdir(parents=True, exist_ok=True)

PLACE_NAME = "Nasr City, Cairo, Egypt"

# Fallback bounding box around Nasr City area.
# This can be refined later after validating the boundary.
FALLBACK_BBOX = {
    "north": 30.095,
    "south": 30.015,
    "east": 31.405,
    "west": 31.285,
}


def download_graph_by_place():
    print(f"Trying to download road network by place: {PLACE_NAME}")
    graph = ox.graph_from_place(
        PLACE_NAME,
        network_type="drive",
        simplify=True,
    )
    return graph


def download_graph_by_bbox():
    print("Place search failed. Trying fallback bounding box...")
    graph = ox.graph_from_bbox(
        north=FALLBACK_BBOX["north"],
        south=FALLBACK_BBOX["south"],
        east=FALLBACK_BBOX["east"],
        west=FALLBACK_BBOX["west"],
        network_type="drive",
        simplify=True,
    )
    return graph


def main():
    try:
        graph = download_graph_by_place()
    except Exception as exc:
        print(f"Place download failed: {exc}")
        graph = download_graph_by_bbox()

    print("Adding speed and travel-time attributes...")
    graph = ox.add_edge_speeds(graph)
    graph = ox.add_edge_travel_times(graph)

    print("Converting graph to GeoDataFrames...")
    nodes, edges = ox.graph_to_gdfs(graph)

    graph_path = OUTPUT_DIR / "nasr_city_graph.graphml"
    nodes_path = OUTPUT_DIR / "nasr_city_nodes.geojson"
    roads_path = OUTPUT_DIR / "nasr_city_roads.geojson"

    print("Saving files...")
    ox.save_graphml(graph, graph_path)
    nodes.to_file(nodes_path, driver="GeoJSON")
    edges.to_file(roads_path, driver="GeoJSON")

    print("Road network extraction complete.")
    print(f"Nodes: {len(nodes)}")
    print(f"Road segments: {len(edges)}")
    print(f"Saved graph: {graph_path}")
    print(f"Saved nodes: {nodes_path}")
    print(f"Saved roads: {roads_path}")


if __name__ == "__main__":
    main()
```

## 17.3 Run Script

From repo root:

```bash
python backend/app/scripts/01_download_nasr_city_roads.py
```

Expected output:

```text
Trying to download road network by place: Nasr City, Cairo, Egypt
Adding speed and travel-time attributes...
Converting graph to GeoDataFrames...
Saving files...
Road network extraction complete.
Nodes: ...
Road segments: ...
```

## 17.4 Common Error

If you get:

```text
Nominatim could not geocode query
```

The script should automatically try the fallback bounding box.

If OSMnx still fails, check internet connection and try again.

---

# 18. Phase 2 — Collect Weather Data

## 18.1 Goal

Collect historical hourly weather data for Nasr City.

Important variables:

- temperature_2m
- relative_humidity_2m
- precipitation
- rain
- wind_speed_10m

Output:

```text
backend/app/data/nasr_city/raw/weather_history.csv
```

## 18.2 Create Weather Collector Script

Create:

```text
backend/app/scripts/02_collect_weather_data.py
```

Content:

```python
from pathlib import Path
import requests
import pandas as pd

OUTPUT_DIR = Path("backend/app/data/nasr_city/raw")
OUTPUT_DIR.mkdir(parents=True, exist_ok=True)

LATITUDE = 30.0561
LONGITUDE = 31.3300
START_DATE = "2024-01-01"
END_DATE = "2024-12-31"


def collect_open_meteo_weather():
    url = "https://archive-api.open-meteo.com/v1/archive"

    params = {
        "latitude": LATITUDE,
        "longitude": LONGITUDE,
        "start_date": START_DATE,
        "end_date": END_DATE,
        "hourly": [
            "temperature_2m",
            "relative_humidity_2m",
            "precipitation",
            "rain",
            "wind_speed_10m",
        ],
        "timezone": "Africa/Cairo",
    }

    print("Requesting weather data from Open-Meteo...")
    response = requests.get(url, params=params, timeout=60)
    response.raise_for_status()

    data = response.json()
    hourly = data["hourly"]

    df = pd.DataFrame(hourly)
    df.rename(columns={"time": "timestamp"}, inplace=True)
    df["latitude"] = LATITUDE
    df["longitude"] = LONGITUDE
    df["source"] = "open_meteo"

    output_path = OUTPUT_DIR / "weather_history.csv"
    df.to_csv(output_path, index=False)

    print("Weather data collection complete.")
    print(f"Rows: {len(df)}")
    print(f"Saved: {output_path}")
    print(df.head())


if __name__ == "__main__":
    collect_open_meteo_weather()
```

## 18.3 Run Script

```bash
python backend/app/scripts/02_collect_weather_data.py
```

Expected output:

```text
Requesting weather data from Open-Meteo...
Weather data collection complete.
Rows: 8784
Saved: backend/app/data/nasr_city/raw/weather_history.csv
```

There are 8784 rows in 2024 because 2024 is a leap year.

## 18.4 NASA POWER Alternative

Later, we can add a NASA POWER script for comparison and validation.

NASA POWER is useful for:

- precipitation
- temperature
- wind
- humidity
- solar/thermal context

For MVP, Open-Meteo is easier because the API response is simpler.

---

# 19. Phase 3 — Generate Nasr City Analysis Zones

## 19.1 Goal

Create zones/grid cells over Nasr City roads.

Why?

The digital twin needs spatial units.

Instead of only road lines, we need zones like:

```text
NSR-Z001
NSR-Z002
NSR-Z003
...
```

Each zone can have:

- flood risk
- heat risk
- traffic delay risk
- emergency accessibility risk
- overall risk score

## 19.2 Create Zone Generator

Create:

```text
backend/app/scripts/03_generate_zones.py
```

Content:

```python
from pathlib import Path
import geopandas as gpd
from shapely.geometry import box

INPUT_ROADS = Path("backend/app/data/nasr_city/processed/nasr_city_roads.geojson")
OUTPUT_ZONES = Path("backend/app/data/nasr_city/processed/nasr_city_zones.geojson")

# Approx grid cell size in degrees.
# Around Cairo, 0.005 degrees is roughly 500m depending on direction.
GRID_SIZE = 0.005


def generate_square_grid(bounds, grid_size):
    minx, miny, maxx, maxy = bounds
    polygons = []
    zone_codes = []
    zone_id = 1

    x = minx
    while x < maxx:
        y = miny
        while y < maxy:
            polygons.append(box(x, y, x + grid_size, y + grid_size))
            zone_codes.append(f"NSR-Z{zone_id:03d}")
            zone_id += 1
            y += grid_size
        x += grid_size

    return gpd.GeoDataFrame(
        {"zone_code": zone_codes},
        geometry=polygons,
        crs="EPSG:4326",
    )


def main():
    print("Loading roads...")
    roads = gpd.read_file(INPUT_ROADS)

    print("Creating bounding area from road network...")
    bounds = roads.total_bounds

    print("Generating grid...")
    zones = generate_square_grid(bounds, GRID_SIZE)

    print("Keeping only zones that intersect roads...")
    roads_union = roads.geometry.union_all()
    zones = zones[zones.intersects(roads_union)].copy()
    zones.reset_index(drop=True, inplace=True)
    zones["zone_code"] = [f"NSR-Z{i+1:03d}" for i in range(len(zones))]

    print("Saving zones...")
    zones.to_file(OUTPUT_ZONES, driver="GeoJSON")

    print("Zone generation complete.")
    print(f"Zones: {len(zones)}")
    print(f"Saved: {OUTPUT_ZONES}")


if __name__ == "__main__":
    main()
```

## 19.3 Run Script

```bash
python backend/app/scripts/03_generate_zones.py
```

Expected output:

```text
Loading roads...
Creating bounding area from road network...
Generating grid...
Keeping only zones that intersect roads...
Saving zones...
Zone generation complete.
Zones: ...
```

---

# 20. Phase 4 — Flood Risk Engine v1

## 20.1 Goal

Create a first flood risk score for each zone.

This is not a hydrological simulation yet. It is a practical MVP risk score.

## 20.2 Flood Risk Formula v1

```text
Flood Risk = rainfall_score + road_density_score + low_area_proxy
```

For v1:

- rainfall_score comes from precipitation/rain
- road_density_score comes from roads per zone
- low_area_proxy is optional and can start as zero

Later v2 will add:

- SRTM elevation
- slope
- flow accumulation
- drainage proximity
- impervious surface
- historical flood reports

## 20.3 Risk Levels

| Score | Severity |
|---:|---|
| 0.00 - 0.33 | low |
| 0.34 - 0.66 | medium |
| 0.67 - 1.00 | high |

## 20.4 Create Engine File

Create:

```text
backend/app/ml/weather_impact/flood_risk_engine.py
```

Content:

```python
import pandas as pd
import geopandas as gpd


def normalize(value, min_value, max_value):
    if max_value == min_value:
        return 0.0
    return max(0.0, min(1.0, (value - min_value) / (max_value - min_value)))


def severity_from_score(score: float) -> str:
    if score >= 0.67:
        return "high"
    if score >= 0.34:
        return "medium"
    return "low"


def rainfall_to_score(rainfall_mm: float) -> float:
    """
    Simple rainfall scoring.
    You can tune these thresholds later based on local climate.
    """
    if rainfall_mm <= 0:
        return 0.0
    if rainfall_mm < 2:
        return 0.2
    if rainfall_mm < 5:
        return 0.4
    if rainfall_mm < 10:
        return 0.6
    if rainfall_mm < 20:
        return 0.8
    return 1.0


def compute_zone_road_density(zones: gpd.GeoDataFrame, roads: gpd.GeoDataFrame) -> gpd.GeoDataFrame:
    zones = zones.copy()
    zones_projected = zones.to_crs(epsg=3857)
    roads_projected = roads.to_crs(epsg=3857)

    road_lengths = []
    for _, zone in zones_projected.iterrows():
        clipped = roads_projected[roads_projected.intersects(zone.geometry)]
        length_m = clipped.geometry.length.sum()
        area_m2 = zone.geometry.area
        density = length_m / area_m2 if area_m2 > 0 else 0
        road_lengths.append(density)

    zones["road_density"] = road_lengths
    min_density = zones["road_density"].min()
    max_density = zones["road_density"].max()
    zones["road_density_score"] = zones["road_density"].apply(
        lambda x: normalize(x, min_density, max_density)
    )
    return zones


def compute_flood_risk(zones: gpd.GeoDataFrame, roads: gpd.GeoDataFrame, rainfall_mm: float) -> gpd.GeoDataFrame:
    zones = compute_zone_road_density(zones, roads)

    rainfall_score = rainfall_to_score(rainfall_mm)

    # v1: low_area_proxy is fixed until SRTM elevation is added.
    zones["low_area_proxy"] = 0.2

    zones["flood_risk"] = (
        0.60 * rainfall_score
        + 0.25 * zones["road_density_score"]
        + 0.15 * zones["low_area_proxy"]
    )

    zones["flood_risk"] = zones["flood_risk"].clip(0, 1)
    zones["flood_severity"] = zones["flood_risk"].apply(severity_from_score)
    zones["rainfall_mm"] = rainfall_mm

    return zones
```

---

# 21. Phase 5 — Heat Risk Engine v1

## 21.1 Goal

Estimate heat risk for each zone.

v1 will use weather temperature and road/built-up proxy.

v2 will use Landsat LST, NDVI, GHSL, and ESA WorldCover.

## 21.2 Heat Risk Formula v1

```text
Heat Risk = temperature_score + built_up_proxy + low_vegetation_proxy
```

For v1:

- temperature_score from weather API
- built_up_proxy from road density
- low_vegetation_proxy fixed until land cover data is added

## 21.3 Create Engine File

Create:

```text
backend/app/ml/weather_impact/heat_risk_engine.py
```

Content:

```python
import geopandas as gpd


def normalize(value, min_value, max_value):
    if max_value == min_value:
        return 0.0
    return max(0.0, min(1.0, (value - min_value) / (max_value - min_value)))


def severity_from_score(score: float) -> str:
    if score >= 0.67:
        return "high"
    if score >= 0.34:
        return "medium"
    return "low"


def temperature_to_score(temp_c: float) -> float:
    if temp_c < 25:
        return 0.1
    if temp_c < 30:
        return 0.3
    if temp_c < 35:
        return 0.6
    if temp_c < 40:
        return 0.8
    return 1.0


def compute_heat_risk(zones: gpd.GeoDataFrame, temperature_c: float) -> gpd.GeoDataFrame:
    zones = zones.copy()

    temp_score = temperature_to_score(temperature_c)

    if "road_density_score" not in zones.columns:
        zones["road_density_score"] = 0.5

    zones["low_vegetation_proxy"] = 0.5

    zones["heat_risk"] = (
        0.55 * temp_score
        + 0.30 * zones["road_density_score"]
        + 0.15 * zones["low_vegetation_proxy"]
    )

    zones["heat_risk"] = zones["heat_risk"].clip(0, 1)
    zones["heat_severity"] = zones["heat_risk"].apply(severity_from_score)
    zones["temperature_c"] = temperature_c

    return zones
```

---

# 22. Phase 6 — Traffic Delay Engine v1

## 22.1 Goal

Calculate how rainfall/flood risk increases road travel time.

## 22.2 Traffic Delay Formula

```text
Adjusted Travel Time = Base Travel Time × Delay Factor
```

Delay factor:

```text
Delay Factor = 1 + rain_penalty + flood_penalty
```

Example:

```text
base travel time = 60 seconds
rain penalty = 0.15
flood penalty = 0.30
adjusted time = 60 × 1.45 = 87 seconds
```

## 22.3 Create Engine File

Create:

```text
backend/app/ml/weather_impact/traffic_delay_engine.py
```

Content:

```python
import geopandas as gpd


def rainfall_penalty(rainfall_mm: float) -> float:
    if rainfall_mm <= 0:
        return 0.0
    if rainfall_mm < 2:
        return 0.05
    if rainfall_mm < 5:
        return 0.10
    if rainfall_mm < 10:
        return 0.20
    if rainfall_mm < 20:
        return 0.35
    return 0.50


def flood_penalty_from_score(flood_risk: float) -> float:
    if flood_risk < 0.34:
        return 0.0
    if flood_risk < 0.67:
        return 0.20
    return 0.45


def severity_from_delay_factor(delay_factor: float) -> str:
    if delay_factor >= 1.6:
        return "high"
    if delay_factor >= 1.25:
        return "medium"
    return "low"


def compute_road_delay(roads: gpd.GeoDataFrame, rainfall_mm: float, average_flood_risk: float) -> gpd.GeoDataFrame:
    roads = roads.copy()

    rain_pen = rainfall_penalty(rainfall_mm)
    flood_pen = flood_penalty_from_score(average_flood_risk)

    roads["rain_penalty"] = rain_pen
    roads["flood_penalty"] = flood_pen
    roads["delay_factor"] = 1 + roads["rain_penalty"] + roads["flood_penalty"]

    # OSMnx edges usually contain travel_time in seconds.
    if "travel_time" in roads.columns:
        roads["base_travel_time_sec"] = roads["travel_time"]
    else:
        roads["base_travel_time_sec"] = 60

    roads["adjusted_travel_time_sec"] = roads["base_travel_time_sec"] * roads["delay_factor"]
    roads["traffic_delay_severity"] = roads["delay_factor"].apply(severity_from_delay_factor)

    return roads
```

---

# 23. Phase 7 — Full Risk Pipeline

## 23.1 Goal

Run flood + heat + traffic engines together.

Output files:

```text
backend/app/data/nasr_city/outputs/zone_weather_impact.geojson
backend/app/data/nasr_city/outputs/road_weather_impacts.geojson
```

## 23.2 Create Pipeline Script

Create:

```text
backend/app/scripts/04_run_risk_pipeline.py
```

Content:

```python
from pathlib import Path
import pandas as pd
import geopandas as gpd

from backend.app.ml.weather_impact.flood_risk_engine import compute_flood_risk
from backend.app.ml.weather_impact.heat_risk_engine import compute_heat_risk
from backend.app.ml.weather_impact.traffic_delay_engine import compute_road_delay

DATA_DIR = Path("backend/app/data/nasr_city")
ROADS_PATH = DATA_DIR / "processed/nasr_city_roads.geojson"
ZONES_PATH = DATA_DIR / "processed/nasr_city_zones.geojson"
WEATHER_PATH = DATA_DIR / "raw/weather_history.csv"
OUTPUT_DIR = DATA_DIR / "outputs"
OUTPUT_DIR.mkdir(parents=True, exist_ok=True)


def severity_from_score(score: float) -> str:
    if score >= 0.67:
        return "high"
    if score >= 0.34:
        return "medium"
    return "low"


def main():
    print("Loading input data...")
    roads = gpd.read_file(ROADS_PATH)
    zones = gpd.read_file(ZONES_PATH)
    weather = pd.read_csv(WEATHER_PATH)

    # Select one weather scenario.
    # You can change this later to select a specific date/hour.
    latest = weather.iloc[-1]
    rainfall_mm = float(latest.get("rain", 0) or latest.get("precipitation", 0) or 0)
    temperature_c = float(latest.get("temperature_2m", 30))

    # For demo/testing, force a rain scenario if the selected hour has no rain.
    # Remove this later when using real storm events.
    if rainfall_mm == 0:
        print("No rain in latest hour. Using demo rainfall value: 12 mm")
        rainfall_mm = 12.0

    print(f"Scenario rainfall: {rainfall_mm} mm")
    print(f"Scenario temperature: {temperature_c} °C")

    print("Computing flood risk...")
    zones = compute_flood_risk(zones, roads, rainfall_mm)

    print("Computing heat risk...")
    zones = compute_heat_risk(zones, temperature_c)

    print("Computing total weather impact score...")
    zones["traffic_delay_risk"] = zones["flood_risk"] * 0.8
    zones["emergency_delay_risk"] = zones["flood_risk"] * 0.7 + zones["traffic_delay_risk"] * 0.3

    zones["overall_weather_impact_score"] = (
        0.35 * zones["flood_risk"]
        + 0.25 * zones["heat_risk"]
        + 0.20 * zones["traffic_delay_risk"]
        + 0.20 * zones["emergency_delay_risk"]
    )

    zones["overall_severity"] = zones["overall_weather_impact_score"].apply(severity_from_score)

    average_flood_risk = float(zones["flood_risk"].mean())

    print("Computing road delay impacts...")
    roads = compute_road_delay(roads, rainfall_mm, average_flood_risk)

    zone_output = OUTPUT_DIR / "zone_weather_impact.geojson"
    road_output = OUTPUT_DIR / "road_weather_impacts.geojson"

    zones.to_file(zone_output, driver="GeoJSON")
    roads.to_file(road_output, driver="GeoJSON")

    print("Risk pipeline complete.")
    print(f"Saved zones: {zone_output}")
    print(f"Saved roads: {road_output}")
    print("Severity counts:")
    print(zones["overall_severity"].value_counts())


if __name__ == "__main__":
    main()
```

## 23.3 Run Pipeline

```bash
python backend/app/scripts/04_run_risk_pipeline.py
```

Expected output:

```text
Loading input data...
No rain in latest hour. Using demo rainfall value: 12 mm
Scenario rainfall: 12.0 mm
Scenario temperature: ... °C
Computing flood risk...
Computing heat risk...
Computing total weather impact score...
Computing road delay impacts...
Risk pipeline complete.
Saved zones: backend/app/data/nasr_city/outputs/zone_weather_impact.geojson
Saved roads: backend/app/data/nasr_city/outputs/road_weather_impacts.geojson
```

---

# 24. Phase 8 — Emergency Route Engine

## 24.1 Goal

Find the safest route between two coordinates.

Normal shortest route uses:

```text
travel_time
```

Weather-safe route uses:

```text
weather_weight = travel_time × delay_factor
```

## 24.2 Create Engine File

Create:

```text
backend/app/ml/weather_impact/emergency_route_engine.py
```

Content:

```python
from pathlib import Path
import osmnx as ox
import networkx as nx
from shapely.geometry import LineString

GRAPH_PATH = Path("backend/app/data/nasr_city/processed/nasr_city_graph.graphml")


def load_graph():
    if not GRAPH_PATH.exists():
        raise FileNotFoundError(f"Graph file not found: {GRAPH_PATH}")
    return ox.load_graphml(GRAPH_PATH)


def add_weather_weights(graph, delay_factor: float = 1.35):
    """
    v1 applies one global delay factor.
    v2 will apply per-road delay factor from road_weather_impacts.geojson.
    """
    for _, _, _, data in graph.edges(keys=True, data=True):
        base_time = float(data.get("travel_time", 60))
        data["weather_weight"] = base_time * delay_factor
    return graph


def route_to_linestring(graph, route):
    points = []
    for node in route:
        node_data = graph.nodes[node]
        points.append((node_data["x"], node_data["y"]))
    return LineString(points)


def compute_emergency_route(start_lat, start_lon, end_lat, end_lon, delay_factor=1.35):
    graph = load_graph()
    graph = add_weather_weights(graph, delay_factor=delay_factor)

    start_node = ox.distance.nearest_nodes(graph, X=start_lon, Y=start_lat)
    end_node = ox.distance.nearest_nodes(graph, X=end_lon, Y=end_lat)

    normal_route = nx.shortest_path(graph, start_node, end_node, weight="travel_time")
    safe_route = nx.shortest_path(graph, start_node, end_node, weight="weather_weight")

    normal_eta_sec = nx.shortest_path_length(graph, start_node, end_node, weight="travel_time")
    safe_eta_sec = nx.shortest_path_length(graph, start_node, end_node, weight="weather_weight")

    route_geom = route_to_linestring(graph, safe_route)

    risk_level = "low"
    if safe_eta_sec / max(normal_eta_sec, 1) >= 1.5:
        risk_level = "high"
    elif safe_eta_sec / max(normal_eta_sec, 1) >= 1.2:
        risk_level = "medium"

    return {
        "start": {"lat": start_lat, "lon": start_lon},
        "end": {"lat": end_lat, "lon": end_lon},
        "normal_eta_minutes": round(normal_eta_sec / 60, 2),
        "weather_safe_eta_minutes": round(safe_eta_sec / 60, 2),
        "delay_minutes": round((safe_eta_sec - normal_eta_sec) / 60, 2),
        "risk_level": risk_level,
        "route_geometry_wkt": route_geom.wkt,
        "route_node_count": len(safe_route),
    }
```

## 24.3 Create Test Script

Create:

```text
backend/app/scripts/05_test_emergency_route.py
```

Content:

```python
from backend.app.ml.weather_impact.emergency_route_engine import compute_emergency_route


def main():
    # Demo coordinates around Nasr City.
    # Replace later with real hospital and incident coordinates.
    start_lat = 30.0610
    start_lon = 31.3400
    end_lat = 30.0450
    end_lon = 31.3600

    result = compute_emergency_route(
        start_lat=start_lat,
        start_lon=start_lon,
        end_lat=end_lat,
        end_lon=end_lon,
        delay_factor=1.35,
    )

    print("Emergency route result:")
    for key, value in result.items():
        print(f"{key}: {value}")


if __name__ == "__main__":
    main()
```

## 24.4 Run Test

```bash
python backend/app/scripts/05_test_emergency_route.py
```

Expected output:

```text
Emergency route result:
start: {'lat': 30.061, 'lon': 31.34}
end: {'lat': 30.045, 'lon': 31.36}
normal_eta_minutes: ...
weather_safe_eta_minutes: ...
delay_minutes: ...
risk_level: ...
route_geometry_wkt: LINESTRING (...)
```

---

# 25. Phase 9 — FastAPI Backend

## 25.1 Create Schemas

Create:

```text
backend/app/schemas/weather_impact.py
```

Content:

```python
from pydantic import BaseModel, Field


class EmergencyRouteRequest(BaseModel):
    start_lat: float = Field(..., example=30.0610)
    start_lon: float = Field(..., example=31.3400)
    end_lat: float = Field(..., example=30.0450)
    end_lon: float = Field(..., example=31.3600)
    delay_factor: float = Field(1.35, example=1.35)


class EmergencyRouteResponse(BaseModel):
    normal_eta_minutes: float
    weather_safe_eta_minutes: float
    delay_minutes: float
    risk_level: str
    route_geometry_wkt: str
    route_node_count: int
```

## 25.2 Create Service File

Create:

```text
backend/app/services/weather_impact_service.py
```

Content:

```python
from pathlib import Path
import json
import geopandas as gpd

from backend.app.ml.weather_impact.emergency_route_engine import compute_emergency_route

DATA_DIR = Path("backend/app/data/nasr_city")
ZONE_OUTPUT = DATA_DIR / "outputs/zone_weather_impact.geojson"
ROAD_OUTPUT = DATA_DIR / "outputs/road_weather_impacts.geojson"


def load_geojson_as_dict(path: Path):
    if not path.exists():
        return {
            "status": "missing",
            "message": f"File not found: {path}. Run the risk pipeline first.",
            "features": [],
        }

    gdf = gpd.read_file(path)
    return json.loads(gdf.to_json())


def get_zone_risks():
    return load_geojson_as_dict(ZONE_OUTPUT)


def get_road_impacts():
    return load_geojson_as_dict(ROAD_OUTPUT)


def get_summary():
    if not ZONE_OUTPUT.exists():
        return {
            "status": "missing",
            "message": "Run 04_run_risk_pipeline.py first.",
        }

    zones = gpd.read_file(ZONE_OUTPUT)

    return {
        "district": "Nasr City",
        "zone_count": int(len(zones)),
        "average_flood_risk": round(float(zones["flood_risk"].mean()), 3),
        "average_heat_risk": round(float(zones["heat_risk"].mean()), 3),
        "average_overall_risk": round(float(zones["overall_weather_impact_score"].mean()), 3),
        "high_risk_zones": int((zones["overall_severity"] == "high").sum()),
        "medium_risk_zones": int((zones["overall_severity"] == "medium").sum()),
        "low_risk_zones": int((zones["overall_severity"] == "low").sum()),
    }


def get_emergency_route(payload):
    return compute_emergency_route(
        start_lat=payload.start_lat,
        start_lon=payload.start_lon,
        end_lat=payload.end_lat,
        end_lon=payload.end_lon,
        delay_factor=payload.delay_factor,
    )
```

## 25.3 Create API Router

Create:

```text
backend/app/api/routes/weather_impact.py
```

Content:

```python
from fastapi import APIRouter

from backend.app.schemas.weather_impact import EmergencyRouteRequest
from backend.app.services.weather_impact_service import (
    get_zone_risks,
    get_road_impacts,
    get_summary,
    get_emergency_route,
)

router = APIRouter(prefix="/weather-impact", tags=["Weather Impact"])


@router.get("/summary")
def summary():
    return get_summary()


@router.get("/zones")
def zones():
    return get_zone_risks()


@router.get("/roads")
def roads():
    return get_road_impacts()


@router.post("/emergency-route")
def emergency_route(payload: EmergencyRouteRequest):
    return get_emergency_route(payload)
```

## 25.4 Create Main FastAPI App

Create:

```text
backend/app/main.py
```

Content:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

from backend.app.api.routes.weather_impact import router as weather_impact_router

app = FastAPI(
    title="Egypt Smart City Digital Twin API",
    description="Weather-Impact Emergency Mobility Module for Nasr City",
    version="0.1.0",
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)


@app.get("/")
def root():
    return {
        "project": "Egypt Smart City Digital Twin",
        "module": "Weather-Impact Emergency Mobility Module",
        "case_study": "Nasr City, Cairo",
        "status": "running",
    }


@app.get("/health")
def health():
    return {"status": "ok"}


app.include_router(weather_impact_router)
```

## 25.5 Run API

From repo root:

```bash
uvicorn backend.app.main:app --reload
```

Open:

```text
http://127.0.0.1:8000/docs
```

Test endpoints:

```text
GET  http://127.0.0.1:8000/
GET  http://127.0.0.1:8000/health
GET  http://127.0.0.1:8000/weather-impact/summary
GET  http://127.0.0.1:8000/weather-impact/zones
GET  http://127.0.0.1:8000/weather-impact/roads
POST http://127.0.0.1:8000/weather-impact/emergency-route
```

POST body:

```json
{
  "start_lat": 30.061,
  "start_lon": 31.34,
  "end_lat": 30.045,
  "end_lon": 31.36,
  "delay_factor": 1.35
}
```

Expected result:

```json
{
  "start": {
    "lat": 30.061,
    "lon": 31.34
  },
  "end": {
    "lat": 30.045,
    "lon": 31.36
  },
  "normal_eta_minutes": 4.72,
  "weather_safe_eta_minutes": 6.37,
  "delay_minutes": 1.65,
  "risk_level": "medium",
  "route_geometry_wkt": "LINESTRING (...)"
}
```

---

# 26. Full Execution Order

Run this order exactly:

```bash
# 1. Activate environment
conda activate smartcity

# 2. Start database
 docker compose up -d

# 3. Download roads
python backend/app/scripts/01_download_nasr_city_roads.py

# 4. Collect weather
python backend/app/scripts/02_collect_weather_data.py

# 5. Generate zones
python backend/app/scripts/03_generate_zones.py

# 6. Run risk pipeline
python backend/app/scripts/04_run_risk_pipeline.py

# 7. Test emergency route
python backend/app/scripts/05_test_emergency_route.py

# 8. Start backend API
uvicorn backend.app.main:app --reload
```

Then open:

```text
http://127.0.0.1:8000/docs
```

---

# 27. Expected Generated Files

After successful execution, you should have:

```text
backend/app/data/nasr_city/processed/nasr_city_graph.graphml
backend/app/data/nasr_city/processed/nasr_city_nodes.geojson
backend/app/data/nasr_city/processed/nasr_city_roads.geojson
backend/app/data/nasr_city/processed/nasr_city_zones.geojson
backend/app/data/nasr_city/raw/weather_history.csv
backend/app/data/nasr_city/outputs/zone_weather_impact.geojson
backend/app/data/nasr_city/outputs/road_weather_impacts.geojson
```

---

# 28. How to Verify Success

## 28.1 Road Data Check

Open:

```text
backend/app/data/nasr_city/processed/nasr_city_roads.geojson
```

in QGIS or geojson.io.

You should see a road network around Nasr City.

## 28.2 Weather Check

Open:

```text
backend/app/data/nasr_city/raw/weather_history.csv
```

You should see columns:

```text
timestamp
temperature_2m
relative_humidity_2m
precipitation
rain
wind_speed_10m
latitude
longitude
source
```

## 28.3 Risk Output Check

Open:

```text
backend/app/data/nasr_city/outputs/zone_weather_impact.geojson
```

Expected properties:

```text
zone_code
road_density
road_density_score
flood_risk
flood_severity
heat_risk
heat_severity
traffic_delay_risk
emergency_delay_risk
overall_weather_impact_score
overall_severity
```

## 28.4 API Check

Open:

```text
http://127.0.0.1:8000/weather-impact/summary
```

Expected:

```json
{
  "district": "Nasr City",
  "zone_count": 100,
  "average_flood_risk": 0.55,
  "average_heat_risk": 0.48,
  "average_overall_risk": 0.52,
  "high_risk_zones": 15,
  "medium_risk_zones": 70,
  "low_risk_zones": 15
}
```

Numbers will be different depending on downloaded roads and scenario.

---

# 29. Frontend Plan After Backend Works

Do not start frontend before backend output works.

After backend is stable, create a React dashboard.

## 29.1 Frontend Features

First version dashboard:

- map centered on Nasr City
- toggle flood layer
- toggle heat layer
- toggle road delay layer
- click zone to show risk details
- route form with start/end coordinates
- emergency route line on map
- summary cards

## 29.2 Dashboard Cards

```text
Average Flood Risk
Average Heat Risk
High Risk Zones
Affected Roads
Emergency Delay Level
```

## 29.3 Map Layers

| Layer | API Endpoint |
|---|---|
| Zone risk layer | `/weather-impact/zones` |
| Road delay layer | `/weather-impact/roads` |
| Emergency route | `/weather-impact/emergency-route` |
| Summary cards | `/weather-impact/summary` |

---

# 30. Methodology for the Report

Use this methodology section in your academic documentation.

## 30.1 Data Acquisition

The system collects road network data from OpenStreetMap using OSMnx and historical hourly weather records from Open-Meteo. Future versions integrate NASA POWER, SRTM elevation data, Landsat Surface Temperature, GHSL built-up density, ESA WorldCover land cover, and WorldPop population exposure.

## 30.2 Geospatial Processing

The road network is converted into geospatial line features. The case study area is divided into a grid of spatial zones. Each road and zone is stored as GeoJSON and can later be inserted into PostGIS.

## 30.3 Flood Risk Modeling

Flood risk is estimated using rainfall intensity, road density, and later elevation/slope/runoff features. The MVP uses a rule-based scoring engine, while later versions can train a supervised ML model if labeled flood data becomes available.

## 30.4 Heat Risk Modeling

Heat risk is initially estimated using air temperature and urban density proxies. Later versions will incorporate Landsat land surface temperature, NDVI, built-up density, and population exposure.

## 30.5 Traffic Delay Modeling

Road segments receive delay factors based on rainfall and flood risk. The adjusted travel time is calculated by multiplying base travel time by a weather delay factor.

## 30.6 Emergency Route Optimization

The road network is treated as a graph. Nodes represent intersections and edges represent road segments. The system compares normal travel time with weather-adjusted travel time and recommends a safer route using weighted shortest path algorithms.

## 30.7 Digital Twin Integration

All outputs are standardized into zone-level and road-level risk layers. The backend API exposes these layers to the dashboard, allowing the system to behave as a digital twin of Nasr City under weather-impact scenarios.

---

# 31. Evaluation Plan

## 31.1 Technical Evaluation

| Component | Evaluation |
|---|---|
| Road extraction | visually compare with map |
| Weather collector | check missing values and date coverage |
| Zone generation | ensure zones cover road network |
| Flood scoring | validate high scores during heavy rainfall scenarios |
| Heat scoring | validate higher risk during hot hours/days |
| Traffic delay | compare base vs adjusted travel time |
| Emergency routing | verify route changes under high delay factor |
| API | test status codes and response structure |

## 31.2 Academic Evaluation

Explain that v1 is a prototype decision-support model, not a certified government flood simulation.

Evaluate by:

- scenario testing
- visual inspection
- sensitivity analysis
- route comparison
- risk map consistency
- expert/manual reasoning

## 31.3 Scenario Tests

Create 3 rainfall scenarios:

| Scenario | Rainfall | Expected Result |
|---|---:|---|
| Dry | 0 mm | low flood and low traffic penalty |
| Moderate Rain | 5 mm | medium risk in dense zones |
| Heavy Rain | 20 mm | high flood/traffic/emergency delay risk |

Create 3 heat scenarios:

| Scenario | Temperature | Expected Result |
|---|---:|---|
| Normal | 25°C | low heat risk |
| Hot | 35°C | medium/high heat risk |
| Extreme | 42°C | high heat risk |

---

# 32. Roadmap

## Phase 0 — Preparation

Duration: 1 day

Small steps:

1. Create branch.
2. Create backend folders.
3. Create requirements file.
4. Create `.env` file.
5. Create Docker Compose.
6. Start PostGIS.
7. Confirm FastAPI can run.

Deliverable:

```text
Backend skeleton running at /health
```

---

## Phase 1 — Road Network Extraction

Duration: 1–2 days

Small steps:

1. Install OSMnx.
2. Download Nasr City roads by place name.
3. If place name fails, use bounding box.
4. Save GraphML.
5. Save roads GeoJSON.
6. Save nodes GeoJSON.
7. Open roads in QGIS or geojson.io.
8. Validate that main roads appear.

Deliverable:

```text
nasr_city_graph.graphml
nasr_city_roads.geojson
nasr_city_nodes.geojson
```

---

## Phase 2 — Weather Data Collection

Duration: 1 day

Small steps:

1. Create Open-Meteo collector.
2. Download 1 year of hourly data.
3. Save CSV.
4. Check null values.
5. Find rainy hours.
6. Select sample rain scenario.

Deliverable:

```text
weather_history.csv
```

---

## Phase 3 — Zone Generation

Duration: 1 day

Small steps:

1. Load roads.
2. Generate grid.
3. Keep zones intersecting roads.
4. Give every zone a code.
5. Save zones GeoJSON.
6. Validate visually.

Deliverable:

```text
nasr_city_zones.geojson
```

---

## Phase 4 — Flood Risk v1

Duration: 2 days

Small steps:

1. Convert rainfall to rainfall score.
2. Calculate road density per zone.
3. Add low-area proxy.
4. Calculate flood risk.
5. Assign severity.
6. Save output.
7. Test dry/moderate/heavy rain scenarios.

Deliverable:

```text
Flood risk layer by zone
```

---

## Phase 5 — Heat Risk v1

Duration: 1–2 days

Small steps:

1. Convert temperature to heat score.
2. Use road density as built-up proxy.
3. Add low vegetation proxy.
4. Calculate heat risk.
5. Assign severity.
6. Save output.
7. Test normal/hot/extreme scenarios.

Deliverable:

```text
Heat risk layer by zone
```

---

## Phase 6 — Traffic Delay v1

Duration: 1–2 days

Small steps:

1. Load road travel times.
2. Create rainfall penalty.
3. Create flood penalty.
4. Calculate delay factor.
5. Calculate adjusted travel time.
6. Save road impact GeoJSON.
7. Visualize delayed roads.

Deliverable:

```text
Road weather impact layer
```

---

## Phase 7 — Emergency Route Optimization

Duration: 2–3 days

Small steps:

1. Load GraphML road graph.
2. Find nearest graph node to start point.
3. Find nearest graph node to destination.
4. Calculate normal shortest path.
5. Calculate weather-weighted path.
6. Compare ETA.
7. Return route geometry.
8. Test with hospital-to-incident examples.

Deliverable:

```text
Emergency route endpoint returning ETA and route geometry
```

---

## Phase 8 — FastAPI Integration

Duration: 2 days

Small steps:

1. Create schemas.
2. Create service layer.
3. Create API router.
4. Create endpoints.
5. Test with `/docs`.
6. Return GeoJSON outputs.
7. Test emergency route POST request.

Deliverable:

```text
Working backend API
```

---

## Phase 9 — Database Integration

Duration: 2–4 days

Small steps:

1. Create PostGIS tables.
2. Insert road segments.
3. Insert zones.
4. Insert weather records.
5. Insert risk outputs.
6. Read outputs from database instead of files.
7. Keep GeoJSON export as backup.

Deliverable:

```text
PostGIS-powered module
```

---

## Phase 10 — Frontend Dashboard

Duration: 5–7 days

Small steps:

1. Create React app.
2. Add Mapbox/MapLibre.
3. Center map on Nasr City.
4. Add zone risk layer.
5. Add road delay layer.
6. Add layer toggles.
7. Add summary cards.
8. Add emergency route form.
9. Draw route on map.
10. Add popups.
11. Polish UI.

Deliverable:

```text
Interactive Nasr City risk dashboard
```

---

## Phase 11 — Satellite/Raster Upgrade

Duration: 1–2 weeks

Small steps:

1. Download SRTM elevation.
2. Calculate slope.
3. Detect low areas.
4. Download Landsat LST.
5. Calculate heat hotspots.
6. Download ESA WorldCover.
7. Calculate vegetation/built-up indicators.
8. Download GHSL built-up surface.
9. Improve flood/heat scoring.

Deliverable:

```text
Scientifically stronger flood and heat layers
```

---

## Phase 12 — Evaluation and Documentation

Duration: 3–5 days

Small steps:

1. Create scenario tests.
2. Capture screenshots.
3. Compare normal vs weather route.
4. Document formulas.
5. Document dataset sources.
6. Write limitations.
7. Write future work.
8. Prepare presentation diagrams.

Deliverable:

```text
Final report-ready module documentation
```

---

# 33. GitHub Issues to Create

Create these issues in GitHub:

## Issue 1

Title:

```text
Set up backend structure for Nasr City weather-impact module
```

Tasks:

- create folders
- add requirements
- add FastAPI main app
- add health endpoint

## Issue 2

Title:

```text
Download and validate Nasr City road network using OSMnx
```

Tasks:

- create road extraction script
- save GraphML
- save GeoJSON
- validate visually

## Issue 3

Title:

```text
Collect historical weather data for Nasr City
```

Tasks:

- create Open-Meteo collector
- save CSV
- validate date range
- check rainy hours

## Issue 4

Title:

```text
Generate analysis zones for Nasr City
```

Tasks:

- create grid
- intersect with roads
- assign zone codes
- save GeoJSON

## Issue 5

Title:

```text
Implement flood risk scoring engine v1
```

Tasks:

- rainfall score
- road density score
- zone flood score
- severity labels

## Issue 6

Title:

```text
Implement heat risk scoring engine v1
```

Tasks:

- temperature score
- built-up proxy
- heat score
- severity labels

## Issue 7

Title:

```text
Implement traffic delay engine under rainfall conditions
```

Tasks:

- rain penalty
- flood penalty
- delay factor
- adjusted travel time

## Issue 8

Title:

```text
Implement emergency route optimization endpoint
```

Tasks:

- nearest nodes
- normal route
- weather-safe route
- ETA comparison
- route geometry response

## Issue 9

Title:

```text
Expose weather-impact module through FastAPI
```

Tasks:

- summary endpoint
- zones endpoint
- roads endpoint
- emergency route endpoint

## Issue 10

Title:

```text
Build first dashboard map for Nasr City risk layers
```

Tasks:

- map setup
- zone layer
- road layer
- summary cards
- route visualization

---

# 34. Troubleshooting

## Problem: GeoPandas installation fails

Best fix:

```bash
conda install -c conda-forge geopandas osmnx
```

Avoid fighting pip on Windows if GDAL/Fiona errors appear.

---

## Problem: OSMnx cannot find Nasr City

Use the fallback bounding box script.

If needed, manually adjust:

```python
FALLBACK_BBOX = {
    "north": 30.095,
    "south": 30.015,
    "east": 31.405,
    "west": 31.285,
}
```

---

## Problem: `touch` command not found on Windows

Use PowerShell:

```powershell
New-Item path\to\file.py -ItemType File
```

---

## Problem: Docker database not initialized

Run:

```bash
docker compose down -v
docker compose up -d
```

Warning: this deletes existing DB data.

---

## Problem: API says output file missing

Run pipeline first:

```bash
python backend/app/scripts/01_download_nasr_city_roads.py
python backend/app/scripts/02_collect_weather_data.py
python backend/app/scripts/03_generate_zones.py
python backend/app/scripts/04_run_risk_pipeline.py
```

---

## Problem: Emergency route fails

Make sure this file exists:

```text
backend/app/data/nasr_city/processed/nasr_city_graph.graphml
```

If not, rerun:

```bash
python backend/app/scripts/01_download_nasr_city_roads.py
```

---

## Problem: `ModuleNotFoundError: No module named 'backend'`

Run commands from the repository root, not from inside `backend/app/scripts`.

Correct:

```bash
python backend/app/scripts/04_run_risk_pipeline.py
```

Wrong:

```bash
cd backend/app/scripts
python 04_run_risk_pipeline.py
```

Alternative fix:

```bash
set PYTHONPATH=.
```

On macOS/Linux:

```bash
export PYTHONPATH=.
```

---

# 35. What Is Ready Now and What Comes Later

## Ready Now

- backend skeleton
- road extraction
- weather data collection
- zone generation
- flood risk v1
- heat risk v1
- traffic delay v1
- emergency routing v1
- FastAPI endpoints

## Later Upgrade

- real flood validation data
- SRTM elevation integration
- Landsat LST integration
- NDVI vegetation scoring
- GHSL built-up scoring
- WorldPop exposure scoring
- PostGIS storage instead of file-based outputs
- frontend dashboard
- machine learning models
- real-time data ingestion

---

# 36. First Coding Checklist

Follow this checklist first:

```text
[ ] Create branch
[ ] Create folders
[ ] Create requirements.txt
[ ] Create .env
[ ] Start Docker PostGIS
[ ] Install Python packages
[ ] Create road extraction script
[ ] Run road extraction
[ ] Create weather collector script
[ ] Run weather collector
[ ] Create zone generator script
[ ] Run zone generator
[ ] Create flood engine
[ ] Create heat engine
[ ] Create traffic delay engine
[ ] Run risk pipeline
[ ] Create emergency route engine
[ ] Test emergency route
[ ] Create FastAPI router
[ ] Start API
[ ] Test /docs
```

---

# 37. Final Execution Command Set

After all files are created:

```bash
conda activate smartcity

docker compose up -d

python backend/app/scripts/01_download_nasr_city_roads.py
python backend/app/scripts/02_collect_weather_data.py
python backend/app/scripts/03_generate_zones.py
python backend/app/scripts/04_run_risk_pipeline.py
python backend/app/scripts/05_test_emergency_route.py

uvicorn backend.app.main:app --reload
```

Open:

```text
http://127.0.0.1:8000/docs
```

---

# 38. Final MVP Definition

The first complete MVP is done when:

1. Nasr City roads are downloaded.
2. Weather data is collected.
3. Zones are generated.
4. Flood risk is calculated.
5. Heat risk is calculated.
6. Road delay is calculated.
7. Emergency route is calculated.
8. FastAPI exposes all outputs.
9. Results can be visualized as GeoJSON.
10. The methodology is documented clearly.

At that point, you have a real working smart-city module, not just an idea.

---

# 39. Best Next Action

Start with Phase 0 and Phase 1 only.

Do not jump to heat maps, satellites, dashboard, or ML yet.

First target:

```text
Get Nasr City road network downloaded and saved successfully.
```

Once this works, everything else becomes easier.

