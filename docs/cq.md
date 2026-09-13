## Competency Questions

BiDO can be used for answering several questions related to bibliometric data.

In the following subsections, some of them are introduced together with their respective SPARQL queries. 

The prefixes that are used in all the SPARQL queries provided below are defined as follows:

    PREFIX bido: <http://purl.org/spar/bido/>
    PREFIX foaf: <http://xmlns.com/foaf/0.1/>
    PREFIX tvc: <http://www.essepuntato.it/2012/04/tvc/>
    PREFIX time: <http://www.w3.org/2006/time#>

### CQ1

What numeric bibliometric data are held by an entity, and according to which source?

    SELECT ?entity ?dataSituation ?source ?measure ?numericValue
    WHERE {
        ?entity bido:holdsBibliometricDataInTime ?dataSituation .
        ?dataSituation a bido:BibliometricDataInTime ;
            bido:withBibliometricData ?bibliometricData .
        ?bibliometricData a bido:NumericBibliometricData ;
            bido:hasMeasure ?measure ;
            bido:hasNumericValue ?numericValue .
        OPTIONAL { ?dataSituation bido:accordingTo ?source . }
    }

### CQ2

What is the validity time interval for bibliometric data held in time?

    SELECT ?entity ?dataSituation ?startDate ?endDate
    WHERE {
        ?entity bido:holdsBibliometricDataInTime ?dataSituation .
        ?dataSituation a bido:BibliometricDataInTime ;
            tvc:atTime ?timeInterval .
        OPTIONAL { ?timeInterval time:hasBeginning ?startDate . }
        OPTIONAL { ?timeInterval time:hasEnd ?endDate . }
    }

### CQ3

Which research career category and detailed trajectory features are assigned to a person?

    SELECT ?person ?category ?trend ?slopeStrength ?slopeGrowth ?orderOfMagnitude ?period
    WHERE {
        ?person a foaf:Person ;
            bido:holdsBibliometricDataInTime ?dataSituation .
        ?dataSituation bido:withBibliometricData ?category .
        ?category a bido:ResearchCareerCategory .
        OPTIONAL {
            ?category bido:hasCurve ?curve .
            ?curve bido:hasTrend ?trend .
        }
        OPTIONAL {
            ?category bido:hasSlope ?slope .
            ?slope bido:hasStrength ?slopeStrength ;
                bido:hasGrowth ?slopeGrowth .
        }
        OPTIONAL { ?category bido:hasOrderOfMagnitude ?orderOfMagnitude . }
        OPTIONAL { ?category bido:concernsResearchPeriod ?period . }
    }

### CQ4

Which algorithms or organizations produce or evaluate bibliometric data held by an entity?

    SELECT ?entity ?dataSituation ?source ?sourceType
    WHERE {
        ?entity bido:holdsBibliometricDataInTime ?dataSituation .
        ?dataSituation a bido:BibliometricDataInTime ;
            bido:accordingTo ?source .
        OPTIONAL { ?source a ?sourceType . }
    }

### CQ5

What categorical bibliometric rankings or measures (such as conference ranks or categorical reviews) are assigned to an entity?

    SELECT ?entity ?dataSituation ?bibliometricData ?source
    WHERE {
        ?entity bido:holdsBibliometricDataInTime ?dataSituation .
        ?dataSituation a bido:BibliometricDataInTime ;
            bido:withBibliometricData ?bibliometricData .
        OPTIONAL { ?dataSituation bido:accordingTo ?source . }
        FILTER NOT EXISTS { ?bibliometricData a bido:NumericBibliometricData . }
    }