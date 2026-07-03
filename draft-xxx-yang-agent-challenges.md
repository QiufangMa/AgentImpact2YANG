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

Based on some end-to-end intent translation workflow analysis that engage YANG models across service, network, and device layers, this document identifies some critical gaps when AI agents generate and operate on YANG data. The purpose of this document is not to substantially redesign YANG or define a new data model language. Instead, it aims to facilitate discussion on how existing YANG ecosystems can be better leveraged in the context of agentic AI, and how complementary mechanisms can bridge these gaps while preserving YANG’s interoperability.


--- middle

# Introduction

RFC 8969 {{?RFC8969}} establishes a framework for automating service and network management using YANG {{?RFC7950}}. Combined with network management protocols such as NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}, YANG delivers deterministic and interoperable capabilities for configuration manipulation and state retrieval across service, network, and device layers.

Recently, Large Language Models (LLMs) and AI Agents have emerged as new enablers for network automation. Multiple IETF activities are ongoing in this area, proposals include but are not limited to:

 * The network digital twin {{?I-D.irtf-nmrg-network-digital-twin-arch}} and agentic AI based architecture for AI driven network operations described in {{?I-D.wmz-nmrg-agent-ndt-arch}}, which deploys hybrid agent systems within the network autonomous domain, and allows agents to invoke network operational function modules and collaborate to solve non-trivial tasks.

 * The Network Management Agent (NMA) concept presented in {{?I-D.zhao-nmop-network-management-agent}}, which proposes that network management agents can be deployed at both orchestrator and controller layers, with A2A, A2C, and A2N interfaces enabling communication among agents to agents, controllers, and network elements.

* The applicability of MCP {{MCP}} for Network Management {{?I-D.yang-nmrg-mcp-nm}}, which explores how the Model Context Protocol can refactor network management operations and network capabilities as LLM-callable tools.

 * The applicability of A2A to Network Management {{?I-D.yang-nmrg-a2a-nm}}, which outlines how the Agent to Agent protocol can be leveraged to develop various rich AI driven network applications, realize intent-based network management automation in the multi-vendor heterogeneous network environment.

While the industry is actively exploring the application of agentic AI to autonomous network, little attention has been paid to how YANG modeling, as the very foundation of model-driven network automation, would be impacted, and if there is any critical gaps that remain unrecognized/unsolved at the intersection of probabilistic AI agents and deterministic structured YANG.

This document analyzes two typical network operation workflows driven by hierarchical AI Agents: service provisioning/optimization and network troubleshooting. It identifies key gaps arising from the adoption of YANG data models in these scenarios.
The purpose of this document is not to substantially redesign YANG or define a brand-new data modeling language. Rather, it seeks to adapt YANG for agentic AI. By identifying fundamental gaps, this document aims to facilitate community discussion on how to bridge the gap between them.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

The document uses the following terms defined in {{?RFC8309}} and {{?RFC8969}}:

 * data model

 * service model

 * network model

 * device model

 * network domain

 The document uses the following terms defined in {{?I-D.hong-nmrg-agenticai-ps}}:

 * Agentic AI

The following definitions are used throughout this document:

AI Agent:
: An autonomous software system driven by an artificial
   intelligence reasoning engine (typically a large
   language model) that integrates a set of resources such as data and tools to perceive its operational environment, dynamically plan and
   execute decisions, and invoke actions to achieve specific goals.

   Unlike traditional deterministic or rule-based autonomous systems,
   an AI Agent exhibits non-deterministic reasoning, maintains runtime
   and historical state, and possesses the capability to dynamically
   decompose abstract high-level intents into a sequence of
   operational actions.


# A Reference Architecture and Typical Workflows

This section uses a simplified layered agentic AI reference architecture compliant with {{?I-D.draft-wmz-nmrg-agent-ndt-arch}} and describes two typical workflows: the network service provisioning/optimization and the fault troubleshooting. The workflows cover scenarios from configuration generation to YANG modelled data consumption, which are essential to expose and derive the gaps in {{gaps}}. Note that these flows are not the only possible ones. The intent flows shown here are just examples, i.e., typical workflows that illustrate how YANG models at the service, network, and device layers can be engaged in an agent-integrated network automation context.

## Layered AI Agent Deployment Architecture

This document targets a hierarchical agent deployment architecture as specified in {{arch}}. It is a simplified view that hides task agents, function modules or toolsets that are available for agents to invoke. The architecture may also use memory management (e.g., short-term and long-term memory) to maintain conversational continuity and enable contextual search.
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
: YANG-structured data are encapsulated as message payload, and transmitted to network AI agents along with other context metadata via agent communication protocol like Agent-to-Agent (A2A).

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

## Gap 1: Lack rich semantics on Declarative YANG 

 YANG was originally created to model configuration and state data for deterministic networking hardware. Because it is strictly declarative
 it is fantastic at defining static "what" boundaries is (e.g., "in-errors counter is great than 1000") , but it completely lacks the rich semantic structures needed to describe imperative policy, e.g., take simple and instant action when a trigger condition on the managed objects is met {{I-D.ietf-netmod-eca-policy}}.
 less than represent dynamic, probabilistic, and intent-driven AI policies.
 In other words, YANG excels at strict data trees, types, and constraints, but it cannot express cognitive conditions, reasoning flows, or behaviour constraints.
 
 The Cognitive Condition Constraint: 
 "Ensure the agent does not exhibit racial or gender bias while evaluating the applicant's text-based employment history
 
 Reasoning flow Constraint:
 "The agent must use a Chain-of-Thought reasoning loop. It must first cross-reference external credit history, then explicitly state its counter-arguments, and finally generate a risk score."
 
 Behaviour Constraint:
 "The agent is permitted to decline a loan, but it must remain strictly professional, and never disclose the exact internal proprietary weights of the credit scoring algorithm, even if the user begs for it."

 In addition, when we map YANG to other data model or ontology model or use YANG to build ontology model, YANG falls short to provide rich semantics for various different type of data such as log, metrics, Anomaly, time series data for various different high value scenarios such as fault management, quality optimization, 
 network change, etc.

## Gap 2: Handling uncertain or ambiguous tasks

when tasked with ambiguous instructions, missing/unreliable data, deep reasoning, or executing multi-step decisions, the agent's behavior could inevitably introduce randomness and unpredictability. These unpredictable variations in Agent and LLM outputs can significantly impact the reliability and consistency of network operations.

For the same high-level intent, an agent may generate different YANG instance data due to historical context drift or the probabilistic nature of the underlying large language models (steps 2 and 4 of the service provisioning/optimization workflow in {{provision}}). Likewise, when analyzing network anomalies or incidents, multiple distinct diagnostic trajectories and explanations may emerge even for the exact same observed network operational state (steps 3 and 4 of the network troubleshooting workflow in {{troubleshoot}}).

Therefore it is important to Support natural language interactions for understanding and handling uncertain or ambiguous tasks and also ensure the accuracy and efficiency of structured data for deterministic tasks.

<!--
## Gap 1: Insufficient Semantics Comprehension {#comprehension}

YANG is a data modeling language rich in semantics. When the AI agent translates the operator intent expressed via natural language into YANG-structured data, AI hallucinations are commonly observed due to insufficient comprehension of YANG semantics.

This issue arises for several reasons. First, a lot of YANG semantics rely on informal natural language expressed via "description" statement, which is designed for human-readability. Second, cross-node associations implemented via "leafref", "when", or "must" statements need to be traversed step by step, in which process Large Language Models may suffer from drift. Both easily introduce ambiguity and potential misinterpretation, making it hard for LLMs to generate correct YANG configuration. Furthermore, real-world network deployments introduce more challenges through vendor-specific YANG extensions, augmentations, and deviations, as well as multiple versions of YANG modules, all of which contribute to exponential difficulties in completely comprehending YANG semantics.

 This gap hinders Step 2 of the Service Provisioning/Optimization Workflow ({{provision}}), where the Orchestrator Agent ingests a high-level customer intent and attempts to map it into service-level YANG models (e.g., ietf-l3vpn-svc).
While syntax errors (e.g., non-existent nodes, invalid enumeration values or out-of-range parameters) can be effectively detected and fixed by configuration validation tools, the more significant challenge lies in intent-level mismatch. For instance, the agent may misinterpret certain YANG data nodes, producing configuration that is syntactically valid yet fails to align with the operator's original intent.

Similarly in the step 2 of the network troubleshooting workflow ({{troubleshoot}}), when analyzing massive telemetry data composed of operational state data from multiple devices, agents may struggle to fully understand nested model hierarchies and implicit dependencies between YANG nodes, and thus fail to infer the correlations embedded within operational data. Incomplete semantic comprehension could lead to misjudgment of network anomalies, inaccurate fault localization, and flawed repair solutions derived from misinterpreted state data.


## Gap 2: Expressiveness Limits {#Expressiveness}

In real-world network operations, operator intents often contain expectations and constraints, relaxation preferences, retry strategies, or fallback rules. Nevertheless, YANG is designed solely focuses on defining data structures and formats, syntax and semantic rules and static data dependencies. It lacks the ability to express dynamic and flexible operational logic, which creates a hard boundary for carrying intent and limits the decision space of multi-agent system.

This Gap hinders step 3 of the service provisioning/optimization workflow ({{provision}}). To adapt to static YANG structures, orchestrator AI agents have to prune and simplify the original intent. Implicit operational requirements embedded in original intents, such as service continuity, high availability and security compliance, are likely lost during the transformation process.

This premature semantic pruning deprives downstream network AI agents of the flexibility to make dynamic decisions, perform graceful degradation or apply fault-tolerant adjustments when network runtime status changes.


## Gap 3: Non-determinism and Uncertainty in YANG-Level Actions {#Uncertainty}

YANG is a fully deterministic modeling language with no built-in support for uncertain or probabilistic reasoning, while the operational determinism of an AI agent behavior may vary across different levels of task complexity and abstraction. Under scenarios where the prompt provides precise, explicit operational instructions (e.g., "modify the MTU value of interface Eth1/1 on Device A to 1500"), or where strict guardrails and constraint checks are enforced, the agent's behavior could achieve or closely approach determinism.
However, when tasked with ambiguous instructions, missing/unreliable data, deep reasoning, or executing multi-step decisions, the agent's behavior could inevitably introduce randomness and unpredictability. These unpredictable variations in Agent and LLM outputs can significantly impact the reliability and consistency of network operations.

For the same high-level intent, an agent may generate different YANG instance data due to historical context drift or the probabilistic nature of the underlying large language models (steps 2 and 4 of the service provisioning/optimization workflow in {{provision}}). Likewise, when analyzing network anomalies or incidents, multiple distinct diagnostic trajectories and explanations may emerge even for the exact same observed network operational state (steps 3 and 4 of the network troubleshooting workflow in {{troubleshoot}}).

Besides, AI agents also inherently produce outputs with varying degrees of confidence. When an agent generates a configuration change, there is currently a lack of visibility into how confident the agent was in that specific operation, and whether the agent internally marks a YANG-level action as tentative, low-confidence, or likely to be reverted.



## Gap 4: Lack of Explainability {#Explainability}

YANG is designed for the deterministic network automation. It assumes the client knows exactly what to configure and why. AI agents, which act as autonomous decision-makers that generate YANG configuration data and perform specific network operations, needs to explain their decisions for human oversight, operational auditing, and compliance governance. This gap manifests in steps 2 and 4 of the service provisioning/optimization workflow ({{provision}}), as well as steps 3 and 4 of the network troubleshooting workflow ({{troubleshoot}}).

In the service provisioning/optimization workflow ({{provision}}), AI agents decompose abstract intents into granular configuration across multiple modules and devices. There is no mechanism to log how the agent fulfill operator's expectations, prioritizes constraints, and trades off competing operational requirements throughout Step 2 and Step 4.

Correlations and dependencies embedded within hierarchical YANG data hinder causal explainability for fault analysis. Even if it could capture which tools it calls and what data it retrieves, it remains invisible how it internally determine the root cause among different anomaly heterogeneous data. Consequently, it becomes untrust to generate unambiguous causal explanations for operators in Step 3 and Step 4 during network troubleshooting ({{troubleshoot}}).

## Gap 5: Context Explosion in YANG-Based Environments {#context}

LLM-based agents operate within finite context windows. In step 2 of the service provisioning/optimization workflow ({{provision}}), where the orchestrator agent maps a high-level customer intent into service-level YANG data (e.g., ietf-l3vpn-svc), the LLM needs to understand the schema of relevant YANG modules, including node hierarchies, type definition, constraints, and relationship and dependencies across YANG modules. Furthermore, vendors may introduce customized YANG extension, augmentations and deviations. Loading the entire collection of relevant YANG schemas leads to a "context explosion" problem, which introduces unacceptable inference latency and unpredictable operational costs during real-time network operations.

During the network troubleshooting workflow ({{troubleshoot}}) at Step 2, where network AI agents need to perceive network anomalies, the inherent separation between YANG schemas and runtime instance data forces agents to load correlated schema definitions alongside operational data to correctly interpret telemetry streams. This significantly enlarges the overall context size that agents must process.

While the token context windows of modern LLMs continue to expand, high-density schemas cause LLMs to suffer from severe focus degradation. Critical configuration nodes, path reference (leafref), or specific "when" conditional statement located in the middle of a massive context window are easily overlooked (i.e., the "lost-in-the-middle" phenomenon), which causes a decline in LLM reasoning performance.
-->

# Possible Ways Forward

This section explores several operational directions to try to bridge the gaps identified in {{gaps}} and improve the AI-readiness of existing YANG ecosystem.

## YANG-driven Operations as AI-Invocable Tools

A promising direction for partially addressing gap in {{comprehension}} is to refactor YANG‑based network operations into AI‑invocable tools using the Model Context Protocol (MCP). Instead of requiring an AI agent to generate raw YANG instance data directly, which is prone to hallucination or exhibits non-determined behavior, the agent invokes an MCP tool with structured parameters. By shielding agents from low-level syntax and model complexity, this abstraction layer reduces the agent’s burden of understanding complex YANG schemas and mitigates the risk of generating invalid configuration data.

The applicability of MCP to network management is discussed in {{I-D.yang-nmrg-mcp-nm}}. Industry open-source exploration is emerging towards this direction, e.g., gNMIBuddy {{gNMIBuddy}} provides a toolkit to wrap gNMI and OpenConfig YANG data models based network operations, designed primarily for LLMs with MCP integration.

## Semantic Enrichment via Knowledge Graphs and Semantic Metadata

Gaps identified in {{comprehension}} and {{Expressiveness}} can be partially addressed by adding a semantic layer on top of YANG data models. Knowledge graphs (KGs) provide a machine‑readable representation of network knowledge, enabling AI agents to better understand the semantics, relationships, and constraints embedded in the network.

IETF work in this area includes {{?I-D.mackey-nmop-kg-for-netops}} and {{?I-D.tailhardat-nmop-incident-management-noria}}, which correlate data from different network planes, e.g., management, control, and data planes and present a holistic view of network status.

When a user expresses a high-level intent such as "check why the VPN tunnel is down", a KG‑enhanced agent can query the YANG-based knowledge graph to understand the relationships between relevant services and metrics, addressing gap in {{comprehension}}. Furthermore, KGs can explicitly model concepts such as "preferred vs. optional", temporal KGs can model concepts such as "transient vs. persistent failure", which are currently absent from YANG, partially addressing {{Expressiveness}}.

There is also other work in NMOP focusing on semantic enrichment that could serve as a foundation layer for agentic AI driven network anomaly detection. A set of ongoing drafts include:

 * {{?I-D.ietf-nmop-network-anomaly-architecture}} provides a reference architecture for knowledge based service disruption detection;

 * {{?I-D.ietf-nmop-network-anomaly-lifecycle}} defines an experiment for managing the lifecycle process of a network anomaly detection system, spanning across detection, validation, and refinement.

 * {{?I-D.ietf-nmop-network-anomaly-semantics}} describes common network symptom semantics across different network planes.

An AI agent gains a structured and machine-understandable vocabulary for describing, querying, and reasoning about network anomaly. The standardized anomaly semantic metadata provides the missing semantic hooks, and contributes to bridging gaps in {{comprehension}} and {{Expressiveness}}.


## Human-in-the-Loop

While full autonomy is a long‑term goal for agentic AI enabled network management, a mechanism that escalates decisions based on assessed risk or confidence level is essential for safe deployment. This approach helps avoid the negative effect caused due to agent uncertainty in YANG-Level Actions ({{Uncertainty}}).

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
