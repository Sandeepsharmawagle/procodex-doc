# Low-Level Design (LLD)

## Overview

Low-Level Design (LLD) is the technical design phase that follows High-Level Design (HLD). While HLD defines system architecture, modules, and technology choices at a coarse-grained level, LLD describes the internal structure of modules, data structures, algorithms, interfaces, and detailed component interactions required to implement the system.

LLD is intended for implementers: developers, reviewers, and testers. It should be precise enough to convert into working code with minimal ambiguity.

## Goals

- Specify classes, interfaces, and methods with signatures and responsibilities.
- Define data models, schemas, and in-memory representations.
- Describe algorithms, edge cases, error handling, and concurrency control.
- Provide diagrams and sequence flows to show interactions.
- Capture non-functional considerations (performance, scalability, security).

## LLD vs HLD

- **HLD:** system-level components, integration points, high-level protocols, third-party services, deployment overview.
- **LLD:** per-component internals, data structures, API contracts, detailed flows, and implementation notes.

## Key Artifacts

- **Class/Component Diagrams:** show classes, interfaces, methods, and relationships.
- **Sequence Diagrams:** show call/request flows for key use-cases.
- **Data Model / Schema:** tables, columns, types, indexes, constraints, and example rows.
- **API Contracts:** endpoints, request/response schemas, error codes, and examples.
- **State Machines / Flowcharts:** for complex lifecycle or protocol logic.
- **Concurrency Plan:** locking, synchronization, thread model, and transaction boundaries.
- **Storage & Caching Plan:** what to store where, TTLs, eviction policies.
- **Failure & Recovery Plan:** retries, circuit breakers, fallback behavior, durability.

## Steps to Create LLD

1. Collect HLD outputs and functional requirements for the module.
2. Identify the core responsibilities the module must fulfill.
3. Draft class/component boundaries and public interfaces.
4. Define data models and persistence format (DB schema / documents / files).
5. For each API or operation, write a sequence diagram or flow describing calls and side effects.
6. Specify algorithms with complexity notes and edge-case handling.
7. Document concurrency model and locking strategy.
8. Add observability hooks: metrics, logs, traces, and health checks.
9. List configuration knobs and failure modes.
10. Review with peers and update based on feedback.


## Example: Simple URL Shortener (LLD highlights)

- **Components:** `URLService`, `Storage`, `Analytics`, `Auth`

- **Class sketch (pseudo-code):**

```text
class URLService:
  def createShort(originalUrl: string, userId?: string) -> string
  def resolve(shortCode: string) -> string

interface Storage:
  def put(key: string, value: object)
  def get(key: string) -> object | null
```

- **Data Model (SQL):** `urls(id PK, short_code UNIQUE, original_url TEXT, user_id, created_at)`

- **Key flows:**

  - Create: validate URL → generate collision-resistant short code (hash+base62 or counter+salt) → store in DB → return code.
  - Resolve: read from cache; if miss, read DB; record analytics asynchronously.

- **Concurrency & Scaling:**

  - Use a cache (e.g., Redis) for hot lookups.
  - For unique short codes, use centralized counter or namespace-sharded counters to avoid collisions and ensure monotonicity.

- **Failure Handling:** retries with backoff for storage operations; idempotency for create operations.

## Concurrency & Data Safety

- Describe where locks or transactions are required (e.g., avoid duplicate inserts).
- Prefer optimistic concurrency when possible; use pessimistic locks for critical sections with low concurrency.
- Define transactional boundaries and compensation actions for partial failures.

## Performance & Scalability Notes

- Document latencies and throughput targets for each API.
- Provide caching strategies, TTLs, and invalidation rules.
- Describe partitioning/sharding schemes and how the system scales horizontally.

## Security & Validation

- Input validation and sanitization rules.
- Authentication and authorization checks at API boundaries.
- Secrets management, encryption (at-rest/in-transit), and auditing requirements.

## Testing Considerations

- Unit tests for algorithms and edge cases.
- Integration tests for component interactions and persistence.
- Load tests for performance targets and chaos tests for failure recovery.

## Documentation & Handoff

- Include code-level examples and small snippets for the most complex parts.
- Provide a README for the module with setup steps, configuration, and run commands.
- Link to related HLD documents and deployment diagrams.

## Checklist (Quick)

- Module responsibilities defined.
- Public APIs and signatures documented.
- Data model and schema specified.
- Sequence/flow diagrams for main use-cases provided.
- Concurrency and failure handling described.
- Observability hooks listed.
- Performance targets and scaling plan included.

## Further Reading

- Design Patterns (Gamma et al.) for reusable solutions.
- System Design literature for distributed considerations.

