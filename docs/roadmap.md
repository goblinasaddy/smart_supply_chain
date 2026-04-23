# 📅 ASCIS — Execution Roadmap

> Hackathon-optimized, phase-by-phase plan. MVP first, then enhancements.

---

## Overview

| Phase | Focus | Priority |
|-------|-------|----------|
| Phase 1 | Data Layer + Simulation | 🔴 Critical |
| Phase 2 | ML Prediction Engine | 🔴 Critical |
| Phase 3 | Graph + Routing Engine | 🔴 Critical |
| Phase 4 | Multi-Agent System (LangGraph) | 🔴 Critical |
| Phase 5 | LLM Explainability | 🟡 High |
| Phase 6 | FastAPI Backend | 🟡 High |
| Phase 7 | Streamlit Dashboard | 🔴 Critical |
| Phase 8 | Integration + Polish | 🟡 High |

---

## Phase 1: Data Layer + Simulation (Day 1 Morning)

**Goal:** Generate realistic supply chain data and external signals.

### Tasks:
1. ✅ Create supply chain network (10+ cities, 20+ routes)
2. ✅ Build shipment data generator
3. ✅ Build weather simulation module
4. ✅ Build traffic simulation module
5. ✅ Create data models (Pydantic)

### Files:
- `backend/data/supply_chain_network.py` — Graph definition
- `backend/data/shipment_generator.py` — Synthetic shipment creation
- `backend/data/weather_simulator.py` — Weather data simulation
- `backend/data/traffic_simulator.py` — Traffic data simulation
- `backend/core/models.py` — Pydantic data models

### Definition of Done:
- Can generate 50+ shipments with realistic parameters
- Weather/traffic data for all network nodes
- All data models validated with Pydantic

---

## Phase 2: ML Prediction Engine (Day 1 Afternoon)

**Goal:** Train a model that predicts shipment disruption risk.

### Tasks:
1. ✅ Generate synthetic training dataset (10K+ samples)
2. ✅ Feature engineering pipeline
3. ✅ Train XGBoost classifier
4. ✅ Evaluate and save model
5. ✅ Create prediction service

### Files:
- `backend/models/training_data_generator.py`
- `backend/models/risk_predictor.py`
- `backend/models/model_trainer.py`
- `data/models/risk_model.joblib`

### Definition of Done:
- F1 > 0.80 on test set
- Model saved and loadable
- Prediction function returns risk score for any shipment

---

## Phase 3: Graph + Routing Engine (Day 1 Evening)

**Goal:** Build the route optimization system.

### Tasks:
1. ✅ Create NetworkX graph from supply chain network
2. ✅ Implement dynamic weight calculation
3. ✅ Implement Dijkstra-based routing
4. ✅ Add rerouting with risk penalties
5. ✅ Compare original vs alternative routes

### Files:
- `backend/decision/route_optimizer.py`
- `backend/decision/graph_builder.py`

### Definition of Done:
- Can find shortest path between any two nodes
- Can reroute when given risk data
- Route comparison (cost, time, risk) working

---

## Phase 4: Multi-Agent System (Day 2 Morning)

**Goal:** Implement LangGraph-based agent orchestration.

### Tasks:
1. ✅ Define shared state schema
2. ✅ Implement Shipment Agent
3. ✅ Implement Risk Analysis Agent
4. ✅ Implement Routing Agent
5. ✅ Implement Coordinator Agent
6. ✅ Build StateGraph with conditional edges
7. ✅ Add memory/checkpointing

### Files:
- `backend/agents/state.py` — State schema
- `backend/agents/shipment_agent.py`
- `backend/agents/risk_agent.py`
- `backend/agents/routing_agent.py`
- `backend/agents/coordinator.py`
- `backend/agents/graph.py` — LangGraph orchestration

### Definition of Done:
- Full agent pipeline executes end-to-end
- Conditional routing works (reroute only on high risk)
- State updates correctly across all agents

---

## Phase 5: LLM Explainability (Day 2 Afternoon)

**Goal:** Generate human-readable explanations for every AI decision.

### Tasks:
1. ✅ Design explanation prompts
2. ✅ Implement Gemini integration
3. ✅ Create explanation service
4. ✅ Structured output parsing

### Files:
- `backend/explainability/prompts.py`
- `backend/explainability/explainer.py`

### Definition of Done:
- Every reroute decision gets an LLM explanation
- Explanations are structured (summary, factors, reasoning)
- Graceful fallback if LLM unavailable

---

## Phase 6: FastAPI Backend (Day 2 Evening)

**Goal:** REST API exposing all system capabilities.

### Tasks:
1. ✅ Define API routes
2. ✅ Implement endpoints
3. ✅ Add WebSocket for real-time updates
4. ✅ Error handling and validation

### Files:
- `backend/api/routes.py`
- `backend/api/schemas.py`
- `backend/services/simulation_service.py`

### Key Endpoints:
| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/api/shipments` | List all shipments |
| GET | `/api/shipments/{id}` | Get shipment details |
| POST | `/api/simulation/start` | Start simulation |
| POST | `/api/simulation/disrupt` | Inject disruption |
| GET | `/api/network` | Get supply chain graph |
| GET | `/api/decisions` | Get decision history |
| WS | `/ws/updates` | Real-time stream |

### Definition of Done:
- All endpoints working and documented
- Swagger UI accessible at `/docs`

---

## Phase 7: Streamlit Dashboard (Day 3 Morning)

**Goal:** Interactive visualization of the entire system.

### Tasks:
1. ✅ Main dashboard layout
2. ✅ Map visualization (routes + shipments)
3. ✅ KPI cards
4. ✅ Shipment table with risk coloring
5. ✅ AI explanation panel
6. ✅ Simulation controls
7. ✅ Risk timeline chart

### Files:
- `app/streamlit_app.py`
- `app/components/` — Reusable UI components

### Definition of Done:
- Dashboard loads with real data
- Map shows routes and shipments
- Disruption injection works from UI
- AI explanations display correctly

---

## Phase 8: Integration + Polish (Day 3 Afternoon)

**Goal:** End-to-end system working, demo-ready.

### Tasks:
1. ✅ End-to-end simulation loop
2. ✅ Error handling
3. ✅ Demo script preparation
4. ✅ README finalization
5. ✅ Code cleanup

### Definition of Done:
- 3-minute demo runs without errors
- System handles multiple simultaneous disruptions
- All components communicate correctly

---

## MVP vs Full Feature Set

| Feature | MVP | Full |
|---------|-----|------|
| Simulated data | ✅ | ✅ |
| ML risk prediction | ✅ | ✅ |
| Graph routing | ✅ | ✅ |
| Multi-agent (LangGraph) | ✅ | ✅ |
| LLM explanations | ✅ | ✅ |
| Streamlit dashboard | ✅ | ✅ |
| Real weather API | ❌ | ✅ |
| WebSocket real-time | ❌ | ✅ |
| Historical analytics | ❌ | ✅ |
| Multi-modal transport | ❌ | ✅ |
| Notification system | ❌ | ✅ |

---

## Risk Mitigation

| Risk | Mitigation |
|------|-----------|
| LLM API rate limits | Cache explanations, batch requests |
| Model accuracy too low | Use simulated data tuned to patterns |
| LangGraph complexity | Start with linear flow, add conditionals later |
| Time pressure | MVP first, enhance incrementally |
