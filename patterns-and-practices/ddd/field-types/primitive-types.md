---
description: Describes how to deal with primitive field types from Cosmos to Domain
---

# Primitive Types

Primitive type fields are a one-to-one copy from Cosmos DB model into the Domain props.

### Cosmos DB Model

{% code title="/infrastructure/data-sources/cosmos-db/models/physician.ts" %}
```typescript
lastName: string;
```
{% endcode %}

### Domain Props

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
lastName: string;
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/sideBySide (1).png" alt=""><figcaption><p>Domain Props on left, Cosmos DB Model on right</p></figcaption></figure>

* Don't need id field on Domain Props side because already defined in EntityProps
* Every field in domain props is required regardless of if it's optional on cosmos db side

### Domain Entity Reference

```typescript
export interface PhysicianEntityReference extends Readonly<PhysicianProps> {} 
```

* primitive types are copied automatically as read-only in entity reference&#x20;
* no need to omit primitive type fields from entity reference like you would for non-primitive type
* if domain object only has primitive types, entity reference should look exactly as above

### Domain Class

* Primitive fields require getter and setter methods in the domain class of the object

#### Getter

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
get lastName() {
    return this.props.lastName;
}
```
{% endcode %}

#### Setter

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
public requestSetLastName(lastName: string) {
    this.props.lastName = lastName;
}
```
{% endcode %}

### Domain Adapter

* Primitive fields require getter and setter methods in the domain adapter of the object

#### Getter

{% code title="/domain/infrastructure/persistence/physician.domain-adapter.ts" %}
```typescript
get lastName() {
    return this.doc.lastName;
}
```
{% endcode %}

#### Setter

{% code title="/domain/infrastructure/persistence/physician.domain-adapter.ts" %}
```typescript
set lastName(lastName) {
    this.doc.lastName = lastName;
}
```
{% endcode %}
