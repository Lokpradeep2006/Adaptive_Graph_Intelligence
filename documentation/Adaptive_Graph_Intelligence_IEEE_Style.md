# Adaptive Graph Intelligence (AGI)
## Formulations and Algorithms Reference

> **Project:** CORTEX – Context & Graph-Aware Multi-Agent Framework for Explainable Industrial IoT Threat Intelligence  
> **Component:** Adaptive Graph Intelligence Engine  
> **Coverage:** Module 1 – Dataset Validation · Module 2 – Graph Feature & Relationship Engineering · Module 3 – Graph Schema · Module 4 – Temporal Knowledge Graph

**Purpose:** This document provides a traceable technical reference for the formulations, metrics, rules, transformations, and algorithms used within the Adaptive Graph Intelligence implementation.

> **Important:** This document describes the **current project implementation**. Standard methods are not claimed as novel; heuristics are explicitly identified; derived/inferred methods are separated from directly observed data; and planned methods are not represented as implemented.

1. CLASSIFICATION OF METHODS

Every formulation or algorithm is classified using the following
categories:

[STANDARD]
A conventional mathematical or algorithmic method.

[PROJECT-DERIVED]
A method formulated specifically from the project's graph/data model.

[HEURISTIC]
A rule-based decision mechanism created for practical processing.

[INFERRED]
Information derived from available evidence rather than directly
observed in the source telemetry.

[ADAPTED]
An established method adapted to the project's requirements.

[PLANNED]
A proposed method that is not currently implemented.
### IMPLEMENTATION STATUS
IMPLEMENTED
    Verified in the current codebase.
### DERIVED
    Computed from available project data.

HEURISTIC
    Generated through explicit project rules.

PLANNED / NOT IMPLEMENTED
    Discussed or proposed but not currently executed.
## 2. CORE GRAPH NOTATION

D
    Telemetry dataset.

N_total
    Total number of records in a dataset.

N_missing
    Number of missing/null values.

c
    Dataset attribute / column.

e_i
    Temporal event node i.

t_i
    Timestamp associated with event i.

Δt
    Temporal difference between two events.

F(e)
    Feature representation associated with entity e.

G = (V, E)
    Graph consisting of nodes V and edges E.

V
    Set of graph nodes.

E
    Set of graph relationships/edges.

C(r)
    Relationship confidence score.

PR(v)
    PageRank score of graph node v.
## 3. MODULE 1 — DATASET VALIDATION
### PURPOSE
-------
Module 1 establishes dataset quality and graph-readiness before
graph feature and relationship construction.
### FORMULA M1-F01 — COLUMN MISSING RATE

Classification:
STANDARD / DATA-QUALITY METRIC

Formula:
`
MissingRate(c) =`
    (N_missing / N_total) × 100

Where:

N_missing = number of null/NaN values in column c
N_total   = total number of records

Purpose:
Measures how much information is missing from an attribute.

Used for:
- Dataset profiling
- Attribute-quality assessment
- Graph-readiness analysis
### FORMULA M1-F02 — ATTRIBUTE COMPLETENESS

Formula:
`
Completeness(c) =`
    100 - MissingRate(c)

Purpose:
Represents the percentage of available values in an attribute.

Higher completeness generally indicates better suitability for
downstream graph representation.
### FORMULA M1-F03 — COLUMN UNIQUENESS RATIO

Formula:
`
Uniqueness(c) =`
    NumberOfUniqueValues(c) / N_total

Purpose:
Measures the degree to which an attribute contains distinct values.

Use:
Helps determine whether an attribute may provide useful identity,
categorization, or graph structure.
### FORMULA M1-F04 — MULTI-FACTOR GRAPH READINESS SCORE

Classification:
PROJECT-DERIVED

Name:
Graph Readiness Score (GRS)

Purpose:
Combines dataset-quality characteristics to assess whether an
attribute is suitable for graph-oriented processing.
### IMPORTANT
The exact weighting and implementation must be taken from the
current dataset_statistics.py implementation rather than assumed
from generic graph literature.

Source:
module_1_dataset_validation/dataset_statistics.py
### ALGORITHM M1-A01 — AUTOMATED DATASET PROFILING

Type:
STANDARD + PROJECT IMPLEMENTATION

Process:
## 1. Discover dataset files.
## 2. Read dataset metadata.
## 3. Inspect columns.
## 4. Calculate missingness.
## 5. Calculate uniqueness/cardinality.
## 6. Inspect data types.
## 7. Calculate graph-readiness indicators.
## 8. Produce validation outputs.

Primary source:
module_1_dataset_validation/dataset_validation.py

Supporting source:
module_1_dataset_validation/dataset_statistics.py
## 4. MODULE 2 — GRAPH FEATURE & RELATIONSHIP ENGINEERING
### PURPOSE
-------
Module 2 converts telemetry attributes into graph-oriented
representations.

Main concepts:
- Entity identification
- Feature-role classification
- Entity feature vectors
- Candidate relationship discovery
- Relationship confidence
- Graph-ready attribute mapping
### FORMULA M2-F01 — ENTITY FEATURE VECTOR

Representation:
`
F(e) = [f1, f2, ..., fn]`

Where:

e  = graph entity
fi = feature associated with entity e

Purpose:
Represents an entity using its available graph-relevant attributes.

Source:
feature_engineering.py
### FORMULA M2-F02 — RELATIONSHIP CONFIDENCE

Representation:
`
C(r) ∈ [0, 1]`

Where:

r = candidate relationship
C(r) = confidence assigned to the relationship
### IMPORTANT
The exact confidence calculation must follow the implemented
relationship_extraction.py logic.

Do NOT interpret confidence as probability unless the implementation
explicitly defines it as such.
### ALGORITHM M2-A01 — TELEMETRY ENTITY EXTRACTION

Type:
HEURISTIC

Purpose:
Identifies candidate graph entities from telemetry attributes.

Process:
## 1. Inspect available dataset attributes.
## 2. Identify attributes representing entity identity or association.
## 3. Map attributes to entity candidates.
## 4. Normalize relevant representations.
## 5. Produce graph-oriented entity mappings.

Source:
entity_extraction.py
### ALGORITHM M2-A02 — CANDIDATE LINK INFERENCE

Type:
HEURISTIC

Purpose:
Identifies possible relationships between graph entities.

Process:
## 1. Identify source entity.
## 2. Identify target entity.
## 3. Inspect supporting telemetry attributes.
## 4. Evaluate relationship evidence.
## 5. Assign relationship confidence.
## 6. Classify relationship evidence.

Important distinction:
### OBSERVED
    Directly supported by telemetry.
### DERIVED
    Calculated from telemetry.
### INFERRED
    Suggested from available evidence.

UNSUPPORTED
    Not sufficiently supported.
### ALGORITHM M2-A03 — FEATURE ROLE CLASSIFICATION

Type:
HEURISTIC

Purpose:
Assigns dataset attributes to graph-oriented functional roles.

Possible roles include:
- Graph Entity
- Graph Node Property
- Relationship Property
- Temporal Attribute
- Event Attribute
- Context Attribute
- Security Attribute
- Derived Feature
- Metadata Attribute
- Truly Ignored

Source:
feature_mapping.py
## 5. MODULE 3 — GRAPH SCHEMA
### PURPOSE
-------
Module 3 converts the engineered graph representation into a
structured graph schema.

Main components:
- Node types
- Node properties
- Relationship types
- Relationship properties
- Primary keys
- Cardinality
- Referential integrity
### FORMULA M3-F01 — NODE PRIMARY KEY DERIVATION

Purpose:
Determines the identifier used to represent a graph node.

The actual key-selection rule is defined by:

node_schema.py

Function:

derive_primary_key_for_node()
### FORMULA M3-F02 — EDGE CARDINALITY INFERENCE

Purpose:
Determines the expected relationship cardinality between source
and target entities.

Examples:

1:1
1:N
N:1
N:N

Source:

edge_schema.py

Function:

derive_edge_cardinality()
### FORMULA M3-F03 — REFERENTIAL INTEGRITY

Conceptual representation:
`
ReferentialIntegrity =`
    ValidReferences / TotalReferences × 100

Purpose:
Measures whether graph relationships reference valid entities.
### IMPORTANT
Use the exact implementation in schema_validator.py as the
authoritative implementation.
### ALGORITHM M3-A01 — DATA-DRIVEN SCHEMA CONSTRUCTION

Type:
PROJECT-DERIVED

Process:
## 1. Read Module 2 entity mappings.
## 2. Identify candidate node types.
## 3. Determine node properties.
## 4. Identify candidate relationships.
## 5. Determine relationship properties.
## 6. Derive identifiers.
## 7. Infer relationship cardinality.
## 8. Generate schema representation.
## 9. Validate schema consistency.

Source:

schema_builder.py
### ALGORITHM M3-A02 — GRAPH SCHEMA VALIDATION

Purpose:
Checks structural consistency of the generated graph schema.

Checks include:
- Node definitions
- Edge definitions
- Identifiers
- References
- Relationship endpoints
- Schema consistency

Source:

schema_validator.py
## 6. MODULE 4 — TEMPORAL KNOWLEDGE GRAPH
### PURPOSE
-------
Module 4 introduces temporal information into graph representation
by transforming timestamped telemetry/events into ordered temporal
graph structures.
### FORMULA M4-F01 — TIMESTAMP REPRESENTATION

Representation:

t_i = timestamp associated with event i

The implementation converts timestamp information into a usable
temporal representation.

Source:

event_ordering.py
parse_timestamp()
### FORMULA M4-F02 — INTER-EVENT TEMPORAL DELTA

Formula:
`
Δt = t_j - t_i`

Where:

t_i = timestamp of earlier event
t_j = timestamp of later event
Δt  = elapsed time between events

Purpose:
Represents the temporal distance between events.
### FORMULA M4-F03 — RELATIVE TIME

Representation:
`
relative_time =`
    event_time - reference_time

Purpose:
Normalizes event timing relative to a selected sequence/session
reference.
### FORMULA M4-F04 — CUMULATIVE ELAPSED TIME

Representation:
`
T_k = Σ Δt_i`

for the ordered events in a temporal sequence.

Purpose:
Represents elapsed time across a sequence of events.
### ALGORITHM M4-A01 — TEMPORAL EVENT SEQUENCE BUILDER

Type:
STANDARD / PROJECT IMPLEMENTATION

Process:
## 1. Read event records.
## 2. Parse timestamps.
## 3. Group events according to the implemented session/entity logic.
## 4. Sort events chronologically.
## 5. Calculate temporal differences.
## 6. Construct ordered event sequences.

Source:

event_ordering.py
### ALGORITHM M4-A02 — PRECEDES EDGE GENERATION

Type:
PROJECT-DERIVED

Purpose:
Connects temporally ordered events.

Concept:

Event_i
   |
   | PRECEDES
   v
Event_j

where:

t_i < t_j

Purpose:
Represents temporal ordering inside the TKG.
### ALGORITHM M4-A03 — TEMPORAL KNOWLEDGE GRAPH BUILDER

Type:
PROJECT-DERIVED

Process:
## 1. Load temporal event data.
## 2. Instantiate temporal nodes.
## 3. Assign unique node identifiers.
## 4. Attach event attributes.
## 5. Create graph relationships.
## 6. Add temporal ordering relationships.
## 7. Export temporal graph structures.

Source:

temporal_graph_builder.py
### ALGORITHM M4-A04 — NODE INSTANCE UNIQUE ID GENERATION

Purpose:
Creates unique identifiers for instantiated temporal event nodes.

This allows multiple observations of the same conceptual entity to
remain distinguishable as separate temporal instances.
## 7. GRAPH ANALYTICS
### CURRENTLY DOCUMENTED / IMPLEMENTED METHODS
The current formulation record identifies:
- Degree Centrality
- PageRank
- Basic graph traversal
- Heuristic compromise-path traversal

These must always be interpreted according to the current repository
implementation.
### ALGORITHM G-A01 — DEGREE CENTRALITY

Purpose:
Identifies highly connected nodes.

For a node v:
`
Degree(v) =`
    Number of edges incident to v

For normalized degree centrality:
`
C_D(v) =`
    degree(v) / (|V| - 1)

Use:
Identifying potential graph hubs or highly connected entities.
### ALGORITHM G-A02 — PAGERANK

Type:
STANDARD

Purpose:
Ranks nodes according to structural importance in the graph.

Conceptual formulation:
`
PR(v) =`
    (1-d)/N
    +
    d × Σ [PR(u) / L(u)]

Where:
`
PR(v) = PageRank of node v`
d     = damping factor
N     = number of nodes
u     = node linking to v
L(u)  = outgoing links from u
### IMPORTANT
The exact implementation/configuration in the project code is the
authoritative version.
### ALGORITHM G-A03 — TEMPORAL / GRAPH PATH TRAVERSAL

Purpose:
Traverses connected graph events to identify possible sequences.

The current implementation should be treated as a graph traversal /
heuristic analysis mechanism rather than a complete automated
multi-stage attack reconstruction framework unless additional code
supports that claim.
## 8. OBSERVED / DERIVED / INFERRED SEPARATION

This distinction is critical for research integrity.
### OBSERVED
--------
Directly available in the telemetry.

Example:
IP address appearing in a network-flow record.
### DERIVED
-------
Calculated directly from observed data.

Example:
`Δt = t_j - t_i`
### INFERRED
--------
A relationship or attribute suggested using evidence/rules.

Example:

Host_A
   |
   | communicates_with [INFERRED]
   v
Host_B
### PLANNED
-------
A future method that has not been implemented.

Never present a PLANNED method as an experimental result.
## 9. COMPLETE METHOD INDEX

| ID     | Method                               | Module   | Status           |
| M1-F01 | Missing Value Rate                   | M1       | IMPLEMENTED      |
| M1-F02 | Attribute Completeness               | M1       | IMPLEMENTED      |
| M1-F03 | Column Uniqueness Ratio              | M1       | IMPLEMENTED      |
| M1-F04 | Graph Readiness Score                | M1       | IMPLEMENTED      |
| M2-F01 | Entity Feature Vector                | M2       | IMPLEMENTED      |
| M2-F02 | Relationship Confidence              | M2       | IMPLEMENTED      |
| M3-F01 | Node Primary Key Derivation          | M3       | IMPLEMENTED      |
| M3-F02 | Edge Cardinality Inference           | M3       | IMPLEMENTED      |
| M3-F03 | Referential Integrity                | M3       | IMPLEMENTED      |
| M4-F01 | Timestamp Parsing                    | M4       | IMPLEMENTED      |
| M4-F02 | Inter-Event Delta                    | M4       | IMPLEMENTED      |
| M4-F03 | Relative Time                        | M4       | IMPLEMENTED      |
| M4-F04 | Cumulative Elapsed Time              | M4       | IMPLEMENTED      |
| G-F01  | Degree Centrality                    | M4       | IMPLEMENTED      |
| G-F02  | PageRank                             | M4       | IMPLEMENTED      |
## 10. COMPLETE ALGORITHM INDEX

| ID       | Algorithm                             | Module   | Type        |
| M1-A01   | Automated Dataset Profiling           | M1       | STANDARD    |
| M2-A01   | Telemetry Entity Extraction           | M2       | HEURISTIC   |
| M2-A02   | Candidate Link Inference              | M2       | HEURISTIC   |
| M2-A03   | Feature Role Classification           | M2       | HEURISTIC   |
| M3-A01   | Data-Driven Schema Construction       | M3       | DERIVED     |
| M3-A02   | Graph Schema Validation               | M3       | STANDARD    |
| M4-A01   | Temporal Event Sequence Builder       | M4       | STANDARD    |
| M4-A02   | PRECEDES Edge Generation              | M4       | DERIVED     |
| M4-A03   | Temporal Knowledge Graph Builder      | M4       | DERIVED     |
| M4-A04   | Temporal Node ID Generation           | M4       | DERIVED     |
| G-A01    | Degree Centrality                     | M4       | STANDARD    |
| G-A02    | PageRank                              | M4       | STANDARD    |
| G-A03    | Graph/Path Traversal                  | M4       | HEURISTIC   |
## 11. METHODS NOT TO BE CLAIMED WITHOUT IMPLEMENTATION EVIDENCE

The following methods must NOT automatically be described as
implemented merely because they appear in research literature,
architecture documents, or future plans:
- Graph Neural Networks
- GraphSAGE
- Graph Attention Networks
- Louvain / Leiden community detection
- Neo4j-based graph analytics
- Yen's K-shortest paths
- Advanced attack-path simulation
- Automated multi-stage attack reconstruction
- LLM-based graph link inference
- External vulnerability intelligence integration
- EPSS/KEV-based risk scoring
- CVE/CWE/CAPEC relationships
- riskWeight
- pExploit
- attackCost
- controlStrength

If any of these are introduced later, they must receive a new
implementation entry and source-file reference.
## 12. TRACEABILITY REQUIREMENT

Every important formulation/algorithm should be traceable through:

DATA
  ↓
MODULE
  ↓
PYTHON FILE
  ↓
FUNCTION / CLASS
  ↓
FORMULATION / ALGORITHM
  ↓
OUTPUT
  ↓
VISUALIZATION


Example:

Telemetry
   ↓
Module 4
   ↓
event_ordering.py
   ↓
build_temporal_ordering_edges()
   ↓
Δt calculation
   ↓
temporal_edges.csv
   ↓
Temporal visualization
## 13. RESEARCH INTEGRITY RULES
## 1. Do not call a heuristic a machine-learning model.
## 2. Do not call an inferred relationship an observed relationship.
## 3. Do not call a standard algorithm novel.
## 4. Do not claim an algorithm is implemented without code evidence.
## 5. Do not introduce unsupported cybersecurity relationships.
## 6. Do not assign risk scores without a defensible source/formulation.
## 7. Keep observed, derived, inferred, and planned information separate.
## 8. Every future method must be clearly labelled as future work.
## 9. Every project-derived formulation must identify its source.
## 10. Every important result should remain reproducible from the
    corresponding input and implementation.
## 14. PURPOSE IN THE OVERALL CORTEX PIPELINE

The Adaptive Graph Intelligence pipeline progressively transforms
telemetry into graph-oriented intelligence:

RAW TELEMETRY
      |
      v
MODULE 1
Dataset Validation
      |
      v
MODULE 2
Feature / Entity / Relationship Engineering
      |
      v
MODULE 3
Graph Schema
      |
      v
MODULE 4
Temporal Knowledge Graph
      |
      v
Graph Analytics / Temporal Investigation


The main research value of the pipeline is the transformation of
heterogeneous telemetry into structured, connected, and temporally
ordered graph information that can support later cybersecurity
reasoning.
## 15. DOCUMENT MAINTENANCE

When the implementation changes:
## 1. Update the affected formulation.
## 2. Update the algorithm entry.
## 3. Update the source-file reference.
## 4. Update implementation status.
## 5. Update the method index.
## 6. Verify downstream dependencies.
## 7. Keep planned and implemented methods separate.

This document should therefore be treated as the technical
mathematical/algorithmic reference for the Adaptive Graph Intelligence
repository.


END OF DOCUMENT
