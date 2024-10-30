---
title: Home
layout: home
date: 2024-10-24
tags: [Mermaid]
mermaid: true
nav_order: 1
---

# {{ page.title }}

Documentation, templates, and tools for Cascade IMPAc-TB

- [{{ page.title }}](#-pagetitle-)
  - [About the Consortium and Studies](#about-the-consortium-and-studies)
  - [Templates](#templates)
  - [Metadata](#metadata)
    - [Connections](#connections)
    - [Legends](#legends)
      - [Cardinality](#cardinality)
      - [Aliases](#aliases)
  - [Repository Connections](#repository-connections)
  - [Assay Designations](#assay-designations)
  - [Data Designation by Species](#data-designation-by-species)
  - [Tools](#tools)

---  

## About the Consortium and Studies

- [NIH IMPAc-TB Site](https://www.niaid.nih.gov/research/immune-mechanisms-protection-mycobacterium-tuberculosis)
- [NIH Reporter Site](https://reporter.nih.gov/search/FfF_w27C_0GdNzBGP7gk1w/project-details/11179082)
- [Cascade Immune Mechanisms of Protection against Mycobacterium tuberculosis (IMPAc-TB): study protocol for the Household Contact Study in the Western Cape, South Africa](https://pmc.ncbi.nlm.nih.gov/articles/PMC9012070/#_ad93_)

## Templates

Can be found at [Github Repository](https://github.com/nlee-scri/Cascade-IMPAc-TB-Data-Submission-Tools/tree/main/templates) under templates. Contains information needed to upload to public repositories.

There are excel templates that are the most useful. Since the main repository is ImmPort, I recomend using [`templates/immport/study_composite_template.xlsx`](https://github.com/nlee-scri/Cascade-IMPAc-TB-Data-Submission-Tools/blob/main/templates/repositories/immport/study_composite_template.xlsx)

## Metadata

Main types of metadata needed

- Study
- Protocols
- Subjects
- Samples
- Experiments
- Results

### Connections

How the metadata is connected for people to find the information.
The main way is primary keys (PK) are used as foreign keys (FK) in the other tables.

When the data is collated it shoud look like this as a table:

| studyId  | protocol ID | Arm ID  | Subject ID | Biospecimen ID | Assay    | Results |
| -------- | ----------- | ------- | ---------- | -------------- | -------- | ------- |
| study_01 | RNAseq_01   | control | subject_01 | biospecimen_01 | assay_01 | ...     |
| study_01 | RNAseq_01   | control | subject_01 | biospecimen_01 | assay_01 | ...     |
| study_01 | RNAseq_01   | control | subject_01 | biospecimen_01 | assay_01 | ...     |

```mermaid
  erDiagram

  Study {
      string studyId PK
      string title
      string studyDescription
  }

  Protocols {
      string protocolId PK
      string studyId FK
      string Description
  }

  Arm {
      string armId PK
      string studyId FK
  }

  Subject {
      string subjectId PK
      string studyId FK
      string armId FK
      string species
  }

  Biospecimen {
      string biospecimenId PK
      string individualId PK
  }

  Assay {
      string biospecimenId PK
      string AssayName
      string protocols
  }

  Study ||--|{ Subject : has
  Study ||--|{ Protocols : has
  Study ||--o{ Arm : has
  Arm ||--|{ Subject : has
  Subject ||--|{ Biospecimen : has
  Biospecimen ||--|{ Assay : has
```

### Legends

| Abbreviation | Full        | Description                |
| ------------ | ----------- | -------------------------- |
| PK           | Primary Key | Unique values              |
| FK           | Foreign Key | Does not have to be unique |

#### Cardinality

| Value (left) | Value (right) | Meaning                       |
| ------------ | ------------- | ----------------------------- |
| \|o          | o\|           | Zero or one                   |
| \|\|         | \|\|          | Exactly one                   |
| \}o          | o\{           | Zero or more (no upper limit) |
| \}\|         | \|\{          | One or more (no upper limit)  |

#### Aliases

| Value (left) | Value (right) | Alias for    |
| ------------ | ------------- | ------------ |
| one or zero  | one or zero   | Zero or one  |
| zero or one  | zero or one   | Zero or one  |
| one or more  | one or more   | One or more  |
| one or many  | one or many   | One or more  |
| many(1)      | many(1)       | One or more  |
| 1+           | 1+            | One or more  |
| zero or more | zero or more  | Zero or more |
| zero or many | zero or many  | Zero or more |
| many(0)      | many(0)       | Zero or more |
| 0+           | 0+            | Zero or more |
| only one     | only one      | Exactly one  |
| 1            | 1             | Exactly one  |

 ---  

## Repository Connections

```mermaid
classDiagram
    %% repositories
    class ImmPort{
        + Public Repository
        + Accession ID or DOI
    }
    class GEO {
        Entity Name
        Accession ID
    }
    class Zenodo {
        Entity Name
        DOI
    }
    class SRA {
        Entity Name
        Accession ID
    }
    class `Cell Image Archive` {
        Entity Name
        Accession ID
    }
    class dbGaP {
        Entity Name
        Accession ID
    }

    ImmPort <|-- GEO
    ImmPort <|-- SRA
    ImmPort <|-- `Cell Image Archive`
    ImmPort <|-- Zenodo
    ImmPort <|-- dbGaP
```
  ---  

## Assay Designations

Where assays go by type

```mermaid
  flowchart LR

  %% repositories
  I[(<a href="https://docs.immport.org/datasubmission/general/">ImmPort</a>)]
  G[(<a href="https://www.ncbi.nlm.nih.gov/geo/info/submission.html">GEO</a>)]
  Z[(<a href="https://zenodo.org/">Zenodo</a>)]
  SRA[(<a href="https://www.ncbi.nlm.nih.gov/sra">Sequence Read Archive</a>)]
  IDB[(<a href="https://www.cellimagelibrary.org/home">Cell Image Library</a>)]
  D[(<a href="https://www.ncbi.nlm.nih.gov/gap/">dbGAP</a>)]

  %% assays
  ST[Spatial Transcriptomics]
  RS["(sc)RNAseq"]
  AS[ATAC-seq]
  P[Phenotyping]
  M[Microscopy]
  FC[Flow Cytometry]
  GWAS
  PCR
  CFU

  %% where does the data go?

  ST --> Z
  RS --> G
  CFU --> D
  GWAS --> D
  P --> D
  AS --> SRA
  PCR --> I
  FC --> I
  M --> IDB
```
  ---  

## Data Designation by Species

Due to human data needing to be under controled access if personal information can not be removed from the files, the data must be placed in *controled access* data repositories.

Model organism data can be placed in public repositories freely.

```mermaid
    flowchart LR
    %% species
    H[Human]
    M[Mouse]

    H --> dbGap
    H -->  Zenodo

    M --> Any

```

## [Tools](tools/README.md)

Python package to:

- Scraping metadata from files such as `.fcs`, `.ims`
- Validating and uploading data to public repositories
