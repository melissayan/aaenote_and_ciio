# Annotated Adverse Event NOte TErminology (AAENOTE) SPARQL Queries For Competency Questions
All SPARQL Queries were performed in Protégé version 5.5.0 using the SPARQL Query tab.

# 1. Does patientA have phlebitis, and was it infectious phlebitis, chemical phlebitis, or mechanical phlebitis?

### List patients who have phlebitis
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT DISTINCT ?indv_patient ?class_phlebitis
WHERE {
	?indv_patient rdf:type :patient ;
		rdf:type ?class_patient .
	FILTER(?class_patient != owl:NamedIndividual) .
	?indv_phlebitis rdf:type* :phlebitis ;
		rdf:type ?class_phlebitis .
	FILTER(?class_phlebitis != owl:NamedIndividual) .
	FILTER(REGEX(str(?class_phlebitis), "phlebitis")) .
	?indv_patient :person_has ?indv_phlebitis . 
}
ORDER BY ?indv_patient
```

### List phlebitis located nearby/on/at/in an anatomical location 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT DISTINCT ?indv_location ?class_phlebitis 
WHERE {
	?indv_phlebitis rdf:type :phlebitis ;
		rdf:type ?class_phlebitis .
	FILTER(?class_phlebitis != owl:NamedIndividual) .
	?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
		rdf:type ?class_location .
	FILTER(?class_location != owl:NamedIndividual) .
	?indv_phlebitis :located_nearby_on_at_in ?indv_location .
} 
ORDER BY ?indv_location
```



# 1. Does patientA have an infection?

### List patients who have infection 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT DISTINCT ?indv_patient ?class_infection
WHERE {
	?indv_patient rdf:type :patient ;
		rdf:type ?class_patient .
	FILTER(?class_patient != owl:NamedIndividual) .
	?indv_infection rdf:type* :infection ;
		rdf:type ?class_infection .
	FILTER(?class_infection != owl:NamedIndividual) .
	FILTER(REGEX(str(?class_infection), "infection")) .
	?indv_patient :person_has ?indv_infection . 
}
ORDER BY ?indv_patient
```

### List patients who have infection and/or other observations
```sparql 
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT DISTINCT ?indv_patient ?observations 
WHERE {
	{
		# Group ?class_observations alphabetically by ?indv_patient
		SELECT ?indv_patient ?class_patient 
		    (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations)
		WHERE {
			# Organize alphabetically ?class_observations
			SELECT ?indv_patient ?class_patient ?class_observation 
			WHERE {
				?indv_patient rdf:type :patient ;
					rdf:type ?class_patient .
				FILTER(?class_patient != owl:NamedIndividual) .
				FILTER(REGEX(str(?class_patient), "patient")) .
				?indv_observation rdf:type/rdfs:subClassOf* :observation ;
					rdf:type ?class_observation .
				FILTER(?class_observation != owl:NamedIndividual) .
				?indv_patient :person_has ?indv_observation .
			}
			GROUP BY ?indv_patient ?class_patient ?class_observation
			ORDER BY ?class_observation
		}
		GROUP BY ?indv_patient ?class_patient
	}
	FILTER REGEX(str(?observations), "infection")
}
ORDER BY ?indv_patient
```

### List infection located nearby/on/at/in an anatomical location 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT DISTINCT ?indv_location ?class_infection 
WHERE {
	?indv_infection rdf:type :infection ;
		rdf:type ?class_infection .
	FILTER(?class_infection != owl:NamedIndividual) .
	?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
		rdf:type ?class_location .
	FILTER(?class_location != owl:NamedIndividual) .
	?indv_infection :located_nearby_on_at_in ?indv_location .
} 
ORDER BY ?indv_location
```

### List infection and other observations located nearby/on/at/in an anatomical location
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT DISTINCT ?indv_location ?observations 
WHERE {
	{
		SELECT ?indv_location ?class_location (GROUP_CONCAT (DISTINCT ?class_observation; separator=", " ) as ?observations)
		WHERE {
			{
				SELECT ?indv_location ?class_location ?class_observation
				WHERE {
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
						rdf:type ?class_location .
					FILTER(?class_location != owl:NamedIndividual) .
					?indv_observation :located_nearby_on_at_in ?indv_location .
				} 
				GROUP BY ?indv_location ?class_location ?class_observation
				ORDER BY ?class_observation 
			}
		}
		GROUP BY ?indv_location ?class_location
		ORDER BY ?class_location
	}
	FILTER REGEX(str(?observations), "infection")
}
ORDER BY ?indv_location  
```

# 2. Does patientA have a BSI?
Cannot determine if there is a BSI without a microbiology laboratory result of a positive blood culture and the cultured bacteria name. 
 
```sparql
None
```


# 3. How many patients have an infection or BSI?

### Count the number of patients with infection and number patients with infection and other observations. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?class_patient ?observations ?n_patients
WHERE {
	{
		SELECT ?class_patient (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) (COUNT (DISTINCT ?indv_patient ) as ?n_patients)
		WHERE {
			?indv_patient rdf:type :patient ;
				rdf:type ?class_patient .
			FILTER(?class_patient != owl:NamedIndividual) .
			?indv_infection rdf:type :infection ;
				rdf:type ?class_observation .
			FILTER(?class_observation != owl:NamedIndividual) .
			?indv_patient :person_has ?indv_infection . 
		}
		GROUP BY ?class_patient ?class_observation
		ORDER BY ?class_observation
	}
	UNION
	{

		SELECT ?class_patient ?observations (COUNT (DISTINCT ?indv_patient ) as ?n_patients)
		WHERE {
			{
				SELECT ?indv_patient ?class_patient (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations) 
				WHERE {
					SELECT ?indv_patient ?class_patient ?class_observation 
					WHERE {
						?indv_patient rdf:type :patient ;
							rdf:type ?class_patient .
						FILTER(?class_patient != owl:NamedIndividual) .
						?indv_observation rdf:type/rdfs:subClassOf* :observation ;
							rdf:type ?class_observation .
						FILTER(?class_observation != owl:NamedIndividual) .
						?indv_patient :person_has ?indv_observation .
					}
					GROUP BY ?indv_patient ?class_patient ?class_observation
					ORDER BY ?class_observation
				}
				GROUP BY ?indv_patient ?class_patient 
			}
			FILTER REGEX(str(?observations), "infection")
		}
		GROUP BY ?class_patient ?observations
	}
}
```

### Count the number of anatomical locations where infection and other observations are located nearby/on/at/in that anatomical location.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?class_location ?observations (COUNT (DISTINCT ?indv_location) as ?n_instances)
WHERE {
	{
		SELECT ?indv_location ?class_location (GROUP_CONCAT (DISTINCT ?class_observation; separator=", " ) as ?observations)
		WHERE {
			{
				SELECT ?indv_location ?class_location ?class_observation
				WHERE {
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
						rdf:type ?class_location .
					FILTER(?class_location != owl:NamedIndividual) .
					?indv_observation :located_nearby_on_at_in ?indv_location .
				} 
				GROUP BY ?indv_location ?class_location ?class_observation
				ORDER BY ?class_observation 
			}
		}
		GROUP BY ?indv_location ?class_location
		ORDER BY ?class_location
	}
	FILTER REGEX(str(?observations), "infection")
}
GROUP BY ?class_location ?observations
ORDER BY ?class_location
```

### Count the number of anatomical locations with the same observations located nearby/on/at/in that anatomical location.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?class_location ?observations (COUNT (DISTINCT ?indv_location) as ?n_instances)
WHERE {
	{
		SELECT ?indv_location ?class_location (GROUP_CONCAT (DISTINCT ?class_observation; separator=", " ) as ?observations)
		WHERE {
			{
				SELECT ?indv_location ?class_location ?class_observation
				WHERE {
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
						rdf:type ?class_location .
					FILTER(?class_location != owl:NamedIndividual) .
					?indv_observation :located_nearby_on_at_in ?indv_location .
				} 
				GROUP BY ?indv_location ?class_location ?class_observation
				ORDER BY ?class_observation 
			}
		}
		GROUP BY ?indv_location ?class_location
		ORDER BY ?class_location
	}
}
GROUP BY ?class_location ?observations
ORDER BY ?class_location
```



# 4. Which patients have sepsis?

### List patients who have sepsis.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?class_sepsis
WHERE {
	?indv_patient rdf:type :patient .
	?indv_sepsis rdf:type :sepsis ;
		rdf:type ?class_sepsis .
	FILTER(?class_sepsis != owl:NamedIndividual) .
	?indv_patient :person_has ?indv_sepsis .
}
ORDER BY ?indv_patient
```

### List patients who have sepsis and/or other observations.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?observations 
WHERE {
	{
		SELECT ?indv_patient ?class_patient (GROUP_CONCAT (DISTINCT str(?class_observation); separator=", " ) as ?observations)
		WHERE {
			{
				SELECT ?indv_patient ?class_patient ?class_observation 
				WHERE {
					?indv_patient rdf:type :patient ;
						rdf:type ?class_patient .
					FILTER(?class_patient != owl:NamedIndividual) .
					?indv_observation rdf:type/rdfs:subClassOf* :observation ;
						rdf:type ?class_observation .
					FILTER(?class_observation != owl:NamedIndividual) .
					?indv_patient :person_has ?indv_observation .
				}
				GROUP BY ?indv_patient ?class_patient ?class_observation
				ORDER BY ?class_observation
			}
		}
		GROUP BY ?indv_patient ?class_patient
	}
	FILTER REGEX(str(?observations), "sepsis")
}
ORDER BY ?indv_patient
```



# 5. Does patientB have a catheter?

### List patients that have a catheter, the catheter's type, and the number of that catheter type.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?class_catheter (COUNT (DISTINCT ?indv_catheter ) as ?n_catheters)
WHERE {
	?indv_patient rdf:type :patient ;
		rdf:type ?class_patient .
	FILTER(?class_patient != owl:NamedIndividual) .
	?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
		rdf:type ?class_catheter .
	FILTER(?class_catheter != owl:NamedIndividual) .
	?indv_patient :person_has ?indv_catheter .
}
GROUP BY ?indv_patient ?class_catheter
ORDER BY ?indv_patient ?class_catheter
```

### List the anatomical location and the type of catheter located nearby/on/at/in there.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_location ?class_catheter
WHERE {
	?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
		rdf:type ?class_catheter .
	FILTER(?class_catheter != owl:NamedIndividual) .
	?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
		rdf:type ?class_location .
	FILTER(?class_location != owl:NamedIndividual) .
	?indv_catheter :located_nearby_on_at_in ?indv_location .
}
ORDER BY ?indv_location
```




# 6. Does patientB have a PIVC?

### List patients that have a PIVC and the number of PIVCs.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?class_pivc (COUNT (DISTINCT ?indv_pivc) as ?n_catheters)
WHERE {
	?indv_patient rdf:type :patient ;
		rdf:type ?class_patient .
	FILTER(?class_patient != owl:NamedIndividual) .
	?indv_pivc rdf:type/rdfs:subClassOf* :peripheral_intravenous_catheter ;
		rdf:type ?class_pivc .
	FILTER(?class_pivc != owl:NamedIndividual) .
	?indv_patient :person_has ?indv_pivc .
}
GROUP BY ?indv_patient ?class_pivc
ORDER BY ?indv_patient
```

### List anatomical locations with a PIVC. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_location ?class_pivc 
WHERE {
	?indv_pivc rdf:type/rdfs:subClassOf* :peripheral_intravenous_catheter ;
		rdf:type ?class_pivc .
	FILTER(?class_pivc != owl:NamedIndividual) .
	?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
		rdf:type ?class_location .
	FILTER(?class_location != owl:NamedIndividual) .
	?indv_pivc :located_nearby_on_at_in ?indv_location .
}
ORDER BY ?indv_location
```



# 7a. How many catheters does patientB have?

### Count the number of catheters a patient has and provide the types of catheters.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
# Group ?class_catheters alphabetically by ?indv_patient
SELECT ?indv_patient (GROUP_CONCAT (DISTINCT str(?class_catheter); separator=", " ) as ?catheters) (COUNT (DISTINCT ?indv_catheter ) as ?n_catheters) 
WHERE {
	{
		# Organize alphabetically ?class_catheter
		SELECT ?indv_patient ?indv_catheter ?class_catheter
		WHERE {
			?indv_patient rdf:type :patient ;
				rdf:type ?class_patient .
			FILTER(?class_patient != owl:NamedIndividual) .
			?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
				rdf:type ?class_catheter .
			FILTER(?class_catheter != owl:NamedIndividual) .
			?indv_patient :person_has ?indv_catheter .
		}
		GROUP BY ?indv_patient ?indv_catheter ?class_catheter
		ORDER BY ?class_catheter
	}
}
GROUP BY ?indv_patient  
ORDER BY ?indv_patient
```

### Count the number of catheters located nearby/on/at/in an anatomical location and provide the types of catheters.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
# Group ?class_catheters alphabetically by ?indv_location
SELECT ?indv_location (GROUP_CONCAT (DISTINCT str(?class_catheter); separator=", " ) as ?catheters) (COUNT (DISTINCT ?indv_catheter ) as ?n_catheters) 
WHERE {
	{
		# Organize alphabetically ?class_catheter
		SELECT ?indv_location ?indv_catheter ?class_catheter
		WHERE {
			?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
				rdf:type ?class_catheter .
			FILTER(?class_catheter != owl:NamedIndividual) .
			?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
				rdf:type ?class_location .
			FILTER(?class_location != owl:NamedIndividual) .

			?indv_catheter :located_nearby_on_at_in ?indv_location .
		}
		GROUP BY ?indv_location ?indv_catheter ?class_catheter
		ORDER BY ?class_catheter
	}
}
GROUP BY ?indv_location
ORDER BY ?indv_location
```



# 7b. Where are the catheters in patientB? 

### List patients that have an anatomical location, a catheter, and the patient's catheter is located nearby/on/at/in the patient's anatomical location.
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?indv_catheter ?indv_location 
WHERE {
	?indv_patient rdf:type :patient ;
		rdf:type ?class_patient .
	FILTER(?class_patient != owl:NamedIndividual) .
	?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
		rdf:type ?class_catheter .
	FILTER(?class_catheter != owl:NamedIndividual) .
	?indv_location rdf:type/rdfs:subClassOf* :anatomical_location ;
		rdf:type ?class_location .
	FILTER(?class_location != owl:NamedIndividual) .
	?indv_patient :person_has ?indv_catheter ;
	    :person_has ?indv_location . 
	?indv_catheter :located_nearby_on_at_in ?indv_location .
}
ORDER BY ?indv_patient
```



# 7c. Why does patientB need the catheter(s)?

### List patients that have a catheter and the procedures which use that catheter. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?class_catheter (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures)
WHERE {
	{
		SELECT ?indv_patient ?class_catheter ?class_procedure
		WHERE {
			?indv_patient rdf:type :patient ;
				rdf:type ?class_patient .
			FILTER(?class_patient != owl:NamedIndividual) .
			?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
				rdf:type ?class_catheter .
			FILTER(?class_catheter != owl:NamedIndividual) .
			?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
				rdf:type ?class_procedure .
			FILTER(?class_procedure != owl:NamedIndividual) .
			?indv_patient :person_has ?indv_catheter .
			?indv_procedure :procedure_uses ?indv_catheter .
		}
		GROUP BY ?indv_patient ?class_catheter ?class_procedure
		ORDER BY ?class_procedure
	}
}
GROUP BY ?indv_patient ?class_catheter
ORDER BY ?indv_patient
```

### List patients that have a catheter, a procedure, and the patient's procedure uses the patient's catheter. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?class_catheter (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures)
WHERE {
	{
		SELECT ?indv_patient ?class_catheter ?class_procedure
		WHERE {
			?indv_patient rdf:type :patient ;
				rdf:type ?class_patient .
			FILTER(?class_patient != owl:NamedIndividual) .
			?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
				rdf:type ?class_catheter .
			FILTER(?class_catheter != owl:NamedIndividual) .
			?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
				rdf:type ?class_procedure .
			FILTER(?class_procedure != owl:NamedIndividual) .
			?indv_patient :person_has ?indv_catheter ;
    			:person_has ?indv_procedure .
			?indv_procedure :procedure_uses ?indv_catheter .
		}
		GROUP BY ?indv_patient ?class_catheter ?class_procedure
		ORDER BY ?class_procedure
	}
}
GROUP BY ?indv_patient ?class_catheter
ORDER BY ?indv_patient
```

### List patients that have a catheter and a procedure, and all catheters and all procedures the patient has. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient (GROUP_CONCAT (DISTINCT str(?class_catheter); separator=", " ) as ?catheters) (GROUP_CONCAT (DISTINCT str(?class_procedure); separator=", " ) as ?procedures)
WHERE {
	{
		SELECT ?indv_patient ?class_catheter ?class_procedure
		WHERE {
			?indv_patient rdf:type :patient ;
				rdf:type ?class_patient .
			FILTER(?class_patient != owl:NamedIndividual) .
			?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
				rdf:type ?class_catheter .
			FILTER(?class_catheter != owl:NamedIndividual) .
			?indv_procedure rdf:type/rdfs:subClassOf* :procedure ;
				rdf:type ?class_procedure .
			FILTER(?class_procedure != owl:NamedIndividual) .
			?indv_patient :person_has ?indv_catheter ;
    			:person_has ?indv_procedure .
		}
		GROUP BY ?indv_patient ?class_catheter ?class_procedure
		ORDER BY ?class_catheter ?class_procedure
	}
}
GROUP BY ?indv_patient 
ORDER BY ?indv_patient
```



# 8a. Does patientC have an infection and a catheter?

### List patients that have an infection and a catheter. 
```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX : <http://www.semanticweb.org/2022/04/aaenote#>
SELECT ?indv_patient ?class_catheter ?class_infection
WHERE {
	?indv_patient rdf:type :patient ;
		rdf:type ?class_patient .
	FILTER(?class_patient != owl:NamedIndividual) .
	?indv_catheter rdf:type/rdfs:subClassOf* :catheter ;
		rdf:type ?class_catheter .
	FILTER(?class_catheter != owl:NamedIndividual) .
	?indv_infection rdf:type :infection ;
		rdf:type ?class_infection .
	FILTER(?class_infection != owl:NamedIndividual) .
	?indv_patient :person_has ?indv_catheter ;
		:person_has ?indv_infection .
}
ORDER BY ?indv_patient
```



# 8b. Was patientC's infection associated with a catheter?
Cannot determine if a patient's infection is associated with a catheter unless that catheter is tested in the microbiology lab. 

