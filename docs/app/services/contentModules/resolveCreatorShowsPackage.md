Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### `fetchContentMetadata`

1.  **Method Name**: `fetchContentMetadata`
2.  **Description**: Fetches video metadata for a given content ID from the database using a replica connection.
3.  **Call Stack**:
    *   **Called by**:
        *   `processContent`: Processes a single content module, including fetching its metadata.
    *   **Calls**:
        *   `replicaConn.getConn()`: (External) Obtains a Prisma client instance from the replica connection.
        *   `prismaClient.videoMetadata.findFirst()`: (External) Queries the database to find the first video metadata entry matching the content ID.
4.  **Example Usage**:

    ```typescript
    const contentId = 'some-unique-content-id';
    const metadata = await fetchContentMetadata(contentId);
    if (metadata) {
        console.log(`Found video metadata for ${contentId}: ${metadata.title}`);
    }
    ```

---

### `filterCreatorShows`

1.  **Method Name**: `filterCreatorShows`
2.  **Description**: Filters and resolves a list of standalone content modules based on user tags and a specific tab, differentiating between home tab and other channels.
3.  **Call Stack**:
    *   **Called by**: (No calls identifiable within the provided file, likely an external API endpoint or service.)
    *   **Calls**:
        *   `resolveHomeTabCreatorShowsPackage`: Processes content modules for the home tab package.
        *   `resolveCreatorShowsFollowed`: Resolves creator shows based on user-followed tags.
        *   `resolveNonHomeTabChannels`: Processes content modules for non-home tab channels.
        *   `logger.error()`: (External) Logs error messages.
4.  **Example Usage**:

    ```typescript
    const contentModules = [/* array of StandaloneContentModule */];
    const tagsDataHomeTab = { tagSlug: 'home-tab', userTags: [] };
    const tagsDataFollowed = { tagSlug: 'home-tab', userTags: [{ uuid: 'user-tag-uuid', type: TagTypeV2.Creator }] };
    const tagsDataOther = { tagSlug: 'explore', userTags: [] };
    const statesFilter = { key: 'videoState', value: 'LIVE' };
    const semanticID = 'CREATOR_SHOWS';

    const homeTabShows = await filterCreatorShows(contentModules, tagsDataHomeTab, statesFilter, semanticID);
    const followedShows = await filterCreatorShows(contentModules, tagsDataFollowed, statesFilter, semanticID);
    const otherChannelShows = await filterCreatorShows(contentModules, tagsDataOther, statesFilter, semanticID);
    ```

---

### `resolveHomeTabCreatorShowsPackage`

1.  **Method Name**: `resolveHomeTabCreatorShowsPackage`
2.  **Description**: Processes a list of standalone content modules, applying a date filter and limiting the results to the top 10 for the home tab creator shows package.
3.  **Call Stack**:
    *   **Called by**:
        *   `filterCreatorShows`: Routes home tab requests to this resolver.
    *   **Calls**:
        *   `processContentModules`: Processes and enriches a list of content modules.
        *   `logger.error()`: (External) Logs error messages.
4.  **Example Usage**:

    ```typescript
    const initialContentModules = [/* array of StandaloneContentModule */];
    const threeDaysAgo = new Date(Date.now() - 3 * 24 * 60 * 60 * 1000);
    const stateFilter = { key: 'videoState', value: 'VOD' };

    const homeTabPackage = await resolveHomeTabCreatorShowsPackage(initialContentModules, threeDaysAgo, stateFilter);
    console.log(`Resolved ${homeTabPackage.length} content modules for home tab.`);
    ```

---

### `resolveNonHomeTabChannels`

1.  **Method Name**: `resolveNonHomeTabChannels`
2.  **Description**: Resolves content modules for non-home tab creator channels, applying a date filter and limiting results, with special handling for 'LiveNow' or 'LiveAndUpcoming' semantic IDs.
3.  **Call Stack**:
    *   **Called by**:
        *   `filterCreatorShows`: Routes non-home tab requests to this resolver.
    *   **Calls**:
        *   `processContentModules`: Processes and enriches a list of content modules.
4.  **Example Usage**:

    ```typescript
    const contentModules = [/* array of StandaloneContentModule */];
    const stateFilter = {};
    const semanticID_live = 'LIVE_NOW'; // Example for LiveNow
    const semanticID_other = 'RECENT_VIDEOS';

    const liveChannels = await resolveNonHomeTabChannels(contentModules, stateFilter, semanticID_live);
    const otherChannels = await resolveNonHomeTabChannels(contentModules, stateFilter, semanticID_other);
    ```

---

### `resolveCreatorShowsFollowed`

1.  **Method Name**: `resolveCreatorShowsFollowed`
2.  **Description**: Retrieves and filters creator show content modules based on a user's followed creator tags and a recent date, excluding already existing content.
3.  **Call Stack**:
    *   **Called by**:
        *   `filterCreatorShows`: Routes home tab requests with user tags to this resolver.
    *   **Calls**:
        *   `replicaConn.getConn()`: (External) Obtains a Prisma client instance from the replica connection.
        *   `Prisma.join()`: (External) Helper for safely joining array elements in raw SQL queries.
        *   `prismaClient.$queryRaw()`: (External) Executes a raw SQL query to fetch module IDs.
        *   `prismaClient.module.findMany()`: (External) Fetches module records based on IDs.
        *   `generateContentModuleIncludes()`: (External) Generates Prisma include options for content modules.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel()`: (External) Maps database results to content module models.
        *   `processContent`: Processes a single content module.
5.  **Example Usage**:

    ```typescript
    const userTags = [{ uuid: 'creator-uuid-1', type: TagTypeV2.Creator }, { uuid: 'creator-uuid-2', type: TagTypeV2.Creator }];
    const threeDaysAgo = new Date(Date.now() - 3 * 24 * 60 * 60 * 1000);
    const existingContents = [/* array of TContentModule to exclude */];

    const followedShows = await resolveCreatorShowsFollowed(userTags, threeDaysAgo, existingContents);
    console.log(`Found ${followedShows.length} followed creator shows.`);
    ```

---

### `processContent`

1.  **Method Name**: `processContent`
2.  **Description**: Processes a single standalone content module by fetching its related video metadata and enriching the module with video state if it's 'VOD' or 'LIVE'.
3.  **Call Stack**:
    *   **Called by**:
        *   `resolveCreatorShowsFollowed`: Processes content modules retrieved from the database.
        *   `processContentModules`: Processes existing and newly fetched content modules.
        *   `fetchRecentCreatorShowsContentGeneric`: Processes recently fetched content modules.
    *   **Calls**:
        *   `fetchContentMetadata`: Fetches video metadata for a given content ID.
4.  **Example Usage**:

    ```typescript
    const contentModule = {
        id: 'module-id-1',
        contentId: 'video-id-1',
        type: 'standalone',
        contentType: 'VideoV2',
        insertedAt: new Date(),
        updatedAt: new Date(),
        contentMetadata: {}
    };
    const processed = await processContent(contentModule);
    if (processed) {
        console.log(`Processed content with video state: ${processed.contentMetadata?.video?.state}`);
    } else {
        console.log('Content was filtered out or had no relevant video state.');
    }
    ```

---

### `processContentModules`

1.  **Method Name**: `processContentModules`
2.  **Description**: Iterates through a list of standalone content modules, processes them, fetches additional recent content, combines the lists, and sorts the final collection.
3.  **Call Stack**:
    *   **Called by**:
        *   `resolveHomeTabCreatorShowsPackage`: Processes home tab content modules.
        *   `resolveNonHomeTabChannels`: Processes non-home tab content modules.
    *   **Calls**:
        *   `processContent`: Processes a single content module.
        *   `fetchRecentCreatorShowsContentGeneric`: Fetches additional recent creator shows.
        *   `sortCreatorContent`: Sorts the combined list of content modules.
4.  **Example Usage**:

    ```typescript
    const existingContentModules = [/* array of TStandaloneContentModule */];
    const stateFilter = { key: 'state', value: 'LIVE' };
    const sinceDate = new Date(Date.now() - 24 * 60 * 60 * 1000); // Last 24 hours

    const finalModules = await processContentModules(existingContentModules, stateFilter, sinceDate);
    console.log(`Total processed modules: ${finalModules.length}`);
    ```

---

### `fetchRecentCreatorShowsContentGeneric`

1.  **Method Name**: `fetchRecentCreatorShowsContentGeneric`
2.  **Description**: Fetches recent standalone content modules of type 'VideoV2' from the database that were inserted after a specified date, excluding already existing content.
3.  **Call Stack**:
    *   **Called by**:
        *   `processContentModules`: Fetches additional content to supplement existing modules.
    *   **Calls**:
        *   `replicaConn.getConn()`: (External) Obtains a Prisma client instance from the replica connection.
        *   `Prisma.join()`: (External) Helper for safely joining array elements in raw SQL queries.
        *   `prismaClient.$queryRaw()`: (External) Executes a raw SQL query to fetch module IDs.
        *   `prismaClient.module.findMany()`: (External) Fetches module records based on IDs.
        *   `generateContentModuleIncludes()`: (External) Generates Prisma include options for content modules.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel()`: (External) Maps database results to content module models.
        *   `processContent`: Processes a single content module.
4.  **Example Usage**:

    ```typescript
    const sinceDate = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000); // Last 7 days
    const existingContentModules = [{ id: 'exclude-id-1', /* ... */ }];

    const recentModules = await fetchRecentCreatorShowsContentGeneric(sinceDate, existingContentModules);
    console.log(`Fetched ${recentModules.length} recent creator shows.`);
    ```

---

### `sortCreatorContent`

1.  **Method Name**: `sortCreatorContent`
2.  **Description**: Sorts an array of standalone content modules, prioritizing live content at the top and then sorting by their insertion date in descending order.
3.  **Call Stack**:
    *   **Called by**:
        *   `processContentModules`: Sorts the combined list of content modules before returning.
    *   **Calls**:
        *   `isLive`: Checks if a content module is currently live.
4.  **Example Usage**:

    ```typescript
    const modulesToSort = [
        { id: '3', insertedAt: new Date('2023-01-05'), contentMetadata: { video: { state: 'VOD' } } },
        { id: '1', insertedAt: new Date('2023-01-01'), contentMetadata: { video: { state: 'LIVE' } } },
        { id: '2', insertedAt: new Date('2023-01-03'), contentMetadata: { video: { state: 'VOD' } } },
    ];
    const sortedModules = sortCreatorContent(modulesToSort);
    // Expected order: [module with id '1' (LIVE), module with id '3' (VOD, newer), module with id '2' (VOD, older)]
    console.log('Sorted modules:', sortedModules.map(m => m.id));
    ```

---

### `isLive`

1.  **Method Name**: `isLive`
2.  **Description**: Checks if a given standalone content module represents live video content by inspecting its video state.
3.  **Call Stack**:
    *   **Called by**:
        *   `sortCreatorContent`: Used as a comparison helper during content module sorting.
    *   **Calls**: (None)
4.  **Example Usage**:

    ```typescript
    const liveModule = { id: 'live-1', contentMetadata: { video: { state: 'LIVE' } } };
    const vodModule = { id: 'vod-1', contentMetadata: { video: { state: 'VOD' } } };
    const pendingModule = { id: 'pending-1', contentMetadata: { video: { state: 'SCHEDULED' } } };
    const noVideoModule = { id: 'no-video', contentMetadata: {} };

    console.log(`Is liveModule live? ${isLive(liveModule)}`); // true
    console.log(`Is vodModule live? ${isLive(vodModule)}`);   // false
    console.log(`Is pendingModule live? ${isLive(pendingModule)}`); // false
    console.log(`Is noVideoModule live? ${isLive(noVideoModule)}`); // false
    ```