Here is the comprehensive documentation for the methods found in the provided file:

---

## `TaxonomyConfigurationService.fetchPinned`

*   **Description**: Returns the currently pinned content module identifier for a given channel, prioritizing a cached value.
*   **Call Stack**:
    *   **Calls**:
        *   `CacheHelper.readCache<string>`: Reads a value from the cache.
        *   `replicaConn.getConn()`: Gets the Prisma client connection from the `replicaConn` instance, typically used for read operations.
        *   `prismaReplica.taxonomyConfiguration.findUnique()`: Finds a unique record in the `taxonomyConfiguration` table based on the provided criteria.
        *   `CacheHelper.writeCache`: Writes a value to the cache.
*   **Example Usage**:
    ```typescript
    import TaxonomyConfigurationService, { PinnedType } from './TaxonomyConfigurationService';

    async function getPinnedContent(channelUUID: string) {
      // Fetch pinned content module for a channel, using cache by default
      const pinnedId = await TaxonomyConfigurationService.fetchPinned(channelUUID);
      if (pinnedId) {
        console.log(`Pinned content module for ${channelUUID}: ${pinnedId}`);
      } else {
        console.log(`No content module pinned for ${channelUUID}.`);
      }

      // Fetch, ignoring cache
      const pinnedIdWithoutCache = await TaxonomyConfigurationService.fetchPinned(
        channelUUID,
        PinnedType.ContentModule,
        { ignoreCache: true }
      );
      console.log(`Pinned ID (ignoring cache): ${pinnedIdWithoutCache}`);
    }

    getPinnedContent('channel-abc-123');
    ```

## `TaxonomyConfigurationService.upsertPinned`

*   **Description**: Persists or updates a pinned content module identifier for the provided channel and invalidates the cache for that channel.
*   **Call Stack**:
    *   **Calls**:
        *   `PrismaConn.getInstance()`: Gets the singleton instance of the Prisma client, typically used for write operations.
        *   `prisma.taxonomyConfiguration.upsert()`: Inserts a new record or updates an existing one in the `taxonomyConfiguration` table.
        *   `CacheHelper.expireCache()`: Invalidates a specific cache key.
*   **Example Usage**:
    ```typescript
    import TaxonomyConfigurationService, { PinnedType } from './TaxonomyConfigurationService';

    async function managePinnedContent(channelUUID: string, contentId: string | null) {
      // Pin a content module
      await TaxonomyConfigurationService.upsertPinned(channelUUID, contentId);
      console.log(`Content module "${contentId}" has been pinned for ${channelUUID}.`);

      // Unpin a content module
      await TaxonomyConfigurationService.upsertPinned(channelUUID, null, PinnedType.ContentModule);
      console.log(`Content module has been unpinned for ${channelUUID}.`);
    }

    managePinnedContent('channel-def-456', 'content-xyz-789');
    ```

## `TaxonomyConfigurationService.upsertTagInformation`

*   **Description**: Creates or updates a record of tag-specific information (type and weight) in the taxonomy configuration.
*   **Call Stack**:
    *   **Calls**:
        *   `PrismaConn.getInstance()`: Gets the singleton instance of the Prisma client, typically used for write operations.
        *   `prisma.taxonomyConfiguration.upsert()`: Inserts a new record or updates an existing one in the `taxonomyConfiguration` table.
*   **Example Usage**:
    ```typescript
    import TaxonomyConfigurationService from './TaxonomyConfigurationService';

    async function updateTagDetails(tagUUID: string) {
      // Set initial tag type and weight
      await TaxonomyConfigurationService.upsertTagInformation(tagUUID, 'CategoryA', 100);
      console.log(`Tag ${tagUUID} information updated.`);

      // Update only the tag weight
      await TaxonomyConfigurationService.upsertTagInformation(tagUUID, undefined, 150);
      console.log(`Tag ${tagUUID} weight updated.`);
    }

    updateTagDetails('tag-id-123');
    ```

## `TaxonomyConfigurationService.fetchTagInformation`

*   **Description**: Retrieves tag-specific configuration information from the database, leveraging a cache for performance.
*   **Call Stack**:
    *   **Calls**:
        *   `CacheHelper.readCache<TaxonomyConfiguration>`: Reads a value from the cache.
        *   `replicaConn.getConn()`: Gets the Prisma client connection from the `replicaConn` instance, typically used for read operations.
        *   `prismaReplica.taxonomyConfiguration.findUnique()`: Finds a unique record in the `taxonomyConfiguration` table based on the provided criteria.
        *   `CacheHelper.writeCache`: Writes a value to the cache.
*   **Example Usage**:
    ```typescript
    import TaxonomyConfigurationService from './TaxonomyConfigurationService';

    async function getTagConfiguration(tagUUID: string) {
      // Fetch tag configuration, using cache by default
      const config = await TaxonomyConfigurationService.fetchTagInformation(tagUUID);
      if (config) {
        console.log(`Configuration for ${tagUUID}:`, config);
      } else {
        console.log(`No configuration found for ${tagUUID}.`);
      }

      // Fetch, ignoring cache
      const configWithoutCache = await TaxonomyConfigurationService.fetchTagInformation(
        tagUUID,
        { ignoreCache: true }
      );
      console.log(`Configuration (ignoring cache) for ${tagUUID}:`, configWithoutCache);
    }

    getTagConfiguration('tag-id-456');
    ```