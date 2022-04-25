# Use case

Peripheral intravenous catheter (PIVCs) are the most common used medical devices at hospitals because they are used for administering intravenous (IV) fluids, IV medications, and blood transfusions [1].  Improper PIVC care can lead to phlebitis, that is infectious, mechanical or chemical inflammation of the vein.  Bacterial phlebitis can cause a bloodstream infection (BSI) which can potentially cause sepsis [2,3].  

Even though PIVCs are frequently used, they are routinely not documented in clinical records [1].  Moreover, sepsis is also poorly documented outside the intensive care units [4].  Hence, adverse event (AE) reports or documents which are customarily used to report PIVC failures were selected as the clinical text for this project.  


# Objective 
Develop a model for representing and reasoning about PIVC-related BSI in the unstructured free-text of AE reports to capture documented observable patient states and infer underlying knowledge of PIVC-related phlebitis or BSI. 


# Research Question
Is there a connection between BSIs and PIVCs at the hospital?


# Competency Questions (CQs)
1. Does patientA have an infection?
2. Does patientA have a BSI?
3. How many patients have an infection or BSI?
4. Which patients have sepsis?
5. Does patientB have a catheter?
6. Does patientB have a PIVC?
7. How many catheters does patientB have, where are they, and why does patientB need them?

    7a. How many catheters does patientB have?

    7b. Where are the catheters in patientB?

    7c. Why does patientB need the catheter(s)? 

8. Does patientC have an infection and catheter? If so, was patientC's infection associated with a catheter?

    8a. Does patientC have an infection and catheter?

    8b. Was patientC's infection associated with a catheter?




# References

[1] 
```
@article{Alexandrou2018UseOS_PVC,
  title={Use of Short Peripheral Intravenous Catheters: Characteristics, Management, and Outcomes Worldwide},
  author={Evan Alexandrou and Gillian Ray-Barruel and Peter J Carr and Steven A. Frost and Sheila Inwood and Niall S Higgins and Frances Lin and Laura Alberto and Leonard A. Mermel and Claire M Rickard},
  journal={J. of Hospital Med.},
  year={2018},
  volume={13 5},
  doi={10.12788/jhm.3039}
}
```
[2]
```
@article{Zhang2016_PIVCBSIGateway,
	title = {Infection risks associated with peripheral vascular catheters},
	volume = {17},
	issn = {1757-1774},
	doi = {10.1177/1757177416655472},
	number = {5},
	journal = {J. of Infection Prevention},
	author = {Zhang, Li and Cao, Siyu and Marsh, Nicole and Ray-Barruel, Gillian and Flynn, Julie and Larsen, Emily and Rickard, Claire M.},
	month = sep,
	year = {2016},
	pages = {207--213},
```
[3]
```
@article{Huerta2019BSIvsSepsis,
    author = {Huerta, Luis E and Rice, Todd W},
    title = "{{Pathologic Difference between Sepsis and Bloodstream Infections}}",
    journal = {The Journal of Applied Laboratory Medicine},
    volume = {3},
    number = {4},
    pages = {654-663},
    year = {2019},
    month = {10},
    issn = {2576-9456},
    doi = {10.1373/jalm.2018.026245},
}
```
[4]
```
@article{Rohde2013PoorSepsisDoc, 
  title={The epidemiology of acute organ system dysfunction from severe sepsis outside of the intensive care unit}, 
  volume={8}, 
  ISSN={1553-5592}, 
  IGNOREurl={http://dx.doi.org/10.1002/jhm.2012}, 
  DOI={10.1002/jhm.2012},
  number={5}, 
  journal={J. of Hospital Med.}, 
  author={Rohde, Jeffrey M. and Odden, Andrew J. and Bonham, Catherine and Kuhn, Latoya and Malani, Preeti N. and Chen, Lena M. and Flanders, Scott A. and Iwashyna, Theodore J.}, 
  year={2013}, 
  month={May}, 
  pages={243–247}
}
```
