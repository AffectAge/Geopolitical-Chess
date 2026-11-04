AGENTS.md — Game Development Architecture & Project Rules

1. Project Overview

This project is a multiplayer, turn-based strategy game with a hexagonal world map.

Technology stack: TypeScript, React, Vite, Phaser, and the existing server-side infrastructure.

Engineering priorities:

- Maintainability and modularity.
- Performance and scalability.
- Reusability and minimal code duplication.
- Data-driven gameplay and visual content.
- Deterministic, server-authoritative simulation.
- Compatibility with existing systems.
- Minimal architectural complexity.

Core principle: Prefer existing, proven solutions over custom implementations. Prefer simple solutions over unnecessary abstractions.

These instructions apply to all AI agents creating, modifying, reviewing, or refactoring project code.

2. Repository Discovery

Before making changes, agents MUST:

1. Read this file and applicable nested "AGENTS.md" files.
2. Inspect the actual repository structure.
3. Read relevant project documentation and architectural decisions.
4. Identify the existing implementation of the requested functionality.
5. Inspect related interfaces, dependencies, configuration, and tests.
6. Identify reusable utilities, services, libraries, and infrastructure.
7. Determine the smallest safe change that satisfies the request.

MUST NOT assume that example architectures, directories, commands, or patterns exist in the repository.

MUST NOT create parallel infrastructure when an existing implementation can be extended.

Existing working systems MUST be preserved unless changes are necessary and authorized.

3. Architecture and Engineering Rules

3.1. General Requirements

- MUST follow established project conventions.
- MUST maintain clear module responsibilities and ownership.
- MUST separate gameplay simulation, rendering, UI, networking, and persistence.
- MUST avoid circular dependencies and unnecessary coupling.
- MUST preserve public interfaces and existing behavior unless changes are authorized.
- MUST prefer composition and reuse where appropriate.
- MUST avoid speculative abstractions and premature optimization.
- MUST avoid duplicate implementations.
- MUST document significant architectural decisions.

Architecture MUST be selected according to actual subsystem requirements, not assumed industry conventions.

ECS, Data-Oriented Design, OOP, Command, State Machine, Event Bus, Strategy, and other patterns MAY be used when justified.

MUST NOT introduce a pattern solely because it is considered a best practice.

3.2. Architecture and Dependency Approval

Before introducing or replacing any framework, library, architectural pattern, major abstraction, or infrastructure component, agents MUST:

1. Investigate existing project solutions.
2. Determine whether existing code can be reused or extended.
3. Evaluate established libraries or frameworks that could solve the problem.
4. Compare relevant alternatives, including no new dependency.
5. Consider maintenance, performance, compatibility, complexity, licensing, and implementation cost.
6. Identify opportunities to avoid writing unnecessary custom code.
7. Present the recommended approach and alternatives to the user.
8. Obtain explicit user approval before implementation.

Agents MAY proactively recommend solutions that are better than the current implementation or provide missing functionality.

Agents SHOULD favor mature, actively maintained libraries when they offer a clear advantage over custom code.

Agents MUST NOT introduce dependencies merely to avoid writing a small amount of straightforward code.

Approval rule: If a proposed solution requires a new dependency, framework, architectural pattern, major abstraction, or substantial replacement of existing infrastructure, implementation MUST wait for explicit user approval.

Approval of one proposal does not authorize unrelated architectural changes.

3.3. User Decision Protocol

When approval is required, agents MUST explain:

- Current solution: What already exists and whether it can be reused.
- Problem: Why a change is necessary.
- Options: Existing implementation, available libraries, or custom development.
- Recommendation: The preferred solution and its justification.
- Trade-offs: Performance, complexity, compatibility, maintenance, and dependencies.
- Impact: Which systems and files would change.

Explanations MUST be understandable without advanced programming knowledge.

Agents MUST distinguish verified repository facts from assumptions.

Agents MUST NOT present an unverified architectural preference as a technical necessity.

Agents SHOULD ask only questions that materially affect the implementation.

Routine implementation details within an approved approach do not require repeated approval.

4. Separation of Responsibilities

4.1. Gameplay Simulation

Gameplay simulation MUST remain independent of presentation frameworks.

Simulation rules MUST NOT depend directly on:

- Phaser scenes, sprites, or GameObjects.
- React components or hooks.
- DOM APIs.
- Animation or visual effects.
- UI-specific state.

The simulation determines game state. Rendering and UI visualize that state.

4.2. Rendering

Rendering systems MUST:

- Consume gameplay state without owning authoritative rules.
- Update affected objects without unnecessarily rebuilding unrelated objects.
- Separate asset selection from rendering implementation.
- Avoid unnecessary per-frame allocations.
- Use caching, batching, and visibility management when justified.
- Preserve deterministic visual variation where required.

4.3. User Interface

React components MUST NOT directly modify authoritative simulation state.

UI components SHOULD focus on presentation, interaction, and state visualization.

Shared gameplay rules MUST NOT be duplicated in UI components.

4.4. Networking and Persistence

- MUST preserve server authority over gameplay state.
- MUST validate state-changing commands on the server.
- MUST maintain explicit ownership of persistent data.
- MUST prevent duplicate application of state-changing operations.
- MUST preserve existing persistence contracts unless migration is authorized.

5. Turn-Based Simulation

The rendering loop and gameplay simulation MUST remain independent.

- MUST define explicit turn boundaries.
- MUST validate commands before execution.
- MUST separate command submission from execution.
- MUST maintain deterministic processing order where required.
- MUST prevent duplicate turn processing.
- MUST preserve consistency during concurrent operations.
- MUST prevent client-side bypass of authoritative rules.
- SHOULD support deterministic replay when required.
- SHOULD version simulation rules when historical compatibility requires it.

Gameplay simulation MUST NOT depend on rendering frame rate.

6. Hexagonal Map Architecture

- MUST use a documented and consistent coordinate convention.
- MUST separate logical map data from visualization.
- MUST centralize coordinate conversion, neighbor, and distance calculations.
- MUST preserve authoritative map data outside Phaser objects.
- SHOULD use chunking for large or expandable worlds.
- SHOULD limit rendering and updates to relevant regions.
- SHOULD cache expensive calculations when justified.
- MUST avoid unnecessary reconstruction of map objects.
- MUST keep terrain transitions independent of unrelated gameplay mechanics.

Map rendering SHOULD support, where required:

- Neighbor-aware terrain transitions.
- Seasonal texture variants.
- Resource-dependent visual appearance.
- Deterministic decoration placement.
- Visibility-based rendering.
- Efficient invalidation of changed regions.

Before introducing custom map rendering algorithms, agents MUST evaluate existing project functionality and suitable established solutions.

7. Data-Driven Game Content

Gameplay and visual content SHOULD be configurable without modifying core engine infrastructure.

Applicable content includes terrain, resources, units, buildings, vegetation, decorations, seasons, and gameplay modifiers.

- MUST separate content definitions from processing algorithms.
- MUST use stable content identifiers.
- MUST validate configuration and asset references.
- MUST document configuration properties and valid values.
- MUST avoid scattering hardcoded content rules across unrelated modules.
- SHOULD allow new content through configuration when supported by existing architecture.
- SHOULD preserve compatibility with persistent game data.
- MUST reuse existing configuration systems before creating new ones.

Agents MUST NOT introduce a new configuration format, registry, or content pipeline without evaluating existing alternatives and obtaining approval.

8. Feature Organization and Module Boundaries

The repository's actual structure is authoritative.

- MUST preserve existing module boundaries.
- MUST keep related functionality together.
- MUST define clear ownership of data and behavior.
- MUST avoid accessing unrelated feature internals directly.
- SHOULD expose deliberate public interfaces.
- MUST avoid circular imports.
- MUST avoid duplicate shared utilities.
- MUST NOT create generic modules that accumulate unrelated functionality.
- MUST NOT reorganize directories without demonstrated need and approval.

Feature-based organization SHOULD be preferred when it improves cohesion and maintainability.

Agents MUST NOT restructure the repository merely to match an example architecture.

9. Assets and Visual Content

- MUST follow existing asset organization and naming conventions.
- MUST keep related visual variants together.
- MUST distinguish source assets from generated runtime assets.
- MUST avoid duplicate source assets.
- MUST maintain consistent visual scale and positioning.
- SHOULD use texture atlases when beneficial.
- SHOULD validate asset references.
- MUST avoid hardcoding asset paths across unrelated systems.
- MUST preserve existing asset pipelines unless changes are authorized.

Visual state SHOULD be derived from gameplay data when appropriate.

Map decorations MUST NOT obscure essential gameplay information.

Rendering MUST remain readable across supported zoom levels.

10. Performance and Scalability

Performance changes MUST be evidence-driven.

- MUST identify actual bottlenecks before major optimization.
- MUST profile performance-sensitive changes where practical.
- MUST avoid unnecessary allocations in frequently executed code.
- SHOULD batch homogeneous operations when beneficial.
- SHOULD use typed arrays or spatial indexing when justified by workload.
- SHOULD minimize updates to invisible or unchanged objects.
- MUST NOT sacrifice maintainability for unmeasured optimization.
- MUST consider memory consumption, rendering cost, and server workload.

Agents MUST NOT introduce ECS, custom memory pools, complex caching, or specialized data structures without demonstrated benefit and required approval.

Performance optimizations MUST preserve gameplay correctness.

11. Naming and Code Conventions

Agents MUST follow the conventions already established in the repository.

For TypeScript, unless existing conventions differ:

- Types, interfaces, and classes: "PascalCase".

- Functions and variables: "camelCase".

- React components: "PascalCase".

- React hooks: "use" prefix.

- Configuration files: consistent existing naming convention.

- Constants: follow existing project style.

- MUST use descriptive names.

- MUST avoid ambiguous abbreviations.

- MUST maintain consistent casing.

- MUST avoid names such as "Manager2", "NewSystem", or "FinalVersion".

- MUST NOT rename established public interfaces without authorization.

- MUST NOT perform unrelated formatting changes.

Existing formatter, linter, and TypeScript configuration take precedence over stylistic preferences.

12. Documentation

Agents MUST update documentation when changing documented behavior, interfaces, configuration, or architectural contracts.

Significant architectural decisions SHOULD be recorded using the project's existing ADR conventions.

Architectural documentation SHOULD describe:

- Purpose and responsibilities.
- Public interfaces.
- Dependencies and data ownership.
- Extension points.
- Compatibility considerations.
- Performance implications.
- Testing and recovery requirements where relevant.

Agents MUST NOT create excessive documentation for trivial changes.

Agents MUST NOT duplicate information already maintained elsewhere.

13. Testing and Validation

Agents MUST use the commands actually defined by the repository.

Before running validation:

1. Inspect "package.json" and relevant workspace configuration.
2. Identify available scripts and the correct package manager.
3. Select checks relevant to the modified components.

Where available and applicable, run:

- Type checking.
- Linting.
- Unit tests.
- Integration tests.
- Production build.

Critical gameplay changes MUST include appropriate tests.

Hex-grid calculations, command validation, deterministic processing, and configuration validation MUST be tested when modified.

Agents MUST NOT claim checks passed unless they were executed successfully.

Unavailable or failing checks MUST be reported accurately.

14. AI Agent Workflow

14.1. Before Implementation

Agents MUST:

1. Read applicable instructions.
2. Inspect relevant code and documentation.
3. Identify existing solutions and reusable components.
4. Determine the smallest safe implementation.
5. Evaluate alternatives when architectural changes are considered.
6. Identify compatibility, performance, and maintenance risks.
7. Request approval for changes requiring user authorization.

14.2. During Implementation

Agents MUST:

1. Implement only the approved scope.
2. Reuse existing infrastructure.
3. Keep changes focused and minimal.
4. Avoid unrelated refactoring.
5. Preserve public contracts.
6. Avoid duplicate functionality.
7. Add or update relevant tests.
8. Validate new configuration and asset references.
9. Maintain compatibility with existing data.
10. Document significant architectural changes.

14.3. Before Finishing

Agents MUST:

1. Run relevant available validation checks.
2. Review the final diff.
3. Check for unintended changes.
4. Verify imports and module boundaries.
5. Confirm that no unauthorized dependencies or architectural changes were introduced.
6. Report remaining limitations.

The final response MUST include:

- Implemented: What changed.
- Files: Main files affected.
- Validation: Checks performed and results.
- Limitations: Remaining issues, if any.
- Approval needed: Any proposed changes not yet authorized.

15. Strict Prohibitions

Agents MUST NOT:

- Introduce dependencies, frameworks, or major architectural changes without required approval.
- Rewrite working subsystems without justification and authorization.
- Create duplicate implementations of existing capabilities.
- Introduce speculative infrastructure for unrequested features.
- Couple gameplay rules directly to Phaser or React.
- Introduce circular dependencies.
- Use uncontrolled global mutable state.
- Silently suppress critical errors.
- Scatter hardcoded gameplay configuration.
- Modify unrelated systems without necessity.
- Introduce placeholder implementations presented as complete.
- Claim successful tests without executing them.
- Change established conventions without justification.
- Add unnecessary complexity when a simpler solution meets the requirements.

16. Final Engineering Principle

Reuse before building. Evaluate before introducing. Ask before changing architecture.

Prefer the simplest maintainable solution that satisfies current requirements and preserves necessary extension points.

Professional game architecture is defined by clear responsibilities, predictable behavior, reliable integration, measured performance, and controlled complexity — not by the number of patterns, layers, or abstractions used.

Every significant architectural decision MUST be justified by actual project requirements.