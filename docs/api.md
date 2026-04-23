# 📡 ASCIS — API Documentation

## Base URL
`http://localhost:8000`

## Endpoints

### Shipments
- `GET /api/shipments` — List all active shipments
- `GET /api/shipments/{id}` — Get shipment details
- `POST /api/shipments` — Create a new shipment

### Simulation
- `POST /api/simulation/start` — Start simulation loop
- `POST /api/simulation/stop` — Stop simulation
- `POST /api/simulation/step` — Run single cycle
- `POST /api/simulation/disrupt` — Inject a disruption

### Network
- `GET /api/network` — Get supply chain graph
- `GET /api/network/routes/{origin}/{destination}` — Get optimal route

### Decisions
- `GET /api/decisions` — Get all decision history
- `GET /api/decisions/{id}` — Get decision details with explanation

### System
- `GET /api/health` — Health check
- `GET /api/stats` — System statistics
