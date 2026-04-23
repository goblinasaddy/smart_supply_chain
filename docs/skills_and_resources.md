# 📘 ASCIS — Skills & Resources Master Guide

> **Autonomous Supply Chain Intelligence System**
> Complete reference for every concept, framework, API, and pattern used in this project.

---

## 1. Core Concepts

### 1.1 Supply Chain Optimization

A supply chain is a network of entities (suppliers, warehouses, distributors, retailers) connected by transportation routes. Optimization means minimizing cost, time, and risk while maximizing reliability.

**Key metrics we track:**
- **Lead Time** — Time from order to delivery
- **On-Time Delivery Rate (OTDR)** — % of shipments arriving on schedule
- **Disruption Frequency** — How often routes are impacted by weather/traffic
- **Cost per Shipment** — Transportation + delay penalties
- **Risk Score** — Composite score (0–1) representing disruption likelihood

**Optimization levers:**
- Route selection (shortest vs safest)
- Carrier assignment
- Inventory pre-positioning
- Dynamic rerouting on disruption detection

### 1.2 Graph Theory Basics

The supply chain is modeled as a **weighted directed graph**:
- **Nodes** = Locations (warehouses, ports, distribution centers)
- **Edges** = Transportation routes between locations
- **Weights** = Cost, time, or risk score

**Algorithms used:**
| Algorithm | Purpose | Complexity |
|-----------|---------|------------|
| Dijkstra's | Shortest path (single source) | O((V+E) log V) |
| A* Search | Heuristic shortest path | O(E) best case |
| Bellman-Ford | Handles negative weights | O(VE) |
| Floyd-Warshall | All-pairs shortest path | O(V³) |

We primarily use **Dijkstra's** with dynamic weight updates based on real-time risk.

### 1.3 Multi-Agent Systems (MAS)

A MAS consists of autonomous agents that:
- Perceive their environment (shared state)
- Make decisions based on their role
- Act to modify the shared state
- Coordinate through a central orchestrator

**Our agent topology:** Star topology with Coordinator as the hub.

```
         ┌──────────────┐
         │  Coordinator  │
         │    Agent      │
         └──────┬───────┘
        ┌───────┼───────┐
        ▼       ▼       ▼
   ┌────────┐ ┌─────┐ ┌───────┐
   │Shipment│ │Risk │ │Routing│
   │ Agent  │ │Agent│ │ Agent │
   └────────┘ └─────┘ └───────┘
```

### 1.4 Explainable AI (XAI)

XAI ensures that AI decisions are transparent and auditable. In supply chain:
- **Why** was a shipment rerouted?
- **What** risk factors triggered the decision?
- **How** confident is the prediction?

We use **LLM-powered explanations** (Gemini) that take structured decision data and produce human-readable narratives.

---

## 2. Frameworks & Tools

### 2.1 LangChain

**Version:** ≥ 0.3.x
**Purpose:** LLM interface, tool binding, prompt management

**Key patterns:**
```python
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage, SystemMessage

# Initialize LLM
llm = ChatGoogleGenerativeAI(model="gemini-2.0-flash", temperature=0.3)

# Define a tool
@tool
def get_shipment_status(shipment_id: str) -> dict:
    """Get current status of a shipment."""
    return {"id": shipment_id, "status": "in_transit", "eta": "2024-03-15"}

# Bind tools to LLM
llm_with_tools = llm.bind_tools([get_shipment_status])
```

### 2.2 LangGraph

**Version:** ≥ 0.2.x
**Purpose:** Stateful multi-agent orchestration with graph-based control flow

**Key patterns:**
```python
from langgraph.graph import StateGraph, START, END
from typing import TypedDict, Annotated
import operator

class SupplyChainState(TypedDict):
    shipments: list
    risk_scores: dict
    routes: dict
    decisions: Annotated[list, operator.add]
    explanations: list

# Build graph
graph = StateGraph(SupplyChainState)
graph.add_node("shipment_agent", shipment_agent_fn)
graph.add_node("risk_agent", risk_agent_fn)
graph.add_node("routing_agent", routing_agent_fn)
graph.add_node("coordinator", coordinator_fn)

# Define edges
graph.add_edge(START, "coordinator")
graph.add_conditional_edges("coordinator", route_decision)
graph.add_edge("shipment_agent", "risk_agent")
graph.add_edge("risk_agent", "routing_agent")
graph.add_edge("routing_agent", "coordinator")
```

### 2.3 FastAPI

**Purpose:** REST API layer for frontend-backend communication

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI(title="ASCIS API", version="1.0.0")

class ShipmentRequest(BaseModel):
    origin: str
    destination: str
    cargo_type: str
    priority: str

@app.post("/api/shipments/")
async def create_shipment(req: ShipmentRequest):
    ...
```

### 2.4 Streamlit

**Purpose:** Interactive dashboard for visualization

Key components: `st.map()`, `st.plotly_chart()`, `st.metric()`, `st.dataframe()`

### 2.5 NetworkX

**Purpose:** Graph-based route modeling and optimization

```python
import networkx as nx

G = nx.DiGraph()
G.add_edge("Mumbai", "Delhi", weight=1400, risk=0.2, time_hrs=24)
G.add_edge("Delhi", "Kolkata", weight=1500, risk=0.3, time_hrs=28)

# Find optimal route
path = nx.dijkstra_path(G, "Mumbai", "Kolkata", weight="weight")
```

### 2.6 Scikit-learn / XGBoost

**Purpose:** ML models for risk prediction

We use **XGBoost** for its gradient-boosted decision trees — ideal for tabular risk prediction with mixed features.

### 2.7 Gemini API

**Purpose:** LLM-powered decision explanations

```python
from langchain_google_genai import ChatGoogleGenerativeAI

llm = ChatGoogleGenerativeAI(
    model="gemini-2.0-flash",
    google_api_key="YOUR_KEY",
    temperature=0.3
)
```

---

## 3. APIs & Data Sources

### 3.1 Weather API (OpenWeatherMap)

```
GET https://api.openweathermap.org/data/2.5/weather
?q={city}&appid={API_KEY}&units=metric
```

**Response fields used:**
- `weather[0].main` — Condition (Rain, Storm, Clear)
- `main.temp` — Temperature
- `wind.speed` — Wind speed
- `main.humidity` — Humidity

**Risk mapping:**
| Condition | Risk Multiplier |
|-----------|----------------|
| Clear | 1.0 |
| Clouds | 1.1 |
| Rain | 1.5 |
| Thunderstorm | 2.5 |
| Snow | 2.0 |
| Fog | 1.8 |

### 3.2 Simulation Strategy (Primary Approach)

For hackathon reliability, we **simulate** external data:

```python
def simulate_weather(city: str, seed: int = None) -> dict:
    conditions = ["Clear", "Rain", "Storm", "Fog", "Snow"]
    weights = [0.4, 0.25, 0.1, 0.15, 0.1]
    return {
        "city": city,
        "condition": random.choices(conditions, weights)[0],
        "temperature": random.uniform(-5, 45),
        "wind_speed": random.uniform(0, 80),
        "humidity": random.uniform(20, 100)
    }
```

### 3.3 Traffic Simulation

```python
def simulate_traffic(route: str) -> dict:
    congestion_levels = ["low", "moderate", "high", "severe"]
    return {
        "route": route,
        "congestion": random.choice(congestion_levels),
        "delay_hours": random.uniform(0, 12),
        "accident_reported": random.random() < 0.1
    }
```

---

## 4. Agent Design Guide

### 4.1 Shipment Agent

| Property | Detail |
|----------|--------|
| **Role** | Monitor and manage active shipments |
| **Input State** | `shipments: list[Shipment]` |
| **Output State** | Updated shipments with current status, location, ETA |
| **Decision Logic** | Check each shipment's progress against schedule. Flag delays. |

**Example behavior:**
```
Input: Shipment #42 — Mumbai → Delhi, departed 6hrs ago, ETA 24hrs
Output: Shipment #42 — Status: ON_TRACK, Progress: 25%, Current: Nashik
```

### 4.2 Risk Analysis Agent

| Property | Detail |
|----------|--------|
| **Role** | Assess risk for each active shipment's route |
| **Input State** | `shipments`, `weather_data`, `traffic_data` |
| **Output State** | `risk_scores: dict[shipment_id, RiskAssessment]` |
| **Decision Logic** | Combine weather risk + traffic risk + historical delay rate. ML model predicts probability of disruption. |

**Risk Score Formula:**
```
risk_score = (0.4 × weather_risk + 0.3 × traffic_risk + 0.3 × ml_prediction)
```

**Thresholds:**
- `< 0.3` → LOW risk (no action)
- `0.3 – 0.6` → MEDIUM risk (monitor closely)
- `0.6 – 0.8` → HIGH risk (prepare alternative route)
- `> 0.8` → CRITICAL (trigger immediate reroute)

### 4.3 Routing Agent

| Property | Detail |
|----------|--------|
| **Role** | Find optimal routes; reroute when risk is high |
| **Input State** | `risk_scores`, `supply_chain_graph`, `shipments` |
| **Output State** | `routes: dict[shipment_id, Route]`, `decisions: list[Decision]` |
| **Decision Logic** | For HIGH/CRITICAL risk: compute alternative routes using Dijkstra with updated edge weights (penalized by risk). Select route minimizing `cost × risk_factor`. |

**Example behavior:**
```
Input: Shipment #42, risk=0.85 (CRITICAL), current route: Mumbai→Nashik→Delhi
Action: Reroute via Mumbai→Pune→Indore→Delhi (risk=0.35, +2hrs, -$200 risk cost)
```

### 4.4 Coordinator Agent

| Property | Detail |
|----------|--------|
| **Role** | Orchestrate all agents, decide execution flow, manage history |
| **Input State** | Full `SupplyChainState` |
| **Output State** | Updated state + execution plan for next cycle |
| **Decision Logic** | 1) Always run Shipment Agent first. 2) Run Risk Agent. 3) If any risk > threshold → run Routing Agent. 4) Generate explanations for all decisions. 5) Log to history. |

**Conditional routing logic:**
```python
def route_decision(state: SupplyChainState) -> str:
    high_risk = any(r.score > 0.6 for r in state["risk_scores"].values())
    if high_risk:
        return "routing_agent"
    return END
```

---

## 5. LangGraph Deep Dive

### 5.1 StateGraph Design

```python
from langgraph.graph import StateGraph, START, END
from typing import TypedDict, Annotated
import operator

class SupplyChainState(TypedDict):
    # Core data
    shipments: list                    # Active shipments
    weather_data: dict                 # City → weather info
    traffic_data: dict                 # Route → traffic info

    # Agent outputs
    risk_scores: dict                  # Shipment ID → risk assessment
    routes: dict                       # Shipment ID → optimal route
    decisions: Annotated[list, operator.add]  # Append-only decision log

    # Explainability
    explanations: list                 # LLM-generated explanations

    # Meta
    cycle_count: int                   # Simulation cycle number
    timestamp: str                     # Current simulation time
```

### 5.2 Node Creation

Each agent is a **node function** that receives state and returns partial state updates:

```python
def shipment_agent(state: SupplyChainState) -> dict:
    updated_shipments = process_shipments(state["shipments"])
    return {"shipments": updated_shipments}

def risk_agent(state: SupplyChainState) -> dict:
    scores = assess_risks(state["shipments"], state["weather_data"])
    return {"risk_scores": scores}
```

### 5.3 Conditional Edges

```python
def should_reroute(state: SupplyChainState) -> str:
    """Decide whether to invoke routing agent."""
    for sid, assessment in state["risk_scores"].items():
        if assessment["score"] > 0.6:
            return "routing_agent"
    return "explain"

graph.add_conditional_edges(
    "risk_agent",
    should_reroute,
    {"routing_agent": "routing_agent", "explain": "explainer"}
)
```

### 5.4 Execution Flow

```
START → coordinator → shipment_agent → risk_agent
                                          │
                                    ┌─────┴─────┐
                                    ▼            ▼
                              routing_agent   explainer
                                    │            │
                                    └─────┬──────┘
                                          ▼
                                     coordinator → END
```

### 5.5 Memory Handling

```python
from langgraph.checkpoint.memory import MemorySaver

memory = MemorySaver()
app = graph.compile(checkpointer=memory)

# Run with thread-based memory
config = {"configurable": {"thread_id": "simulation-1"}}
result = app.invoke(initial_state, config)
```

---

## 6. ML Model Guide

### 6.1 Features

| Feature | Type | Source |
|---------|------|--------|
| `weather_severity` | float (0–1) | Weather API / simulation |
| `wind_speed` | float (km/h) | Weather API / simulation |
| `temperature` | float (°C) | Weather API / simulation |
| `traffic_congestion` | categorical | Traffic simulation |
| `distance_km` | float | Graph edge weight |
| `cargo_type` | categorical | Shipment data |
| `time_of_day` | int (0–23) | Simulation clock |
| `day_of_week` | int (0–6) | Simulation clock |
| `historical_delay_rate` | float | Historical data |
| `route_complexity` | int | Number of intermediate stops |

### 6.2 Target Variable

`is_disrupted` — Binary (0/1): Will this shipment be disrupted?

Secondary target: `delay_hours` — Regression: How many hours delayed?

### 6.3 Training Approach

1. **Generate synthetic training data** (10,000+ samples)
2. **Feature engineering** — encode categoricals, normalize numerics
3. **Train XGBoost classifier** for disruption prediction
4. **Train XGBoost regressor** for delay estimation
5. **Cross-validate** with 5-fold stratified CV
6. **Save model** with joblib

```python
from xgboost import XGBClassifier
from sklearn.model_selection import cross_val_score

model = XGBClassifier(
    n_estimators=200,
    max_depth=6,
    learning_rate=0.1,
    random_state=42
)
scores = cross_val_score(model, X, y, cv=5, scoring='f1')
```

### 6.4 Evaluation Metrics

| Metric | Target |
|--------|--------|
| F1 Score | > 0.80 |
| AUC-ROC | > 0.85 |
| Precision | > 0.75 |
| Recall | > 0.85 |

---

## 7. Routing Logic

### 7.1 Graph Representation

```python
supply_chain_graph = {
    "nodes": [
        {"id": "MUM", "name": "Mumbai", "type": "port", "lat": 19.07, "lon": 72.87},
        {"id": "DEL", "name": "Delhi", "type": "warehouse", "lat": 28.61, "lon": 77.20},
        {"id": "BLR", "name": "Bangalore", "type": "distribution", "lat": 12.97, "lon": 77.59},
        # ... more nodes
    ],
    "edges": [
        {"from": "MUM", "to": "DEL", "distance_km": 1400, "base_time_hrs": 24, "cost": 5000},
        # ... more edges
    ]
}
```

### 7.2 Dynamic Weight Calculation

```python
def compute_edge_weight(edge: dict, risk: float) -> float:
    """Combine distance, time, cost, and risk into a single weight."""
    risk_penalty = 1 + (risk * 3)  # risk=0.8 → 3.4x penalty
    return (
        0.3 * edge["distance_km"] / 1000 +
        0.3 * edge["base_time_hrs"] / 24 +
        0.2 * edge["cost"] / 10000 +
        0.2 * risk_penalty
    )
```

### 7.3 Shortest Path with Constraints

```python
def find_optimal_route(graph, origin, destination, risk_data):
    G = nx.DiGraph()
    for edge in graph["edges"]:
        risk = risk_data.get(f"{edge['from']}-{edge['to']}", 0.0)
        weight = compute_edge_weight(edge, risk)
        G.add_edge(edge["from"], edge["to"], weight=weight)

    path = nx.dijkstra_path(G, origin, destination, weight="weight")
    cost = nx.dijkstra_path_length(G, origin, destination, weight="weight")
    return {"path": path, "cost": cost}
```

---

## 8. LLM Integration

### 8.1 Prompt Engineering for Explanations

```python
EXPLANATION_PROMPT = """You are an AI supply chain analyst. Explain the following 
decision in clear, professional language.

**Decision:** {decision_type}
**Shipment:** {shipment_id} ({origin} → {destination})
**Risk Level:** {risk_level} (Score: {risk_score:.2f})
**Factors:**
{risk_factors}

**Action Taken:** {action}
**New Route:** {new_route}
**Impact:** {impact}

Provide:
1. A one-sentence summary
2. Key risk factors that triggered this decision
3. Why the chosen alternative is better
4. Expected outcome

Keep it concise and actionable. Use bullet points."""
```

### 8.2 Structured Output

```python
from pydantic import BaseModel

class DecisionExplanation(BaseModel):
    summary: str
    risk_factors: list[str]
    reasoning: str
    expected_outcome: str
    confidence: float
```

---

## 9. Frontend Design

### 9.1 Streamlit Layout

```
┌──────────────────────────────────────────┐
│  🚀 ASCIS — Supply Chain Intelligence    │
├──────────┬───────────────────────────────┤
│ Sidebar  │  Main Dashboard              │
│          │  ┌───────────────────────┐    │
│ Controls │  │   Map Visualization   │    │
│          │  └───────────────────────┘    │
│ Filters  │  ┌─────┬─────┬─────┬────┐   │
│          │  │ KPI │ KPI │ KPI │ KPI│   │
│ Actions  │  └─────┴─────┴─────┴────┘   │
│          │  ┌───────────────────────┐    │
│          │  │  Active Shipments     │    │
│          │  │  Table                │    │
│          │  └───────────────────────┘    │
│          │  ┌───────────────────────┐    │
│          │  │  AI Explanations      │    │
│          │  └───────────────────────┘    │
└──────────┴───────────────────────────────┘
```

### 9.2 Key Components

- **Map** — Plotly/Folium map showing routes + shipments
- **KPI Cards** — Active shipments, avg risk, reroutes today, on-time %
- **Shipment Table** — Sortable, filterable, color-coded by risk
- **Risk Timeline** — Line chart of risk scores over time
- **AI Explanation Panel** — Expandable cards with LLM explanations
- **Simulation Controls** — Start/stop, speed, inject disruption

### 9.3 Real-Time Updates

Use `st.rerun()` with `time.sleep()` for simulation loop, or Streamlit's `st.fragment` for partial reruns.

---

## 10. Demo Strategy

### 10.1 Simulating Disruptions

1. **Weather Event** — Inject a storm on a major route
2. **Traffic Jam** — Simulate severe congestion
3. **Multi-cascade** — Weather + traffic on same corridor

### 10.2 Demo Flow (3 minutes)

| Time | Action | What Audience Sees |
|------|--------|-------------------|
| 0:00 | Start simulation | Shipments moving on map |
| 0:30 | System detects rising risk | Risk scores turn yellow/red |
| 1:00 | AI triggers reroute | Route animation changes, notification appears |
| 1:30 | LLM explanation | AI explanation card expands with reasoning |
| 2:00 | Show before/after metrics | Cost/time comparison chart |
| 2:30 | Inject another disruption | System handles it autonomously |
| 3:00 | Summary dashboard | Overall system performance |

### 10.3 Wow Factors

- **Autonomous decision-making** — No human intervention needed
- **Real-time visualization** — Shipments move on the map
- **AI reasoning** — LLM explains every decision in plain English
- **Predictive, not reactive** — System acts BEFORE disruption hits
- **Multi-agent coordination** — Show the agent communication graph
