# Design

These pages document the engineering decisions and reusable patterns behind our AI systems. They're written for the people building and maintaining them.

Each page addresses a specific design concern: the recommended approach, when to use it, how it works, and the alternatives worth considering. The goal is to make decisions reusable. A project specification can reference `design/pipelines.md`, for example, rather than explain the same design choices again.

Pages start with a short explanation of the approach and its intended use, followed by implementation details and trade-offs. Where a pattern has not yet been implemented or tested, that should be clear.

The scoped knowledge stores example shows how several of these patterns fit together.

## Terminology

We use the following terms consistently across the design and delivery documentation.

| Term             | Definition                                                                                                                                                                         |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Package**      | Reusable code that is independent of any client or domain. Client-specific behavior comes from configuration.                                                                      |
| **Instance**     | A configured use of a package, defined by YAML and Markdown files, with its outputs under a common root, locally or in object storage.                                             |
| **Deployment**   | Where and how a system runs. This includes its hosting environment and operational configuration.                                                                                  |
| **Pipeline**     | A sequence of processing steps. Each step can run independently and consumes the output of the previous step.                                                                      |
| **Store**        | A collection of content made available for retrieval. A wiki store holds summarized Markdown and provenance; a records store holds source records, their parts, and embeddings.    |
| **Adapter**      | A thin interface between a component and its consumer, such as a read-only server over a store or a reader for another producer's format.                                          |
| **Build store**  | Engineering knowledge used to build AI systems, including context engineering, harness engineering, and post-training. We maintain these stores and reuse them across engagements. |
| **Domain store** | Knowledge and data belonging to a client's domain. Built from their sources and owned by the client.                                                                               |

The distinction between build and domain stores is about **content and ownership**, not implementation. Either can use a wiki or records store, exposed through the same read-only MCP interface.

## Working principles

Some decisions apply across the system and don't need a separate design page. Unless a project has a documented reason to do otherwise, we follow these rules:

- **Control spending.** Plan paid operations, show estimated costs, and require approval. Check each call against a spending ceiling before execution and report the cost afterward.
- **Keep records append-only.** Preserve existing records rather than silently overwriting them.
- **Test offline.** Tests must run without external services or model credentials.
- **Prefer established tools.** Adopt an existing solution when it meets the requirements rather than building another one.
- **Keep evidence with the design.** Record benchmarks, experiments, and measured findings alongside the decisions they support.
- **Enforce trust boundaries.** Treat retrieved content as untrusted, expose stores through read-only interfaces, and validate anything written back.

Larger architectural principles, such as separating generic code from configuration and failing closed, have their own pages below.

## Design index

The documentation is organized into three levels: **principles** establish rules that apply across projects, **patterns** describe how individual components should be built, and **architectures** show how those components work together.

*Planned* means the page has not yet been written and reviewed.

### Principles

Design decisions that should hold across the system.

| Page                   | Purpose                                                                                         | Status  |
| ---------------------- | ----------------------------------------------------------------------------------------------- | ------- |
| `separation.md`        | Keep reusable code separate from client-specific configuration                                  | planned |
| `model-at-the-edge.md` | Pass the model into the system; keep core code runnable without credentials                     | planned |
| `fail-closed.md`       | Reject unsupported operations rather than silently changing behavior or reporting false success | planned |

### Patterns

Reusable approaches to individual engineering problems. Each page explains the recommended design, its implementation, and when a different approach makes more sense.

| Page             | Purpose                                                                                     | Status  |
| ---------------- | ------------------------------------------------------------------------------------------- | ------- |
| `pipelines.md`   | Compose processing steps for acquisition, conversion, and storage                           | planned |
| `stores.md`      | Provide a consistent reader and a read-only server over each store                          | planned |
| `authoring.md`   | Use models to generate content within a deterministic authoring process                     | planned |
| `evaluation.md`  | Run evaluations once and score the results using configurable judges and expert criteria    | planned |
| `integration.md` | Integrate producers through published formats and small, dedicated adapters                 | planned |
| `backends.md`    | Support multiple tools behind a common interface and choose defaults using measured results | planned |
| `agents.md`      | Structure an agent client around runtimes, hosts, events, and context                       | planned |
| `interfaces.md`  | Build a reusable library first, then expose it through a CLI or server as needed            | planned |

### Architectures

Complete systems assembled from the principles and patterns above.

| Page                  | Purpose                                                                                             | Status  |
| --------------------- | --------------------------------------------------------------------------------------------------- | ------- |
| `knowledge-system.md` | The scoped knowledge stores example, developed into a full system design through hosting boundaries | planned |


