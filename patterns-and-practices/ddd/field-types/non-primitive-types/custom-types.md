---
description: >-
  Describes how to deal with a cosmos db fields of custom types local to that
  domain object
---

# Custom Types

### Cosmos DB Model

{% code title="/infrastructure/data-sources/cosmos-db/models/minicex.ts" %}
```typescript
review?: MinicexReview;
```
{% endcode %}

### Domain Props

{% code title="/domain/contexts/new-application/minicex.ts" %}
```typescript
readonly review: MinicexReviewProps;
```
{% endcode %}

* Note: Custom types local to aggregate root like MinicexReview do not need a setRef method
* Only need setRef for PopulatedDoc field types which refer to other aggregate roots not local to the domain object

### Domain Entity Reference

{% code title="/domain/contexts/new-application/minicex.ts" %}
```typescript
export interface MinicexEntityReference 
extends Readonly<
    Omit<
        MinicexProps,
        "review"
        >
    > {
    readonly review: MinicexReviewEntityReference;
}
```
{% endcode %}

### Domain Class

#### Getter

{% code title="/domain/contexts/new-application/minicex.ts" %}
```typescript
get review(): MinicexReviewEntityReference {
    return new MinicexReview(this.props.review, this.context);
}
```
{% endcode %}

#### Setter

* Note: only need setter method if you have a setRef method for that field
* Otherwise setting the props of the custom type is handled in that custom type's domain class

### Domain Adapter

#### Getter

{% code title="/domain/infrastrucure/persistence/minicex.domain-adapter.ts" %}
```typescript
get review()
    if (this.doc.review) {
        return new MinicexReviewDomainAdapter(this.doc.review);
    }
}
```
{% endcode %}

#### Setter

* Note: no setter needed for custom types local to the aggregate root

