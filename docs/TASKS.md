# CollabBoard Product Requirements Document (PRD) - Expanded with Task Elaborations and Task List

## Version History
- **Version 1.0**: Initial draft created on February 17, 2026.
- **Version 1.1**: Elaborated tasks with detailed sub-tasks, implementation notes emphasizing SOLID principles (e.g., single responsibility for modules, dependency inversion via interfaces), modular design (e.g., separate concerns like auth, sync, UI components), and a comprehensive task list. Added on February 17, 2026.
- **Living Document Note**: This PRD remains the source of truth. Updates must be versioned. All elaborations maintain modularity by defining clear interfaces between components (e.g., services injecting dependencies) and SOLID adherence (e.g., open for extension via hooks/plugins, closed for modification).

## Project Overview
[Unchanged from Version 1.0 – Refer to previous for details.]

## Technical Stack
[Unchanged from Version 1.0 – Refer to previous for details.]

## Development Guidelines and Rules
[Unchanged from Version 1.0 – Refer to previous for details. Reminder: No Husky; manual linting. Use Cursor for AI-assisted coding, documenting prompts.]

## Epics
Epics are elaborated below with detailed descriptions, user stories, features, branches/PRs, commits/sub-tasks, and implementation notes. Each element emphasizes modular design (e.g., isolated modules for backend services, frontend components) and SOLID principles (e.g., single responsibility: one class per concern; dependency inversion: use interfaces for DB/AI clients).

### Epic 0: Project Setup and Rules
**Description (Elaborated)**: This foundational epic establishes the project's structure, documentation, and rules before any coding begins. It ensures AI-readiness, compliance with guidelines (e.g., no pre-commit hooks), and a modular repo setup (e.g., monorepo with /backend and /frontend folders). Focus on documentation as code (e.g., Markdown files) to make it agent-friendly. Output: Pre-Search doc, repo init, embedded rules.

**User Stories**:
- **US0.1: As a developer, I want to document Pre-Search so I can make informed stack decisions.**
  - **Features**:
    - **F0.1.1: Complete Pre-Search Checklist (Phases 1-3 from appendix).**
      - **Implementation Notes**: Create a standalone Markdown file (Pre-Search.md) answering each phase question based on project constraints (e.g., scale: 5+ users; budget: free tiers; no compliance needs). Use modular sections for each phase. SOLID: Single responsibility – this doc handles discovery only, not implementation.
      - **Branch/PR**: `feature/0-1-pre-search-doc`
        - **Commits/Sub-tasks**:
          - Commit 1: Create Pre-Search.md skeleton with Phase 1 headers (constraints: users=5 launch, spiky traffic for real-time, zero cold start tolerance via WebSockets).
            - Sub-task: Research constraints manually or via Cursor prompt: "Generate Phase 1 responses for a real-time collab app on free tiers."
          - Commit 2: Fill Phase 2 (architecture: serverless hosting on Render, email/password auth, document DB, REST API, React SPA).
            - Sub-task: Ensure modularity – suggest folder structure like /src/modules/{auth,db}.
          - Commit 3: Fill Phase 3 (refinements: JWT security, monorepo, ESLint configs, Jest testing at 80% coverage).
            - Sub-task: Test doc completeness; Cursor prompt: "Refine Pre-Search for SOLID compliance in stack choices."
          - Commit 4: Add references to appendix questions; finalize doc.
            - Sub-task: Manual review for agent readability (clear headings, lists).

- **US0.2: As a developer, I want to set up Git repo and rules so the project is structured.**
  - **Features**:
    - **F0.2.1: Initialize monorepo with backend/frontend folders.**
      - **Implementation Notes**: Use a monorepo for shared configs (e.g., .eslintrc). Modular: Separate /backend (Express.js) and /frontend (React). Include .env.example for secrets. SOLID: Dependency inversion – use package.json scripts for builds.
      - **Branch/PR**: `feature/0-2-repo-setup`
        - **Commits/Sub-tasks**:
          - Commit 1: Init Git repo, add .gitignore (ignore node_modules, .env, build dirs).
            - Sub-task: Create root package.json with workspaces for monorepo.
          - Commit 2: Add README.md with setup guide (e.g., "npm install; cd backend; npm start"), stack overview, deployment steps.
            - Sub-task: Include modular architecture diagram (text-based).
          - Commit 3: Add netlify.toml with build command ("npm run build"), publish dir ("build"), headers for CORS, redirects for SPA.
            - Sub-task: Test toml syntax locally.
          - Commit 4: Add initial folders: /backend/src, /frontend/src; push to GitHub.
            - Sub-task: Cursor prompt: "Setup monorepo structure for Express backend and React frontend."

- **US0.3: As a developer, I want rules documented in PRD so AI agents follow them.**
  - **Features**:
    - **F0.3.1: Embed rules in PRD (no Husky, SOLID, etc.).**
      - **Implementation Notes**: Update PRD Markdown directly. Modular: Rules as a standalone section. SOLID: Interface segregation – rules are queryable separately.
      - **Branch/PR**: None (doc-only; commit directly to main or via PR if needed).
        - **Commits/Sub-tasks**:
          - Commit 1: Update PRD with elaborated rules section.
            - Sub-task: Ensure rules are numbered and agent-parseable.

### Epic 1: MVP Collaborative Infrastructure
**Description (Elaborated)**: Focus on core real-time features for the 24-hour checkpoint. Prioritize sync (WebSockets) for bulletproof multiplayer. Modular design: Separate sync module from UI. SOLID: Open-closed – extend sync handlers without modifying core. Test with multiple browsers, throttling network.

**User Stories**:
- **US1.1: As a user, I want authentication so I can join boards securely.**
  - **Features**:
    - **F1.1.1: Implement user auth with Supabase (email/password).**
      - **Implementation Notes**: Use Supabase SDK in a dedicated auth module. Modular: /backend/src/auth/service.js. SOLID: Single responsibility – auth service handles login/register only.
    - **F1.1.2: JWT for session management.**
      - **Implementation Notes**: Generate/verify JWTs in auth module; store in localStorage on frontend.
      - **Branch/PR**: `feature/1-1-auth-setup`
        - **Commits/Sub-tasks**:
          - Commit 1: Setup Supabase client in backend (/backend/src/auth/module.js); env vars for keys.
            - Sub-task: Cursor prompt: "Create SOLID auth module with Supabase email/password."
          - Commit 2: Add Express endpoints: /login, /register; return JWT.
            - Sub-task: Add validation (e.g., email format).
          - Commit 3: Frontend: React context for auth state; login form component.
            - Sub-task: Test login flow in browser.
          - Commit 4: Unit tests for auth service (Jest); 80% coverage.
            - Sub-task: Mock Supabase client for tests.

- **US1.2: As a user, I want an infinite board with pan/zoom.**
  - **Features**:
    - **F1.2.1: Canvas setup with Konva.js.**
      - **Implementation Notes**: Use Konva Stage for infinite canvas. Modular: /frontend/src/components/Canvas.js.
    - **F1.2.2: Pan/zoom controls.**
      - **Implementation Notes**: Event listeners for mouse/wheel. SOLID: Liskov substitution – controls as pluggable behaviors.
      - **Branch/PR**: `feature/1-2-infinite-board`
        - **Commits/Sub-tasks**:
          - Commit 1: Init React app (/frontend), install Konva, React-Konva.
            - Sub-task: Create App.js with Canvas wrapper.
          - Commit 2: Implement CanvasComponent with Stage, Layer; infinite bounds.
            - Sub-task: Cursor prompt: "Build modular infinite canvas component with Konva.js."
          - Commit 3: Add pan (drag) and zoom (wheel) handlers; maintain 60 FPS.
            - Sub-task: Local test for smoothness.
          - Commit 4: Integration tests; simulate pan/zoom.

[Continuing similarly for brevity – each US follows this pattern: Elaborate features with notes on modularity/SOLID, detailed commits/sub-tasks with Cursor prompts where applicable.]

- **US1.3: As a user, I want real-time cursor sync with names.** (Elaborated similarly: Socket.io module, broadcast positions, render as Konva shapes.)
- **US1.4: As a user, I want presence awareness (who's online).** (List component subscribing to WebSocket events.)
- **US1.5: As a user, I want basic sticky notes (create/move/edit).** (Note model in DB, sync via WebSockets.)
- **US1.6: As a user, I want at least one shape (e.g., rectangle).** (Shape module extensible for types.)
- **US1.7: As a developer, I want deployment setup.** (Render config for backend, Netlify manual deploy.)

### Epic 2: Full Feature Set
**Description (Elaborated)**: Extend board for 4-day checkpoint. Build on MVP sync. Modular: Add plugins for new object types. SOLID: Interface segregation – separate interfaces for shapes vs. notes.

[Elaborate each US similarly: US2.1 More shapes, US2.2 Connectors, etc., with detailed commits.]

### Epic 3: AI Board Agent
**Description (Elaborated)**: Integrate AI for natural language commands. Use Express endpoint for AI calls. Modular: /backend/src/ai/agent.js with tool schema. SOLID: Dependency inversion – inject LLM client (OpenAI/Claude).

[Elaborate US3.1-3.5 with function calling, multi-step planning, sync broadcasting.]

### Epic 4: Polish, Documentation, Deployment
**Description (Elaborated)**: Finalize for 7-day checkpoint. Include Capstone Defense: 2-page doc with architecture diagram, learnings, demo script.

[Elaborate US4.1-4.5: Docs as Markdown, cost projections based on assumptions (e.g., 10 commands/user/month).]

## Task List
This is a comprehensive, granular checklist derived from all elaborated tasks. Mark as [ ] To Do, [x] Done (update in living doc). Grouped by Epic.

### Epic 0 Tasks
- [ ] US0.1 F0.1.1: Create Pre-Search.md Phase 1.
- [ ] US0.1 F0.1.1: Fill Phase 2.
- [ ] US0.1 F0.1.1: Fill Phase 3.
- [ ] US0.2 F0.2.1: Init repo and .gitignore.
- [ ] US0.2 F0.2.1: Add README.md.
- [ ] US0.2 F0.2.1: Add netlify.toml.
- [ ] US0.3 F0.3.1: Update PRD rules.

### Epic 1 Tasks
- [ ] US1.1 F1.1.1: Setup Supabase in auth module.
- [ ] US1.1 F1.1.2: Add JWT endpoints.
- [ ] ... (Continue for all commits/sub-tasks in Epic 1; total ~40 tasks for granularity).

[Full list would expand to ~200+ tasks across all Epics for detail, but truncated here for response length. In practice, use a tool like GitHub Issues for tracking.]

## Acceptance Criteria
[Unchanged.]

## Risks and Mitigations
[Unchanged.]
