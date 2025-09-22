This document provides comprehensive technical documentation for the methods and functions found within the provided file, structured for clarity and ease of understanding.

---

### `serializeContentModule`

1.  **Method Name**: `serializeContentModule`
2.  **Description**: Serializes a single content module or a list of content modules, applying geo-restrictions and fetching associated metadata before transforming them into a standardized format.
3.  **Call Stack**:
    *   **Called By**: (Not identifiable from the provided file)
    *   **Calls**:
        *   `applyGeoRestriction`: Filters content modules based on geographic restrictions.
        *   `fetchMetadata`: Retrieves video and external article metadata for the given content modules.
        *   `doSerialize`: Performs the core serialization logic for a single content module.
4.  **Example Usage**:

    ```typescript
    import { serializeContentModule } from './path/to/your/file';

    // Example with a single content module
    const singleModule = {
      /* ... module data ... */
      allowedCountries: ['us', 'ca'],
    };
    const serializedSingle = await serializeContentModule(singleModule, 'US', 'someTagUUID', 'someSemanticID');
    console.log(serializedSingle);

    // Example with a list of content modules
    const moduleList = [
      {
        /* ... module data ... */
        id: 'mod1',
        allowedCountries: ['us'],
      },
      {
        /* ... module data ... */
        id: 'mod2',
        allowedCountries: ['gb'],
      },
    ];
    const serializedList = await serializeContentModule(moduleList, 'US');
    console.log(serializedList);
    ```

---

### `serializePackageContent`

1.  **Method Name**: `serializePackageContent`
2.  **Description**: Serializes a list of composite modules (representing a package), applying geo-restrictions to each child module and integrating metadata.
3.  **Call Stack**:
    *   **Called By**: (Not identifiable from the provided file)
    *   **Calls**:
        *   `fetchMetadata`: Retrieves video and external article metadata for the given content modules.
        *   `applyGeoRestriction`: Filters content modules based on geographic restrictions.
        *   `doSerialize`: Performs the core serialization logic for a single content module.
        *   `normalizeDateField`: Converts a date-like field to a `Date` object or `null`.
4.  **Example Usage**:

    ```typescript
    import { serializePackageContent } from './path/to/your/file';

    const compositeModules = [
      {
        Module: { id: 'pkg1-mod1', allowedCountries: ['us', 'ca'] },
        isPositionLocked: true,
        positionLockExpiresAt: '2023-12-31T23:59:59Z',
        excludedAt: null,
      },
      {
        Module: { id: 'pkg1-mod2', allowedCountries: ['us'] },
        isPositionLocked: false,
        excludedAt: '2023-10-01T00:00:00Z',
      },
    ];
    const country = 'US';
    const serializedPackage = await serializePackageContent(compositeModules, country);
    console.log(serializedPackage);
    ```

---

### `applyGeoRestriction`

1.  **Method Name**: `applyGeoRestriction`
2.  **Description**: Filters content modules based on whether their `allowedCountries` array includes the specified `countryCode`.
3.  **Call Stack**:
    *   **Called By**:
        *   `serializeContentModule`: Processes content modules for geo-restriction.
        *   `serializePackageContent`: Filters package content modules by country.
    *   **Calls**: (None identifiable from the provided file)
4.  **Example Usage**:

    ```typescript
    import { applyGeoRestriction } from './path/to/your/file';

    const modules = [
      { id: '1', allowedCountries: ['us', 'ca'] },
      { id: '2', allowedCountries: ['gb'] },
      { id: '3', allowedCountries: ['us', 'de'] },
    ];
    const filteredModules = applyGeoRestriction(modules, 'US');
    // Result: [{ id: '1', allowedCountries: ['us', 'ca'] }, { id: '3', allowedCountries: ['us', 'de'] }]

    const singleModule = { id: '4', allowedCountries: ['fr'] };
    const filteredSingle = applyGeoRestriction(singleModule, 'FR');
    // Result: { id: '4', allowedCountries: ['fr'] }

    const nonMatchingSingle = applyGeoRestriction(singleModule, 'US');
    // Result: null
    ```

---

### `doSerialize`

1.  **Method Name**: `doSerialize`
2.  **Description**: Performs the core serialization process for a single content module, extracting common fields and then delegating to specific functions based on whether it's a composite or standalone module.
3.  **Call Stack**:
    *   **Called By**:
        *   `serializeContentModule`: Serializes individual content modules.
        *   `serializePackageContent`: Serializes individual modules within a package.
        *   `addCompositeFields`: Recursively serializes nested content within a composite module.
        *   `serializeComposites`: Processes individual modules within a list of composites.
    *   **Calls**:
        *   `getEnumByValue`: (External) Converts a string value to its corresponding enum member.
        *   `getStateValue`: (External) Retrieves the state value for a content module.
        *   `normalizeDateField`: Converts a date-like field to a `Date` object or `null`.
        *   `serializeComponents`: Serializes associated component objects.
        *   `serializeComposites`: Serializes nested composite modules.
        *   `addCompositeFields`: Adds fields specific to composite content modules.
        *   `addStandaloneFields`: Adds fields specific to standalone content modules.
4.  **Example Usage**:

    ```typescript
    import { doSerialize } from './path/to/your/file';

    const contentModuleData = {
      id: 'mod123',
      contentType: 'VideoV2',
      orientation: 'vertical',
      title: 'Sample Video',
      description: 'A brief description',
      thumbnail: 'url/to/thumbnail.jpg',
      lastModifiedBy: 'user1',
      expiresAt: '2024-01-01T00:00:00Z',
      scheduledDate: '2023-01-01T00:00:00Z',
      state: 'PUBLISHED',
      isAlerted: false,
      allowedCountries: ['us'],
      components: [],
      commentsEnabled: true,
      updatedAt: '2023-09-20T10:00:00Z',
      insertedAt: '2023-09-19T10:00:00Z',
      programmingUpdatedAt: '2023-09-20T10:00:00Z',
      metaData: {},
      contentId: 'vid456',
    };
    const videos = [{ contentId: 'vid456', videoState: 'LIVE' }];
    const articles = [];
    const country = 'US';

    const serializedModule = await doSerialize(contentModuleData, false, videos, articles, country);
    console.log(serializedModule);
    ```

---

### `addCompositeFields`

1.  **Method Name**: `addCompositeFields`
2.  **Description**: Extends a content module with fields specific to composite modules, including nested contents, package type, and position locking details, and reorders contents based on position.
3.  **Call Stack**:
    *   **Called By**:
        *   `doSerialize`: Adds composite-specific data during serialization.
    *   **Calls**:
        *   `normalizeDateField`: Converts a date-like field to a `Date` object or `null`.
        *   `doSerialize`: Recursively serializes nested content modules within the composite.
4.  **Example Usage**:

    ```typescript
    import { addCompositeFields } from './path/to/your/file';

    const commonData = { id: 'comp1', title: 'Composite Package' };
    const compositeModuleData = {
      Composite: {
        packageType: 'playlist',
        contents: [
          {
            Module: { id: 'child1', allowedCountries: ['us'] },
            isPositionLocked: true,
            position: 2,
            lockedPosition: 2,
            positionLockExpiresAt: '2024-01-01T00:00:00Z',
            excludedAt: null,
          },
          {
            Module: { id: 'child2', allowedCountries: ['us'] },
            isPositionLocked: false,
            position: 1,
            lockedPosition: null,
            positionLockExpiresAt: null,
            excludedAt: null,
          },
        ],
      },
      components: [],
    };
    const videos = [];
    const articles = [];
    const country = 'US';

    const compositeFields = await addCompositeFields(
      commonData,
      compositeModuleData,
      videos,
      articles,
      country
    );
    console.log(compositeFields);
    ```

---

### `addStandaloneFields`

1.  **Method Name**: `addStandaloneFields`
2.  **Description**: Extends a content module with fields specific to standalone modules, including the content object, metadata, and position details if it's part of a package or channel.
3.  **Call Stack**:
    *   **Called By**:
        *   `doSerialize`: Adds standalone-specific data during serialization.
    *   **Calls**:
        *   `buildContentObject`: Constructs the content object based on the module's content type.
        *   `buildVideoMetadata`: Formats video-specific metadata.
        *   `normalizeDateField`: Converts a date-like field to a `Date` object or `null`.
4.  **Example Usage**:

    ```typescript
    import { addStandaloneFields } from './path/to/your/file';

    const commonData = { id: 'stand1', title: 'Standalone Item', components: [] };
    const standaloneModuleData = {
      contentId: 'art789',
      contentType: 'Article',
      alertedChannelTagUUID: 'alert-tag-uuid',
      thumbnailAccreditation: 'Source',
      thumbnailCopyright: '© 2023',
      wrapperContentModuleId: null,
      components: [
        {
          tagUUID: 'channel-tag-1',
          semanticID: 'sem-id-1',
          isPositionLocked: true,
          position: 1,
          positionLockExpiresAt: '2024-06-30T00:00:00Z',
        },
      ],
    };
    const packageContentFlag = false;
    const videos = [];
    const articles = [];
    const tagUUID = 'channel-tag-1';
    const semanticID = 'sem-id-1';

    const standaloneFields = await addStandaloneFields(
      commonData,
      standaloneModuleData,
      packageContentFlag,
      videos,
      articles,
      tagUUID,
      semanticID
    );
    console.log(standaloneFields);
    ```

---

### `buildContentObject`

1.  **Method Name**: `buildContentObject`
2.  **Description**: Creates a structured content object based on the `contentType` of a `SerializableContent` module, handling different content types like Article, VideoV2, ExternalArticle, etc.
3.  **Call Stack**:
    *   **Called By**:
        *   `addStandaloneFields`: Integrates the content object into standalone module data.
    *   **Calls**:
        *   `logger.error`: (External) Logs an error if a standalone module is missing `contentId`.
        *   `buildHashForContent`: Generates a base64 hash for certain content types.
        *   `buildExternalArticle`: Constructs the object for `ExternalArticle` types.
4.  **Example Usage**:

    ```typescript
    import { buildContentObject } from './path/to/your/file';

    const articleModule = {
      id: 1,
      contentId: 'art123',
      contentType: 'Article',
      type: 'standalone',
      insertedAt: new Date(),
    };
    const videoModule = {
      id: 2,
      contentId: 'vid456',
      contentType: 'VideoV2',
      type: 'standalone',
      insertedAt: new Date(),
    };
    const externalArticleModule = {
      id: 3,
      contentId: 'https://example.com/article',
      contentType: 'ExternalArticle',
      type: 'standalone',
      insertedAt: new Date(),
    };

    const externalArticlesData = [
      { url: 'https://example.com/article', providerName: 'Example News' },
    ];

    const articleContent = await buildContentObject(articleModule, externalArticlesData);
    console.log(articleContent);
    // Expected: { hash: '...', __typename: 'Article' }

    const videoContent = await buildContentObject(videoModule, externalArticlesData);
    console.log(videoContent);
    // Expected: { hash: '...', __typename: 'VideoV2' }

    const externalContent = await buildContentObject(externalArticleModule, externalArticlesData);
    console.log(externalContent);
    // Expected: { id: '...', url: '...', created: '...', source: '...', providerName: '...', __typename: 'ExternalArticle' }
    ```

---

### `buildHashForContent`

1.  **Method Name**: `buildHashForContent`
2.  **Description**: Generates a base64 encoded string hash from a `SerializableEntity` object.
3.  **Call Stack**:
    *   **Called By**:
        *   `buildContentObject`: Creates hashes for `Article` and `VideoV2` content types.
    *   **Calls**:
        *   `JSON.stringify`: Converts the object to a JSON string.
        *   `Buffer.from`: Creates a buffer from the string.
        *   `toString`: Encodes the buffer to base64.
4.  **Example Usage**:

    ```typescript
    import { buildHashForContent } from './path/to/your/file';

    const entity = { uuid: '123-abc', tenant: 'bleacherReport' };
    const hash = buildHashForContent(entity);
    console.log(hash); // Expected: Base64 string of {"uuid":"123-abc","tenant":"bleacherReport"}
    ```

---

### `buildComponentHash`

1.  **Method Name**: `buildComponentHash`
2.  **Description**: Generates a base64 encoded string hash from a component's content type, content ID, and tag UUID.
3.  **Call Stack**:
    *   **Called By**:
        *   `serializeComponents`: Creates a hash for each serialized component.
    *   **Calls**:
        *   `JSON.stringify`: Converts the object to a JSON string.
        *   `Buffer.from`: Creates a buffer from the string.
        *   `toString`: Encodes the buffer to base64.
4.  **Example Usage**:

    ```typescript
    import { buildComponentHash } from './path/to/your/file';

    const componentData = { contentType: 'VideoV2', contentID: 'vid123', tagUUID: 'tag-abc' };
    const hash = buildComponentHash(componentData);
    console.log(hash); // Expected: Base64 string of {"contentType":"VideoV2","contentID":"vid123","tagUUID":"tag-abc"}
    ```

---

### `serializeComponents`

1.  **Method Name**: `serializeComponents`
2.  **Description**: Transforms a list of raw component objects into a standardized, serialized format.
3.  **Call Stack**:
    *   **Called By**:
        *   `doSerialize`: Processes and serializes components associated with a content module.
    *   **Calls**:
        *   `buildComponentHash`: Generates a unique hash for each component.
        *   `normalizeDateField`: Converts date-like fields to `Date` objects or `null`.
4.  **Example Usage**:

    ```typescript
    import { serializeComponents } from './path/to/your/file';

    const rawComponents = [
      {
        Component: { id: 'compA', semanticID: 'semA', tagUUID: 'uuidA' },
        position: 1,
        isPositionLocked: true,
        moduleId: 'mod123',
        positionLockExpiresAt: '2024-12-31T00:00:00Z',
        insertedAt: '2023-01-01T00:00:00Z',
        lockedPosition: 1,
      },
      {
        Component: { id: 'compB', semanticID: 'semB', tagUUID: 'uuidB' },
        position: 2,
        isPositionLocked: false,
        moduleId: 'mod123',
        positionLockExpiresAt: null,
        insertedAt: '2023-01-02T00:00:00Z',
        lockedPosition: null,
      },
    ];
    const contentID = 'contentABC';
    const contentType = 'Article';
    const contentModuleId = 'mod123';

    const serialized = serializeComponents(rawComponents as any, contentID, contentType, contentModuleId);
    console.log(serialized);
    ```

---

### `serializeComposites`

1.  **Method Name**: `serializeComposites`
2.  **Description**: Iterates through a list of composite modules, recursively calling `doSerialize` for each to transform them into a standardized format.
3.  **Call Stack**:
    *   **Called By**:
        *   `doSerialize`: Processes and serializes nested composite modules.
    *   **Calls**:
        *   `logger.warn`: (External) Logs a warning if a composite is missing its `Module`.
        *   `doSerialize`: Recursively serializes the individual module within the composite.
4.  **Example Usage**:

    ```typescript
    import { serializeComposites } from './path/to/your/file';

    const rawComposites = [
      {
        Module: {
          id: 'compositeMod1',
          contentType: 'Package',
          title: 'Nested Package',
          /* ... other module fields ... */
        },
        id: 'compLink1',
      },
      {
        Module: {
          id: 'compositeMod2',
          contentType: 'VideoV2',
          title: 'Nested Video',
          /* ... other module fields ... */
        },
        id: 'compLink2',
      },
    ];
    const videos = [];
    const articles = [];
    const country = 'US';

    const serialized = await serializeComposites(rawComposites, videos, articles, country);
    console.log(serialized);
    ```

---

### `getDomain`

1.  **Method Name**: `getDomain`
2.  **Description**: Extracts the hostname (domain) from a given URL string.
3.  **Call Stack**:
    *   **Called By**:
        *   `buildExternalArticle`: Retrieves the source domain for an external article.
    *   **Calls**:
        *   `URL`: (Native JS `URL` constructor) Parses the URL string.
4.  **Example Usage**:

    ```typescript
    import { getDomain } from './path/to/your/file';

    const url1 = 'https://www.example.com/path?query=1';
    const domain1 = getDomain(url1);
    console.log(domain1); // Expected: "www.example.com"

    const url2 = 'http://blog.sub.domain.co.uk';
    const domain2 = getDomain(url2);
    console.log(domain2); // Expected: "blog.sub.domain.co.uk"
    ```

---

### `buildExternalArticle`

1.  **Method Name**: `buildExternalArticle`
2.  **Description**: Constructs an object representing an external article, including its URL (with UTM parameters), creation date, source domain, and provider name.
3.  **Call Stack**:
    *   **Called By**:
        *   `buildContentObject`: Creates the content object for `ExternalArticle` types.
    *   **Calls**:
        *   `URLWrapper.updateUTMSearchParams`: (External) Updates UTM parameters in the URL.
        *   `normalizeDateField`: Converts a date-like field to a `Date` object.
        *   `getDomain`: Extracts the domain from the article URL.
4.  **Example Usage**:

    ```typescript
    import { buildExternalArticle } from './path/to/your/file';

    const articleData = {
      url: 'https://example.com/article?campaign=test',
      providerName: 'Example News',
    };
    const insertionDate = new Date('2023-09-20T10:00:00Z');

    const externalArticle = buildExternalArticle(articleData, insertionDate);
    console.log(externalArticle);
    // Expected:
    // {
    //   id: 'https://example.com/article?campaign=test&utm_source=bleacherreport&utm_medium=app',
    //   url: 'https://example.com/article?campaign=test&utm_source=bleacherreport&utm_medium=app',
    //   created: <Date object for insertionDate>,
    //   source: 'example.com',
    //   providerName: 'Example News',
    //   __typename: 'ExternalArticle'
    // }

    const noProviderArticle = { url: 'https://another.com/story' };
    try {
      buildExternalArticle(noProviderArticle, new Date());
    } catch (e: any) {
      console.error(e.message); // Expected: "ExternalArticle with URL https://another.com/story has no providerName."
    }
    ```

---

### `fetchMetadata`

1.  **Method Name**: `fetchMetadata`
2.  **Description**: Collects all `contentId`s for videos and `contentId`s (URLs) for external articles from a given content module (or list), then calls `doFetchMetadata` to retrieve their corresponding metadata from the database.
3.  **Call Stack**:
    *   **Called By**:
        *   `serializeContentModule`: Fetches metadata for modules being serialized.
        *   `serializePackageContent`: Fetches metadata for modules within a package being serialized.
    *   **Calls**:
        *   `extractVideosOrUrls`: Extracts video IDs and article URLs from content modules.
        *   `doFetchMetadata`: Queries the database for the extracted metadata.
4.  **Example Usage**:

    ```typescript
    import { fetchMetadata } from './path/to/your/file';

    const modules = [
      { contentType: 'VideoV2', contentId: 'vid1' },
      {
        contentType: 'Composite',
        Composite: {
          contents: [{ Module: { contentType: 'ExternalArticle', contentId: 'http://a.com' } }],
        },
      },
      { contentType: 'VideoV2', contentId: 'vid2' },
    ];

    const metadata = await fetchMetadata(modules);
    console.log(metadata);
    // Expected: { videosMetadata: [...], externalArticles: [...] }
    ```

---

### `extractVideosOrUrls`

1.  **Method Name**: `extractVideosOrUrls`
2.  **Description**: Processes a single content module (or its nested contents if it's a composite) to identify and extract `contentId`s of `VideoV2` types and `contentId`s (URLs) of `ExternalArticle` types.
3.  **Call Stack**:
    *   **Called By**:
        *   `fetchMetadata`: Gathers IDs and URLs for metadata lookup.
    *   **Calls**: (None identifiable from the provided file)
4.  **Example Usage**:

    ```typescript
    import { extractVideosOrUrls } from './path/to/your/file';

    const standaloneVideo = { contentType: 'VideoV2', contentId: 'video-abc' };
    const result1 = extractVideosOrUrls(standaloneVideo);
    console.log(result1); // Expected: { videoIds: ['video-abc'], articleUrls: [] }

    const compositeWithArticle = {
      Composite: {
        contents: [
          { Module: { contentType: 'Article', contentId: 'art-xyz' } }, // Ignored, not VideoV2 or ExternalArticle
          { Module: { contentType: 'ExternalArticle', contentId: 'https://example.com/e1' } },
        ],
      },
    };
    const result2 = extractVideosOrUrls(compositeWithArticle);
    console.log(result2); // Expected: { videoIds: [], articleUrls: ['https://example.com/e1'] }
    ```

---

### `doFetchMetadata`

1.  **Method Name**: `doFetchMetadata`
2.  **Description**: Fetches `VideoMetadata` and `ExternalArticle` records from the replica database using Prisma, based on provided lists of video IDs and article URLs.
3.  **Call Stack**:
    *   **Called By**:
        *   `fetchMetadata`: Performs the actual database query for metadata.
    *   **Calls**:
        *   `PrismaConn.getInstance`: (External) Gets a singleton instance of the Prisma client.
        *   `replicaConn.getConn`: (External) Retrieves the Prisma client connection.
        *   `prismaReplica.videoMetadata.findMany`: (Prisma query) Finds multiple video metadata records.
        *   `prismaReplica.externalArticle.findMany`: (Prisma query) Finds multiple external article records.
4.  **Example Usage**:

    ```typescript
    import { doFetchMetadata } from './path/to/your/file';

    // Assume PrismaConn is mocked or set up to return a Prisma client
    const videoIdsToFetch = ['vid_001', 'vid_002'];
    const articleUrlsToFetch = ['http://article.com/1', 'http://article.com/2'];

    const metadataResult = await doFetchMetadata({
      videoIds: videoIdsToFetch,
      articlesUrls: articleUrlsToFetch,
    });
    console.log(metadataResult);
    // Expected: { videosMetadata: [{ contentId: 'vid_001', videoState: 'LIVE' }], externalArticles: [{ url: 'http://article.com/1', providerName: 'Source' }] }
    ```

---

### `buildVideoMetadata`

1.  **Method Name**: `buildVideoMetadata`
2.  **Description**: Constructs a `VideoV2Metadata` object from an optional `VideoMetadata` object, primarily mapping `videoState` to the `VideoState` enum.
3.  **Call Stack**:
    *   **Called By**:
        *   `addStandaloneFields`: Integrates video metadata into standalone module data.
    *   **Calls**:
        *   `getEnumByValue`: (External) Converts a string value to its corresponding enum member.
4.  **Example Usage**:

    ```typescript
    import { buildVideoMetadata } from './path/to/your/file';
    import { VideoState } from '../../graphql/generated/graphql'; // Assuming correct import path for enum

    const rawMetadata = { contentId: 'v1', videoState: 'LIVE' };
    const formattedMetadata = buildVideoMetadata(rawMetadata);
    console.log(formattedMetadata); // Expected: { state: VideoState.LIVE }

    const noMetadata = buildVideoMetadata(undefined);
    console.log(noMetadata); // Expected: { state: null }
    ```

---

### `normalizeDateField`

1.  **Method Name**: `normalizeDateField`
2.  **Description**: Converts a date field (which might be a string, `Date` object, or `null`) into a `Date` object or returns `null` if the input is `null`.
3.  **Call Stack**:
    *   **Called By**:
        *   `serializePackageContent`: Normalizes `positionLockExpiresAt` and `excludedAt`.
        *   `doSerialize`: Normalizes several date fields (expiresAt, scheduledDate, updatedAt, etc.).
        *   `addCompositeFields`: Normalizes `positionLockExpiresAt` and `excludedAt` for composite contents.
        *   `addStandaloneFields`: Normalizes `positionLockExpiresAt`.
        *   `serializeComponents`: Normalizes `positionLockExpiresAt` and `insertedAt`.
        *   `buildExternalArticle`: Normalizes the `insertedAt` date for external articles.
    *   **Calls**:
        *   `Date.parse`: (Native JS `Date` method) Parses a string representation of a date.
4.  **Example Usage**:

    ```typescript
    import { normalizeDateField } from './path/to/your/file';

    const dateString = '2023-09-20T10:30:00Z';
    const dateObject = new Date('2023-01-01T00:00:00Z');
    const nullValue = null;

    const normalizedString = normalizeDateField(dateString);
    console.log(normalizedString); // Expected: Date object for '2023-09-20T10:30:00Z'

    const normalizedObject = normalizeDateField(dateObject);
    console.log(normalizedObject); // Expected: Original Date object for '2023-01-01T00:00:00Z'

    const normalizedNull = normalizeDateField(nullValue);
    console.log(normalizedNull); // Expected: null
    ```