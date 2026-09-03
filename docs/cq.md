# Competency Questions

CiTO can be used for answering several questions related to citations, their intent and their overall context.

In the following subsections, some of them are introduced together with their respective SPARQL queries. 

The prefixes that are used in all the SPARQL queries provided below are defined as follows:

    PREFIX : <http://www.sparontologies.net/example/>
    PREFIX c4o: <http://purl.org/spar/c4o/>
    PREFIX cito: <http://purl.org/spar/cito>
    PREFIX cnt: <http://www.w3.org/2011/content#>
    PREFIX oa: <http://www.w3.org/ns/oa#>
    PREFIX per: <http://data.semanticweb.org/person/>

## CQ1

Which papers directly extend other papers?

    SELECT ?citingPaper ?citedPaper
    WHERE {
        ?citingPaper cito:extends ?citedPaper .
    }

## CQ2

What are the reified citations originated by a specific paper, and what are their citation functions?

    SELECT ?citation ?citedPaper ?characterization
    WHERE {
        ?citation a cito:Citation ;
            cito:hasCitingEntity :paper-a ;
            cito:hasCitedEntity ?citedPaper ;
            cito:hasCitationCharacterization ?characterization .
    }

## CQ3

What is the text of the motivational comment associated with a citation?

    SELECT ?citation ?commentText
    WHERE {
        ?annotation a oa:Annotation ;
                    oa:motivatedBy oa:commenting ;
                    oa:hasTarget ?citation ;
                    oa:hasBody ?comment .
        
        ?comment a cnt:ContentAsText ;
                cnt:chars ?commentText .
    }

## CQ4

Which in-text reference pointers have been annotated by a specific agent?

    SELECT ?pointer ?textValue
    WHERE {
        ?annotation a oa:Annotation ;
                    oa:annotatedBy per:silvio-peroni ;
                    oa:hasTarget ?pointer .
        
        ?pointer a c4o:InTextReferencePointer ;
                c4o:hasContent ?textValue .
    }

## CQ5

Which citations are linked to an in-text reference pointer, and what are the papers involved?

    SELECT ?pointer ?citation ?citingPaper ?citedPaper
    WHERE {
        ?annotation a oa:Annotation ;
                    oa:hasTarget ?pointer ;
                    oa:hasBody ?citation .
        
        ?pointer a c4o:InTextReferencePointer .
        
        ?citation a cito:Citation ;
                cito:hasCitingEntity ?citingPaper ;
                cito:hasCitedEntity ?citedPaper .
    }