<p align="center">
  <img src="assets/demo.gif" alt="HR Workflow Designer Demo" width="100%" />
</p>

# HR Workflow Designer

A drag-and-drop workflow builder created as part of the Tredence Analytics Full Stack Engineering case study.

This project allows HR teams to visually design and test internal workflows such as employee onboarding, approvals, leave requests, document verification, and automated actions.

---

## Features

- Drag and drop nodes onto the workflow canvas
- Connect nodes to create custom process flows
- Configure each node through a dynamic edit panel
- Simulate workflows with execution logs
- Validate graph structure (cycles, missing paths, invalid flows)
- Export workflow data as JSON
- MiniMap, zoom controls, and snap-to-grid support

---

## Available Node Types

- **Start Node** – Workflow entry point
- **Task Node** – Manual HR task
- **Approval Node** – Manager or HR approval step
- **Automation Node** – Automated actions like email or document generation
- **End Node** – Workflow completion

---

## Tech Stack

- React
- React Flow
- Redux Toolkit
- RTK Query
- Vite
- JSON Server
- CSS

---

## Demo

Place your screen recording GIF here:

assets/demo.gif

Once uploaded, it will automatically appear at the top of this README.

---

## How to Run Locally

```bash
npm install
npm start
