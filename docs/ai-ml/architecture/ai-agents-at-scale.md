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

## Agent Onboarding Process 

The idea behind this process is to maintain a high-quality, conflict-free multi-agent system where every agent addition, update, or removal is deliberate and validated. Agents are the building blocks of intelligent orchestration, so introducing or modifying one without checks can lead to degraded performance, overlapping responsibilities, or broken user experiences. To prevent this, the lifecycle emphasizes evaluation-driven governance at every stage. Below is a flow diagram of the onboarding process. 

![AI Agents Onboarding Process](_images/ai-agents-at-scale-onboarding-process.png) 

The process starts with onboarding a new agent, which involves verifying the uniqueness of its name, description, and sample utterances. Once validated, a temporary semantic cache is created, and the system runs semantic and response evaluations to ensure the new agent doesn’t negatively impact existing ones. If results meet benchmarks, the agent is promoted to production, and the golden dataset—our ground truth for evaluations—is updated to reflect the new capabilities. Similarly, when updating an agent, the same validation and regression checks apply to avoid selection drift. Updating the golden dataset is critical for keeping evaluations aligned with real-world usage, while deleting an agent requires careful decommissioning steps to remove dependencies and maintain system integrity. This structured approach ensures scalability without sacrificing accuracy or reliability. 

## Observability


## Conclusion



