## Description

The _Citation Typing Ontology_ (_CiTO_) makes it possible for authors (or others) to mark citation links and to capture their citation intent (e.g., `cito:extends`, `cito:usesMethodIn`, `cito:supports`) when someone cites a particular publication. 
In particular, CiTO allows one to create metadata describing citations that are distinct from metadata describing the cited works themselves, and permits the motives of an author when referring to another document to be captured.

CiTO contains just two main object properties, `cito:cites` and its inverse `cito:isCitedBy`, each of which has forty-one sub-properties, plus four additional generic object properties – i.e. `cito:shareAuthorWith`, `cito:sharesAuthorInstitutionWith`, `cito:sharesFundingAgencyWith` and `cito:likes` – that may be used even outside a citation. 
As defined in _Functions of Citations Ontology_, all these properties (and, consequently, their inverses) may be classified as rhetorical and/or factual, with the rhetorical properties being grouped in three sets depending on their connotation: positive, informative (or neutral) or negative. 
Note that all the domain and range constraints from the object properties are not defined, so that this ontology could be easily integrated with other models, e.g., [FaBiO](http://purl.org/spar/fabio).

CiTO makes available a mechanism that permits the citation itself to be reified, so that it can become the subject or object of other RDF statements. 
In particular, the reifying class is `cito:Citation`, and its accompanying object properties, i.e., `cito:hasCitingEntity`, `cito:hasCitationCharacterization` and `cito:hasCitedEntity`, can be employed to reify direct citation statements made using the CiTO citation object property `cito:cites` or one of its sub-properties.

## Examples of use

In the following subsections, we introduce some examples to showcase how to use CiTO. 

The prefixes that are used in all the examples provided below are defined as follows:

    @prefix : <http://www.sparontologies.net/example/> .
    @prefix c4o: <http://purl.org/spar/c4o/> .
    @prefix cito: <http://purl.org/spar/cito> .
    @prefix cnt: <http://www.w3.org/2011/content#> .
    @prefix oa: <http://www.w3.org/ns/oa#> .
    @prefix per: <http://data.semanticweb.org/person/> .

### Defining citation links and their functions

CiTO allows one to link two papers – or, more generally, two generic resources – where one (i.e., the citing paper) cites another one (i.e., the cited paper) according to a particular citation function (e.g., `cito:extends`, `cito:usesMethodIn`, `cito:supports`).

CiTO makes available two different approaches for creating such links. 
In the direct approach, we can use any of the CiTO properties as predicate of statements for defining citations. 
In the reified approach, we can define the citation as a proper individual of the class `cito:Citation`, in order to use it as subject/object of other statements.

    # Direct form for a citation
    :paper-a cito:extends :paper-b .

    # Reified form for a citation sharing the same
    # citation function of the above one
    :citation a cito:Citation ;
        cito:hasCitingEntity :paper-a ;
        cito:hasCitationCharacterization cito:extends ;
        cito:hasCitedEntity :paper-b .

### Annotating a citation with an additional text-defined citation function

Despite the extensive list of CiTO properties, there could be situations in which the purpose of making a citation cannot be adequately expressed using these CiTO properties. 
It is now possible to use the [Open Annotation Data Model](http://www.openannotation.org/spec/core/) to define the reason for, or the nature of, the citation.

In the [Open Annotation Data Model Ontology](http://www.w3.org/ns/oa), an annotation is described as a member of the class `oa:Annotation`, which has a body containing the annotation itself defined by `oa:hasBody`, and an annotation target (the thing to which the annotation relates) defined by `oa:hasTarget`.

In order to express a more precise justification of a citation, the target of the annotation should be an individual of the class `cito:Citation`, while the body, i.e. the textual content of the annotation itself, is described using the [W3C Content Vocabulary](http://www.w3.org/TR/Content-in-RDF10/) as an individual of the class `cnt:ContentAsText`, which the property `cnt:chars` relates to the text string actually providing the annotation. 
In addition, an OA annotation can be further characterized by the motivation for making such annotation, defined by `oa:motivatedBy`. 
In this case, the appropriate motivation is `oa:commenting`, an instance of the class `oa:Motivation`.

The whole example has been extracted from the blog post 'Extending CiTO to enable use of the Open Annotation Data Model to describe citations'.

    :annotation a oa:Annotation;
        oa:motivatedBy oa:commenting ;
        oa:hasBody :comment ;
        oa:hasTarget :citation .

    :comment a cnt:ContentAsText ;
        cnt:chars \"I\'m citing that paper because it
            initiated this whole new field of research.\" .

    :citation a cito:Citation;
        cito:hasCitingEntity :paper-a ;
        cito:hasCitationCharacterization cito:cites ;
        cito:hasCitedEntity :paper-b .

### Annotating an in-text reference pointer with a citation function

Several citations to the same article could exist in the same text, and such citations could express different functions depending on the author\'s view. 
Usually, in a scientific paper, each of these citations is actually described by means of an in-text reference pointers, i.e., a textual device (e.g., "[6]") denoting a single bibliographic reference (referring to the cited paper) that is embedded in the text of a document within the context of a particular sentence. 
One of the SPAR Ontologies, i.e., [C4O](https://www.sparontologies.net/ontologies/c4o), defines the entities for describing in-text reference pointers (individuals of the class `c4o:InTextReferencePointer`) and to link them to the related bibliographic references included in the paper.

The [Open Annotation Data Model Ontology](http://www.w3.org/ns/oa) can be used to annotate each in-text reference pointer of a paper with the particular citation it conveys. 
In particular, we can create an annotation (i.e., an individual of the class `oa:Annotation`) with a body (object property `oa:hasBody`) containing a specific citation among the citing paper and the cited paper, and with the in-text reference pointer as target (object property `oa:hasTarget`) of the annotation. 
In addition, it is also possible to specify (through the object property `oa:annotatedBy`) the agent who created such annotation – who can be the author of the paper containing the in-text reference pointer, a reader or a software agent.

    :annotation a oa:Annotation ;
        oa:hasBody :citation ;
        oa:hasTarget :in-text-ref-pointer ;
        oa:annotatedBy per:silvio-peroni .

    :citation a cito:Citation;
        cito:hasCitingEntity :paper-a ;
        cito:hasCitationEvent cito:extends ;
        cito:hasCitedEntity :paper-b .

    :in-text-ref-pointer a c4o:InTextReferencePointer ;
        c4o:hasContent '[6]' .

## Competency Questions

CiTO can be used for answering several questions related to citations, their intent and their overall context.

In the following subsections, some of them are introduced together with their respective SPARQL queries. 

The prefixes that are used in all the SPARQL queries provided below are defined as follows:

    PREFIX : <http://www.sparontologies.net/example/>
    PREFIX c4o: <http://purl.org/spar/c4o/>
    PREFIX cito: <http://purl.org/spar/cito>
    PREFIX cnt: <http://www.w3.org/2011/content#>
    PREFIX oa: <http://www.w3.org/ns/oa#>
    PREFIX per: <http://data.semanticweb.org/person/>

### CQ1

Which papers directly extend other papers?

    SELECT ?citingPaper ?citedPaper
    WHERE {
        ?citingPaper cito:extends ?citedPaper .
    }

### CQ2

What are the reified citations originated by a specific paper, and what are their citation functions?

    SELECT ?citation ?citedPaper ?characterization
    WHERE {
        ?citation a cito:Citation ;
            cito:hasCitingEntity :paper-a ;
            cito:hasCitedEntity ?citedPaper ;
            cito:hasCitationCharacterization ?characterization .
    }

### CQ3

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

### CQ4

Which in-text reference pointers have been annotated by a specific agent?

    SELECT ?pointer ?textValue
    WHERE {
        ?annotation a oa:Annotation ;
                    oa:annotatedBy per:silvio-peroni ;
                    oa:hasTarget ?pointer .
        
        ?pointer a c4o:InTextReferencePointer ;
                c4o:hasContent ?textValue .
    }

### CQ5

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