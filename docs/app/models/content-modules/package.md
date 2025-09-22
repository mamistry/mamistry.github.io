Here is the comprehensive documentation for the methods and functions found in the provided file:

---

### `isStaticPackageType`

1.  **Method Name**: `isStaticPackageType`
2.  **Description**: Checks if a given package type string corresponds to one of the predefined static package types.
3.  **Call Stack**:
    *   **Called by**:
        *   `updatePackage`: Checks if the package being updated is a static type.
    *   **Calls**:
        *   `Object.values`: (External) Returns an array of a given object's own enumerable string-keyed property values.
        *   `Array.includes`: (External) Determines whether an array includes a certain value among its entries.
4.  **Example Usage**:
    ```typescript
    const isCreatorShows = isStaticPackageType("CreatorShows"); // true
    const isRegularPackage = isStaticPackageType("RegularPackage"); // false
    ```

---

### `createPackage`

1.  **Method Name**: `createPackage`
2.  **Description**: Creates a new package content module, associating it with specified channels and standalone content modules.
3.  **Call Stack**:
    *   **Called by**:
        *   `upsertPackageContentModules`: Creates a package if no `contentModuleId` is provided.
    *   **Calls**:
        *   `tracer.trace`: (External)
        *   `prismaConn.getConn()`: (External)
        *   `preparePackage`: Prepares allowed countries, component modules, and contents.
        *   `validateAndPrepareStandalones`: Validates and prepares standalone contents for upsert.
        *   `prepareStandaloneOperations`: Converts prepared standalones into database operations.
        *   `internalUpsertStandlone`: Executes create/update operations for standalone modules.
        *   `prismaClient.module.create()`: (External)
        *   `upsertPackageStandaloneAssoc`: Associates standalone modules with the package.
        *   `removeSurplusFromStaticPackages`: Removes excess content from static packages.
        *   `markPackageToNormalize`: Marks the package for position normalization.
        *   `fetchContentModuleById`: (External)
        *   `AsyncHelper.fireAndForget`: (External)
        *   `CacheHelper.expireCache`: (External)
        *   `markComponentToNormalize`: (External)
        *   `updateChannelPinnedModule`: (External)
        *   `isAutoProgrammed`: (External)
4.  **Example Usage**:
    ```typescript
    import { PackageType } from '../types';

    const newPackageArgs = {
      title: "Weekly Highlights",
      packageType: PackageType.TrendingVideos,
      lastModifiedBy: "admin",
      channels: [{ semanticID: "sports-channel", tagUUID: "abc-123" }],
      // ... other package properties
    };
    const packageContents = [
      { contentId: "video-1", contentType: "Video", position: 1 },
      { contentId: "video-2", contentType: "Video", position: 2 },
    ];
    const createdPackage = await createPackage(newPackageArgs, packageContents);
    ```

---

### `updatePackage`

1.  **Method Name**: `updatePackage`
2.  **Description**: Updates an existing package content module, including its associated channels, properties, and standalone content modules.
3.  **Call Stack**:
    *   **Called by**:
        *   `upsertPackageContentModules`: Updates a package if a `contentModuleId` is provided.
    *   **Calls**:
        *   `tracer.trace`: (External)
        *   `prismaConn.getConn()`: (External)
        *   `fetchContentModuleById`: (External)
        *   `ContentModuleValidationError`: (External)
        *   `isStaticPackageType`: Checks if the package being updated is a static type.
        *   `preparePackage`: Prepares allowed countries, component modules, and contents.
        *   `validateAndPrepareStandalones`: Validates and prepares standalone contents for upsert.
        *   `prepareStandaloneOperations`: Converts prepared standalones into database operations.
        *   `internalUpsertStandlone`: Executes create/update operations for standalone modules.
        *   `prismaClient.module.update()`: (External)
        *   `upsertPackageStandaloneAssoc`: Associates standalone modules with the updated package.
        *   `removeSurplusFromStaticPackages`: Removes excess content from static packages.
        *   `markPackageToNormalize`: Marks the package for position normalization.
        *   `AsyncHelper.fireAndForget`: (External)
        *   `CacheHelper.expireCache`: (External)
        *   `markComponentToNormalize`: (External)
        *   `updateChannelPinnedModule`: (External)
        *   `isAutoProgrammed`: (External)
4.  **Example Usage**:
    ```typescript
    import { PackageType } from '../types';

    const updatePackageArgs = {
      contentModuleId: "existing-pkg-id",
      title: "Updated Weekly Highlights",
      packageType: PackageType.TrendingVideos,
      lastModifiedBy: "editor",
      channels: [{ semanticID: "sports-channel", tagUUID: "abc-123" }],
      // ... other updated package properties
    };
    const updatedContents = [
      { contentId: "video-1", contentType: "Video", position: 1 },
      { contentId: "video-3", contentType: "Video", position: 3 }, // Added new content
    ];
    const updatedPackage = await updatePackage(updatePackageArgs, updatedContents);
    ```

---

### `addContentToPackage`

1.  **Method Name**: `addContentToPackage`
2.  **Description**: Adds a single standalone content module to an existing package.
3.  **Call Stack**:
    *   **Called by**: None identified in this file.
    *   **Calls**:
        *   `tracer.trace`: (External)
        *   `prismaConn.getConn()`: (External)
        *   `validateContents`: Validates the content arguments.
        *   `validateAndPreparePackage`: Validates the target package and content type.
        *   `validateAndPrepareStandalones`: Validates and prepares the standalone content for upsert.
        *   `prepareStandaloneOperations`: Converts prepared standalone into a database operation.
        *   `internalUpsertStandlone`: Executes the create/update operation for the standalone module.
        *   `upsertPackageStandaloneAssoc`: Associates the standalone module with the package.
        *   `removeSurplusFromStaticPackages`: Removes excess content from static packages.
        *   `AsyncHelper.fireAndForget`: (External)
        *   `CacheHelper.expireCache`: (External)
        *   `markPackageToNormalize`: Marks the package for position normalization.
        *   `fetchContentModuleById`: (External)
4.  **Example Usage**:
    ```typescript
    const addArgs = {
      packageModuleId: "existing-pkg-id",
      contentId: "new-article-id",
      contentType: "Article",
      title: "New Article",
      lastModifiedBy: "author",
      position: 5,
    };
    const result = await addContentToPackage(addArgs);
    ```

---

### `deleteContentFromPackageControl`

1.  **Method Name**: `deleteContentFromPackageControl`
2.  **Description**: Deletes a specific standalone content module from an existing package.
3.  **Call Stack**:
    *   **Called by**: None identified in this file.
    *   **Calls**:
        *   `tracer.trace`: (External)
        *   `fetchContentModuleById`: (External)
        *   `ContentModuleValidationError`: (External)
        *   `prismaConn.getConn()`: (External)
        *   `prismaClient.compositeModule.delete()`: (External)
        *   `AsyncHelper.fireAndForget`: (External)
        *   `CacheHelper.expireCache`: (External)
4.  **Example Usage**:
    ```typescript
    const deleteArgs = {
      packageContentModuleId: "existing-pkg-id",
      contentModuleId: "standalone-to-remove-id",
    };
    const success = await deleteContentFromPackageControl(deleteArgs); // true
    ```

---

### `upsertPackageContentModules`

1.  **Method Name**: `upsertPackageContentModules`
2.  **Description**: Handles the creation, updating, and deletion of multiple package content modules and their associated standalone contents in a single operation.
3.  **Call Stack**:
    *   **Called by**: None identified in this file.
    *   **Calls**:
        *   `validateContents`: Validates the content arguments for packages.
        *   `updatePackage`: Updates an existing package.
        *   `createPackage`: Creates a new package.
        *   `deleteContentModule`: (External)
        *   `Promise.all`: (External)
4.  **Example Usage**:
    ```typescript
    const upsertMutationArgs = {
      packages: [
        { title: "New Package 1", lastModifiedBy: "user", channels: [{ semanticID: "ch1", tagUUID: "t1" }] },
        { contentModuleId: "pkg-id-2", title: "Updated Package 2", lastModifiedBy: "user", channels: [{ semanticID: "ch2", tagUUID: "t2" }] },
      ],
      contents: [
        { contentId: "art-1", contentType: "Article", position: 1 },
      ],
      delete: ["pkg-id-3-to-delete"],
    };
    const results = await upsertPackageContentModules(upsertMutationArgs);
    ```

---

### `createStaticPackages`

1.  **Method Name**: `createStaticPackages`
2.  **Description**: Creates multiple static package content modules based on specified channels and package types.
3.  **Call Stack**:
    *   **Called by**: None identified in this file.
    *   **Calls**:
        *   `prismaConn.getConn()`: (External)
        *   `Promise.all`: (External)
        *   `findOrCreateComponent`: (External)
        *   `processAllowedCountries`: (External)
        *   `allCountriesCodes`: (External)
        *   `ContentModuleValidationError`: (External)
        *   `buildComponentModule`: (External)
        *   `prismaClient.module.create()`: (External)
        *   `generateContentModuleIncludes`: (External)
        *   `isAutoProgrammed`: (External)
4.  **Example Usage**:
    ```typescript
    import { PackageType } from '../types';

    const staticPackagesArgs = {
      channels: [{ semanticID: "news-channel", tagUUID: "news-uuid" }],
      packageTypes: [PackageType.TopHeadlines, PackageType.WhatsBuzzing],
      lastModifiedBy: "system",
      contentType: "Article",
      description: "Auto-generated news packages",
    };
    const createdStaticPackages = await createStaticPackages(staticPackagesArgs);
    ```

---

### `normalizePackageContentsPositions`

1.  **Method Name**: `normalizePackageContentsPositions`
2.  **Description**: Normalizes the positions of standalone content modules within packages that have been marked for position recalculation.
3.  **Call Stack**:
    *   **Called by**: None identified in this file.
    *   **Calls**:
        *   `tracer.trace`: (External)
        *   `prismaConn.getConn()`: (External)
        *   `replicaConn.getConn()`: (External)
        *   `replicaClient.compositeNormalization.findMany()`: (External)
        *   `LexicalPositionService.positionStep`: (External)
        *   `Prisma.sql`: (External)
        *   `Prisma.join`: (External)
        *   `prismaClient.$executeRaw`: (External)
        *   `prismaClient.compositeNormalization.deleteMany()`: (External)
4.  **Example Usage**:
    ```typescript
    await normalizePackageContentsPositions();
    ```

---

### `markPackageToNormalize`

1.  **Method Name**: `markPackageToNormalize`
2.  **Description**: Marks a specific package's contents for position normalization by creating or updating an entry in the `compositeNormalization` table.
3.  **Call Stack**:
    *   **Called by**:
        *   `createPackage`: Marks a newly created package for normalization.
        *   `updatePackage`: Marks an updated package for normalization.
        *   `addContentToPackage`: Marks a package with new content for normalization.
    *   **Calls**:
        *   `prismaConn.getConn()`: (External)
        *   `prismaClient.compositeNormalization.upsert()`: (External)
4.  **Example Usage**:
    ```typescript
    await markPackageToNormalize(500); // Mark composite with ID 500 for normalization
    ```

---

### `preparePackage`

1.  **Method Name**: `preparePackage`
2.  **Description**: Prepares package-related data, including component modules and allowed countries, for use during package creation or update.
3.  **Call Stack**:
    *   **Called by**:
        *   `createPackage`: Prepares data for creating a new package.
        *   `updatePackage`: Prepares data for updating an existing package.
    *   **Calls**:
        *   `Promise.all`: (External)
        *   `findOrCreateComponent`: (External)
        *   `ContentModuleValidationError`: (External)
        *   `processAllowedCountries`: (External)
        *   `buildComponentModule`: (External)
4.  **Example Usage**:
    ```typescript
    const args = {
      channels: [{ semanticID: "test-channel", tagUUID: "xyz-789" }],
      allowedCountries: ["US", "CA"],
    };
    const { allowedCountries, componentModules, contents } = await preparePackage(args);
    ```

---

### `validateAndPreparePackage`

1.  **Method Name**: `validateAndPreparePackage`
2.  **Description**: Validates that a target package exists, is of type 'package', and determines the content type for new additions to ensure consistency.
3.  **Call Stack**:
    *   **Called by**:
        *   `addContentToPackage`: Validates the package before adding content.
    *   **Calls**:
        *   `fetchContentModuleById`: (External)
        *   `ContentModuleValidationError`: (External)
4.  **Example Usage**:
    ```typescript
    const args = {
      packageModuleId: "pkg-id-123",
      contentId: "art-456",
      contentType: "Article",
    };
    const { updatingPackage, contentType } = await validateAndPreparePackage(args);
    ```

---

### `validateAndPrepareStandalones`

1.  **Method Name**: `validateAndPrepareStandalones`
2.  **Description**: Validates a list of standalone content arguments, checks for duplicates, and assigns lexical positions before database operations.
3.  **Call Stack**:
    *   **Called by**:
        *   `createPackage`: Prepares standalones for a new package.
        *   `updatePackage`: Prepares standalones for an updated package.
        *   `addContentToPackage`: Prepares standalones for adding to a package.
    *   **Calls**:
        *   `ContentModuleValidationError`: (External)
        *   `validateInclusion`: Validates individual content inclusion.
        *   `externalArticleService.findOrCreateExternalArticleByUrl`: (External)
        *   `LexicalPositionService.assignPosition`: (External)
        *   `createPreparedStandalone`: Creates a prepared standalone object.
4.  **Example Usage**:
    ```typescript
    const contentsArgs = [
      { contentId: "video-1", contentType: "Video", position: 1 },
      { contentId: "video-2", contentType: "Video", position: 2 },
    ];
    const prepared = await validateAndPrepareStandalones(contentsArgs, "admin");
    ```

---

### `validateInclusion`

1.  **Method Name**: `validateInclusion`
2.  **Description**: Validates if a specific content module can be included in a package, checking for existence, module type, and preventing duplicates.
3.  **Call Stack**:
    *   **Called by**:
        *   `validateAndPrepareStandalones`: Validates each standalone content.
    *   **Calls**:
        *   `fetchContentModuleById`: (External)
        *   `ContentModuleValidationError`: (External)
4.  **Example Usage**:
    ```typescript
    const contentArg = { contentId: "art-1", contentType: "Article" };
    const existingContents = []; // e.g., from updatingPackage.Composite.contents
    const allContentArgs = [contentArg]; // e.g., the `contentsArgs` array passed to validateAndPrepareStandalones
    await validateInclusion(contentArg, existingContents, allContentArgs);
    ```

---

### `createPreparedStandalone`

1.  **Method Name**: `createPreparedStandalone`
2.  **Description**: Constructs a `PreparedStandalone` object, encapsulating all necessary data for creating or updating a standalone content module in the database.
3.  **Call Stack**:
    *   **Called by**:
        *   `validateAndPrepareStandalones`: Creates a prepared object for each standalone.
    *   **Calls**:
        *   `isAutoProgrammed`: (External)
        *   `processAllowedCountries`: (External)
        *   `findOrGenerateWrapperId`: (External)
4.  **Example Usage**:
    ```typescript
    const contentData = { contentId: "img-1", contentType: "Image", title: "My Image" };
    const prepared = await createPreparedStandalone(contentData, "uploader", 1);
    ```

---

### `prepareStandaloneOperations`

1.  **Method Name**: `prepareStandaloneOperations`
2.  **Description**: Transforms an array of `PreparedStandalone` objects into a list of `StandaloneUpsertOperation` objects, indicating whether each should be a 'create' or 'update' operation.
3.  **Call Stack**:
    *   **Called by**:
        *   `createPackage`: Prepares standalone operations for a new package.
        *   `updatePackage`: Prepares standalone operations for an updated package.
        *   `addContentToPackage`: Prepares standalone operations for content being added to a package.
    *   **Calls**: None.
4.  **Example Usage**:
    ```typescript
    const preparedStandalones = [
      { data: { /* ... */ }, contentModuleId: "s-123", position: 1, lockedPosition: null },
      { data: { /* ... */ }, position: 2, lockedPosition: null }, // new standalone
    ];
    const operations = prepareStandaloneOperations(preparedStandalones);
    ```

---

### `internalUpsertStandlone`

1.  **Method Name**: `internalUpsertStandlone`
2.  **Description**: Executes the create or update operations for a list of standalone content modules in the database.
3.  **Call Stack**:
    *   **Called by**:
        *   `createPackage`: Upserts standalones during package creation.
        *   `updatePackage`: Upserts standalones during package update.
        *   `addContentToPackage`: Upserts standalones during content addition.
    *   **Calls**:
        *   `prismaConn.getConn()`: (External)
        *   `prismaClient.module.update()`: (External)
        *   `prismaClient.module.create()`: (External)
4.  **Example Usage**:
    ```typescript
    const operations = [
      { operation: 'create', data: { type: 'standalone', contentId: 'a1', contentType: 'Article', /* ... */ }, position: 1, lockedPosition: null },
      { operation: 'update', moduleId: 's-456', data: { type: 'standalone', title: 'New Title', /* ... */ }, position: 2, lockedPosition: null },
    ];
    const upsertedStandalones = await internalUpsertStandlone(operations);
    ```

---

### `upsertPackageStandaloneAssoc`

1.  **Method Name**: `upsertPackageStandaloneAssoc`
2.  **Description**: Creates or updates the association records between a composite (package) module and its standalone content modules in the database.
3.  **Call Stack**:
    *   **Called by**:
        *   `createPackage`: Associates standalones with a new package.
        *   `updatePackage`: Associates standalones with an updated package.
        *   `addContentToPackage`: Associates standalones when adding to a package.
    *   **Calls**:
        *   `prismaConn.getConn()`: (External)
        *   `prismaClient.compositeModule.upsert()`: (External)
4.  **Example Usage**:
    ```typescript
    const packageCompositeId = 101;
    const standalonesToAssociate = [
      { moduleId: "s-789", position: 1, isPositionLocked: false, lockedPosition: null },
      { moduleId: "s-101", position: 2, isPositionLocked: true, lockedPosition: 2 },
    ];
    await upsertPackageStandaloneAssoc(packageCompositeId, standalonesToAssociate);
    ```

---

### `removeSurplusFromStaticPackages`

1.  **Method Name**: `removeSurplusFromStaticPackages`
2.  **Description**: For static packages with content limits, identifies and marks excess content modules for exclusion, potentially moving them to a general feed.
3.  **Call Stack**:
    *   **Called by**:
        *   `createPackage`: Removes surplus after package creation.
        *   `updatePackage`: Removes surplus after package update.
        *   `addContentToPackage`: Removes surplus after adding content.
    *   **Calls**:
        *   `prismaConn.getConn()`: (External)
        *   `prismaClient.compositeModule.findMany()`: (External)
        *   `moveContentFromCompositeToGeneralFeed`: Moves content to the general feed if applicable.
        *   `prismaClient.compositeModule.updateMany()`: (External)
4.  **Example Usage**:
    ```typescript
    import { PackageType } from '../types';

    const compositeInfo = { id: 202, packageType: PackageType.TopHeadlines };
    await removeSurplusFromStaticPackages(compositeInfo);
    ```

---

### `validateContents`

1.  **Method Name**: `validateContents`
2.  **Description**: Validates the input arguments for content modules, ensuring that valid identifiers (either `contentModuleId` or `(contentId + contentType)`) are provided.
3.  **Call Stack**:
    *   **Called by**:
        *   `addContentToPackage`: Validates content before adding.
        *   `upsertPackageContentModules`: Validates content for upsert operations.
    *   **Calls**:
        *   `ContentModuleValidationError`: (External)
4.  **Example Usage**:
    ```typescript
    import { PackageContent } from '../types';

    const validContent: PackageContent[] = [{ contentId: "a1", contentType: "Article", position: 1 }];
    validateContents(validContent); // No error

    const invalidContent: PackageContent[] = [{ contentId: "a2", contentType: "Article", contentModuleId: "s1" }];
    try {
      validateContents(invalidContent); // Throws ContentModuleValidationError
    } catch (e) {
      console.error(e.message);
    }
    ```

---

### `moveContentFromCompositeToGeneralFeed`

1.  **Method Name**: `moveContentFromCompositeToGeneralFeed`
2.  **Description**: Moves specified content modules from a composite (package) back to the general feed by updating their channel associations as standalone modules.
3.  **Call Stack**:
    *   **Called by**:
        *   `removeSurplusFromStaticPackages`: Moves excess content when package limits are enforced.
    *   **Calls**:
        *   `replicaConn.getConn()`: (External)
        *   `replicaClient.composite.findFirst()`: (External)
        *   `updateStandalone`: (External)
4.  **Example Usage**:
    ```typescript
    const exceedingModules = [
      { compositeId: 202, moduleId: "s-303" },
      { compositeId: 202, moduleId: "s-404" },
    ];
    await moveContentFromCompositeToGeneralFeed(exceedingModules);
    ```