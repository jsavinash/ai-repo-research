# LLM Tools Field Guide: Repositories, Skills, Agents, and Operations

**Research snapshot:** November 7, 2026  
**Scope:** Public GitHub repositories and open-source components useful when developing, evaluating, operating, or securing LLM applications and agents.

## Executive overview

Working with an LLM involves more than selecting a model. A useful engineering toolkit covers the full loop:

1. **Prepare context and data** so the model receives relevant, well-formed information.
2. **Connect tools** so a model can take useful actions through controlled interfaces.
3. **Define repeatable procedures** with skills and agent workflows.
4. **Constrain outputs and actions** with schemas, permissions, hooks, and policy checks.
5. **Evaluate behavior** against representative examples and adversarial cases.
6. **Trace real usage** to find failures, latency, and cost drivers.

The sections below are a practical shortlist, not a global ranking. Star counts are approximate, time-sensitive signals of attention. They are not user counts, quality scores, security reviews, or guarantees of maintenance. Public GitHub tracking snapshots list examples such as Anthropic's `skills` at about 188k stars, `obra/superpowers` above 300k in one skill leaderboard, the MCP server catalog `punkpeye/awesome-mcp-servers` near 97k, Langfuse around 31–35k, and Promptfoo around 26k. These figures vary by collection and date; directories and skill bundles can accumulate stars more quickly than infrastructure libraries. [Anthropic's GitHub organization](https://github.com/anthropics), [skill leaderboard](https://www.skillleaderboard.com/agent/claude), [MCP server ranking](https://github.locker/topics/mcp-servers), [LLM evaluation tools snapshot](https://evalsreview.com/state-of-llm-evals)

## 1. Skills, instructions, hooks, and agent extensions

### What these pieces do

- **Instructions** describe project-wide conventions and constraints (for example, `AGENTS.md`, `CLAUDE.md`, or Copilot instructions).
- **Skills** package focused procedural knowledge, often as a `SKILL.md` plus optional scripts, examples, or references. A skill tells an agent how to do a kind of work; it does not itself grant access to an external service.
- **Hooks** run code at defined lifecycle events, such as before a tool call, after a file edit, or when a session starts or ends. Hooks are executable automation and deserve code review.
- **Plugins/extensions** bundle combinations of skills, hooks, commands, agents, and MCP configurations for a particular client.
- **MCP servers** expose tools and resources over a protocol. They provide connectivity; skills can explain when and how the agent should use that connectivity.

These components are not interchangeable. A skill can explain a workflow, while an MCP server exposes an action, and a hook can enforce a deterministic check at a lifecycle boundary. Agent Skills are increasingly cross-client, but exact locations and plugin behavior vary between clients. [Anthropic's skills repository](https://github.com/anthropics/skills), [Composio's skills catalog](https://github.com/ComposioHQ/awesome-claude-skills)

### Repositories to explore

| Repository | Useful for | Notes |
|---|---|---|
| [anthropics/skills](https://github.com/anthropics/skills) | Official example skills for document, design, and other workflows | A strong reference for skill structure; adapt to your client and verify the license for included material. |
| [obra/superpowers](https://github.com/obra/superpowers) | Structured software-development practices: planning, implementation, testing, and review | A popular, opinionated workflow bundle. Start with the individual skills you want, rather than installing everything blindly. [Repository releases](https://github.com/obra/superpowers/releases) |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Engineering workflows and codebase design practices | Personal skill collection; evaluate individual instructions for fit with your team's conventions. |
| [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | Front-end and software engineering workflows | Useful examples of reusable development guidance across agent environments. |
| [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) | React and web-development conventions | Focused implementation guidance; useful in front-end repositories. |
| [github/awesome-copilot](https://github.com/github/awesome-copilot) | Copilot instructions, agents, prompts, and skills | Helpful catalog for GitHub Copilot setups. |
| [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | Discovering skills and workflow examples by category | A large catalog, not a guarantee that every linked skill is maintained or safe. |
| [VoltAgent/awesome-openclaw-skills](https://github.com/VoltAgent/awesome-openclaw-skills) | Finding OpenClaw skills | Treat third-party skill directories as discovery indexes; inspect the actual code and permissions. |
| [everything-claude-code](https://github.com/affaan-m/everything-claude-code) | A broader agent setup with skills, commands, rules, hooks, and MCP examples | Multi-component configuration can save setup time but needs auditing and selective adoption. |
| [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | Anthropic-maintained plugin marketplace metadata | Useful starting point for plugins in its supported environment. |

### Hooks and deterministic automation

Hooks are most useful for checks that should run consistently: formatting, tests, secret scanning, permission prompts, audit logging, and blocking risky shell commands. They are less suitable for fuzzy judgments that need broad context. Search the plugin marketplace or the agent's official extension repository for hook examples; there is not one universal hook API across clients. [Claude Code plugin directory](https://claudecode.directory/plugins), [hook directory](https://claudecode.directory/hooks)

**Good practice:** keep hooks small, version-controlled, and understandable; log their actions; fail clearly; avoid downloading and executing remote code at runtime; and test the hook in a disposable project before enabling it across a workspace.

## 2. Agents and orchestration

An agent combines a model with a loop, tools, state, and stopping conditions. Pick an agent project by the environment it must act in—not only by its star count.

| Repository | Best fit | What stands out |
|---|---|---|
| [OpenClaw](https://github.com/openclaw/openclaw) | Persistent personal assistant with integrations and scheduled tasks | Broad assistant and tool ecosystem; review its current project governance and extension ecosystem before deployment. |
| [OpenHands](https://github.com/OpenHands/OpenHands) | Software-development agents in an inspectable execution environment | Open platform for agents that work with code and developer tools. [Research paper](https://arxiv.org/abs/2407.16741) |
| [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) | Stateful agents with explicit branching and checkpoints | Graph-based control flow; the project describes itself as infrastructure for resilient agents and had about 44.5k stars in a recent GitHub page snapshot. [Repository](https://github.com/langchain-ai/langgraph) |
| [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) | Role-based teams of agents | Concise abstractions for assigning roles, tools, and tasks. |
| [microsoft/autogen](https://github.com/microsoft/autogen) | Multi-agent application patterns and conversation | Useful when agent interaction and handoffs are central. |
| [FoundationAgents/MetaGPT](https://github.com/FoundationAgents/MetaGPT) | Role-based software task simulation | Models a structured team process as collaborating agents. |
| [pydantic/pydantic-ai](https://github.com/pydantic/pydantic-ai) | Typed Python agents and validated results | Type-safe outputs and dependency injection fit Python services. |
| [google/adk-python](https://github.com/google/adk-python) | Building and evaluating agents with Google's development toolkit | Agent, tool, and orchestration abstractions. |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Agents that interact with websites | Browser automation through an agent-oriented API. Pair with a restricted browser profile. |
| [Aider-AI/aider](https://github.com/Aider-AI/aider) | Git-aware coding assistance in the terminal | Edits existing repositories and works with normal Git review practices. |

For coding agents, consider an isolated worktree or container, limited credentials, explicit network allowlists, and human approval before consequential changes. A framework's “human in the loop” option is not a replacement for correct access control.

## 3. Tools and connections: MCP and APIs

MCP is a protocol for exposing tools and resources to compatible clients. Its practical value is reusable connectivity: an agent can use a GitHub, filesystem, database, or browser integration without bespoke glue for every model. The official server repository is an example/reference collection; `awesome-mcp-servers` is a discovery index. [Model Context Protocol servers](https://github.com/modelcontextprotocol/servers), [awesome MCP servers](https://github.com/punkpeye/awesome-mcp-servers)

| Repository | Capability | When to consider it |
|---|---|---|
| [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) | Reference MCP server implementations | Learn the protocol and inspect example integrations. Check each server's current maintenance status. |
| [punkpeye/awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) | Directory of MCP servers | Discover integrations by use case; vet the destination repository separately. |
| [jlowin/fastmcp](https://github.com/jlowin/fastmcp) | Build MCP servers and clients in Python | A streamlined Python SDK for exposing tools and resources. |
| [github/github-mcp-server](https://github.com/github/github-mcp-server) | Connect an agent to GitHub data and actions | Useful for issues, pull requests, repositories, and other GitHub workflows; scope tokens carefully. |
| [ChromeDevTools/chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp) | Browser inspection and automation through Chrome DevTools | Useful for web testing and browser debugging workflows. |
| [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) | Browser automation using Playwright | Use for controlled browser tasks and test automation. |
| [modelcontextprotocol/servers-archived](https://github.com/modelcontextprotocol/servers-archived) | Historical server examples | Archived examples can teach patterns, but should not be assumed maintained or production-ready. |

Use the narrowest tool set and least-privileged credentials that solve the job. Prefer read-only server permissions for research tasks. For write operations, use explicit approval gates, scoped tokens, and audit logs. MCP transports do not automatically make a server safe.

## 4. Evaluation and testing

LLM evaluation asks whether behavior meets a defined expectation across representative cases. Build a small “golden” dataset early: normal cases, edge cases, malformed inputs, refusals, and adversarial examples. Track answer quality and operational outcomes separately.

| Repository | Good for | Practical use |
|---|---|---|
| [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) | Prompt/model comparisons, regression tests, and red-team checks | Run a repeatable matrix of prompts and providers locally or in CI. |
| [confident-ai/deepeval](https://github.com/confident-ai/deepeval) | Python tests for LLM applications | Add metrics and test cases alongside application tests. |
| [vibrantlabsai/ragas](https://github.com/vibrantlabsai/ragas) | RAG evaluation and test-data generation | Measure retrieval and answer groundedness against your own domain examples. |
| [EleutherAI/lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) | Reproducible model benchmark evaluation | Compare models across benchmark tasks using a common harness. |
| [UKGovernmentBEIS/inspect_ai](https://github.com/UKGovernmentBEIS/inspect_ai) | Evaluations with tools, multi-turn tasks, and scoring | Useful for more involved agent evaluations and safety evaluations. |
| [openai/evals](https://github.com/openai/evals) | Evaluation examples and framework | Useful patterns for defining and running evals; inspect current project guidance before adopting. |
| [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) | Evaluation tied to traces and experiments | Connect qualitative debugging with dataset scoring. |
| [comet-ml/opik](https://github.com/comet-ml/opik) | LLM application evaluation and observability | An integrated platform option; review deployment and license details. |

A dated October 2026 survey put Promptfoo near 26k stars, Ragas near 16k, and recent comparisons consistently include DeepEval, Phoenix, Langfuse, MLflow, and Inspect among active choices. Stars and feature comparisons change; check release history and current license before standardizing. [Evaluation tools snapshot](https://evalsreview.com/state-of-llm-evals), [LLM evaluation comparison](https://vdf.ai/blog/llm-evaluation-tools/)

### Evaluation patterns worth adopting

- **Unit-style tests:** assert schema validity, required fields, tool arguments, and refusal behavior.
- **Golden-set regression:** compare outputs against reviewed examples after prompt, model, retrieval, or tool changes.
- **Pairwise review:** compare two candidate outputs and use human review for subjective or high-impact tasks.
- **RAG checks:** score context relevance, retrieval recall, answer relevance, and whether claims are supported by retrieved evidence.
- **Agent trajectory checks:** verify the tools selected, arguments used, stopping condition, and side effects—not just the final response.
- **Adversarial tests:** try indirect prompt injection, untrusted documents, malformed tool output, and attempts to access unrelated resources.

Do not let an LLM judge be the only judge for a high-stakes behavior. Calibrate automated scoring against human review and track disagreements.

## 5. Tracing and observability

Tracing records the path through an application: request, prompt version, model, retrieved context, tool calls, retries, output, latency, and token use. It helps answer “why did this run fail or cost more?” and creates useful material for later evaluations.

| Repository | Focus | Good fit |
|---|---|---|
| [langfuse/langfuse](https://github.com/langfuse/langfuse) | Traces, prompt management, datasets, evaluations | Self-hostable LLM development and production observability. |
| [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) | Tracing, evaluation, and debugging | Inspect retrieval and agent spans and connect them to evaluations. |
| [comet-ml/opik](https://github.com/comet-ml/opik) | Tracing and evaluation | Teams seeking an integrated LLM development platform. |
| [Helicone/helicone](https://github.com/Helicone/helicone) | LLM gateway and request observability | Useful for centralized request logging and provider visibility. |
| [mlflow/mlflow](https://github.com/mlflow/mlflow) | Experiment tracking and model lifecycle, including GenAI workflows | Fits teams already using MLflow for model operations. |
| [BerriAI/litellm](https://github.com/BerriAI/litellm) | Unified model gateway, routing, and logging | Useful when routing, fallback, budgets, and provider abstraction matter. |

Instrument only what you need. Prompts, retrieved documents, and tool arguments can contain secrets or personal data; redact or hash sensitive fields and apply retention controls. Record model and prompt versions so a trace remains interpretable later.

## 6. Data preparation and retrieval

RAG quality often depends more on parsing, chunking, metadata, and retrieval than on changing the language model. Build a baseline retrieval evaluation before tuning complicated agent loops.

| Repository | Focus | Best starting use |
|---|---|---|
| [Unstructured-IO/unstructured](https://github.com/Unstructured-IO/unstructured) | Extracting and partitioning content from common file formats | Ingest mixed PDFs, HTML, office documents, and other business files. |
| [UKPLab/sentence-transformers](https://github.com/UKPLab/sentence-transformers) | Embeddings and reranking models | Semantic search and similarity baselines. |
| [FlagOpen/FlagEmbedding](https://github.com/FlagOpen/FlagEmbedding) | Embedding and reranking models | Explore retrieval models and reranking for RAG. |
| [run-llama/llama_index](https://github.com/run-llama/llama_index) | Data connectors, indexing, retrieval, and query workflows | Build data-connected LLM applications. |
| [deepset-ai/haystack](https://github.com/deepset-ai/haystack) | Search and RAG pipelines | Compose retrieval systems as modular pipelines. |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | RAG with document understanding | Consider when complex documents and parsing are central. |
| [chroma-core/chroma](https://github.com/chroma-core/chroma) | Embedding database and retrieval | A simple vector-store starting point for prototypes and applications. |
| [qdrant/qdrant](https://github.com/qdrant/qdrant) | Vector search engine | Production-oriented vector retrieval and filtering. |
| [weaviate/weaviate](https://github.com/weaviate/weaviate) | Vector database and search | Explore hybrid and vector search with an application-facing API. |
| [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) | Declarative LLM programs and optimization | Systematically tune multi-step LLM programs on examples. |

Design data ingestion with provenance: preserve source identifiers, timestamps, access controls, and document versions. Evaluate retrieval on queries users actually ask. A high similarity score does not prove that the retrieved passage contains the answer.

## 7. Reliable outputs and guardrails

Reliability controls should be layered. Use constrained schemas for format, deterministic validation for business rules, authorization checks for actions, and model-level policy checks as an additional layer. No prompt-only rule or content filter reliably prevents all misuse.

| Repository | Focus | Suitable role |
|---|---|---|
| [guardrails-ai/guardrails](https://github.com/guardrails-ai/guardrails) | Output validation and corrective flows | Enforce structured output and validate content against defined checks. |
| [NVIDIA/NeMo-Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) | Programmable rails around conversation, retrieval, and tool execution | Express policy and conversation constraints. |
| [protectai/llm-guard](https://github.com/protectai/llm-guard) | Input and output scanners | Add detectors for sensitive data, unsafe content, and other defined risks. |
| [NVIDIA/garak](https://github.com/NVIDIA/garak) | Adversarial security probes | Test models and applications for vulnerabilities; use findings to improve controls. |
| [meta-llama/PurpleLlama](https://github.com/meta-llama/PurpleLlama) | LLM safety and cybersecurity resources | Explore evaluation and safety tooling from Meta. |
| [facebookresearch/LlamaFirewall](https://github.com/meta-llama/PurpleLlama/tree/main/LlamaFirewall) | Agent security guardrails and scanners | Study defense layers for tool-using agents. [Technical paper](https://arxiv.org/abs/2505.03574) |
| [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) | Red-team testing and policy regressions | Test for prompt injection, jailbreaks, and unsafe behavior in CI. |

Guardrails need an explicit threat model and test suite. A recent paper on guardrail evaluation reports substantial bypass rates in code-generation scenarios, underscoring that filters should not be treated as complete security boundaries. [CS-Guard benchmark](https://arxiv.org/abs/2609.09798), [guardrail limitations review](https://arxiv.org/abs/2402.01822)

For agent safety, controls outside the model are essential: isolate execution, scope tool permissions, separate read and write credentials, put approval gates before consequential actions, and log tool calls. Treat skills, hooks, MCP servers, and prompt templates as software dependencies that can execute code or influence privileged actions. A recent supply-chain study of skills shows why adoption and risk signals should be assessed separately. [Skill Constellations study](https://arxiv.org/abs/2610.11169)

## 8. Model access, serving, and local development

- [ollama/ollama](https://github.com/ollama/ollama) — Quick local model downloads, execution, and an API.
- [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) — Portable quantized inference on CPUs and supported accelerators.
- [vllm-project/vllm](https://github.com/vllm-project/vllm) — High-throughput model serving for GPU workloads.
- [sgl-project/sglang](https://github.com/sgl-project/sglang) — Serving runtime and structured generation workflows.
- [huggingface/transformers](https://github.com/huggingface/transformers) — Broad model loading, training, and inference library.
- [open-webui/open-webui](https://github.com/open-webui/open-webui) — Self-hosted user interface for local and compatible hosted models.
- [Aider-AI/aider](https://github.com/Aider-AI/aider) — Terminal-based, Git-aware coding assistant.
- [cline/cline](https://github.com/cline/cline) — IDE-based coding agent that uses developer tools with user oversight.

Use local inference when privacy, offline use, or experimentation matters; use a serving engine when concurrency and throughput matter. Benchmark on your own prompts, context lengths, hardware, and concurrency. Model quality and serving speed are separate selection questions.

## 9. Suggested starter stacks

### Personal research assistant

- Model: Ollama for local experiments or a hosted model through LiteLLM.
- Data: Unstructured for ingestion; sentence-transformers plus Chroma or Qdrant for retrieval.
- Interface: Open WebUI or a small application built with LlamaIndex.
- Evaluation: Promptfoo for behavior tests and Ragas for RAG-specific checks.

### Production RAG service

- Ingestion: Unstructured, with source metadata and access labels.
- Retrieval: Qdrant or Weaviate; compare dense, keyword, and hybrid retrieval.
- Orchestration: Haystack or LlamaIndex.
- Evaluation: Ragas or DeepEval, plus a reviewed golden dataset.
- Observability: Langfuse or Phoenix with prompt/model version tracking.
- Guardrails: schema validation plus application authorization and promptfoo red-team cases.

### Coding agent in a repository

- Begin with repository instructions and a small, trusted set of skills.
- Run the agent in a branch, worktree, or isolated container.
- Grant only the tools needed for the task; use read-only credentials by default.
- Add hooks for formatting, tests, secret checks, and risky command blocking.
- Use an agent suited to the task: Aider, OpenHands, Cline, or a controlled LangGraph workflow.
- Require human review before merging or deploying.

## 10. Selection checklist

Before adopting a repository, check:

1. **Fit:** Does it solve a specific workflow you have, or only add another layer?
2. **Maintenance:** Recent releases, meaningful commits, issue responses, and clear ownership.
3. **License:** Code, models, datasets, and bundled assets can have different terms.
4. **Security:** Review install scripts, hooks, dependencies, network calls, credentials, and permissions.
5. **Interoperability:** Can it export traces, tests, data, and prompts in usable formats?
6. **Evaluation:** Can you measure a baseline and catch regressions for your task?
7. **Operations:** Deployment, upgrade, backup, data retention, and failure modes are understood.
8. **Cost:** Include inference, retrieval, tool, logging, and evaluation costs.

## 11. Recommended adoption order

For most teams, a sensible order is:

1. Create a small, reviewed evaluation set before adding complexity.
2. Add tracing and prompt/model versioning.
3. Build the data or tool connection required for the first use case.
4. Add schema validation and deterministic application checks.
5. Add an agent loop only when fixed orchestration is insufficient.
6. Add hooks and guardrails around actual failure modes found in testing.
7. Expand permissions and automation gradually, with approval for high-impact side effects.

This sequence makes failures observable before the system receives broad access or autonomy.

## Sources and how popularity was assessed

- GitHub repository pages and project documentation linked throughout the guide are the primary references for project purpose and code.
- Star counts are approximate snapshots from public trackers or GitHub pages observed around November 7, 2026; they are intentionally not used as the sole ranking criterion.
- Category comparisons: [LLM evaluation tools](https://evalsreview.com/state-of-llm-evals), [open-source evaluation comparison](https://vdf.ai/blog/llm-evaluation-tools/), [MCP server ranking](https://github.locker/topics/mcp-servers), [skills leaderboard](https://www.skillleaderboard.com/agent/claude).
- Safety and ecosystem research: [Skills supply-chain study](https://arxiv.org/abs/2610.11169), [agent plugin maintenance study](https://arxiv.org/abs/2608.28497), [guardrail limitations review](https://arxiv.org/abs/2402.01822).
