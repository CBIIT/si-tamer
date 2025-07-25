_This repo is under construction._

# SI Tamer

SI (Semantic Infrastructure) Tamer is a developing set of Retrieval Augmented Generation (RAG) tools for querying a custom graph-based vector store that aggregates data elements from the NCI Cancer Data Standards Repository ([caDSR](https://cadsr.cancer.gov)) and the NCI Thesaurus ([NCIT](https://evsexplore.semantics.cancer.gov)).

The tools are based on [LangChain](https://python.langchain.com), including Neo4j vendor-contributed "[GraphRAG](https://neo4j.com/docs/neo4j-graphrag-python/current/index.html)" components.

## Rationale and Purpose

Cancer data submissions to large repositories come from diverse projects and institutions. Such data may be created under different standards, or include idiosyncratic, project-specific values and variables. Preparing such data for inclusion in a [FAIR](https://fairsharing.org/) repository often requires mapping and matching incoming data to equivalent concepts and terminologies into the broad NCI standards represented by caDSR and NCIT. These resources are large (caDSR includes over 70k active common data elements (CDEs), NCIT defines over 100k concepts) and unwieldly to search manually.

The SI Tamer toolkit together with a local graph database of caDSR and NCIt entities are intended to provide a modular foundation for automated and AI-driven [semantic search](https://cloud.google.com/discover/what-is-semantic-search?hl=en) across NCI's semantic infrastructure content. SI Tamer will be suitable for building semantic search components that can be incorporated into so-called "schema matching" packages such as [BDIKit](https://bdi-kit.readthedocs.io/stable/) and SME-friendly user interfaces.

## Graph Vector Store

We use [Neo4j](https://neo4j.com) (Community Edition v5.20) as the basis for our store. Serialized representations of [caDSR](https://cadsr.nci.nih.gov/ftp/caDSR_Downloads/CDE/XML/) and [NCIt](https://evs.nci.nih.gov/ftp1/NCI_Thesaurus/) are extracted and loaded.

The knowledge structure of the graph is under development. Currently it preserves the caDSR entities that are created and linked to each Common Data Element according to the [ISO 1179 metadata standard](https://wiki.nci.nih.gov/spaces/caDSR/pages/10861397/caDSR+and+ISO+11179). CDEs as well as Value Domains and Permissible Values are present and vector-searchable in the database. NCIT Concepts ([OWL](https://linkeddatatools.com/introducing-rdfs-owl/) classes) are present and linked with caDSR entities that reference them.

![SI Tamer graph schema](/schema.png)

|Node Label | Entity Name | Vector Index Name |
|---|---|---|
| CDE | Common Data Element | `cdeIndex` |
| DEC | Data Element Concept | - |
| NCIT | NCI Thesaurus Concept | `ncitIndex` |
| OC | Object Class | - |
| PR | Property (Attribute) | - |
| PV | Permissible Value | `pvIndex` |
| VDM | Value Domain | `vdmIndex` |
| SYN | NCI Metathesaurus Synonym | - |

Each of these node types also has node label `:Term`. Term nodes may have the 
following properties:

|Property|Description|
|---|---|
|term|Title, term, or value|
|code|ID within the source terminology (e.g., CDE ID, Concept Code|
|version|Version number within the source terminology|
|definition|Definition (if provided)|
|openai_embedding|Vector embedding (`text-embedding-ada-002`)of definition|
|context|caDSR context or synonym source terminology|
|md5|MD5 hash of definition text|
|id|MD5 hash of definition text (nodes with embedding properties)|


ELT scripts for building the database are found in [/python/data_utils](/python/data_utils).
