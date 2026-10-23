# Semantic Construction Dashboard

**Semantic-driven visualization of construction project information across time, cost, resources, and BIM geometry**

## Overview

This repository accompanies the research article **“Semantic-Driven Data Visualization for Construction Management: An Interactive Dashboard for Cross-Domain Project Information Exploration.”** It provides supporting materials for a proof-of-concept workflow that combines RDF-based construction project information, predefined SPARQL queries, and interactive Power BI dashboard views.

The purpose of the prototype is to make selected relationships across scheduling activities, cost items, construction resources, and BIM elements available through visual interfaces. The RDF knowledge graph acts as the semantic backend; the Power BI dashboard acts as a user-oriented frontend for exploring query results without requiring users to write SPARQL or navigate the graph directly.

**Research scope:** The technical contribution is the systematic organization of five cross-domain query perspectives and the mapping of their outputs to corresponding dashboard views. The prototype is intended for **information retrieval and visual exploration**, not automated consistency validation.

## Architecture

The workflow described in the study comprises the following stages:

1. **Semantic representation.** Project data originating from IFC building models, construction schedules, and cost information are represented as RDF using existing domain ontologies and conversion processes developed in earlier research.
2. **Cross-domain linking.** Explicit RDF assignment relationships are **prepared manually** to connect tasks, cost items, resources, and geometric elements in the integrated knowledge graph. These links are not discovered automatically.
3. **SPARQL querying.** Five predefined queries retrieve selected cross-domain relationships from the graph.
4. **Data transfer.** In the demonstrated implementation, a Python-based process executes the queries and exports tabular results to CSV files. **Power BI does not directly query the RDF store.**
5. **Visualization.** Power BI reads the exported tables and represents their contents through interactive dashboard views. Updated CSV files can be reflected in the dashboard after refreshing its data sources.

**Simplified flow:**

`IFC / schedule / cost sources → RDF datasets + manually prepared assignments → knowledge graph → SPARQL queries → CSV exports → Power BI dashboard`

The sample RDF dataset made available with the research package is intended to allow reproduction of the workflow **starting from the prepared semantic data**. Reproduction of the original native-file-to-RDF conversion process requires the separate conversion tools and source files used in the earlier research.

## Five query perspectives

| Query | Cross-domain perspective | Information retrieved | Corresponding dashboard view |
|---|---|---|---|
| **Q1** | Activity–Resource | Resources assigned to scheduling activities and their semantic types | Activity–Resource |
| **Q2** | Activity–Geometry | BIM elements associated with scheduling activities, including available IFC `GlobalId` values | Activity–Geometry |
| **Q3** | Activity–Cost | Cost items explicitly associated with scheduling activities | Activity–Cost |
| **Q4** | Cost–Resource | Resources associated with cost items and their semantic types | Cost–Resource |
| **Q5** | Cost–Geometry | BIM elements associated with cost items, including available IFC `GlobalId` values | Cost–Geometry |

The queries are **retrieval-oriented**. Some queries use optional patterns to retain activities without particular associations; this does not constitute automated identification or classification of constraint violations.

## Repository contents

The research release is intended to contain the following files. **Verify that each file is present and that its name matches the published version before archiving a release.**

```text
semantic-construction-dashboard/
├── README.md
├── data/
│   └── sample_dataset.ttl
├── queries/
│   ├── Q1_activity_resource.rq
│   ├── Q2_activity_geometry.rq
│   ├── Q3_activity_cost.rq
│   ├── Q4_cost_resource.rq
│   └── Q5_cost_geometry.rq
└── dashboard/
    └── semantic_construction_dashboard.pbit
```

- **`data/`** — An anonymized RDF sample with the entities and manually prepared cross-domain assignments necessary to run the published queries.
- **`queries/`** — The five SPARQL 1.1 query files corresponding to the dashboard perspectives.
- **`dashboard/`** — A Power BI template (`.pbit`) containing the dashboard layout and data model, without the original project's confidential data.

Any additional scripts or documentation included in a release should be described here and have their execution requirements documented.

## Reproducing the demonstration

### Requirements

- An RDF environment that supports **SPARQL 1.1** queries and can load Turtle (`.ttl`) files (for example, a compatible local triple store).
- A way to export SPARQL `SELECT` results to CSV while preserving the query variable names as column headers.
- **Microsoft Power BI Desktop** to open the `.pbit` template.
- **Python**, only if using the automated SPARQL-to-CSV extraction procedure described in the paper or an accompanying script.

### Steps

1. **Obtain the research package.** Clone or download this repository, and locate the RDF sample, five queries, and Power BI template.
2. **Load the RDF data.** Import `data/sample_dataset.ttl` into a SPARQL-capable RDF environment. Ensure that all required triples, including the manually prepared cross-domain assignment instances, are loaded.
3. **Run Q1–Q5.** Execute the `.rq` files against the loaded graph. Preserve the namespace IRIs used in the dataset: RDF identifiers using `http://` and `https://` are not automatically interchangeable.
4. **Export the results.** Save each query's tabular result as a CSV file. Preserve column names, value types, and the IFC `GlobalId` strings where returned. The Power BI template must be configured to read the resulting tables.
5. **Connect the Power BI template.** Open `dashboard/semantic_construction_dashboard.pbit` in Power BI Desktop. If prompted, set or update the CSV file locations in the template's parameters or Power Query data-source settings. Ensure that each query output is connected to its corresponding dataset/view.
6. **Refresh and inspect.** Refresh the Power BI data model and inspect the five dashboard views. The dashboard displays **query results from the prepared dataset**, rather than dynamically querying the knowledge graph.

**Implementation note:** The paper describes an automated Python-to-CSV extraction bridge. If that bridge is not included in the published package, Steps 3–4 can be performed using a SPARQL-capable application with CSV export. In that case, the sample reproduces the query outputs and dashboard views, **not the automated export schedule**. For full pipeline replication, the extraction script and the exact CSV-to-Power-BI source mappings must also be released.

## Dataset and reproducibility scope

The manuscript's case-study demonstration used **90 structural BIM elements, 29 scheduling activities, and 4 cost items**. The publicly distributed RDF sample may contain a different number of instances because of anonymization and disclosure restrictions. Results produced using a reduced or modified sample should not be assumed to match the quantitative summaries reported in the article.

Cross-domain relationships are represented using explicit assignment instances, including:

- `cr:ResourceToTaskAssignment`
- `cr:TaskToGeometryAssignment`
- `cr:TaskToCostItemAssignment`
- `cr:ResourceToCostItemAssignment`
- `cr:CostItemToGeometryAssignment`

The demonstration builds on existing ontologies, including the Construction Resources (`cr:`), Cost Item (`ci:`), Digital Twin Construction (`dtc:`), and ifcOWL (`ifc:`) vocabularies. The query prefix declarations must be consistent with the exact identifiers in the distributed RDF files.

## Limitations

- Cross-domain assignments are **manually created**, not produced by automated entity matching or reasoning.
- The SPARQL queries **retrieve and structure information**; they do not implement SHACL-based, rule-based, temporal, or quantitative consistency validation.
- The dashboard provides visual exploration of the retrieved results. No controlled user study established reduced cognitive workload, improved decision-making efficiency, or superior usability.
- The demonstration is based on a limited case study and does not establish scalability or performance on large industrial datasets.
- The system is **snapshot-based** and does not include live sensor streams or continuous graph-to-dashboard querying.

## Related publication and prior research

This repository supports the journal study titled:

> *Semantic-Driven Data Visualization for Construction Management: An Interactive Dashboard for Cross-Domain Project Information Exploration.*

The study extends a DFBI 2025 conference contribution by **Cassandro and Mirarchi (2026, pp. 509–518)**. The journal study's incremental technical contribution is limited to the five structured query perspectives and their corresponding dashboard mappings. The ontologies, broader semantic integration approach, and general concept of dashboard-based visualization originate from earlier work.

**Publication citation:** Add the final journal bibliographic reference and DOI when available.

## Data availability and permanent archive

**GitHub repository:** Add the public repository URL after publication.

**Permanent archive (Zenodo DOI):** Add the verified DOI after a release is archived and publicly accessible.

For the journal's Data Availability Statement, reference the permanent archived release and verify that it contains the RDF sample, all five SPARQL query files, the `.pbit` template, and sufficient instructions to reproduce the demonstrated views.

## License and contact

**License:** To be specified by the repository maintainers. Do not assume that the underlying project data, ontologies, and source code all have the same reuse permissions.

**Research authors:** Jacopo Cassandro and Claudio Mirarchi, Politecnico di Milano.

For scholarly questions, please use the contact information provided in the associated publication or the repository's issue tracker if enabled.
