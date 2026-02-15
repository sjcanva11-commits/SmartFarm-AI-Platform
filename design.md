# SmartFarm AI - System Design Document

## 1. Overview

SmartFarm AI is a cloud-based, AI-driven agricultural platform that integrates:

- Mobile & Web applications
- AI/ML services (disease detection, price prediction, crop planning)
- IoT-based irrigation automation
- Satellite data ingestion and analytics
- Subscription-based feature control (Free vs Premium)

The system follows a microservices architecture deployed on a scalable cloud infrastructure.

---

# 2. High-Level System Architecture

## 2.1 Architecture Overview

Mobile App (Android / iOS) -> Web Dashboard -> API Gateway -> 
| Authentication Service |
| Subscription Service |
| Crop Planning Service |
| Disease Detection Service (AI) |
| Market Prediction Service (ML) |
| Irrigation Service (IoT Control) |
| Satellite Monitoring Service |
| Voice Assistant Service |->
| PostgreSQL (Relational Database) |
| Time-Series Database (IoT & NDVI Data) |
| Object Storage (Images & Satellite Data) |
| ML Model Registry |


---

# 3. Component Design

## 3.1 Mobile Application

### Responsibilities
- Image capture & upload
- Voice input/output
- Field visualization (maps & heatmaps)
- Irrigation control interface
- Market price dashboard
- Subscription management

### Technology Options
- Flutter / React Native (cross-platform)
- Offline-first support (local storage)
- Push notifications

---

## 3.2 API Gateway

### Responsibilities
- Route requests to services
- Handle authentication
- Rate limiting (Free tier restrictions)
- Logging and monitoring

---

# 4. Backend Services Design

## 4.1 Authentication Service
- JWT-based authentication
- OAuth2 support
- Role-based access control

---

## 4.2 Subscription Service

### Responsibilities
- Manage subscription tiers
- Enforce feature access rules
- Track usage limits

### Tier Logic

#### Free Tier
- Limited disease scans per month (e.g., 20)
- Basic irrigation recommendations
- 7-day price forecast
- Standard satellite resolution
- Basic voice assistant

#### Premium Tier
- Unlimited disease scans
- Automated irrigation control
- 30+ day price forecasting
- Profit simulation tools
- High-resolution satellite analytics
- Advanced voice assistant memory

Feature access validation pseudocode:

if user.subscription == "FREE":
check_usage_limit(feature)
restrict_premium_features()
else:
allow_full_access()


---

## 4.3 Disease AI Service

### Model Architecture
- Convolutional Neural Network (CNN)
- Transfer learning (e.g., EfficientNet/ResNet)
- Multi-class classification model

### Flow

User Image Upload -> Preprocessing (Resize, Normalize) -> CNN Model Inference -> Disease Label + Confidence Score -> Treatment Recommendation Engine 


### Deployment
- Containerized model (Docker)
- Served via REST endpoint
- GPU-enabled inference server (optional)

---

## 4.4 Market Price Prediction Service

### Model Type
- Time-series forecasting:
  - LSTM
  - Prophet
  - ARIMA (baseline)

### Input Features
- Historical crop prices
- Seasonal trends
- Market demand indicators
- Weather impact variables

### Output
- Predicted price
- Confidence interval
- Trend direction

---

## 4.5 Irrigation Service (IoT Integration)

### Components

Soil Moisture Sensors -> IoT Gateway Device -> Cloud IoT Broker (MQTT) -> Irrigation Service -> Water Valve Controller


### Functionality
- Collect real-time soil moisture data
- Trigger irrigation events
- Support manual override
- Log irrigation activity

### Automation Logic

if soil_moisture < threshold AND rain_forecast == false:
trigger_irrigation()
---

## 4.6 Satellite Monitoring Service

### Data Sources
- Satellite imagery provider APIs
- NDVI calculation

### Data Flow
Satellite API -> Image Ingestion Pipeline -> NDVI Processing -> Field Health Map Generation -> Alert Engine


### Alerts
- Drought stress
- Waterlogging
- Crop stress zones

---

## 4.7 Voice Assistant Service

### Components
- Speech-to-Text Engine
- Natural Language Understanding (NLU)
- Intent Classification
- Text-to-Speech Engine

### Query Flow

Voice Input -> Speech-to-Text -> Intent Detection -> Call Relevant Service -> Text-to-Speech Response


---

# 5. Database Design

## 5.1 Relational Database (PostgreSQL)

### Tables

#### Users
- id (UUID)
- name
- email
- phone
- password_hash
- subscription_id
- created_at

#### Subscriptions
- id
- user_id
- tier (FREE / PREMIUM)
- start_date
- end_date
- usage_limits

#### Fields
- id
- user_id
- location (GeoJSON)
- soil_type
- area_size

#### Crops
- id
- field_id
- crop_type
- planting_date
- growth_stage

#### Disease_Reports
- id
- field_id
- image_url
- disease_name
- confidence_score
- severity
- created_at

#### Market_Prices
- id
- crop_type
- market_region
- price
- date

---

## 5.2 Time-Series Database

### Irrigation_Logs
- field_id
- soil_moisture
- irrigation_triggered (boolean)
- timestamp

### Satellite_Health_Index
- field_id
- ndvi_value
- timestamp

---

## 5.3 Object Storage

- Uploaded images
- Satellite imagery
- Model artifacts

---

# 6. API Design

## 6.1 Authentication

POST /api/auth/register  
POST /api/auth/login  

---

## 6.2 Disease Detection

POST /api/disease/analyze  
GET  /api/disease/history  

---

## 6.3 Irrigation

GET  /api/irrigation/status  
POST /api/irrigation/manual-trigger  
GET  /api/irrigation/history  

---

## 6.4 Market Prediction

GET /api/market/predict?crop=rice&days=7  

---

## 6.5 Crop Planning

POST /api/crop/recommend  
POST /api/crop/rotation-plan  

---

## 6.6 Satellite Monitoring

GET /api/satellite/health-map  
GET /api/satellite/history  

---

## 6.7 Subscription

GET  /api/subscription/status  
POST /api/subscription/upgrade  

---

# 7. Scalability & Deployment

## 7.1 Infrastructure
- Cloud provider (AWS / Azure / GCP)
- Kubernetes for orchestration
- CI/CD pipeline
- Auto-scaling groups

## 7.2 Model Lifecycle
- Model training pipeline
- Model registry
- A/B testing for model versions
- Continuous retraining with new data

---

# 8. Security Considerations

- HTTPS enforced
- Data encryption at rest
- Secure IoT communication (TLS)
- Role-based access control
- Audit logging

---

# 9. Monitoring & Observability

- Centralized logging
- Metrics (CPU, memory, API latency)
- Alerting for irrigation failures
- Model accuracy tracking

---

# 10. Future Enhancements

- Drone image integration
- Blockchain-based produce traceability
- Crop insurance risk scoring
- Multi-farm enterprise dashboards

---

# End of Design Document
