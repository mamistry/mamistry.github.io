Here is the comprehensive documentation for the methods found in the provided file.

---

### Method Name: `isValidUUID`

*   **Description**: Checks if a given string is a valid UUID (Universally Unique Identifier).
*   **Call Stack**:
    *   **Called by**:
        *   `ContentModuleRepository.fetchStandaloneContentModulesFromTagsTheUserFollow`: Used to validate each tag in the `followedTags` array.
    *   **Calls**: None.
*   **Example Usage**:

    ```typescript
    const isValid = ContentModuleRepository.isValidUUID('123e4567-e89b-12d3-a456-426614174000');
    console.log(isValid); // true

    const isNotValid = ContentModuleRepository.isValidUUID('not-a-uuid');
    console.log(isNotValid); // false
    ```

### Method Name: `fetchStandaloneContentModulesFromTagsTheUserFollow`

*   **Description**: Fetches standalone content modules associated with a user's followed tags, applying various filters and utilizing a cache for performance.
*   **Call Stack**:
    *   **Called by**: None identifiable from provided code.
    *   **Calls**:
        *   `ContentModuleRepository.isValidUUID`: Validates if a tag string is a legitimate UUID.
        *   `logger.info`: Logs informational messages (external).
        *   `CacheHelper.readCache`: Reads data from a cache (external).
        *   `replicaConn.getConn`: Retrieves a Prisma client connection (external).
        *   `Prisma.join`: Helper for safely joining array elements into a SQL string (external).
        *   `Prisma.sql`: A tagged template literal for constructing raw SQL queries (external).
        *   `prismaClient.$queryRaw`: Executes a raw SQL query against the database (external).
        *   `prismaClient.module.findMany`: Fetches multiple module records using Prisma ORM (external).
        *   `generateContentModuleIncludes`: Generates Prisma include clauses for fetching related data (external).
        *   `uniqBy`: Lodash utility to create a duplicate-free array based on an identifier (external).
        *   `CacheHelper.writeCache`: Writes data to a cache (external).
*   **Example Usage**:

    ```typescript
    import { SemanticID } from '@warnermediacode/graphql-base-enums';
    import { ContentModuleType } from '../../../graphql/generated/graphql';

    const followedTags = ['a9c7b204-1e0e-4f05-8b3d-7d8f5a6b0c1e', 'b2e1d3c4-5f6g-7h8i-9j0k-1l2m3n4o5p6q'];
    const includeContentTypes = [ContentModuleType.Article, ContentModuleType.Video];
    const excludeSemanticIDs = [SemanticID.ADVERTISEMENT];
    const semanticID = SemanticID.NEWS;
    const excludeContents = ['content-id-to-exclude'];

    const modules = await ContentModuleRepository.fetchStandaloneContentModulesFromTagsTheUserFollow(
      followedTags,
      includeContentTypes,
      excludeSemanticIDs,
      semanticID,
      excludeContents
    );
    console.log(`Fetched ${modules.length} content modules.`);
    ```

### Method Name: `fetchContentModulesByContentIdAndContentType`

*   **Description**: Retrieves content modules that match a specific content ID, content type, and a set of desired publication states.
*   **Call Stack**:
    *   **Called by**: None identifiable from provided code.
    *   **Calls**:
        *   `ContentModuleRepository.generateStatesFilter`: Generates a SQL filter condition based on desired content states.
        *   `replicaConn.getConn`: Retrieves a Prisma client connection (external).
        *   `Prisma.sql`: A tagged template literal for constructing raw SQL queries (external).
        *   `prismaClient.$queryRaw`: Executes a raw SQL query against the database (external).
        *   `prismaClient.module.findMany`: Fetches multiple module records using Prisma ORM (external).
        *   `generateContentModuleIncludes`: Generates Prisma include clauses for fetching related data (external).
*   **Example Usage**:

    ```typescript
    import { States } from '../../models/types';

    const contentType = 'VideoV2';
    const contentId = 'a1b2c3d4-e5f6-7g8h-9i0j-1k2l3m4n5o6p';
    const desiredStates = [States.PROGRAMMED, States.SCHEDULED];

    const modules = await ContentModuleRepository.fetchContentModulesByContentIdAndContentType(
      contentType,
      contentId,
      desiredStates
    );
    console.log(`Found ${modules.length} modules for content ID ${contentId}.`);
    ```

### Method Name: `fetchTweetsForHomeCommunityCollection`

*   **Description**: Fetches unique Tweet content modules for a specific tag within a given time frame, filtered by desired publication states and ordered by insertion time.
*   **Call Stack**:
    *   **Called by**: None identifiable from provided code.
    *   **Calls**:
        *   `ContentModuleRepository.generateStatesFilter`: Generates a SQL filter condition based on desired content states.
        *   `replicaConn.getConn`: Retrieves a Prisma client connection (external).
        *   `Prisma.sql`: A tagged template literal for constructing raw SQL queries (external).
        *   `prismaReplica.$queryRaw`: Executes a raw SQL query against the database (external).
        *   `prismaReplica.module.findMany`: Fetches multiple module records using Prisma ORM (external).
        *   `generateContentModuleIncludes`: Generates Prisma include clauses for fetching related data (external).
*   **Example Usage**:

    ```typescript
    import { States } from '../../models/types';

    const tagUUID = 'tag-uuid-for-tweets';
    const desiredStates = [States.PROGRAMMED];
    const timeAgo = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000); // Tweets from the last 7 days
    const limit = 20;

    const tweets = await ContentModuleRepository.fetchTweetsForHomeCommunityCollection(
      tagUUID,
      desiredStates,
      timeAgo,
      limit
    );
    console.log(`Fetched ${tweets.length} tweets for tag ${tagUUID}.`);
    ```

### Method Name: `fetchVideosForHomeCommunityCollection`

*   **Description**: Fetches unique landscape-oriented VideoV2 content modules for a specific tag within a given time frame, filtered by desired publication states and ordered by insertion time.
*   **Call Stack**:
    *   **Called by**: None identifiable from provided code.
    *   **Calls**:
        *   `ContentModuleRepository.generateStatesFilter`: Generates a SQL filter condition based on desired content states.
        *   `replicaConn.getConn`: Retrieves a Prisma client connection (external).
        *   `Prisma.sql`: A tagged template literal for constructing raw SQL queries (external).
        *   `prismaReplica.$queryRaw`: Executes a raw SQL query against the database (external).
        *   `prismaReplica.module.findMany`: Fetches multiple module records using Prisma ORM (external).
        *   `generateContentModuleIncludes`: Generates Prisma include clauses for fetching related data (external).
*   **Example Usage**:

    ```typescript
    import { States } from '../../models/types';

    const tagUUID = 'tag-uuid-for-videos';
    const desiredStates = [States.PROGRAMMED, States.SCHEDULED];
    const timeAgo = new Date(Date.now() - 30 * 24 * 60 * 60 * 1000); // Videos from the last 30 days
    const limit = 15;

    const videos = await ContentModuleRepository.fetchVideosForHomeCommunityCollection(
      tagUUID,
      desiredStates,
      timeAgo,
      limit
    );
    console.log(`Fetched ${videos.length} videos for tag ${tagUUID}.`);
    ```

### Method Name: `fetchArticlesForHomeCommunityCollection`

*   **Description**: Fetches unique Article content modules for a specific tag within a given time frame, filtered by desired publication states and ordered by insertion time.
*   **Call Stack**:
    *   **Called by**: None identifiable from provided code.
    *   **Calls**:
        *   `ContentModuleRepository.generateStatesFilter`: Generates a SQL filter condition based on desired content states.
        *   `replicaConn.getConn`: Retrieves a Prisma client connection (external).
        *   `Prisma.sql`: A tagged template literal for constructing raw SQL queries (external).
        *   `prismaReplica.$queryRaw`: Executes a raw SQL query against the database (external).
        *   `prismaReplica.module.findMany`: Fetches multiple module records using Prisma ORM (external).
        *   `generateContentModuleIncludes`: Generates Prisma include clauses for fetching related data (external).
*   **Example Usage**:

    ```typescript
    import { States } from '../../models/types';

    const tagUUID = 'tag-uuid-for-articles';
    const desiredStates = [States.PROGRAMMED];
    const timeAgo = new Date(Date.now() - 14 * 24 * 60 * 60 * 1000); // Articles from the last 14 days
    const limit = 10;

    const articles = await ContentModuleRepository.fetchArticlesForHomeCommunityCollection(
      tagUUID,
      desiredStates,
      timeAgo,
      limit
    );
    console.log(`Fetched ${articles.length} articles for tag ${tagUUID}.`);
    ```

### Method Name: `fetchPackagesByTitle`

*   **Description**: Fetches content modules classified as packages (composite modules) that have titles matching a given string, filtered by desired publication states.
*   **Call Stack**:
    *   **Called by**: None identifiable from provided code.
    *   **Calls**:
        *   `ContentModuleRepository.generateStatesFilter`: Generates a SQL filter condition based on desired content states.
        *   `replicaConn.getConn`: Retrieves a Prisma client connection (external).
        *   `Prisma.sql`: A tagged template literal for constructing raw SQL queries (external).
        *   `prismaReplica.$queryRaw`: Executes a raw SQL query against the database (external).
        *   `prismaReplica.module.findMany`: Fetches multiple module records using Prisma ORM (external).
        *   `generateContentModuleIncludes`: Generates Prisma include clauses for fetching related data (external).
*   **Example Usage**:

    ```typescript
    import { States } from '../../models/types';

    const titleSearch = 'weekly recap';
    const desiredStates = [States.PROGRAMMED];

    const packages = await ContentModuleRepository.fetchPackagesByTitle(
      titleSearch,
      desiredStates
    );
    console.log(`Found ${packages.length} packages matching title '${titleSearch}'.`);
    ```

### Method Name: `generateStatesFilter`

*   **Description**: Dynamically constructs a SQL `WHERE` clause fragment to filter content modules based on a list of desired publication states (SCHEDULED, PROGRAMMED, UNPROGRAMMED).
*   **Call Stack**:
    *   **Called by**:
        *   `ContentModuleRepository.fetchContentModulesByContentIdAndContentType`: Used to include state filtering in the raw SQL query.
        *   `ContentModuleRepository.fetchTweetsForHomeCommunityCollection`: Used to include state filtering in the raw SQL query.
        *   `ContentModuleRepository.fetchVideosForHomeCommunityCollection`: Used to include state filtering in the raw SQL query.
        *   `ContentModuleRepository.fetchArticlesForHomeCommunityCollection`: Used to include state filtering in the raw SQL query.
        *   `ContentModuleRepository.fetchPackagesByTitle`: Used to include state filtering in the raw SQL query.
    *   **Calls**:
        *   `Prisma.sql`: A tagged template literal for constructing raw SQL query parts (external).
        *   `Prisma.join`: Helper for safely joining an array of SQL parts with an `OR` operator (external).
*   **Example Usage**:

    ```typescript
    import { States } from '../../models/types';

    // Filter for currently programmed content
    const programmedFilter = ContentModuleRepository.generateStatesFilter([States.PROGRAMMED]);
    // Example output (Prisma.Sql object): (COALESCE(m."scheduledDate", '1970-01-01') <= '2023-10-27T10:00:00.000Z' AND COALESCE(m."expiresAt", '9999-12-31') > '2023-10-27T10:00:00.000Z')

    // Filter for scheduled or unprogrammed content
    const mixedFilter = ContentModuleRepository.generateStatesFilter([States.SCHEDULED, States.UNPROGRAMMED]);
    // Example output (Prisma.Sql object): (COALESCE(m."scheduledDate", '1970-01-01 00:00:00') >= '2023-10-27T10:00:00.000Z') OR (COALESCE(m."expiresAt", '9999-12-31 23:59:59') <= '2023-10-27T10:00:00.000Z')

    // No specific state filter (returns a '1=1' condition)
    const allStatesFilter = ContentModuleRepository.generateStatesFilter([]);
    // Example output (Prisma.Sql object): 1=1
    ```