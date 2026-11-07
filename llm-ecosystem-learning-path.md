# LLM Ecosystem Learning Path

*A guided, practical route through the repositories and tools catalogued in this folder*

**Prepared:** November 7, 2026  
**Audience:** Developers and technical researchers who want to understand, build, and operate useful LLM applications and agents.

## How to use this guide

This guide consolidates the two research documents in the folder:

- [GitHub AI ecosystem directory](github-ai-ecosystem.md) — a broad, category-based catalog of 104 repositories.
- [LLM tools field guide](llm-tools-field-guide.md) — practical repositories and engineering guidance for applications, agents, evaluation, operations, and safety.

The directory is a discovery map; this document is the recommended learning order. You do not need to learn every repository. For each phase, choose one primary tool, build the small deliverable, and keep the other repositories as alternatives to compare when a real need appears.

## The mental model: learn the stack in dependency order

An LLM system can be viewed as a pipeline:

```text
User/task
   ↓
Application logic and instructions
   ↓
Context: data retrieval + tool results
   ↓
Model API or local inference runtime
   ↓
Validation, policy checks, and approval gates
   ↓
Response or controlled action
   ↘ evaluation + traces at every step
```

Skills describe reusable procedures. Tools expose capabilities. MCP is one way to connect tools. Agents add a loop that can select tools and continue working. Hooks run code at lifecycle points. Evaluations measure behavior. Tracing records what happened. Guardrails and ordinary software controls limit failures and side effects.

Learn these concepts before adopting a large agent framework: it is much easier to debug an explicit pipeline than a system with hidden retrieval, tool calls, retries, and state.

## Learning roadmap

### Phase 1 — Understand models, tokens, prompts, and limits

**Goal:** Know what a model receives, what it returns, and why output can be wrong.

Learn:

- Tokens, context windows, sampling, system/user messages, and structured outputs.
- The distinction between a model, an inference runtime, an application framework, and a hosted service.
- Why model weights, code, and training data may have different licenses.
- Why a plausible answer is not evidence that the answer is correct.

Read or use:

- [Generative AI for Beginners](https://github.com/microsoft/generative-ai-for-beginners)
- [Hands-On Large Language Models](https://github.com/HandsOnLLM/Hands-On-Large-Language-Models)
- [Transformers](https://github.com/huggingface/transformers) as a model-library reference.

**Exercise:** Call one model with the same task using three prompt variants. Record the prompt, model, output, latency, and what failed. Turn five representative inputs into a small manual evaluation set.

**Checkpoint:** Explain why prompting alone cannot guarantee a valid answer, reliable tool use, or safe action.

### Phase 2 — Run a model and understand inference tradeoffs

**Goal:** Compare local inference and hosted model access without confusing ease of use with model quality.

Learn:

- Local model size, quantization, memory, throughput, and latency.
- The difference between interactive local inference and a shared production serving engine.
- How to benchmark on your own hardware and representative prompts.

Choose one starting point:

- [Ollama](https://github.com/ollama/ollama): simple local model download/run workflow.
- [llama.cpp](https://github.com/ggml-org/llama.cpp): portable, quantized inference with more runtime controls.
- [MLX-LM](https://github.com/ml-explore/mlx-lm): local experimentation on Apple silicon.
- [Transformers](https://github.com/huggingface/transformers): direct model loading and broader model experimentation.

Later, compare serving engines:

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [TensorRT-LLM](https://github.com/NVIDIA/TensorRT-LLM)

**Exercise:** Run a small model locally and a hosted model on the same test prompts. Compare output quality, speed, memory use, and setup effort. Do not assume a public benchmark predicts your workload.

**Checkpoint:** Choose a model based on task quality, latency, privacy, deployment constraints, and cost—not star count alone.

### Phase 3 — Build a simple application before adding retrieval or agents

**Goal:** Make one reliable model call through ordinary application code.

Learn:

- Provider APIs, retries, timeouts, streaming, and structured output schemas.
- Application-side validation and error handling.
- Why the model should not own business authorization or deterministic rules.

Useful references:

- [LangChain](https://github.com/langchain-ai/langchain) for integrations and application components.
- [DSPy](https://github.com/stanfordnlp/dspy) for declarative, example-driven optimization of LLM programs.
- [LiteLLM](https://github.com/BerriAI/litellm) for provider routing and a common model gateway.

**Exercise:** Build a small extraction or classification endpoint. Require JSON output, validate it in code, and handle malformed responses explicitly.

**Checkpoint:** The application detects invalid model output and fails safely rather than silently trusting it.

### Phase 4 — Add evaluation and regression tests

**Goal:** Be able to tell whether a change improved the system.

Learn:

- Golden datasets, deterministic assertions, human review, pairwise comparison, and model-as-judge limits.
- The difference between model benchmarking and application evaluation.
- How to evaluate tool-use trajectories and side effects, not just final text.

Start with:

- [Promptfoo](https://github.com/promptfoo/promptfoo): prompt/model comparisons, regression checks, and red-team tests.
- [DeepEval](https://github.com/confident-ai/deepeval): Python-oriented LLM application tests.
- [OpenAI Evals](https://github.com/openai/evals): evaluation patterns and examples.

For model benchmark work, explore [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) or [Inspect AI](https://github.com/UKGovernmentBEIS/inspect_ai). For RAG evaluation later, use [Ragas](https://github.com/vibrantlabsai/ragas).

**Exercise:** Create 20–50 reviewed cases: common inputs, edge cases, malformed input, refusal cases, and known failure cases. Run them before and after one prompt/model change.

**Checkpoint:** You can reproduce a baseline and identify regressions. Keep subjective quality review separate from hard constraints such as valid schema or allowed tool calls.

### Phase 5 — Prepare data and add retrieval (RAG)

**Goal:** Ground answers in a bounded document collection and measure retrieval quality.

Learn in this order:

1. Parse documents and preserve source, version, timestamp, and access-control metadata.
2. Chunk the content and choose embedding and keyword retrieval methods.
3. Add vector storage and metadata filtering.
4. Rerank candidates when necessary.
5. Cite retrieved sources in answers and test that the source supports the claim.

Repository options:

- Parsing: [Unstructured](https://github.com/Unstructured-IO/unstructured)
- Embeddings/reranking: [Sentence Transformers](https://github.com/UKPLab/sentence-transformers), [FlagEmbedding](https://github.com/FlagOpen/FlagEmbedding)
- Vector search: [Chroma](https://github.com/chroma-core/chroma), [Qdrant](https://github.com/qdrant/qdrant), [Weaviate](https://github.com/weaviate/weaviate)
- RAG orchestration: [LlamaIndex](https://github.com/run-llama/llama_index), [Haystack](https://github.com/deepset-ai/haystack), [RAGFlow](https://github.com/infiniflow/ragflow)
- Evaluation: [Ragas](https://github.com/vibrantlabsai/ragas)

Pick one parser, one retriever/database, and one orchestration path. Avoid combining several frameworks until a measured need calls for it.

**Exercise:** Build a document Q&A prototype with source citations. Create questions whose answers are present, absent, ambiguous, and restricted by access. Score retrieval separately from answer quality.

**Checkpoint:** The system can abstain when the collection lacks evidence; it does not treat a similar passage as proof.

### Phase 6 — Add tools and MCP connections

**Goal:** Let the application access only the external capabilities it needs.

Learn:

- Function/tool schemas, argument validation, tool result handling, and idempotency.
- MCP concepts: clients, servers, tools, resources, transport, and permissions.
- Read-only versus write access, scoped credentials, audit logs, and approval flows.

Explore:

- [Official MCP servers](https://github.com/modelcontextprotocol/servers) for examples and reference implementations.
- [Awesome MCP Servers](https://github.com/punkpeye/awesome-mcp-servers) as a discovery catalog; vet each linked project separately.
- [FastMCP](https://github.com/jlowin/fastmcp) for building Python MCP servers.
- [GitHub MCP Server](https://github.com/github/github-mcp-server) for repository and issue workflows.
- [Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp) or [Playwright MCP](https://github.com/microsoft/playwright-mcp) for browser tasks.

**Exercise:** Add one read-only tool (for example, searching a test dataset). Validate inputs, return a small typed result, and log every invocation. Only add write access after the read workflow is reliable.

**Checkpoint:** Tool access is explicit, minimal, and independently authorized by application code; the model cannot grant itself new permissions.

### Phase 7 — Use skills, instructions, and hooks

**Goal:** Make good workflows repeatable and automate deterministic checks.

Learn:

- Put durable project conventions in repository instructions.
- Use a skill for a repeatable task procedure, with examples and optional helper scripts.
- Use a hook for deterministic work at a known event, such as running formatting or blocking a dangerous command.
- Treat plugins as dependency bundles and inspect what they install and execute.

Repositories to sample:

- [Anthropic skills](https://github.com/anthropics/skills): reference skill patterns.
- [obra/superpowers](https://github.com/obra/superpowers): structured software-development workflows.
- [GitHub Awesome Copilot](https://github.com/github/awesome-copilot): Copilot instructions, agents, and skills.
- [Composio Awesome Claude Skills](https://github.com/ComposioHQ/awesome-claude-skills): broad skills discovery catalog.
- [Everything Claude Code](https://github.com/affaan-m/everything-claude-code): example bundle of skills, rules, hooks, and MCP configuration.

**Exercise:** Write one project skill for a workflow you perform repeatedly, including when it applies, what input it needs, what steps to follow, and how to verify the result. Add a separate hook for a deterministic check.

**Checkpoint:** The skill teaches procedure; the hook enforces a check. Neither is trusted just because it is popular.

### Phase 8 — Introduce agents only when fixed workflows are insufficient

**Goal:** Use autonomy for tasks that benefit from planning or tool selection, while making state and stopping conditions visible.

Start with a workflow graph or a single agent, not a team of agents. Multi-agent systems add coordination, cost, latency, and more failure paths.

- Fixed/stateful workflows: [LangGraph](https://github.com/langchain-ai/langgraph)
- Typed Python agents: [Pydantic AI](https://github.com/pydantic/pydantic-ai)
- Multi-agent patterns: [AutoGen](https://github.com/microsoft/autogen), [CrewAI](https://github.com/crewAIInc/crewAI), [MetaGPT](https://github.com/FoundationAgents/MetaGPT)
- Coding agents: [OpenHands](https://github.com/OpenHands/OpenHands), [Aider](https://github.com/Aider-AI/aider), [Cline](https://github.com/cline/cline)
- Browser agents: [Browser Use](https://github.com/browser-use/browser-use)
- Persistent assistant: [OpenClaw](https://github.com/openclaw/openclaw)

**Exercise:** Create a bounded agent task with one tool, a maximum step count, explicit success criteria, and a human approval gate before any external write. Test interruption, tool failure, repeated calls, and malicious instructions in retrieved content.

**Checkpoint:** You can inspect the plan, state, tool calls, budget, and stopping reason. If a fixed pipeline solves the task, prefer the fixed pipeline.

### Phase 9 — Add tracing, monitoring, and cost controls

**Goal:** Understand production runs and diagnose why a user received a result.

Learn to trace model and prompt versions, retrieval spans, tool calls, retries, latency, token use, errors, and final outputs. Minimize sensitive data in logs and set retention rules.

- [Langfuse](https://github.com/langfuse/langfuse): traces, prompt management, datasets, and evaluations.
- [Arize Phoenix](https://github.com/Arize-ai/phoenix): tracing and debugging retrieval/agent workflows.
- [Opik](https://github.com/comet-ml/opik): integrated evaluation and observability.
- [Helicone](https://github.com/Helicone/helicone): request gateway and LLM observability.
- [MLflow](https://github.com/mlflow/mlflow): experiment and model lifecycle tracking.
- [LiteLLM](https://github.com/BerriAI/litellm): routing, budgets, and provider abstraction.

**Exercise:** Trace one successful and one failed run end to end. Confirm that you can identify prompt/model version, retrieval results, tool parameters, latency, and cost without exposing secrets.

**Checkpoint:** Your team can debug a failure from evidence, and can detect a material increase in latency or cost.

### Phase 10 — Add guardrails and security controls

**Goal:** Reduce risk with layered, testable controls around inputs, outputs, tools, and execution.

Learn the distinction between:

- Output structure validation and policy enforcement.
- Content moderation and security boundaries.
- Prompt-injection detection and access control.
- Model refusals and application authorization.

Explore:

- [Guardrails AI](https://github.com/guardrails-ai/guardrails) for output validation patterns.
- [NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) for programmable conversation and tool rails.
- [LLM Guard](https://github.com/protectai/llm-guard) for input/output scanners.
- [Garak](https://github.com/NVIDIA/garak) and [Promptfoo](https://github.com/promptfoo/promptfoo) for adversarial testing.
- [PurpleLlama](https://github.com/meta-llama/PurpleLlama) for safety and cybersecurity tooling.

**Exercise:** Test prompt injection from both a user message and a retrieved document. Confirm the agent cannot reach unapproved files, credentials, hosts, or APIs even if the model follows malicious instructions.

**Checkpoint:** Security depends on permissions, isolation, and validation outside the model. Filters are supplementary defenses, not guarantees. [Guardrail limitations review](https://arxiv.org/abs/2402.01822), [agent skill supply-chain study](https://arxiv.org/abs/2610.11169)

### Phase 11 — Deploy, scale, and maintain

**Goal:** Move the system from prototype to operation with a clear rollback and maintenance path.

Learn:

- Service isolation, secrets handling, model/provider failover, rate limits, queues, and resource planning.
- Inference benchmarks using representative context lengths and concurrency.
- Versioning of models, prompts, skills, tools, and evaluation data.
- Runbooks for incidents, provider changes, data refreshes, and rollback.

Explore [vLLM](https://github.com/vllm-project/vllm) or [SGLang](https://github.com/sgl-project/sglang) for serving, [Ray](https://github.com/ray-project/ray) for distributed workloads, [BentoML](https://github.com/BentoML/BentoML) for model services, and [MLflow](https://github.com/mlflow/mlflow) for experiment/model lifecycle needs.

**Exercise:** Deploy a small service with a health check, rate limit, trace export, cost budget, and rollback to the previous prompt/model configuration.

**Checkpoint:** A failure can be detected, contained, and rolled back without losing evaluation history.

## Choose a track

### Application engineer

Follow phases 1 → 3 → 4 → 5 → 6 → 9 → 10. Add agent orchestration only if your product needs dynamic planning or multi-step tool use.

### AI/ML engineer

Follow phases 1 → 2 → 4 → 5 → 9 → 11. Add fine-tuning with [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory) or [Axolotl](https://github.com/axolotl-ai-cloud/axolotl) only after evaluation shows that prompting, retrieval, or model selection is insufficient.

### Agent/tool builder

Follow phases 1 → 3 → 4 → 6 → 7 → 8 → 9 → 10. Start with one tool and one agent loop; add multi-agent orchestration only when independent roles produce a measurable benefit.

### Research learner

Follow phases 1 → 2 → 4, then study [Transformers](https://github.com/huggingface/transformers), [PyTorch](https://github.com/pytorch/pytorch), [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness), and [Open Instruct](https://github.com/allenai/open-instruct). Read model and dataset licenses before attempting reproduction.

## Compact reference: where to look in the source documents

| Need | Start in the source document |
|---|---|
| Survey of popular projects across eight AI categories | [GitHub AI ecosystem directory](github-ai-ecosystem.md) |
| Practical skills, agent, MCP, eval, tracing, RAG, guardrail, and starter-stack guidance | [LLM tools field guide](llm-tools-field-guide.md) |
| Full repository purpose, stack, and popularity rationale | Ecosystem directory entries |
| Practical selection and security advice | Field guide sections 9–11 |

## Principles to carry forward

1. **Measure before adding complexity.** A small evaluation set is more valuable than another framework dependency.
2. **Make each layer visible.** Preserve versions and traces for prompts, models, retrieved data, tools, and skills.
3. **Keep authority in ordinary software.** The model proposes; application logic validates and authorizes.
4. **Prefer the smallest useful system.** Begin with one model call, one dataset, and one tool if needed.
5. **Treat popularity as discovery, not evidence.** Verify maintenance, license, security, and fit at the repository you plan to use.
6. **Grow autonomy slowly.** Add side-effecting tools only after evaluation, isolation, least privilege, and approval controls are in place.
