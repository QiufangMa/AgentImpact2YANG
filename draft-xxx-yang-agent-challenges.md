---
title: "Rethinking YANG-based Service and Network Management in the Era of Agentic AI"
abbrev: "Agent implications for YANG"
category: info

docname: draft-xxx-yang-agent-challenges-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - Agentic AI
 - YANG
 - intent
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "QiufangMa/AgentImpact2YANG"
  latest: "https://QiufangMa.github.io/AgentImpact2YANG/draft-xxx-yang-agent-challenges.html"

author:
 -
    fullname: Qiufang Ma
    organization: Huawei
    role: editor
    street: 101 Software Avenue, Yuhua District
    city: Jiangsu
    code: 210012
    country: China
    email: maqiufang1@huawei.com
 -
    fullname: Qin Wu
    organization: Huawei
    street: 101 Software Avenue, Yuhua District
    city: Nanjing, Jiangsu
    code: 210012
    country: China
    email: bill.wu@huawei.com
normative:

informative:

  MCP:
    title: Model Context Protocol
    target: https://modelcontextprotocol.io/
    date: November 2024

  gNMIBuddy:
    title: gNMIBuddy
    target: https://github.com/jillesca/gNMIBuddy
    date: February 2026

--- abstract

The industry today is actively exploring the application of agentic AI to autonomous network operations. However, there is little attention given to the impacts that the introduction of agentic AI may impose on YANG modeling, which serves as the foundation of model-driven network automation.

Based on some end-to-end intent translation workflow analysis that engage YANG models across service, network, and device layers, this document identifies some critical gaps when AI agents generate and operate on YANG data. The purpose of this document is not to propose major YANG extensions or new modeling languages. Instead, it aims to facilitate discussion on how existing YANG ecosystems can be better leveraged, and how complementary mechanisms can bridge these gaps while preserving YANG’s interoperability.


--- middle

# Introduction

RFC 8969 {{?RFC8969}} establishes a framework for automating service and network management using YANG {{?RFC7950}}. Combined with network management protocols such as NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}, YANG delivers deterministic and interoperable capabilities for configuration manipulation and state retrieval across service, network, and device layers.

Recently, Large Language Models (LLMs) and AI Agents have emerged as new enablers for network automation. Multiple IETF activities are ongoing in this area, proposals include but are not limited to:

 * The network digital twin {{?I-D.irtf-nmrg-network-digital-twin-arch}} and agentic AI based architecture for AI driven network operations described in {{?I-D.wmz-nmrg-agent-ndt-arch}}, which deploys hybrid agent systems within the network autonomous domain, and allows agents to invoke network operational function modules and collaborate to solve non-trivial tasks.

 * The Network Management Agent (NMA) concept presented in {{?I-D.zhao-nmop-network-management-agent}}, which proposes that network management agents can be deployed at both orchestrator and controller layers, with A2A, A2C, and A2N interfaces enabling communication among agents to agents, controllers, and network elements.

* The applicability of MCP {{MCP}} for Network Management {{?I-D.yang-nmrg-mcp-nm}}, which explores how the Model Context Protocol can refactor network management operations and network capabilities as LLM-callable tools.

 * The applicability of A2A to Network Management {{?I-D.yang-nmrg-a2a-nm}}, which outlines how the Agent to Agent protocol can be leveraged to develop various rich AI driven network applications, realize intent-based network management automation in the multi-vendor heterogeneous network environment.

While the industry is actively exploring the application of agentic AI to autonomous network, little attention has been paid to how YANG modeling, as the very foundation of model-driven network automation, would be impacted, and if there is any critical gaps that remain unrecognized/unsolved at the intersection of probabilistic AI agents and deterministic structured YANG.

The purpose of this document is not to substantially redesign YANG or define a brand-new data modeling language. Rather, it seeks to adapt YANG for agentic AI. By identifying fundamental gaps, this document aims to facilitate community discussion on how to bridge the gap between them.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

The document uses the following terms defined in {{?RFC8309}} and {{?RFC8969}}:

 * data model

 * service model

 * network model

 * device model

 * network domain

The following definitions are used throughout this document:

Agentic AI:
: to be completed...

# A Reference Architecture and Typical Workflows

This section uses a simplified layered agentic AI reference architecture compliant with {{?I-D.draft-wmz-nmrg-agent-ndt-arch}} and describes two typical workflows: the network service provisioning/optimization and the fault troubleshooting. The workflows cover scenarios from configuration generation to YANG modelled data consumption, which are essential to expose and derive the gaps in {{gaps}}. Note that these flows are not the only possible ones. The intent flows shown here are just examples, i.e., typical workflows that illustrate how YANG models at the service, network, and device layers can be engaged in an agent-integrated network automation context.

## Layered AI Agent Deployment Architecture

This document targets a two-layer agent deployment architecture as specified in {{arch}}. It is a simplified view that hides task agents, function modules or toolsets that are available for agents to invoke. The architecture may also use memory management (e.g., short-term and long-term memory) to maintain conversational continuity and enable contextual search.
Refer to {{?I-D.draft-wmz-nmrg-agent-ndt-arch}} for the complete architecture design.


~~~~
+--------------------------+
|Orchestrator              |
|+------------------------+|
||Orchestrator AI Agent(s)||
|+-----------^------------+|
+------------+-------------+
             |
             |A2A Protocol
             |//YANG-structured payload; or domain-level intent
             |
  +----------+----------+
  |Controller|          |
  |+---------v---------+|
  ||Network AI Agent(s)||
  |+-------------------+|
  +----------^----------+
             |
             |NETCONF/RESTCONF/Telemetry
             |
  +----------v----------+
  |   Network Devices   |
  +---------------------+
~~~~
{: #arch title="A simplified Hierarchical AI Agent Architecture" artwork-align="center"}


## Workflow Example 1: Intent-Driven Service Provisioning/Optimization {#provision}

A complete end-to-end workflow focusing on AI agents generating configuration from high-level intents is defined as follows.

Step 1:
: A network operator submits an end-to-end service request expressed in natural language including explicit service requirements. The intent may imply some constraints such as not affecting existing network services.

Step 2:
: The orchestrator AI agent parses the natural language intent, maps it into the corresponding service model and parameters for user confirmation, and then decomposes it into structured configuration using network model.

Step 3:
: YANG-structured data are encapsulated as message payload, and transmitted to network AI agents along with other context metadata via A2A protocol.

Step 4:
: The network AI agent converts received tasks into structured device model layer operations. The configuration is handed off to the network controller's Southbound Interface (SBI) and delivered via YANG-driven protocols such as NETCONF and RESTCONF. Validation checks may be performed before the configuration is delivered to network devices.

Step 5:
: Network devices receive incoming configuration, execute changes, and report execution response.

## Workflow Example 2: Network Troubleshooting {#troubleshoot}

Step 1:
: A network operator submits a high-level operational intent, requesting the network AI agent to continuously monitor the network, perceive emerging anomalies, perform active diagnosis, and conduct autonomous repair while reporting root causes and handling results.

Step 2:
: Network AI agent grasps a holistic view of the network operational state across multiple network devices and YANG modules. Instead of flooding the Network AI agent with massive, raw streaming telemetry data which would instantly overwhelm the LLM's context window and incur prohibitive token costs, agent may leverage existing network data analytics components or tools to be aware of real-time network state. Network AI agent could detect network anomalies swiftly, which enables the prompt identification of potential issues before they escalate into major faults.


Step 3:
: Once an issue or fault is identified, network AI agent diagnoses the exact cause and generate targeted repair solutions. These solutions may involve making configuration changes on relevant network devices, for example, when a failure is caused by misconfiguration.

Step 4:
: The network AI agent autonomously executes the formulated repair actions by delivering corresponding YANG configuration changes to devices, after invoking the network digital twin to simulate the proposed repair solution successfully.

# Gap Analysis {#gaps}

## Gap 1: Insufficient Agent Comprehension of YANG Semantics {#comprehension}

YANG is a data modeling language rich in semantics. YANG data models and corresponding instance data require fully qualified XPaths, strictly typed values, and full compliance with model constraints such as "when", "must", and "leafref" statements.

This gap hinders Step 2 of the Service Provisioning/Optimization Workflow ({{provision}}), where the Orchestrator Agent ingests a high-level customer intent and attempts to map it into service-level YANG models (e.g., ietf-l3vpn-svc).
When the AI agent translates the operator intent expressed via natural language into YANG-structured data, AI hallucinations are commonly observed due to insufficient comprehension of YANG semantics or incomplete YANG semantics. While syntax errors (e.g., non-existent nodes, invalid enumeration values or out-of-range parameters) may be effectively detected and fixed by configuration validation tools, the more significant challenge lies in intent-level mismatch. For instance, the agent may misinterpret some YANG data nodes, resulting in the delivered configuration inconsistent with the original operator intent.

And in the step 2 of the network troubleshooting workflow ({{troubleshoot}}), when analyzing massive telemetry data composed of operational state data from multiple devices, agents may fail to fully understand nested model hierarchies and implicit dependencies between YANG nodes and modules. Incomplete semantic comprehension could lead to misjudgment of network anomalies, inaccurate fault localization, and flawed repair solutions derived from misinterpreted state data.

Furthermore, real-world network deployments introduce more challenges through vendor-specific YANG extensions, augmentations, and deviations, as well as multiple versions of YANG modules, all of which contribute to exponential difficulties in completely comprehending YANG semantics.

## Gap 2: Expressiveness Limits {#Expressiveness}

Natural language intents from operators inherently carry ambiguity, implicit constraints, missing parameters and diverse interpretations. Nevertheless, YANG focuses on defining data structures and formats, syntax and semantic rules and static constraints, without capabilities to represent flexible operational logic.

In real-world network operations, operator intents often contain constraints, relaxation preferences, degradation policies, retry strategies, or conditional fallback rules. These dynamic and flexible requirements cannot be natively expressed by standard YANG models.

This Gap hinders step 3 of the service provisioning/optimization workflow ({{provision}}). To adapt to static YANG structures, orchestrator AI agents have to trim and simplify the original intent. Implicit operational requirements embedded in original intents, such as service continuity, high availability and security compliance, are likely lost during the transformation process.

This premature semantic trimming deprives downstream network AI agents of the flexibility to make dynamic decisions, perform graceful degradation or apply fault-tolerant adjustments when network runtime status changes.

In short, the fixed-state design of YANG creates a hard boundary for carrying intent relaxation, which limits the autonomous decision space of multi-agent systems.

## Gap 3: Lack of Trust or Explainability {#Explainability}

YANG is designed for the deterministic network automation. It assumes the client knows exactly what to configure and why. AI agents, which act as autonomous decision-makers that generate YANG configuration data and invoke related operations (e.g., create, replace, delete), and actively retrieve network state data, needs to explain their decisions to build trust. This gap exists in the steps 2 and 4 of the service provisioning/optimization workflow ({{provision}}), and steps 3 and 4 of the network troubleshooting workflow ({{troubleshoot}}).

For trusted, auditable, and human-collaborative autonomous operations, agents are required to provide:

 * Action-level explanation: For instance, why did the agent perform this specific YANG operation, why specific configuration nodes are modified, why particular network states are retrieved for incident diagnosis, and which intent or task triggered it.

 * Task-level explanation: A high-level intent may involve dozens of YANG operations action multiple devices. There is a need to explain why a series of actions were taken, or how a task was fulfilled.

 * Causal explanation: when a specific YANG action leads to an unexpected network behavior, an agent needs to explain the causal chain from the change to the observed state.

## Gap 4: Agent Non-determinism and Uncertainty in YANG-Level Actions {#Uncertainty}

The operational determinism of an AI agent behavior may vary across different levels of task complexity and abstraction. Under scenarios where the prompt provides precise, explicit operational instructions (e.g., "modify the MTU value of interface Eth1/1 on Device A to 1500"), or where strict guardrails and constraint checks are enforced, the agent's behavior could achieve or closely approach absolute determinism.
However, when tasked with deep reasoning, processing complex contexts, or executing multi-step decisions, the agent's behavior inevitably introduces randomness and unpredictability. For the same high-level intent, an agent may generate different YANG instance data due to historical context drift or the probabilistic nature of the underlying large language models (steps 2 and 4 of the service provisioning/optimization workflow in {{provision}}). Likewise, when analyzing network anomalies or incidents, multiple distinct diagnostic trajectories and explanations may emerge for the exact same observed network operational state (steps 3 and 4 of the network troubleshooting workflow in {{troubleshoot}}).

Besides, AI agents also inherently produce outputs with varying degrees of confidence.
When an agent generates a configuration change, there is currently a lack of visibility into how confident the agent was in that specific operation, and whether the agent internally marks a YANG-level action as tentative, low-confidence, or likely to be reverted.

## Gap 5: Context Explosion in YANG-Based Environments {#context}

LLM-based agents operate within finite context windows. In step 2 of the service provisioning/optimization workflow ({{provision}}), where the orchestrator agent maps a high-level customer intent into service-level YANG data (e.g., ietf-l3vpn-svc), the LLM needs to understand the schema of relevant YANG modules, including node hierarchies, type definition, constraints, and relationship and dependencies across YANG modules. Furthermore, vendors may introduce customized YANG extension, augmentations and deviations. Loading the entire collection of relevant YANG schemas leads to a "context explosion" problem, which introduces unacceptable inference latency and unpredictable operational costs during real-time network operations.

While the token context windows of modern LLMs continue to expand, high-density schemas cause LLMs to suffer from severe focus degradation. Critical configuration nodes, path reference (leafref), or specific "when" conditional statement located in the middle of a massive context window are easily overlooked (i.e., the "lost-in-the-middle" phenomenon), which causes a decline in LLM reasoning performance.

# Possible Ways Forward

This section explores several operational directions to try to bridge the gaps identified in {{gaps}} and improve the AI-readiness of existing YANG ecosystem.

## YANG-driven Operations as AI-Invocable Tools

A promising direction for partially addressing {{comprehension}} is to refactor YANG‑based network operations into AI‑invocable tools using the Model Context Protocol (MCP). Instead of requiring an AI agent to generate raw YANG instance data directly, which is prone to hallucination or exhibits non-determined behavior, the agent invokes an MCP tool with structured parameters. By shielding agents from low-level syntax and model complexity, this abstraction layer reduces the agent’s burden of understanding complex YANG schemas and mitigates the risk of generating invalid configuration data.

The applicability of MCP to network management is discussed in {{I-D.yang-nmrg-mcp-nm}}. Industry open-source exploration is emerging towards this direction, e.g., gNMIBuddy {{gNMIBuddy}} provides a toolkit to wrap gNMI and OpenConfig YANG data models based network operations, designed primarily for LLMs with MCP integration.

## Semantic Enrichment via Knowledge Graphs and Semantic Metadata

Gap1 Insufficient Agent Comprehension of YANG Semantics and Gap 2 Expressiveness Limits can be partially addressed by adding a semantic layer on top of YANG data models. Knowledge graphs (KGs) provide a machine‑readable representation of network knowledge, enabling AI agents to better understand the semantics, relationships, and constraints embedded in the network.

IETF work in this area includes {{?I-D.mackey-nmop-kg-for-netops}} and {{?I-D.tailhardat-nmop-incident-management-noria}}, which correlate data from different network planes, e.g., management, control, and data planes and present a holistic view of network status.

When a user expresses a high-level intent such as "check why the VPN tunnel is down", a KG‑enhanced agent can query the YANG-based knowledge graph to understand the relationships between relevant services and metrics, addressing Gap 1 (Insufficient Agent Comprehension of YANG Semantics). Furthermore, KGs can explicitly model concepts such as "preferred vs. optional", temporal KGs can model concepts such as "transient vs. persistent failure", which are currently absent from YANG, partially addressing {{Expressiveness}}.

There is also other work in NMOP focusing on semantic enrichment that could serve as a foundation layer for agentic AI driven network anomaly detection. A set of ongoing drafts include:

 * {{?I-D.ietf-nmop-network-anomaly-architecture}} provides a reference architecture for knowledge based service disruption detection;

 * {{?I-D.ietf-nmop-network-anomaly-lifecycle}} defines an experiment for managing the lifecycle process of a network anomaly detection system, spanning across detection, validation, and refinement.

 * {{?I-D.ietf-nmop-network-anomaly-semantics}} describes common network symptom semantics across different network planes.

An AI agent gains a structured and machine-understandable vocabulary for describing, querying, and reasoning about network anomaly. The standardized anomaly semantic metadata provides the missing semantic hooks, and contributes to bridging Gap1 (Insufficient Agent Comprehension of YANG Semantics) and Gap 2 (Expressiveness Limits). Moreover, the standardized anomaly semantics also help improve explainability (Gap 3), as agents can log which anomaly or symptom has triggered a repair action using well-defined metadata.


## Human-in-the-Loop

While full autonomy is a long‑term goal for agentic AI enabled network management, a mechanism that escalates decisions based on assessed risk or confidence level is essential for safe deployment. This approach partially addresses Gap 3 (Lack of Explainability), specifically the need for human oversight of agent decisions, and also helps mitigate Agent Uncertainty in YANG-Level Actions (Gap 4).

For example, a low-risk operation proceeds automatically without human approval, while a high-risk one renders a visual diff, the network digital twin simulation report, and the agent’s intent explanation (including confidence and evidence), and pushes the report to a human for approval. It could be possible for users to verify agent decisions and further clarify their intentions.


# Security Considerations

The integration of agentic AI with YANG-based network automation introduces new attack surfaces and operational risks that differ from traditional deterministic network management.

For example, AI-generated configuration risks are introduced by agent autonomous decision-making. Unlike manually crafted or scripted configurations, LLM-generated configuration edits may contain unintended misconfiguration or semantic deviations. Such errors can lead to service outages, policy violations, or enlarged attack surfaces. The human-in-the-loop mechanism and network digital twin simulation and validation could mitigate this concern by blocking high-risk unvalidated changes before configuration is committed. Furthermore, tool invocation and credential exposure risks arise when AI agents are granted access to network devices via MCP servers. Open-source MCP implementations enable LLM clients to execute read/write operations over managed devices. Unauthorized or malicious prompt injection may trigger abusive command execution, configuration tampering, or information leakage. Token-based authentication, transport encryption, and tool-level guardrail mechanisms (blocked command lists and restricted configuration scopes) are required to enforce access control.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
