Here is the comprehensive documentation for the methods found in the provided file:

---

### Method Name: `applyResultFlagMapFactory`

*   **Description**: Generates a map of flags indicating the availability of various content categories, defaulting all to `true`.

*   **Call Stack**:
    *   **Called by**:
        *   `PackageTypeResultReplacerService.applyRules`: Provides default flags for rule application if `applyFlagMap` is not explicitly provided.
    *   **Calls**:
        *   None.

*   **Example Usage**:
    ```typescript
    const service = new PackageTypeResultReplacerService();
    const defaultFlags = service.applyResultFlagMapFactory();
    /*
    defaultFlags will be:
    {
      TopHeadlines: true,
      TrendingVideos: true,
      WhatsBuzzing: true,
      CreatorShows: true,
      FromTheFans: true,
      AdHoc: true,
      TrendingBets: true,
      Highlights: true,
    }
    */
    ```

---

### Method Name: `applyRules`

*   **Description**: Applies rules and modifications to content modules, especially for package modules, based on provided arguments.

*   **Call Stack**:
    *   **Called by**:
        *   (No identifiable callers within the provided file, as this is a public class method).
    *   **Calls**:
        *   `PackageTypeResultReplacerService.applyResultFlagMapFactory`: Generates a default flag map if `applyFlagMap` is not provided in the arguments.
        *   `PackageTypeResultReplacerService.processWhatsBuzzing`: Handles the specific processing logic for `WhatsBuzzing` package types.
        *   `filterCreatorShows`: Filters content modules specifically for Creator Shows packages.
        *   `cloneDeep` (from `lodash`): Deeply clones an object, typically used to avoid reference issues when modifying content modules.

*   **Example Usage**:
    ```typescript
    import { packageTypeResultReplacerService } from './PackageTypeResultReplacerService';
    import { PackageTypeResultMap } from './PackageTypeResultMap';
    import { ModuleType, TStandaloneContentModule } from './ContentModuleTypes';

    const args = {
      contentModules: [
        /* ... array of TContentModule ... */
        {
          type: ModuleType.Package,
          Composite: {
            id: 1,
            packageType: 'CreatorShows', // PackageType enum
            contents: [], // TCompositeModule[]
          },
        },
      ],
      packageTypeResultMap: new PackageTypeResultMap(), // Instance of PackageTypeResultMap
      applyFlagMap: {
        TopHeadlines: true,
        WhatsBuzzing: false,
        CreatorShows: true,
        // ... other flags
      },
      tagData: { tagSlug: 'sports', userTags: [] },
      semanticID: 'unique-semantic-id',
      tagUUID: 'unique-tag-uuid',
      statesFilter: { country: 'US' },
    };

    async function processContent() {
      const modifiedModules = await packageTypeResultReplacerService.applyRules(args);
      console.log('Modified Content Modules:', modifiedModules);
    }

    processContent();
    ```

---

### Method Name: `processWhatsBuzzing`

*   **Description**: Handles the specific processing logic for `WhatsBuzzing` package type modules based on a feature flag.

*   **Call Stack**:
    *   **Called by**:
        *   `PackageTypeResultReplacerService.applyRules`: Invoked when a content module is identified as a `WhatsBuzzing` package type.
    *   **Calls**:
        *   `PackageTypeResultReplacerService.createSortedCompositeContents`: Creates and sorts composite contents from standalone modules.
        *   `filterWhatzBuzzing`: Filters 'Whats Buzzing' content modules based on a tag UUID.
        *   `PackageTypeResultReplacerService.mapToCompositeContents`: Maps standalone modules to composite contents with positions.

*   **Example Usage**: (This is a private method and not intended for direct external use)
    ```typescript
    // Example of internal call within PackageTypeResultReplacerService.applyRules
    // const processedContents = await this.processWhatsBuzzing(
    //   cm, // TPackageContentModule
    //   packageTypeResultMap, // PackageTypeResultMap
    //   defaultApplyFlagMap, // IApplyResultFlagMap
    //   args // IApplyRulesArgs
    // );
    ```

---

### Method Name: `createSortedCompositeContents`

*   **Description**: Creates composite content modules from a list of standalone modules and sorts them by `insertedAt` date (descending) then by `position` (ascending).

*   **Call Stack**:
    *   **Called by**:
        *   `PackageTypeResultReplacerService.processWhatsBuzzing`: Used to generate sorted composite contents for the WhatsBuzzing package type when its flag is enabled.
    *   **Calls**:
        *   `createCompositeContentsFromStandaloneContentModules`: Creates composite content modules from an array of standalone modules and a composite object.

*   **Example Usage**: (This is a private method and not intended for direct external use)
    ```typescript
    // Example of internal call within PackageTypeResultReplacerService.processWhatsBuzzing
    // const sortedComposites = await this.createSortedCompositeContents(
    //   standalones, // TStandaloneContentModule[]
    //   module.Composite // TComposite
    // );
    ```

---

### Method Name: `mapToCompositeContents`

*   **Description**: Maps an array of standalone content modules to an array of composite content modules, assigning positions and composite metadata.

*   **Call Stack**:
    *   **Called by**:
        *   `PackageTypeResultReplacerService.processWhatsBuzzing`: Used to map filtered standalone modules to composite contents when the WhatsBuzzing flag is disabled.
    *   **Calls**:
        *   `cloneDeep` (from `lodash`): Deeply clones a standalone module before adding it to the composite structure to avoid reference issues.

*   **Example Usage**: (This is a private method and not intended for direct external use)
    ```typescript
    // Example of internal call within PackageTypeResultReplacerService.processWhatsBuzzing
    // const mappedContents = await this.mapToCompositeContents(
    //   resolvedContents, // TStandaloneContentModule[]
    //   module.Composite.id, // number
    //   args.tagUUID // string
    // );
    ```