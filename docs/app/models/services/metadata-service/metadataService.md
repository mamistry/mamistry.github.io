This document provides a comprehensive overview of the methods and functions found within the provided TypeScript file. Each entry details the method's purpose, its interaction within the program's call hierarchy, and practical examples of its usage.

---

### `sendCompositeCreateEvent`

1.  **Method Name**: `sendCompositeCreateEvent`
2.  **Description**: Orchestrates the process of sending composite creation events to BMM for standalone or package content modules.
3.  **Call Stack**:
    *   **Called by**: (External context, not identifiable within this file)
    *   **Calls**:
        *   `logger.info`: Logs informational messages regarding the event processing.
        *   `processEvent`: Handles the core logic for generating and sending BMM composite and offering payloads.
4.  **Example Usage**:
    ```typescript
    import { TContentModule } from '../../../services/contentModules/ContentModuleTypes';

    const myContentModule: TContentModule = {
      id: 'module-123',
      type: 'standalone', // or 'package'
      title: 'My Article',
      description: 'An interesting piece.',
      insertedAt: new Date(),
      // ... other TContentModule properties
    };
    const tagUUIDs: string[] = ['tag-uuid-1', 'tag-uuid-2'];

    await sendCompositeCreateEvent({ contentModule: myContentModule, tagUUIDs });
    ```

---

### `sendDeleteCompositeEvent`

1.  **Method Name**: `sendDeleteCompositeEvent`
2.  **Description**: Sends requests to delete both the composite and offering associated with a given module ID from BMM.
3.  **Call Stack**:
    *   **Called by**: (External context, not identifiable within this file)
    *   **Calls**:
        *   `bmmRestClient.request`: Makes HTTP DELETE requests to the BMM ingest API for composites and offerings.
4.  **Example Usage**:
    ```typescript
    const moduleIdToDelete = 'some-module-identifier-123';
    await sendDeleteCompositeEvent(moduleIdToDelete);
    ```

---

### `processEvent`

1.  **Method Name**: `processEvent`
2.  **Description**: Processes a `TContentModule` to generate and send its corresponding BMM `Composite` and `Offering` payloads.
3.  **Call Stack**:
    *   **Called by**:
        *   `sendCompositeCreateEvent`: Initiates the processing for content module creation events.
    *   **Calls**:
        *   `logger.info`: Logs various stages of event processing and debug information.
        *   `logger.warn`: Logs warnings, e.g., for missing titles or descriptions.
        *   `logger.error`: Logs errors that occur during the event processing or sending.
        *   `bmmService.getTaxonomyById`: Fetches taxonomy details from BMM for given UUIDs.
        *   `getTaxonomyReferences`: Transforms raw tag data into structured taxonomy reference groups for the composite.
        *   `getModuleAttributes`: Extracts core attributes (ID, title, description, type) from the content module.
        *   `getModuleRelationships`: Determines and constructs relationships for the composite based on its type and content.
        *   `Timestamp` (constructor): Creates protobuf `Timestamp` objects for date fields.
        *   `Composite` (constructor): Instantiates a protobuf `Composite` object.
        *   `getModuleState`: Determines the composite's status (e.g., `SCHEDULED`, `PROGRAMMED`).
        *   `getModuleAlternateIds`: Retrieves any alternate identifiers for the content module.
        *   `getOfferingPayload`: Constructs the protobuf `Offering` object for the content module.
        *   `axios.all`: Concurrently executes promises to send composite and offering payloads.
        *   `sendComposite`: Sends the generated `Composite` payload to BMM.
        *   `sendOffering`: Sends the generated `Offering` payload to BMM.
4.  **Example Usage**:
    ```typescript
    import { TContentModule } from '../../../services/contentModules/ContentModuleTypes';

    const myStandaloneModule: TContentModule = {
      id: 'article-789',
      type: 'standalone',
      title: 'Breaking News',
      description: 'The latest updates.',
      insertedAt: new Date(),
      scheduledDate: new Date(Date.now() + 3600 * 1000), // An hour from now
      // ... other required properties
    };
    const associatedTagUUIDs: string[] = ['sports-tag-uuid'];

    await processEvent({ contentModule: myStandaloneModule, tagUUIDs: associatedTagUUIDs }, 'standalone');
    ```

---

### `sendComposite`

1.  **Method Name**: `sendComposite`
2.  **Description**: Sends a serialized `Composite` protobuf payload to the BMM ingest service, with optional validation.
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Invoked to send the created composite payload to BMM.
    *   **Calls**:
        *   `logger.info`: Logs method entry, payload details, validation status, and BMM response.
        *   `logger.error`: Logs errors occurring during payload serialization or network request.
        *   `validateCompositePayload`: Performs validation checks on the composite payload structure and content.
        *   `compositePayload.toJson()`: Serializes the protobuf `Composite` object to a JSON representation.
        *   `compositePayload.toJsonString()`: Serializes the protobuf `Composite` object to a JSON string.
        *   `bmmRestClient.request`: Makes an HTTP PUT request to the BMM ingest API.
4.  **Example Usage**:
    ```typescript
    import { Composite, CompositeType, CompositeStatus } from '../../../../wbd/protobuf/metadata/composite/v4/composite_pb';
    import { EntityClass, Identifier } from '../../../../wbd/protobuf/metadata/common/v4/model_pb';
    import { Timestamp } from '@bufbuild/protobuf';
    import { NAMESPACES } from './namespaces';

    const sampleComposite = new Composite({
      id: new Identifier({ id: 'my-composite-id', namespace: NAMESPACES.sports_content_modules + 'composite-id' }),
      entityClass: EntityClass.CONTENT_MODULE_COMPOSITE,
      type: CompositeType.INDIVIDUAL,
      titles: [{ localizations: [{ generic: true, language: 'en-US', value: 'My Test Composite' }] }],
      synopses: [{ localizations: [{ generic: true, language: 'en-US', value: 'A test composite description' }] }],
      lastModifiedDateTime: new Timestamp({ seconds: BigInt(Date.now() / 1000), nanos: 0 }),
      createdDateTime: new Timestamp({ seconds: BigInt(Date.now() / 1000), nanos: 0 }),
      compositeStatus: CompositeStatus.PROGRAMMED,
    });

    const moduleAttrs = {
      moduleId: 'my-composite-id',
      moduleDesc: 'A test composite description',
      moduleTitle: 'My Test Composite',
      compositeType: CompositeType.INDIVIDUAL,
    };

    await sendComposite(sampleComposite, moduleAttrs);
    ```

---

### `sendOffering`

1.  **Method Name**: `sendOffering`
2.  **Description**: Sends a serialized `Offering` protobuf payload to the BMM ingest service, with optional validation.
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Invoked to send the created offering payload to BMM.
    *   **Calls**:
        *   `logger.info`: Logs the offering payload being sent.
        *   `logger.error`: Logs errors if sending the offering payload fails.
        *   `validateOfferingPayload`: Performs validation checks on the offering payload.
        *   `offeringPayload.toJsonString()`: Serializes the protobuf `Offering` object to a JSON string.
        *   `bmmRestClient.request`: Makes an HTTP PUT request to the BMM ingest API.
4.  **Example Usage**:
    ```typescript
    import { Offering, Action, Brand, ProductLine } from '../../../../wbd/protobuf/metadata/offering/v4/offering_pb';
    import { EntityClass, Identifier } from '../../../../wbd/protobuf/metadata/common/v4/model_pb';
    import { Timestamp } from '@bufbuild/protobuf';
    import { NAMESPACES } from './namespaces';
    import { CompositeType } from '../../../../wbd/protobuf/metadata/composite/v4/composite_pb';

    const sampleOffering = new Offering({
      id: new Identifier({ id: 'my-offering-id', namespace: NAMESPACES.sports_content_modules + 'offering-id' }),
      territories: ['US'],
      contentClass: EntityClass.CONTENT_MODULE_COMPOSITE,
      actions: { list: new Action({}), play: new Action({}), download: new Action({}) },
      productLines: [new ProductLine({ label: 'br', id: { id: 'd646434d-bcf9-4e40-88fb-c5ca52d8ccf0', namespace: 'urn:wbd:identifier:distribute:partner-id' } })],
      brands: [new Brand({ label: 'Bleacher Report', primary: true, id: new Identifier({ id: 'br-brand-id', namespace: 'br-brand-namespace' }) })],
      appNames: { allowValues: ['br'] },
      packages: ['BRUnlimited'],
      platforms: { allowAll: true },
      firstAvailableDate: new Timestamp({ seconds: BigInt(Date.now() / 1000), nanos: 0 }),
      startDate: new Timestamp({ seconds: BigInt(Date.now() / 1000), nanos: 0 }),
      endDate: new Timestamp({ seconds: BigInt(Date.now() / 1000), nanos: 0 }),
      contentId: new Identifier({ id: 'my-composite-id', namespace: NAMESPACES.sports_content_modules + 'composite-id' }),
      lastModifiedDateTime: new Timestamp({ seconds: BigInt(Date.now() / 1000), nanos: 0 }),
      createdDateTime: new Timestamp({ seconds: BigInt(Date.now() / 1000), nanos: 0 }),
    });

    const moduleAttrs = {
      moduleId: 'my-offering-id',
      moduleDesc: 'Offering description',
      moduleTitle: 'Offering Title',
      compositeType: CompositeType.INDIVIDUAL,
    };

    await sendOffering(sampleOffering, moduleAttrs);
    ```

---

### `getTaxonomyReferences`

1.  **Method Name**: `getTaxonomyReferences`
2.  **Description**: Converts an array of simplified tag objects (containing UUID and type) into an array of BMM `TaxonomyReferenceGroup` protobuf objects.
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Used to populate the `taxonomyReferenceGroups` field of the `Composite` payload.
    *   **Calls**:
        *   `TaxonomyReferenceGroup` (constructor): Instantiates a protobuf `TaxonomyReferenceGroup` object.
        *   `TaxonomyReference` (constructor): Instantiates a protobuf `TaxonomyReference` object.
4.  **Example Usage**:
    ```typescript
    import { TaxonomyReferenceGroup } from '../../../../wbd/protobuf/metadata/common/v4/model_pb';

    const tags = [
      { uuid: 'sport-uuid-1', type: 'Sport' },
      { uuid: 'league-uuid-2', type: 'League' },
    ];
    const taxRefGroups: TaxonomyReferenceGroup[] = getTaxonomyReferences(tags);
    // Result will be an array of TaxonomyReferenceGroup, each containing a TaxonomyReference
    // e.g., [{ kind: 'Sport', taxonomyReferences: [{ taxonomyId: { id: 'sport-uuid-1', namespace: 'urn:wbd:identifier:sports-cms:taxonomy-id' } }] }, ...]
    ```

---

### `getOfferingPayload`

1.  **Method Name**: `getOfferingPayload`
2.  **Description**: Constructs a complete BMM `Offering` protobuf payload from a `TContentModule`, including territorial restrictions, actions, product lines, brands, and timestamps.
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Utilized to create the `Offering` payload that accompanies a `Composite` event.
    *   **Calls**:
        *   `CompositeMessageValidationError` (constructor): Throws an error if required fields like `allowedCountries` are missing.
        *   `bmmService.getBrandByTitle`: Retrieves brand details (e.g., 'Bleacher Report') from BMM.
        *   `Offering` (constructor): Instantiates a protobuf `Offering` object.
        *   `Identifier` (constructor): Creates protobuf `Identifier` objects for various IDs.
        *   `Action` (constructor): Creates protobuf `Action` objects (list, play, download).
        *   `ProductLine` (constructor): Creates protobuf `ProductLine` objects.
        *   `Brand` (constructor): Creates protobuf `Brand` objects.
        *   `Timestamp` (constructor): Creates protobuf `Timestamp` objects for various date fields.
4.  **Example Usage**:
    ```typescript
    import { TContentModule } from '../../../services/contentModules/ContentModuleTypes';
    import { Offering } from '../../../../wbd/protobuf/metadata/offering/v4/offering_pb';

    const contentModule: TContentModule = {
      id: 'example-module-id',
      allowedCountries: ['US', 'GB'],
      insertedAt: new Date('2023-01-01T10:00:00Z'),
      title: 'Module Title',
      description: 'Module Description',
      type: 'standalone',
      // ... other TContentModule properties
    };

    const offeringPayload: Offering = await getOfferingPayload(contentModule);
    // The offeringPayload will be a populated protobuf Offering object.
    ```

---

### `getModuleAttributes`

1.  **Method Name**: `getModuleAttributes`
2.  **Description**: Extracts and normalizes essential attributes (module ID, description, title, and composite type) from a `TContentModule` for use in BMM payloads.
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Provides common module attributes needed for both `Composite` and `Offering` payloads.
    *   **Calls**:
        *   `CompositeMessageValidationError` (constructor): Throws an error if the content module ID is missing.
4.  **Example Usage**:
    ```typescript
    import { TContentModule } from '../../../services/contentModules/ContentModuleTypes';
    import { CompositeType } from '../../../../wbd/protobuf/metadata/composite/v4/composite_pb';

    const contentModule: TContentModule = {
      id: 'module-abcd',
      title: 'Awesome Content',
      description: 'A fantastic read.',
      type: 'standalone', // or 'package'
      insertedAt: new Date(),
    };

    const attributes = getModuleAttributes(contentModule);
    // attributes will be: {
    //   moduleId: 'module-abcd',
    //   moduleDesc: 'A fantastic read.',
    //   moduleTitle: 'Awesome Content',
    //   compositeType: CompositeType.INDIVIDUAL
    // }
    ```

---

### `getModuleRelationships`

1.  **Method Name**: `getModuleRelationships`
2.  **Description**: Generates a list of `Relationship` protobuf objects that define how a content module relates to other entities (e.g., an article, video, or other composite modules).
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Used to populate the `relationships` field of the `Composite` payload.
    *   **Calls**:
        *   `Relationship` (constructor): Instantiates a protobuf `Relationship` object.
        *   `Identifier` (constructor): Creates protobuf `Identifier` objects for relationship IDs and related entity IDs.
        *   `namespaceFromContentType`: Determines the correct BMM namespace for the related entity based on its content type.
        *   `entityClassFromContentType`: Maps the content type to a protobuf `EntityClass`.
        *   `relatedEntityTypeFromContentType`: Maps the content type to a string representing the related entity's type.
        *   `CompositeMessageValidationError` (constructor): Throws an error if the content module type is invalid.
4.  **Example Usage**:
    ```typescript
    import { TContentModule } from '../../../services/contentModules/ContentModuleTypes';
    import { Relationship } from '../../../../wbd/protobuf/metadata/common/v4/model_pb';

    const standaloneModule: TContentModule = {
      id: 'standalone-module-id',
      type: 'standalone',
      contentType: 'Article',
      contentId: 'external-article-id-1',
      insertedAt: new Date(),
      title: 'Standalone Article', description: 'desc', // Minimal TContentModule
    };
    const standaloneRelationships: Relationship[] = await getModuleRelationships(standaloneModule, 'standalone');
    // relationships will include one 'has-member' relationship to the external article.

    const packageModule: TContentModule = {
      id: 'package-module-id',
      type: 'package',
      Composite: {
        contents: [
          { moduleId: 'member-module-1' },
          { moduleId: 'member-module-2' },
        ],
      },
      insertedAt: new Date(),
      title: 'Package Module', description: 'desc', // Minimal TContentModule
    };
    const packageRelationships: Relationship[] = await getModuleRelationships(packageModule, 'composite');
    // relationships will include 'has-member' relationships to member-module-1 and member-module-2.
    ```

---

### `entityClassFromContentType`

1.  **Method Name**: `entityClassFromContentType`
2.  **Description**: Maps a `ProgrammedContent` string type to its corresponding `EntityClass` protobuf enum value.
3.  **Call Stack**:
    *   **Called by**:
        *   `getModuleRelationships`: Used to set the `relatedEntityClass` for standalone content module relationships.
    *   **Calls**:
        *   `CompositeMessageValidationError` (constructor): Throws an error for unsupported content types.
4.  **Example Usage**:
    ```typescript
    import { EntityClass } from '../../../../wbd/protobuf/metadata/common/v4/model_pb';

    const articleEntityClass: EntityClass = entityClassFromContentType('Article'); // Returns EntityClass.ARTICLE
    const tweetEntityClass: EntityClass = entityClassFromContentType('Tweet');     // Returns EntityClass.SOCIAL_MEDIA_TWEET
    ```

---

### `relatedEntityTypeFromContentType`

1.  **Method Name**: `relatedEntityTypeFromContentType`
2.  **Description**: Maps a `ProgrammedContent` string type to a standardized string representation of its entity type, primarily for BMM relationship fields.
3.  **Call Stack**:
    *   **Called by**:
        *   `getModuleRelationships`: Used to set the `relatedEntityType` for standalone content module relationships.
    *   **Calls**: (None directly within the function)
4.  **Example Usage**:
    ```typescript
    const tweetType: string = relatedEntityTypeFromContentType('Tweet'); // Returns 'socialmedia'
    const articleType: string = relatedEntityTypeFromContentType('Article'); // Returns 'article'
    ```

---

### `namespaceFromContentType`

1.  **Method Name**: `namespaceFromContentType`
2.  **Description**: Determines the appropriate BMM namespace for a given `ProgrammedContent` type and entity ID, querying BMM or CVS if necessary.
3.  **Call Stack**:
    *   **Called by**:
        *   `getModuleRelationships`: Used to resolve the namespace for `relatedEntityId` in composite relationships.
    *   **Calls**:
        *   `CompositeMessageValidationError` (constructor): Throws an error for invalid content types.
        *   `axios.get`: Makes HTTP GET requests to CVS (Content Verification Service) for namespace resolution.
        *   `logger.info`: Logs debugging and response information from external service calls.
        *   `logger.error`: Logs errors encountered during namespace resolution from BMM or CVS.
        *   `HashUtils.toHash`: Hashes the `entityId` for `ExternalArticle` content types.
        *   `bmmService.getObjectIdentifier`: Attempts to retrieve the object identifier from BMM.
4.  **Example Usage**:
    ```typescript
    import { ContentModuleType as ContentType } from '../../../../graphql/generated/graphql';

    const articleNamespace = await namespaceFromContentType(ContentType.Article, 'my-article-id-123');
    // articleNamespace might be 'urn:wbd:identifier:article-id' or null if not found.

    const gamecastNamespace = await namespaceFromContentType(ContentType.StatsGamecast, 'nba-game-slug');
    // gamecastNamespace would involve multiple CVS calls to resolve.
    ```

---

### `getModuleState`

1.  **Method Name**: `getModuleState`
2.  **Description**: Maps the internal state value of a `TContentModule` to a BMM `CompositeStatus` protobuf enum value.
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Used to set the `compositeStatus` field in the `Composite` payload.
    *   **Calls**:
        *   `getStateValue`: Retrieves the content module's state as a string (e.g., 'SCHEDULED').
4.  **Example Usage**:
    ```typescript
    import { CompositeStatus } from '../../../../wbd/protobuf/metadata/composite/v4/composite_pb';
    import { TContentModule } from '../../../services/contentModules/ContentModuleTypes';

    const scheduledModule: TContentModule = {
      id: 'id-1', state: 'SCHEDULED', title: '', description: '', type: 'standalone', insertedAt: new Date()
    };
    const statusScheduled: CompositeStatus = getModuleState(scheduledModule); // Returns CompositeStatus.SCHEDULED

    const unprogrammedModule: TContentModule = {
      id: 'id-2', state: 'UNPROGRAMMED', title: '', description: '', type: 'standalone', insertedAt: new Date()
    };
    const statusUnprogrammed: CompositeStatus = getModuleState(unprogrammedModule); // Returns CompositeStatus.UNPROGRAMMED
    ```

---

### `getModuleAlternateIds`

1.  **Method Name**: `getModuleAlternateIds`
2.  **Description**: Generates an array of BMM `Identifier` protobuf objects for a content module's alternate IDs, specifically for the `wrapperContentModuleId`.
3.  **Call Stack**:
    *   **Called by**:
        *   `processEvent`: Used to populate the `alternateIds` field in the `Composite` payload.
    *   **Calls**:
        *   `Identifier` (constructor): Creates a protobuf `Identifier` object.
4.  **Example Usage**:
    ```typescript
    import { Identifier } from '../../../../wbd/protobuf/metadata/common/v4/model_pb';
    import { NAMESPACES } from './namespaces';
    import { TContentModule } from '../../../services/contentModules/ContentModuleTypes';

    const moduleWithWrapper: TContentModule = {
      id: 'parent-module-1',
      wrapperContentModuleId: 'child-module-789',
      title: 'Title', description: 'Desc', type: 'standalone', insertedAt: new Date(),
    };
    const alternateIds: Identifier[] | undefined = getModuleAlternateIds(moduleWithWrapper);
    // alternateIds will be:
    // [new Identifier({ id: 'child-module-789', namespace: NAMESPACES.sports_content_modules + 'wrapper-content-module-id' })]

    const moduleWithoutWrapper: TContentModule = {
      id: 'simple-module-1',
      title: 'Title', description: 'Desc', type: 'standalone', insertedAt: new Date(),
    };
    const noAlternateIds: Identifier[] | undefined = getModuleAlternateIds(moduleWithoutWrapper); // Returns undefined
    ```