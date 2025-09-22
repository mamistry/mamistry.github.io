This document provides comprehensive documentation for the methods and functions found within the provided TypeScript file.

---

### Method Name: `validateMessage`

*   **Description**: Validates an incoming `ContentCommandMessage` for essential data points and command type, extracting and returning a structured object with relevant UGC information or `null` if the message is invalid or not an UPSERT command.
*   **Call Stack**:
    *   **Called by**:
        *   `UGCProcessor.processMessage`: Validates the incoming message before proceeding with processing.
    *   **Calls**:
        *   `BmmUtils.extractProgrammingOptionsData`: Extracts programming options from the content command message object.
        *   `BmmUtils.getTagUUIDsFromTaxonomyReferenceGroups`: Retrieves tag UUIDs from the taxonomy reference groups within the message.
*   **Example Usage**:

    ```typescript
    import { ContentCommandMessage } from '../../wbd/protobuf/messagebus/contentcommandevent/v4/contentcommand_pb';
    import { UGCProcessor } from './your-file'; // Assuming this is your file

    const processor = new UGCProcessor();
    const message: ContentCommandMessage = {
      // ... populate with ContentCommandMessage data ...
      contentCommand: {
        id: { namespace: 'urn:wbd:identifier:hydration-station:socialmedia-id', id: 'some-id' },
        commandType: 1, // CommandType.UPSERT
        contentId: { id: 'ugc-content-123', namespace: 'ugc' },
        taxonomyReferenceGroups: [{ tagUUIDs: ['tag-1', 'tag-2'] }],
        widgets: [{ id: 'widget-1', type: 'poll' }],
      },
    };

    try {
      const validCommand = processor.validateMessage(message);
      if (validCommand) {
        console.log('Message is valid for service:', validCommand.serviceName);
      } else {
        console.log('Message is not a valid UPSERT command or is malformed.');
      }
    } catch (error) {
      console.error('Validation error:', error.message);
    }
    ```

---

### Method Name: `hasUGCBeenProcessed`

*   **Description**: Asynchronously checks the database to determine if any content modules corresponding to the given widget IDs have already been processed.
*   **Call Stack**:
    *   **Called by**:
        *   `UGCProcessor.processMessage`: Checks for existing UGC content modules to prevent duplicate processing.
    *   **Calls**:
        *   `replicaConn.getConn()`: Obtains a read-replica Prisma database connection.
        *   `prismaReplica.module.findMany()`: Queries the database for content modules matching the provided content IDs.
        *   `contentModuleDTOService.mapDBFindManyResultListToModel`: Maps a list of database results to a list of content module models.
        *   `from` (rxjs): Creates an Observable from a Promise.
        *   `map` (rxjs operator): Transforms values emitted by the Observable.
        *   `lastValueFrom` (rxjs): Converts an Observable into a Promise, resolving with its last emitted value.
        *   `logger.info()`: Logs informational messages about execution time.
*   **Example Usage**:

    ```typescript
    import { UGCProcessor } from './your-file'; // Assuming this is your file

    async function checkProcessedWidgets() {
      const processor = new UGCProcessor();
      const widgetIdsToCheck = ['widget-abc', 'widget-xyz'];
      const processedModules = await processor.hasUGCBeenProcessed(widgetIdsToCheck);

      if (processedModules.length > 0) {
        console.log('Found already processed UGC modules:', processedModules.map((m) => m.contentId));
      } else {
        console.log('No existing UGC modules found for these widget IDs.');
      }
    }

    checkProcessedWidgets();
    ```

---

### Method Name: `prepareUGCDbReqs`

*   **Description**: Constructs and executes a database request to create a new standalone content module based on widget details, programming channels, and service information.
*   **Call Stack**:
    *   **Called by**:
        *   `UGCProcessor.processMessage`: Called iteratively to create content modules for each relevant widget and tag/channel combination.
    *   **Calls**:
        *   `BmmUtils.getContentTypeFromWidgetType()`: Determines the appropriate content type based on the widget type.
        *   `createStandalone()`: Initiates the creation of a standalone content module in the database.
        *   `contentModuleDTOService.mapDBResultToModel`: Maps a single database result to a content module model.
        *   `from` (rxjs): Creates an Observable from a Promise.
        *   `map` (rxjs operator): Transforms values emitted by the Observable.
        *   `lastValueFrom` (rxjs): Converts an Observable into a Promise, resolving with its last emitted value.
        *   `logger.info()`: Logs informational messages about execution time.
*   **Example Usage**:

    ```typescript
    import { UGCProcessor } from './your-file'; // Assuming this is your file
    import { SemanticId } from '../../graphql/generated/graphql';

    async function createUGCModule() {
      const processor = new UGCProcessor();
      const args = {
        widgetContentId: 'ugc-image-poll-1',
        widgetType: 'image_poll',
        serviceName: 'hydration-station',
        channels: [
          {
            tagUUID: 'channel-tag-123',
            position: 1,
            isPositionLocked: false,
            semanticID: SemanticId.UgcFeed,
          },
        ],
      };

      try {
        const newModule = await processor.prepareUGCDbReqs(args);
        console.log('Successfully created content module with ID:', newModule.id);
      } catch (error) {
        console.error('Failed to create content module:', error.message);
      }
    }

    createUGCModule();
    ```

---

### Method Name: `processMessage`

*   **Description**: Orchestrates the entire UGC processing flow, from message validation and duplicate checking to the creation of multiple content modules in the database for each valid widget and associated programming options or tags.
*   **Call Stack**:
    *   **Called by**: (Likely an external message bus consumer or worker, as this is the entry point for processing a `ContentCommandMessage`).
    *   **Calls**:
        *   `this.validateMessage()`: Validates the incoming content command message.
        *   `this.hasUGCBeenProcessed()`: Checks if the UGC content has already been processed.
        *   `this.prepareUGCDbReqs()`: Prepares and executes database requests to create individual content modules.
        *   `from` (rxjs): Creates an Observable from a Promise.
        *   `combineLatest` (rxjs): Combines multiple Observables into a single Observable whose values are an array of the latest values from each input Observable.
        *   `lastValueFrom` (rxjs): Converts an Observable into a Promise, resolving with its last emitted value.
        *   `console.info()`: Logs messages indicating skipped processing.
        *   `logger.info()`: Logs informational messages about execution time.
*   **Example Usage**:

    ```typescript
    import { ContentCommandMessage, CommandType } from '../../wbd/protobuf/messagebus/contentcommandevent/v4/contentcommand_pb';
    import { UGCProcessor } from './your-file'; // Assuming this is your file

    async function handleIncomingUGC() {
      const processor = new UGCProcessor();
      const sampleMessage: ContentCommandMessage = {
        contentCommand: {
          id: { namespace: 'urn:wbd:identifier:hydration-station:socialmedia-id', id: 'unique-social-id-456' },
          commandType: CommandType.UPSERT,
          contentId: { id: 'ugc-post-content-456', namespace: 'ugc' },
          taxonomyReferenceGroups: [{ tagUUIDs: ['tag-news-feed'] }],
          widgets: [{ id: 'widget-post-1', type: 'rich_post' }],
          // Add other necessary fields for a valid message
        },
      };

      try {
        console.log('Attempting to process UGC message...');
        const resultModules = await processor.processMessage(sampleMessage);

        if (resultModules) {
          console.log(`Successfully processed message, created ${resultModules.length} content modules.`);
          resultModules.forEach((mod) => console.log(`  - Module ID: ${mod.id}, Content ID: ${mod.contentId}`));
        } else {
          console.log('Message processing skipped (either invalid or already processed).');
        }
      } catch (error) {
        console.error('Error during UGC message processing:', error.message);
      }
    }

    handleIncomingUGC();
    ```