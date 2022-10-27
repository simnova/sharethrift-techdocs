---
description: Describes how to deal with a cosmos db field type of Types.DocumentArray
---

# Types.DocumentArray

### Cosmos DB Model

{% code title="/infrastructure/data-sources/cosmos-db/models/physician.ts" %}
```typescript
licenses: Types.DocumentArray<PhysicianLicense>;
```
{% endcode %}

### Domain Props

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
readonly licenses: PropArray<PhysicianLicenseProps>;
```
{% endcode %}

### Domain Entity Reference

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
export interface PhysicianEntityReference
    extends Readonly<
        Omit<
            PhysicianProps,
            "licenses"
        >
    > {
    readonly licenses: ReadonlyArray<PhysicianLicenseEntityReference>;
}
```
{% endcode %}

### Domain Class

#### Getter

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
get licenses(): ReadonlyArray<PhysicianLicenseEntityReference> {
    return this.props.licenses.items.map(
        (license) => new PhysicianLicense(license, this.context)
    );
}
```
{% endcode %}

#### Setter

* No setter for domain objects local to the aggregate root (Entity or ValueObject)
* Only need setRef method for PopulatedDoc type fields

### Domain Adapter

#### Getter

{% code title="/domain/infrastructure/persistence/physician.domain-adapter.ts" %}
```typescript
get licenses() {
    if (this.doc.licenses) {
        return new MongoosePropArray(this.doc.licenses, PhysicianLicenseDomainAdapter);
    }
}
```
{% endcode %}

* Note: PhysicianLicenseDomainAdapter should exist in same file as PhysicianDomainAdapter

#### Setter

* No setter for Types.DocumentArray type fields
* Wil be handled in domain adapter for that domain context, PhysicianLicenseDomainAdapter
