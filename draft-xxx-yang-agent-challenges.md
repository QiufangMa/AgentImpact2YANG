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

 * The network digital twin and agentic AI based architecture for AI driven network operations described in {{?I-D.wmz‑nmrg‑agent‑ndt‑arch}}, which deploys hybrid agent systems within the network autonomous domain, and allows agents to invoke network operational function modules and collaborate to solve non-trivial tasks.

 * The Network Management Agent (NMA) concept presented in {{?I‑D.zhao‑nmop‑network‑management‑agent}}, which proposes that network management agents can be deployed at both orchestrator and controller layers, with A2A, A2C, and A2N interfaces enabling communication among agents to agents, controllers, and network elements.

* The applicability of MCP for Network Management {{?I‑D.yang‑nmrg‑mcp‑nm}}, which explores how the Model Context Protocol can refactor network management operations and network capabilities as LLM-callable tools.

 * The applicability of A2A to Network Management {{?I‑D.yang‑nmrg‑a2a‑nm}}, which outlines how the Agent to Agent protocol can be leveraged to develop various rich AI driven network applications, realize intent-based network management automation in the multi-vendor heterogeneous network environment.

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

## Gap 1: Semantic Incompleteness/Loss


## Gap 2: Expressiveness Limits


## Gap 3: Transaction Conflicts



## Gap 4: Closed-Loop Validation and Explainability



# YANG in the Era of Agentic AI: Potential Paths Forward


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
