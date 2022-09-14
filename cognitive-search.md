---
description: Useful Vocabulary For Cognitive Search
---

# Cognitive Search

Documentation: [https://docs.microsoft.com/en-us/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest](https://docs.microsoft.com/en-us/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest)

### What is Cognitive Search?

It is a search as a service that gives a developer an API to add a search experience through a user defined index

### Index definition&#x20;

A user defined Object that lets cognitive search know what to return based on certain fields&#x20;

**Fields Include:**&#x20;

* Searchable - Allows a field to be searched
* Facetable - Allows a field to be counted
* Retrievable - Cannot be searched, but the field name will be returned
* Filterable - Indicates whether to enable the field to be referenced in $filter queries
* Sortable - Indicates whether to enable the field to be referenced in $orderby expressions&#x20;

### Important Classes

**SearchClient**

* Searching your indexed documents using rich queries and powerful data shaping&#x20;
* Autocompleting partially typed search terms based on documents in the index&#x20;
* Suggesting the most likely matching text in documents as a user types&#x20;
* Adding, Updating or Deleting Documents documents from an index&#x20;

**SearchIndexClient**&#x20;

* Create, delete, update, or configure a search index
* Declare custom synonym maps to expand or rewrite queries

**SearchIndexerClient**

* Start indexers to automatically crawl data sources
* Define AI powered Skillsets to transform and enrich your data

### Consuming Cognitive Search

* Create an index for a document using **SearchIndexClient**.
* Get/Search a document from an index using **SearchClient**
* Add documents into an index: based on the index definition, the library will look for the documents in the data source to add it to the index.

