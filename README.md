# Reference architectures

Patterns for designing and delivering AI systems on a client's infrastructure.

The documentation is split into two parts. Design covers the engineering decisions and reusable patterns behind a system. Delivery covers how to package, deploy, and operate that system, including runbooks for specific hosting environments.

## A working pattern: scoped knowledge stores

An agent doesn't need all its knowledge in one place. It can work across several smaller, topic-specific stores, retrieving what it needs while keeping the sources separate.

Consider an engineering team building an agentic system. The team maintains reusable knowledge in three areas: **context engineering, harness engineering, and post-training**. Each area has two stores:

- **Wiki:** Summaries of technical references, decisions, and guidance, with links back to the original sources.
- **Records:** Source documents, their individual sections, and embeddings for retrieval.

These are build stores. They contain the engineering knowledge used to design and build the system, rather than the client's domain data.

Each store is exposed through its own read-only MCP server. An agent can query all six stores in the same session, using a consistent interface while preserving where each result came from.

The same pattern applies to the system being built. Instead of engineering references, its stores contain the client's own knowledge and data. The agent accesses those stores through the same MCP interface.

**Why organize it this way?** Each store can be maintained, evaluated, and updated independently. A change to the context engineering references doesn't affect the post-training material or the client's data. New topics can be added without redesigning the agent or combining everything into a single knowledge base.

The result is a reusable architecture: the agent connects to the stores it needs, and the stores can change independently of the agent.

```
                         engineering agent
                                 │
                ┌────────────────┼────────────────┐
                │                │                │
                ▼                ▼                ▼
            Context          Harness           Post-
           engineering      engineering        training
                │                │                │
          wiki + records   wiki + records   wiki + records
                │                │                │
                └────────────────┼────────────────┘
                                 │
                          informs design
                                 │
                                 ▼
                        client agent system
                                 │
                       ┌─────────┴─────────┐
                       │                   │
                       ▼                   ▼
                  client wiki        client records
                       │                   │
                       └─────────┬─────────┘
                                 │
                         client's domain
                            knowledge

          Each individual store has its own read-only MCP server.
```

See `design/knowledge-system.md` for the full design.


## Design

The example above brings together several patterns: separating knowledge by topic, putting stores behind consistent interfaces, and keeping the agent independent of the data it reads.

The design pages cover these patterns individually, along with the principles that guide their implementation. Some apply to almost any project; others address specific concerns such as retrieval, evaluation, model integration, or agent runtimes. Together, they provide the building blocks for larger architectures like the knowledge system shown above.

Start with design/README.md for the terminology, principles, and page index.

| Page | Purpose | Status |
|---|---|---|
| [`design/README.md`](design/README.md) | Purpose, vocabulary, the one-line principles, the page index | written |
| **Principles** | | |
| `design/separation.md` | Generic code, specific configuration | planned |
| `design/model-at-the-edge.md` | The model is passed in; code runs without a key | planned |
| `design/fail-closed.md` | Refuse what you cannot honour; never fake a pass | planned |
| **Patterns** | | |
| `design/pipelines.md` | Acquire, convert, store: steps that chain | planned |
| `design/stores.md` | One reader, and a read-only server over it | planned |
| `design/authoring.md` | Content written by a model, under a deterministic tool | planned |
| `design/evaluation.md` | Run once, score many; judges and experts in files | planned |
| `design/integration.md` | Published formats, one adapter per producer | planned |
| `design/backends.md` | Many tools behind one interface, defaults measured | planned |
| `design/agents.md` | An agent client: runtimes, hosts, events, context | planned |
| `design/interfaces.md` | Library first, then CLI, then server | planned |
| **Architectures** | | |
| `design/knowledge-system.md` | The example above, worked in full up to hosting | planned |

**Skills** (planned). The design pages are also intended to be used by coding agents. Each pattern will have a corresponding skill, packaged with its reference documentation, so the agent can apply it when writing specifications or code. Rules that must always hold will also have deterministic checks or hooks rather than relying on instructions alone.

## Delivery

Designing the system is only part of the job. It also needs to reach the client in a form they can use, maintain, and operate.

That doesn't always mean deploying a full agent application. A skill or MCP server may be enough. Other projects need a CLI, a web interface, or a scheduled process. The delivery pages help make that choice and then work through what needs to be built, hosted, and handed over.

Start with the playbook for the sequence of decisions. The overviews compare delivery options, and the recipes explain how each one works. Once an option is selected, the runbooks provide the host-specific instructions for deploying and testing it.

**Playbook**

| Page | Purpose | Status |
|---|---|---|
| `playbook.md` | What to deliver, where it runs, and in what order; the decisions in the order you face them | draft |

**Overviews:** which delivery option to choose.

| Page | Purpose | Status |
|---|---|---|
| `agents.md` | The seven delivery options, compared by scope and cost | draft |
| `agent-anatomy.md` | The parts of an agent (model, files, tools, harness) and where each runs | draft |

**Recipes:** one per delivery option. Each covers what the option is, when to choose it, how to
host it, where its data lives, what it costs and how it fails.

| Page | Purpose | Status |
|---|---|---|
| `skills.md` | 1. Skills: the cheapest, and usually the right first deliverable | draft |
| `mcp-server.md` | 2. An MCP server: the default for a domain system | draft |
| `vendor-workbench.md` | 3. A connector and skills inside someone else's product | draft |
| `cli.md` | 4. A command-line tool, including pipeline tools that are not agents | draft |
| `web-application.md` | 5. A web application, from chat to full workbench | draft |
| `headless.md` | 6. Scheduled work that produces documents or data | draft |
| `their-surfaces.md` | 7. The client's own chat platforms, notebooks and portals | draft |

**Under every option**

| Page | Purpose | Status |
|---|---|---|
| `data-layer.md` | Where records live, provenance, retention and portability | draft |
| `hosting.md` | What runs where, and who is on call | draft |

**Runbooks:** one per component and host. Each gives the parameters, a smoke test with its
expected output, teardown, and the files needed.

| Page | Purpose | Status |
|---|---|---|
| `deployments/README.md` | The runbook index: scheduled against served, by host | draft |
| `deployments/pipeline-local.md` | A pipeline on one machine: acquire, convert, extract | draft |
| `deployments/pipeline-on-aws.md` | The same pipeline on a timer in AWS | draft |
| `deployments/mcp-on-aws.md` | An MCP server in AWS, the default access layer | draft |
| `deployments/web-on-aws.md` | A web application in AWS | draft |
| `deployments/storage.md` | The store both halves share, and what it costs a reader | draft |
| `deployments/access-shape.md` | What every served surface needs and no scheduled one does | draft |
| `deployments/runbook-shape.md` | The seven phases every runbook follows | draft |
| `deployments/permissions.md` | Least privilege per cloud | draft |
| `deployments/egress.md` | What must be reachable from a private subnet | draft |
| `deployments/assets/` | Dockerfile, justfile, permission policies, smoke manifest | draft |
