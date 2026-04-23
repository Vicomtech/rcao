# Resilient Collective Awareness Ontology (RCAO)

RCAO offers a minimal set of classes and relations to guide the representation of knowledge in RDF/OWL.

The ontoloy IRI is: [https://w3id.org/rcao/](https://w3id.org/rcao/)

Here's the RCAO Postcard showing all the classes and relations:<br>
<img src="rcao/sections/media/rcao-logo-min.png" alt="Unified Common Meaning of RCAO" width="800">

## Availability
The ontology is available in a number of formats:
* [Turtle](./rcao/latest/ontology.ttl)
* [RDF/XML](./rcao/latest/ontology.owl)
* [JSON-LD](./rcao/latest/ontology.jsonld)
* [NTriples](./rcao/latest/ontology.nt)


You can retrieve any of these through content type negotiation
```
curl -L -H "Accept: text/turtle https://w3id.org/rcao
curl -L -H "Accept: application/ld+json" https://w3id.org/rcao
curl -L -H "Accept: application/rdf+xml" https://w3id.org/rcao
curl -L -H "Accept: application/n-triples" https://w3id.org/rcao
```

In addition, individual versions of RCAO are available through the ontology IRI pattern ```https://w3id.org/rcao/sulo-X.X.X.ttl```  All versions of the ontology are available from [github](rcao/)


## Documentation
We automatically generate documentation for RCAO using OntoSpy, Pylode and Widoco wizard:

* [OntoSpy](https://vicomtech.github.io/rcao/docs/ontospy/html-multi-page/index.html)
* [Widoco](https://vicomtech.github.io/rcao/rcao/latest/index-en.html)

## Contributing

## Cite


## Project status
Work In Progress (WIP)

