Here is the comprehensive documentation for the methods and functions found in the provided file.

---

### Method Name: `constructor`

*   **Description**: Initializes the `TagDataContentResolverService` instance, establishing connections to Prisma and Redis and binding specific methods to the instance.
*   **Call Stack**:
    *   **Called by**:
        *   `tagDataContentResolverService` (global instance creation): Creates a singleton instance of this service.
    *   **Calls**:
        *   `PrismaConn.getInstance(true)`: Gets a singleton instance of the Prisma database client.
        *   `RedisClient.getInstance()`: Gets a singleton instance of the Redis client for caching.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    // This is typically handled by the global export:
    // export const tagDataContentResolverService = new TagDataContentResolverService();
    ```

---

### Method Name: `collectTagUUIDs`

*   **Description**: Extracts and filters valid UUIDs from an array of optional `TagV2` objects, ensuring only existing UUIDs are returned.
*   **Call Stack**:
    *   **Called by**:
        *   `resolveGamecastContentBase`: Collects UUIDs from team tags to be used in database queries.
    *   **Calls**: None (uses standard array methods like `filter` and `map`).
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const teamTags = [
        { uuid: 'tag-1-uuid', name: 'Team A', type: TagTypeV2.Team },
        null,
        { uuid: 'tag-2-uuid', name: 'Team B', type: TagTypeV2.Team }
    ];
    const uuids = service.collectTagUUIDs(teamTags);
    // Expected output: ['tag-1-uuid', 'tag-2-uuid']
    ```

---

### Method Name: `removeDuplicates`

*   **Description**: Filters an array of `TContentModule` objects to remove duplicates based on their `contentId` property.
*   **Call Stack**:
    *   **Called by**:
        *   `resolveGamecastContentBase`: Cleans up the list of fetched content modules by removing any duplicates before returning them.
    *   **Calls**: None.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const modules = [
        { contentId: 'module-a', type: ContentModuleType.Article },
        { contentId: 'module-b', type: ContentModuleType.VideoV2 },
        { contentId: 'module-a', type: ContentModuleType.Article } // Duplicate
    ];
    const uniqueModules = service.removeDuplicates(modules);
    // Expected output: [{ contentId: 'module-a', ... }, { contentId: 'module-b', ... }]
    ```

---

### Method Name: `buildTweetContentQuery`

*   **Description**: Constructs a Prisma SQL query specifically designed to retrieve 'Tweet' type content modules associated with given team tags and within a specified time range.
*   **Call Stack**:
    *   **Called by**:
        *   `buildSportsContentQuery`: Delegates to this method when the `contentTypeFilter` is 'Tweet'.
    *   **Calls**:
        *   `Prisma.sql`: (External) Prisma's tagged template literal for building raw SQL queries safely.
        *   `Prisma.join`: (External) Prisma's utility for safely joining an array of values into an SQL list.
        *   `this.buildTimeFilter`: Generates the time-based `WHERE` clause for the SQL query.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const queryParams = {
        teamTagUUIDs: ['team-tag-1-uuid'],
        startTime: new Date('2023-10-26T00:00:00Z'),
        endTime: new Date('2023-10-27T00:00:00Z'),
        resultLimit: 5
    };
    const sqlQuery = service.buildTweetContentQuery(queryParams);
    // sqlQuery is a Prisma.Sql object representing the raw SQL.
    ```

---

### Method Name: `buildTimeFilter`

*   **Description**: Generates a Prisma SQL `WHERE` clause string for filtering content modules based on a start time and an optional end time, accounting for game start and end buffers.
*   **Call Stack**:
    *   **Called by**:
        *   `buildTweetContentQuery`: Integrates the time filter into the Tweet content query.
        *   `buildGamecastHomeContentQuery`: Integrates the time filter into the gamecast home content query.
    *   **Calls**:
        *   `new Date()`: (Native JS) Creates a new `Date` object.
        *   `Date.getTime()`: (Native JS) Gets the time value in milliseconds.
        *   `Date.toISOString()`: (Native JS) Converts the `Date` object to an ISO 8601 string.
        *   `Prisma.sql`: (External) Prisma's tagged template literal for building raw SQL queries safely.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const startTime = new Date('2023-10-26T15:00:00Z');
    const endTime = new Date('2023-10-26T18:00:00Z');
    const timeFilterSql = service.buildTimeFilter(startTime, endTime);
    // timeFilterSql is a Prisma.Sql object, e.g., "AND m."programmingUpdatedAt" BETWEEN '...' AND '...'"
    ```

---

### Method Name: `buildGamecastHomeContentQuery`

*   **Description**: Constructs a Prisma SQL query for fetching a diverse set of content modules (excluding certain Tweet types) relevant to a gamecast home feed, based on team tags, time, and content criteria.
*   **Call Stack**:
    *   **Called by**:
        *   `buildSportsContentQuery`: Delegates to this method when the `contentTypeFilter` is 'All'.
    *   **Calls**:
        *   `Prisma.sql`: (External) Prisma's tagged template literal for building raw SQL queries safely.
        *   `Prisma.join`: (External) Prisma's utility for safely joining an array of values into an SQL list.
        *   `this.buildTimeFilter`: Generates the time-based `WHERE` clause for the SQL query.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const queryParams = {
        teamTagUUIDs: ['team-tag-1-uuid', 'team-tag-2-uuid'],
        startTime: new Date('2023-10-26T10:00:00Z'),
        endTime: undefined, // Fetching content until now
        resultLimit: 20
    };
    const sqlQuery = service.buildGamecastHomeContentQuery(queryParams);
    // sqlQuery is a Prisma.Sql object representing the raw SQL.
    ```

---

### Method Name: `buildSportsContentQuery`

*   **Description**: Serves as a dispatcher to select and build the appropriate content query (Tweet-specific or general gamecast home) based on the `contentTypeFilter` parameter.
*   **Call Stack**:
    *   **Called by**:
        *   `resolveGamecastContentBase`: Determines which specific query builder to invoke based on the requested content type.
    *   **Calls**:
        *   `this.buildTweetContentQuery`: Builds a SQL query for Tweet content.
        *   `this.buildGamecastHomeContentQuery`: Builds a SQL query for a broader range of gamecast content.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const teamUUIDs = ['some-team-uuid'];
    const now = new Date();
    const tweetQuery = service.buildSportsContentQuery(teamUUIDs, now, undefined, 5, 'Tweet');
    const allContentQuery = service.buildSportsContentQuery(teamUUIDs, now, now, 10, 'All');
    // tweetQuery and allContentQuery are Prisma.Sql objects.
    ```

---

### Method Name: `resolveGamecastContentBase`

*   **Description**: The core method for resolving and retrieving content modules for a `GameCast` tag, handling validation, cache checks, database queries, and post-processing like deduplication and caching.
*   **Call Stack**:
    *   **Called by**:
        *   `resolveGamecastSocialContent`: Resolves social (Tweet) content by calling this base method with `contentTypeFilter: 'Tweet'`.
        *   `resolveGamecastContent`: Resolves all general gamecast content by calling this base method with `contentTypeFilter: 'All'`.
    *   **Calls**:
        *   `logger.warn`: (External) Logs warnings for invalid tag data or missing UUIDs.
        *   `this.collectTagUUIDs`: Gathers valid team tag UUIDs from the `tagData`.
        *   `CacheHelper.readCache`: (External) Attempts to retrieve content from Redis cache.
        *   `this._replicaConn.getConn()`: Obtains the Prisma replica database connection.
        *   `this.buildSportsContentQuery`: Constructs the appropriate SQL query (Tweet or All content).
        *   `prismaReplica.$queryRaw`: (External) Executes the raw SQL query against the database.
        *   `prismaReplica.module.findMany`: (External) Fetches detailed module data from the database.
        *   `generateContentModuleIncludes`: (External) Provides Prisma include options for related module data.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel`: (External) Transforms database results into `TContentModule` objects.
        *   `this.removeDuplicates`: Eliminates any duplicate content modules found.
        *   `CacheHelper.writeCache`: (External) Stores the resolved content modules in Redis cache.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const gamecastTag: TagV2 = {
        uuid: 'game-uuid-1',
        type: TagTypeV2.GameCast,
        startTime: new Date('2023-10-26T10:00:00Z'),
        endTime: new Date('2023-10-26T13:00:00Z'),
        children: [{ uuid: 'team-uuid-1', name: 'Team Alpha', type: TagTypeV2.Team }],
        parent: null,
        name: 'Game Alpha'
    };
    const resolvedContent = await service.resolveGamecastContentBase(
        gamecastTag,
        SemanticId.ContentCommunityFeed,
        {
            contentTypeFilter: 'All',
            cacheFeature: 'GAMECAST_COMMUNITY_FEED',
            limit: 10,
            minItems: 3
        }
    );
    // resolvedContent is an array of TContentModule objects.
    ```

---

### Method Name: `resolveGamecastSocialContent`

*   **Description**: Retrieves social media (Tweet) content modules for a given `GameCast` tag, using `resolveGamecastContentBase` with specific parameters for social content and caching.
*   **Call Stack**:
    *   **Called by**: (Likely an external GraphQL resolver or API endpoint)
    *   **Calls**:
        *   `this.resolveGamecastContentBase`: The underlying method that performs the content resolution logic.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const gamecastTag: TagV2 = {
        uuid: 'game-uuid-2',
        type: TagTypeV2.GameCast,
        startTime: new Date('2023-10-26T10:00:00Z'),
        endTime: new Date('2023-10-26T13:00:00Z'),
        children: [{ uuid: 'team-uuid-2', name: 'Team Beta', type: TagTypeV2.Team }],
        parent: null,
        name: 'Game Beta'
    };
    const socialContent = await service.resolveGamecastSocialContent(
        gamecastTag,
        5, // limit
        SemanticId.ContentWhatsBuzzing,
        2  // minItems
    );
    // socialContent is an array of TContentModule objects.
    ```

---

### Method Name: `resolveGamecastContent`

*   **Description**: Retrieves a broad range of content modules for a given `GameCast` tag, utilizing `resolveGamecastContentBase` with parameters configured for general gamecast content and caching.
*   **Call Stack**:
    *   **Called by**: (Likely an external GraphQL resolver or API endpoint)
    *   **Calls**:
        *   `this.resolveGamecastContentBase`: The underlying method that performs the content resolution logic.
*   **Example Usage**:
    ```typescript
    const service = new TagDataContentResolverService();
    const gamecastTag: TagV2 = {
        uuid: 'game-uuid-3',
        type: TagTypeV2.GameCast,
        startTime: new Date('2023-10-26T10:00:00Z'),
        endTime: new Date('2023-10-26T13:00:00Z'),
        children: [{ uuid: 'team-uuid-3', name: 'Team Gamma', type: TagTypeV2.Team }],
        parent: null,
        name: 'Game Gamma'
    };
    const allGamecastContent = await service.resolveGamecastContent(
        gamecastTag,
        15, // limit
        SemanticId.ContentCommunityFeed
    );
    // allGamecastContent is an array of TContentModule objects.
    ```