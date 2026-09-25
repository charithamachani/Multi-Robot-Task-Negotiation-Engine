# 🤖 Swarm Intelligence — Decentralized Multi-Robot Simulation

A **production-grade** simulation of 500+ autonomous warehouse robots using **swarm intelligence**, featuring decentralized task negotiation, reciprocal collision avoidance, mesh networking, and a stunning real-time dashboard.

> **No central server. No bottlenecks. Pure swarm coordination.**

---

## ✨ Key Features

| Feature | Algorithm | Description |
|:--------|:----------|:------------|
| **Task Allocation** | CBBA (Consensus-Based Bundle Algorithm) | Decentralized auction where robots bid on tasks based on distance, battery, and capability |
| **Collision Avoidance** | ORCA (Optimal Reciprocal Collision Avoidance) | Each robot computes collision-free velocities in milliseconds using half-plane constraints |
| **Communication** | Gossip Protocol over Mesh Network | P2P messaging within 15m radius with simulated latency (100–500ms) and 5% packet loss |
| **Deadlock Resolution** | Priority-Based Yielding | Stuck robots yield to wait zones based on battery level and task priority |
| **Failure Recovery** | Heartbeat Detection + Re-Auction | Dead robots' tasks are automatically re-auctioned to nearby survivors |
| **Battery Management** | Self-Scheduling Charge Tasks | Low-battery robots bid for charging station slots against other low-battery robots |
| **Heterogeneous Swarm** | 100 Heavy Lifters + 400 Scouts | Negotiation engine handles different speeds, capacities, and task compatibility |

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────┐
│                  Dashboard (Browser)              │
│  ┌─────────┐  ┌──────────────┐  ┌─────────────┐ │
│  │ Metrics  │  │ Canvas 2D    │  │  Inspector   │ │
│  │ Panel    │  │ Renderer     │  │  Panel       │ │
│  └─────────┘  └──────────────┘  └─────────────┘ │
│          ↕ WebSocket (15 FPS)                     │
├──────────────────────────────────────────────────┤
│              FastAPI Server                       │
│          ↕ Engine State Snapshots                 │
├──────────────────────────────────────────────────┤
│            Simulation Engine (60 FPS)             │
│  ┌──────┐  ┌──────┐  ┌──────┐  ┌─────────────┐ │
│  │ ORCA │  │ CBBA │  │Gossip│  │ World/Tasks  │ │
│  │      │  │      │  │      │  │              │ │
│  └──────┘  └──────┘  └──────┘  └─────────────┘ │
└──────────────────────────────────────────────────┘
```

---

## 🚀 Quick Start

### 1. Install Dependencies

```bash
cd swarm-robotics
pip install -r requirements.txt
```

### 2. Run the Simulation

```bash
python -m backend.main
```

### 3. Open the Dashboard

Navigate to **http://localhost:8000** in your browser.

### 4. Controls

| Action | Method |
|:-------|:-------|
| **Start** | Click "Start" button or press `Space` |
| **Stop** | Click "Stop" button or press `Space` |
| **Reset** | Click "Reset" or press `R` |
| **Speed** | Drag the speed slider (1×–10×) |
| **Inspect Robot** | Click any robot on the map |
| **Battery Heatmap** | Toggle checkbox or press `H` |
| **Mesh Links** | Toggle checkbox or press `M` |
| **Task Overlay** | Toggle checkbox or press `T` |
| **Deselect** | Press `Escape` |

---

## 📁 Project Structure

```
swarm-robotics/
├── backend/
│   ├── simulation/
│   │   ├── config.py       # All tunable parameters
│   │   ├── engine.py       # Main simulation loop (60 FPS)
│   │   ├── orca.py         # ORCA collision avoidance
│   │   ├── cbba.py         # CBBA task allocation
│   │   ├── gossip.py       # Gossip protocol + mesh network
│   │   ├── task.py         # Task definitions & generator
│   │   └── world.py        # Warehouse layout & obstacles
│   ├── api/
│   │   └── server.py       # FastAPI + WebSocket server
│   └── main.py             # Entry point
├── frontend/
│   ├── index.html           # Dashboard layout
│   ├── css/styles.css       # Premium dark theme
│   └── js/
│       ├── app.js           # Main controller
│       ├── renderer.js      # Canvas 2D warehouse renderer
│       ├── dashboard.js     # Metrics & charts
│       └── websocket.js     # WebSocket client
├── requirements.txt
└── README.md
```

---

## ⚙️ Configuration

All parameters are in [`backend/simulation/config.py`](backend/simulation/config.py):

| Parameter | Default | Description |
|:----------|:--------|:------------|
| `num_robots` | 500 | Total swarm size |
| `num_heavy_lifters` | 100 | Heavy-duty robots (slower, stronger) |
| `mesh_range` | 15.0m | P2P communication radius |
| `packet_loss_rate` | 5% | Message drop probability |
| `orca_time_horizon` | 5.0s | ORCA look-ahead time |
| `cbba_max_bundle_size` | 3 | Max tasks per robot |
| `battery_low_threshold` | 20% | Triggers charge-seeking |
| `gossip_fanout` | 3 | Neighbors per gossip round |

---

## 📊 Dashboard Metrics

- **Throughput**: Tasks completed per minute (rolling window)
- **Network Traffic**: Total bytes exchanged (lower = better)
- **Deadlock Counter**: Conflicts detected and resolved
- **Battery Heatmap**: Floor-level battery visualization
- **Status Distribution**: Idle / Moving / Working / Charging / Dead
- **Robot Inspector**: Click any robot for detailed state

---

## 🔬 Algorithms Deep Dive

### CBBA (Task Allocation)
1. **Bundle Building**: Each robot greedily picks the highest-scoring available task
2. **Scoring**: `bid = reward × battery_factor × workload_factor × capability_factor / distance`
3. **Consensus**: Robots exchange bid tables with mesh neighbors; higher bid wins
4. **Convergence**: 5 rounds of gossip ensure swarm-wide agreement

### ORCA (Collision Avoidance)
1. For each neighbor, compute a half-plane constraint (velocity obstacle)
2. Solve a 2D linear program to find the closest safe velocity to the preferred velocity
3. Each robot takes 50% responsibility for avoidance (reciprocal)
4. Handles overlapping robots with emergency push-apart

### Gossip Protocol
- Each robot gossips state to 3 random mesh neighbors per round
- Information spreads exponentially: O(log N) rounds for full dissemination
- 100–500ms simulated latency per message
- 5% packet loss rate

---

## 🎯 What Makes This Industry-Grade

1. **Truly Decentralized**: No central coordinator — robots negotiate peer-to-peer
2. **Heterogeneous Fleet**: Heavy lifters + scouts with different capabilities
3. **Realistic Network**: Simulated latency, packet loss, and bandwidth constraints
4. **Fault Tolerant**: Dead robots' tasks are automatically re-auctioned
5. **Scalable**: SoA (Structure of Arrays) design with NumPy for 500+ robots at 60 FPS
6. **Observable**: Real-time dashboard with throughput, network, and health metrics
