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


--- abstract

 to be completed...
 discussion on the impacts imposed on YANG ecosystems (including device, network and service models) remain insufficient.


--- middle

# Introduction

RFC 8969 {{?RFC8969}} establishes a framework for automating service and network management with YANG {{?RFC7950}}. Combined with network management protocols such as NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}, YANG delivers deterministic and interoperable capabilities for configuration manipulation and state retrieval across service, network, and device layers.

Recently, Large Language Models (LLMs) and AI Agents have emerged as new enablers for network automation. Multiple IETF activities are ongoing in this area, proposals include but are not limited to:

 * The network digital twin and agentic AI based architecture for AI driven network operations described in {{?I-D.wmz-nmrg-agent-ndt-arch}}, which deploys hybrid agent systems within the network autonomous domain, and allows agents to invoke network operational function modules and collaborate to solve non-trivial tasks.

 * The Network Management Agent (NMA) concept presented in test {{?I-D.smith-vxlan-group-policy}} {{?I‑D.zhao-nmop-network-management-agent}}, which proposes that network management agents can be deployed at both orchestrator and controller layers, with A2A, A2C, and A2N interfaces enabling communication among agents to agents, controllers, and network elements.

* The applicability of MCP for Network Management {{?I‑D.yang-nmrg-mcp-nm}}, which explores how the Model Context Protocol can refactor network management operations and network capabilities as LLM-callable tools.

 * The applicability of A2A to Network Management {{?I‑D.yang-nmrg-a2a-nm}}, which outlines how the Agent to Agent protocol can be leveraged to develop various rich AI driven network applications, realize intent-based network management automation in the multi-vendor heterogeneous network environment.

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

# A Reference Intent Workflow: From Natural Language to YANG Configuration

## Layered AI Agent Deployment Architecture

This document targets a two-layer agent deployment architecture as specified in {{arch}}:

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

A complete end-to-end workflow is defined as follows. Note that the following flow is not the only possible one. The intent flow shown here is an example — a typical workflow that illustrates how YANG models at the service, network, and device layers can be engaged in an agent-integrated network automation context.

Step 1:
: A network operator submits an E2E service request expressed in natural language.

Step 2:
: The orchestrator AI agent parses the natural language intent, mapped it into the corresponding service model for user confirmation, and then decomposes it into structured network model layer YANG data.

Step 3:
: YANG-structured data are encapsulated as message payload, and transmitted to network AI agents along with other context metadata via A2A protocol.

Step 4:
: The network AI agent converts received tasks into structured device model layer operations. It either invokes standard YANG tools through MCP, or directly delivers YANG configuration via NETCONF or RESTCONF.

Step 5:
: Network devices receive incoming configurations against YANG models, execute changes, and report execution response.


# Gap Analysis

## Gap 1: Semantic Incompleteness

Natural language intents from operators inherently carry ambiguity, implicit constraints, missing parameters and diverse interpretations. In contrast, YANG models and corresponding instance data require fully qualified XPaths, strictly typed values, and full compliance with model constraints such as when, must and leafref.

When the AI agent translates natural language into YANG-structured data, two outstanding issues arise. First, AI hallucinations are commonly observed: it may generate a non-existent YANG node, invalid enumeration values or out-of-range parameters, or overlook a "when"/"must" constraints on configuration data defined by YANG. This problem is even amplified in multi-vendor and cross-version model scenarios. Second, implicit operational requirements embedded in original intents, such as service continuity, high availability and security compliance, are likely lost during the transformation process.

As a result, the generated configuration data are either syntactically invalid, semantically incomplete, or inconsistent with the original operator intent.

## Gap 2: Expressiveness Limits

YANG is designed to describe static and target state of services, networks and devices. It focuses on defining data structures and formats, syntax and semantic rules and static constraints, without capabilities to represent flexible operational logic.

In real-world network operations, operator intents often contain relaxation preferences, degradation policies, retry strategies, or conditional fallback rules. These dynamic and flexible requirements cannot be natively expressed by standard YANG models. To adapt to rigid YANG structures, orchestrator AI agents have to trim and simplify the original intent. This premature semantic trimming deprives downstream network AI agents of the flexibility to make dynamic decisions, perform graceful degradation or apply fault-tolerant adjustments when network runtime status changes.

In short, the fixed-state design of YANG creates a hard boundary for carrying intent relaxation, which limits the autonomous decision space of multi-agent systems.

## Gap 3: Transaction Conflicts

In the reference two-layer agentic AI architecture, orchestrator AI agent and network AI agent could operate autonomously and asynchronously with no centralized global scheduling. Different agents may initiate independent tasks simultaneously against the same set of network devices and configuration data.

A typical conflicting scenario occurs when an orchestrator AI agent is executing a service provisioning task, while a network AI agent triggers real-time network incident diagnosis and resolution, based on continuous telemetry streams and analysis.

Concurrent write operations on identical YANG nodes may lead to configuration overwriting and policy conflicts, which might cause critical risks in asynchronous multi-agent operational environments.

## Gap 4: Explainability

YANG is designed for deterministic network automation. It assumes the client knows exactly what to configure and why. AI agents, on the contrary, needs to explain their decisions to build trust. AI agents act as autonomous decision-makers that generate YANG configuration data and related operations (e.g., create, replace, delete), and actively retrieve network state data. For trusted, auditable, and human-collaborative autonomous operations, agents are required to provide explanation for every YANG-level action, e.g., why specific configuration nodes are modified, and why particular network states are retrieved for incident diagnosis.

Currently, YANG and YANG-driven network management protocols has no standard fields for such explanatory metadata. An agent may silently send an \<edit-config\> RPC operation that deletes a leaf, without any indication of why the deletion was necessary.





# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
