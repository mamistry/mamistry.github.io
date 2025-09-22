This document provides comprehensive technical documentation for the methods within the `GamecastProcessor` class.

---

### **Method Name**: `updateOldGamecasts`

*   **Description**: Updates the expiration date for legacy gamecast content modules that were previously scheduled without an end time, setting it to 24 hours after their scheduled start time.
*   **Call Stack**:
    *   **Called by**: (Not explicitly called by other methods within this file; likely triggered externally, e.g., by a scheduled job).
    *   **Calls**:
        *   `prismaConn.getConn()`: Retrieves the Prisma client for database operations.
        *   `replicaConn.getConn()`: Retrieves the read-replica Prisma client for database queries.
        *   `replicaClient.module.findMany`: Queries the database to find content modules.
        *   `prismaClient.module.update`: Updates a content module's record in the database.
        *   `logger.info`: Logs informational messages about the process.
        *   `logger.error`: Logs error messages if the update fails.
        *   `ContentModuleWorkerError`: Throws a custom error if the update operation encounters a failure.
*   **Example Usage**:

    ```typescript
    import { gamecastProcessor } from './your-file-path'; // Assuming this is where GamecastProcessor is exported

    async function runUpdate() {
      try {
        await gamecastProcessor.updateOldGamecasts();
        console.log('Legacy gamecasts updated successfully.');
      } catch (error) {
        console.error('Failed to update legacy gamecasts:', error);
      }
    }

    runUpdate();
    ```

---

### **Method Name**: `validateTaxonomyMessage`

*   **Description**: Validates and extracts essential gamecast-related information from an incoming `TaxonomyMessage`.
*   **Call Stack**:
    *   **Called by**:
        *   `processMessage`: Orchestrates the overall message processing flow.
    *   **Calls**:
        *   `logger.info`: Logs informational messages, especially when critical data is missing.
        *   `ContentModuleWorkerError`: Throws a custom error if required taxonomy data (like ID or slug) is missing.
*   **Example Usage**:

    ```typescript
    import { TaxonomyMessage } from '../../wbd/protobuf/messagebus/taxonomyevent/v4/taxonomy_pb';
    import { gamecastProcessor } from './your-file-path'; // Assuming this is where GamecastProcessor is exported

    const sampleMessage: TaxonomyMessage = {
      // ... populate with valid TaxonomyMessage data
      taxonomy: {
        id: { id: 'some-id', namespace: 'urn:wbd:identifier:some-service:some-type' },
        alternateIds: [{ namespace: 'urn:wbd:identifier:statmilk:slug', id: 'game-slug-123' }],
        synopses: [
          {
            type: 'main-description',
            localizations: [{ language: 'en', value: 'Game description here' }],
          },
        ],
        attributes: [
          { type: 'start-time-utc', value: ['2023-10-27T18:00:00Z'] },
          { type: 'end-time-utc', value: ['2023-10-27T20:00:00Z'] },
        ],
        relationships: [
          { relatedEntityType: 'team', type: 'has-member', relatedEntityId: { id: 'team-a-id' } },
          { relatedEntityType: 'team', type: 'has-member', relatedEntityId: { id: 'team-b-id' } },
        ],
      },
      // ... other fields
    };

    try {
      const validatedData = gamecastProcessor.validateTaxonomyMessage(sampleMessage);
      console.log('Validated gamecast data:', validatedData);
    } catch (error) {
      console.error('Failed to validate taxonomy message:', error);
    }
    ```

---

### **Method Name**: `hasGamecastBeenProcessed`

*   **Description**: Checks the database to determine if a gamecast with a matching slug and description has already been processed and exists.
*   **Call Stack**:
    *   **Called by**:
        *   `processMessage`: Orchestrates the overall message processing flow.
    *   **Calls**:
        *   `replicaConn.getConn()`: Retrieves the read-replica Prisma client for database queries.
        *   `replicaClient.module.findFirst`: Queries the database to find a single content module.
        *   `from`: Converts a Promise into an RxJS Observable.
        *   `map`: Transforms the data emitted by the Observable, converting the database result to a content module model or `null`.
        *   `logger.info`: Logs whether an existing gamecast was found or not.
        *   `contentModuleDTOService.mapDBResultToModel`: Converts a raw database result into a `TContentModule` model.
        *   `lastValueFrom`: Converts the RxJS Observable back to a Promise, returning its last emitted value.
*   **Example Usage**:

    ```typescript
    import { gamecastProcessor } from './your-file-path'; // Assuming this is where GamecastProcessor is exported

    const validatedMessagePartial = {
      slug: 'game-slug-123',
      gamecastDescription: 'Game description here',
    };

    async function checkProcessedStatus() {
      const existingModule = await gamecastProcessor.hasGamecastBeenProcessed(validatedMessagePartial);
      if (existingModule) {
        console.log('Gamecast already processed:', existingModule.id);
      } else {
        console.log('Gamecast not found, needs processing.');
      }
    }

    checkProcessedStatus();
    ```

---

### **Method Name**: `createStandaloneGamecast`

*   **Description**: Creates a new standalone content module in the database for a gamecast, including scheduling, expiration, and channel assignments.
*   **Call Stack**:
    *   **Called by**:
        *   `processMessage`: Orchestrates the overall message processing flow.
    *   **Calls**:
        *   `createStandalone`: An external function responsible for creating the standalone content module in the database.
        *   `sendCompositeCreateEvent`: An external function that sends a composite create event to a metadata service.
        *   `logger.info`: Logs creation arguments, the created module, and other relevant information.
        *   `ContentModuleWorkerError`: Throws a custom error if no team IDs are found, as they are required for channel creation.
        *   `from`: Converts a Promise into an RxJS Observable.
        *   `map`: Transforms the data emitted by the Observable, converting the database result to a content module model.
        *   `contentModuleDTOService.mapDBResultToModel`: Converts a raw database result into a `TContentModule` model.
        *   `lastValueFrom`: Converts the RxJS Observable back to a Promise, returning its last emitted value.
*   **Example Usage**:

    ```typescript
    import { gamecastProcessor } from './your-file-path'; // Assuming this is where GamecastProcessor is exported

    const validatedGamecastArgs = {
      id: 'some-game-id',
      serviceName: 'some-service',
      slug: 'game-slug-456',
      gamecastDescription: 'Another game description',
      startTime: '2023-10-28T10:00:00Z',
      endTime: '2023-10-28T12:00:00Z',
      teamIds: ['team-c-id', 'team-d-id'],
    };

    async function createGamecast() {
      try {
        const newGamecast = await gamecastProcessor.createStandaloneGamecast(validatedGamecastArgs);
        console.log('New standalone gamecast created:', newGamecast.id);
      } catch (error) {
        console.error('Failed to create standalone gamecast:', error);
      }
    }

    createGamecast();
    ```

---

### **Method Name**: `createStaticGamecast`

*   **Description**: Creates static content packages, such as highlight packages, associated with a gamecast.
*   **Call Stack**:
    *   **Called by**:
        *   `processMessage`: Orchestrates the overall message processing flow.
    *   **Calls**:
        *   `createStaticPackages`: An external function responsible for creating static content packages in the database.
        *   `logger.info`: Logs informational messages related to the creation process.
        *   `from`: Converts a Promise into an RxJS Observable.
        *   `map`: Transforms the data emitted by the Observable, converting the database results to content module models.
        *   `contentModuleDTOService.mapDBFindManyResultListToModel`: Converts an array of raw database results into an array of `TContentModule` models.
        *   `lastValueFrom`: Converts the RxJS Observable back to a Promise, returning its last emitted value.
*   **Example Usage**:

    ```typescript
    import { gamecastProcessor } from './your-file-path'; // Assuming this is where GamecastProcessor is exported

    const validatedGamecastArgs = {
      id: 'some-game-id',
      serviceName: 'some-service',
      slug: 'game-slug-456',
      gamecastDescription: 'Another game description',
      // ... other fields not directly used by this method but part of IValidatedGamecastTaxonomy
    };

    async function createStaticPackages() {
      try {
        const staticPackages = await gamecastProcessor.createStaticGamecast(validatedGamecastArgs);
        console.log(`Created ${staticPackages.length} static packages.`);
      } catch (error) {
        console.error('Failed to create static gamecast packages:', error);
      }
    }

    createStaticPackages();
    ```

---

### **Method Name**: `processMessage`

*   **Description**: The main entry point for processing a `TaxonomyMessage`, handling validation, checking for existing gamecasts, and creating or updating them as needed.
*   **Call Stack**:
    *   **Called by**: (Not explicitly called by other methods within this file; likely serves as a message bus consumer callback).
    *   **Calls**:
        *   `logger.info`: Logs various stages of message processing, including validation, existence checks, and creation/update actions.
        *   `this.validateTaxonomyMessage`: Validates and extracts key data from the taxonomy message.
        *   `this.hasGamecastBeenProcessed`: Checks if a gamecast corresponding to the message already exists.
        *   `this.updateGamecastExpiration`: Updates the expiration time of an existing gamecast.
        *   `this.createStandaloneGamecast`: Creates a new standalone gamecast content module.
        *   `this.createStaticGamecast`: Creates static content packages related to the gamecast.
*   **Example Usage**:

    ```typescript
    import { TaxonomyMessage } from '../../wbd/protobuf/messagebus/taxonomyevent/v4/taxonomy_pb';
    import { gamecastProcessor } from './your-file-path'; // Assuming this is where GamecastProcessor is exported

    const incomingTaxonomyMessage: TaxonomyMessage = {
      // ... populate with a real TaxonomyMessage
      taxonomy: {
        id: { id: 'game-123', namespace: 'urn:wbd:identifier:some-service:game' },
        alternateIds: [{ namespace: 'urn:wbd:identifier:statmilk:slug', id: 'amazing-game-match' }],
        synopses: [
          {
            type: 'main-description',
            localizations: [{ language: 'en', value: 'The most anticipated match of the season!' }],
          },
        ],
        attributes: [
          { type: 'start-time-utc', value: ['2023-11-01T15:00:00Z'] },
          { type: 'end-time-utc', value: ['2023-11-01T17:00:00Z'] },
        ],
        relationships: [
          { relatedEntityType: 'team', type: 'has-member', relatedEntityId: { id: 'team-blue' } },
          { relatedEntityType: 'team', type: 'has-member', relatedEntityId: { id: 'team-red' } },
        ],
      },
      // ... other fields
    };

    async function processIncomingMessage() {
      try {
        const resultModule = await gamecastProcessor.processMessage(incomingTaxonomyMessage);
        if (resultModule) {
          console.log('Message processed, gamecast module created/updated:', resultModule.id);
        } else {
          console.log('Message processed, no new module created or no updates needed.');
        }
      } catch (error) {
        console.error('Error processing message:', error);
      }
    }

    processIncomingMessage();
    ```

---

### **Method Name**: `updateGamecastExpiration`

*   **Description**: Updates the `expiresAt` field of an existing `TContentModule` based on a new end time.
*   **Call Stack**:
    *   **Called by**:
        *   `processMessage`: Orchestrates the overall message processing flow, specifically when an existing module's expiration needs to be extended.
    *   **Calls**:
        *   `logger.info`: Logs the old and new expiration times during the update process.
        *   `prismaConn.getConn()`: Retrieves the Prisma client for database operations.
        *   `prismaClient.module.update`: Updates a content module's record in the database.
        *   `from`: Converts a Promise into an RxJS Observable.
        *   `map`: Transforms the data emitted by the Observable, converting the database result to a content module model.
        *   `contentModuleDTOService.mapDBResultToModel`: Converts a raw database result into a `TContentModule` model.
        *   `lastValueFrom`: Converts the RxJS Observable back to a Promise, returning its last emitted value.
*   **Example Usage**:

    ```typescript
    import { gamecastProcessor } from './your-file-path'; // Assuming this is where GamecastProcessor is exported
    import { TContentModule } from '../../app/services/contentModules/ContentModuleTypes';

    const existingModule: TContentModule = {
      id: 'existing-gamecast-id',
      contentId: 'game-slug-001',
      description: 'An existing gamecast',
      contentType: 'StatsGamecast',
      lastModifiedBy: 'system',
      createdAt: new Date(),
      updatedAt: new Date(),
      version: 1,
      channels: [],
      title: '',
      thumbnail: '',
      expiresAt: new Date('2023-10-29T00:00:00Z'),
      scheduledDate: new Date('2023-10-27T10:00:00Z'),
    };
    const newEndTime = '2023-10-28T22:00:00Z'; // Assuming this is the new game end time

    async function updateExpiration() {
      try {
        const updatedModule = await gamecastProcessor.updateGamecastExpiration(existingModule, newEndTime);
        console.log('Gamecast expiration updated:', updatedModule.expiresAt);
      } catch (error) {
        console.error('Failed to update gamecast expiration:', error);
      }
    }

    updateExpiration();
    ```