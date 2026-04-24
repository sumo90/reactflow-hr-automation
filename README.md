# HR Workflow Designer – Tredence Analytics Case Study

A visual, drag-and-drop HR Workflow Designer built with **React + React Flow**, **Redux Toolkit (RTK Query)**, and **JSON Server**.

---

## How to Run

### Prerequisites
- Node.js ≥ 18
- npm ≥ 9

### Steps

```bash
# 1. Install dependencies
npm install

# 2. Start both JSON Server (port 3001) + Vite dev server (port 5173) together
npm start

# Or run them separately:
npm run server   # JSON Server on :3001
npm run dev      # Vite on :5173
```

Open [http://localhost:5173](http://localhost:5173)

---

## Architecture

```
src/
├── api/
│   └── workflowApi.js        # RTK Query — GET /automations, POST /simulate
├── store/
│   ├── index.js              # configureStore — combines workflow + api reducers
│   └── slices/
│       └── workflowSlice.js  # nodes, edges, selectedNodeId state + reducers
├── hooks/
│   └── useWorkflow.js        # Single custom hook exposing all canvas actions
├── components/
│   ├── nodes/                # 5 custom React Flow node components
│   │   ├── BaseNode.jsx      # Shared wrapper (handles, styling, selection)
│   │   ├── StartNode.jsx
│   │   ├── TaskNode.jsx
│   │   ├── ApprovalNode.jsx
│   │   ├── AutoNode.jsx
│   │   └── EndNode.jsx
│   ├── forms/                # Node-specific edit forms
│   │   ├── NodeFormPanel.jsx # Renders the correct form for selected node
│   │   ├── StartForm.jsx
│   │   ├── TaskForm.jsx
│   │   ├── ApprovalForm.jsx
│   │   ├── AutoForm.jsx      # Fetches automations from JSON Server via RTK Query
│   │   ├── EndForm.jsx
│   │   └── KeyValueEditor.jsx# Reusable key-value pair editor
│   ├── Sidebar/
│   │   └── Sidebar.jsx       # Drag source palette for all node types
│   ├── Canvas/
│   │   ├── Canvas.jsx        # ReactFlow canvas with drop handler
│   │   └── Toolbar.jsx       # Top bar — node/edge counts, clear, Test Workflow btn
│   └── SandboxPanel/
│       └── SandboxPanel.jsx  # Simulate modal — runs validation + execution log
├── styles/
│   └── global.css            # CSS variables, buttons, form controls, RF overrides
├── App.jsx                   # Root layout — Sidebar + Toolbar + Canvas + FormPanel
└── main.jsx                  # React entry point with Redux Provider
db.json                       # JSON Server data (automations + simulations)
```

---

## Design Decisions

### State Management (Redux Toolkit)
- **`workflowSlice`** owns all nodes, edges, and the selected node ID.  
- React Flow's `applyNodeChanges` / `applyEdgeChanges` are called inside Redux reducers so React Flow's internal state and Redux stay in sync.
- **RTK Query** (`workflowApi`) handles async data:  
  - `GET /automations` — cached, auto-refetched on mount.  
  - `POST /simulate` — uses `queryFn` for local simulation logic; also POSTs result to `/simulations` for persistence.

### Custom Hook (`useWorkflow`)
All canvas interactions (drop, click, connect, delete) are funnelled through a single hook. Components never `dispatch` directly — they call semantic functions like `onDropNode`, `onDeleteNode`, `deselectNode`.

### Node Form Architecture
`NodeFormPanel` is a router that renders the right form based on `selectedNode.type`. Each form is a pure controlled component: it receives `data` and calls `onChange(partialData)`. Adding a new node type = add one form file + register in `FORM_MAP`.

### Mock API / JSON Server
- JSON Server runs on `:3001`. Vite proxies `/api/*` → `localhost:3001/*` so no CORS issues.
- `GET /automations` returns real data from `db.json`.
- `POST /simulate` is handled **locally** (graph traversal in `workflowApi.js`) because JSON Server has no custom route logic. Results are persisted to `/simulations` for a record.

### Simulation Engine (local)
The simulation:
1. Validates: exactly one Start Node, at least one End Node, no disconnected nodes, no cycles (DFS).
2. BFS-traverses the graph from Start → End.
3. Returns a step-by-step log with node type, label, and key metadata.

---

## What's Complete

- ✅ All 5 node types with custom React Flow nodes
- ✅ Drag from sidebar onto canvas
- ✅ Connect nodes with edges (prevents duplicates)
- ✅ Click to select node → form panel opens
- ✅ Delete key removes selected node/edge
- ✅ All 5 node edit forms (controlled, with validation feedback)
- ✅ Dynamic param fields in AutoNode form (from API)
- ✅ RTK Query for GET /automations + simulate mutation
- ✅ Sandbox panel with validation + step-by-step log
- ✅ Export workflow as JSON
- ✅ MiniMap, zoom controls, snap-to-grid
- ✅ Clean Redux slice with selectors

## What I'd Add With More Time

- Undo/Redo (Redux history middleware or Immer patches)
- Import workflow JSON back into canvas
- Visual validation errors shown directly on nodes (red borders)
- Auto-layout (Dagre / ELK)
- Node search / filter in sidebar
- Real backend with persistence (PostgreSQL + Express)
- Unit tests for simulation engine
- E2E tests with Playwright
