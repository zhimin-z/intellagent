# IntellAgent Supported Strategies

This document identifies which strategies from the **Unified Evaluation Workflow** are natively supported by IntellAgent. A strategy is considered "supported" only if IntellAgent provides it in its full installation without requiring custom modules or external library integrations.

## Summary

IntellAgent is a specialized evaluation harness designed for **conversational AI systems** (chatbots and agents). It focuses on stress-testing conversational agents through simulated edge-case scenarios and provides fine-grained policy-based evaluation.

---

## Phase 0: Provisioning (The Runtime)

### Step A: Harness Installation

**✅ Strategy 1: Git Clone**
- **Supported**: YES
- **Evidence**: Installation instructions explicitly include `git clone git@github.com:plurai-ai/intellagent.git` (README.md, docs/installation.md)

**✅ Strategy 2: PyPI Packages**
- **Supported**: YES
- **Evidence**: Full installation via `pip install -r requirements.txt` with dependencies including langchain, langgraph, pandas, streamlit, plotly, networkx, and various LLM provider SDKs (requirements.txt)

**❌ Strategy 3: Node Package**
- **Supported**: NO
- **Evidence**: IntellAgent is Python-only; no Node.js/npm installation available

**❌ Strategy 4: Binary Packages**
- **Supported**: NO
- **Evidence**: No standalone binary distribution provided

**❌ Strategy 5: Container Images**
- **Supported**: NO
- **Evidence**: No Docker/OCI container images available in official distribution

### Step B: Service Authentication

**✅ Strategy 1: API Provider Authentication**
- **Supported**: YES
- **Evidence**: Configuration via `config/llm_env.yml` for API keys including OpenAI, Azure OpenAI, Vertex AI (Google Cloud), and Anthropic (README.md, docs/installation.md, config/llm_env.yml)

**✅ Strategy 2: Repository Authentication**
- **Supported**: YES (implicit)
- **Evidence**: Uses langchain and langchain_community which support Hugging Face Hub authentication for model/dataset access (requirements.txt)

**❌ Strategy 3: Evaluation Platform Authentication**
- **Supported**: NO
- **Evidence**: No built-in leaderboard or evaluation platform authentication

---

## Phase I: Specification (The Contract)

### Step A: SUT Preparation

**✅ Strategy 1: Model-as-a-Service (Remote Inference)**
- **Supported**: YES
- **Evidence**: Native support for remote API calls to OpenAI, Azure OpenAI, Google Vertex AI, and Anthropic via langchain integrations (config/config_default.yml, simulator/dialog/dialog_manager.py, simulator/utils/llm_utils.py)

**✅ Strategy 2: Model-in-Process (Local Inference)**
- **Supported**: YES (limited)
- **Evidence**: Can load and use local LLM models through langchain_community integrations, though primarily designed for API-based models (requirements.txt includes langchain_community)

**❌ Strategy 3: Algorithm Implementation (In-Memory Structures)**
- **Supported**: NO
- **Evidence**: No support for vector indexes, BM25, or signal processing pipelines; focuses exclusively on conversational models

**✅ Strategy 4: Policy/Agent Instantiation (Stateful Controllers)**
- **Supported**: YES
- **Evidence**: Native support for LangGraph agents with tool-calling capabilities and stateful dialog management (simulator/agents_graphs/dialog_graph.py, simulator/dialog/dialog_manager.py, requirements.txt includes langgraph)

### Step B: Benchmark Preparation (Inputs)

**❌ Strategy 1: Benchmark Dataset Preparation (Offline)**
- **Supported**: NO
- **Evidence**: IntellAgent generates its own synthetic test scenarios rather than loading pre-existing benchmark datasets

**✅ Strategy 2: Synthetic Data Generation (Generative)**
- **Supported**: YES
- **Evidence**: Core capability - generates synthetic edge-case scenarios through policy graph sampling and LLM-based event generation (simulator/dataset/descriptor_generator.py, simulator/dataset/events_generator.py, docs/architecture.md)

**✅ Strategy 3: Simulation Environment Setup (Simulated)**
- **Supported**: YES
- **Evidence**: Creates simulated database states and scenario contexts as part of event generation (simulator/dataset/events_generator.py, simulator/env.py)

**❌ Strategy 4: Production Traffic Sampling (Online)**
- **Supported**: NO
- **Evidence**: No built-in production traffic sampling or stream buffering capabilities

### Step C: Benchmark Preparation (References)

**✅ Strategy 1: Ground Truth Preparation**
- **Supported**: YES
- **Evidence**: Pre-computes expected behaviors and policy requirements for each generated scenario (simulator/dataset/descriptor_generator.py, simulator/dataset/definitions.py)

**✅ Strategy 2: Judge Preparation**
- **Supported**: YES
- **Evidence**: Configurable LLM-based critique/judge models for evaluating conversation adherence to policies (config/config_default.yml critique_config, simulator/dialog/dialog_manager.py set_critique method)

---

## Phase II: Execution (The Run)

### Step A: SUT Invocation

**✅ Strategy 1: Batch Inference**
- **Supported**: YES
- **Evidence**: Executes multiple scenarios through dialog simulation with batch processing support (simulator/simulator_executor.py run_simulation method, config/config_default.yml mini_batch_size parameter)

**❌ Strategy 2: Arena Battle**
- **Supported**: NO
- **Evidence**: No built-in support for concurrent multi-SUT comparison; evaluates one agent at a time

**✅ Strategy 3: Interactive Loop**
- **Supported**: YES
- **Evidence**: Core capability - implements stateful multi-turn dialog simulation between user agent and chatbot with tool usage (simulator/agents_graphs/dialog_graph.py, simulator/dialog/dialog_manager.py)

**❌ Strategy 4: Production Streaming**
- **Supported**: NO
- **Evidence**: No production traffic streaming or real-time monitoring capabilities

---

## Phase III: Assessment (The Score)

### Step A: Individual Scoring

**✅ Strategy 1: Deterministic Measurement**
- **Supported**: YES
- **Evidence**: Binary success/failure scoring based on policy violation detection and conversation termination conditions (simulator/simulator_executor.py analyze_results method)

**❌ Strategy 2: Embedding Measurement**
- **Supported**: NO
- **Evidence**: No semantic similarity or embedding-based metrics implemented natively

**✅ Strategy 3: Subjective Measurement**
- **Supported**: YES
- **Evidence**: LLM-based critique agent evaluates conversations against policies and provides subjective judgments (simulator/dialog/dialog_manager.py, simulator/utils/analysis.py)

**❌ Strategy 4: Performance Measurement**
- **Supported**: NO (limited)
- **Evidence**: Tracks token costs but no built-in latency, throughput, memory, or energy consumption metrics

### Step B: Aggregate Scoring

**✅ Strategy 1: Distributional Statistics**
- **Supported**: YES
- **Evidence**: Computes success rates, failure rates, and performance breakdowns by complexity level and policy category (simulator/simulator_executor.py analyze_results, simulator/visualization/)

**❌ Strategy 2: Uncertainty Quantification**
- **Supported**: NO
- **Evidence**: No bootstrap resampling or confidence interval estimation implemented

---

## Phase IV: Reporting (The Output)

### Step A: Insight Presentation

**✅ Strategy 1: Execution Tracing**
- **Supported**: YES
- **Evidence**: Captures detailed dialog logs, user thoughts, chatbot messages, and tool calls in SQLite database (simulator/utils/sqlite_handler.py, simulator/dialog/dialog_manager.py)

**✅ Strategy 2: Subgroup Analysis**
- **Supported**: YES
- **Evidence**: Performance breakdown by challenge level, policy category, and flow (simulator/visualization/pages/1_📈_Experiments_Report.py)

**❌ Strategy 3: Regression Alerting**
- **Supported**: NO
- **Evidence**: No automated regression detection or alerting against historical baselines

**✅ Strategy 4: Chart Generation**
- **Supported**: YES
- **Evidence**: Generates performance visualizations including success rate charts and challenge level distributions using Plotly (simulator/visualization/, requirements.txt includes plotly)

**✅ Strategy 5: Dashboard Creation**
- **Supported**: YES
- **Evidence**: Interactive Streamlit dashboard for visualization with experiment comparisons and policy analysis (simulator/visualization/Simulator_Visualizer.py, simulator/visualization/pages/)

**❌ Strategy 6: Leaderboard Publication**
- **Supported**: NO
- **Evidence**: No public or private leaderboard submission capabilities

---

## Supported Strategies Summary

**Total Supported: 18 out of 40 strategies**

### Provisioning (3/7)
- Git Clone ✅
- PyPI Packages ✅
- API Provider Authentication ✅
- Repository Authentication ✅ (implicit)

### Specification (6/8)
- Model-as-a-Service ✅
- Model-in-Process ✅ (limited)
- Policy/Agent Instantiation ✅
- Synthetic Data Generation ✅
- Simulation Environment Setup ✅
- Ground Truth Preparation ✅
- Judge Preparation ✅

### Execution (2/4)
- Batch Inference ✅
- Interactive Loop ✅

### Assessment (3/6)
- Deterministic Measurement ✅
- Subjective Measurement ✅
- Distributional Statistics ✅

### Reporting (4/6)
- Execution Tracing ✅
- Subgroup Analysis ✅
- Chart Generation ✅
- Dashboard Creation ✅

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
