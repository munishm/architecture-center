# Dynamic AI Agents at scale pattern
Description

# Key Challenges 
Only discuss the challenges in the following sections, not the solutions.
## Dynamic inclusion of agents
## Cost optimization
## Orchestration
## Evaluating as system evolves
## Evolution

# Architecture

<img width="1093" height="886" alt="image" src="https://github.com/user-attachments/assets/1da43bdb-b2c1-4cf7-a884-b132676bcac7" />


## System Design High-level
Diagram of high level infrastructure and services, Then sections on each component double click

## Workflow

## Components

### Agent Selection
Time to find a better name for this
#### Evaluation criteria

### Orchestration
Options of orchestration (Agents as tools), complexity with multi-turn, Short-cut paths, 
#### Evaluation criteria

### Agent Implementation options - In-Code, Yaml, MCP, A2A
#### Evaluation criteria

### Evolution of system - Creating/updating Agents

## Evaluation Framework 
Details around a possible structure of an evaluation framework

## Observability

When building AI solutions—especially those powered by **multi-agent architectures**—reliability, maintainability, and performance are non-negotiable.  
However, as these systems expand in scale and complexity, maintaining visibility into their behavior becomes increasingly difficult.

Unlike traditional applications, agentic systems involve multiple intelligent components that collaborate dynamically: language models, orchestration layers, caching mechanisms, retrieval engines, and external APIs.  
Each component contributes to the outcome, which makes **monitoring, debugging, and optimization** far more intricate.

That's where **observability** becomes essential—not as a buzzword, but as the foundation for understanding emergent AI behavior.

---

## Why observability matters in AI systems

Observability provides insight into what your application is doing—not just whether the underlying infrastructure is running. You need to distinguish between **system observability** (infrastructure metrics like CPU, memory, and network) and **application observability** (orchestration logic, agent behavior, prompt flows, and model inference).

In multi-agent environments, application observability answers questions such as:

- Why did one agent take longer to respond than others?  
- What sequence of calls led to a poor or inconsistent result?  
- Which model parameters or prompts were in play during a failure?  

By combining signals from infrastructure, orchestration, and model behavior, observability bridges the gap between **system performance** and **model intelligence**.


## Our observability framework

We've built our observability framework on **OpenTelemetry** for instrumentation and **Azure Application Insights** as the telemetry backend.  

- **OpenTelemetry** standardizes how traces, metrics, and logs are captured across agents and services. It ensures interoperability across frameworks and programming languages.  
- **Application Insights** aggregates and visualizes this telemetry—offering dashboards, alerts, and the ability to explore correlations between infrastructure metrics, application traces, and LLM inference data.

For agents built with **Semantic Kernel**, observability is integrated through OpenTelemetry's standardized instrumentation. Semantic Kernel automatically emits traces, logs, and metrics for kernel operations such as function invocations, prompt executions, and plugin calls when you configure OpenTelemetry:

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

# Configure OpenTelemetry with Application Insights
trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)

span_processor = BatchSpanProcessor(
    AzureMonitorTraceExporter.from_connection_string(
        connection_string="<your-application-insights-connection-string>"
    )
)
trace.get_tracer_provider().add_span_processor(span_processor)
```


## The three dimensions of agentic observability

Traditional observability stops at *logs, metrics, and traces*.  
In AI systems, we extend those pillars to include **semantic and behavioral observability**—how agents reason, collaborate, and evolve during execution.

### 1. Execution logs  
Beyond infrastructure logging, we capture **semantic events**: prompts, responses, and intermediate reasoning steps between agents.  
Logs here are not just for debugging—they help reconstruct **conversation context**, agent decisions, and prompt flow.  

All log data is streamed via **OpenTelemetry exporters** to **Azure Log Analytics**, where we use **KQL** to correlate across agents and identify anomalies at the conversation level.

---

### 2. System and model metrics  
Metrics provide quantitative signals about both system and model performance.  
We track latency, throughput, and cost—but also **AI-specific metrics** such as:

- Token usage (prompt vs. completion)  
- TTFT (time to first token)  
- Model call success and error rates  
- Conversation depth and engagement trends  

These metrics help balance efficiency, quality, and responsiveness—critical for understanding user experience and inference cost at scale.

---

### 3. Distributed traces with context  
Traces connect every service and agent involved in a single conversation.  
By using **trace IDs** and **span IDs**, we can view the full path of an inference request—from the orchestrator to downstream agents, caches, and external calls.

Each trace carries **semantic context**, such as conversation ID and agent name, enabling a unified view of model collaboration.  
This is particularly useful for diagnosing latency spikes, identifying network bottlenecks, or analyzing where agent coordination might fail.


## Observability data flow for agentic systems

<img src="../architecture/_images/ai-agents-at-scale-observability-flow.png" alt="Observability data flow for agentic systems" width="1200" />

1. **Instrumentation:** Agents and services are instrumented with OpenTelemetry to emit logs, traces, and metrics.  
2. **Export:** Data flows to **Azure Application Insights** via OpenTelemetry SDKs.  
3. **Storage & Querying:**  
   - Logs → Log Analytics (KQL for cross-agent queries)  
   - Traces → Transaction Search and distributed trace view  
   - Metrics → Azure Metrics Explorer  
4. **Visualization & Alerting:** Azure Monitor dashboards track real-time performance, with rule-based alerts triggering incident response workflows.

A single trace ID flows through the entire conversation lifecycle—from the initial request to the orchestrator, through agent invocations, function tool calls, and external API services. Each component creates child spans under the parent trace, allowing you to reconstruct the complete execution path and identify where latency or errors occurred across agents and auxiliary services.

## Observability for LLM and agent systems

For LLM-driven architectures, observability must capture **the cognitive layer**—what the model or agent saw, decided, and produced.

We track not just infrastructure telemetry, but contextual data such as:

- **System prompts:** The instructions that guided behavior.  
- **Model parameters:** Temperature, top-p, token limits.  
- **User inputs and conversation history:** What context was passed to the model.  
- **Outputs:** The actual text, decisions, or structured responses.  

Capturing this metadata enables **reproducibility** of inference runs—helping data scientists analyze why an output differed, whether drift occurred, or if bias emerged.

## Key metrics categories

To maintain comprehensive visibility, we track multiple metric layers:

### **System Performance**
- Latency (per component and overall)
- Throughput and active request load
- Resource utilization (CPU, memory, disk I/O)
- Reliability (error rate, uptime)
- Connection and authentication failures

### **LLM Inference Performance**
- TTFT (time to first token)
- Token usage (prompt vs. completion)
- Error rates and retry counts
- Content safety triggers and blocked responses

### **Usage & Engagement**
- Active conversations and users (DAU, MAU)
- Conversation depth and duration
- Repeated or abandoned queries

### **Quality & Model Accuracy**
- Intent and agent selection accuracy (precision, recall, F1)
- Sentiment trends in generated responses
- Instruction adherence and factual consistency
- Bias, fairness, and groundedness indicators

## Example: Capturing LLM metrics

We use lightweight wrappers to record LLM inference metrics directly from agent services:

```python
from observability.record_metrics.record_llm_inference_metrics import record_llm_metrics

# Record token usage
record_llm_metrics(
    prompt_tokens=prompt_tokens,
    completion_tokens=completion_tokens,
    total_tokens=total_tokens,
    labels={"agent_name": "reception-agent","operation": "generate-response", "status": "success"},
    component="agent-service"
)
```

## Best practices

- **Uniform Instrumentation:** Apply OpenTelemetry consistently across all microservices and agents.  
- **Correlation IDs:** Include trace and span IDs in every log and metric.  
- **Sampling and Retention:** Balance data richness with cost efficiency using intelligent sampling.  
- **Dashboards and Alerts:** Define SLIs/SLOs and automate alerting for anomalies.  
- **Secure Data Handling:** Mask or omit sensitive information from logs and traces.  
- **Cross-functional Collaboration:** Engineers, data scientists, and product teams should share a unified observability view.
