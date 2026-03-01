# FlowCharts Tasks

A collection of **5 system design tasks**, each modeled with pseudocode, Mermaid flowcharts, and Graphviz DOT diagrams. Each task represents a real-world system with complex logic, validation gates, loops, and decision branches.

---

## Tasks Overview

| Task | System | Description |
|------|--------|-------------|
| [Task 1](task1/) | **ATM Cash Withdrawal** | PIN verification (3 attempts), balance/increment/daily limit checks, multi-transaction support |
| [Task 2](task2/) | **E-Commerce Checkout** | User auth, product browsing, cart management, promo codes, shipping rules, payment processing |
| [Task 3](task3/) | **Hospital System** | Patient ID verification, appointment booking with SMS, lab results with PDF download |
| [Task 4](task4/) | **University Registration** | 4-gate course validation (capacity, prereqs, conflicts, credits), GPA-based advisor approval |
| [Task 5](task5/) | **Smart Home Security** | Continuous sensor monitoring, 3-level alarm system, geofence false alarm prevention, multi-channel alerts |

---

## Repository Structure

```
FlowChartsTasks/
├── Readme.md                  # This file
├── task1/
│   ├── Readme.md              # Task documentation
│   ├── pseudocode.md          # ATM system pseudocode
│   ├── flowchart.mmd          # Mermaid flowchart
│   ├── flowchart.dot          # Graphviz DOT flowchart
│   ├── llm_conversation.md    # LLM conversation link
│   ├── task1-mermaid.png      # Rendered Mermaid image
│   └── task1-graphviz.png     # Rendered Graphviz image
├── task2/
│   ├── Readme.md
│   ├── pseudocode.md
│   ├── flowchart.mmd
│   ├── flowchart.dot
│   └── llm_conversation.txt
├── task3/
│   ├── Readme.md
│   ├── pseudocode.md
│   ├── flowchart.mmd
│   ├── flowchart.dot
│   └── llm_conversation.txt
├── task4/
│   ├── Readme.md
│   ├── pseudocode.md
│   ├── flowchart.mmd
│   ├── flowchart.dot
│   └── llm_conversation.txt
└── task5/
    ├── Readme.md
    ├── pseudocode.md
    ├── flowchart.mmd
    ├── flowchart.dot
    └── llm_conversation.txt
```

---

## Each Task Contains

| File | Format | Purpose |
|------|--------|---------|
| `Readme.md` | Markdown | Full documentation with overview, flow, pseudocode, flowcharts, and key features |
| `pseudocode.md` | Markdown | Detailed pseudocode describing the system logic |
| `flowchart.mmd` | Mermaid | Flowchart diagram viewable on GitHub and compatible editors |
| `flowchart.dot` | Graphviz DOT | Flowchart diagram for rendering with Graphviz tools |
| `llm_conversation.*` | Text/Markdown | Link to the LLM conversation used during task creation |

---

## Task Summaries

### Task 1: ATM Cash Withdrawal System
An ATM process with **PIN authentication** (max 3 tries, card block on failure) and **3 sequential withdrawal validations**: sufficient balance, multiple of 20 TL, and daily withdrawal limit. Supports multiple transactions per session with receipt printing.

### Task 2: E-Commerce Checkout System
A complete online shopping flow covering **5 sections**: user login, product browsing with stock checks, cart management with quantity validation, checkout with minimum order ($10) and shipping threshold ($40), and payment processing with multiple methods (Credit Card, PayPal, Crypto).

### Task 3: Hospital Appointment & Lab Results System
A modular hospital system with **National ID verification** and two independent modules. Module 1 handles appointment booking across clinics with doctor/time selection and SMS confirmation. Module 2 manages lab result viewing with availability checks and optional PDF generation.

### Task 4: University Course Registration System
A registration system with **4 nested validation gates** (capacity, prerequisites, schedule conflicts, 35-credit limit). Supports course adding and dropping. Final registration status depends on **GPA check** — students below 2.5 GPA require advisor approval before confirmation.

### Task 5: Smart Home Security System
A continuous monitoring loop reading motion and door/window sensors. Features **geofence-based false alarm prevention**, a **3-level alarm classification** (Low/Medium/High based on sensor combinations), **multi-channel notifications** (SMS, App, Email with camera clip), and user-controlled reset functionality.

---

## Tools Used

- **Pseudocode** — Structured logic representation
- **Mermaid** — Diagram-as-code flowcharts (renders on GitHub)
- **Graphviz DOT** — Graph description language for flowcharts
- **Gemini AI** — LLM used for task generation and iteration
