---
description: >-
  These refer to domain context classes which correspond to a cosmos db model
  schema. Each collection in database should have an aggregate root in the
  domain layer of the app.
---

# Aggregate Root

## Cosmos DB Model

{% code title="/src/infrastructure-services-impl/datastore/mongodb/models/service.ts" %}
```typescript
export interface Service extends Base {
  serviceName: string;
  description: string;
  community: PopulatedDoc<Community.Community> | ObjectId;
  isActive: boolean;
}

export const ServiceModel = model<Service>('Service',new Schema<Service, Model<Service>, Service>(
  {
    schemaVersion: { type: String, default: '1.0.0' },
    serviceName: { type: String, required: true, maxlength: 100 },
    description: { type: String, required: false, maxlength: 500 },
    community: { type: Schema.Types.ObjectId, ref:Community.CommunityModel.modelName, required: true, index: true },    
    isActive: { type: Boolean, required: true, default: true },
    createdAt: { type: Date, default: Date.now },
    updatedAt: { type: Date, default: Date.now }
  },
  {
    timestamps: true, 
    versionKey: 'version',
  }
  ).index(
    { serviceName: 1, community: 1 }, { unique: true }
  )
);
```
{% endcode %}

Above is an example of a simple mongodb schema model for the _Service_ aggregate root in owner community. Each aggregate root requires a schema file in _/src/infrastructure-services-impl/datastore/mongodb/models/_

Each schema file requires a type interface named after the aggregate root which extends the seedwork _Base_ type interface. This interface should contain all of the required fields laid out in the schema specs.&#x20;

In the same file, you also need to define a SchemaModel which uses the model function and Schema constructor from mongoose. The same fields defined in the interface need to be provided in the Schema constructor.&#x20;

This is where the field-level constraints on the schema go. These should be provided in the document you are working off for for the schema specs.  Typically, we put required flag here, max length requirements and it's also used to refer to reference field types, subdocument types, and nested path types.

There is an additional object to set timestamps and versionKey. timestamps provides createdAt and updatedAt fields automatically so no need to define them on the type interface or schema model. versionKey adds an \_v field on the document for versioning.

You can optionally index fields and set unique constraints at the end.&#x20;

#### Primitive Fields

```typescript
serviceName: string;
description?: string;
```

For primitive fields, these are straightforward typings. If the field is optional on the schema specs, you can mark it optional on the interface using the question mark notation.

```typescript
serviceName: { type: String, required: true, maxlength: 100 },
description: { type: String, required: false, maxlength: 500 },
```

#### Reference Fields

```typescript
community: PopulatedDoc<Community.Community> | ObjectId;
```

Reference fields refer to fields which store an _ObjectID_ to another aggregate root in a different collection. In order to type this on the interface, we use a union typing of _PopulatedDoc_ and _ObjectID_, both coming from mongoose. Inside of the _PopulatedDoc_, we pass in the imported schema of the aggregate root the reference field refers to. In this case, we import the _Community_ schema from the community models file.

```typescript
community: { type: Schema.Types.ObjectId, ref: Community.CommunityModel.modelName, required: true, index: true },
```

Note the typing of _ObjectId_ coming from mongoose and the additional field for ref which is using the model imported from the _Community_ schema file.&#x20;

#### Nested Path Fields

```typescript
revisionRequest?: ServiceTicketRevisionRequest;
```

For nested path fields, you will need to define another type interface for that schema object. See below for an example of the interface type signature. Make sure to extend _NestedPath_ from a seedwork file.

```typescript
export interface ServiceTicketRevisionRequest extends NestedPath {
  requestedAt: Date;
  requestedBy: PopulatedDoc<Member.Member>;
  revisionSummary: string;
  requestedChanges: ServiceTicketRevisionRequestChanges;
  revisionSubmittedAt?: Date;
}

export const ServiceTicketRevisionRequestType = {
  requestedAt: { type: Date, required: true },
  requestedBy: { type: Schema.Types.ObjectId, ref: Member.MemberModel.modelName, required: true },
  revisionSummary: { type: String, required: true },
  requestedChanges: { type: ServiceTicketRevisionRequestChangesType, required: true, ...NestedPathOptions },
  revisionSubmittedAt: {type: Date, required: false}
}
```

You will need to define both a type interface and a schema model type for each nested path field. The same rules apply for the interface and schema model as for aggregate roots. Follow the same patterns depending on the types of fields defined on the nested path object.

```typescript
profile: { type: ServiceTicketRevisionRequestType, required: false, ...NestedPathOptions },
```

On the aggregate root object, you can specify the type as the schema type you created for the nested path as shown above. Make to sure to include the _NestedPathOptions_ imported from a seedwork file to remove the \_id field from all nested path objects.&#x20;

**Note**: _NestedPathOptions_ needs to be provided for every nested path, even sub nested path fields as demonstrated with _requestedChanges_ in _ServiceTicketRevisionRequestType_ in the example above.

Subdocument Fields

Subdocument fields are fields that are arrays which contain objects instead of primitive types. A subdocument is necessary instead of a nested path as we need an \_id field on each object in the array to be able to keep track of them and use them for updating the correct object in the array.

```typescript
messages: Types.DocumentArray<ServiceTicketMessage>;
```

On the aggregate root type interface, we use Types.DocumentArray from mongoose and similar to nested path fields, we need to define a type interface and schema model type for each subdocument.&#x20;

See below for an example subdocument type interface and schema model. Note that type interface extends SubdocumentBase from a seedwork file and the schema model use the _Schema_ constructor from mongoose similar to aggregate roots.

Also be sure to include the required _id_ field, which is of type _ObjectId_ coming from mongoose, on all subdocument type interfaces

```typescript
export interface ServiceTicketMessage extends SubdocumentBase {
  id: ObjectId;
  sentBy: string;
  initiatedBy?: PopulatedDoc<Member.Member>;
  message: string;
  embedding?: string;
  createdAt: Date;
  isHiddenFromApplicant: boolean;
}

const ServiceTicketMessageSchema = new Schema<ServiceTicketMessage, Model<ServiceTicketMessage>, ServiceTicketMessage>({
  sentBy: { type: String, required: true, enum: ['external', 'internal'] },
  initiatedBy: { type: Schema.Types.ObjectId, ref: Member.MemberModel.modelName, required: false, index: true },
  message: { type: String, required: true, maxlength: 2000 },
  embedding: { type: String, required: false, maxlength: 2000 },
  createdAt: { type: Date, default: Date.now },
  isHiddenFromApplicant: { type: Boolean, required: true, default: false },
});
```

On the aggregate root schema model, you can simply wrap the schema model for the subdocument in square brackets to set the type of the subdocument field.

```typescript
messages: [ServiceTicketMessageSchema]
```

## File Requirements

Each aggregate root **requires:**

* Domain context file&#x20;
  * Domain context files for subdocuments ([Entity](entity.md)) and nested paths ([ValueObject](value-object.md))
* Domain repository file
* Domain unit of work file
* Domain adapter file
  * Domain adapter classes for subdocuments ([Entity](entity.md)) and nested paths ([ValueObject](value-object.md))
* Mongo repository file
* Mongo unit of work file

## Domain Context

#### Convention: \<aggregate-root>.ts

{% code title="/src/app/domain/contexts/physician/physician.ts" %}
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

Each domain context corresponds to a schema interface, and contains one Props type interface, one EntityReference interface and one domain context class.&#x20;

The naming conventions for each are:

* \<aggregate-root>Props
* \<aggregate-root>EntityReference
* \<aggregate-root>

\<aggregate-root> in the example above would be **Physician**.

### Props

{% code title="src/app/domain/contexts/cases/service-ticket/service-ticket.ts" %}
```typescript
export interface ServiceTicketV1Props extends EntityProps {
  readonly community: CommunityProps;
  setCommunityRef(community: CommunityEntityReference): void;
  readonly property: PropertyProps;
  setPropertyRef(property: PropertyEntityReference): void;
  title: string;
  description: string;
  readonly ticketType?: string;
  status: string;
  priority: number;
  readonly activityLog: PropArray<ActivityDetailProps>;
  readonly messages: PropArray<ServiceTicketV1MessageProps>;
  readonly revisionRequest?: ServiceTicketV1RevisionRequestProps;
  readonly photos: PropArray<PhotoProps>;

  readonly createdAt: Date;
  readonly updatedAt: Date;
  readonly schemaVersion: string;

  hash: string;
  lastIndexed: Date; // success
  updateIndexFailedDate: Date; // failure
}
```
{% endcode %}

Here is an example of how to setup a Props interface for any domain context class, whether it be an AggregateRoot, Entity, or a ValueObject. The idea is we are matching the schema model defined in infrastructure layer one-to-one. So each field on the schema needs to be added on the Props interface.

#### Primitive Fields

For primitive fields such as strings, numbers, booleans, and Dates, we can type them as normal primitives on the Props interface. Examples include title, description, priority, and createdAt.

#### Reference Fields

For reference fields, which contain an _ObjectID_ which refers to an aggregate root of another type, we type that field using the Props interface of that domain object class and set it to readonly.&#x20;

For example, in the interface above, the community field is a reference field on _ServiceTicketV1_ to the _Community_ class. So on the interface, we set community field as _readonly_ and give it the type _CommunityProps_, which is imported from the domain context file for the community aggregate root.

We also need to include an additional field on the Props interface for reference fields. This field is a function which follows the naming convention of set\<ReferenceFieldName>Ref&#x20;

The function takes in an entity reference of the field it is assigning. In the example above, this extra field is called _setCommunityRef_ and its type is _(community: CommunityEntityReference) => void_

#### Prop Array Fields

For prop array fields, we need to set the field as _readonly_ and type it with the _PropArray_ type which takes in Props type interface of the object being stored in the array. In the example above, the messages field is set as _readonly_ and typed with _PropArray\<ServiceTicketV1MessageProps>_

#### Nested Path Fields

For nested path fields, we need to set the field as readonly and type it with the Props type interface for that nested path's domain context file. In the example above, _revisionRequest_ is a nested path so we set it to _readonly_ and assign it the _ServiceTicketV1RevisionRequestProps_ type interface, which is imported from the domain context file for the service ticket v1 revision request nested path

### Entity Reference

```typescript
export interface ServiceTicketV1EntityReference
  extends Readonly<
    Omit<
      ServiceTicketV1Props,
      | 'community'
      | 'setCommunityRef'
      | 'property'
      | 'setPropertyRef'
      | 'requestor'
      | 'setRequestorRef'
      | 'assignedTo'
      | 'setAssignedToRef'
      | 'service'
      | 'setServiceRef'
      | 'activityLog'
      | 'messages'
      | 'photos'
      | 'revisionRequest'
    >
  > {
  readonly community: CommunityEntityReference;
  readonly property: PropertyEntityReference;
  readonly requestor: MemberEntityReference;
  readonly assignedTo: MemberEntityReference;
  readonly service: ServiceEntityReference;
  readonly activityLog: ReadonlyArray<ActivityDetailEntityReference>;
  readonly messages: ReadonlyArray<ServiceTicketV1MessageEntityReference>;
  readonly photos: ReadonlyArray<PhotoEntityReference>;
  readonly revisionRequest: ServiceTicketV1RevisionRequestEntityReference;
}
```

The Entity Reference is a required type interface which strips off complex fields like reference fields, nested path fields, and prop array fields.&#x20;

Here is an example of how to setup the ServiceTicketV1EntityReference based on the ServiceTicketV1Props type interface from above.&#x20;

#### Reference Fields

For reference fields, omit the reference field and set\<ReferenceFieldName>Ref field from the props interface. Inside of the interface, define a readonly field with the same name as the reference field and assign it to the EntityReference type of the domain context class it refers to.

In the above example, **community** is a reference field so we omit '_community_' and '_setCommunityRef_' from the _ServiceTicketV1Props_ interface and we have a _readonly community_ field assigned to _CommunityEntityReference_ type.

#### Prop Array Fields

For prop array fields, omit the field from the props interface. Inside of the interface, define a readonly field with the same name as the object array field, assign it to the type of PropArray, and pass in the entity reference of the object being stored in that prop array.

In the example above, messages is an object array field so we omit '_messages_' from the _ServiceTicketV1Props_ interface, and inside of the interface, we define a _readonly_ _messages_ field and assign it to the type _PropArray\<ServiceTicketV1MessageEntityReference>_

#### Nested Path Fields

For nested path fields, omit the field from the props interface and inside of the interface, define a readonly field with the same name as the nested path field and assign it the type of the entity reference of the object being stored in that nested path.

In the example above, revisionRequest is a nested path field so we omit '_revisionRequest_' from the ServiceTicketV1Props interface, and inside of the interface, we define a _readonly revisionRequest_ field and assign it the type of _ServiceTicketV1RevisionRequestEntityReference_

### Class

```typescript
export class ServiceTicketV1<props extends ServiceTicketV1Props> extends AggregateRoot<props> implements ServiceTicketV1EntityReference {
  private isNew: boolean = false;
  private readonly visa: ServiceTicketV1Visa;
  constructor(props: props, private context: DomainExecutionContext) {
    super(props);
    this.visa = context.domainVisa.forServiceTicketV1(this);
  }
```

The domain context class contains the business logic for updating fields for that schema model. They contain getter and setter methods for the fields defined on the props interface.&#x20;

For aggregate root domain context classes, the class extends a seedwork class called _AggregateRoot_, as shown above. It also types the Props interface as a generic on the class. The class also implements the Entity Reference interface.

Each class will also have a few private fields on the class and constructor. Each class needs a context and a visa for permissions checks. An additional boolean field called isNew is required and is initially set to false. This flag is used to bypass some permissions checks on certain fields that are set in the static getNewInstance method.

#### Static getNewInstance Method

```typescript
public static getNewInstance<props extends ServiceTicketV1Props>(
    newProps: props,
    title: string,
    description: string,
    community: CommunityEntityReference,
    property: PropertyEntityReference,
    requestor: MemberEntityReference,
    context: DomainExecutionContext
  ): ServiceTicketV1<props> {
    let serviceTicket = new ServiceTicketV1(newProps, context);
    serviceTicket.MarkAsNew();
    serviceTicket.Title = title;
    serviceTicket.Description = description;
    serviceTicket.Community = community;
    serviceTicket.Property = property;
    serviceTicket.Requestor = requestor;
    serviceTicket.Status = ValueObjects.StatusCodes.Draft;
    serviceTicket.Priority = 5;
    let newActivity = serviceTicket.requestNewActivityDetail();
    newActivity.ActivityType = ActivityDetailValueObjects.ActivityTypeCodes.Created;
    newActivity.ActivityDescription = 'Created';
    newActivity.ActivityBy = requestor;
    serviceTicket.isNew = false;
    return serviceTicket;
}

private MarkAsNew(): void {
  this.isNew = true;
  this.addIntegrationEvent(ServiceTicketV1CreatedEvent, { id: this.props.id });
}
```

Each Aggregate Root class requires a static getNewInstance method which will always take in newProps which are typed to the Props interface for this domain context and a context which is of type DomainExecutionContext. The rest of the parameters are dependent on the required fields on the schema model that are necssary to a create the object and save it to database.&#x20;

Inside of the method, the constructor for the domain context is invoked passing in the props and context. Then, it calls the private MarkAsNew method which toggles the isNew boolean flag which allows for the following setters to bypass the usual permissions checks. The MarkAsNew method may also add an integration event related to that domain object's creation. Once all of the required setters are called, the isNew flag is toggled back to false and the new domain context class instance is returned.

#### Getters

```typescript

  get community() {
    return new Community(this.props.community, this.context);
  }
  get property() {
    return new Property(this.props.property, this.context);
  }
  get requestor() {
    return new Member(this.props.requestor, this.context);
  }
  get assignedTo() {
    return this.props.assignedTo ? new Member(this.props.assignedTo, this.context) : undefined;
  }
  get service() {
    return this.props.service ? new Service(this.props.service, this.context) : undefined;
  }
  get title() {
    return this.props.title;
  }
  get description() {
    return this.props.description;
  }
  get ticketType() {
    return this.props.ticketType;
  }
  get status() {
    return this.props.status;
  }
  get priority() {
    return this.props.priority;
  }
  get activityLog(): ReadonlyArray<ActivityDetailEntityReference> {
    return this.props.activityLog.items.map((a) => new ActivityDetail(a, this.context, this.visa));
  }
  get messages(): ReadonlyArray<ServiceTicketV1Message> {
    return this.props.messages.items.map((m) => new ServiceTicketV1Message(m, this.context, this.visa));
  }
  get photos(): ReadonlyArray<PhotoEntityReference> {
    return this.props.photos.items.map((p) => new Photo(p, this.context, this.visa));
  }
  get createdAt(): Date {
    return this.props.createdAt;
  }
  get updatedAt(): Date {
    return this.props.updatedAt;
  }
  get schemaVersion(): string {
    return this.props.schemaVersion;
  }

  get hash() {
    return this.props.hash;
  }

  get lastIndexed() {
    return this.props.lastIndexed;
  }

  get updateIndexFailedDate() {
    return this.props.updateIndexFailedDate;
  }

  get revisionRequest() {
    return this.props.revisionRequest ? new ServiceTicketV1RevisionRequest(this.props.revisionRequest, this.context, this.visa) : undefined;
  }
```

Each field on the Props interface requires a getter method defined in the domain context class. All getters are named to match a field on the props. For most fields, the getter will simply return the field on the props. Other fields require special exceptions for their getter methods.

#### Primitive Fields

```typescript
get description() {
    return this.props.description;
}
```

For primitive fields, simply return the field on the props.&#x20;

Above is an example for the primitive field _description_ which is of type _string_

Reference Fields

```typescript
get community() {
    return new Community(this.props.community, this.context);
}
```

For reference fields, you need to import the domain context class that the reference fields returns to, and pass the props field and context into that constructor, which is what is returned in the getter.&#x20;

The above example shows the getter method for the reference field _community_ which is of type _CommunityProps_. The _Community_ domain context class is imported and its constructor is invoked with _this.props.community_ and _this.context_

**Note**: Some constructors may require the context, some may require the visa. Some constructors could even require both. Ideally, all constructors should take a visa for permissions checks, and any domain context class with nested path, reference, or prop array fields will require a context.&#x20;

#### Nested Path Fields

```typescript
get listingDetail() {
    return new ListingDetails(this.props.listingDetail, this.visa);
}
```

For nested path fields, the same rule applies as above for reference fields.&#x20;

Above example is for a nested path field called _listingDetail_ which is of type _PropertyListingDetailProps_

#### Optional Reference/Nested Path Fields

```typescript
get revisionRequest() {
    return this.props.revisionRequest ? new ServiceTicketV1RevisionRequest(this.props.revisionRequest, this.context, this.visa) : undefined;
}
```

For optional reference and optional nested path fields, the same rule applies. In the case of _this.props.\<field>_ being undefined, we don't want to pass those props into the constructor. So the getter needs to use ternary operator to test _if this.props.\<field>_ is defined. If it is, return the constructor invocation as usual. Otherwise, return undefined.

In the above example, _revisionRequest_ is an optional nested path of type _ServiceTicketV1RevisionRequestProps._

#### Prop Array Fields

{% code overflow="wrap" %}
```typescript
get messages(): ReadonlyArray<ServiceTicketV1Message> {
  return this.props.messages.items.map((m) => new ServiceTicketV1Message(m, this.context, this.visa));
}
```
{% endcode %}

For prop array fields, we need to map over the props field and return each element of the array as a constructor for the domain context class of the object stored in that array.

In the example above, _messages_ is a prop array field of type _ServiceTicketV1MessageProps_ so the constructor calls map on _this.props.messages_ and returns a new _ServiceTicketV1Message_ for each element contained in the array.

Note: These are the only getters we include a type on, which is _ReadonlyArray_ with the domain context class of the array elements passed in.

#### Setters

<pre class="language-typescript"><code class="lang-typescript"><strong>...
</strong><strong>set AssignedTo(assignedTo: MemberEntityReference) {
</strong>  if (!this.isNew &#x26;&#x26; !this.visa.determineIf((permissions) => permissions.isSystemAccount || permissions.canAssignTickets)) {
    throw new Error('Unauthorized2');
  }
  this.props.setAssignedToRef(assignedTo);
}

set Service(service: ServiceEntityReference) {
  if (
    !this.isNew &#x26;&#x26;
    !this.visa.determineIf((permissions) => permissions.isSystemAccount || permissions.canManageTickets || (permissions.canCreateTickets &#x26;&#x26; permissions.isEditingOwnTicket))
  ) {
    throw new Error('Unauthorized3a');
  }
  this.props.setServiceRef(service);
}

set Title(title: string) {
  if (
    !this.isNew &#x26;&#x26;
    !this.visa.determineIf((permissions) => permissions.isSystemAccount || permissions.canManageTickets || (permissions.canCreateTickets &#x26;&#x26; permissions.isEditingOwnTicket))
  ) {
    throw new Error('Unauthorized3b');
  }
  this.props.title = new ValueObjects.Title(title).valueOf();
}

set Description(description: string) {
  if (
    !this.isNew &#x26;&#x26;
    !this.visa.determineIf((permissions) => permissions.isSystemAccount || permissions.canManageTickets || (permissions.canCreateTickets &#x26;&#x26; permissions.isEditingOwnTicket))
  ) {
    throw new Error('Unauthorized4');
  }
  this.props.description = new ValueObjects.Description(description).valueOf();
}
  ...
</code></pre>

Similar to getters, we need setters for most of the fields on the Props interface. Above are a few examples of setter methods. These contain the business logic that drives the permissions and constraints of our applications.&#x20;

They begin with a condition usually checking the visa for various permissions specific to each project which determine whether the caller of the mutation updating that specific field is allowed to do so.

When first setting up the project, these permissions most likely will not exist, so for the first pass, the setter methods can be written without the visa permission checks.&#x20;

The second half of the business logic is contained in our Value Objects. We use an npm package called value-objects which allows us to create wrapper types around primitives to set constraints on any individual field, such as max length, enum values, or regex patterns.

The convention for our setter methods is the field name capitalized and the argument is the field name with the primitive type it is assigned to. When assigning the new value to the props, the argument is passed in to the corresponding Value Object constructor for that field, which validates it against the field-level constraints, and call valueOf() method to get back the underlying primitive type to save to the props.

If Value Objects haven't been set up yet, just set the prop field to the argument directly for now.

**Note**: Not all fields will need value objects. Depends on business requirements and the validation set on the schema model.

#### Primitive Fields

```typescript
set Description(description: string) {
  if (
    !this.isNew &&
    !this.visa.determineIf((permissions) => permissions.isSystemAccount || permissions.canManageTickets || (permissions.canCreateTickets && permissions.isEditingOwnTicket))
  ) {
    throw new Error('Unauthorized4');
  }
  this.props.description = new ValueObjects.Description(description).valueOf();
}
```

Reference Fields

```typescript
set Property(property: PropertyEntityReference) {
  if (
    !this.isNew &&
    !this.visa.determineIf((permissions) => permissions.isSystemAccount || permissions.canManageTickets || (permissions.canCreateTickets && permissions.isEditingOwnTicket))
  ) {
    throw new Error('Unauthorized1');
  }
  this.props.setPropertyRef(property);
}
```

For reference fields, the setter method takes in an Entity Reference as its argument and instead of setting t_his.props.\<referenceField>_, it uses the _set\<ReferenceField>Ref_ method and passes in the argument parameter to that function.

#### Prop Array Fields

```typescript
private requestNewMessage(): ServiceTicketV1Message {
  let message = this.props.messages.getNewItem();
  return new ServiceTicketV1Message(message, this.context, this.visa);
}

public requestAddMessage(message: string, sentBy: string, embedding?: string, initiatedBy?: MemberEntityReference): void {
  if (
    !this.visa.determineIf(
      (permissions) =>
        permissions.isSystemAccount ||
        (permissions.canCreateTickets && permissions.isEditingOwnTicket) ||
        (permissions.canWorkOnTickets && permissions.isEditingAssignedTicket) ||
        permissions.canManageTickets
    )
  ) {
    throw new Error('Unauthorized7');
  }
  const newMessage = this.requestNewMessage();
  newMessage.Message = new MessageValueObjects.Message(message);
  newMessage.SentBy = new MessageValueObjects.SentBy(sentBy);
  if (embedding !== undefined) newMessage.Embedding = new MessageValueObjects.Embedding(embedding);
  if (initiatedBy !== undefined) {
    newMessage.InitiatedBy = initiatedBy;
  }
}
```

For prop array fields, we don't have a direct setter method for the array field. Instead, we have special request methods which create a new instance of the array element and return it, as well as a method for adding a new element to the prop array.&#x20;

So we need to create two methods. First, a _private requestNew\<PropArrayField>_ method which invokes _getNewItem()_ method on _this.props.\<propArrayField>_ and returns a constructor invocation passing in the new props, context and visa into the domain context class that the prop array holds.

The second method is the public facing setter method called requestAdd\<PropArrayField> which calls the private requestNew method and uses the arguments to set the required fields on the new array element. Which fields need to be set varies for each domain context class.

#### Nested Path Fields

For nested path fields, these are actually exempt from setters and have no need for one as the domain context field for the nested path contains the setter methods for its underlying fields, so the parent class only needs a getter method for nested paths to access those setters.&#x20;

#### onSave override

```typescript
public override onSave(isModified: boolean): void {
  if (isModified && !super.isDeleted) {
    this.addIntegrationEvent(ServiceTicketV1UpdatedEvent, { id: this.props.id });
  }
}
```

Some aggregate roots may have an associated domain event which is tied to any updates on that domain context class. In order to trigger this event, the domain context class may override its parent onSave method to add an integration event for the updated event for that domain object.&#x20;

Again, this will not be necessary for all aggregate roots. Typically this only applies to aggregate roots which are a part of cognitive search collections and need to trigger a reindex when any fields are updated.

## Domain Repository

{% code title="src/app/domain/contexts/cases/service-ticket/service-ticket.repository.ts" %}
```typescript
export interface ServiceTicketV1Repository<props extends ServiceTicketV1Props> extends Repository<ServiceTicketV1<props>> {
  getNewInstance(
    title: string,
    description: string,
    community: CommunityEntityReference,
    property: PropertyEntityReference,
    requestor: MemberEntityReference
  ): Promise<ServiceTicketV1<props>>;

  getById(id: string): Promise<ServiceTicketV1<props>>
}
```
{% endcode %}

Each Aggregate Root requires a Domain Repository file. This file goes in the same folder as the domain context class file. The filename convention is \<aggregate-root>.repository.ts

The Domain Repository is an interface which uses the Props interface from Domain Context file and a seedwork _Repository_ class which also takes in the props and domain context class.

The exact methods to be implemented for each repository can be specific to each aggregate root. For the initial setup, put the method signature for the _static getNewInstance()_ method, excluding for the props and context arguments. You can also define a _getById()_ method which returns the domain context class type.

## Domain Unit of Work

{% code title="/src/app/domain/contexts/cases/service-ticket/service-ticket.uow.ts" %}
```typescript
import { UnitOfWork } from '../../../../../../../seedwork/domain-seedwork/unit-of-work';
import { DomainExecutionContext } from '../../../../domain-execution-context';
import { ServiceTicketV1, ServiceTicketV1Props } from './service-ticket';
import { ServiceTicketV1Repository } from './service-ticket.repository';

export interface ServiceTicketV1UnitOfWork extends UnitOfWork<
                                DomainExecutionContext, 
                                ServiceTicketV1Props, 
                                ServiceTicketV1<ServiceTicketV1Props>, 
                                ServiceTicketV1Repository<ServiceTicketV1Props>> {}

```
{% endcode %}

Each Aggregate Root also requires a Domain Unit of Work file in the same folder as the domain context class file. The filename convention is \<aggregate-root>.uow.ts

The Domain Unit of Work is an empty interface which extends the seedwork class UnitOfWork and takes in various files from the aggregate root's domain context folder.

## Infrastructure

In _/src/infrastructure-services-impl/datastore/mongodb/infrastructure/_, create a folder for your aggregate root named after the collection. Inside will be:

* \<aggregate-root>.domain-adapter.ts
* \<aggregate-root>.mongo-repository.ts
* \<aggregate-root>.uow.ts

### Domain Adapter

{% code title="/src/infrastructure-services-impl/datastore/mongodb/infrastructure/cases/service-ticket/service-ticket.domain-adapter.ts" %}
```typescript
export class ServiceTicketV1Converter extends MongoTypeConverter<
  DomainExecutionContext,
  ServiceTicket,
  ServiceTicketV1DomainAdapter,
  ServiceTicketDO<ServiceTicketV1DomainAdapter>
> {
  constructor() {
    super(ServiceTicketV1DomainAdapter, ServiceTicketDO);
  }
}

export class ServiceTicketV1DomainAdapter extends MongooseDomainAdapter<ServiceTicket> implements ServiceTicketV1Props {
  get community() {
    if (this.doc.community) {
      return new CommunityDomainAdapter(this.doc.community);
    }
  }
  public setCommunityRef(community: CommunityEntityReference) {
    this.doc.set('community', community.id);
  }

  get property() {
    if (this.doc.property) {
      return new PropertyDomainAdapter(this.doc.property);
    }
  }
  public setPropertyRef(property: PropertyEntityReference) {
    this.doc.set('property', property.id);
  }
```
{% endcode %}

Each aggregate root requires a domain adapter file for converting from domain to persistence. The Domain Adapter file contains a _\<AggregateRoot>Converter_ class which extends the _MongoTypeConverter_ seedwork class. There is also the _\<AggregateRoot>DomainAdapter_ class which extends the _MongooseDomainAdapter_ seedwork class.

Each domain adapter must provide getters and setter methods for each of the fields defined on the Props interface for that domain context class. The convention in domain adapter files is to put the getter and setter for a field together in the code, as shown in the example above.

#### Primitive Fields

```typescript
get description() {
    return this.doc.description;
}
```

For primitive getters, simply return the field on the document.&#x20;

```typescript
set description(description) {
    this.doc.description = description;
}
```

For the primitive setters, just assign the input parameter to the document field to set it.

#### Reference Fields

```typescript
get community() {
  if (this.doc.community) {
    return new CommunityDomainAdapter(this.doc.community);
  }
}
```

For reference field getters, even if the field is required, we have a conditional check to ensure that the underlying field is defined before passing it into the constructor for the domain adapter for that aggregate root.&#x20;

In the example above, community is a reference field to the aggregate root _Community_ so we pass _this.doc.community_ into the _CommunityDomainAdapter_ class so long as it isn't undefined.

```typescript
public setCommunityRef(community: CommunityEntityReference) {
    this.doc.set('community', community['props']['doc']);
}
```

For reference field setters, instead of the usual set method, here is where we implement the special set\<ReferenceFieldName>Ref method we defined on the Props interface in domain layer.

We call _this.doc.set()_ where the first argument is a string of the reference field name on the document and the second argument is the _community_ input with 'props' and 'doc' selected with square bracket notation.

#### Optional Reference Fields

<pre class="language-typescript"><code class="lang-typescript">public setAssignedToRef(assignedTo: MemberEntityReference) {
<strong>    this.doc.set('assignedTo', assignedTo ? assignedTo['props']['doc'] : null);
</strong>}
</code></pre>

For optional reference fields, the getter is the same as above. However for the setter, since we are allowing the field to be cleared of its value, we need to handle the input parameter being undefined. If the argument is defined, we set the document how we usually do for reference fields. If not, we set the document field to null.

#### Nested Path Fields

```typescript
get listingDetail() {
  if (!this.doc.listingDetail) {
    this.doc.set('listingDetail', {});
  }
  return new ListingDetailDomainAdapter(this.doc.listingDetail);
}
```

For nested path fields, we only need to provide a getter method, similar to the domain context class. For both optional and required nested path fields, we first check if the document field is undefined. If true, we set the document field to an empty object to initialize it. Otherwise, we pass the document field into a domain adapter class for that nested path object.

**Note**: You will have to create Domain Adapter classes for all nested paths (and subdocuments)  in an aggregate root similar to the domain context files. All sub domain adapters should be put in the same file as the aggregate root domain adapter they are a part of.&#x20;

Prop Array Fields

```typescript
get messages() {
    return new MongoosePropArray(this.doc.messages, ServiceTicketV1MessageDomainAdapter);
}
```

For prop array fields, we also only need to provide a getter method. Return the constructor invocation for the _MongoosePropArray_ seedwork file and pass in the document field as well as a domain adapter class for the subdocument that the array contains.

Note: Same as for nested path fields, you will need to create a domain adapter class for prop array field that provides the getters and setters for the underlying object held in the prop array. These also go in the same file as the aggregate root domain adapter.

### Mongo Repository

{% code title="/src/infrastructure-services-impl/datastore/mongodb/infrastructure/cases/service-ticket/service-ticket.mongo-repository.ts" %}
```typescript
export class MongoServiceTicketV1Repository<PropType extends ServiceTicketV1Props>
  extends MongoRepositoryBase<DomainExecutionContext, ServiceTicket, PropType, ServiceTicketDO<PropType>>
  implements ServiceTicketV1Repository<PropType>
{
  async getNewInstance(
    title: string,
    description: string,
    community: CommunityEntityReference,
    property: PropertyEntityReference,
    requestor: MemberEntityReference
  ): Promise<ServiceTicketDO<PropType>> {
    let adapter = this.typeConverter.toAdapter(new this.model());
    return ServiceTicketDO.getNewInstance(adapter, title, description, community, property, requestor, this.context);
  }

  async getById(id: string): Promise<ServiceTicketDO<PropType>> {
    let member = await this.model.findById(id).populate(['community', 'property', 'requestor', 'assignedTo', 'service']).exec();
    return this.typeConverter.toDomain(member, this.context);
  }
}
```
{% endcode %}

The Mongo Repository class extends a _MongoRepositoryBase_ seedwork class and implements the Domain Repository interface for a given aggregate root.  In the example above, _ServiceTicket_ is imported from the mongodb schema models from infrastructure layer and _ServiceTicketDO_ is an alias for the _ServiceTicket_ domain context class from domain layer.

The Mongo Repository implements the methods defined on the Domain Repository interface. Follow the example above for both implementations. Ignore the _populate()_ call in the _getById()_ method, just return the result of _findById()_ for now.&#x20;

### Mongo Unit of Work

{% code title="/src/infrastructure-services-impl/datastore/mongodb/infrastructure/cases/service-ticket/service-ticket.uow.ts" %}
```typescript
import { MongoUnitOfWork } from '../../../../../../../../seedwork/services-seedwork-datastore-mongodb/infrastructure/mongo-unit-of-work';
import { ServiceTicketModel } from '../../../../models/cases/service-ticket';
import { ServiceTicketV1Converter } from './service-ticket.domain-adapter';
import { MongoServiceTicketV1Repository } from './service-ticket.mongo-repository';
import { InProcEventBusInstance, NodeEventBusInstance } from '../../../../../../../../seedwork/event-bus-seedwork-node';

export const MongoServiceTicketV1UnitOfWork = new MongoUnitOfWork(
                                InProcEventBusInstance, 
                                NodeEventBusInstance, 
                                ServiceTicketModel, 
                                new ServiceTicketV1Converter(), 
                                MongoServiceTicketV1Repository);
```
{% endcode %}

The Mongo Unit of Work for each aggregate root requires the type converter from the domain adapter file, the model from the mongodb schema file, and the mongo repository. It also imports a few seedwork classes.&#x20;
