# Using Already Imported Individuals
The individuals are already imported with Cellfie into ```aaenote-individuals.owl```. To view individuals in AAENOTE, simply: 
1. Open ```aaenote-individuals.owl``` and import ```aaenote.owl```.


# Using Cellfie
Alternatively, individuals can be imported into Cellfie by:
1. In Protégé, select ```Tools > Create axioms from Excel workbook``` to start the Cellfie plugin and select 
 the Excel file ```import_AE_annotated.xlsx```. 
2. In Cellfie, select ```Load Rules```, add JSON file ```Sheets1-7.3_add_individuals.json```, click ```Generate Axioms```, and wait for the "Generated Axioms" popup.
3. In Cellfie's "Generated Axioms" popup, select ```Add to new ontology```.  Now all individuals are added to a separate ontology.



*note: Tab ```4.5_label_attr``` in ```import_AE_annotated.xlsx``` contains 1 individual which was not included in the ontology because that label was reported as an AnnotationError by the [BRAT rapid annotation tool](https://brat.nlplab.org/). 



For more information on Cellfie, please refer to:
  - [Protégé Cellfie Plugin GitHub](https://github.com/protegeproject/cellfie-plugin)
  - [Cellfie Tutorial](https://github.com/protegeproject/cellfie-plugin/wiki/Grocery-Tutorial)
