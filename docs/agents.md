# 🤖 ASCIS — Agent Documentation

## Agent Overview

The system uses 4 specialized agents orchestrated via LangGraph:

| Agent | Role | Trigger |
|-------|------|---------|
| Coordinator | Orchestrate workflow | Every cycle |
| Shipment | Monitor shipments | Every cycle |
| Risk Analysis | Assess disruption risk | Every cycle |
| Routing | Reroute shipments | When risk > 0.6 |

## Shared State Schema

All agents operate on a shared `SupplyChainState` TypedDict. Each agent reads what it needs and returns partial updates.

## Communication Pattern

Agents do NOT communicate directly. All communication flows through the shared state managed by LangGraph. The Coordinator decides execution order via conditional edges.

## Agent Lifecycle

1. Coordinator evaluates current state
2. Shipment Agent updates positions/statuses
3. Risk Agent computes risk scores
4. If high risk detected → Routing Agent activates
5. Explainer generates reasoning
6. State is checkpointed for history
