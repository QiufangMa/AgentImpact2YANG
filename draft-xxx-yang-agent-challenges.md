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

The industry today is actively exploring the application of agentic AI to autonomous network operations. However, insufficient attention has been paid to the impacts that the introduction of agentic AI may impose on YANG modeling, which serves as the foundation of model-driven network automation.

Based on an end-to-end intent translation workflow that engages YANG models across service, network, and device layers, this document identifies some critical gaps when AI agents generate and operate on YANG data. The purpose of this document is not to propose YANG extensions or new modeling languages. Instead, it aims to facilitate discussion on how existing YANG ecosystems can be better leveraged, and how complementary mechanisms can bridge these gaps while preserving YANG’s interoperability.


--- middle

# Introduction

RFC 8969 {{?RFC8969}} establishes a framework for automating service and network management with YANG {{?RFC7950}}. Combined with network management protocols such as NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}, YANG delivers deterministic and interoperable capabilities for configuration manipulation and state retrieval across service, network, and device layers.

Recently, Large Language Models (LLMs) and AI Agents have emerged as new enablers for network automation. Multiple IETF activities are ongoing in this area, proposals include but are not limited to:

 * The network digital twin and agentic AI based architecture for AI driven network operations described in {{?I-D.wmz-nmrg-agent-ndt-arch}}, which deploys hybrid agent systems within the network autonomous domain, and allows agents to invoke network operational function modules and collaborate to solve non-trivial tasks.

 * The Network Management Agent (NMA) concept presented in {{?I-D.zhao-nmop-network-management-agent}}, which proposes that network management agents can be deployed at both orchestrator and controller layers, with A2A, A2C, and A2N interfaces enabling communication among agents to agents, controllers, and network elements.

* The applicability of MCP {{MCP}} for Network Management {{?I-D.yang-nmrg-mcp-nm}}, which explores how the Model Context Protocol can refactor network management operations and network capabilities as LLM-callable tools.

 * The applicability of A2A to Network Management {{?I-D.yang-nmrg-a2a-nm}}, which outlines how the Agent to Agent protocol can be leveraged to develop various rich AI driven network applications, realize intent-based network management automation in the multi-vendor heterogeneous network environment.

While the industry is actively exploring the application of agentic AI to autonomous network, litter work considers how YANG modeling, as the very foundation of model-driven network automation, would be impacted, and if there is any critical gaps that remain unrecognized at the intersection of probabilistic AI agents and deterministic YANG.

The purpose of this document is not to extend YANG, or define a brand new data model language. Rather, it seeks how to make IETF-developed YANG AI-ready and be better leveraged by agentic AI, by attempting to identify some fundamental gaps in the hope of facilitating community discussion on how to bridge them.


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

This section uses a simplied layered agentic AI reference architecture compliant with {{I-D.draft-wmz-nmrg-agent-ndt-arch}} and describes two typical workflows: network service provisioning/optimization and fault troubleshooting. The workflows cover scenarios from configuration generation to YANG data consumption, which are essential to expose and derive the gaps in {{gaps}}. Note that these flows are not the only possible ones. The intent flows shown here are just examples, i.e., typical workflows that illustrate how YANG models at the service, network, and device layers can be engaged in an agent-integrated network automation context.

## Layered AI Agent Deployment Architecture

This document targets a two-layer agent deployment architecture as specified in {{arch}}. It is a simplied view that hides task agents, function modules or toolsets that are available for agents to invoke. Refer to {{?I-D.draft-wmz-nmrg-agent-ndt-arch}} for the complete architecture design.


~~~~
+---------------------+
|    Orchestrator     |
|  +----------------+ |
|  |  AI Agent(s)   | |
|  +------^---------+ |
+---------+-----------+
          |
          |A2A Protocol
          |//YANG-structured payload; or domain-level intent
          |
+---------+-----------+
|     Controller      |
|+--------v----------+|
||Network AI Agent(s)||
|+--------^----------+|
+---------+-----------+
          |
          |NETCONF/RESTCONF/MCP
          |
+---------v-----------+
|   Network Devices   |
+---------------------+
~~~~
{: #arch title="A simplified reference architecture" artwork-align="center"}


## Workflow 1: Service Provisioning/Optimization

A complete end-to-end workflow focusing on AI agents generating configuration from high-level intents is defined as follows.

Step 1:
: A network operator submits an end-to-end service request expressed in natural language including eplicit service requirements. The intent may imply some constraints such as not affecting existing network services.

Step 2:
: The orchestrator AI agent parses the natural language intent, mapped it into the corresponding service model and parameters for user confirmation, and then decomposes it into structured configuration using network model.

Step 3:
: YANG-structured data are encapsulated as message payload, and transmitted to network AI agents along with other context metadata via A2A protocol.

Step 4:
: The network AI agent converts received tasks into structured device model layer operations. It either invokes standard YANG tools through MCP, or directly delivers YANG configuration via NETCONF or RESTCONF. Validation checks such as syntax and semantic validation may be performed before the device configuration is delivered.

Step 5:
: Network devices receive incoming configuration, execute changes, and report execution response.

## Workflow 2: Network Troubleshooting

Step 1:
: A network operator submits a high-level operational intent, requesting the network AI agent to continuously monitor the network, perceive emerging anomalies, perform active diagnosis, and conduct autonomous repair while reporting root causes and handling results.

Step 2:
: Network AI agent receives the comprehensive telemetry data which provides a holistic view of the network operational state across multiple network devices and YANG modules. By analyzing the real-time data, it could detect network anomalies swiftly, which enables the prompt identification of potential issues before they escalate into major faults.

Step 3:
: Once a issue or fault is identified, network AI agent diagnoses the exact cause and generate targeted repair solutions. These solutions may involve making configuration changes on relevant network devices, such as when a failure is caused by misconfiguration.

Step 4:
: The network AI agent autonomously executes the formulated repair actions by delivering corresponding YANG configuration changes to devices, after invoking the network digital twin to simulate the proposed repair solution successfully.

# Gap Analysis {#gaps}

## Gap 1: Semantic Incompleteness


Natural language intents from operators inherently carry ambiguity, implicit constraints, missing parameters and diverse interpretations. In contrast, YANG models and corresponding instance data require fully qualified XPaths, strictly typed values, and full compliance with model constraints such as when, must and leafref.

When the AI agent translates natural language into YANG-structured data, two outstanding issues arise. First, AI hallucinations are commonly observed: it may generate a non-existent YANG node, invalid enumeration values or out-of-range parameters, or overlook a "when"/"must" constraints on configuration data defined by YANG. This problem is even amplified in multi-vendor and cross-version model scenarios. Second, implicit operational requirements embedded in original intents, such as service continuity, high availability and security compliance, are likely lost during the transformation process. As a result, the generated configuration data are either syntactically invalid, semantically incomplete, or inconsistent with the original operator intent.

When analyzing massive telemetry data composed of operational state data from multiple devices, agents may fail to fully understand nested model hierarchies and implicit dependencies between YANG nodes. Incomplete semantic comprehension could lead to misjudgment of network anomalies, inaccurate fault localization, and flawed repair solutions derived from misinterpreted state data.

## Gap 2: Expressiveness Limits

YANG is designed to describe static and target state of services, networks and devices. It focuses on defining data structures and formats, syntax and semantic rules and static constraints, without capabilities to represent flexible operational logic.

In real-world network operations, operator intents often contain relaxation preferences, degradation policies, retry strategies, or conditional fallback rules. These dynamic and flexible requirements cannot be natively expressed by standard YANG models. To adapt to rigid YANG structures, orchestrator AI agents have to trim and simplify the original intent. This premature semantic trimming deprives downstream network AI agents of the flexibility to make dynamic decisions, perform graceful degradation or apply fault-tolerant adjustments when network runtime status changes.

In short, the fixed-state design of YANG creates a hard boundary for carrying intent relaxation, which limits the autonomous decision space of multi-agent systems.

## Gap 3: Transaction Conflicts

In the reference two-layer agentic AI architecture, orchestrator AI agent and network AI agent could operate autonomously and asynchronously with no centralized global scheduling. Different agents may initiate independent tasks simultaneously against the same set of network devices and configuration data.

A typical conflicting scenario occurs when an orchestrator AI agent is executing a service provisioning task, while a network AI agent triggers real-time network fault diagnosis and resolution, based on continuous telemetry streams and analysis.

Concurrent write operations on identical YANG nodes may lead to configuration overwriting and policy conflicts, which might cause critical risks in asynchronous multi-agent operational environments.

## Gap 4: Lack of Explainability

YANG is designed for deterministic network automation. It assumes the client knows exactly what to configure and why. AI agents, on the contrary, needs to explain their decisions to build trust. AI agents act as autonomous decision-makers that generate YANG configuration data and related operations (e.g., create, replace, delete), and actively retrieve network state data. For trusted, auditable, and human-collaborative autonomous operations, agents are required to provide explanation for every YANG-level action, e.g., why specific configuration nodes are modified, and why particular network states are retrieved for incident diagnosis.

Currently, YANG and YANG-driven network management protocols has no standard fields for such explanatory metadata. An agent may silently send an \<edit-config\> RPC operation that deletes a leaf, without any indication of why the deletion was necessary.

# Possible Ways Forward

This section explores several operational directions to try to bridge the gaps identified in {{gaps}} and improve the AI-readiness of existing YANG ecosystem.

## YANG-driven Operations as AI-Invocable Tools

A promising direction for partially addressing Gap 1 (Semantic Incompleteness) is to refactor YANG‑based network operations into AI‑invocable tools using the Model Context Protocol (MCP). Instead of requiring an AI agent to generate raw YANG instance data directly, which is prone to hallucination, the agent invokes an MCP tool with structured parameters. By shielding agents from low-level syntax and model complexity, this abstraction layer reduces the agent’s burden of understanding complex YANG schemas and mitigates the risk of generating invalid configuration data.

The applicability of MCP to network management is discussed in {{I-D.yang-nmrg-mcp-nm}}. Industry open-source exploration is emerging towards this direction, e.g., gNMIBuddy {{gNMIBuddy}} provides a toolkit to wrap gNMI and OpenConfig YANG data models based network operations, designed primarily for LLMs with MCP integration.

## Semantic Enrichment via Knowledge Graphs

Gap 1 (Semantic Incompleteness) and Gap 2 (Expressiveness Limits) can be partially addressed by adding a semantic layer on top of YANG data models. Knowledge graphs (KGs) provide a machine‑readable representation of network knowledge, enabling AI agents to better understand the semantics, relationships, and constraints embedded in the network.

IETF work in this area includes {{?I-D.mackey-nmop-kg-for-netops}} and {{?I-D.tailhardat-nmop-incident-management-noria}}, which correlate data from different network planes, e.g., management, control, and data planes and present a holistic view of network status.

When a user expresses an high-level intent such as "check why the VPN tunnel is down", a KG‑enhanced agent can query the YANG-based knowledge graph to understand the relationships between relevant services and metrics, addressing Gap 1. Furthermore, KGs can explicitly model concepts such as "preferred vs. optional", temporal KGs can model concepts such as "transient vs. persistent failure", which are currently absent from YANG, partially addressing Gap 2.

## Human-in-the-Loop

While full autonomy is a long‑term goal for agentic AI enabled network management, a mechanism that escalates decisions based on assessed risk or confidence level is essential for safe deployment. This approach partially addresses Gap 4 (Explainability), specifically the need for human oversight of agent decisions, and also helps mitigate high‑impact transaction conflicts (Gap 3).

For example, a low-risk operation proceeds automatically without human approval, while a high-risk one renders a visual diff, the network digital twin simulation report, and the agent’s intent explanation (including confidence and evidence), and pushes the report to a human for approval.


# Security Considerations

The integration of agentic AI with YANG-based network automation introduces new attack surfaces and operational risks that differ from traditional deterministic network management.

For example, AI-generated configuration risks are introduced by agent autonomous decision-making. Unlike manually crafted or scripted configurations, LLM-generated configuration edits may contain unintended misconfiguration or semantic deviations. Such errors can lead to service outages, policy violations, or enlarged attack surfaces. The human-in-the-loop mechanism and network digital twin simulation and validation could mitigate this concern by blocking high-risk unvalidated changes before cofiguration is committed. Furthermore, tool invocation and credential exposure risks arise when AI agents are granted access to network devices via MCP servers. Open-source MCP implementations enable LLM clients to execute read/write operations over managed devices. Unauthorized or malicious prompt injection may trigger abusive command execution, configuration tampering, or information leakage. Token-based authentication, transport encryption, and tool-level guardrail mechanisms (blocked command lists and restricted configuration scopes) are required to enforce access control.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
