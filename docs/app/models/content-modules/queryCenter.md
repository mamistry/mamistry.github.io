This document provides comprehensive documentation for the TypeScript functions and methods found within the provided file.

---

### `buildQueryForSpecificSemanticID`

1.  **Method Name**: `buildQueryForSpecificSemanticID`
2.  **Description**: This function attempts to build a specific content query based on a given semantic ID, delegating to predefined query functions if a match is found in the `SPECIFIC_SEMANTIC_IDS` map.
3.  **Call Stack**:
    *   **Called by**: (None identifiable in this file)
    *   **Calls**:
        *   `ugcFromTheFansQuery`: Constructs a Prisma query input specifically for "UGC From The Fans" content. (Called indirectly via `SPECIFIC_SEMANTIC_IDS` map).
4.  **Example Usage**:

    ```typescript
    import { ChannelContentsProps, ContentFilterType } from '../types';

    const args: ChannelContentsProps = { semanticID: 'UgcFromTheFans', tagUUID: 'some-tag-uuid' };
    const filters: ContentFilterType = { state: 'PUBLISHED' };
    const xFedapiAuth = 'your-x-fedapi-auth-token';
    const authorizationHeader = 'Bearer your-auth-token';

    async function example() {
      const query = await buildQueryForSpecificSemanticID(
        args,
        filters,
        xFedapiAuth,
        authorizationHeader
      );

      if (query) {
        console.log('Generated Specific Query:', JSON.stringify(query, null, 2));
      } else {
        console.log('No specific query function found for semantic ID:', args.semanticID);
      }
    }
    example();
    ```

---

### `ugcFromTheFansQuery`

1.  **Method Name**: `ugcFromTheFansQuery`
2.  **Description**: This function constructs a Prisma query input specifically for "UGC From The Fans" content, incorporating a tag UUID and content state filters.
3.  **Call Stack**:
    *   **Called by**:
        *   `buildQueryForSpecificSemanticID`: Attempts to build a specific content query based on semantic ID.
    *   **Calls**:
        *   `composeStatusQuery`: (External function) Composes a query object for content states.
4.  **Example Usage**:

    ```typescript
    import { ChannelContentsProps, ContentFilterType } from '../types';

    const args: ChannelContentsProps = { tagUUID: 'fans-tag-123' };
    const filters: ContentFilterType = { state: 'ACTIVE' };

    const queryInput = ugcFromTheFansQuery(args, filters);
    console.log('UGC From The Fans Query Input:', JSON.stringify(queryInput, null, 2));
    /*
    Expected output structure:
    {
      "Component": { "tagUUID": "fans-tag-123" },
      "Module": {
        "Composite": { "packageType": "FROM_THE_FANS" },
        // ... stateFilter output from composeStatusQuery
      }
    }
    */
    ```

---

### `findContentModulesBySemanticIdAndTagQuery`

1.  **Method Name**: `findContentModulesBySemanticIdAndTagQuery`
2.  **Description**: This function generates a Prisma query input to find content modules based on a specific semantic ID, tag UUID, and content state filters.
3.  **Call Stack**:
    *   **Called by**: (None identifiable in this file)
    *   **Calls**:
        *   `composeStatusQuery`: (External function) Composes a query object for content states.
4.  **Example Usage**:

    ```typescript
    import { ChannelContentsProps, ContentFilterType } from '../types';

    const args: ChannelContentsProps = { tagUUID: 'sports-tag-abc', semanticID: 'LatestNews' };
    const filters: ContentFilterType = { state: 'DRAFT' };

    const queryInput = findContentModulesBySemanticIdAndTagQuery(args, filters);
    console.log('Content Modules Query Input:', JSON.stringify(queryInput, null, 2));
    /*
    Expected output structure:
    {
      "Component": { "tagUUID": "sports-tag-abc", "semanticID": "LatestNews" },
      "Module": {
        // ... stateFilter output from composeStatusQuery
      }
    }
    */
    ```