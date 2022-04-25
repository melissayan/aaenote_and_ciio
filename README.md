---
# Adverse Event NOte TErminology (AAENOTE) & Catheter Infection Indications Ontology (CIIO)
---

This repository contains the terminology, ontology, competency questions, and SPARQL queries for the manuscript submitted to the [Semantic Web Journal](http://www.semantic-web-journal.net/):
```
@ARTICLE{yan2022,
author={Yan, Melissa Y. and Gustad, Lise Tuset and Høvik, Lise Husby and Nytrø, Øystein},
title={Terminology and Ontology Development for Semantic Annotation: A Use Case on Sepsis and Adverse Events},
year={2022}
}
```


# Terminology and Ontology Information 

## Terminology - AAENOTE
- Annotated Adverse Event NOte TErminology (AAENOTE) or Annotert AvviksmEldingsNOtat TErminologi in Norwegian.
- The terminology used by annotators to annotate an adverse event document and it provides an index for annotated document.
- Detailed specifications in [English](https://folk.ntnu.no/melissay/ontology/aaenote/index-en.html) or [Norwegian](https://folk.ntnu.no/melissay/ontology/aaenote/index-no.html)

## Ontology - CIIO 
- Catheter Infection Indications Ontology (CIIO) or Kateter InfeksjonsIndikasjoner Ontologi in Norwegian.
- The ontology which accompanies AANOTE by representing clinical knowledge needed to infer indications of catheters and infections. 
- Detailed specifications in [English](https://folk.ntnu.no/melissay/ontology/ciio/index-en.html) or [Norwegian](https://folk.ntnu.no/melissay/ontology/ciio/index-no.html)

## Additional Information in the [Wiki](https://github.com/melissayan/aaenote_and_ciio/wiki) 
The [Wiki](https://github.com/melissayan/aaenote_and_ciio/wiki) contains:
- [a description of AAENOTE and CIIO](https://github.com/melissayan/aaenote_and_ciio/wiki)
- [the use case, objective, and competency questions](https://github.com/melissayan/aaenote_and_ciio/wiki/Use-Case,-Objective,-and-Competency-Questions)
- SPARQL queries for [AAENOTE](https://github.com/melissayan/aaenote_and_ciio/wiki/Terminology-SPARQL-Queries) and [CIIO](https://github.com/melissayan/aaenote_and_ciio/wiki/Ontology-SPARQL-Queries)
- [instructions on how to import annotated labels as individuals into AAENOTE](https://github.com/melissayan/aaenote_and_ciio/wiki/Import-Individuals-for-Terminology)
- [clinical knowledge used in CIIO and SPARQL queries](https://github.com/melissayan/aaenote_and_ciio/wiki/Catheter-Infection-Indications-Ontology-(CIIO)-Clinical-Knowledge).



# Software Utilized
The following software was used to develop and present the terminology and ontology:

| Software                                                                                                                | Purpose                                                         |
| ----------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| [Protégé version 5.5.0](https://github.com/protegeproject/protege)                                                      | Terminology and ontology development                            |
| Protégé's [SPARQL Query Tab (SPARQL Query Plugin version 3.0.0)](https://github.com/protegeproject/sparql-query-plugin) | SPARQL queries                                                  |
| Protégé's [Cellfie Plugin (Protege 5.0+ Plugin version 2.1.0)](https://github.com/protegeproject/cellfie-plugin)        | Importing individuals into the Terminology                      |
| [WIDOCO](https://github.com/dgarijo/Widoco)                                                                             | Generating HTML specifications for the terminology and ontology | 


# Usage
 1. Download the following files 
 ```
.
├── aaenote-individuals.owl
├── aaenote.owl
├── cellfie_create_axioms
│   ├── Indv_sheets1-7.3.json
│   └── import_AE_annotated.xlsx
└── ciio.owl
 ```
2. To view the terminology, open ```aaenote.owl``` with Protégé.  To view individuals for AAENOTE, choose a or b: 
    - a. open ```aaenote-individuals.owl``` with Protégé and import ```aaenote.owl```.
    - b. populate individuals using files in ```cellfie_create_axioms``` and follow the [Wiki instructions](https://github.com/melissayan/aaenote_and_ciio/wiki/Import-Individuals-for-Terminology)
3. Use Protégé's SPARQL Query Tab to answer the [competency questions](https://github.com/melissayan/aaenote_and_ciio/wiki/Use-Case,-Objective,-and-Competency-Questions) using [SPARQL queries for the terminology](https://github.com/melissayan/aaenote_and_ciio/wiki/Terminology-SPARQL-Queries). 
4. To view the ontology, open ```ciio.owl``` with Protégé. 
5. Use Protégé's SPARQL Query Tab to answer the [competency questions](https://github.com/melissayan/aaenote_and_ciio/wiki/Use-Case,-Objective,-and-Competency-Questions) using [SPARQL queries for the ontology](https://github.com/melissayan/aaenote_and_ciio/wiki/Ontology-SPARQL-Queries).  


# Extra Files
Wiki markdown files: 
```
.
└── aaenote_and_ciio.wiki
   ├── Catheter-Infection-Indications-Ontology-(CIIO)-Clinical-Knowledge.md
   ├── Home.md
   ├── Import-Individuals-for-Terminology.md
   ├── Ontology-SPARQL-Queries.md
   ├── Terminology-SPARQL-Queries.md
   ├── Use-Case,-Objective,-and-Competency-Questions.md
   └── _Sidebar.md
```
Where to find files used to generate HTML specifications for AAENOTE and CIIO: 
```
.
└── html_specs
    ├── aaenote
    └── ciio
```


# Acknowledgements
- The authors would like to acknowledge the eight annotators for producing the annotated clinical adverse event corpus.  
- Funding was provided by the [Computational Sepsis Mining and Modelling (CoSeM)](https://www.ntnu.edu/cosem) project through the Norwegian University of Science and Technology (NTNU) Health Strategic Area.
- Ethical approval was granted by the Norwegian Regional Committees for Medical and Health Research Ethics (REK), approval no. 26814
- Detailed specifications for AAENOTE and CIIO were generated using [WIDOCO](https://github.com/dgarijo/Widoco) by [Daniel Garijo Verdejo](https://github.com/dgarijo)
