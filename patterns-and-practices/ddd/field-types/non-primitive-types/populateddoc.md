---
description: Describes how to deal with a cosmos db field type of PopulatedDoc
---

# PopulatedDoc

### Cosmos DB Model

{% code title="/infrastructure/data-sources/cosmos-db/models/physician.ts" %}
```typescript
updatedBy: PopulatedDoc<Account>;
```
{% endcode %}

### Domain Props

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
readonly updatedBy: AccountProps;
setUpdatedByRef: (updatedBy: AccountEntityReference) => void;
```
{% endcode %}

* Every PopulatedDoc field needs the read-only field with props type and a setRef field which is a method that takes in an entity reference of the domain object referenced in previous field
* The argument of setRef method should be the same as the name of the field above it

### Domain Entity Reference

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
export interface PhysicianEntityReference 
extends Readonly<
    Omit<
        PhysicianProps,
        "updatedBy" | "setUpdatedByRef"
        >
    > {
    readonly updatedBy: AccountEntityReference;
}
```
{% endcode %}

* Note: must include field name and setRef method name in the omit union of strings

### Domain Class

#### Getter

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
get updatedBy(): AccountEntityReference {
    return new Account(this.props.updatedBy, this.context);
}
```
{% endcode %}

#### Setter

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
public requestSetUpdatedBy(updatedBy: AccountEntityReference) {
    this.setUpdatedByRef(updatedBy);
}
```
{% endcode %}

* setRef method comes from field defined in Domain Props
* Note: setRef method takes in entity reference of domain object as an argument

### Domain Adapter

#### Getter

{% code title="/domain/infrastructure/persistence/physician.domain-adapter.ts" %}
```typescript
get updatedBy(): AccountProps {
    if (this.doc.updatedBy) {
        return new AccountDomainAdapter(this.doc.updatedBy);
    }
}
```
{% endcode %}

#### Setter

{% code title="/domain/infrastructure/persistence/physician.domain-adapter.ts" %}
```typescript
setUpdatedByRef(updatedBy: AccountEntityReference) {
    this.doc.set("updatedBy", updatedBy["props"]["doc"]);
}
```
{% endcode %}

* Needs a setter for the setRef method created in domain props
* takes in entity reference of domain object being stored in that field
* Note: use exactly the notation below for setting the doc: first argument is the field name as a string and the second is the argument being passed in to the function with square bracket notation accessing the doc of that domain object
