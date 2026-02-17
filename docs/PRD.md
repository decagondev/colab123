# CollabBoard Product Requirements Document (PRD)

## Version History
- **Version 1.0**: Initial draft created on February 17, 2026.
- **Living Document Note**: This PRD serves as the source of truth for the entire project. It is designed to be readable and actionable by humans, LLMs, and AI agents (e.g., Cursor). Developers may update sections with justifications for changes (e.g., due to technical constraints or discoveries). Updates must be versioned, dated, and appended to this history. All decisions must align with SOLID principles (Single Responsibility, Open-Closed, Liskov Substitution, Interface Segregation, Dependency Inversion) and a modular design, emphasizing loose coupling, high cohesion, and reusability. If conflicts arise, prioritize MVP requirements from the project spec.

## Project Overview
CollabBoard is a real-time collaborative whiteboard tool inspired by Miro, focusing on AI-first development. It includes an infinite canvas, sticky notes, shapes, real-time sync, multiplayer cursors, user authentication, and an AI agent for natural language board manipulation. The project is a one-week sprint with checkpoints: MVP (24 hours), Early Submission (4 days), Final (7 days). Completion is gated for Austin admission.

### Key Goals
- Build bulletproof multiplayer sync first.
- Integrate AI for board commands.
- Document AI-first workflows, costs, and Pre-Search.
- Deploy publicly: Backend on Render.com (free plan, Express.js for AI), Frontend on Netlify.com (manual deploy with netlify.toml).

### Scope
- **In Scope**: MVP features, core collaboration, AI agent with 6+ commands, documentation, deployment.
- **Out of Scope**: Advanced features like voting, embeds, or mobile optimization unless time allows.
- **Assumptions**: Solo developer using Cursor for code generation/iteration. Free tiers suffice for dev/testing. No paid services beyond potential AI API costs.

## Technical Stack
- **Backend**: Express.js (Node.js) for AI integrations (e.g., OpenAI/Anthropic API calls with function calling). Hosted on Render.com free plan. Use WebSockets (e.g., Socket.io) for real-time sync. Database: Supabase (free tier) for persistence, auth, and real-time (alternative to Firebase).
- **Frontend**: React with Konva.js for canvas rendering. Hosted on Netlify.com.
- **AI Integration**: OpenAI GPT-4 or Anthropic Claude for agent commands.
- **Other**: Git for version control. No additional frameworks unless justified.
- **Deployment**:
  - Backend: Render.com (free web service, auto-deploys from GitHub).
  - Frontend: Netlify.com (manual deploy via site UI; include netlify.toml for build settings).
- **netlify.toml Example** (Sane defaults for React):
  ```
  [build]
  command = "npm run build"
  publish = "build"

  [[headers]]
  for = "/*"
  [headers.values]
  Access-Control-Allow-Origin = "*"

  [[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
  ```
  This enables SPA routing, CORS for dev, and standard React build.

## Development Guidelines and Rules
These rules must be followed strictly. They are set in Epic 0 and enforce AI-first, modular design.

1. **AI-First Workflow**: Use Cursor as the primary agentic tool for code generation, refactoring, and debugging. Document prompts and % AI-generated code in AI Development Log.
2. **SOLID and Modular Design**: All code must adhere to SOLID. Use modules for separation (e.g., separate files for auth, sync, AI). Favor composition over inheritance.
3. **Git Practices**:
   - Branch Naming: `feature/epic-number-user-story-number-feature-name` (e.g., `feature/1-1-auth-setup`).
   - Commits: Atomic, descriptive (e.g., "Add user model with validation"). No squash; keep history granular.
   - PRs: One per feature, with description linking to User Story, sub-tasks completed, and tests.
4. **No Husky or Pre-Commit Hooks**: Do not use Husky or any automated pre-commit tools. Manually lint/format before commits.
5. **Testing**: Unit/integration tests with Jest. Aim for 80% coverage on core sync/AI. Test multiplayer in multiple browsers.
6. **Documentation**: Update this PRD for changes. Create Pre-Search doc before code. Post-MVP: AI Dev Log, Cost Analysis, Capstone Defense (summary of architecture, learnings, demo script).
7. **Cost Tracking**: Log AI API usage (tokens, calls) for Cost Analysis.
8. **Performance**: Target <100ms sync latency. Throttle network in tests.
9. **Updates**: If stack changes (e.g., switch DB), document in Version History with rationale.

## Epics
Epics are high-level groupings aligned with project checkpoints. Prioritize vertically: Finish sync before features.

### Epic 0: Project Setup and Rules
**Description**: Establish rules, docs, and initial repo before code. This epic ensures compliance and AI-readiness.
**User Stories**:
- US0.1: As a developer, I want to document Pre-Search so I can make informed stack decisions.
  - Features:
    - F0.1.1: Complete Pre-Search Checklist (Phases 1-3 from appendix).
    - Branch/PR: `feature/0-1-pre-search-doc`
      - Commits/Sub-tasks:
        - Commit 1: Create Pre-Search.md with Phase 1 constraints (scale: 5 users; budget: free; time: 1 week).
        - Commit 2: Add Phase 2 architecture (auth: Supabase; DB: document; frontend: React/Konva).
        - Commit 3: Add Phase 3 refinements (security: JWT; file structure: src/modules).
        - Sub-task: Use Cursor prompt: "Generate Pre-Search doc based on appendix, using Express.js backend, React frontend."
- US0.2: As a developer, I want to set up Git repo and rules so the project is structured.
  - Features:
    - F0.2.1: Initialize monorepo with backend/frontend folders.
    - Branch/PR: `feature/0-2-repo-setup`
      - Commits/Sub-tasks:
        - Commit 1: Init Git repo, add .gitignore (node_modules, env).
        - Commit 2: Add README.md with setup guide, stack overview.
        - Commit 3: Add netlify.toml as per spec.
        - Sub-task: Manual: Push to GitHub.
- US0.3: As a developer, I want rules documented in PRD so AI agents follow them.
  - Features:
    - F0.3.1: Embed rules in PRD (no Husky, SOLID, etc.).
    - Branch/PR: None (doc-only; update PRD directly).

### Epic 1: MVP Collaborative Infrastructure
**Description**: Build core real-time sync and basic board (24-hour checkpoint).
**User Stories**:
- US1.1: As a user, I want authentication so I can join boards securely.
  - Features:
    - F1.1.1: Implement user auth with Supabase (email/password).
    - F1.1.2: JWT for session management.
    - Branch/PR: `feature/1-1-auth-setup`
      - Commits/Sub-tasks:
        - Commit 1: Setup Supabase client in backend (modular: src/auth/module.js).
        - Commit 2: Add login/register endpoints in Express.js.
        - Commit 3: Integrate auth in frontend (React context).
        - Sub-task: Test login in browser; use Cursor for "Implement SOLID auth module with Supabase."
- US1.2: As a user, I want an infinite board with pan/zoom.
  - Features:
    - F1.2.1: Canvas setup with Konva.js.
    - F1.2.2: Pan/zoom controls.
    - Branch/PR: `feature/1-2-infinite-board`
      - Commits/Sub-tasks:
        - Commit 1: Init React app, install Konva.
        - Commit 2: Create CanvasComponent (SOLID: single responsibility for rendering).
        - Commit 3: Add pan/zoom event handlers.
        - Sub-task: Local test; Cursor prompt: "Build modular infinite canvas with Konva.js."
- US1.3: As a user, I want real-time cursor sync with names.
  - Features:
    - F1.3.1: WebSocket setup for cursor positions.
    - F1.3.2: Display cursors with user labels.
    - Branch/PR: `feature/1-3-cursor-sync`
      - Commits/Sub-tasks:
        - Commit 1: Add Socket.io to backend (modular: src/sync/cursor.js).
        - Commit 2: Frontend subscribe to cursor updates.
        - Commit 3: Render cursors on canvas.
        - Sub-task: Test with 2 browsers; Cursor: "Implement real-time cursor sync module."
- US1.4: As a user, I want presence awareness (who's online).
  - Features:
    - F1.4.1: Track online users via WebSockets.
    - F1.4.2: UI list of active users.
    - Branch/PR: `feature/1-4-presence`
      - Commits/Sub-tasks:
        - Commit 1: Backend presence module.
        - Commit 2: Frontend display component.
        - Commit 3: Handle disconnect/reconnect.
- US1.5: As a user, I want basic sticky notes (create/move/edit).
  - Features:
    - F1.5.1: Note model and sync.
    - Branch/PR: `feature/1-5-sticky-notes`
      - Commits/Sub-tasks:
        - Commit 1: Define note schema in DB.
        - Commit 2: CRUD endpoints/sync.
        - Commit 3: Frontend rendering/editing.
- US1.6: As a user, I want at least one shape (e.g., rectangle).
  - Features:
    - F1.6.1: Shape model and sync.
    - Branch/PR: `feature/1-6-shapes`
      - Similar commits as above.
- US1.7: As a developer, I want deployment setup.
  - Features:
    - F1.7.1: Deploy backend to Render.com.
    - F1.7.2: Manual frontend to Netlify.
    - Branch/PR: `feature/1-7-deployment`
      - Commits: Config files, env vars.

### Epic 2: Full Feature Set
**Description**: Add remaining board features (4-day checkpoint).
**User Stories**:
- US2.1: As a user, I want shapes (circle, line) and colors.
  - Features: F2.1.1: Extend shape types. Branch: `feature/2-1-more-shapes`.
- US2.2: As a user, I want connectors (lines/arrows).
  - Features: F2.2.1: Connector model/sync. Branch: `feature/2-2-connectors`.
- US2.3: As a user, I want text elements.
  - Features: F2.3.1: Text module. Branch: `feature/2-3-text`.
- US2.4: As a user, I want frames for grouping.
  - Features: F2.4.1: Frame model. Branch: `feature/2-4-frames`.
- US2.5: As a user, I want transforms (resize/rotate).
  - Features: F2.5.1: Transform handlers. Branch: `feature/2-5-transforms`.
- US2.6: As a user, I want selection/operations (multi-select, delete, duplicate).
  - Features: F2.6.1: Selection module. Branch: `feature/2-6-selection-ops`.
- US2.7: As a user, I want conflict resolution (last-write-wins).
  - Features: F2.7.1: Implement in sync layer. Branch: `feature/2-7-conflicts`.
- US2.8: As a user, I want persistence/resilience.
  - Features: F2.8.1: DB save on changes. Branch: `feature/2-8-persistence`.

### Epic 3: AI Board Agent
**Description**: Integrate AI for commands.
**User Stories**:
- US3.1: As a user, I want creation commands (e.g., add sticky note).
  - Features: F3.1.1: AI endpoint in Express.js. Branch: `feature/3-1-creation-cmds`.
    - Commits: Tool schema, function calling.
- US3.2: As a user, I want manipulation commands (move/resize).
  - Features: F3.2.1: Extend AI tools. Branch: `feature/3-2-manip-cmds`.
- US3.3: As a user, I want layout commands (grid/arrange).
  - Features: F3.3.1: Layout logic. Branch: `feature/3-3-layout-cmds`.
- US3.4: As a user, I want complex commands (SWOT, journey map).
  - Features: F3.4.1: Multi-step execution. Branch: `feature/3-4-complex-cmds`.
- US3.5: As a user, I want shared AI state (real-time sync).
  - Features: F3.5.1: Broadcast AI changes. Branch: `feature/3-5-shared-ai`.

### Epic 4: Polish, Documentation, Deployment
**Description**: Final checkpoint (7 days).
**User Stories**:
- US4.1: As a developer, I want AI Dev Log.
  - Features: F4.1.1: 1-page doc. Branch: None (doc).
- US4.2: As a developer, I want AI Cost Analysis.
  - Features: F4.2.1: Track dev costs; project scales. Branch: None.
- US4.3: As a developer, I want Capstone Defense doc.
  - Features: F4.3.1: Architecture summary, learnings. Branch: None.
    - Sub-task: Create after MVP; include demo video script.
- US4.4: As a user, I want full deployment.
  - Features: F4.4.1: Public access, 5+ users. Branch: `feature/4-4-final-deploy`.
- US4.5: As a developer, I want social post.
  - Features: None (manual).

## Acceptance Criteria
- All features tested against spec scenarios (e.g., 2 users editing, refresh persistence).
- Performance targets met.
- Code modular, SOLID-compliant.

## Risks and Mitigations
- Risk: Free tier limits. Mitigation: Monitor usage; fallback to local dev.
- Risk: Sync bugs. Mitigation: Prioritize testing.

This PRD is now the blueprint. Proceed to Epic 0.
