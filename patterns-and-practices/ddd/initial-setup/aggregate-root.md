---
description: >-
  If the cosmos db interface extends Base, it is an Aggregate Root on domain
  side
---

# Aggregate Root

### Cosmos DB Model

{% code title="/infrastructure/data-sources/cosmos-db/models/physician.ts" %}
```typescript
export interface Physician extends Base {
```
{% endcode %}

Each aggregate root **requires:**

* Domain context file
* Domain repository file
* Domain adapter file
* Mongo repository file
* Unit of Work file

### Domain Context

{% code title="/domain/contexts/physician/physician.ts" %}
```typescript
import { AggregateRoot } from "../../shared/aggregate-root";
import { EntityProps } from "../../shared/entity";
import { DomainExecutionContext } from "../context";

export interface PhysicianProps extends EntityProps {
  // Add fields according to the model
}

export interface PhysicianEntityReference extends Readonly<PhysicianProps> {}

export class Physician<props extends PhysicianProps> 
  extends AggregateRoot<props> implements PhysicianEntityReference {
  constructor(props: props, private readonly context: DomainExecutionContext) { 
    super(props);
  }
  // Add getters and setters for the field that was mentioned/defined above
}
```
{% endcode %}

* domain context file in the /domain/context/\<name>/ directory
* contains domain props, entity reference, and domain class

### Domain Repository

{% code title="/domain/contexts/physician/physician.repository.ts" %}
```typescript
import { Repository } from "../../shared/repository";
import { Physician, PhysicianProps } from "./physician";

export interface PhysicianRepository<props extends PhysicianProps> 
    extends Repository<Physician<props>> {}
```
{% endcode %}

* domain repository file in same directory as context file
* every repository should look like above, only thing that changes is domain object name

### Domain Adapter

{% code title="/domain/infrastructure/persistence/physician.domain-adapter.ts" %}
```typescript
import {...

export class PhysicianConverter extends MongoTypeConverter<
  DomainExecutionContext,
  Physician,
  PhysicianDomainAdapter,
  PhysicianDO<PhysicianDomainAdapter>
> {
  constructor() {
    super(PhysicianDomainAdapter, PhysicianDO);
  }
}

export class PhysicianDomainAdapter 
  extends MongooseDomainAdapter<Physician> implements PhysicianProps {
  constructor(doc: Physician) {
    super(doc);
  }
}
```
{% endcode %}

* domain adapter file in /domain/infrastructure/persistence/ directory
* contains getters and setters for fields in domain props
* **PhysicianDO** is Physician class from domain context file in /domain/contexts/physician/
* **Physician** is Physician schema model from cosmos db file in /infrastructure/data-sources/cosmos-db/models/

### Mongo Repository

{% code title="/domain/infrastructure/persistence/physician.mongo-repository.ts" %}
```typescript
import {...

export class PhysicianMongoRepository<PropType extends PhysicianProps>
  extends MongoRepositoryBase<
    DomainExecutionContext, 
    Physician, 
    PropType, 
    PhysicianDO<PropType>
  >
  implements PhysicianRepository<PropType>
{
  constructor(
    eventBus: EventBus,
    modelType: typeof PhysicianModel,
    typeConverter: TypeConverter<Physician, PhysicianDO<PropType>, PropType, DomainExecutionContext>,
    session: ClientSession,
    context: DomainExecutionContext
  ) {
    super(eventBus, modelType, typeConverter, session, context);
  }
}
```
{% endcode %}

* mongo repository file in /domain/infrastructure/persistence/ directory
* every mongo repo should start out the same as above, only the domain name changes
* Additional methods will be added as needed per business logic

### Unit of Work

{% code title="/domain/infrastructure/persistence/physician.uow.ts" %}
```typescript
import { NodeEventBus } from "../core/events/node-event-bus";
import { InProcEventBus } from "../core/events/in-proc-event-bus";
import { MongoUnitOfWork } from "../core/mongo/mongo-unit-of-work";

import { PhysicianModel } from "../../../infrastructure/data-sources/cosmos-db/models/physician";
import { PhysicianConverter } from "./physician.domain-adapter";
import { PhysicianMongoRepository } from "./physician.mongo-repository";

export const PhysicianUnitOfWork = new MongoUnitOfWork(
                                InProcEventBus, 
                                NodeEventBus, 
                                PhysicianModel, 
                                new PhysicianConverter(), 
                                PhysicianMongoRepository);
```
{% endcode %}

* unit of work file in /domain/infrastructure/persistence/ directory
* every unit of work file should look the same as above, only the domain name changes
