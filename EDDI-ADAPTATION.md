# EDDI × SuperInstance Adaptation Plan

> Comprehensive integration plan for adapting EDDI (183K-line Java/Quarkus multi-agent orchestration middleware) into the SuperInstance ecosystem, targeting massive parallel agent coordination with CUDAclaw GPU dispatch.

---

## Table of Contents

1. [EDDI Architecture Analysis](#1-eddi-architecture-analysis)
2. [SuperInstance Adaptation Plan](#2-superinstance-adaptation-plan)
3. [Concrete Integration Steps](#3-concrete-integration-steps)
4. [Agent Config Examples](#4-agent-config-examples)
5. [Appendix: Key EDDI Source Maps](#5-appendix-key-eddi-source-maps)

---

## 1. EDDI Architecture Analysis

### 1.1 Config-Driven Agent System

EDDI is fundamentally **config-driven**: agents are defined entirely through JSON configuration documents stored as versioned resources in MongoDB/PostgreSQL. No code is needed to create an agent.

**How it works:**

An agent is assembled from typed resource documents, each identified by URI:

```
AgentConfiguration
  ├── workflows[] (packages) → ordered list of ExtensionDescriptors
  │     ├── eddi://ai.labs.parser          → NLP parsing (dictionaries, expressions)
  │     ├── eddi://ai.labs.rules           → Behavior rules (condition → action mapping)
  │     ├── eddi://ai.labs.properties      → Property extraction
  │     ├── eddi://ai.labs.httpcalls       → REST API calls (pipeline mode)
  │     ├── eddi://ai.labs.mcpcalls        → MCP server connections (pipeline + agent mode)
  │     ├── eddi://ai.labs.llm             → LLM interaction (chat or agent/tool-calling mode)
  │     └── eddi://ai.labs.output          → Output generation
  └── channels[] → Slack, webhook integrations
```

Each extension descriptor has:
- **type**: URI identifying the extension module (e.g., `eddi://ai.labs.llm`)
- **configs**: Key-value parameters (systemMessage, model, provider, tools, etc.)
- **extensions**: Nested sub-extensions (e.g., httpcall tools nested under llm)

**Resource Versioning**: Every config document (rules, dictionaries, LLM configs, packages, agents) is versioned. Agents reference specific resource versions, enabling rollback and A/B testing.

**Deployment Environments**: Agents are deployed to `production` or `test` environments via `AgentFactory.deployAgent()`. Multiple versions can coexist; the latest ready version is served.

### 1.2 Agent Lifecycle: Creation → Runtime → Memory → Termination

```
┌─────────────┐     ┌──────────────┐     ┌───────────────────┐
│  CREATE      │────▶│  DEPLOY      │────▶│  RUNTIME          │
│  Config docs │     │  AgentFactory│     │  LifecycleManager │
│  → Package   │     │  → Agent obj │     │  → Task pipeline  │
│  → Agent     │     │  → Workflow  │     │  → Memory object  │
└─────────────┘     └──────────────┘     └───────┬───────────┘
                                                   │
                                          ┌────────▼────────┐
                                          │  CONVERSATION    │
                                          │  start/say/end   │
                                          │  IConversationMemory
                                          │  → Steps → Data  │
                                          └─────────────────┘
```

**Detailed lifecycle flow:**

1. **Creation**: JSON configs uploaded via REST API → stored as versioned resources
2. **Packaging**: A "package" (workflow) document lists ordered extension descriptors
3. **Agent Definition**: An `AgentConfiguration` references one or more packages
4. **Deployment**: `AgentFactory.deployAgent()` loads config, builds `Agent` with `IExecutableWorkflow` objects containing `ILifecycleTask` instances
5. **Conversation Start**: `Agent.startConversation()` creates `ConversationMemory` (implements `IConversationMemory`), initializes context
6. **Lifecycle Execution**: `LifecycleManager.executeLifecycle()` runs the task pipeline:
   - Each `ILifecycleTask.execute(memory, component)` transforms `IConversationMemory`
   - Tasks are stateless — all state lives in memory
   - Pipeline: Parser → Rules → API/MCP Calls → LLM → Output
   - Interruptible via `STOP_CONVERSATION` action
7. **Memory**: `IConversationMemory` is a stack of `IConversationStep` objects, each holding typed `IData<T>` entries with keys, results, and public/private visibility
8. **Conversation End**: Explicit stop, timeout, or undeployment

**Key runtime classes:**
- `Agent` — holds workflows, creates conversations
- `AgentFactory` — manages agent lifecycle (deploy/undeploy), maintains per-environment agent maps
- `WorkflowFactory` — caches executable workflows by ID+version
- `LifecycleManager` — executes the task pipeline with OTel tracing, Micrometer metrics, and strict write discipline
- `ConversationMemory` — stack-based conversation state with undo/redo

### 1.3 A2A Protocol (Agent-to-Agent)

EDDI implements the **A2A protocol** via JSON-RPC 2.0 endpoints:

**Endpoints:**
- `GET /.well-known/agent.json` — default Agent Card discovery
- `GET /a2a/agents/{agentId}/agent.json` — per-agent Agent Card
- `POST /a2a/agents/{agentId}` — JSON-RPC 2.0 endpoint (tasks/send, tasks/get, tasks/cancel)
- `GET /.well-known/capabilities` — public capability discovery
- `GET /.well-known/capabilities/skills` — skill registry

**A2A Data Model (A2AModels.java):**
```
AgentCard:     name, description, url, provider, version, capabilities, skills, authentication
A2ATask:       id, contextId, status, history[], artifacts[], metadata
A2AMessage:    role, parts[], metadata
Part:          type ("text"|"data"), text, data, metadata
TaskState:     submitted | working | input_required | completed | canceled | failed | unknown
```

**Key features:**
- **Task-conversation mapping**: A2A tasks map 1:1 to EDDI conversations via `A2ATaskHandler`
- **Multi-turn context**: `contextId` enables conversation reuse across multiple `tasks/send` calls
- **Capability Registry**: `CapabilityRegistryService` enables skill-based agent discovery with confidence scoring
- **OIDC Auth**: JSON-RPC endpoints protected by OIDC when auth is enabled
- **Cryptographic Identity**: Agents have `AgentIdentity` (DID + public key), can sign inter-agent messages
- **Soft Routing**: `capabilityMatch` behavior rule condition routes to best-matching agent

### 1.4 MCP Integration Patterns

EDDI has **deep MCP integration** at two levels:

**Pipeline Mode** (deterministic):
- `McpCallsConfiguration` defines MCP server URLs + tool call bindings
- Behavior rules emit actions → trigger specific MCP tool invocations
- No LLM involvement; pure rule-driven execution

**Agent Mode** (LLM-driven):
- LLM agent auto-discovers MCP tools from workflow via `AgentOrchestrator.discoverMcpCallTools()`
- Tools become available for the LLM to call reactively
- `McpCallsConfiguration` supports whitelist/blacklist filtering

**Built-in MCP Tools** (Quarkiverse MCP Server):
- `McpSetupTools` — `setup_agent`, `create_api_agent` (single-call agent creation)
- `McpAdminTools` — CRUD for agents, deployments, resources
- `McpMemoryTools` — persistent user memory operations
- `McpConversationTools` — conversation management
- `McpGdprTools` — GDPR data export/deletion
- `McpDocResources` — documentation access
- `McpGroupTools` — multi-agent orchestration

**MCP Configuration Features:**
- Transport: HTTP (StreamableHTTP) or SSE
- API key support with vault references (`${vault:key-name}`)
- Per-connection timeout, tool filtering
- Secret resolution via `SecretResolver` with envelope encryption

### 1.5 Multi-Tenancy Model

EDDI supports full multi-tenancy:

- **Tenant Quota Service**: `TenantQuotaService` enforces per-tenant limits (agents, conversations, API calls)
- **Quota Stores**: MongoDB or PostgreSQL backends (`MongoTenantQuotaStore`, `PostgresTenantQuotaStore`)
- **Usage Tracking**: `TenantUsageCounters` track real-time usage
- **Quota Enforcement**: `QuotaExceededException` raised when limits exceeded
- **OIDC Integration**: Tenant identity from OIDC tokens
- **Per-tenant Data Isolation**: MongoDB/PostgreSQL collections partitioned by tenant

### 1.6 LLM Provider Architecture

EDDI supports **12+ LLM providers** via a builder pattern:

```
ILanguageModelBuilder (interface)
  ├── AnthropicLanguageModelBuilder
  ├── OpenAILanguageModelBuilder
  ├── AzureOpenAiLanguageModelBuilder
  ├── GeminiLanguageModelBuilder
  ├── VertexGeminiLanguageModelBuilder
  ├── BedrockLanguageModelBuilder
  ├── MistralAiLanguageModelBuilder
  ├── HuggingFaceLanguageModelBuilder
  ├── OllamaLanguageModelBuilder
  ├── JlamaLanguageModelBuilder
  └── OracleGenAiLanguageModelBuilder
```

**Key features:**
- **ChatModelRegistry**: Caches model instances, avoids re-creation per request
- **Unified Configuration**: `LlmConfiguration.Task` supports provider-agnostic setup
- **Tool Calling**: Automatic agent mode when tools are configured (built-in, httpcall, MCP, or A2A)
- **Model Cascade**: Fallback chains for resilience
- **Streaming**: SSE-based streaming via `ConversationEventSink`
- **RAG Integration**: `RagContextProvider` injects retrieved context into prompts
- **Conversation Summarization**: `ConversationSummarizer` for long conversations
- **Token Counting**: `TokenCounterFactory` for cost tracking
- **Identity Masking**: `IdentityMaskingService` for PII protection
- **Counterweight Balancing**: `CounterweightService` for response quality tuning

### 1.7 RAG Pipeline

- `RagIngestionService`: Async document ingestion with virtual threads
- Uses LangChain4j `EmbeddingStoreIngestor` for chunk → embed → store
- Configurable chunk size and overlap
- Supports multiple embedding models and vector stores via `EmbeddingModelFactory` / `EmbeddingStoreFactory`
- Ingestion status tracking with Caffeine cache

### 1.8 Persistent Memory

- **User Memory**: Per-user persistent memory with categories (preference, fact, context)
- **Memory Tools**: LLM can `remember`, `recall`, `forget` via built-in tools
- **Dream Consolidation**: Background LLM-powered memory consolidation with contradiction detection, summarization, and stale entry pruning
- **Write Guardrails**: Configurable limits on key/value length, writes per turn, allowed categories
- **Session Management**: Auto-snapshot before state-changing tool executions, conversation forking (planned)

---

## 2. SuperInstance Adaptation Plan

### 2.1 Constraint-Aware Agent Orchestration

**Concept**: Each EDDI agent becomes a tradition-specific constraint solver. The existing lifecycle pipeline (Parser → Rules → LLM → Output) maps naturally to constraint analysis.

**Implementation:**

```
┌─────────────────────────────────────────────────────────┐
│  SuperInstance Agent (EDDI-based)                       │
│                                                         │
│  AgentConfiguration:                                    │
│    packages: [constraint-analysis-workflow]             │
│    capabilities:                                        │
│      - skill: "constraint-analysis"                     │
│        attributes: { tradition: "jiu-jitsu" }           │
│        confidence: "high"                               │
│    a2aEnabled: true                                     │
│                                                         │
│  Workflow Extensions:                                   │
│    1. Parser → Extract constraint keywords              │
│    2. Rules → Map to tradition-specific dial positions  │
│    3. MCP Calls → constraint-toolkit operations         │
│    4. LLM → Constraint analysis + recommendations      │
│    5. Output → Structured constraint report             │
│                                                         │
│  Memory:                                                │
│    - Tradition knowledge (persistent user memory)       │
│    - Constraint history per user                        │
│    - Dream consolidation of patterns                    │
└─────────────────────────────────────────────────────────┘
```

**Constraint Metadata in A2A Protocol:**

Extend A2A `Task.metadata` and `Part.data` to carry constraint information:

```json
{
  "id": "task-123",
  "contextId": "constraint-session-456",
  "metadata": {
    "tradition": "catch-wrestling",
    "constraintType": "dial_position",
    "urgency": "high",
    "trinityScore": {
      "ethos": 0.85,
      "pathos": 0.72,
      "logos": 0.91
    }
  }
}
```

### 2.2 CUDAclaw Integration

**Architecture:**

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  EDDI Agent  │────▶│  MCP Bridge  │────▶│   CUDAclaw   │
│  (Orchestr.) │     │  (Tool Prov) │     │  (GPU Exec)  │
└──────────────┘     └──────────────┘     └──────────────┘
      │                                           │
      │  ┌──────────────┐     ┌──────────────┐   │
      └─▶│  RAG Index   │     │  Fleet Agent │◀──┘
         │  (Knowledge) │     │  (Monitor)   │
         └──────────────┘     └──────────────┘
```

**EDDI Agents dispatch GPU work to CUDAclaw via MCP tools:**

1. **CUDAclaw MCP Server**: Expose GPU operations as MCP tools
   - `gpu_constraint_solve` — parallel constraint solving on GPU
   - `gpu_tradition_analyze` — batch tradition analysis
   - `gpu_vector_search` — high-dimensional similarity search
   - `gpu_tensor_compute` — general tensor operations

2. **EDDI MCP Client Config**: Each agent connects to the CUDAclaw MCP server
   ```json
   {
     "mcpServerUrl": "http://cudaclaw:7070/mcp",
     "transport": "http",
     "toolsWhitelist": ["gpu_constraint_solve", "gpu_vector_search"],
     "timeoutMs": 60000
   }
   ```

3. **Parallel Agent Pattern**: Deploy N EDDI agents (one per tradition), each independently dispatching GPU work:
   ```
   Agent[jiu-jitsu] → MCP → CUDAclaw[gpu_solve(jiu_jitsu_constraints)]
   Agent[catch-wrestling] → MCP → CUDAclaw[gpu_solve(catch_wrestling_constraints)]
   Agent[sambo] → MCP → CUDAclaw[gpu_solve(sambo_constraints)]
   ```

4. **RAG Integration**: Index the full SuperInstance knowledge base (II-Commons, tradition docs, constraint catalogs) via EDDI's `RagIngestionService`

### 2.3 Fleet Coordination

**Mapping EDDI agents to fleet roles:**

| Fleet Agent | EDDI Agent Type | Config Role | A2A Skills |
|------------|-----------------|-------------|------------|
| CCC | Compliance Monitor | Rules-heavy agent with compliance checks | `compliance-check`, `constraint-validation` |
| Oracle1 | Prediction/Analysis | LLM agent with RAG + analysis tools | `prediction`, `trend-analysis` |
| FM (Forgemaster) | Build/Compile | Agent with MCP tools for code gen | `build`, `compile`, `deploy` |
| TurboVec | Vector/Search | RAG-optimized search agent | `vector-search`, `similarity` |

**Trinity Score as Agent Health Metric:**

```json
{
  "capabilities": [
    {
      "skill": "constraint-analysis",
      "attributes": {
        "tradition": "jiu-jitsu",
        "trinity.ethos": "0.85",
        "trinity.pathos": "0.72",
        "trinity.logos": "0.91"
      },
      "confidence": "high"
    }
  ]
}
```

Trinity scores inform A2A soft routing: `CapabilityRegistryService` selects agents with highest combined `ethos×pathos×logos` for tasks requiring that balance.

**Conservation Constraints as Inter-Agent Protocols:**

Use EDDI's existing behavior rules system to encode conservation constraints:
- Rules evaluate conservation invariants across agent outputs
- `STOP_CONVERSATION` action prevents constraint-violating outputs
- A2A protocol carries constraint metadata between agents
- Strict write discipline ensures failed constraint checks don't pollute memory

### 2.4 PLATO Room Integration

**Concept**: Each EDDI agent gets a PLATO room. Room state maps to agent working memory.

```
PLATO Room ←→ EDDI Agent
  Room State     =  ConversationMemory (IConversationMemory)
  Tiles          =  ConversationOutput entries
  Room Musicians =  A2A agent communication channels
  Room History   =  Conversation steps (undo/redo stack)
```

**Implementation:**

1. **Room State Adapter**: Implement `IConversationMemory` backed by PLATO room state
   - `getCurrentStep()` → current room state
   - `storeData()` → update room tile
   - `getConversationOutputs()` → room tiles as output entries

2. **Room-Triggered Lifecycle**: PLATO room events trigger EDDI lifecycle execution
   - New room message → `startConversation()` or `continueConversation()`
   - Room state change → lifecycle task execution with updated context
   - Room musician messages → A2A `tasks/send` to other agents

3. **Agent-as-Room-Host**: Each deployed EDDI agent hosts a PLATO room
   ```
   Deploy Agent → Create PLATO Room → Bind Room State to Agent Memory
   ```

---

## 3. Concrete Integration Steps

### Phase 1: EDDI as Fleet Orchestration Layer (Weeks 1-3)

**Goal**: Deploy EDDI as the central orchestration layer for existing fleet agents.

**Steps:**
1. Deploy EDDI on K8s alongside existing SuperInstance services
2. Configure MongoDB/PostgreSQL for agent config persistence
3. Create agent configurations for each fleet member (CCC, Oracle1, FM, TurboVec)
4. Enable A2A protocol for inter-agent communication
5. Configure OIDC for secure agent-to-agent auth
6. Set up capability registry with fleet-specific skills
7. Create behavior rules for fleet coordination policies

**Deliverable**: EDDI orchestrating fleet agents via A2A, each agent with its own lifecycle pipeline.

### Phase 2: Constraint-Aware Agent Configs (Weeks 4-6)

**Goal**: Build tradition-specific constraint analysis agents.

**Steps:**
1. Define constraint-specific parser dictionaries (constraint keywords, dial position terms)
2. Create behavior rules mapping constraint queries to tradition-specific actions
3. Build constraint-toolkit MCP server exposing constraint operations
4. Create `McpCallsConfiguration` connecting agents to constraint-toolkit
5. Configure LLM tasks with constraint-aware system prompts
6. Enable persistent user memory for tradition knowledge accumulation
7. Configure Dream consolidation for pattern detection

**Deliverable**: Constraint analysis agents that understand tradition-specific dial positions and constraint systems.

### Phase 3: CUDAclaw GPU Dispatch (Weeks 7-10)

**Goal**: Connect EDDI agents to CUDAclaw for GPU-accelerated parallel processing.

**Steps:**
1. Build CUDAclaw MCP Server exposing GPU operations as MCP tools
2. Create MCP tool definitions for: `gpu_constraint_solve`, `gpu_tradition_analyze`, `gpu_vector_search`, `gpu_tensor_compute`
3. Configure EDDI agents with CUDAclaw MCP connections
4. Implement parallel agent deployment (N agents, one per tradition)
5. Set up load balancing and GPU resource scheduling
6. Configure timeout and retry policies for GPU operations
7. Implement result aggregation via A2A protocol

**Deliverable**: Massively parallel constraint analysis with GPU acceleration.

### Phase 4: PLATO Room Integration (Weeks 11-14)

**Goal**: Connect EDDI agent memory to PLATO room state.

**Steps:**
1. Design `IConversationMemory` adapter backed by PLATO room state
2. Implement room state → conversation step mapping
3. Build tile rendering from `ConversationOutput` entries
4. Implement room event → lifecycle trigger bridge
5. Connect room musicians to A2A communication channels
6. Build room-based agent dashboard (live view of agent working memory)
7. Implement room history as conversation undo/redo

**Deliverable**: PLATO rooms as live visualizations of agent working memory with interactive control.

### Phase 5: Full SuperInstance Agent Network (Weeks 15-20)

**Goal**: Complete integration of all components into a unified agent network.

**Steps:**
1. Index full II-Commons knowledge base via RAG ingestion
2. Build cross-tradition analysis agents (multi-agent collaboration)
3. Implement conservation constraint validation across the fleet
4. Deploy Trinity score monitoring and alerting
5. Build evolution agents using flux-genome for agent self-improvement
6. Implement full audit trail (EDDI's existing audit system)
7. Configure GDPR compliance tools for user data
8. Performance tuning: virtual threads, connection pooling, GPU scheduling
9. Load testing at scale (1000+ concurrent agents)
10. Documentation and runbooks

**Deliverable**: Fully operational SuperInstance agent network with EDDI orchestration.

---

## 4. Agent Config Examples

### 4.1 Constraint Analysis Agent (uses constraint-toolkit)

```json
{
  "workflows": [
    "eddi://ai.labs.packages/constraint-analysis-v2?version=5"
  ],
  "description": "Jiu-Jitsu constraint analysis agent — evaluates dial positions, constraint interactions, and tradition-specific optimization",
  "a2aEnabled": true,
  "a2aSkills": ["constraint-analysis", "dial-optimization", "tradition-jiu-jitsu"],
  "capabilities": [
    {
      "skill": "constraint-analysis",
      "attributes": {
        "tradition": "jiu-jitsu",
        "maxDialPositions": "24",
        "solverType": "gpu-accelerated"
      },
      "confidence": "high"
    },
    {
      "skill": "dial-optimization",
      "attributes": {
        "method": "gradient-descent",
        "gpuBatchSize": "128"
      },
      "confidence": "medium"
    }
  ],
  "enableMemoryTools": true,
  "userMemoryConfig": {
    "defaultVisibility": "self",
    "maxRecallEntries": 100,
    "autoRecallCategories": ["preference", "fact", "constraint_history"],
    "guardrails": {
      "maxWritesPerTurn": 20,
      "allowedCategories": ["preference", "fact", "context", "constraint_result"]
    },
    "dream": {
      "enabled": true,
      "schedule": "0 3 * * *",
      "detectContradictions": true,
      "summarizeInteractions": true,
      "llmProvider": "anthropic",
      "llmModel": "claude-sonnet-4-6"
    }
  },
  "memoryPolicy": {
    "strictWriteDiscipline": {
      "enabled": true,
      "onFailure": "digest"
    }
  },
  "security": {
    "signInterAgentMessages": true,
    "requirePeerVerification": true
  }
}
```

**Constraint-Toolkit MCP Connection (workflow extension):**

```json
{
  "mcpServerUrl": "http://constraint-toolkit:7070/mcp",
  "name": "constraint-toolkit",
  "transport": "http",
  "timeoutMs": 45000,
  "toolsWhitelist": [
    "evaluate_constraint",
    "solve_dial_position",
    "batch_constraint_check",
    "get_tradition_model"
  ]
}
```

**CUDAclaw GPU Connection (workflow extension):**

```json
{
  "mcpServerUrl": "http://cudaclaw:7070/mcp",
  "name": "cudaclaw-gpu",
  "transport": "http",
  "timeoutMs": 120000,
  "toolsWhitelist": [
    "gpu_constraint_solve",
    "gpu_tradition_analyze",
    "gpu_vector_search"
  ]
}
```

### 4.2 Music Generation Agent (uses constraint-audio)

```json
{
  "workflows": [
    "eddi://ai.labs.packages/music-generation-v1?version=3"
  ],
  "description": "Generates tradition-inspired music compositions using constraint-audio system with GPU-accelerated synthesis",
  "a2aEnabled": true,
  "a2aSkills": ["music-generation", "audio-synthesis", "tradition-music"],
  "capabilities": [
    {
      "skill": "music-generation",
      "attributes": {
        "maxDuration": "300",
        "sampleRate": "48000",
        "traditions": "jiu-jitsu,catch-wrestling,sambo"
      },
      "confidence": "high"
    }
  ],
  "enableMemoryTools": true,
  "userMemoryConfig": {
    "defaultVisibility": "self",
    "autoRecallCategories": ["preference", "musical_style", "composition_history"],
    "guardrails": {
      "allowedCategories": ["preference", "fact", "musical_style", "composition"]
    }
  }
}
```

### 4.3 Research Agent (uses II-Commons)

```json
{
  "workflows": [
    "eddi://ai.labs.packages/research-agent-v1?version=7"
  ],
  "description": "Deep research agent with RAG access to II-Commons knowledge base — tradition history, technique catalogs, biomechanical data",
  "a2aEnabled": true,
  "a2aSkills": ["research", "knowledge-retrieval", "ii-commons"],
  "capabilities": [
    {
      "skill": "research",
      "attributes": {
        "knowledgeBase": "ii-commons",
        "searchDepth": "deep",
        "citationSupport": "true"
      },
      "confidence": "high"
    },
    {
      "skill": "knowledge-retrieval",
      "attributes": {
        "ragEnabled": "true",
        "embeddingModel": "text-embedding-3-large",
        "chunkSize": "512"
      },
      "confidence": "high"
    }
  ],
  "enableMemoryTools": true,
  "userMemoryConfig": {
    "defaultVisibility": "shared",
    "maxRecallEntries": 200,
    "autoRecallCategories": ["preference", "research_topic", "citation"],
    "dream": {
      "enabled": true,
      "schedule": "0 4 * * *",
      "summarizeInteractions": true,
      "summarizeGroupBy": "category"
    }
  }
}
```

### 4.4 Fleet Monitoring Agent (uses ccc-os)

```json
{
  "workflows": [
    "eddi://ai.labs.packages/fleet-monitor-v1?version=2"
  ],
  "description": "Monitors fleet health, trinity scores, conservation constraints, and GPU utilization across the SuperInstance network",
  "a2aEnabled": true,
  "a2aSkills": ["fleet-monitoring", "compliance", "health-check"],
  "capabilities": [
    {
      "skill": "fleet-monitoring",
      "attributes": {
        "managedAgents": "ccc,oracle1,fm,turbovec",
        "checkInterval": "30s",
        "alertThreshold": "0.7"
      },
      "confidence": "high"
    },
    {
      "skill": "compliance",
      "attributes": {
        "framework": "eu-ai-act",
        "gdprEnabled": "true",
        "auditTrail": "true"
      },
      "confidence": "high"
    }
  ],
  "enableMemoryTools": false,
  "memoryPolicy": {
    "strictWriteDiscipline": {
      "enabled": true,
      "onFailure": "digest"
    }
  },
  "sessionManagement": {
    "autoSnapshot": {
      "enabled": true,
      "triggerOn": ["before_tool", "before_action"]
    },
    "maxCheckpointsPerConversation": 50
  }
}
```

### 4.5 Evolution Agent (uses flux-genome)

```json
{
  "workflows": [
    "eddi://ai.labs.packages/evolution-agent-v1?version=1"
  ],
  "description": "Evolves agent configurations using flux-genome genetic algorithms — optimizes constraint parameters, prompt strategies, and tool selections",
  "a2aEnabled": true,
  "a2aSkills": ["evolution", "genetic-optimization", "agent-improvement"],
  "capabilities": [
    {
      "skill": "evolution",
      "attributes": {
        "populationSize": "50",
        "mutationRate": "0.05",
        "crossoverRate": "0.7",
        "fitnessFunction": "trinity_score"
      },
      "confidence": "medium"
    },
    {
      "skill": "genetic-optimization",
      "attributes": {
        "targetTraits": "constraint_accuracy,response_time,trinity_balance",
        "gpuAccelerated": "true"
      },
      "confidence": "medium"
    }
  ],
  "enableMemoryTools": true,
  "userMemoryConfig": {
    "defaultVisibility": "shared",
    "maxRecallEntries": 500,
    "autoRecallCategories": ["evolution_result", "fitness_score", "mutation_history"],
    "dream": {
      "enabled": true,
      "schedule": "0 2 * * *",
      "detectContradictions": true,
      "summarizeInteractions": true,
      "preserveAgentProvenance": true
    }
  }
}
```

### 4.6 LLM Task Config for Constraint Agent (within workflow)

```json
{
  "tasks": [
    {
      "id": "constraint-analysis",
      "actions": ["analyze_constraints", "evaluate_dial"],
      "type": "anthropic",
      "description": "Analyze constraint systems for tradition-specific optimization",
      "parameters": {
        "systemMessage": "You are a constraint analysis expert specializing in {{tradition}}. Analyze the given dial positions and constraint interactions. Consider conservation laws, biomechanical limits, and tradition-specific principles. Output structured JSON with constraint evaluations.",
        "prompt": "Analyze constraints: {{constraint_input}}\nTradition: {{tradition}}\nDial positions: {{dial_positions}}"
      },
      "tools": [
        "eddi://ai.labs.mcpcalls/constraint-toolkit?version=1",
        "eddi://ai.labs.mcpcalls/cudaclaw-gpu?version=1"
      ],
      "a2aAgents": [
        {
          "agentId": "research-agent",
          "url": "http://eddi:8080/a2a/agents/research-agent",
          "skills": ["knowledge-retrieval"]
        }
      ],
      "enableBuiltInTools": true,
      "enableHttpCallTools": true,
      "maxBudgetPerConversation": 2.0
    }
  ]
}
```

---

## 5. Appendix: Key EDDI Source Maps

### Core Engine
| Component | Path | Lines |
|-----------|------|-------|
| LifecycleManager | `engine/lifecycle/internal/LifecycleManager.java` | ~350 |
| Agent | `engine/runtime/internal/Agent.java` | ~100 |
| AgentFactory | `engine/runtime/internal/AgentFactory.java` | ~250 |
| WorkflowFactory | `engine/runtime/internal/WorkflowFactory.java` | ~60 |
| ConversationMemory | `engine/memory/ConversationMemory.java` | — |
| IConversationMemory | `engine/memory/IConversationMemory.java` | ~200 |

### A2A Protocol
| Component | Path |
|-----------|------|
| A2AModels | `engine/a2a/A2AModels.java` |
| A2ATaskHandler | `engine/a2a/A2ATaskHandler.java` |
| RestA2AEndpoint | `engine/a2a/RestA2AEndpoint.java` |
| AgentCardService | `engine/a2a/AgentCardService.java` |

### MCP Integration
| Component | Path |
|-----------|------|
| McpSetupTools | `engine/mcp/McpSetupTools.java` |
| McpAdminTools | `engine/mcp/McpAdminTools.java` |
| McpMemoryTools | `engine/mcp/McpMemoryTools.java` |
| McpConversationTools | `engine/mcp/McpConversationTools.java` |
| McpCallsConfiguration | `configs/mcpcalls/model/McpCallsConfiguration.java` |

### LLM Module
| Component | Path |
|-----------|------|
| LlmTask | `modules/llm/impl/LlmTask.java` |
| LlmConfiguration | `modules/llm/model/LlmConfiguration.java` |
| ChatModelRegistry | `modules/llm/impl/ChatModelRegistry.java` |
| AgentOrchestrator | `modules/llm/impl/AgentOrchestrator.java` |
| Builders (12+) | `modules/llm/impl/builder/*.java` |

### Config & Agent Definition
| Component | Path |
|-----------|------|
| AgentConfiguration | `configs/agents/model/AgentConfiguration.java` |
| ExtensionDescriptor | `configs/workflows/model/ExtensionDescriptor.java` |

### RAG
| Component | Path |
|-----------|------|
| RagIngestionService | `modules/rag/RagIngestionService.java` |

### Multi-Tenancy
| Component | Path |
|-----------|------|
| TenantQuotaService | `engine/tenancy/TenantQuotaService.java` |
| TenantQuota | `engine/tenancy/model/TenantQuota.java` |

---

## Architecture Decision Records

### ADR-1: EDDI as Orchestration Layer (not replacement)
**Decision**: Use EDDI as the orchestration middleware on top of existing SuperInstance services, not as a replacement.
**Rationale**: EDDI's strength is config-driven agent definition, lifecycle management, and multi-agent coordination. SuperInstance services (CUDAclaw, constraint-toolkit, PLATO rooms) provide domain-specific computation. The two complement each other.

### ADR-2: MCP as Primary Integration Protocol
**Decision**: Use MCP as the primary protocol between EDDI agents and SuperInstance services.
**Rationale**: EDDI already has deep MCP support (both pipeline and agent modes). MCP is becoming the standard for tool-calling. Building MCP servers for CUDAclaw, constraint-toolkit, etc. is the lowest-friction integration path.

### ADR-3: A2A for Inter-Agent Communication
**Decision**: Use EDDI's native A2A protocol for inter-agent communication within the fleet.
**Rationale**: A2A is already implemented with task tracking, conversation mapping, capability discovery, and cryptographic identity. No need to build a custom protocol.

### ADR-4: Virtual Threads for Parallel Agent Execution
**Decision**: Leverage EDDI's virtual thread support (Java 21+) for massive parallel agent execution.
**Rationale**: EDDI already uses virtual threads for RAG ingestion and async operations. With CUDAclaw handling GPU work, EDDI agents can be lightweight virtual-thread orchestrators dispatching work to GPU.

### ADR-5: Trinity Scores as Capability Confidence
**Decision**: Map Trinity scores (Ethos×Pathos×Logos) to EDDI's existing capability confidence system.
**Rationale**: EDDI's `CapabilityRegistryService` already supports confidence-based agent selection. Trinity scores provide a natural confidence metric that enables soft routing to the best agent for a given task.

---

*Generated: 2026-05-25 | EDDI commit: latest | SuperInstance ecosystem integration plan v1.0*
