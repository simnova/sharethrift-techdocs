---
description: >-
  If the cosmos db interface extends NestedPath, it is a Value Object on domain
  side
---

# Value Object

### Cosmos DB Model

{% code title="/infrastructure/data-sources/cosmos-db/models/minicex.ts" %}
```typescript
export interface MinicexRequest extends NestedPath {
```
{% endcode %}

Each value object **requires:**

* Domain Context file
* Domain Adapter

### Domain Context

{% code title="/domain/contexts/new-application/minicex-request.ts" %}
```typescript
import { ValueObject, ValueObjectProps } from "../../shared/value-object";
import { DomainExecutionContext } from "../context";

export interface MinicexRequestProps extends ValueObjectProps{
  // Add fields according to the model
}

export interface MinicexRequestEntityReference extends Readonly<MinicexRequestProps> {}

export class MinicexRequest
  extends ValueObject<MinicexRequestProps> implements MinicexRequestEntityReference {
  constructor(props: props, private readonly context: DomainExecutionContext) { 
    super(props);
  }
  // Add getters and setters for the field that was mentioned/defined above
}
```
{% endcode %}

* domain context file in same directory as context file of aggregate root it belongs to
* in this example, minicex.ts is in /domain/contexts/new-application/ so minicex-request.ts is also in new-application/ directory

### Domain Adapter

```typescript
export class MinicexRequestDomainAdapter implements MinicexRequestProps {
  constructor(private readonly props: MinicexRequest) {}
  
  // Add getters and setters for the field that was mentioned/defined in Domain Context file
}
```

* domain adapter in same file as domain adapter of aggregate root
* if too many adapters in one file, can split out into multiple files as needed
* in this example, MinicexRequestDomainAdapter goes in minicex.domain-adapter.ts
* Note: argument in constructor is called props instead of doc
