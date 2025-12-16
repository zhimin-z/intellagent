# IntellAgent Supported Strategies

This document identifies which strategies from the **Unified Evaluation Workflow** are supported by IntellAgent. The Unified Evaluation Workflow is an abstraction of evaluation patterns across 50+ frameworks spanning diverse domains—from text generation to robot manipulation, molecular property prediction, and hardware stress testing.

## Classification Framework

Strategies are classified into three categories:

- **Natively Supported**: Available immediately after installing IntellAgent (`pip install -r requirements.txt`), requires only import statements and minimal configuration (≤2 lines), no external dependencies beyond the harness itself, no custom implementation or glue code required.

- **Supported via Third-Party Integration**: Requires installing ≥1 external package(s), requires glue code, has documented integration pattern or official example, functionality enabled through third-party tools rather than the harness alone.

- **Not Supported**: Functionality is unavailable even with third-party integrations.

## Summary

IntellAgent is a specialized evaluation harness designed for **conversational AI systems** (chatbots and agents). It focuses on stress-testing conversational agents through simulated edge-case scenarios and provides fine-grained policy-based evaluation.

---

## Phase 0: Provisioning (The Runtime)

### Step A: Harness Installation

**✅ Strategy 1: Git Clone**
- **Support Level**: Natively Supported
- **Evidence**: Installation instructions explicitly include `git clone git@github.com:plurai-ai/intellagent.git` (README.md, docs/installation.md)

**✅ Strategy 2: PyPI Packages**
- **Support Level**: Natively Supported
- **Evidence**: Full installation via `pip install -r requirements.txt` with dependencies including LangChain, LangGraph, pandas, streamlit, plotly, networkx, and various LLM provider SDKs (requirements.txt)

**❌ Strategy 3: Node Package**
- **Support Level**: Not Supported
- **Evidence**: IntellAgent is Python-only; no Node.js/npm installation available

**❌ Strategy 4: Binary Packages**
- **Support Level**: Not Supported
- **Evidence**: No standalone binary distribution provided

**❌ Strategy 5: Container Images**
- **Support Level**: Not Supported
- **Evidence**: No Docker/OCI container images available in official distribution

### Step B: Service Authentication

**✅ Strategy 1: API Provider Authentication**
- **Support Level**: Natively Supported
- **Evidence**: Configuration via `config/llm_env.yml` for API keys (OpenAI, Azure OpenAI, Vertex AI, Anthropic). Requires minimal configuration: setting API keys in YAML file (README.md, docs/installation.md, config/llm_env.yml)

**🔌 Strategy 2: Repository Authentication**
- **Support Level**: Supported via Third-Party Integration
- **Evidence**: Uses LangChain's langchain_community which supports Hugging Face Hub authentication. Requires external authentication step via HF CLI (`huggingface-cli login`) beyond IntellAgent installation (requirements.txt includes langchain_community)

**❌ Strategy 3: Evaluation Platform Authentication**
- **Support Level**: Not Supported
- **Evidence**: No built-in leaderboard or evaluation platform authentication

---

## Phase I: Specification (The Contract)

### Step A: SUT Preparation

**✅ Strategy 1: Model-as-a-Service (Remote Inference)**
- **Support Level**: Natively Supported
- **Evidence**: Native support for remote API calls to OpenAI, Azure OpenAI, Google Vertex AI, and Anthropic via LangChain integrations. Configuration requires setting API keys in `config/llm_env.yml` and model name in config (≤2 lines). Available immediately after installation (config/config_default.yml, simulator/dialog/dialog_manager.py, simulator/utils/llm_utils.py)

**🔌 Strategy 2: Model-in-Process (Local Inference)**
- **Support Level**: Supported via Third-Party Integration
- **Evidence**: Supports local model loading via HuggingFacePipeline integration (see HuggingFacePipeline.from_model_id implementation in get_llm function in simulator/utils/llm_utils.py). Requires setting `type: 'huggingfacepipeline'` in config and model weights available locally or downloaded via transformers library. Primarily designed for API-based models

**❌ Strategy 3: Algorithm Implementation (In-Memory Structures)**
- **Support Level**: Not Supported
- **Evidence**: No support for vector indexes, BM25, or signal processing pipelines; focuses exclusively on conversational models

**✅ Strategy 4: Policy/Agent Instantiation (Stateful Controllers)**
- **Support Level**: Natively Supported
- **Evidence**: Native support for LangGraph agents with tool-calling capabilities and stateful dialog management. Available immediately after installation with minimal configuration (simulator/agents_graphs/dialog_graph.py, simulator/dialog/dialog_manager.py, requirements.txt includes langgraph)

### Step B: Benchmark Preparation (Inputs)

**❌ Strategy 1: Benchmark Dataset Preparation (Offline)**
- **Support Level**: Not Supported
- **Evidence**: IntellAgent generates its own synthetic test scenarios rather than loading pre-existing benchmark datasets

**✅ Strategy 2: Synthetic Data Generation (Generative)**
- **Support Level**: Natively Supported
- **Evidence**: Core capability - generates synthetic edge-case scenarios through policy graph sampling and LLM-based event generation. Available immediately after installation (simulator/dataset/descriptor_generator.py, simulator/dataset/events_generator.py, docs/architecture.md)

**✅ Strategy 3: Simulation Environment Setup (Simulated)**
- **Support Level**: Natively Supported
- **Evidence**: Creates simulated database states and scenario contexts as part of event generation. Core functionality available immediately (simulator/dataset/events_generator.py, simulator/env.py)

**❌ Strategy 4: Production Traffic Sampling (Online)**
- **Support Level**: Not Supported
- **Evidence**: No built-in production traffic sampling or stream buffering capabilities

### Step C: Benchmark Preparation (References)

**✅ Strategy 1: Ground Truth Preparation**
- **Support Level**: Natively Supported
- **Evidence**: Pre-computes expected behaviors and policy requirements for each generated scenario. Core functionality (simulator/dataset/descriptor_generator.py, simulator/dataset/definitions.py)

**✅ Strategy 2: Judge Preparation**
- **Support Level**: Natively Supported
- **Evidence**: Configurable LLM-based critique/judge models for evaluating conversation adherence to policies. Requires only setting judge LLM in config (config/config_default.yml critique_config, simulator/dialog/dialog_manager.py set_critique method)

---

## Phase II: Execution (The Run)

### Step A: SUT Invocation

**✅ Strategy 1: Batch Inference**
- **Support Level**: Natively Supported
- **Evidence**: Executes multiple scenarios through dialog simulation with batch processing support. Core functionality available immediately (simulator/simulator_executor.py run_simulation method, config/config_default.yml mini_batch_size parameter)

**❌ Strategy 2: Arena Battle**
- **Support Level**: Not Supported
- **Evidence**: No built-in support for concurrent multi-SUT comparison; evaluates one agent at a time

**✅ Strategy 3: Interactive Loop**
- **Support Level**: Natively Supported
- **Evidence**: Core capability - implements stateful multi-turn dialog simulation between user agent and chatbot with tool usage. Available immediately after installation (simulator/agents_graphs/dialog_graph.py, simulator/dialog/dialog_manager.py)

**❌ Strategy 4: Production Streaming**
- **Support Level**: Not Supported
- **Evidence**: No production traffic streaming or real-time monitoring capabilities

---

## Phase III: Assessment (The Score)

### Step A: Individual Scoring

**✅ Strategy 1: Deterministic Measurement**
- **Support Level**: Natively Supported
- **Evidence**: Binary success/failure scoring based on policy violation detection and conversation termination conditions. Core evaluation functionality (simulator/simulator_executor.py analyze_results method)

**❌ Strategy 2: Embedding Measurement**
- **Support Level**: Not Supported
- **Evidence**: No semantic similarity or embedding-based metrics implemented natively

**✅ Strategy 3: Subjective Measurement**
- **Support Level**: Natively Supported
- **Evidence**: LLM-based critique agent evaluates conversations against policies and provides subjective judgments. Core functionality requiring only LLM configuration (simulator/dialog/dialog_manager.py, simulator/utils/analysis.py)

**❌ Strategy 4: Performance Measurement**
- **Support Level**: Not Supported
- **Evidence**: Tracks token costs but no built-in latency, throughput, memory, or energy consumption metrics

### Step B: Aggregate Scoring

**✅ Strategy 1: Distributional Statistics**
- **Support Level**: Natively Supported
- **Evidence**: Computes success rates, failure rates, and performance breakdowns by complexity level and policy category. Core analysis functionality (simulator/simulator_executor.py analyze_results, simulator/visualization/)

**❌ Strategy 2: Uncertainty Quantification**
- **Support Level**: Not Supported
- **Evidence**: No bootstrap resampling or confidence interval estimation implemented

---

## Phase IV: Reporting (The Output)

### Step A: Insight Presentation

**✅ Strategy 1: Execution Tracing**
- **Support Level**: Natively Supported
- **Evidence**: Captures detailed dialog logs, user thoughts, chatbot messages, and tool calls in SQLite database. Core functionality available immediately (simulator/utils/sqlite_handler.py, simulator/dialog/dialog_manager.py)

**✅ Strategy 2: Subgroup Analysis**
- **Support Level**: Natively Supported
- **Evidence**: Performance breakdown by challenge level, policy category, and flow. Core analysis and visualization functionality (simulator/visualization/pages/1_📈_Experiments_Report.py)

**❌ Strategy 3: Regression Alerting**
- **Support Level**: Not Supported
- **Evidence**: No automated regression detection or alerting against historical baselines

**✅ Strategy 4: Chart Generation**
- **Support Level**: Natively Supported
- **Evidence**: Generates performance visualizations including success rate charts and challenge level distributions using Plotly. Core visualization capability (simulator/visualization/, requirements.txt includes plotly)

**✅ Strategy 5: Dashboard Creation**
- **Support Level**: Natively Supported
- **Evidence**: Interactive Streamlit dashboard for visualization with experiment comparisons and policy analysis. Available via `streamlit run simulator/visualization/Simulator_Visualizer.py` (simulator/visualization/Simulator_Visualizer.py, simulator/visualization/pages/)

**❌ Strategy 6: Leaderboard Publication**
- **Support Level**: Not Supported
- **Evidence**: No public or private leaderboard submission capabilities

---

## Supported Strategies Summary

**Total: 19 Natively Supported + 1 via Third-Party Integration = 20 Supported out of 34 strategies**

### Provisioning (3 Native + 1 Integration = 4/8)
- Git Clone ✅ (Native)
- PyPI Packages ✅ (Native)
- API Provider Authentication ✅ (Native)
- Repository Authentication 🔌 (Third-Party Integration)

### Specification (6 Native + 1 Integration = 7/10)
- Model-as-a-Service ✅ (Native)
- Model-in-Process 🔌 (Third-Party Integration)
- Policy/Agent Instantiation ✅ (Native)
- Synthetic Data Generation ✅ (Native)
- Simulation Environment Setup ✅ (Native)
- Ground Truth Preparation ✅ (Native)
- Judge Preparation ✅ (Native)

### Execution (2 Native = 2/4)
- Batch Inference ✅ (Native)
- Interactive Loop ✅ (Native)

### Assessment (3 Native = 3/6)
- Deterministic Measurement ✅ (Native)
- Subjective Measurement ✅ (Native)
- Distributional Statistics ✅ (Native)

### Reporting (4 Native = 4/6)
- Execution Tracing ✅ (Native)
- Subgroup Analysis ✅ (Native)
- Chart Generation ✅ (Native)
- Dashboard Creation ✅ (Native)

### Legend
- ✅ = Natively Supported (out-of-the-box functionality)
- 🔌 = Supported via Third-Party Integration (requires external packages/setup)
- ❌ = Not Supported

---

## IntellAgent's Unique Focus

IntellAgent specializes in:
1. **Conversational AI evaluation** through multi-turn dialog simulation
2. **Policy-based testing** with automated edge-case scenario generation
3. **LangGraph agent support** with native tool-calling integration
4. **Fine-grained diagnostics** with policy violation tracking and complexity-based analysis

It is **not** designed for:
- Static benchmark dataset evaluation
- Multi-model arena comparisons
- Production monitoring or drift detection
- Non-conversational modalities (vision, audio, etc.)
- Embedding-based or performance-based metrics
