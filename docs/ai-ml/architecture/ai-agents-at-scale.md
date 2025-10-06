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
The Agent Selector efficiently identifies and selects the most relevant agents to address user inquiries from a broad pool of available agents. By integrating Azure AI Search (utilizing vector similarity) with a Large Language Model (LLM), the system delivers contextually-aware agent selection. This approach enhances downstream processes, including seamless inter-agent and agent-user interactions, to produce the desired response or action in line with the user's query. This document presents an overview of the components and workflow underpinning the Agent Selector. 

The structure of the Agent Selector system is illustrated in the following diagram: 

![Agent Selector System Diagram](_images/ai-agents-at-scale-agent-selection.png)

#### Workflow Summary 
User Query → Alias Mapping → Semantic Search (Azure AI Search) → Agent Scoring & Filtering → Orchestrator (Select & Invoke Agent) 

1. User submits a query along with registered device IDs.
2. Query goes through alias mapping to get a normalized query.
3. Normalized query is sent to Azure AI Search (semantic cache) to find top matching agent utterances.
4. Each agent is assigned the highest similarity score based on vector similarity scores of the normalized query with utterances in the semantic cache.
5. Agents with scores above predefined thresholds are shortlisted.
6. Candidate agents are intersected with the user’s registered device agents.
7. Duplicate agents are removed; each agent is assigned its highest score.
8. If only one agent remains and its score is above the confidence threshold, it is selected; otherwise, SupervisorAgent is added.
9. Agents from the previous turn are added from chat history.
10. Final agent list is sent to the Orchestrator.
11. Orchestrator invokes the single agent directly, or uses an LLM to select if multiple agents are available.
#### Evaluation criteria

### Orchestration
Options of orchestration (Agents as tools), complexity with multi-turn, Short-cut paths, 
#### Evaluation criteria

### Agent Implementation options - In-Code, Yaml, MCP, A2A
#### Evaluation criteria

### Evolution of system - Creating/updating Agents

## Evaluation Framework 
Details around a possible structure of an evaluation framework

## Agent Onboarding Process 

The idea behind this process is to maintain a high-quality, conflict-free multi-agent system where every agent addition, update, or removal is deliberate and validated. Agents are the building blocks of intelligent orchestration, so introducing or modifying one without checks can lead to degraded performance, overlapping responsibilities, or broken user experiences. To prevent this, the lifecycle emphasizes evaluation-driven governance at every stage. Below is a flow diagram of the onboarding process. 

![AI Agents Onboarding Process](_images/ai-agents-at-scale-onboarding-process.png) 

The process starts with onboarding a new agent, which involves verifying the uniqueness of its name, description, and sample utterances. Once validated, a temporary semantic cache is created, and the system runs semantic and response evaluations to ensure the new agent doesn’t negatively impact existing ones. If results meet benchmarks, the agent is promoted to production, and the golden dataset—our ground truth for evaluations—is updated to reflect the new capabilities. Similarly, when updating an agent, the same validation and regression checks apply to avoid selection drift. Updating the golden dataset is critical for keeping evaluations aligned with real-world usage, while deleting an agent requires careful decommissioning steps to remove dependencies and maintain system integrity. This structured approach ensures scalability without sacrificing accuracy or reliability. 

## Observability


## Conclusion



