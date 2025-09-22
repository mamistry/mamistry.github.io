Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### **Method Name**: `fetchContentModuleByIdThroughCache`

*   **Description**: Fetches a content module by its ID, first attempting to retrieve it from cache, and falling back to a database query if not found.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `CacheHelper.readCache`: Reads data from the cache.
        *   `fetchContentModuleById`: Fetches a content module directly from the database.
        *   `CacheHelper.writeCache`: Writes data to the cache.
        *   `serializeContentModule`: Transforms a content module model into a serializable format.
*   **Example Usage**:
    ```typescript
    const contentModule = await fetchContentModuleByIdThroughCache('some-module-id', 'US');
    if (contentModule) {
      console.log('Fetched module ID:', contentModule.id);
    }
    ```

---

### **Method Name**: `fetchContentModuleById`

*   **Description**: Retrieves a single content module from the database by its ID, with optional Prisma connection and default filters.
*   **Call Stack**:
    *   **Called By**:
        *   `fetchContentModuleByIdThroughCache`: Attempts to fetch a content module by ID, falling back to this function if not in cache.
    *   **Calls**:
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `prismaClient.module.findFirst`: Prisma ORM method to find the first record matching the criteria.
        *   `generateContentModuleIncludes`: Generates Prisma `include` options for content module queries.
        *   `contentModuleDTOService.mapDBResultWithIncludeToModel`: Maps a database result object to a content module model.
*   **Example Usage**:
    ```typescript
    const moduleById = await fetchContentModuleById('module-abc-123');
    if (moduleById) {
      console.log(`Found module with ID: ${moduleById.id}`);
    }
    ```

---

### **Method Name**: `fetchPackageByTypeAndTag`

*   **Description**: Fetches content modules of type 'Package' that match a given package type and tag(s).
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `prismaClient.module.findMany`: Prisma ORM method to find multiple records matching the criteria.
        *   `generateContentModuleIncludes`: Generates Prisma `include` options for content module queries.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel`: Maps a list of database results to a list of content module models.
*   **Example Usage**:
    ```typescript
    const packages = await fetchPackageByTypeAndTag('MoviePackage', 'action-tag');
    console.log(`Found ${packages.length} packages.`);
    ```

---

### **Method Name**: `fetchContentModulesByContentIdAndContentType`

*   **Description**: Fetches content modules associated with a specific content ID and content type, with optional state filtering.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `ContentModuleRepository.fetchContentModulesByContentIdAndContentType`: Retrieves content modules from the repository.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel`: Maps a list of database results to a list of content module models.
*   **Example Usage**:
    ```typescript
    const modules = await fetchContentModulesByContentIdAndContentType('content-123', 'Movie', ['ACTIVE']);
    console.log(`Found ${modules.length} modules for content ID.`);
    ```

---

### **Method Name**: `findContentModulesBySemanticIdAndTag`

*   **Description**: Finds content modules by a given semantic ID and tag(s), with optional caching, content type filtering, and lexical position reordering.
*   **Call Stack**:
    *   **Called By**:
        *   `fetchRecentContentFromCreators`: Fetches recent content by creators using semantic ID and tags.
    *   **Calls**:
        *   `CacheHelper.readCache`: Reads data from the cache.
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `prismaClient.componentModule.findMany`: Prisma ORM method to find multiple `ComponentModule` records.
        *   `LexicalPositionService.locksReordering`: Reorders modules based on their locked positions.
        *   `prismaClient.module.findMany`: Prisma ORM method to find multiple `Module` records.
        *   `generateContentModuleIncludes`: Generates Prisma `include` options for content module queries.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel`: Maps a list of database results to a list of content module models.
        *   `CacheHelper.writeCache`: Writes data to the cache.
*   **Example Usage**:
    ```typescript
    const args = {
      semanticID: 'EPISODE',
      tagUUID: ['comedy', 'drama'],
      statesFilter: { isActive: true },
      limit: 10,
    };
    const modules = await findContentModulesBySemanticIdAndTag(args);
    console.log(`Found ${modules.length} modules.`);
    ```

---

### **Method Name**: `findPackageByTitle`

*   **Description**: Searches for content packages by a given title and an array of states.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `ContentModuleRepository.fetchPackagesByTitle`: Fetches packages from the repository by title.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel`: Maps a list of database results to a list of content module models.
*   **Example Usage**:
    ```typescript
    const packages = await findPackageByTitle('The Big Show', ['ACTIVE']);
    console.log(`Found ${packages.length} packages with title "The Big Show".`);
    ```

---

### **Method Name**: `fetchScheduledModules`

*   **Description**: Fetches scheduled content modules within an optional date range, ordered by schedule date.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `prismaClient.module.findMany`: Prisma ORM method to find multiple `Module` records.
        *   `generateContentModuleIncludes`: Generates Prisma `include` options for content module queries.
*   **Example Usage**:
    ```typescript
    const scheduled = await fetchScheduledModules({ startDate: '2023-01-01', endDate: '2023-12-31', limit: 5 });
    console.log(`Found ${scheduled.length} scheduled modules.`);
    ```

---

### **Method Name**: `fetchRecentContentFromCreators`

*   **Description**: Retrieves a limited number of recent content modules created by specific users based on their tags, semantic ID, and an optional time frame.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `findContentModulesBySemanticIdAndTag`: Finds content modules by semantic ID and tags, which handles the core logic.
*   **Example Usage**:
    ```typescript
    const recentContent = await fetchRecentContentFromCreators(
      ['user-tag-a', 'user-tag-b'],
      { isActive: true },
      SemanticID.CHANNEL, // Assuming SemanticID is an enum
      new Date(Date.now() - 7 * 24 * 60 * 60 * 1000) // last 7 days
    );
    console.log(`Found ${recentContent.length} recent modules from creators.`);
    ```

---

### **Method Name**: `fetchContentModulesBySemanticIdNotInTagsWithTime`

*   **Description**: Fetches content modules of type 'standalone' by semantic ID, excluding specific tags, filtered by insertion time and additional states.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `prismaClient.module.findMany`: Prisma ORM method to find multiple `Module` records.
*   **Example Usage**:
    ```typescript
    const modules = await fetchContentModulesBySemanticIdNotInTagsWithTime(
      SemanticID.CATEGORY,
      new Date('2023-01-01T00:00:00Z'),
      { status: 'PUBLISHED' },
      ['exclusive', 'premium'],
      'ARTICLE'
    );
    console.log(`Found ${modules.length} modules.`);
    ```

---

### **Method Name**: `fetchContentModuleByIds`

*   **Description**: Fetches multiple content modules given an array of their IDs.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `prismaClient.module.findMany`: Prisma ORM method to find multiple `Module` records.
        *   `generateContentModuleIncludes`: Generates Prisma `include` options for content module queries.
*   **Example Usage**:
    ```typescript
    const modules = await fetchContentModuleByIds(['id-1', 'id-2', 'id-3']);
    console.log(`Fetched ${modules.length} modules by IDs.`);
    ```

---

### **Method Name**: `buildExcludedAtFilter`

*   **Description**: Constructs a filter object for the `excludedAt` field of a `CompositeModule` based on an array of `ContentStatus` values.
*   **Call Stack**:
    *   **Called By**:
        *   `fetchPackageContents`: Fetches the contents of a package, using this to build exclusion filters.
    *   **Calls**: (None identifiable from this file)
*   **Example Usage**:
    ```typescript
    const now = new Date();
    const activeFilter = buildExcludedAtFilter([ContentStatus.ACTIVE], now);
    // console.log(activeFilter); // e.g., { OR: [{ excludedAt: null }, { excludedAt: { gt: now } }] }

    const deletedFilter = buildExcludedAtFilter([ContentStatus.DELETED], now);
    // console.log(deletedFilter); // e.g., { excludedAt: { not: null, lte: now } }
    ```

---

### **Method Name**: `fetchPackageContents`

*   **Description**: Fetches the composite modules that are part of a specific package content module, applying filters for content status and schedule dates.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `buildExcludedAtFilter`: Constructs the filter for the `excludedAt` field.
        *   `prismaClient.compositeModule.findMany`: Prisma ORM method to find multiple `CompositeModule` records.
        *   `contentModuleDTOService.mapDBCompositeModuleResultListToModel`: Maps a list of database `CompositeModule` results to a list of content module models.
*   **Example Usage**:
    ```typescript
    const packageContents = await fetchPackageContents('package-module-id-456', [ContentStatus.ACTIVE]);
    console.log(`Package contains ${packageContents.length} modules.`);
    ```

---

### **Method Name**: `getContentModulesByWrapperIds`

*   **Description**: Retrieves content modules associated with a list of wrapper content module IDs, utilizing caching to optimize performance.
*   **Call Stack**:
    *   **Called By**: (Not identifiable from this file)
    *   **Calls**:
        *   `CacheHelper.readCache`: Reads data from the cache.
        *   `replicaConn.getConn`: Gets the Prisma connection instance.
        *   `prismaClient.module.findMany`: Prisma ORM method to find multiple `Module` records.
        *   `generateContentModuleIncludes`: Generates Prisma `include` options for content module queries.
        *   `contentModuleDTOService.mapDBFindManyResultListWithIncludeToModel`: Maps a list of database results to a list of content module models.
        *   `CacheHelper.writeCache`: Writes data to the cache.
*   **Example Usage**:
    ```typescript
    const wrapperModules = await getContentModulesByWrapperIds(['wrapper-id-1', 'wrapper-id-2']);
    console.log(`Found ${wrapperModules.length} modules for wrapper IDs.`);
    ```