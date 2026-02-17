# Diagrams

```mermaid
flowchart TD
    A[Start: User Opens App] --> B{Authenticated?}
    B -->|No| C[Login/Register via Email/Password]
    C --> D[Backend: Auth with Supabase, Generate JWT]
    D --> E[Frontend: Store JWT in LocalStorage]
    E --> F[Join or Create Board]
    B -->|Yes| F
    F --> G[Infinite Canvas Loads with Pan/Zoom]
    G --> H[Presence: Show Online Users]
    H --> I[Display Multiplayer Cursors with Names]
    I --> J{User Action?}
    J -->|Create Sticky Note| K[Create Note: Text, Color, Position]
    K --> L[Sync via WebSockets to Backend]
    L --> M[Persist in Supabase DB]
    M --> N[Broadcast to All Clients]
    N --> O[Update Canvas in Real-Time]
    J -->|Create Shape| P[Create Rectangle/Circle/Line: Type, Position, Color]
    P --> L
    J -->|Move/Edit Object| Q[Transform: Move, Resize, Rotate, Edit Text]
    Q --> L
    J -->|Select/Operate| R[Single/Multi-Select, Delete, Duplicate]
    R --> L
    J -->|Issue AI Command| S[Go to AI Agent Flow]
    J -->|Logout| T[End Session]
    O --> J
```

### User Flow Diagram
This flowchart illustrates the end-user journey in CollabBoard, from authentication to real-time collaboration. It follows a top-down structure for clarity, with decision points and loops for ongoing interactions. Branches represent modular user actions aligned with SOLID principles (e.g., separate concerns for auth, sync, and object manipulation).

```mermaid
sequenceDiagram
    participant User as User Frontend
    participant FE as Frontend (React/Konva)
    participant BE as Backend (Express.js/Socket.io)
    participant DB as Supabase DB
    participant AI as AI Service (OpenAI/Claude)
    User->>FE: Input Natural Language Command (e.g., "Create SWOT analysis")
    FE->>BE: POST /ai-command with command text
    BE->>AI: Call LLM with function calling (tools: createStickyNote, etc.)
    AI->>BE: Parse command, return function calls (e.g., createFrame for quadrants)
    loop Multi-Step Execution
        BE->>BE: Plan steps if complex (e.g., create 4 frames, add labels)
        BE->>DB: Execute tools: Create/Update objects in DB
        BE->>BE: Get board state for context if needed
    end
    BE->>DB: Persist changes
    BE->>FE: Broadcast updates via WebSockets (real-time sync)
    FE->>User: Render changes on canvas (shared state)
    Note right of BE: Handle conflicts (last-write-wins), latency <2s
    Note over User,AI: Supports 6+ commands across creation, manipulation, layout, complex
```

### AI Agent Flow Diagram
This sequence diagram details the AI agent's processing pipeline, emphasizing modularity (e.g., separate AI module in backend). It shows synchronous and asynchronous interactions, with loops for multi-step commands. Best practices include actor labels, notes for performance targets, and clear request-response flows to highlight dependency inversion (AI client injected).

```mermaid
flowchart LR
    A[App Start] --> B[Init Frontend: React App Loads]
    B --> C[Auth Check: Use JWT]
    C --> D[Connect WebSockets for Sync]
    D --> E[Load Board State from DB via API]
    E --> F[Render Canvas with Konva.js]
    F --> G[Event Loop: User Inputs - Mouse, Keyboard]
    G --> H[Local Updates: e.g., Cursor Move]
    H --> I[Emit to Backend: Sync Changes]
    I --> J[Backend: Validate, Persist to Supabase]
    J --> K[Broadcast to All Connected Clients]
    K --> L[Clients Update Canvas in Real-Time]
    G --> M[AI Command Trigger]
    M --> N[Process AI Flow]
    N --> I
    L --> G
    subgraph Frontend
    B-->F-->G-->H-->L
    end
    subgraph Backend
    D-->I-->J-->K
    end
    subgraph Database
    E-->J
    end
```

### General Program Flow Diagram
This high-level flowchart depicts the overall application lifecycle and data flow, using subgraphs for modular components (frontend, backend, DB). It incorporates loops for continuous collaboration and branches for AI integration. Follows left-to-right progression with color-coding for start/loop points.

```mermaid
graph TD
    A[Client - Browser] -->|HTTP/REST| B[Express.js Server]
    A -->|WebSockets| C[Socket.io for Real-Time]
    B --> D[Supabase Auth & DB]
    C --> D
    B --> E[AI Integration: OpenAI/Claude API]
    D --> F[Persistence: Board Objects, Users]
    E --> E1[Function Calling for Tools]
    D --> D1[Real-Time Subscriptions]
    subgraph Hosting
    B-->G[Render.com - Backend]
    A-->H[Netlify.com - Frontend]
    end
```

### System Architecture Diagram
A component diagram showing high-level modules and connections. Uses graph TD for directional flows, with subgraphs for deployment. Best practices: Simple nodes, arrows for data flow, notes for key features.

```mermaid
sequenceDiagram
    participant U1 as User 1 Frontend
    participant BE as Backend
    participant DB as DB
    participant U2 as User 2 Frontend
    U1->>BE: Create/Move Object (e.g., Sticky Note)
    BE->>DB: Save Change (with Timestamp)
    BE->>U1: Acknowledge
    BE->>U2: Broadcast Update
    U2->>U2: Render Update
    alt Simultaneous Edit Conflict
        U2->>BE: Edit Same Object
        BE->>DB: Apply Last-Write-Wins (Compare Timestamps)
        BE->>U1: Broadcast Resolved State
        BE->>U2: Broadcast Resolved State
    end
    Note over U1,U2: Latency <100ms, Handles Disconnect/Reconnect
```

### Real-Time Data Sync Flow Diagram
Sequence diagram focused on collaboration sync, including conflict resolution. Highlights resilience and performance targets with notes and alt branches.

```mermaid
flowchart TD
    A[Priority 1: Cursor Sync] --> B[Two Cursors Moving]
    B --> C[Object Sync: Sticky Notes Appear]
    C --> D[Conflict Handling: Simultaneous Edits]
    D --> E[State Persistence: Survive Refreshes]
    E --> F[Board Features: Shapes, Frames]
    F --> G[AI Basic: Single-Step]
    G --> H[AI Complex: Multi-Step]
    style A fill:#green,stroke:#333
    style H fill:#red,stroke:#333
```

### Build Strategy Flow Diagram
Simple flowchart based on the spec's priority order, with progressive stages. Color-coded start/end for emphasis.

```mermaid
erDiagram
    USER ||--o{ SESSION : has
    USER {
        string id PK
        string email
        string name
    }
    SESSION {
        string id PK
        string user_id FK
        string jwt
    }
    BOARD ||--o{ OBJECT : contains
    BOARD {
        string id PK
        string name
    }
    OBJECT {
        string id PK
        string board_id FK
        string type "note, shape, frame, etc."
        json position
        string color
        string text
    }
    USER ||--o{ BOARD : accesses
```

### Entity-Relationship Diagram (Data Model)
An ER diagram outlining key data entities for persistence. Aligns with modular design, showing relationships for board objects.

These diagrams provide a comprehensive visual overview of CollabBoard. Each uses Mermaid's best practices: consistent syntax, descriptive labels, and appropriate diagram types (flowchart for processes, sequence for interactions, ER for data). They are based on the project spec, PRD, and stack (e.g., Supabase, Express.js, React/Konva).
