# 🚀 ASCIS — Autonomous Supply Chain Intelligence System

> An AI-powered system that predicts supply chain disruptions, autonomously reroutes shipments, and explains every decision using LLM reasoning.

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![LangGraph](https://img.shields.io/badge/LangGraph-Multi--Agent-green)
![Gemini](https://img.shields.io/badge/Gemini-LLM-orange)
![FastAPI](https://img.shields.io/badge/FastAPI-Backend-teal)
![Streamlit](https://img.shields.io/badge/Streamlit-Dashboard-red)

---

## 🎯 What It Does

1. **Predicts** supply chain disruptions BEFORE they occur using ML
2. **Orchestrates** multi-agent AI (LangChain + LangGraph) for autonomous decisions
3. **Reroutes** shipments automatically when risk is detected
4. **Explains** every decision using Gemini LLM in plain English
5. **Visualizes** everything in a real-time interactive dashboard

## 🏗️ Architecture

```
Streamlit Dashboard → FastAPI → LangGraph Agents → ML + NetworkX + Gemini
```

**4 Autonomous Agents:**
- 📦 **Shipment Agent** — Monitors all active shipments
- ⚠️ **Risk Agent** — Predicts disruption probability (XGBoost)
- 🗺️ **Routing Agent** — Finds optimal alternative routes (NetworkX)
- 🎯 **Coordinator** — Orchestrates the entire pipeline

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| Agent Orchestration | LangGraph + LangChain |
| ML Prediction | XGBoost + scikit-learn |
| Route Optimization | NetworkX (Dijkstra) |
| LLM Reasoning | Google Gemini |
| Backend API | FastAPI |
| Frontend | Streamlit |
| Data Models | Pydantic |

## 📁 Project Structure

```
smart_supply_chain/
├── app/                    # Streamlit frontend
├── backend/
│   ├── api/                # FastAPI routes
│   ├── core/               # Data models, config
│   ├── data/               # Data simulation
│   ├── models/             # ML models
│   ├── agents/             # LangGraph agents
│   ├── decision/           # Route optimization
│   ├── explainability/     # LLM explanations
│   └── services/           # Business logic
├── data/                   # Saved models, datasets
├── docs/                   # Documentation
├── tests/                  # Test suite
├── requirements.txt
├── run.py                  # Entry point
└── README.md
```

## 🚀 Quick Start

```bash
# 1. Clone & setup
git clone https://github.com/your-repo/smart_supply_chain.git
cd smart_supply_chain
python -m venv venv && venv\Scripts\activate
pip install -r requirements.txt

# 2. Set environment
echo GEMINI_API_KEY=your_key > .env

# 3. Run
python run.py
```

## 🎬 Demo

The system demonstrates:
- Shipments moving across India's supply chain network
- Real-time risk detection from weather/traffic
- Autonomous rerouting with AI explanation
- Multi-agent coordination visible in dashboard

## 📄 Documentation

- [Architecture](docs/architecture.md)
- [Agent Design](docs/agents.md)
- [API Reference](docs/api.md)
- [Setup Guide](docs/setup.md)
- [Roadmap](docs/roadmap.md)
- [Skills & Resources](docs/skills_and_resources.md)

## 📜 License

MIT License