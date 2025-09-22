Here is the comprehensive documentation for the methods found in the provided file.

---

### `createStandalone`

*   **Description**: Creates a new standalone content module, associating it with specified channels and handling various content types and properties.
*   **Call Stack**:
    *   **Called by**:
        *   `upsertStandaloneContentModules`: Orchestrates the creation and updates of content modules.
    *   **Calls**:
        *   `tracer.trace`: Traces the execution of the function for observability.
        *   `PrismaConn.getInstance()`: Retrieves an instance of the Prisma connection manager.
        *   `prismaConn.getConn()`: Gets the active Prisma client connection.
        *   `findOrCreateComponent`: Finds an existing component or creates a new one for a given channel.
        *   `processAllowedCountries`: Processes the list of allowed countries for the content module.
        *   `ContentModuleValidationError`: Custom error for validation failures related to content modules.
        *   `externalArticleService.findOrCreateExternalArticleByUrl`: Ensures an external article exists in the database for the provided URL.
        *   `buildComponentModule`: Constructs the necessary data for a component-module relationship.
        *   `isAutoProgrammed`: Determines if the content module is automatically programmed.
        *   `findOrGenerateWrapperId`: Finds an existing wrapper ID or generates a new one if not provided.
        *   `generateContentModuleIncludes`: Generates the Prisma `include` options for the content module query.
        *   `prismaClient.module.create`: Performs the database operation to create a new content module record.
        *   `contentModuleDTOService.mapDBResultWithIncludeToModel`: Maps the raw database result to a structured content module DTO.
        *   `AsyncHelper.fireAndForget`: Executes an asynchronous function without awaiting its completion.
        *   `CacheHelper.expireCache`: Invalidates specific cache entries.
        *   `markComponentToNormalize`: Marks a component for position normalization.
        *   `updateChannelPinnedModule`: Updates the pinned module status for specified channels.
        *   `logger.info`: Logs informational messages.
*   **Example Usage**:

    ```typescript
    import { ContentModuleType } from '../../../graphql/generated/graphql';
    import { CreateContentModuleMutation } from '../types';
    import { createStandalone } from './standalone'; // Assuming this is the file path

    async function exampleCreateStandalone() {
      const newModuleArgs: CreateContentModuleMutation = {
        channels: [{ id: 1, type: 'Tag', semanticID: 'exampleTag', tagUUID: 'uuid123' }],
        lastModifiedBy: 'testUser',
        allowedCountries: ['US', 'CA'],
        title: 'My New Standalone Article',
        description: 'This is a test article.',
        thumbnail: 'https://example.com/thumb.jpg',
        contentId: 'externalArticleId123',
        contentType: ContentModuleType.ExternalArticle,
        orientation: 'Landscape',
        expiresAt: null,
        scheduledDate: new Date(),
        commentsEnabled: true,
        thumbnailAccreditation: 'Source A',
        thumbnailCopyright: '© 2023 Example Co.',
      };

      try {
        const result = await createStandalone(newModuleArgs);
        console.log('Standalone content module created:', result.id, result.title);
      } catch (error) {
        console.error('Error creating standalone module:', error);
      }
    }

    // exampleCreateStandalone();
    ```

---

### `updateStandalone`

*   **Description**: Updates an existing standalone content module, modifying its properties, associated channels, and handling various content types.
*   **Call Stack**:
    *   **Called by**:
        *   `upsertStandaloneContentModules`: Orchestrates the creation and updates of content modules.
    *   **Calls**:
        *   `tracer.trace`: Traces the execution of the function for observability.
        *   `PrismaConn.getInstance()`: Retrieves an instance of the Prisma connection manager.
        *   `prismaConn.getConn()`: Gets the active primary Prisma client connection.
        *   `replicaConn.getConn()`: Gets the active replica Prisma client connection for read operations.
        *   `ContentModuleValidationError`: Custom error for validation failures related to content modules.
        *   `findOrCreateComponent`: Finds an existing component or creates a new one for a given channel.
        *   `replicaClient.module.findUnique`: Queries the replica database to find a unique content module by its ID.
        *   `externalArticleService.findOrCreateExternalArticleByUrl`: Ensures an external article exists in the database for the provided URL.
        *   `buildComponentModule`: Constructs the necessary data for a component-module relationship.
        *   `processAllowedCountries`: Processes the list of allowed countries for the content module.
        *   `isAutoProgrammed`: Determines if the content module is automatically programmed.
        *   `prismaClient.module.update`: Performs the database operation to update an existing content module record.
        *   `generateContentModuleIncludes`: Generates the Prisma `include` options for the content module query.
        *   `updateChannelPinnedModule`: Updates the pinned module status for specified channels.
        *   `contentModuleDTOService.mapDBResultWithIncludeToModel`: Maps the raw database result to a structured content module DTO.
        *   `AsyncHelper.fireAndForget`: Executes an asynchronous function without awaiting its completion.
        *   `CacheHelper.expireCache`: Invalidates specific cache entries.
        *   `markComponentToNormalize`: Marks a component for position normalization.
*   **Example Usage**:

    ```typescript
    import { ContentModuleType } from '../../../graphql/generated/graphql';
    import { UpdateStandaloneContentModuleMutation } from '../types';
    import { updateStandalone } from './standalone'; // Assuming this is the file path

    async function exampleUpdateStandalone() {
      const updateModuleArgs: UpdateStandaloneContentModuleMutation = {
        id: 'some-existing-module-id', // Replace with an actual ID
        channels: [{ id: 1, type: 'Tag', semanticID: 'exampleTag', tagUUID: 'uuid123' }],
        lastModifiedBy: 'adminUser',
        title: 'Updated Standalone Title',
        description: 'The description has been modified.',
        contentId: 'newArticleId456',
        contentType: ContentModuleType.Video,
        updateProgrammingTimestamp: true,
      };

      try {
        const result = await updateStandalone(updateModuleArgs);
        console.log('Standalone content module updated:', result.id, result.title);
      } catch (error) {
        console.error('Error updating standalone module:', error);
      }
    }

    // exampleUpdateStandalone();
    ```

---

### `upsertStandaloneContentModules`

*   **Description**: Orchestrates the creation, update, and deletion of multiple standalone content modules in a single batch operation.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `createStandalone`: Creates a new standalone content module.
        *   `updateStandalone`: Updates an existing standalone content module.
        *   `deleteContentModule`: Deletes a content module by its ID.
*   **Example Usage**:

    ```typescript
    import { ContentModuleType } from '../../../graphql/generated/graphql';
    import { UpsertContentModulesInput } from '../types';
    import { upsertStandaloneContentModules } from './standalone'; // Assuming this is the file path

    async function exampleUpsertStandaloneContentModules() {
      const input: UpsertContentModulesInput = {
        data: [
          {
            // New module to create
            channels: [{ id: 2, type: 'Channel', semanticID: 'newsChannel', tagUUID: 'uuid456' }],
            lastModifiedBy: 'batchCreator',
            title: 'Batch Created Module',
            contentType: ContentModuleType.Article,
            contentId: 'articleXYZ',
          },
          {
            // Existing module to update
            id: 'existing-module-abc', // Replace with an actual ID
            lastModifiedBy: 'batchUpdater',
            title: 'Updated Batch Module',
            description: 'New description for batch update.',
          },
        ],
        delete: ['module-to-delete-1', 'module-to-delete-2'], // Replace with actual IDs
      };

      try {
        const result = await upsertStandaloneContentModules(input);
        console.log('Upsert results:', result.upsertResults.map(r => r.id));
        console.log('Delete results:', result.deleteResults);
      } catch (error) {
        console.error('Error during upsert operation:', error);
      }
    }

    // exampleUpsertStandaloneContentModules();
    ```

---

### `normalizeStandalonesContentsPositions`

*   **Description**: Normalizes the positions of content modules within components that have been marked for normalization, ensuring consistent spacing.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `tracer.trace`: Traces the execution of the function for observability.
        *   `PrismaConn.getInstance()`: Retrieves an instance of the Prisma connection manager.
        *   `prismaConn.getConn()`: Gets the active primary Prisma client connection.
        *   `replicaConn.getConn()`: Gets the active replica Prisma client connection for read operations.
        *   `replicaClient.componentNormalization.findMany`: Finds all entries in the `componentNormalization` table.
        *   `LexicalPositionService.positionStep`: Provides the step value used in lexical positioning calculations.
        *   `Prisma.sql`: A utility to construct raw SQL queries safely.
        *   `prismaClient.$executeRaw`: Executes a raw SQL query directly against the database.
        *   `prismaClient.componentNormalization.deleteMany`: Deletes multiple entries from the `componentNormalization` table.
*   **Example Usage**:

    ```typescript
    import { normalizeStandalonesContentsPositions } from './standalone'; // Assuming this is the file path

    async function runNormalization() {
      console.log('Starting content module position normalization...');
      try {
        await normalizeStandalonesContentsPositions();
        console.log('Content module positions normalized successfully.');
      } catch (error) {
        console.error('Error during normalization:', error);
      }
    }

    // runNormalization();
    ```

---

### `markComponentToNormalize`

*   **Description**: Marks a specific component for future position normalization by creating or updating an entry in the `componentNormalization` table.
*   **Call Stack**:
    *   **Called by**:
        *   `createStandalone`: Marks components associated with a newly created content module.
        *   `updateStandalone`: Marks components associated with an updated content module.
    *   **Calls**:
        *   `PrismaConn.getInstance()`: Retrieves an instance of the Prisma connection manager.
        *   `prismaConn.getConn()`: Gets the active primary Prisma client connection.
        *   `prismaClient.componentNormalization.upsert`: Creates or updates a record for a component in the `componentNormalization` table.
*   **Example Usage**:

    ```typescript
    import { markComponentToNormalize } from './standalone'; // Assuming this is the file path

    async function markAComponent() {
      const componentIdToNormalize = 12345; // Replace with an actual component ID
      try {
        await markComponentToNormalize(componentIdToNormalize);
        console.log(`Component ${componentIdToNormalize} marked for normalization.`);
      } catch (error) {
        console.error('Error marking component for normalization:', error);
      }
    }

    // markAComponent();
    ```