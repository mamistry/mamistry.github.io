Here is the comprehensive documentation for the methods and functions found in your provided file:

---

### Method Name: `generateContentModuleIncludes`

*   **Description**: Generates an object defining Prisma include and where conditions for fetching content modules, typically applying schedule and expiry date filters.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `Prisma.SortOrder.desc`: (External) Prisma utility to specify a descending sort order.
*   **Example Usage**:
    ```typescript
    import { Prisma } from '@prisma/client';

    // Generate includes with default scheduledDate and expiresAt filters
    const includesWithDefaults = generateContentModuleIncludes();
    console.log(JSON.stringify(includesWithDefaults, null, 2));

    // Generate includes with a custom state filter and disabling default date filters
    const customIncludes = generateContentModuleIncludes(
      { isPublished: true },
      { scheduledDate: false, expiresAt: false }
    );
    console.log(JSON.stringify(customIncludes, null, 2));
    ```

---

### Method Name: `processAllowedCountries`

*   **Description**: Validates a list of country codes and returns them in lowercase, or all supported country codes if the input is empty or undefined.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `allCountriesCodes`: (External) Provides a list of all recognized country codes.
        *   `validateCountryCodes`: (External) Checks if an array of country codes contains only valid codes.
        *   `ContentModuleValidationError`: (External) An error class indicating invalid input for content module operations.
*   **Example Usage**:
    ```typescript
    // Assuming allCountriesCodes and validateCountryCodes are imported
    // import { allCountriesCodes, validateCountryCodes } from '../../util/countryCodeValidator';
    // import { ContentModuleValidationError } from './errors';

    // Example with valid country codes
    const allowed = ['US', 'CA'];
    const processed = processAllowedCountries(allowed);
    console.log('Processed countries:', processed); // Output: ['us', 'ca']

    // Example with no allowed countries (returns all supported)
    const allSupported = processAllowedCountries([]);
    console.log('All supported countries (truncated):', allSupported.slice(0, 5));

    // Example of invalid country code (throws an error)
    try {
      processAllowedCountries(['invalid_code']);
    } catch (e: any) {
      console.error('Error:', e.message); // Output: Error: Invalid country code
    }
    ```

---

### Method Name: `buildComponentModule`

*   **Description**: Constructs the data for a `ComponentModule` record, determining its position within a channel and handling channel limits.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `tracer.trace`: (External) Datadog tracing utility to instrument asynchronous operations.
        *   `ContentModuleValidationError`: (External) An error class indicating invalid input for content module operations.
        *   `replicaConn.getConn`: (External) Retrieves a Prisma client instance configured for replica database reads.
        *   `prismaReplica.componentModule.findMany`: (External) Prisma ORM method to query multiple `ComponentModule` records.
        *   `LexicalPositionService.assignPosition`: (External) Calculates and assigns a position for a new module within an ordered list of existing modules.
        *   `configs.CHANNEL_MODULE_LIMIT`: (External) A configuration variable specifying the maximum number of modules allowed in a channel.
        *   `AsyncHelper.fireAndForget`: (External) Executes an asynchronous function in the background without awaiting its completion.
        *   `deleteContentModule`: Deletes a content module from the database and manages cache invalidation.
*   **Example Usage**:
    ```typescript
    // Assuming imports for tracer, replicaConn, LexicalPositionService, configs, AsyncHelper

    const mockComponent = { id: 101, tagUUID: 'tag-1', semanticID: 'sem-A' };
    const mockChannels = [
      { tagUUID: 'tag-1', semanticID: 'sem-A', position: 1, isPositionLocked: true },
      { tagUUID: 'tag-2', semanticID: 'sem-B', position: 5, isPositionLocked: false },
    ];
    const mockArgs = { id: 'module-xyz', channels: mockChannels, scheduledDate: new Date() };

    async function runBuildComponentModuleExample() {
      try {
        const result = await buildComponentModule(mockArgs, mockComponent);
        console.log('Build Component Module Result:', result);
      } catch (error: any) {
        console.error('Error in buildComponentModule:', error.message);
      }
    }
    runBuildComponentModuleExample();
    ```

---

### Method Name: `deleteContentModule`

*   **Description**: Deletes a content module from the database identified by its ID and triggers cache invalidation for the deleted module.
*   **Call Stack**:
    *   **Called by**:
        *   `buildComponentModule`: Constructs the data for a `ComponentModule` record, determining its position within a channel and handling channel limits.
    *   **Calls**:
        *   `tracer.trace`: (External) Datadog tracing utility to instrument asynchronous operations.
        *   `prismaConn.getConn`: (External) Retrieves a Prisma client instance configured for the primary database connection.
        *   `prismaClient.module.delete`: (External) Prisma ORM method to delete a `Module` record by its unique identifier.
        *   `AsyncHelper.fireAndForget`: (External) Executes an asynchronous function in the background without awaiting its completion.
        *   `CacheHelper.expireCache`: (External) A utility to invalidate specific cache entries.
*   **Example Usage**:
    ```typescript
    // Assuming imports for tracer, prismaConn, AsyncHelper, CacheHelper

    async function runDeleteContentModuleExample() {
      const moduleIdToDelete = 'some-module-uuid-123';
      const result = await deleteContentModule({ id: moduleIdToDelete });

      if (result) {
        console.log(`Successfully deleted module with ID: ${result.id}`);
      } else {
        console.log(`Failed to delete module with ID: ${moduleIdToDelete} (it might not exist).`);
      }
    }
    runDeleteContentModuleExample();
    ```

---

### Method Name: `findOrGenerateWrapperId`

*   **Description**: Finds an existing `wrapperContentModuleId` for a given `contentId` and `contentType` of type 'standalone', or generates a new UUID if none is found.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `logger.warn`: (External) Logs a warning message through the configured logging system.
        *   `replicaConn.getConn`: (External) Retrieves a Prisma client instance configured for replica database reads.
        *   `Prisma.sql`: (External) Prisma utility to construct raw SQL queries securely.
        *   `prismaReplica.$queryRaw`: (External) Prisma ORM method to execute a raw SQL query directly against the database.
        *   `randomUUID`: (External) Node.js crypto module function to generate a random RFC 4122 UUID.
*   **Example Usage**:
    ```typescript
    // Assuming imports for logger, replicaConn, Prisma, randomUUID

    async function runFindOrGenerateWrapperIdExample() {
      // Example 1: Simulate finding an existing wrapper ID
      // (This would require a matching record in the database for 'existing-content' and 'article')
      const existingWrapper = await findOrGenerateWrapperId({
        contentId: 'existing-content-id-123',
        contentType: 'article',
      });
      console.log('Existing or generated wrapper ID (scenario 1):', existingWrapper);

      // Example 2: Likely generates a new wrapper ID if no match is found
      const newWrapper = await findOrGenerateWrapperId({
        contentId: 'new-content-id-456',
        contentType: 'video',
      });
      console.log('Existing or generated wrapper ID (scenario 2):', newWrapper);

      // Example 3: Invalid input, logs a warning and returns an empty string
      const invalidInput = await findOrGenerateWrapperId({});
      console.log('Wrapper ID for invalid input:', invalidInput);
    }
    runFindOrGenerateWrapperIdExample();
    ```

---

### Method Name: `updateChannelPinnedModule`

*   **Description**: Updates the pinned status of content modules within channels based on the provided `ComponentModule` configurations.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `TaxonomyConfigurationService.fetchPinned`: (External) Fetches the currently pinned item for a given tag and type from the Taxonomy Configuration Service.
        *   `TaxonomyConfigurationService.upsertPinned`: (External) Inserts or updates a pinned item in the Taxonomy Configuration Service.
        *   `PinnedType.ContentModule`: (External) An enum member specifying that the pinned item is a content module.
*   **Example Usage**:
    ```typescript
    // Assuming imports for TaxonomyConfigurationService and PinnedType

    async function runUpdateChannelPinnedModuleExample() {
      const contentModuleIdBeingProcessed = 'module-ABC-uuid';
      const componentModulesConfig = [
        { tagUUID: 'channel-A', semanticID: 'sem-1', isPinned: true }, // Pin module-ABC-uuid to channel-A
        { tagUUID: 'channel-B', semanticID: 'sem-2', isPinned: false }, // Unpin whatever is currently pinned to channel-B if it's module-ABC-uuid
        { tagUUID: 'channel-C', semanticID: 'sem-3', isPinned: true }, // Pin module-ABC-uuid to channel-C
      ];

      console.log('Updating channel pinned modules...');
      await updateChannelPinnedModule(componentModulesConfig, contentModuleIdBeingProcessed);
      console.log('Channel pinned modules update process completed.');
    }
    runUpdateChannelPinnedModuleExample();
    ```

---

### Method Name: `isAutoProgrammed`

*   **Description**: Checks if a content module was automatically programmed by a system service based on the `lastModifiedBy` field.
*   **Call Stack**:
    *   **Called by**: (Not identifiable from the provided code)
    *   **Calls**:
        *   `BR_ENV_SERVICE_MATCH_PATTERN.test`: (External) A regular expression method to test if a string matches the defined pattern for automated services.
*   **Example Usage**:
    ```typescript
    const autoProgrammedIdentifier = 'br:some_service:production';
    const manualUserIdentifier = 'john.doe@example.com';
    const emptyIdentifier = '';
    const nullIdentifier = null;

    console.log(`'${autoProgrammedIdentifier}' is auto-programmed: ${isAutoProgrammed(autoProgrammedIdentifier)}`); // true
    console.log(`'${manualUserIdentifier}' is auto-programmed: ${isAutoProgrammed(manualUserIdentifier)}`); // false
    console.log(`'${emptyIdentifier}' is auto-programmed: ${isAutoProgrammed(emptyIdentifier)}`); // false
    console.log(`'${nullIdentifier}' is auto-programmed: ${isAutoProgrammed(nullIdentifier as any)}`); // false
    ```