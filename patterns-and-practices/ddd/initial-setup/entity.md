---
description: >-
  If the cosmos db interface extends SubdocumentBase, it is an Entity on domain
  side
---

# Entity

### Cosmos DB Model

{% code title="/infrastructure/data-sources/cosmos-db/models/physician.ts" %}
```typescript
export interface PhysicianLicense extends SubdocumentBase {
```
{% endcode %}

Each entity **requires:**

* Domain Context file
* Domain Adapter

### Domain Context

{% code title="/domain/contexts/physician/physician-license.ts" %}
```typescript
import { Entity, EntityProps } from "../../shared/entity";
import { DomainExecutionContext } from "../context";

export interface PhysicianLicenseProps extends EntityProps {
  // Add fields according to the model
}

export interface PhysicianLicenseEntityReference extends Readonly<PhysicianLicenseProps> {}

export class PhysicianLicense<props extends PhysicianLicenseProps> 
  extends Entity<props> implements PhysicianLicenseEntityReference {
  constructor(props: props, private readonly context: DomainExecutionContext) { 
    super(props);
  }
  // Add getters and setters for the field that was mentioned/defined above
}
```
{% endcode %}

* domain context file in same directory as context file of aggregate root it belongs to
* in this example, physician-license.ts is placed in contexts/physician/ directory alongside physician.ts context file

### Domain Adapter

{% code title="/domain/infrastructure/persistence/physician.domain-adapter.ts" %}
```typescript
class PhysicianLicenseDomainAdapter implements PhysicianLicenseProps {
  constructor(public readonly doc: PhysicianLicense) {}
  get id() { return this.doc.id.valueOf() as string; }
  
  // Add getters and setters for the field that was mentioned/defined in Domain Context file
}
```
{% endcode %}

* domain adapter in same file as domain adapter of aggregate root
* if too many adapters in one file, you can split out the domain adapters into multiple files as needed
* in this example, PhysicianLicenseDomainAdapter goes in physician.domain-adapter.ts
