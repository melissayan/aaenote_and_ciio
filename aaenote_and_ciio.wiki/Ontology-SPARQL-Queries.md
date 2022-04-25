# Catheter Infection Indications Ontology (CIIO) SPARQL Queries For Competency Questions

# Assumptions
1. 1 adverse event (AE) report or document = 1 patient.  
    - In the actual electronic incident reporting system database, if the AE is patient-related, the patient ID(s) will be included.  This allows users to know if the AE is about 1 patient, many patients, or no patients. 
1. In the same document but within different sentences of the same document, concepts that are documented are likely describing concepts that occurred in the same event for the same patient.
2. In the same sentence, concepts that are linked by a relationship are directly related.  Certain relationships can provide the reason for why a concept was or is needed (i.e., procedureA ```-procedure_uses->``` deviceX, therefore deviceX was needed to perform procedureA). 
3. Medical devices in this ontology only contain catheters or parts of catheters.



# Note
* All SPARQL Queries were performed in Protégé version 5.5.0 using the SPARQL Query tab.
* "CONCAT" does not work in protege's SPARQL Query Tab, so it's only possible to list observations of interest for each document. (More information on why not all SPARQL 1.1 functions work here in the [GitHub Ticket](https://github.com/protegeproject/rdf-library/issues/6))


---
# 1. Does patientA have phlebitis, and was it infectious phlebitis, chemical phlebitis, or mechanical phlebitis?

### List documents with observations, anatomical locations, and procedures that indicate early stage infusion phlebitis.
- Early stage of infusion phlebitis is indicated by an insertion site or infusion with 2 of the following signs: 
    - (i) pain or tenderness, 
    - (ii) red, 
    - (iii) swollen or edema, or 
    - (iv) warm.  
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?observations); separator=", " ) as ?document_observations) (GROUP_CONCAT (DISTINCT str(?locations); separator=", " ) as ?document_locations) (GROUP_CONCAT (DISTINCT str(?procedures); separator=", " ) as ?document_procedures) 
WHERE {
	{
		# Sentence with observations at the insertion site
		SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_location); separator=", " ) as ?locations)
		WHERE {
			{
				SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
				WHERE {
					?indv_document rdf:type :document .
					?indv_sentence rdf:type :sentence .
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
						rdf:type ?class_location .
					FILTER(?class_location != owl:NamedIndividual) .
					?indv_document :contains ?indv_sentence . 
					?indv_sentence :contains ?indv_observation ;
						:contains ?indv_location .
					?indv_observation :located_nearby_on_at_in ?indv_location .  
				}
				GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
				ORDER BY ?class_observation
			}
			FILTER (REGEX(str(?class_location), "insertion_site"))
		}
		GROUP BY ?indv_document 
	}
	UNION
	{
		# Sentence with observations and procedures for: iv, infusion, or catheter procedure
		SELECT ?indv_document  (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures)
		WHERE {
			{
				SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure
				WHERE {
					?indv_document rdf:type :document .
					?indv_sentence rdf:type :sentence .
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
						rdf:type ?class_procedure .
					FILTER(?class_procedure != owl:NamedIndividual) .
					?indv_document :contains ?indv_sentence .
					?indv_sentence :contains ?indv_observation ;
						:contains ?indv_procedure .
				}
				GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure
			}
			FILTER ((REGEX(str(?class_procedure), "iv") || REGEX(str(?class_procedure), "infusion")) || REGEX(str(?class_procedure), "catheter"))
		}
		GROUP BY ?indv_document 
	}
	FILTER (
	((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && REGEX(str(?observations), "red")) 
	|| 
	((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")))
	||
	((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")))
	||
	(REGEX(str(?observations), "red")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema"))
	||
	(REGEX(str(?observations), "red") && REGEX(str(?observations), "warm"))
	||
	((REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")) && REGEX(str(?observations), "warm"))
	)
}
GROUP BY ?indv_document 
ORDER BY ?indv_document 
```

### List documents with observations, anatomical locations, and procedures that indicate medium stage infusion phlebitis. 
- Medium stage of infusion phlebitis is indicated by: 
    - (1) a vein with pain or tenderness, and 
    - (2) an insertion site or infusion with 2 signs: 
        - (i) red, 
        - (ii) swollen or edema, or 
        - (iii) warm.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?observations); separator=", " ) as ?document_observations) (GROUP_CONCAT (DISTINCT str(?locations); separator=", " ) as ?document_locations) (GROUP_CONCAT (DISTINCT str(?procedures); separator=", " ) as ?document_procedures) 
WHERE {
	{
		# Sentence with vein with pain or tenderness
		SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_location); separator=", " ) as ?locations)
		WHERE {
			{
				SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
				WHERE {
					?indv_document rdf:type :document .
					?indv_sentence rdf:type :sentence .
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
						rdf:type ?class_location .
					FILTER(?class_location != owl:NamedIndividual) .
					?indv_document :contains ?indv_sentence . 
					?indv_sentence :contains ?indv_observation ;
						:contains ?indv_location .
					?indv_observation :located_nearby_on_at_in ?indv_location .  
				}
				GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
				ORDER BY ?class_observation
			}
			FILTER (REGEX(str(?class_location), "vein") && ((REGEX(str(?class_observation), "pain")) || (REGEX(str(?class_observation), "tenderness"))))
		}
		GROUP BY ?indv_document 
	}
	UNION
	{
		{
			# Sentence with observations at the insertion site
			SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_location); separator=", " ) as ?locations)
			WHERE {
				{
					SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
					WHERE {
						?indv_document rdf:type :document .
						?indv_sentence rdf:type :sentence .
						?indv_observation rdf:type/rdfs:subClassOf* :observation ;
							rdf:type ?class_observation .
						FILTER(?class_observation != owl:NamedIndividual) .
						?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
							rdf:type ?class_location .
						FILTER(?class_location != owl:NamedIndividual) .
						?indv_document :contains ?indv_sentence . 
						?indv_sentence :contains ?indv_observation ;
							:contains ?indv_location .
						?indv_observation :located_nearby_on_at_in ?indv_location .  
					}
					GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
					ORDER BY ?class_observation
				}
				FILTER (REGEX(str(?class_location), "insertion_site"))
			}
			GROUP BY ?indv_document 
		}
		UNION
		{
			# Sentence with observations and procedures for: iv, infusion, or catheter procedure
			SELECT ?indv_document  (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures)
			WHERE {
				{
					SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure
					WHERE {
						?indv_document rdf:type :document .
						?indv_sentence rdf:type :sentence .
						?indv_observation rdf:type/rdfs:subClassOf* :observation ;
							rdf:type ?class_observation .
						FILTER(?class_observation != owl:NamedIndividual) .
						?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
							rdf:type ?class_procedure .
						FILTER(?class_procedure != owl:NamedIndividual) .
						?indv_document :contains ?indv_sentence .
						?indv_sentence :contains ?indv_observation ;
							:contains ?indv_procedure .
					}
					GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure
				}
				FILTER ((REGEX(str(?class_procedure), "iv") || REGEX(str(?class_procedure), "infusion")) || REGEX(str(?class_procedure), "catheter"))
			}
			GROUP BY ?indv_document 
		}
		FILTER (
		((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && REGEX(str(?observations), "red")) 
		|| 
		((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")))
		||
		((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")))
		||
		(REGEX(str(?observations), "red")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema"))
		||
		(REGEX(str(?observations), "red") && REGEX(str(?observations), "warm"))
		||
		((REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")) && REGEX(str(?observations), "warm"))
	)	
	}
}
GROUP BY ?indv_document 
ORDER BY ?indv_document 
```

### List documents with observations, anatomical locations, and procedures that indicate advanced stage infusion phlebitis.
- Advanced stage of infusion phlebitis is indicated by: 
    - (1) a vein with hardness and pain or tenderness, and 
    - (2) an insertion site or infusion with 2 signs: 
        - (i) red, 
        - (ii) swollen or edema, or 
        - (iii) warm.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?observations); separator=", " ) as ?document_observations) (GROUP_CONCAT (DISTINCT str(?locations); separator=", " ) as ?document_locations) (GROUP_CONCAT (DISTINCT str(?procedures); separator=", " ) as ?document_procedures) 
WHERE {
	{	
		# Sentence with vein with hardness and pain or tenderness
		SELECT ?indv_document ?observations (GROUP_CONCAT (DISTINCT str(?class_location); separator=", ") as ?locations)
		WHERE {
			{
				SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) ?indv_location ?class_location
				WHERE {
					SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
					WHERE {
						?indv_document rdf:type :document .
						?indv_sentence rdf:type :sentence .
						?indv_observation rdf:type/rdfs:subClassOf* :observation ;
							rdf:type ?class_observation .
						FILTER(?class_observation != owl:NamedIndividual) .
						?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
							rdf:type ?class_location .
						FILTER(?class_location != owl:NamedIndividual) .
						?indv_document :contains ?indv_sentence . 
						?indv_sentence :contains ?indv_observation ;
							:contains ?indv_location .
						?indv_observation :located_nearby_on_at_in ?indv_location .  
					}
					GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
					ORDER BY ?class_observation
				}
				GROUP BY ?indv_document ?indv_location ?class_location
			}
			FILTER (
				REGEX(str(?class_location), "vein") && REGEX(str(?observations), "hardness") &&
				((REGEX(str(?observations), "pain") || (REGEX(str(?observations), "tenderness"))))
			)
		}
		GROUP BY ?indv_document ?observations
	}
	UNION
	{
		{
			# Sentence with observations at the insertion site
			SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_location); separator=", " ) as ?locations)
			WHERE {
				{
					SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
					WHERE {
						?indv_document rdf:type :document .
						?indv_sentence rdf:type :sentence .
						?indv_observation rdf:type/rdfs:subClassOf* :observation ;
							rdf:type ?class_observation .
						FILTER(?class_observation != owl:NamedIndividual) .
						?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
							rdf:type ?class_location .
						FILTER(?class_location != owl:NamedIndividual) .
						?indv_document :contains ?indv_sentence . 
						?indv_sentence :contains ?indv_observation ;
							:contains ?indv_location .
						?indv_observation :located_nearby_on_at_in ?indv_location .  
					}
					GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
					ORDER BY ?class_observation
				}
				FILTER (REGEX(str(?class_location), "insertion_site"))
			}
			GROUP BY ?indv_document 
		}
		UNION
		{
			# Sentence with observations and procedures for: iv, infusion, or catheter procedure
			SELECT ?indv_document  (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures)
			WHERE {
				{
					SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure
					WHERE {
						?indv_document rdf:type :document .
						?indv_sentence rdf:type :sentence .
						?indv_observation rdf:type/rdfs:subClassOf* :observation ;
							rdf:type ?class_observation .
						FILTER(?class_observation != owl:NamedIndividual) .
						?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
							rdf:type ?class_procedure .
						FILTER(?class_procedure != owl:NamedIndividual) .
						?indv_document :contains ?indv_sentence .
						?indv_sentence :contains ?indv_observation ;
							:contains ?indv_procedure .
					}
					GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure
				}
				FILTER ((REGEX(str(?class_procedure), "iv") || REGEX(str(?class_procedure), "infusion")) || REGEX(str(?class_procedure), "catheter"))
			}
			GROUP BY ?indv_document 
		}
		FILTER (
		((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && REGEX(str(?observations), "red")) 
		|| 
		((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")))
		||
		((REGEX(str(?observations), "pain") || REGEX(str(?observations), "tenderness")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")))
		||
		(REGEX(str(?observations), "red")) && (REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema"))
		||
		(REGEX(str(?observations), "red") && REGEX(str(?observations), "warm"))
		||
		((REGEX(str(?observations), "swollen") || REGEX(str(?observations), "edema")) && REGEX(str(?observations), "warm"))
	)	
	}
}
GROUP BY ?indv_document 
ORDER BY ?indv_document 
```



# 2. Does patientA have an infection?

### List documents with pus at the insertion site.
- Pus at an insertion site indicates an infection.  Because pus present is a sure sign of infection. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?indv_sentence ?indv_pus ?indv_insertion_site
WHERE {
	?indv_document rdf:type :document .
	?indv_sentence rdf:type :sentence .
	?indv_pus rdf:type :pus .
	?indv_insertion_site rdf:type :insertion_site . 
	
	?indv_document :contains ?indv_sentence . 
	?indv_sentence :contains ?indv_pus ;
		:contains ?indv_insertion_site . 
	?indv_pus :located_nearby_on_at_in ?indv_insertion_site . 
}
ORDER BY ?indv_document
```



# 3. Does patientB have a bloodstream infection (BSI)?
- A bloodstream infection is indicated by a blood test with a positive test result and/or the name of the cultured bacteria. 
- Need a list of all bacteria names and possible abbreviations. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?indv_blood_test (GROUP_CONCAT (DISTINCT str(?val_blood_test); separator=", " ) as ?values_blood_test)
WHERE{
	{
		?indv_document rdf:type :document .
		?indv_sentence rdf:type :sentence .
		?indv_blood_test rdf:type :blood_test ;
			rdf:type ?class_blood_test . 
		FILTER(?class_blood_test != owl:NamedIndividual) .
		OPTIONAL
		{
			?attr_blood_test rdfs:subPropertyOf* :blood_test_result . 
			?indv_blood_test ?attr_blood_test ?val_blood_test .
			FILTER(?val_blood_test != owl:NamedIndividual) .
		}
		?indv_document :contains ?indv_sentence . 
		?indv_sentence :contains ?indv_blood_test .
	}
	FILTER ((REGEX(str(?val_blood_test), "Positive") || REGEX(str(?val_blood_test), "Strep")) || REGEX(str(?val_blood_test), "Staph"))
}
GROUP BY ?indv_document ?indv_blood_test
ORDER BY ?indv_document
```



# 4. How many patients have an infection or BSI?

### Count the number of documents with pus at insertion site.
- Pus at an insertion site indicates an infection.  Because pus present is a sure sign of infection. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT (SUM (?n_documents) as ?sum_documents) 
WHERE {
	SELECT ?class_pus ?class_insertion_site (COUNT (DISTINCT ?indv_document) as ?n_documents)
	WHERE {
		?indv_document rdf:type :document .
		?indv_sentence rdf:type :sentence .
		?indv_pus rdf:type :pus ;
			rdf:type ?class_pus . 
		FILTER(?class_pus != owl:NamedIndividual) .
		?indv_insertion_site rdf:type :insertion_site ;
			rdf:type ?class_insertion_site . 
		FILTER(?class_insertion_site != owl:NamedIndividual) .
		?indv_document :contains ?indv_sentence . 
		?indv_sentence :contains ?indv_pus ;
			:contains ?indv_insertion_site . 
		?indv_pus :located_nearby_on_at_in ?indv_insertion_site . 
	}
	GROUP BY ?class_pus ?class_insertion_site
	ORDER BY ?indv_document
}
```

### Count the number of documents with either pus at insertion site or a blood test with a positive test result and/or the name of the cultured bacteria. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT (SUM (?n_documents) as ?sum_documents) 
WHERE {
	SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?indv_pus); separator=", " ) as ?pus) (GROUP_CONCAT (DISTINCT str(?indv_insertion_site); separator=", " ) as ?indv_insertion_site) (GROUP_CONCAT (DISTINCT str(?indv_blood_test); separator=", " ) as ?blood_tests) (GROUP_CONCAT (DISTINCT str(?val_blood_test); separator=", " ) as ?values_blood_test) (COUNT(DISTINCT ?indv_document) as ?n_documents)
	WHERE {
		{
			SELECT ?indv_document ?indv_pus ?indv_insertion_site 
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_pus rdf:type :pus ;
					rdf:type ?class_pus . 
				FILTER(?class_pus != owl:NamedIndividual) .
				?indv_insertion_site rdf:type :insertion_site ;
					rdf:type ?class_insertion_site . 
				FILTER(?class_insertion_site != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence . 
				?indv_sentence :contains ?indv_pus ;
					:contains ?indv_insertion_site . 
				?indv_pus :located_nearby_on_at_in ?indv_insertion_site . 
			}
			GROUP BY ?indv_document ?indv_pus ?indv_insertion_site 
		}
		UNION
		{
			SELECT ?indv_document ?indv_blood_test (GROUP_CONCAT (DISTINCT str(?val_blood_test); separator=", " ) as ?values_blood_test)
			WHERE{
				{
					?indv_document rdf:type :document .
					?indv_sentence rdf:type :sentence .
					?indv_blood_test rdf:type :blood_test ;
						rdf:type ?class_blood_test . 
					FILTER(?class_blood_test != owl:NamedIndividual) .
					OPTIONAL
					{
						?attr_blood_test rdfs:subPropertyOf* :blood_test_result . 
						?indv_blood_test ?attr_blood_test ?val_blood_test .
						FILTER(?val_blood_test != owl:NamedIndividual) .
					}
					?indv_document :contains ?indv_sentence . 
					?indv_sentence :contains ?indv_blood_test .
				}
				FILTER ((REGEX(str(?val_blood_test), "Positive") || REGEX(str(?val_blood_test), "Strep")) || REGEX(str(?val_blood_test), "Staph"))
			}
			GROUP BY ?indv_document ?indv_blood_test
			ORDER BY ?indv_document
		}
	}
	GROUP BY ?indv_document
	ORDER BY ?indv_document
}
```


# 5. Which patients have sepsis?

### List documents with observations, observation values, and anatomical locations that match the observations of a patient with sepsis.
- Sepsis is indicated by an infection indication combined with: 
    - (1) mobility impairment, 
    - (2) high body temperature (i.e., hyperthermia), and 
    - (3) frostbite.
- Need to manually verify if a document contains all 4 requirements above because observations and values cannot be concatenated using ```CONCAT``` in the SPARQL Query tab of Protégé version 5.5.0. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?class_observation ?val_observation ?class_location
WHERE {
	# Infection (i.e., pus at insertion site)
	{
		SELECT ?indv_document ?class_observation ?class_location ?indv_location
		WHERE {
			{
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .	
				?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
					rdf:type ?class_location .  
				FILTER(?class_location != owl:NamedIndividual) .
				?indv_observation rdf:type/rdfs:subClassOf* :observation ;
					rdf:type ?class_observation .
				FILTER(?class_observation != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence . 
				?indv_sentence :contains ?indv_observation ;
					:contains ?indv_location .
				?indv_observation :located_nearby_on_at_in ?indv_location .
			}
			FILTER (
				(REGEX(str(?class_observation), "pus") && REGEX(str(?class_location), "insertion_site"))
			)
		}
	}
	UNION 
	# other sepsis criteria
	{
		SELECT ?indv_document ?class_observation ?val_observation
		WHERE {
			{
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .	
				?indv_observation rdf:type/rdfs:subClassOf* :observation ;
					rdf:type ?class_observation .
				FILTER(?class_observation != owl:NamedIndividual) .
				OPTIONAL 
				{ 
					?attr_observation rdfs:subPropertyOf* :observation_attribute . 
					?indv_observation ?attr_observation ?val_observation .
					FILTER(?val_observation != owl:NamedIndividual) .
				}
				?indv_document :contains ?indv_sentence . 
				?indv_sentence :contains ?indv_observation .
			}
			FILTER (
				(REGEX(str(?class_observation), "mobility_impairment"))
				||
				(REGEX(str(?class_observation), "body_temperature") && REGEX(str(?val_observation), "Hyperthermia"))
				||
				(REGEX(str(?class_observation), "frostbite"))
			)
		}
	}
} 
GROUPBY ?indv_document ?class_observation ?val_observation ?class_location ?indv_location
ORDERBY ?indv_document
```

### List documents with observations, observation values, and anatomical locations that have an infection indication and meet the qSOFA score.
- Meeting the Quick Sequential Organ Failure Assessment Score (qSOFA) [2] sepsis criteria is indicated if there is an infection indication and at least 2 of the following: 
    - (1) high respiratory rate, 
    - (2) low blood pressure, or 
    - (3) a consciousness level that is either confusion, verbally responsive, painfully responsive, or unresponsive. 

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?class_observation ?val_observation ?class_location
WHERE {
	# qSOFA infection criteria (i.e., pus at insertion site).
	{
		SELECT ?indv_document ?class_observation ?class_location ?indv_location
		WHERE {
			{
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .	
				?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
					rdf:type ?class_location .  
				FILTER(?class_location != owl:NamedIndividual) .
				?indv_observation rdf:type/rdfs:subClassOf* :observation ;
					rdf:type ?class_observation .
				FILTER(?class_observation != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence . 
				?indv_sentence :contains ?indv_observation ;
					:contains ?indv_location .
				?indv_observation :located_nearby_on_at_in ?indv_location .
			}
			FILTER ((REGEX(str(?class_observation), "pus") && REGEX(str(?class_location), "insertion_site")))
		}
	}
	UNION 
	# Other qSOFA criteria of high respiratory rate, low blood pressure, and consciousness level of confusion, verbally responsive, painfully repsonsive, or unresponsive.
	{
		SELECT ?indv_document ?class_observation ?val_observation
		WHERE {
			{
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .	
				?indv_observation rdf:type/rdfs:subClassOf* :observation ;
					rdf:type ?class_observation .
				FILTER(?class_observation != owl:NamedIndividual) .
				OPTIONAL 
				{ 
					?attr_observation rdfs:subPropertyOf* :observation_attribute . 
					?indv_observation ?attr_observation ?val_observation .
					FILTER(?val_observation != owl:NamedIndividual) .
				}
				?indv_document :contains ?indv_sentence . 
				?indv_sentence :contains ?indv_observation .
			}
			FILTER (
				(REGEX(str(?class_observation), "respiratory_rate") && REGEX(str(?val_observation), "High"))
				||
				(REGEX(str(?class_observation), "blood_pressure") && REGEX(str(?val_observation), "Low"))
				||
				(REGEX(str(?class_observation), "consciousness_level") && (
					REGEX(str(?val_observation), "Confusion")
					||
					REGEX(str(?val_observation), "Verbally responsive")
					||
					REGEX(str(?val_observation), "Painfully responsive")
					||
					REGEX(str(?val_observation), "Unresponsive")
					)
				)
			)
		}
	}
} 
GROUPBY ?indv_document ?class_observation ?val_observation ?class_location 
ORDERBY ?indv_document
```

### List documents with observations, observation values, and anatomical locations that have an infection indication and meet the NEWS2 score.
- Sepsis is indicated if there is an infection indication and the National Early Warning Score 2 (NEWS2) [3] criteria for clinical deterioration is met by a combination of:
    - (1) high respiratory rate, 
    - (2) low blood pressure, 
    - (3) high pulse, 
    - (4) low body temperature or high body temperature (i.e., hypothermia or hyperthermia), and 
    - (5) consciousness level = confusion, verbally responsive, painfully responsive, or unresponsive.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?class_observation ?val_observation ?class_location
WHERE {
	# Infection criteria (i.e., pus at insertion site).
	{
		SELECT ?indv_document ?class_observation ?class_location ?indv_location
		WHERE {
			{
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .	
				?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
					rdf:type ?class_location .  
				FILTER(?class_location != owl:NamedIndividual) .
				?indv_observation rdf:type/rdfs:subClassOf* :observation ;
					rdf:type ?class_observation .
				FILTER(?class_observation != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence . 
				?indv_sentence :contains ?indv_observation ;
					:contains ?indv_location .
				?indv_observation :located_nearby_on_at_in ?indv_location .
			}
			FILTER (
				(REGEX(str(?class_observation), "pus") && REGEX(str(?class_location), "insertion_site"))
			)
		}
	}
	UNION 
	# NEWS2 deterioration criteria of high respiratory rate, low blood pressure, high pulse, hypothermia or hyperthermia body temperature, and consciousness level of confusion, verbally responsive, painfully repsonsive, or unresponsive.
	{
		SELECT ?indv_document ?class_observation ?val_observation
		WHERE {
			{
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .	
				?indv_observation rdf:type/rdfs:subClassOf* :observation ;
					rdf:type ?class_observation .
				FILTER(?class_observation != owl:NamedIndividual) .
				OPTIONAL 
				{ 
					?attr_observation rdfs:subPropertyOf* :observation_attribute . 
					?indv_observation ?attr_observation ?val_observation .
					FILTER(?val_observation != owl:NamedIndividual) .
				}
				?indv_document :contains ?indv_sentence . 
				?indv_sentence :contains ?indv_observation .
			}
			FILTER (
				(REGEX(str(?class_observation), "respiratory_rate") && REGEX(str(?val_observation), "High"))
				||
				(REGEX(str(?class_observation), "blood_pressure") && REGEX(str(?val_observation), "Low"))
				||
				(REGEX(str(?class_observation), "pulse") && REGEX(str(?val_observation), "High"))
				||
				(REGEX(str(?class_observation), "body_temperature") && 
					REGEX(str(?val_observation), "Hypothermia")
					||
					REGEX(str(?val_observation), "Hyperthermia")
					)
				||
				(REGEX(str(?class_observation), "consciousness_level") && (
					REGEX(str(?val_observation), "Confusion")
					||
					REGEX(str(?val_observation), "Verbally responsive")
					||
					REGEX(str(?val_observation), "Painfully responsive")
					||
					REGEX(str(?val_observation), "Unresponsive")
					)
				)
			)
		}
	}
} 
GROUPBY ?indv_document ?class_observation ?val_observation ?class_location
ORDERBY ?indv_document
```



# 6. Does patientB have a catheter?

### List documents with medical devices or procedures of IV usage, infusion, or catheter procedures that indicate a catheter is or was in use. 
- A patient has a specific catheter documented. 
- Any intravenous (IV) usage or infusion indicates some type of catheter is used.  
    - IV usage includes general IV, IV medication, IV fluid, and IV antibiotics.  Infusion includes infusion, intraosseous infusion, intravenous infusion, and subcutaneous infusion.  
    - Based on the type of IV usage or infusion alone, it is not enough to determine what type of catheter was used.  
- Catheter procedures indicate that a catheter is or was present because they require a catheter.  
    - Catheter procedures include catheter insertion, catheter discontinued use, catheter removal, catheter replacement, and catheter self-removal.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_device); separator=", " ) as ?devices)  (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures) 
WHERE {
	SELECT ?indv_document ?indv_sentence ?indv_device ?class_device ?indv_procedure ?class_procedure
	WHERE {
		{
			SELECT ?indv_document ?indv_sentence ?indv_device ?class_device
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_device rdf:type/rdfs:subClassOf* :medical_device ;
					rdf:type ?class_device . 
				FILTER(?class_device != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence .
				?indv_sentence :contains ?indv_device .
			}
			ORDER BY ?indv_sentence ?indv_device ?class_device
		} 
		UNION
		{
			SELECT ?indv_document ?indv_sentence ?indv_procedure ?class_procedure
			WHERE {
				{		
					SELECT ?indv_document ?indv_sentence ?indv_procedure ?class_procedure
					WHERE {
						?indv_document rdf:type :document .
						?indv_sentence rdf:type :sentence .
						?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
							rdf:type ?class_procedure .
						FILTER(?class_procedure != owl:NamedIndividual) .
						?indv_document :contains ?indv_sentence .
						?indv_sentence :contains ?indv_procedure .
					}
					ORDER BY ?indv_sentence ?indv_procedure ?class_procedure
				}
				FILTER ((REGEX(str(?class_procedure), "iv") || REGEX(str(?class_procedure), "infusion")) || REGEX(str(?class_procedure), "catheter"))
			}
		}
	}
	ORDER BY ?indv_document ?indv_sentence 
}
GROUP BY ?indv_document
```



# 7. Does patientB have a PIVC?

### List documents with the sentences that contain a PIVC.
- PIVCs are rarely documented, so any PIVC explicitly documented indicates a PIVC was used or in use. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?indv_sentence ?indv_pivc 
WHERE {
	?indv_document rdf:type :document .
	?indv_sentence rdf:type :sentence .
	?indv_pivc rdf:type :peripheral_intravenous_catheter .
	?indv_document :contains ?indv_sentence . 
	?indv_sentence :contains ?indv_pivc . 
}
ORDER BY ?indv_document ?indv_sentence
```

### List documents with the sentences that contain observations of procedures indicating a PIVC is or was in use.
- Leaking IV or an infusion at the arm, elbow, or hand indicates a PIVC is used.  
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?indv_sentence ?observations ?class_procedure ?class_location
WHERE {
	{	
		# Group ?class_observations alphabetically by ?indv_document and ?indv_sentence
		SELECT ?indv_document  ?indv_sentence (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) ?indv_procedure ?class_procedure ?class_location
		WHERE {
			{
				# Observation at arm 
				SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure ?indv_location ?class_location
				WHERE {
					?indv_document rdf:type :document .
					?indv_sentence rdf:type :sentence .
					
					?indv_location rdf:type/rdfs:subClassOf* :arm ;
						rdf:type ?class_location .
					FILTER(?class_location != owl:NamedIndividual) .
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
						rdf:type ?class_procedure .
					FILTER(?class_procedure != owl:NamedIndividual) .
					
					?indv_document :contains ?indv_sentence . 
					?indv_sentence 	:contains ?indv_location ;
						:contains ?indv_procedure ;
						:contains ?indv_observation .
					
					?indv_observation :is_observed_with ?indv_procedure .  
					?indv_observation :located_nearby_on_at_in ?indv_location .  
				}
				GROUP BY ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_procedure ?class_procedure ?indv_location ?class_location
				ORDER BY ?class_observation
			}
		}
		GROUP BY ?indv_document ?indv_sentence ?indv_procedure ?class_procedure ?class_location
	}
	FILTER (
		(REGEX(str(?observations), "leakage")) 
		&& 
		(REGEX(str(?class_procedure), "iv") || REGEX(str(?class_procedure), "infusion"))
	)
}
ORDER BY ?indv_document ?indv_sentence
```



# 8a. How many catheters does patientB have?

### List documents with devices or catheter-related procedures and count the number of devices and catheter-related procedures in each document.
- We cannot count the exact number of catheters per document because:
    - multiple sentences within the document could be describing the same catheter. 
    - multiple procedures documented could be using the same catheter. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_device); separator=", " ) as ?devices) (COUNT (DISTINCT ?indv_device) as ?n_devices) (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures) (COUNT (DISTINCT ?indv_procedure) as ?n_procedures)
WHERE {
	{
		{
			SELECT ?indv_document  ?class_device ?indv_device
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_device rdf:type/rdfs:subClassOf* :medical_device ;
					rdf:type ?class_device .
				FILTER(?class_device != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence .
				?indv_sentence :contains ?indv_device .
			}
			ORDER BY ?class_device
		}
	}
	UNION
	{
		{
			SELECT ?indv_document  ?class_procedure ?indv_procedure
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
					rdf:type ?class_procedure .
				FILTER(?class_procedure != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence .
				?indv_sentence :contains ?indv_procedure .
			}
			ORDER BY ?class_procedure
		}
		FILTER ((REGEX(str(?class_procedure), "iv") || REGEX(str(?class_procedure), "infusion")) || REGEX(str(?class_procedure), "catheter"))
	}
}
GROUP BY ?indv_document 
ORDER BY ?indv_document
```



# 8b. Where are the catheters in patientB?

### List documents and the sentences with devices or catheter-related procedures located nearby/on/at/in an anatomical location and if available, list additional anatomical location descriptors (i.e., front, back, left, or right).
- We cannot know the exact locations of catheters per document because:
    - multiple sentences within a document could be describing a catheter at the same location but using more general terms (i.e., arm instead of hand).
    - it is often unclear if the same location is documented (i.e., we cannot distinguish if elbowA is on armX, unless it is explicitly documented that elbowA is on the right and armX is also on the right). 
    - multiple procedures can be performed at the same location. 
- An additional anatomical ontology is needed to infer location based on catheter type.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?indv_sentence (GROUP_CONCAT (DISTINCT str(?class_device); separator=", " ) as ?devices)  (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures) (GROUP_CONCAT (DISTINCT str(?class_location); separator=", " ) as ?locations) (GROUP_CONCAT (DISTINCT str(?val_location); separator=", " ) as ?val_locations)
WHERE {
	SELECT ?indv_document ?indv_sentence ?class_device ?class_procedure ?class_location ?val_location
	WHERE {
		{
			SELECT ?indv_document ?indv_sentence ?class_device ?class_location ?val_location
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
					rdf:type ?class_location .
				FILTER(?class_location != owl:NamedIndividual) .
				OPTIONAL 
				{ 
					?attr_location rdfs:subPropertyOf* :anatomical_location_descriptor . 
					?indv_location ?attr_location ?val_location .
					FILTER(?val_location != owl:NamedIndividual) .
				}
				?indv_device rdf:type/rdfs:subClassOf* :medical_device ;
					rdf:type ?class_device . 
				FILTER(?class_device != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence .
				?indv_sentence :contains ?indv_location ;
					:contains ?indv_device .
				?indv_device :located_nearby_on_at_in ?indv_location . 
			}
			ORDER BY ?indv_sentence ?class_device ?class_location
		} 
		UNION
		{
			SELECT ?indv_document ?indv_sentence ?class_procedure ?class_location ?val_location
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
					rdf:type ?class_location .
				FILTER(?class_location != owl:NamedIndividual) .
				OPTIONAL 
				{ 
					?attr_location rdfs:subPropertyOf* :anatomical_location_descriptor . 
					?indv_location ?attr_location ?val_location .
					FILTER(?val_location != owl:NamedIndividual) .
				}
				?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
					rdf:type ?class_procedure .
				FILTER(?class_procedure != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence .
				?indv_sentence :contains ?indv_location ; 
					:contains ?indv_procedure .
			}
			ORDER BY ?indv_sentence ?class_procedure ?class_location
		}
	}
	ORDER BY ?indv_document ?indv_sentence 
}
GROUP BY ?indv_document ?indv_sentence
```



# 8c. Why does patientB need the catheter(s)?

### List documents and the sentences where a procedure uses a medical device.
- The medical device is needed and used in a specific procedure. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?indv_sentence (GROUP_CONCAT (DISTINCT str(?indv_device); separator=", " ) as ?devices)  (GROUP_CONCAT (DISTINCT str(?indv_procedure); separator=", " ) as ?procedures) 
WHERE {
	SELECT ?indv_document ?indv_sentence ?indv_device ?class_device ?indv_procedure ?class_procedure
	WHERE {
		?indv_document rdf:type :document .
		?indv_sentence rdf:type :sentence .
		?indv_device rdf:type/rdfs:subClassOf* :medical_device ;
			rdf:type ?class_device . 
		FILTER(?class_device != owl:NamedIndividual) .
		?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
			rdf:type ?class_procedure .
		FILTER(?class_procedure != owl:NamedIndividual) .
		?indv_document :contains ?indv_sentence .
		?indv_sentence :contains ?indv_device ;
			:contains ?indv_procedure .
		?indv_procedure :procedure_uses ?indv_device . 
	}
	ORDER BY ?indv_sentence ?indv_device ?class_device ?indv_procedure ?class_procedure 
}
GROUP BY ?indv_document ?indv_sentence
```

### List documents and the sentences with medical devices or catheter-related procedures. 
- All medical devices in this ontology are about catheters or catheter parts.
- In the same sentence, if the procedure uses a medical device, then that procedure is why the medical device is or was needed. 
- In the same document but within different sentences of the same document, if a medical device and procedure are documented, then it is likely describing concepts that occurred in the same event for a patient.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?indv_sentence (GROUP_CONCAT (DISTINCT str(?indv_device); separator=", " ) as ?devices)  (GROUP_CONCAT (DISTINCT str(?indv_procedure); separator=", " ) as ?procedures) 
WHERE {
	SELECT ?indv_document ?indv_sentence ?indv_device ?class_device ?indv_procedure ?class_procedure
	WHERE {
		{
			SELECT ?indv_document ?indv_sentence ?indv_device ?class_device
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_device rdf:type/rdfs:subClassOf* :medical_device ;
					rdf:type ?class_device . 
				FILTER(?class_device != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence .
				?indv_sentence :contains ?indv_device .
			}
			ORDER BY ?indv_sentence ?indv_device ?class_device
		} 
		UNION
		{
			SELECT ?indv_document ?indv_sentence ?indv_procedure ?class_procedure
			WHERE {
				?indv_document rdf:type :document .
				?indv_sentence rdf:type :sentence .
				?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
					rdf:type ?class_procedure .
				FILTER(?class_procedure != owl:NamedIndividual) .
				?indv_document :contains ?indv_sentence .
				?indv_sentence :contains ?indv_procedure .
			}
			ORDER BY ?indv_sentence ?indv_procedure ?class_procedure		
		}
	}
	ORDER BY ?indv_document ?indv_sentence 
}
GROUP BY ?indv_document ?indv_sentence
```



# 9a. Does patientC have an infection and catheter?
### List documents with observations, locations, devices, and procedures if there is an infection indication and catheter indication. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/ciio#>
SELECT ?indv_document ?observations ?locations ?devices ?procedures ?n_documents
WHERE {
	{
		SELECT ?indv_document ?observations ?locations ?devices ?procedures (COUNT (?indv_document) as ?n_documents)
		WHERE {
			SELECT ?indv_document (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (GROUP_CONCAT (DISTINCT str(?class_location); separator=", " ) as ?locations) (GROUP_CONCAT (DISTINCT str(?class_device); separator=", " ) as ?devices)  (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures) 
			WHERE {
				{
					# Infection indication (i.e., pus at the insertion site)
					SELECT ?indv_document ?indv_sentence ?indv_observation ?class_observation ?indv_location ?class_location
					WHERE {
						{
							?indv_document rdf:type :document .
							?indv_sentence rdf:type :sentence .
							?indv_observation rdf:type/rdfs:subClassOf* :observation ;
								rdf:type ?class_observation .
							FILTER(?class_observation != owl:NamedIndividual) .
							?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
								rdf:type ?class_location .
							FILTER(?class_location != owl:NamedIndividual) .
							?indv_document :contains ?indv_sentence . 
							?indv_sentence :contains ?indv_observation ;
								:contains ?indv_location . 
							?indv_observation :located_nearby_on_at_in ?indv_location . 
						}
						FILTER ((REGEX(str(?class_observation), "pus") && REGEX(str(?class_location), "insertion_site")))
					}
					ORDER BY ?indv_document ?indv_sentence 
				}
				UNION
				{
					# A catheter or catheter-related procedure is documented
					SELECT ?indv_document ?indv_sentence ?indv_device ?class_device ?indv_procedure ?class_procedure
					WHERE {
						{
							SELECT ?indv_document ?indv_sentence ?indv_device ?class_device
							WHERE {
								?indv_document rdf:type :document .
								?indv_sentence rdf:type :sentence .
								?indv_device rdf:type/rdfs:subClassOf* :medical_device ;
									rdf:type ?class_device . 
								FILTER(?class_device != owl:NamedIndividual) .
								?indv_document :contains ?indv_sentence .
								?indv_sentence :contains ?indv_device .
							}
							ORDER BY ?indv_sentence ?indv_device ?class_device
						}
						UNION 
						{
							SELECT ?indv_document ?indv_sentence ?indv_procedure ?class_procedure
							WHERE {
								{		
									SELECT ?indv_document ?indv_sentence ?indv_procedure ?class_procedure
									WHERE {
										?indv_document rdf:type :document .
										?indv_sentence rdf:type :sentence .
										?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
											rdf:type ?class_procedure .
										FILTER(?class_procedure != owl:NamedIndividual) .
										?indv_document :contains ?indv_sentence .
										?indv_sentence :contains ?indv_procedure .
									}
									ORDER BY ?indv_sentence ?indv_procedure ?class_procedure
								}
								FILTER ((REGEX(str(?class_procedure), "iv") || REGEX(str(?class_procedure), "infusion")) || REGEX(str(?class_procedure), "catheter"))
							}
						}
					}
					ORDER BY ?indv_document ?indv_sentence 
				}
			}
			GROUP BY ?indv_document 
			ORDER BY ?indv_document  
		}
		GROUP BY ?indv_document ?observations ?locations ?devices ?procedures
	}
	FILTER (REGEX(str(?observations), "http") && REGEX(str(?locations), "http") && REGEX(str(?devices), "http") && REGEX(str(?procedures), "http"))
}
```



# 9b. Was patientC's infection associated with a catheter?
Cannot determine if an infection is associated with a catheter unless that catheter is tested in the microbiology lab.



