# Dynamic AI Agents at scale pattern
This architecture describes a multi-agent agentic solution, that allows dynamic selection of probable agents in a conversation out of a big universe of 100s of agents. The architecture explains the key challenges in building a dynamic inclusion agentic system, possible orchestration options and evaluating the system as it scales to 100s of agents.

This architecture applies Azure AI Foundry, Azure AI Search, Azure Open AI in Azure AI Foundry models, and other azure services to build a scalable agentic solution. 

The architecture is mostly valid for use-cases where there are many agents involved in open-ended conversations with the clients, and conversation domain is not fixed. 

# Key Challenges 

## Dynamic inclusion of agents
Imagine that your organization has multiple domain specialized agents, and you want to build a single conversation AI which allows the clients to use any of these agents, without excatly being concerned about which agent is doing the work. In addition, there can be situations where more than one agent is being used in a single conversation (multi-intent scenario), for example - "Help me book a conference room in Yosemite floor, and inform the parking services that I may need 5 spots for customers meeting on 26th." - This use case could be using ConferenceBooking Agent, as well as the ParkingServiceAgent to do both works. Essentially, it's a simple problem to solve when the number of agents/tools is smaller (like under 20), and mostly [function calling](https://learn.microsoft.com/en-us/semantic-kernel/concepts/ai-services/chat-completion/function-calling/?pivots=programming-language-python) pattern does a good job.
Choosing which particular function before adding in a conversation is one of the key challenges here, when the list of agents grow longer.

## Cost optimization

Some caveats of function calling are 
1. Doesn't scale well with larger number of functions - Maximum limit being 19.
2. Token count increases as number of functions increase
3. With Token count the cost and time both increase

Return on investment is a big concern for all agentic systems, Token count is a big factor in agentic system costs. As the number of agents in system grow, the context window of the system increases to retain more knowledge about all the agents/functions in the system and the cost keeps growing.

## Orchestration

There are many options in orchestrating a multi-agent conversation; options like Group chat, Handoff, concurrent, sequential, Magentic etc.
A key challenge is to figure out what is the orchestration pattern for your business in particular cases. In many multi-agent systems - specialized agents are built to converse with each other or do a job after one another, while in many systems the nature of each agent can be stark different and yet the client's may expect multiple agents to finish the job concurrently.
In this architecture, we will put out a view point on possible selection of orchestration pattern when working at a dynamic scale, as at dynamic scale the nature of exact business and relations within agents may not be always known.

## Evaluating as system evolves


## Evolution

# Architecture

<img width="1093" height="886" alt="image" src="https://github.com/user-attachments/assets/1da43bdb-b2c1-4cf7-a884-b132676bcac7" />


## System Design High-level
Diagram of high level infrastructure and services, Then sections on each component double click

## Workflow

## Components

### Agent Selection
The Agent Selector is designed to efficiently identify and choose the most appropriate agents for addressing user inquiries from an extensive pool of candidates. Through the integration of Azure AI Search—leveraging vector similarity as a semantic cache—to narrow the list of agents, followed by the application of a Large Language Model (LLM) to select from this refined group, the system ensures contextually-aware agent selection. This methodology enhances subsequent processes, promoting effective inter-agent and agent-user interactions to produce responses or actions aligned with the user's query. This document provides an overview of the key components and workflow that underpin the Agent Selector. 

The structure of the Agent Selector system is illustrated in the following diagram: 

![Agent Selector System Diagram](_images/ai-agents-at-scale-agent-selection.png)

#### Workflow Summary 
User Query → Alias Mapping → Semantic Search (Azure AI Search) → Agent Scoring & Filtering → Orchestrator (Select & Invoke Agent) 

1. User submits a query along with registered agent IDs.
2. Query goes through alias mapping to get a normalized query.
3. Normalized query is sent to Azure AI Search (semantic cache) to find top matching agent utterances.
4. Each agent is assigned the highest similarity score based on vector similarity scores of the normalized query with utterances in the semantic cache.
5. Agents with scores above predefined thresholds are shortlisted.
6. Candidate agents are intersected with the user’s registered agents.
7. Remove duplicate agents by retaining only the highest similarity score for each agent based on matched utterances.
8. If a single agent remains and its score exceeds the confidence threshold, select that agent. If not, include the SupervisorAgent for further evaluation.
9. Incorporate agents from the previous conversation turn using chat history.
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



