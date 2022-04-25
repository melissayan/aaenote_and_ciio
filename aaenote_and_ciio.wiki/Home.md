# Terminology, Ontology, Competency Questions, and SPARQL Queries for Sepsis and Adverse Events


## Annotated Adverse Event NOte TErminology (AAENOTE)

The AAENOTE is a terminology that provides an index of what is annotated in a clinical adverse event document. The terminology was developed using the bottom-up approach based on the annotation guideline refinement process from 4 iterations. Competency questions were not used to create this terminology. This terminology assists annotators who want to label text and allows users interested in performing downstream analyses the ability to adjust granularity of labels.

It is not used to design a language's syntax, grammar, or terms because AAENOTE is a terminology for understanding the language and underlying meanings.  Instead, it is the interpreted formalized language that has been translated into basic statements for reasoning.  To capture relevant information, the underlying note was represented by annotated labels and relationships for the task of question answering and text understanding instead of solely retrieving information. Hence, the terminology focuses only on items of interest and is blind to items not within the terminology.


## Catheter Infection Indications Ontology (CIIO)

The CIIO represents clinical knowledge for signs of infections and catheters to identify peripheral intravenous catheter-related bloodstream infections, and was developed to accompany the AAENOTE.  The corresponding CIIO is an ontology that models clinical knowledge missing from AAENOTE.  It provides the missing clinical knowledge required to reason about the presence of catheters and infections documented in clinical text.  Although the data modeled is documented text, it enables clinicians to think about the data as an individual patient because they already do this routinely when documenting patient states. 


# Manuscript Info

This repository contains the terminology, ontology, competency questions, and SPARQL queries for the manuscript submitted to the [Semantic Web Journal](http://www.semantic-web-journal.net/):
```
@ARTICLE{yan2022,
author={Yan, Melissa Y. and Gustad, Lise Tuset and Høvik, Lise Husby and Nytrø, Øystein},
title={Terminology and Ontology Development for Semantic Annotation: A Use Case on Sepsis and Adverse Events},
year={2022}
}
```